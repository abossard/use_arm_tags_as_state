# COMMANDS

az deployment group create -g anbossar-test-kv --template-file ./kv.json --parameters objectId=`az ad signed-in-user  show -o table --query 'objectId' | tail -1`  --confirm-with-what-if

az deployment group create -g anbossar-test-kv --template-file ./kv.json --parameters objectId='8fd16e4b-7e47-447f-8639-08409203784e'  --confirm-with-what-if
az deployment group create -g anbossar-test-kv --template-file ./kv_recover.json --parameters objectId='8fd16e4b-7e47-447f-8639-08409203784e'  --confirm-with-what-if

az deployment group create -g anbossar-test-kv --template-file ./kv_prepare.json 


az deployment group create -g anbossar-test-kv --template-file ./kv_undeploy.json  --confirm-with-what-if --mode complete