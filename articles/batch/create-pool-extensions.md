---
title: Use extensões com piscinas de lote
description: As extensões são pequenas aplicações que facilitam a configuração pós-provisão e a configuração nos nós de computação batch.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 1bf9847af57347c143ee3d790d89988ba7cd48e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417445"
---
# <a name="use-extensions-with-batch-pools"></a>Use extensões com piscinas de lote

As extensões são pequenas aplicações que facilitam a configuração pós-provisão e a configuração nos nós de computação batch. Pode selecionar qualquer uma das extensões permitidas pelo Azure Batch e instalá-las nos nós de computação à medida que forem previstas. Depois disso, a extensão pode executar o funcionamento pretendido.

Pode verificar o estado vivo das extensões que utiliza e recuperar as informações que devolvem para prosseguir com quaisquer capacidades de deteção, correção ou diagnóstico.

## <a name="prerequisites"></a>Pré-requisitos

- As piscinas com extensões devem utilizar [a Configuração da Máquina Virtual.](nodes-and-pools.md#virtual-machine-configuration)
- O tipo de extensão CustomScript está reservado para o serviço Azure Batch e não pode ser ultrapassado.

### <a name="supported-extensions"></a>Extensões apoiadas

As seguintes extensões podem atualmente ser instaladas ao criar um pool batch. 

- Extensão do cofre da chave Azure para [Linux](../virtual-machines/extensions/key-vault-linux.md) e [Windows](../virtual-machines/extensions/key-vault-windows.md)
- Registar analítica e extensão de monitorização tanto para [Linux](../virtual-machines/extensions/oms-linux.md) como [para Windows](../virtual-machines/extensions/oms-windows.md)
- Pacote de Segurança Azure

Pode solicitar apoio a editores adicionais e/ou tipos de extensão abrindo um pedido de apoio.

## <a name="create-a-pool-with-extensions"></a>Criar uma piscina com extensões

O exemplo abaixo cria um lote de nós Linux que usa a extensão Azure Key Vault.

URI da API REST

```http
 PUT https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Batch/batchAccounts/<batchaccountName>/pools/<batchpoolName>?api-version=2021-01-01
```

Corpo do Pedido

```json
{
    "name": "test1",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "secretext",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2.vault.azure.net/secrets/authsecreat"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "885b1a3d-f13c-4030-afcf-9f05044d78dc"
                            }
                        },
                        "protectedSettings":{}
                    }
                ]
            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "targetLowPriorityNodes": 0,
                "resizeTimeout": "PT15M"
            }
        }
```

## <a name="get-extension-data-from-a-pool"></a>Obtenha dados de extensão de um pool

O exemplo abaixo recupera dados da extensão Azure Key Vault.

URI da API REST

```http
 GET https://<accountname>.<region>.batch.azure.com/pools/test3/nodes/tvmps_a3ce79db285d6c124399c5bd3f3cf308d652c89675d9f1f14bfc184476525278_d/extensions/secretext?api-version=2010-01-01
```

Corpo de Resposta

```json
{
  "odata.metadata":"https://testwestus2batch.westus2.batch.azure.com/$metadata#extensions/@Element","instanceView":{
    "name":"secretext","statuses":[
      {
        "code":"ProvisioningState/succeeded","level":0,"displayStatus":"Provisioning succeeded","message":"Successfully started Key Vault extension service. 2021-02-08T19:49:39Z"
      }
    ]
  },"vmExtension":{
    "name":"KVExtensions","publisher":"Microsoft.Azure.KeyVault","type":"KeyVaultForLinux","typeHandlerVersion":"1.0","autoUpgradeMinorVersion":true,"settings":"{\r\n  \"secretsManagementSettings\": {\r\n    \"pollingIntervalInS\": \"300\",\r\n    \"certificateStoreLocation\": \"/var/lib/waagent/Microsoft.Azure.KeyVault\",\r\n    \"requireInitialSync\": true,\r\n    \"observedCertificates\": [\r\n      \"https://testkvwestus2.vault.azure.net/secrets/testumi\"\r\n    ]\r\n  },\r\n  \"authenticationSettings\": {\r\n    \"msiEndpoint\": \"http://169.254.169.254/metadata/identity\",\r\n    \"msiClientId\": \"885b1a3d-f13c-4030-afcf-922f05044d78dc\"\r\n  }\r\n}"
  }
}

```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre várias formas de [copiar aplicações e dados para juntar nós.](batch-applications-to-pool-nodes.md)
- Saiba mais sobre trabalhar com [nós e piscinas.](nodes-and-pools.md)
