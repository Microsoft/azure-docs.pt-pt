---
title: Implantação em hospedeiro dedicado
description: Utilize um hospedeiro dedicado para alcançar um verdadeiro isolamento ao nível do hospedeiro para as suas cargas de trabalho de casos de contentores Azure
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: a614d6b5d0cf5c6c1df5ffcb90e56960d6b8a2a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025038"
---
# <a name="deploy-on-dedicated-hosts"></a>Implementar em anfitriões dedicados

"Dedicado" é um sku Azure Container Instances (ACI) que proporciona um ambiente de computação isolado e dedicado para recipientes de corrida seguros. Utilizando os resultados dedicados em cada grupo de contentores, tendo um servidor físico dedicado num centro de dados Azure, garantindo o isolamento total da carga de trabalho para ajudar a satisfazer os requisitos de segurança e conformidade da sua organização. 

O sku dedicado é apropriado para cargas de trabalho de contentores que requerem isolamento de carga de trabalho do ponto de vista do servidor físico.

## <a name="prerequisites"></a>Pré-requisitos

* O limite padrão para qualquer subscrição usar o sku dedicado é 0. Se quiser utilizar este sku para as suas implantações de contentores de produção, crie um pedido de [Suporte Azure][azure-support] para aumentar o limite.

## <a name="use-the-dedicated-sku"></a>Use o sku dedicado

> [!IMPORTANT]
> A utilização do sku dedicado só está disponível na versão Mais recente da API (2019-12-01) que está atualmente a ser lançada. Especifique esta versão API no seu modelo de implementação.
>

Começando com a versão API 2019-12-01, existe uma `sku` propriedade sob a secção de propriedades do grupo de contentores de um modelo de implantação, que é necessário para uma implementação de ACI. Atualmente, você pode usar esta propriedade como parte de um modelo de implementação do Gestor de Recursos Azure para ACI. Saiba mais sobre a implementação de recursos ACI com um modelo no [Tutorial: Implante um grupo multi-contentor usando um modelo](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)de Gestor de Recursos . 

O `sku` imóvel pode ter um dos seguintes valores:
* `Standard`- a escolha padrão de implantação do ACI, que ainda garante a segurança ao nível do hipervisor 
* `Dedicated`- utilizado para o isolamento do nível de carga de trabalho com hospedeiros físicos dedicados para o grupo de contentores

## <a name="modify-your-json-deployment-template"></a>Modifique o seu modelo de implementação JSON

No seu modelo de implementação, modifique ou adicione as seguintes propriedades:
* Abaixo, `resources` `apiVersion` definido `2019-12-01`para.
* Sob as propriedades do `sku` grupo de `Dedicated`contentores, adicione uma propriedade com valor.

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

Segue-se um modelo completo que implanta um grupo de contentores de amostras que executa uma única instância de contentor:

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

## <a name="deploy-your-container-group"></a>Implante o seu grupo de contentores

Se criou e editou o ficheiro do modelo de implementação no seu ambiente de trabalho, pode carregá-lo para o seu diretório Cloud Shell arrastando o ficheiro para o mesmo. 

Crie um grupo de recursos com o comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Desloque o modelo com a implementação do [grupo AZ criar][az-group-deployment-create] comando.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Dentro de alguns segundos, deverá receber uma resposta inicial do Azure. Uma implantação bem sucedida ocorre num hospedeiro dedicado.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
