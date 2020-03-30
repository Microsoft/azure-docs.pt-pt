---
title: Implementar OKD em Azure
description: Implementar OKD em Azure.
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
ms.openlocfilehash: 6ecae09b0efbc0e35e591b31dbb54fb49ce101b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035409"
---
# <a name="deploy-okd-in-azure"></a>Implementar OKD em Azure

Você pode usar uma de duas maneiras de implementar OKD (antiga OpenShift Origin) em Azure:

- Pode implantar manualmente todos os componentes de infraestrutura Azure necessários e, em seguida, seguir a [documentação OKD](https://docs.okd.io).
- Também pode usar um modelo de Gestor de [Recursos](https://github.com/Microsoft/openshift-origin) existente que simplifica a implementação do cluster OKD.

## <a name="deploy-using-the-okd-template"></a>Implementar usando o modelo OKD

Para implementar utilizando o modelo de Gestor de Recursos, utilize um ficheiro de parâmetros para fornecer os parâmetros de entrada. Para personalizar ainda mais a implementação, bifurque o repo GitHub e altere os itens apropriados.

Algumas opções comuns de personalização incluem, mas não se limitam a:

- Tamanho Bastião VM (variável em azuredeploy.json)
- Convenções de nomeação (variáveis em azuredeploy.json)
- Especificidades do cluster OpenShift, modificados através de ficheiros anfitriões (deployOpenShift.sh)

O [modelo OKD](https://github.com/Microsoft/openshift-origin) tem vários ramos disponíveis para diferentes versões de OKD.  Com base nas suas necessidades, pode ser implantado diretamente a partir do repo ou pode bifurcar o repo e fazer alterações personalizadas antes de ser implementado.

Utilize `appId` o valor do diretor de serviço `aadClientId` que criou anteriormente para o parâmetro.

Segue-se um exemplo de um ficheiro de parâmetros chamado azuredeploy.parameters.json com todas as inputs necessárias.

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

As versões diferentes podem ter parâmetros diferentes, por isso verifique os parâmetros necessários para o ramo que utiliza.

### <a name="deploy-using-azure-cli"></a>Implementar com a CLI do Azure


> [!NOTE] 
> O seguinte comando requer Azure CLI 2.0.8 ou posterior. Pode verificar a versão CLI com o `az --version` comando. Para atualizar a versão CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

O exemplo seguinte implanta o cluster OKD e todos os recursos relacionados num grupo de recursos chamado openshiftrg, com um nome de implementação do myOpenShiftCluster. O modelo é referenciado diretamente a partir do repo GitHub enquanto utiliza um ficheiro de parâmetros locais chamado azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A implantação leva pelo menos 30 minutos para terminar, com base no número total de nós implantados. O URL da consola OpenShift e o nome DNS do master OpenShift imprimem-se no terminal quando a implementação terminar. Em alternativa, pode visualizar a secção de saídas da implantação a partir do portal Azure.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Se não quiser ligar a linha de comando à espera `--no-wait` que a implantação esteja concluída, adicione como uma das opções para a implantação do grupo. A saída da implantação pode ser recuperada do portal Azure na secção de implantação do grupo de recursos.

## <a name="connect-to-the-okd-cluster"></a>Ligue-se ao cluster OKD

Quando a implementação terminar, ligue-se à consola `OpenShift Console Url`OpenShift com o seu navegador utilizando o . Em alternativa, pode SSH para o mestre OKD. Segue-se um exemplo que utiliza a saída a partir da implantação:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar recursos

Use o [grupo AZ eliminar](/cli/azure/group) o comando para remover o grupo de recursos, o cluster OpenShift, e todos os recursos relacionados quando já não são necessários.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Passos seguintes

- [Tarefas de pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Implantação openShift de resolução de problemas](./openshift-container-platform-3x-troubleshooting.md)
- [Começando com OKD](https://docs.okd.io)
