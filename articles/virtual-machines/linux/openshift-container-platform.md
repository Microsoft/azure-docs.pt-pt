---
title: Implementar o OpenShift Container Platform no Azure | Documentos da Microsoft
description: Implemente o OpenShift Container Platform no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2019
ms.author: haroldw
ms.openlocfilehash: 39eea84cc9301263381533e03e8f783e0a73ea19
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999965"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Implementar o OpenShift Container Platform no Azure

Pode utilizar um dos vários métodos para implementar o OpenShift Container Platform no Azure:

- Pode implementar manualmente os componentes de infraestrutura do Azure necessários e, em seguida, siga os [documentação do OpenShift Container Platform](https://docs.openshift.com/container-platform).
- Também pode utilizar existente [modelo do Resource Manager](https://github.com/Microsoft/openshift-container-platform/) que simplifica a implementação do cluster OpenShift Container Platform.
- Outra opção consiste em utilizar o [oferta do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Para todas as opções, é necessária uma subscrição de Red Hat. Durante a implantação, a instância de Red Hat Enterprise Linux está registada para a subscrição do Red Hat e anexada ao ID de agrupamento que contém a elegibilidade para o OpenShift Container Platform.
Certifique-se de que tem um válidas Red Hat subscrição Manager (RHSM) nome de utilizador, palavra-passe e ID do conjunto. Pode utilizar uma chave de ativação, o ID da organização e o ID do conjunto. Pode verificar estas informações ao iniciar sessão para https://access.redhat.com.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Implementar com o modelo de Gestor de recursos do OpenShift Container Platform

### <a name="private-clusters"></a>Clusters de privada

Implantando clusters do OpenShift privadas requer mais do que simplesmente não ter um IP público associado ao balanceador de carga mestre (consola web) ou para a infra-estrutura (roteador) do Balanceador de carga.  Em geral, um cluster privado usa um servidor DNS personalizado (não a predefinição DNS do Azure), um nome de domínio personalizado (por exemplo, contoso.com) e rede ou redes virtuais predefinidos.  Para clusters privadas, terá de configurar a rede virtual com todas as sub-redes apropriadas e definições do servidor DNS com antecedência.  Em seguida, utilize **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference**, e  **existingNodeSubnetReference** para especificar a sub-rede existente para utilização pelo cluster.

Se for selecionado o mestre privada (**masterClusterType**= privada), tem de ser especificado para um IP privado estático **masterPrivateClusterIp**.  Este IP será atribuído para o front-end de Balanceador de carga mestre.  O IP tem de estar no CIDR para a sub-rede principal e não está em utilização.  **masterClusterDnsType** tem de ser definido para "personalizado" e o mestre de nome DNS deve ser fornecido para **masterClusterDns**.  O nome DNS tem de mapear para o IP privado estático e será utilizado para aceder à consola em nós do mestres.

Se for selecionado o roteador privada (**routerClusterType**= privada), tem de ser especificado para um IP privado estático **routerPrivateClusterIp**.  Este IP será atribuído para o front-end do Balanceador de carga infra-estrutura.  O IP tem de estar no CIDR para a infra-estrutura sub-rede e não em utilização.  **routingSubDomainType** tem de ser definido para "personalizado" e o nome DNS com carateres universais para encaminhamento deve ser fornecido para **routingSubDomain**.  

Se forem selecionados modelos de estrutura mestres privados e router privada, também é necessário introduzir o nome de domínio personalizado para **domainName**

Após a implementação com êxito, o nó de Bastion é o único nó com um IP público que pode ssh em.  Mesmo que os nós principais estão configurados para acesso público, elas não são expostas para ssh acesso.

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
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
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
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
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
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
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
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Substitua os parâmetros com as suas informações específicas.

Diferentes versões podem ter diferentes parâmetros, pelo que deve verificar os parâmetros necessários para o ramo que utilizar.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Ficheiro Parameters JSON explicado

| Propriedade | Descrição | Opções válidas | Valor Predefinido |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL para artefactos (json, scripts, etc.) |  |  https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Região do Azure para implementar recursos |  |  |
| `masterVmSize` | Tamanho da VM principal. Selecionar a partir de um dos tamanhos de VM permitidos listados no ficheiro azuredeploy. JSON |  | Standard_E2s_v3 |
| `infraVmSize` | Tamanho da infra-estrutura VM. Selecionar a partir de um dos tamanhos de VM permitidos listados no ficheiro azuredeploy. JSON |  | Standard_D4s_v3 |
| `nodeVmSize` | Tamanho da VM de nó da aplicação. Selecionar a partir de um dos tamanhos de VM permitidos listados no ficheiro azuredeploy. JSON |  | Standard_D4s_v3 |
| `cnsVmSize` | Tamanho do nó de armazenamento nativas (CNS) contentor VM. Selecionar a partir de um dos tamanhos de VM permitidos listados no ficheiro azuredeploy. JSON |  | Standard_E4s_v3 |
| `osImageType` | A imagem RHEL a utilizar. defaultgallery: Sob demanda; Marketplace: imagem de terceiros | defaultgallery <br> marketplace | defaultgallery |
| `marketplaceOsImage` | Se `osImageType` é mercado, em seguida, introduza os valores adequados para "publisher", "oferta", "sku", versão da oferta do marketplace. Este parâmetro é um tipo de objeto |  |  |
| `storageKind` | O tipo de armazenamento a ser utilizado  | Gerido<br> Não gerido | Gerido |
| `openshiftClusterPrefix` | O prefixo utilizado para configurar nomes de anfitriões para todos os nós do cluster.  Entre 1 e 20 carateres |  | meucluster |
| `minoVersion` | A versão secundária do OpenShift Container Platform 3.11 para implementar |  | 69 |
| `masterInstanceCount` | Número de nós de modelos de estrutura mestres para implementar | 1, 3, 5 | 3 |
| `infraInstanceCount` | Número de infra-estrutura nós para implementar | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Número de nós para implementar | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Número de nós de CNS para implementar | 3, 4 | 3 |
| `osDiskSize` | Tamanho do disco de SO para a VM (em GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Tamanho do disco de dados para anexar a nós para o volume do Docker (em GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Tamanho do disco de dados para ligar a nós de CNS para utilização por glusterfs (em GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Nome de utilizador de administrador para o início de sessão do sistema operacional (VM) e o utilizador inicial do OpenShift |  | ocpadmin |
| `enableMetrics` | Ative as métricas. Métricas requerem que mais recursos por isso, selecione o tamanho adequado para a VM infra-estrutura | true <br> false | false |
| `enableLogging` | Ative o registo. elasticsearch pod necessita de 8 GB de RAM por isso, selecione o tamanho adequado para a VM infra-estrutura | true <br> false | false |
| `enableCNS` | Ativar o contentor de armazenamento nativas | true <br> false | false |
| `rhsmUsernameOrOrgId` | ID de nome de utilizador do Gestor de subscrições Hat ou organização vermelho |  |  |
| `rhsmPoolId` | O ID do Red Hat Gestor de subscrições conjunto que contém a sua elegibilidade OpenShift para nós de computação |  |  |
| `rhsmBrokerPoolId` | O Red Hat Gestor de subscrições ID do conjunto que contém nós de suas elegibilidades OpenShift para modelos de estrutura mestres e infra-estrutura. Se não tiver outro conjunto de IDs, introduza o mesmo ID de agrupamento como 'rhsmPoolId' |  |
| `sshPublicKey` | Copie a chave pública SSH aqui |  |  |
| `keyVaultSubscriptionId` | O ID de subscrição da subscrição que contém o Key Vault |  |  |
| `keyVaultResourceGroup` | O nome do grupo de recursos que contém o Key Vault |  |  |
| `keyVaultName` | O nome do Cofre de chaves que criou |  |  |
| `enableAzure` | Ativar fornecedor de Cloud do Azure | true <br> false | true |
| `aadClientId` | ID do Azure Active Directory do cliente também conhecido como ID de aplicação para o Principal de serviço |  |  |
| `domainName` | Nome do nome de domínio personalizado para utilizar (se aplicável). Definido como "none" se não for implementar cluster totalmente privada |  | nenhum |
| `masterClusterDnsType` | Tipo de domínio para a consola da web de OpenShift. 'default' utilizará a etiqueta de DNS do mestre de infra-estrutura IP público. 'custom' permite-lhe definir seu próprio nome de | predefinição <br> personalizado | predefinição |
| `masterClusterDns` | O nome DNS personalizado para utilizar para aceder à consola de web do OpenShift, se tiver selecionado 'custom' para `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Se definido como 'nipio', `routingSubDomain` utilizará nip.io.  Utilizar o 'custom' se tiver o seu próprio domínio que pretende utilizar para o encaminhamento | nipio <br> personalizado | nipio |
| `routingSubDomain` | O nome DNS com carateres universais que pretende utilizar para o encaminhamento se tiver selecionado 'custom' para `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Selecione se pretende utilizar uma rede Virtual existente ou criar uma nova rede Virtual | existente <br> novo | novo |
| `virtualNetworkResourceGroupName` | Nome do grupo de recursos para a nova rede Virtual, se tiver selecionado "novo" para `virtualNetworkNewOrExisting` |  | resourceGroup().name |
| `virtualNetworkName` | O nome da nova rede Virtual para criar se tiver selecionado "novo" para `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Prefixo de endereço da nova rede virtual |  | 10.0.0.0/14 |
| `masterSubnetName` | O nome da sub-rede mestre |  | mastersubnet |
| `masterSubnetPrefix` | CIDR utilizado para a sub-rede mestre - tem de ser um subconjunto do addressPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | O nome da infra-estrutura sub-rede |  | infrasubnet |
| `infraSubnetPrefix` | CIDR utilizados para a infra-estrutura sub-rede - tem de ser um subconjunto do addressPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | O nome da sub-rede do nó |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR utilizado para a sub-rede de nó - tem de ser um subconjunto do addressPrefix |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Referência completa a sub-rede existente para nós principais. Não necessário se a criar a nova vNet / sub-rede |  |  |
| `existingInfraSubnetReference` | Completo infra-estrutura referência a sub-rede existente para nós. Não necessário se a criar a nova vNet / sub-rede |  |  |
| `existingCnsSubnetReference` | Referência completa a sub-rede existente para nós CNS. Não necessário se a criar a nova vNet / sub-rede |  |  |
| `existingNodeSubnetReference` | Referência completa a sub-rede existente para nós de computação. Não necessário se a criar a nova vNet / sub-rede |  |  |
| `masterClusterType` | Especifique se o cluster utiliza privadas ou públicas nós principais. Se for escolhida privada, os nós principais não são expostos à Internet através de um IP público. Em vez disso, ele usará o IP privado especificado no `masterPrivateClusterIp` | público <br> privado | público |
| `masterPrivateClusterIp` | Se forem selecionadas privadas nós principais, um endereço IP privado tem ser especificado para utilização pelo balanceador de carga interno para nós principais. Este IP estático tem de estar dentro do bloco de CIDR para a sub-rede mestre e ainda não estiver em utilização. Se forem selecionadas públicas nós principais, este valor não será usado, mas ainda tem de ser especificado |  | 10.1.0.200 |
| `routerClusterType` | Especifique se o cluster utiliza privado ou público infra-estrutura nós. Se for escolhida privada, a infra-estrutura de nós não são expostos à Internet através de um IP público. Em vez disso, ele usará o IP privado especificado no `routerPrivateClusterIp` | público <br> privado | público |
| `routerPrivateClusterIp` | Se privada infra-estrutura nós estão selecionados, em seguida, tem de ser especificado um endereço IP privado para utilizar pelo balanceador de carga interno para infra-estrutura nós. Este IP estático tem de estar dentro do bloco de CIDR para a sub-rede mestre e ainda não estiver em utilização. Se públicos infra-estrutura nós estão selecionados, este valor não será usado, mas ainda tem de ser especificado |  | 10.2.0.200 |
| `routingCertType` | Utilizar certificado personalizado para o domínio de encaminhamento ou o certificado autoassinado predefinido, siga as instruções em **certificados personalizados** secção | selfsigned <br> personalizado | selfsigned |
| `masterCertType` | Utilizar certificado personalizado para o domínio principal ou o certificado autoassinado predefinido, siga as instruções em **certificados personalizados** secção | selfsigned <br> personalizado | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Implementar com a CLI do Azure

> [!NOTE] 
> Requer que o seguinte comando da CLI do Azure 2.0.8 ou posterior. Pode verificar a versão da CLI com o `az --version` comando. Para atualizar a versão da CLI, veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

O exemplo seguinte implementa o cluster de OpenShift e todos os recursos relacionados num grupo de recursos com o nome openshiftrg, com um nome de implementação de myOpenShiftCluster. O modelo é referenciado diretamente a partir do repositório do GitHub e um local parâmetros ficheiro azuredeploy. ini ao ficheiro é utilizado.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Após a implantação, pelo menos, 60 minutos a concluir, com base no número total de nós implementados e opções configuradas. O FQDN de DNS de Bastion e o URL da consola do OpenShift imprime terminal, quando a conclusão da implementação.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Se não pretender obstruir a linha de comandos a aguardar que a implementação concluir, adicionar `--no-wait` como uma das opções para a implementação de grupo. O resultado da implementação pode ser obtido a partir do portal do Azure, na secção de implementação para o grupo de recursos.

## <a name="connect-to-the-openshift-cluster"></a>Ligue ao cluster do OpenShift

Quando termina, a implantação, obter a ligação da secção de saída da implementação. Ligar à consola do OpenShift com o seu browser, utilizando o **URL de Console do OpenShift**. Pode também SSH para o anfitrião de bastião. Segue-se um exemplo em que o nome de utilizador do administrador é clusteradmin e o IP público de bastion DNS FQDN é bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

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
