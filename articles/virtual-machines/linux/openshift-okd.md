---
title: Implementar OKD no Azure | Documentos da Microsoft
description: Implemente OKD no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/02/2019
ms.author: haroldw
ms.openlocfilehash: 7db50007dd32c84a360eaec25bf860709272437b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999080"
---
# <a name="deploy-okd-in-azure"></a>Implementar OKD no Azure

Pode utilizar uma das duas formas de implementar OKD (anteriormente conhecido como o OpenShift Origin) no Azure:

- Pode implementar manualmente todos os componentes de infraestrutura do Azure necessários e, em seguida, siga os [documentação OKD](https://docs.okd.io).
- Também pode utilizar existente [modelo do Resource Manager](https://github.com/Microsoft/openshift-origin) que simplifica a implementação do OKD cluster.

## <a name="deploy-using-the-okd-template"></a>Implementar com o modelo OKD

Para implementar com o modelo do Resource Manager, utiliza um ficheiro de parâmetros para fornecer os parâmetros de entrada. Para personalizar ainda mais a implementação, bifurcar o repositório do GitHub e altere os itens adequados.

Algumas opções de personalização comuns incluem, mas não estão limitadas a:

- Tamanho de VM de bastion (variável no azuredeploy. JSON)
- Convenções de nomenclatura (variáveis no azuredeploy. JSON)
- Especificações de cluster do OpenShift, modificado através do ficheiro de anfitriões (deployOpenShift.sh)

O [modelo OKD](https://github.com/Microsoft/openshift-origin) tem vários ramos disponíveis para versões diferentes do OKD.  Com base nas suas necessidades, pode implementar diretamente a partir do repositório ou pode bifurcar o repositório e fazer alterações personalizadas antes de implementar.

Utilize o `appId` valor do principal de serviço que criou anteriormente para o `aadClientId` parâmetro.

Segue-se um exemplo de um ficheiro de parâmetros com o nome azuredeploy com todas as entradas necessárias.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Substitua os parâmetros com as suas informações específicas.

Diferentes versões podem ter parâmetros diferentes por isso, verifique se os parâmetros necessários para o ramo que utilizar.

### <a name="deploy-using-azure-cli"></a>Implementar com a CLI do Azure


> [!NOTE] 
> Requer que o seguinte comando da CLI do Azure 2.0.8 ou posterior. Pode verificar a versão da CLI com o `az --version` comando. Para atualizar a versão da CLI, veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

O exemplo seguinte implementa o cluster OKD e todos os recursos relacionados num grupo de recursos com o nome openshiftrg, com um nome de implementação de myOpenShiftCluster. O modelo é referenciado diretamente a partir do repositório do GitHub, ao utilizar um ficheiro de parâmetros local com o nome azuredeploy.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A implantação se, pelo menos, 30 minutos a concluir, com base no número total de nós implementados. O URL da consola do OpenShift e o nome DNS das impressões mestres OpenShift terminal quando a conclusão da implementação. Em alternativa, pode ver a secção de saídas da implementação a partir do portal do Azure.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Se não pretender obstruir a linha de comandos a aguardar que a implementação concluir, adicionar `--no-wait` como uma das opções para a implementação de grupo. O resultado da implementação pode ser obtido a partir do portal do Azure, na secção de implementação para o grupo de recursos.

## <a name="connect-to-the-okd-cluster"></a>Ligar ao OKD cluster

Quando a conclusão da implementação, ligar à consola do OpenShift com o seu browser com o `OpenShift Console Url`. Em alternativa, pode encaminhar o SSH para o mestre de OKD. Segue-se um exemplo que usa o resultado da implementação:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar recursos

Utilize o [eliminação do grupo de az](/cli/azure/group) comando para remover o grupo de recursos, o OpenShift cluster e todos os recursos relacionados quando já não forem necessários.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Passos Seguintes

- [Tarefas de pós-implementação](./openshift-post-deployment.md)
- [Resolver problemas de implementação do OpenShift](./openshift-troubleshooting.md)
- [Guia de introdução OKD](https://docs.okd.io)
