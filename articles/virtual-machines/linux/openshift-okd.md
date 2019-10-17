---
title: Implantar o OKD no Azure | Microsoft Docs
description: Implante o OKD no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: 66d17fb2a96bbb5b1dcb51151242f014b4116b86
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390535"
---
# <a name="deploy-okd-in-azure"></a>Implantar o OKD no Azure

Você pode usar uma das duas maneiras de implantar o OKD (anteriormente OpenShift Origin) no Azure:

- Você pode implantar manualmente todos os componentes necessários da infraestrutura do Azure e, em seguida, seguir a [documentação do OKD](https://docs.okd.io).
- Você também pode usar um modelo existente do [Resource Manager](https://github.com/Microsoft/openshift-origin) que simplifica a implantação do cluster OKD.

## <a name="deploy-using-the-okd-template"></a>Implantar usando o modelo OKD

Para implantar usando o modelo do Resource Manager, você usa um arquivo de parâmetros para fornecer os parâmetros de entrada. Para personalizar ainda mais a implantação, crie bifurcação do repositório GitHub e altere os itens apropriados.

Algumas opções de personalização comuns incluem, mas não se limitam a:

- Tamanho da VM de bastiões (variável em azuredeploy. JSON)
- Convenções de nomenclatura (variáveis em azuredeploy. JSON)
- OpenShift específicas do cluster, modificadas por meio do arquivo de hosts (deployOpenShift.sh)

O [modelo OKD](https://github.com/Microsoft/openshift-origin) tem vários branches disponíveis para diferentes versões do OKD.  Com base em suas necessidades, você pode implantar diretamente do repositório ou pode bifurcar o repositório e fazer alterações personalizadas antes da implantação.

Use o valor `appId` da entidade de serviço que você criou anteriormente para o parâmetro `aadClientId`.

Veja a seguir um exemplo de um arquivo de parâmetros chamado azuredeploy. Parameters. JSON com todas as entradas necessárias.

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

Substitua os parâmetros por suas informações específicas.

Versões diferentes podem ter parâmetros diferentes, portanto, verifique os parâmetros necessários para a ramificação que você usa.

### <a name="deploy-using-azure-cli"></a>Implementar com a CLI do Azure


> [!NOTE] 
> O comando a seguir requer CLI do Azure 2.0.8 ou posterior. Você pode verificar a versão da CLI com o comando `az --version`. Para atualizar a versão da CLI, consulte [instalar CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

O exemplo a seguir implanta o cluster OKD e todos os recursos relacionados em um grupo de recursos chamado openshiftrg, com um nome de implantação de myOpenShiftCluster. O modelo é referenciado diretamente do repositório GitHub ao usar um arquivo de parâmetros local chamado azuredeploy. Parameters. JSON.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A implantação leva pelo menos 30 minutos para ser concluída, com base no número total de nós implantados. A URL do console do OpenShift e o nome DNS do mestre OpenShift são impressas no terminal quando a implantação é concluída. Como alternativa, você pode exibir a seção de saídas da implantação do portal do Azure.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Se você não quiser vincular a linha de comando aguardando a conclusão da implantação, adicione `--no-wait` como uma das opções para a implantação do grupo. A saída da implantação pode ser recuperada do portal do Azure na seção de implantação do grupo de recursos.

## <a name="connect-to-the-okd-cluster"></a>Conectar-se ao cluster OKD

Quando a implantação for concluída, conecte-se ao console do OpenShift com seu navegador usando o `OpenShift Console Url`. Como alternativa, você pode fazer SSH para o mestre OKD. Veja a seguir um exemplo que usa a saída da implantação:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar recursos

Use o comando [AZ Group Delete](/cli/azure/group) para remover o grupo de recursos, o cluster OpenShift e todos os recursos relacionados quando eles não forem mais necessários.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Passos seguintes

- [Tarefas pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Solucionar problemas de implantação do OpenShift](./openshift-container-platform-3x-troubleshooting.md)
- [Introdução ao OKD](https://docs.okd.io)
