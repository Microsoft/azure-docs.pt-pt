---
title: Executar recipientes init
description: Execute recipientes init em Instâncias de Contentores Azure para executar tarefas de configuração num grupo de contentores antes do funcionamento dos recipientes de aplicação.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 5a729263ee632eb9227694ec8684eb6889c6324b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85954286"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>Executar um recipiente init para tarefas de configuração em um grupo de contentores

Azure Container Instances suporta *recipientes init* num grupo de contentores. Os recipientes init são concluídos antes do início do recipiente ou dos recipientes de aplicação. Semelhante aos [recipientes init Kubernetes,](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)use um ou mais recipientes init para executar a lógica de inicialização para os seus recipientes de aplicações, tais como definir contas, executar scripts de configuração ou configurar bases de dados.

Este artigo mostra como usar um modelo de Gestor de Recursos Azure para configurar um grupo de contentores com um recipiente init.

## <a name="things-to-know"></a>Aspetos importantes

* **Versão API** - É necessário pelo menos a versão API de Instâncias de Contentores Azure 2019-12-01 para implantar recipientes init. Implemente usando uma `initContainers` propriedade num [ficheiro YAML](container-instances-multi-container-yaml.md) ou num [modelo de Gestor de Recursos.](container-instances-multi-container-group.md)
* **Ordem de execução** - Os recipientes init são executados na ordem especificada no modelo e antes de outros contentores. Por predefinição, pode especificar um máximo de 59 recipientes init por grupo de contentores. Pelo menos um contentor não in noit deve estar no grupo.
* **Ambiente de anfitrião** - Os recipientes init funcionam no mesmo hardware que os restantes contentores do grupo.
* **Recursos** - Não se especifica recursos para contentores init. São-lhes concedidos os recursos totais, tais como CPUs e memória disponível para o grupo de contentores. Enquanto um recipiente de init funciona, nenhum outro contentor funciona no grupo.
* **Propriedades suportadas** - Os recipientes init podem usar propriedades de grupo como volumes, segredos e identidades geridas. No entanto, não podem utilizar portas ou um endereço IP se configurados para o grupo de contentores. 
* **Regra de reinício** - Cada recipiente init deve sair com sucesso antes do próximo recipiente do grupo começar. Se um recipiente init não sair com sucesso, a sua ação de reinício depende da política de [reinício](container-instances-restart-policy.md) configurada para o grupo:

    |Política em grupo  |Política emit  |
    |---------|---------|
    |Sempre     |OnFailure         |
    |OnFailure     |OnFailure         |
    |Nunca     |Nunca         |
* **Encargos** - O grupo de contentores incorre nas cargas a partir da primeira colocação de um recipiente init.

## <a name="resource-manager-template-example"></a>Exemplo do modelo do gestor de recursos

Comece por copiar o seguinte JSON num novo ficheiro chamado `azuredeploy.json` . O modelo configura um grupo de contentores com um recipiente init e dois recipientes de aplicação:

* O contentor *init1* executa a imagem da [caixa ocupada](https://hub.docker.com/_/busybox) do Docker Hub. Dorme durante 60 segundos e depois escreve uma linha de comando para um ficheiro num [volume deDir vazio](container-instances-volume-emptydir.md).
* Ambos os recipientes de aplicação executam a imagem do recipiente da `aci-wordcount` Microsoft:
    * O contentor *da aldeia* executa a aplicação wordcount na sua configuração padrão, contando frequências de palavras na peça de Shakespeare *Hamlet*.
    * O contentor da aplicação *Juliet* lê a corda da linha de comando do volume emptDir para executar a aplicação wordcount em vez de *Romeu e Julieta* de Shakespeare.

Para obter mais informações e exemplos utilizando a `aci-wordcount` imagem, consulte [variáveis de ambiente definidos em instâncias de recipientes](container-instances-environment-variables.md).

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
                "sku": "Standard",
                "initContainers": [
                {
                    "name": "init1",
                    "properties": {
                        "image": "busybox",
                        "environmentVariables": [],
                        "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                         "command": [
                            "/bin/sh",
                            "-c",
                            "sleep 60; echo python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html > /mnt/emptydir/command_line.txt"
                        ]
                    }
                }
            ], 
                "containers": [
                    {
                        "name": "hamlet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    },
                    {
                        "name": "juliet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                            ],
                            "command": [
                                "/bin/sh",
                                "-c",
                                "$(cat /mnt/emptydir/command_line.txt)"
                            ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "OnFailure",
                "osType": "Linux",
                "volumes": [
                    {
                        "name": "emptydir1",
                        "emptyDir": {}
                    }
                ]           
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-the-template"></a>Implementar o modelo

Crie um grupo de recursos com o comando [az group create][az-group-create].

```azurecli
az group create --name myResourceGroup --location eastus
```

Implementar o modelo com o [grupo de implementação az criar][az-deployment-group-create] comando.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

Num grupo com um recipiente init, o tempo de implantação é aumentado devido ao tempo que o recipiente ou recipientes do init demoram a completar.


## <a name="view-container-logs"></a>Ver registos de contentor

Para verificar se o recipiente init correu com sucesso, consulte a saída de registo dos recipientes de aplicação utilizando o comando [de registos de contentores az.][az-container-logs] O `--container-name` argumento especifica o recipiente a partir do qual puxar os troncos. Neste exemplo, puxe os troncos para os recipientes *de aldeia* e *julieta,* que mostram diferentes saídas de comando:

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name hamlet
```

Resultado:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name juliet
```

Resultado:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Passos seguintes

Os recipientes init ajudam-no a executar tarefas de configuração e inicialização para os seus recipientes de aplicação. Para obter mais informações sobre a execução de recipientes baseados em tarefas, consulte [executar tarefas contentorizadas com políticas de reinício](container-instances-restart-policy.md).

Azure Container Instances fornece outras opções para modificar o comportamento dos recipientes de aplicação. Os exemplos incluem:

* [Definir variáveis ambientais em instâncias de contentores](container-instances-environment-variables.md)
* [Definir a linha de comando numa instância de contentor para anular a operação da linha de comando predefinido](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-container-logs]: /cli/azure/container#az-container-logs
