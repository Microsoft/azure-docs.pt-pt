---
title: Implantação em hospedeiro dedicado
description: Utilize um hospedeiro dedicado para alcançar um verdadeiro isolamento ao nível do hospedeiro para as suas cargas de trabalho de casos de contentores Azure
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: adad0ddfc78530b3a3a7c139d9a95ec4790c8053
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934152"
---
# <a name="deploy-on-dedicated-hosts"></a>Implementar em anfitriões dedicados

"Dedicado" é um SKU de ACI (instâncias de contêiner do Azure) que fornece um ambiente de computação isolado e dedicado para executar contêineres com segurança. O uso do SKU dedicado resulta em cada grupo de contêineres com um servidor físico dedicado em um datacenter do Azure, garantindo o isolamento completo da carga de trabalho para ajudar a atender aos requisitos de segurança e conformidade da sua organização. 

O SKU dedicado é apropriado para cargas de trabalho de contêiner que exigem isolamento de carga de trabalho de uma perspectiva de servidor físico.

## <a name="prerequisites"></a>Pré-requisitos

* O limite padrão para qualquer subscrição usar o sku dedicado é 0. Se quiser utilizar este sku para as suas implantações de contentores de produção, crie um pedido de [Suporte Azure][azure-support] para aumentar o limite.

## <a name="use-the-dedicated-sku"></a>Use o sku dedicado

> [!IMPORTANT]
> O uso do SKU dedicado só está disponível na versão mais recente da API (2019-12-01) que está sendo distribuída no momento. Especifique essa versão de API em seu modelo de implantação.
>

Começando com a versão API 2019-12-01, existe uma propriedade `sku` sob a secção de propriedades do grupo de contentores de um modelo de implantação, que é necessário para uma implementação de ACI. No momento, você pode usar essa propriedade como parte de um modelo de implantação Azure Resource Manager para ACI. Saiba mais sobre a implementação de recursos ACI com um modelo no [Tutorial: Implante um grupo multi-contentor usando um modelo](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)de Gestor de Recursos . 

A propriedade `sku` pode ter um dos seguintes valores:
* `Standard` - a escolha padrão de implantação do ACI, que ainda garante a segurança ao nível do hipervisor 
* `Dedicated` - utilizado para o isolamento do nível de carga de trabalho com hospedeiros físicos dedicados para o grupo de contentores

## <a name="modify-your-json-deployment-template"></a>Modificar seu modelo de implantação JSON

No seu modelo de implementação, modifique ou adicione as seguintes propriedades:
* Em `resources`, `apiVersion` definida para `2012-12-01`.
* Sob as propriedades do grupo de contentores, adicione uma propriedade `sku` com valor `Dedicated`.

Aqui está um trecho de código de exemplo para a seção de recursos de um modelo de implantação de grupo de contêineres que usa o SKU dedicado:

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

## <a name="deploy-your-container-group"></a>Implantar seu grupo de contêineres

Se você criou e editou o arquivo de modelo de implantação em sua área de trabalho, você pode carregá-lo em seu diretório Cloud Shell arrastando o arquivo para ele. 

Crie um grupo de recursos com o comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implante o modelo com o comando [AZ Group Deployment Create][az-group-deployment-create] .

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Dentro de alguns segundos, deverá receber uma resposta inicial do Azure. Uma implantação bem sucedida ocorre num hospedeiro dedicado.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
