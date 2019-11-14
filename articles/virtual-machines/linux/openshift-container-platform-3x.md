---
title: Implantar a plataforma de contêiner OpenShift 3,11 no Azure
description: Implante a plataforma de contêiner OpenShift 3,11 no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 56607de57939be769b1951f0eee9078c46d610c0
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035452"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Implantar a plataforma de contêiner OpenShift 3,11 no Azure

Você pode usar um dos vários métodos para implantar a plataforma de contêiner do OpenShift 3,11 no Azure:

- Você pode implantar manualmente os componentes necessários da infraestrutura do Azure e, em seguida, seguir a [documentação da plataforma de contêiner do OpenShift](https://docs.openshift.com/container-platform).
- Você também pode usar um modelo existente do [Resource Manager](https://github.com/Microsoft/openshift-container-platform/) que simplifica a implantação do cluster da plataforma de contêiner do OpenShift.
- Outra opção é usar a [oferta do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Para todas as opções, é necessária uma assinatura do Red Hat. Durante a implantação, a instância de Red Hat Enterprise Linux é registrada na assinatura do Red Hat e anexada à ID do pool que contém os direitos para a plataforma de contêiner OpenShift.
Verifique se você tem um nome de usuário, senha e ID de pool válidos do RHSM (Red Hat Subscription Manager). Você pode usar uma chave de ativação, ID da organização e ID do pool. Você pode verificar essas informações entrando em https://access.redhat.com.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Implantar usando o modelo do Gerenciador de recursos da plataforma de contêiner OpenShift 3,11

### <a name="private-clusters"></a>Clusters privados

Implantar clusters OpenShift privados requer mais do que simplesmente não ter um IP público associado ao balanceador de carga mestre (console Web) ou ao balanceador de carga (roteador).  Um cluster privado geralmente usa um servidor DNS personalizado (não o DNS padrão do Azure), um nome de domínio personalizado (como contoso.com) e redes virtuais predefinidas.  Para clusters privados, você precisa configurar sua rede virtual com todas as sub-redes apropriadas e as configurações do servidor DNS com antecedência.  Em seguida, use **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference**e **existingNodeSubnetReference** para especificar a sub-rede existente a ser usada pelo cluster.

Se o mestre privado estiver selecionado (**masterClusterType**= Private), um IP privado estático precisará ser especificado para **masterPrivateClusterIp**.  Esse IP será atribuído ao front-end do balanceador de carga mestre.  O IP deve estar dentro do CIDR para a sub-rede mestre e não em uso.  **masterClusterDnsType** deve ser definido como "Custom" e o nome DNS mestre deve ser fornecido para **masterClusterDns**.  O nome DNS deve mapear para o IP privado estático e será usado para acessar o console do nos nós mestres.

Se o roteador privado for selecionado (**routerClusterType**= Private), um IP privado estático precisará ser especificado para **routerPrivateClusterIp**.  Esse IP será atribuído ao front-end do balanceador de carga de infraestrutura.  O IP deve estar dentro do CIDR para a sub-rede de infraestrutura e não em uso.  **routingSubDomainType** deve ser definido como "Custom" e o nome DNS do curinga para roteamento deve ser fornecido para **routingSubDomain**.  

Se os mestres privados e o roteador privado forem selecionados, o nome de domínio personalizado também deverá ser inserido para **DomainName**

Após a implantação bem-sucedida, o nó de bastiões é o único nó com um IP público no qual você pode SSH.  Mesmo que os nós mestres estejam configurados para acesso público, eles não são expostos para acesso SSH.

Para implantar usando o modelo do Resource Manager, você usa um arquivo de parâmetros para fornecer os parâmetros de entrada. Para personalizar ainda mais a implantação, crie bifurcação do repositório GitHub e altere os itens apropriados.

Algumas opções de personalização comuns incluem, mas não se limitam a:

- Tamanho da VM de bastiões (variável em azuredeploy. JSON)
- Convenções de nomenclatura (variáveis em azuredeploy. JSON)
- OpenShift específicas do cluster, modificadas por meio do arquivo de hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Configurar o arquivo de parâmetros

O [modelo de plataforma de contêiner OpenShift](https://github.com/Microsoft/openshift-container-platform) tem vários branches disponíveis para versões diferentes da plataforma de contêiner do OpenShift.  Com base em suas necessidades, você pode implantar diretamente do repositório ou pode bifurcar o repositório e fazer alterações personalizadas nos modelos ou scripts antes da implantação.

Use o valor `appId` da entidade de serviço que você criou anteriormente para o parâmetro `aadClientId`.

O exemplo a seguir mostra um arquivo de parâmetros chamado azuredeploy. Parameters. JSON com todas as entradas necessárias.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Substitua os parâmetros por suas informações específicas.

Versões diferentes podem ter parâmetros diferentes, portanto, verifique os parâmetros necessários para a ramificação que você usa.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Arquivo Parameters. JSON explicado

| Propriedade | Descrição | Opções válidas | Valor Predefinido |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL para artefatos (JSON, scripts, etc.) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Região do Azure para implantar recursos |  |  |
| `masterVmSize` | Tamanho da VM mestre. Selecione um dos tamanhos de VM permitidos listados no arquivo azuredeploy. JSON |  | Standard_E2s_v3 |
| `infraVmSize` | Tamanho da VM de infraestrutura. Selecione um dos tamanhos de VM permitidos listados no arquivo azuredeploy. JSON |  | Standard_D4s_v3 |
| `nodeVmSize` | Tamanho da VM do nó do aplicativo. Selecione um dos tamanhos de VM permitidos listados no arquivo azuredeploy. JSON |  | Standard_D4s_v3 |
| `cnsVmSize` | Tamanho da VM do nó do armazenamento nativo do contêiner (CNS). Selecione um dos tamanhos de VM permitidos listados no arquivo azuredeploy. JSON |  | Standard_E4s_v3 |
| `osImageType` | A imagem RHEL a ser usada. defaultgallery: sob demanda; Marketplace: imagem de terceiros | defaultgallery <br> marketplace | defaultgallery |
| `marketplaceOsImage` | Se `osImageType` for Marketplace, insira os valores apropriados para ' publicador ', ' oferta ', ' SKU ', ' versão ' da oferta do Marketplace. Este parâmetro é um tipo de objeto |  |  |
| `storageKind` | O tipo de armazenamento a ser usado  | administra<br> não gerenciados | administra |
| `openshiftClusterPrefix` | Prefixo de cluster usado para configurar os nomes de host para todos os nós.  Entre 1 e 20 caracteres |  | mycluster |
| `minoVersion` | A versão secundária da plataforma de contêiner do OpenShift 3,11 a ser implantada |  | 69 |
| `masterInstanceCount` | Número de nós mestres a serem implantados | 1, 3, 5 | 3 |
| `infraInstanceCount` | Número de nós de infraestrutura a serem implantados | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Número de nós a serem implantados | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Número de nós do CNS a serem implantados | 3, 4 | 3 |
| `osDiskSize` | Tamanho do disco do sistema operacional para a VM (em GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Tamanho do disco de dados a ser anexado aos nós para o volume do Docker (em GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Tamanho do disco de dados a ser anexado aos nós do CNS para uso pelo GlusterFS (em GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Nome de usuário do administrador para o logon do sistema operacional (VM) e o usuário OpenShift inicial |  | ocpadmin |
| `enableMetrics` | Habilitar métricas. As métricas exigem mais recursos, portanto, selecione o tamanho adequado para a máquina virtual de infraestrutura | true <br> false | false |
| `enableLogging` | Habilite o registro em log. o Pod elasticsearch requer 8 GB de RAM, portanto, selecione o tamanho adequado para a VM de infraestrutura | true <br> false | false |
| `enableCNS` | Habilitar o armazenamento nativo do contêiner | true <br> false | false |
| `rhsmUsernameOrOrgId` | Nome de usuário ou ID da organização do Red Hat Subscription Manager |  |  |
| `rhsmPoolId` | A ID do pool do Gerenciador de assinaturas do Red Hat que contém seus direitos de OpenShift para nós de computação |  |  |
| `rhsmBrokerPoolId` | A ID do pool do Gerenciador de assinaturas do Red Hat que contém seus direitos de OpenShift para nós mestres e de infra-estrutura. Se você não tiver IDs de pool diferentes, insira a mesma ID de pool que ' rhsmPoolId ' |  |
| `sshPublicKey` | Copie sua chave pública SSH aqui |  |  |
| `keyVaultSubscriptionId` | A ID da assinatura que contém o Key Vault |  |  |
| `keyVaultResourceGroup` | O nome do grupo de recursos que contém o Key Vault |  |  |
| `keyVaultName` | O nome da Key Vault que você criou |  |  |
| `enableAzure` | Habilitar o provedor de nuvem do Azure | true <br> false | true |
| `aadClientId` | Azure Active Directory ID do cliente também conhecida como ID do aplicativo para a entidade de serviço |  |  |
| `domainName` | Nome do nome de domínio personalizado a ser usado (se aplicável). Defina como "nenhum" se não estiver implantando um cluster totalmente privado |  | nenhuma |
| `masterClusterDnsType` | Tipo de domínio do console Web do OpenShift. ' default ' usará o rótulo DNS do IP de infraestrutura principal. ' Custom ' permite que você defina seu próprio nome | predefinição <br> Personalizar | predefinição |
| `masterClusterDns` | O nome DNS personalizado a ser usado para acessar o console Web OpenShift se você selecionou ' Custom ' para `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Se definido como ' nipio ', `routingSubDomain` usará nip.io.  Use ' Custom ' se você tiver seu próprio domínio que deseja usar para roteamento | nipio <br> Personalizar | nipio |
| `routingSubDomain` | O nome DNS do curinga que você deseja usar para roteamento se selecionou ' personalizado ' para `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Selecione se deseja usar uma rede virtual existente ou criar uma nova rede virtual | pré-existente <br> novo | novo |
| `virtualNetworkResourceGroupName` | Nome do grupo de recursos para a nova rede virtual se você selecionou ' novo ' para `virtualNetworkNewOrExisting` |  | resourceGroup().name |
| `virtualNetworkName` | O nome da nova rede virtual a ser criada se você selecionou ' novo ' para `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Prefixo de endereço da nova rede virtual |  | 10.0.0.0/14 |
| `masterSubnetName` | O nome da sub-rede mestre |  | mastersubnet |
| `masterSubnetPrefix` | CIDR usado para a sub-rede mestre-precisa ser um subconjunto do addressPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | O nome da sub-rede de infraestrutura |  | infrasubnet |
| `infraSubnetPrefix` | CIDR usado para a sub-rede de infraestrutura – precisa ser um subconjunto do addressPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | O nome da sub-rede do nó |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR usado para a sub-rede do nó-precisa ser um subconjunto do addressPrefix |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Referência completa à sub-rede existente para nós mestres. Não é necessário se estiver criando uma nova vNet/sub-rede |  |  |
| `existingInfraSubnetReference` | Referência completa à sub-rede existente para nós de infraestrutura. Não é necessário se estiver criando uma nova vNet/sub-rede |  |  |
| `existingCnsSubnetReference` | Referência completa para a sub-rede existente para nós do CNS. Não é necessário se estiver criando uma nova vNet/sub-rede |  |  |
| `existingNodeSubnetReference` | Referência completa para a sub-rede existente para nós de computação. Não é necessário se estiver criando uma nova vNet/sub-rede |  |  |
| `masterClusterType` | Especifique se o cluster usa nós mestres privados ou públicos. Se privado for escolhido, os nós mestres não serão expostos à Internet por meio de um IP público. Em vez disso, ele usará o IP privado especificado no `masterPrivateClusterIp` | publicada <br> privado | publicada |
| `masterPrivateClusterIp` | Se nós mestres privados forem selecionados, um endereço IP privado deverá ser especificado para ser usado pelo balanceador de carga interno para nós mestres. Esse IP estático deve estar dentro do bloco CIDR para a sub-rede mestre e ainda não está em uso. Se os nós mestres públicos forem selecionados, esse valor não será usado, mas ainda deverá ser especificado |  | 10.1.0.200 |
| `routerClusterType` | Especifique se o cluster usa nós de infraestrutura privada ou pública. Se privado for escolhido, os nós de infra-estrutura não serão expostos à Internet por meio de um IP público. Em vez disso, ele usará o IP privado especificado no `routerPrivateClusterIp` | publicada <br> privado | publicada |
| `routerPrivateClusterIp` | Se os nós de infraestrutura privada forem selecionados, um endereço IP privado deverá ser especificado para ser usado pelo balanceador de carga interno para os nós de infraestrutura. Esse IP estático deve estar dentro do bloco CIDR para a sub-rede mestre e ainda não está em uso. Se os nós de infraestrutura pública forem selecionados, esse valor não será usado, mas ainda deverá ser especificado |  | 10.2.0.200 |
| `routingCertType` | Usar certificado personalizado para o domínio de roteamento ou o certificado autoassinado padrão-siga as instruções na seção de **certificados personalizados** | selfsigned <br> Personalizar | selfsigned |
| `masterCertType` | Usar certificado personalizado para o domínio mestre ou o certificado autoassinado padrão-siga as instruções na seção de **certificados personalizados** | selfsigned <br> Personalizar | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Implementar com a CLI do Azure

> [!NOTE] 
> O comando a seguir requer CLI do Azure 2.0.8 ou posterior. Você pode verificar a versão da CLI com o comando `az --version`. Para atualizar a versão da CLI, consulte [instalar CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

O exemplo a seguir implanta o cluster OpenShift e todos os recursos relacionados em um grupo de recursos chamado openshiftrg, com um nome de implantação de myOpenShiftCluster. O modelo é referenciado diretamente do repositório GitHub e um arquivo de parâmetros local chamado azuredeploy. Parameters. JSON é usado.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A implantação leva pelo menos 60 minutos para ser concluída, com base no número total de nós implantados e nas opções configuradas. O FQDN do DNS de bastiões e a URL do console do OpenShift são impressas no terminal quando a implantação é concluída.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Se você não quiser vincular a linha de comando aguardando a conclusão da implantação, adicione `--no-wait` como uma das opções para a implantação do grupo. A saída da implantação pode ser recuperada do portal do Azure na seção de implantação do grupo de recursos.

## <a name="connect-to-the-openshift-cluster"></a>Conectar-se ao cluster OpenShift

Quando a implantação for concluída, recupere a conexão da seção de saída da implantação. Conecte-se ao console do OpenShift com seu navegador usando a **URL do console do OpenShift**. Você também pode SSH para o host bastião. Veja a seguir um exemplo em que o nome de usuário do administrador é clusteradmin e o FQDN de DNS do IP público de bastiões é bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar recursos

Use o comando [AZ Group Delete](/cli/azure/group) para remover o grupo de recursos, o cluster OpenShift e todos os recursos relacionados quando eles não forem mais necessários.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Passos seguintes

- [Tarefas pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Solucionar problemas de implantação do OpenShift no Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Introdução à plataforma de contêiner do OpenShift](https://docs.openshift.com)
