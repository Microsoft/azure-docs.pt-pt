---
title: Implementar OKD em Azure
description: Implemente OKD em Azure.
author: haroldwongms
manager: joraio
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: dc14b10081cf175581d29524dcea60c52763b03c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667220"
---
# <a name="deploy-okd-in-azure"></a>Implementar OKD em Azure

Pode utilizar uma de duas formas de implementar OKD (anteriormente OpenShift Origin) em Azure:

- Pode implantar manualmente todos os componentes de infraestrutura Azure necessários e, em seguida, seguir a [documentação OKD](https://docs.okd.io).
- Também pode utilizar um modelo de [Gestor de Recursos](https://github.com/Microsoft/openshift-origin) existente que simplifica a implementação do cluster OKD.

## <a name="deploy-using-the-okd-template"></a>Implementar usando o modelo OKD

Para implementar usando o modelo de Gestor de Recursos, utilize um ficheiro de parâmetros para fornecer os parâmetros de entrada. Para personalizar ainda mais a implementação, forque o repo GitHub e altere os itens apropriados.

Algumas opções comuns de personalização incluem, mas não se limitam a:

- Tamanho VM de bastião (variável em azuredeploy.jsem)
- Convenções de nomeação (variáveis em azuredeploy.js)
- Especificidades do cluster OpenShift, modificadas através do ficheiro hostes (deployOpenShift.sh)

O [modelo OKD](https://github.com/Microsoft/openshift-origin) tem vários ramos disponíveis para diferentes versões de OKD.  Com base nas suas necessidades, pode desdobrar-se diretamente a partir do repo ou pode forquilição do repo e fazer alterações personalizadas antes de ser implementado.

Utilize o `appId` valor do principal de serviço que criou anteriormente para o `aadClientId` parâmetro.

Segue-se um exemplo de um ficheiro de parâmetros denominado azuredeploy.parameters.jscom todas as entradas necessárias.

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

Substitua os parâmetros pelas suas informações específicas.

Diferentes versões podem ter diferentes parâmetros, por isso verifique os parâmetros necessários para o ramo que utiliza.

### <a name="deploy-using-azure-cli"></a>Implementar com a CLI do Azure


> [!NOTE] 
> O seguinte comando requer Azure CLI 2.0.8 ou mais tarde. Pode verificar a versão CLI com o `az --version` comando. Para atualizar a versão CLI, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

O exemplo seguinte implanta o cluster OKD e todos os recursos relacionados num grupo de recursos chamado openshiftrg, com um nome de implantação do myOpenShiftCluster. O modelo é referenciado diretamente a partir do repo GitHub enquanto usa um ficheiro de parâmetros locais nomeado azuredeploy.parameters.js.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A colocação leva pelo menos 30 minutos para terminar, com base no número total de nós implantados. O URL da consola OpenShift e o nome DNS das impressões principais OpenShift para o terminal quando a implementação terminar. Em alternativa, pode visualizar a secção de saídas da implantação a partir do portal Azure.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Se não quiser amarrar a linha de comando à espera que a implantação esteja concluída, adicione `--no-wait` como uma das opções para a implementação do grupo. A saída da implantação pode ser recuperada a partir do portal Azure na secção de implantação do grupo de recursos.

## <a name="connect-to-the-okd-cluster"></a>Ligue-se ao cluster OKD

Quando a implementação terminar, ligue-se à consola OpenShift com o seu navegador utilizando o `OpenShift Console Url` . Em alternativa, pode SSH ao mestre OKD. Segue-se um exemplo que utiliza a saída a partir da implantação:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar os recursos

Utilize o comando de eliminação do [grupo az](/cli/azure/group) para remover o grupo de recursos, o cluster OpenShift e todos os recursos relacionados quando já não são necessários.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Passos seguintes

- [Tarefas pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Resolução de problemas OpenShift deployment](./openshift-container-platform-3x-troubleshooting.md)
- [Começar com OKD](https://docs.okd.io)
