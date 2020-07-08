---
title: Implementar em anfitrião dedicado
description: Use um hospedeiro dedicado para alcançar um verdadeiro isolamento ao nível do hospedeiro para as suas cargas de trabalho de instâncias de contentores Azure
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: a614d6b5d0cf5c6c1df5ffcb90e56960d6b8a2a9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82025038"
---
# <a name="deploy-on-dedicated-hosts"></a>Implementar em anfitriões dedicados

"Dedicado" é um sku de instâncias de contentores Azure (ACI) que proporciona um ambiente de computação isolado e dedicado para recipientes de funcionamento seguro. A utilização do sku dedicado resulta em cada grupo de contentores ter um servidor físico dedicado num datacenter Azure, garantindo o isolamento total da carga de trabalho para ajudar a satisfazer os requisitos de segurança e conformidade da sua organização. 

O sku dedicado é apropriado para cargas de trabalho de contentores que requerem isolamento de carga de trabalho do ponto de vista do servidor físico.

## <a name="prerequisites"></a>Pré-requisitos

* O limite padrão para qualquer subscrição para usar o sku dedicado é 0. Se quiser utilizar este sku para as suas instalações de contentores de produção, crie um [pedido de Suporte Azure][azure-support] para aumentar o limite.

## <a name="use-the-dedicated-sku"></a>Use o sku dedicado

> [!IMPORTANT]
> A utilização do sku dedicado só está disponível na versão mais recente da API (2019-12-01) que está neste momento a ser lançada. Especifique esta versão API no seu modelo de implementação.
>

Começando pela versão API 2019-12-01, existe uma `sku` propriedade sob a secção de propriedades do grupo de contentores de um modelo de implantação, que é necessário para uma implantação de ACI. Atualmente, você pode usar esta propriedade como parte de um modelo de implementação de Azure Resource Manager para ACI. Saiba mais sobre a implantação de recursos ACI com um modelo no [Tutorial: Implemente um grupo multi-contentores utilizando um modelo de Gestor de Recursos](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

O `sku` imóvel pode ter um dos seguintes valores:
* `Standard`- a escolha padrão de implantação do ACI, que ainda garante a segurança ao nível do hipervisor 
* `Dedicated`- utilizado para o isolamento do nível de carga de trabalho com hospedeiros físicos dedicados para o grupo de contentores

## <a name="modify-your-json-deployment-template"></a>Modifique o seu modelo de implementação JSON

No seu modelo de implantação, modifique ou adicione as seguintes propriedades:
* Em `resources` baixo , definido para `apiVersion` `2019-12-01` .
* Sob as propriedades do grupo de contentores, adicione uma `sku` propriedade com `Dedicated` valor.

Aqui está um exemplo de snippet para a secção de recursos de um modelo de implantação de grupo de contentores que usa o sku dedicado:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

Segue-se um modelo completo que implanta um grupo de recipientes de amostra que executa uma única instância de contentor:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Desdobre o seu grupo de contentores

Se criou e editou o ficheiro de modelo de implementação no seu ambiente de trabalho, pode carregá-lo para o seu diretório Cloud Shell arrastando o ficheiro para o mesmo. 

Crie um grupo de recursos com o comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementar o modelo com a implementação do [grupo az criar][az-group-deployment-create] comando.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Dentro de alguns segundos, deverá receber uma resposta inicial do Azure. Uma implantação bem sucedida ocorre num hospedeiro dedicado.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
