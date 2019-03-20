---
title: Implementar o OpenShift Container Platform no Azure | Documentos da Microsoft
description: Implemente o OpenShift Container Platform no Azure.
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
ms.date: 02/04/2018
ms.author: haroldw
ms.openlocfilehash: 1d869d822cdeb0051836a5fc5f01eb69c523f9e3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995535"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Implementar o OpenShift Container Platform no Azure

Pode utilizar um dos vários métodos para implementar o OpenShift Container Platform no Azure:

- Pode implementar manualmente os componentes de infraestrutura do Azure necessários e, em seguida, siga os [documentação do OpenShift Container Platform](https://docs.openshift.com/container-platform).
- Também pode utilizar existente [modelo do Resource Manager](https://github.com/Microsoft/openshift-container-platform/) que simplifica a implementação do cluster OpenShift Container Platform.
- Outra opção consiste em utilizar o [oferta do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Para todas as opções, é necessária uma subscrição de Red Hat. Durante a implantação, a instância de Red Hat Enterprise Linux está registada para a subscrição do Red Hat e anexada ao ID de agrupamento que contém a elegibilidade para o OpenShift Container Platform.
Certifique-se de que tem um válidas Red Hat subscrição Manager (RHSM) nome de utilizador, palavra-passe e ID do conjunto. Pode utilizar uma chave de ativação, o ID da organização e o ID do conjunto. Pode verificar estas informações ao iniciar sessão para https://access.redhat.com.

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Implementar com o modelo de Gestor de recursos do OpenShift Container Platform

Para implementar com o modelo do Resource Manager, utiliza um ficheiro de parâmetros para fornecer os parâmetros de entrada. Para personalizar ainda mais a implementação, bifurcar o repositório do GitHub e altere os itens adequados.

Algumas opções de personalização comuns incluem, mas não estão limitadas a:

- Tamanho de VM de bastion (variável no azuredeploy. JSON)
- Convenções de nomenclatura (variáveis no azuredeploy. JSON)
- Especificações de cluster do OpenShift, modificado através do ficheiro de anfitriões (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Configurar o ficheiro de parâmetros

O [modelo de plataforma de contentores do OpenShift](https://github.com/Microsoft/openshift-container-platform) tem vários ramos disponíveis para versões diferentes do OpenShift Container Platform.  Com base nas suas necessidades, pode implementar diretamente a partir do repositório ou pode bifurcar o repositório e fazer alterações personalizadas para os modelos ou scripts antes de implementar.

Utilize o `appId` valor do principal de serviço que criou anteriormente para o `aadClientId` parâmetro.

O exemplo seguinte mostra um ficheiro de parâmetros com o nome azuredeploy com todas as entradas necessárias.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
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
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
        },
        "rhsmBrokerPoolId": {
            "value": "{Pool ID}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
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
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "routing.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "openshiftvnet"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "mastersubnet"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "infrasubnet"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "nodesubnet"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/masterinfrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.201"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        },
        "proxySettings": {
            "value": "none"
        },
        "httpProxyEntry": {
            "value": "none"
        },
        "httpsProxyEntry": {
            "value": "none"
        },
        "noProxyEntry": {
            "value": "none"
        }
    }
}
```

Substitua os parâmetros com as suas informações específicas.

Diferentes versões podem ter diferentes parâmetros, pelo que deve verificar os parâmetros necessários para o ramo que utilizar.

### <a name="deploy-using-azure-cli"></a>Implementar com a CLI do Azure

> [!NOTE] 
> Requer que o seguinte comando da CLI do Azure 2.0.8 ou posterior. Pode verificar a versão da CLI com o `az --version` comando. Para atualizar a versão da CLI, veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

O exemplo seguinte implementa o cluster de OpenShift e todos os recursos relacionados num grupo de recursos com o nome openshiftrg, com um nome de implementação de myOpenShiftCluster. O modelo é referenciado diretamente a partir do repositório do GitHub e um local parâmetros ficheiro azuredeploy. ini ao ficheiro é utilizado.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A implantação se, pelo menos, 30 minutos a concluir, com base no número total de nós implementados e opções configuradas. O FQDN de DNS de Bastion e o URL da consola do OpenShift imprime terminal, quando a conclusão da implementação.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Se não pretender obstruir a linha de comandos a aguardar que a implementação concluir, adicionar `--no-wait` como uma das opções para a implementação de grupo. O resultado da implementação pode ser obtido a partir do portal do Azure, na secção de implementação para o grupo de recursos.
 
## <a name="deploy-using-the-openshift-container-platform-azure-marketplace-offer"></a>Implementar com a oferta do OpenShift Container Platform Azure Marketplace

A forma mais simples de implementar o OpenShift Container Platform no Azure está a utilizar o [oferta do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Esta é a opção mais simples, mas ele também tem limitada capacidades de personalização. A oferta do Marketplace inclui as seguintes opções de configuração:

- **Nós do mestre**: Tipo de mestre de nós de três (3) com a instância configurável.
- **Infra-estrutura nós**: Tipo de três (3) infra-estrutura nós com a instância configurável.
- **Nós**: O número de nós é configurável (entre 2 e 9), bem como o tipo de instância.
- **Tipo de disco**: Discos geridos são utilizados.
- **Funcionamento em rede**: Suporte para a rede nova ou existente, bem como o intervalo CIDR personalizado.
- **CNS**: CNS pode ser ativado.
- **Métricas**: As métricas podem ser ativadas.
- **Registo**: O registo pode ser ativado.
- **Fornecedor de Cloud do Azure**: Pode ser ativado.

## <a name="connect-to-the-openshift-cluster"></a>Ligue ao cluster do OpenShift

Quando termina, a implantação, obter a ligação da secção de saída da implementação. Ligar à consola do OpenShift com o seu browser, utilizando o `OpenShift Console URL`. Em alternativa, pode encaminhar o SSH para o anfitrião de bastião. Segue-se um exemplo em que o nome de utilizador do administrador é clusteradmin e o IP público de bastion DNS FQDN é bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar recursos

Utilize o [eliminação do grupo de az](/cli/azure/group) comando para remover o grupo de recursos, o OpenShift cluster e todos os recursos relacionados quando já não forem necessários.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Passos Seguintes

- [Tarefas de pós-implementação](./openshift-post-deployment.md)
- [Resolver problemas de implementação do OpenShift no Azure](./openshift-troubleshooting.md)
- [Introdução ao OpenShift Container Platform](https://docs.openshift.com/container-platform)

### <a name="documentation-contributors"></a>Colaboradores de documentação

Obrigado a energia de Vincent (vincepower) e Alfred Sin (asinn826) por suas contribuições para manter esta documentação atualizadas!
