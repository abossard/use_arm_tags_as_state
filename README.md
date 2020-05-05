# Use Tags on the resource group to keep existing key vault policies

## Rational
Sometimes you want to deploy changes to a key vault and keep the existing access policies. There are multiple problems with that:
- properties.accessPolicies is a mandatory field
- if it's left as empty array, all policies are removed
- if there's any policy in it, all existing policies will be removed

This behavior is in line with desired state configuration, but sometimes not helpful.

## Scenario A
1. deploy a key vault with a default policy (or no policy)
1. subsequent applications deployments each add their own access policies to the key vault
1. A change to the kv has to be applied with a deployment, but the deployments from step 2. should be kept


## Solution
- add a tag on deployment of the key vault as "state"
- deployment is structured in two nested templates to decouple the template referencing (you can't reference a resource in the template where's it's deployed)
- first nested template is evaluating the tag and either returns existing policies merged with the default policies or just the or just the default policies.
- the second nested template deploys the key vault and uses the output from the prior template 


## COMMANDS

### 0. Prepare the environment
```
set GROUP=my-test-kv 
```

### 1. Start the deployment

```
az deployment group create -g $GROUP --template-file ./kv_nested.json --parameters objectId=`az ad signed-in-user  show -o table --query 'objectId' | tail -1`  --confirm-with-what-if
```

> Note: what-if is not working with a nested template.

### 2. Add a policy

Add another policy to the the KV (manually or however you prefer)

## 3. Execure the deployment again
```
az deployment group create -g $GROUP --template-file ./kv_nested.json --parameters objectId=`az ad signed-in-user  show -o table --query 'objectId' | tail -1`  --confirm-with-what-if
```

> Note: Because the Resource Group has a tag "KV-Keep-Policies" it will first get the existing access policies

## clean up
```
az deployment group create -g $GROUP --template-file ./helpers/kv_undeploy.json  --confirm-with-what-if --mode complete
```