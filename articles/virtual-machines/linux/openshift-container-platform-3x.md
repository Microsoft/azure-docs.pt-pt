---
title: Implementar plataforma de contentores openshift 3.11 em Azure
description: Implementar a Plataforma de Contentores OpenShift 3.11 em Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: ce3f56530a7bad6eecd4c2edd3b21debe8c75a24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102214155"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Implementar plataforma de contentores openshift 3.11 em Azure

Pode utilizar um dos vários métodos para implantar a Plataforma de Contentores OpenShift 3.11 em Azure:

- Pode implantar manualmente os componentes de infraestrutura Azure necessários e, em seguida, seguir a documentação da [Plataforma de Contentores OpenShift](https://docs.openshift.com/container-platform).
- Também pode utilizar um modelo de [Gestor de Recursos](https://github.com/Microsoft/openshift-container-platform/) existente que simplifica a implantação do cluster da Plataforma de Contentores OpenShift.
- Outra opção é utilizar a oferta do [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)

Para todas as opções, é necessária uma subscrição do Red Hat. Durante a implementação, a instância Red Hat Enterprise Linux está registada na assinatura Red Hat e anexada ao Pool ID que contém os direitos da Plataforma de Contentores OpenShift.
Certifique-se de que tem um nome de utilizador válido do Red Hat Subscription Manager (RHSM), palavra-passe e ID do Pool. Você pode usar uma chave de ativação, ID org e Pool ID. Pode verificar esta informação ao iniciar sessão em https://access.redhat.com .


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Implementar utilizando o modelo de gestor de recursos da plataforma de contentores openshift 3.11

### <a name="private-clusters"></a>Aglomerados Privados

A implantação de clusters openshift privados requer mais do que simplesmente não ter um IP público associado ao equilibrador de carga principal (consola web) ou ao equilibrador de infravermelhos (router).  Um cluster privado geralmente usa um servidor DNS personalizado (não o padrão Azure DNS), um nome de domínio personalizado (como contoso.com) e redes virtuais pré-definidas).  Para clusters privados, é necessário configurar a sua rede virtual com todas as sub-redes apropriadas e as definições de servidor DNS com antecedência.  Em seguida, utilize **a referência DeresubnetReference** existente , **indemnizadoraInfraSubnetReference**, **a referência existenteCnsSubnetReference,** e a referência **ExistenteNodeSubnetReference** para especificar a sub-rede existente para utilização pelo cluster.

Se o mestre privado for selecionado **(masterClusterType**=privado), um IP privado estático precisa de ser especificado para **masterPrivateClusterIp**.  Este IP será atribuído à parte frontal do equilibral de carga principal.  O IP deve estar dentro do CIDR para a sub-rede principal e não para a utilização.  **masterClusterDnsType** deve ser definido como "personalizado" e o nome principal DNS deve ser fornecido para **masterClusterDns**.  O nome DNS deve mapear para o IP privado estático e será usado para aceder à consola nos nós principais.

Se for selecionado um router privado **(routerClusterType**=privado), é necessário especificar um IP privado estático para **o routerPrivateClusterIp**.  Este IP será atribuído à parte frontal do balançador de carga infra.  O IP deve estar dentro do CIDR para a sub-rede de infra-redes e não para ser utilizado.  **encaminhamentoSubDomainType** deve ser definido como "personalizado" e o nome DNS wildcard para encaminhamento deve ser fornecido para **o encaminhamentoSubDomain**.  

Se os mestres privados e o router privado forem selecionados, o nome de domínio personalizado também deve ser introduzido para **o domínioName**

Após uma implementação bem sucedida, o Nó de Bastião é o único nó com um IP público em que pode ssh.  Mesmo que os nós mestres estejam configurados para acesso público, não estão expostos para acesso ssh.

Para implementar usando o modelo de Gestor de Recursos, utilize um ficheiro de parâmetros para fornecer os parâmetros de entrada. Para personalizar ainda mais a implementação, forque o repo GitHub e altere os itens apropriados.

Algumas opções comuns de personalização incluem, mas não se limitam a:

- Tamanho VM de bastião (variável em azuredeploy.jsem)
- Convenções de nomeação (variáveis em azuredeploy.js)
- Especificidades do cluster OpenShift, modificadas através do ficheiro hostes (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Configure o ficheiro de parâmetros

O [modelo da Plataforma de Contentores OpenShift](https://github.com/Microsoft/openshift-container-platform) tem vários ramos disponíveis para diferentes versões da Plataforma de Contentores OpenShift.  Com base nas suas necessidades, pode implementar diretamente a partir do repo ou pode bifurcar o repo e fazer alterações personalizadas nos modelos ou scripts antes de implementar.

Utilize o `appId` valor do principal de serviço que criou anteriormente para o `aadClientId` parâmetro.

O exemplo a seguir mostra um ficheiro de parâmetros nomeado azuredeploy.parameters.jscom todas as entradas necessárias.

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

Substitua os parâmetros pelas suas informações específicas.

Diferentes versões podem ter diferentes parâmetros, por isso verifique os parâmetros necessários para o ramo que utiliza.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy.Parameters.jsno ficheiro explicado

| Propriedade | Descrição | Opções válidas | Valor Predefinido |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL para artefactos (json, scripts, etc.) |  |  https: \/ /raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Região de Azure para implantar recursos para |  |  |
| `masterVmSize` | Tamanho do Master VM. Selecione de um dos tamanhos de VM permitidos listados no azuredeploy.jsno ficheiro |  | Standard_E2s_v3 |
| `infraVmSize` | Tamanho do Infra VM. Selecione de um dos tamanhos de VM permitidos listados no azuredeploy.jsno ficheiro |  | Standard_D4s_v3 |
| `nodeVmSize` | Tamanho do Nó de Aplicação VM. Selecione de um dos tamanhos de VM permitidos listados no azuredeploy.jsno ficheiro |  | Standard_D4s_v3 |
| `cnsVmSize` | Tamanho do nó de armazenamento nativo do recipiente (CNS) VM. Selecione de um dos tamanhos de VM permitidos listados no azuredeploy.jsno ficheiro |  | Standard_E4s_v3 |
| `osImageType` | A imagem RHEL para usar. predefinição: On-Demand; mercado: imagem de terceiros | predefinição <br> marketplace | predefinição |
| `marketplaceOsImage` | Se `osImageType` for marketplace, insira os valores adequados para 'publisher', 'offer', 'sku', 'versão' da oferta de mercado. Este parâmetro é um tipo de objeto |  |  |
| `storageKind` | O tipo de armazenamento a ser usado  | gerido<br> não gerido | gerido |
| `openshiftClusterPrefix` | O prefixo do cluster usado para configurar os hostnames para todos os nós.  Entre 1 e 20 caracteres |  | mycluster |
| `minoVersion` | A versão menor da Plataforma de Contentores OpenShift 3.11 para implantar |  | 69 |
| `masterInstanceCount` | Número de nós Masters para implantar | 1, 3, 5 | 3 |
| `infraInstanceCount` | Número de nós de infravermelhos para implantar | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Número de nós para implantar | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Número de nós cns para implantar | 3, 4 | 3 |
| `osDiskSize` | Tamanho do disco de SO para o VM (em GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Tamanho do disco de dados para anexar aos nós para o volume docker (em GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Tamanho do disco de dados a anexar aos nosdes de SNC para utilização por glusterfs (em GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Nome de utilizador admin tanto para o login de OS (VM) como para o utilizador inicial OpenShift |  | ocpadmin |
| `enableMetrics` | Ativar métricas. As métricas requerem mais recursos para selecionar o tamanho adequado para Infra VM | true <br> false | false |
| `enableLogging` | Ativar o registo. cápsula de elasticsearca requer 8 GB de RAM para selecionar o tamanho adequado para Infra VM | true <br> false | false |
| `enableCNS` | Ativar o armazenamento nativo do contentor | true <br> false | false |
| `rhsmUsernameOrOrgId` | Nome de utilizador ou ID do gestor de subscrição de chapéu vermelho ou iD da organização |  |  |
| `rhsmPoolId` | O ID do Gestor de Assinaturas red Hat que contém os seus direitos OpenShift para os acenos de computação |  |  |
| `rhsmBrokerPoolId` | O ID do Pool Pool do Red Hat Que contém os seus direitos OpenShift para mestres e nódulos infra. Se você não tem diferentes IDs de piscina, insira o mesmo ID da piscina que 'rhsmPoolId' |  |
| `sshPublicKey` | Copie aqui a sua Chave Pública SSH |  |  |
| `keyVaultSubscriptionId` | O ID de subscrição da subscrição que contém o Cofre-Chave |  |  |
| `keyVaultResourceGroup` | O nome do Grupo de Recursos que contém o Cofre de Chaves |  |  |
| `keyVaultName` | O nome do Cofre-Chave que criaste |  |  |
| `enableAzure` | Ativar o Fornecedor de Nuvem Azure | true <br> false | true |
| `aadClientId` | ID do cliente do Azure Ative Directory também conhecido como ID de aplicação para diretor de serviço |  |  |
| `domainName` | Nome do nome de domínio personalizado a utilizar (se aplicável). Definido para "nenhum" se não implementar cluster totalmente privado |  | nenhum |
| `masterClusterDnsType` | Tipo de domínio para consola web OpenShift. 'predefinição' utilizará a etiqueta DNS do IP principal infra público. 'costume' permite-lhe definir o seu próprio nome | predefinição <br> personalizadas | predefinição |
| `masterClusterDns` | O nome DNS personalizado para usar para aceder à consola web OpenShift se selecionou 'custom' para `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Se estiver definido para 'nipio', `routingSubDomain` utilizará nip.io.  Use 'personalizado' se tiver o seu próprio domínio que pretende usar para encaminhamento | nipio <br> personalizadas | nipio |
| `routingSubDomain` | O nome DNS wildcard que pretende usar para encaminhamento se tiver selecionado 'custom' para `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Selecione se usa uma Rede Virtual existente ou cria uma nova Rede Virtual | existente <br> novo | novo |
| `virtualNetworkResourceGroupName` | Nome do Grupo de Recursos para a nova Rede Virtual se selecionar 'novo' para `virtualNetworkNewOrExisting` |  | grupo de recursos().nome |
| `virtualNetworkName` | O nome da nova Rede Virtual para criar se selecionar 'novo' para `virtualNetworkNewOrExisting` |  | turnos de turno aberto |
| `addressPrefixes` | Prefixo de endereço da nova rede virtual |  | 10.0.0.0/14 |
| `masterSubnetName` | O nome da sub-rede principal |  | mastersubnet |
| `masterSubnetPrefix` | CIDR usado para a sub-rede principal - precisa ser um subconjunto do endereçoPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | O nome da sub-rede infra |  | infra-subnet |
| `infraSubnetPrefix` | CIDR utilizado para a sub-rede de infra-redes - precisa de ser um subconjunto do endereçoPrefixo |  | 10.2.0.0/16 |
| `nodeSubnetName` | O nome da sub-rede do nó |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR usado para a sub-rede do nó - precisa ser um subconjunto do endereçoPrefixo |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Referência completa à sub-rede existente para nós mestres. Não é necessário se criar novo vNet / Subnet |  |  |
| `existingInfraSubnetReference` | Referência completa à sub-rede existente para os nós infra. Não é necessário se criar novo vNet / Subnet |  |  |
| `existingCnsSubnetReference` | Referência completa à sub-rede existente para nós DE SNC. Não é necessário se criar novo vNet / Subnet |  |  |
| `existingNodeSubnetReference` | Referência completa à sub-rede existente para nós computacional. Não é necessário se criar novo vNet / Subnet |  |  |
| `masterClusterType` | Especificar se o cluster utiliza nós de mestres privados ou públicos. Se forem escolhidos em privado, os nós principais não serão expostos à Internet através de um IP público. Em vez disso, utilizará o IP privado especificado no `masterPrivateClusterIp` | público <br> privado | público |
| `masterPrivateClusterIp` | Se forem selecionados nós-mestre privados, um endereço IP privado deve ser especificado para utilização pelo equilibrador de carga interno para os nós principais. Este IP estático deve estar dentro do bloco CIDR para a sub-rede principal e ainda não está a ser utilizado. Se os nosdes de mestre público forem selecionados, este valor não será usado, mas ainda deve ser especificado |  | 10.1.0.200 |
| `routerClusterType` | Especificar se o cluster utiliza nóns de infravermelhos privados ou públicos. Se forem escolhidos em privado, os nós de infravermelhos não serão expostos à Internet através de um IP público. Em vez disso, utilizará o IP privado especificado no `routerPrivateClusterIp` | público <br> privado | público |
| `routerPrivateClusterIp` | Se forem selecionados os nós de infravermelhos privados, deve então ser especificado um endereço IP privado para utilização pelo balançador interno de carga para os nós infra. Este IP estático deve estar dentro do bloco CIDR para a sub-rede e ainda não está a ser utilizado. Se os nosmos de infravermelhos públicos forem selecionados, este valor não será usado, mas ainda deve ser especificado |  | 10.2.0.200 |
| `routingCertType` | Use certificado personalizado para o domínio de encaminhamento ou o certificado auto-assinado por defeito - siga as instruções na secção **de Certificados Personalizados** | auto-assinado <br> personalizadas | auto-assinado |
| `masterCertType` | Use certificado personalizado para domínio principal ou o certificado auto-assinado por defeito - siga as instruções na secção **de Certificados Personalizados** | auto-assinado <br> personalizadas | auto-assinado |

<br>

### <a name="deploy-using-azure-cli"></a>Implementar com a CLI do Azure

> [!NOTE] 
> O seguinte comando requer Azure CLI 2.0.8 ou mais tarde. Pode verificar a versão CLI com o `az --version` comando. Para atualizar a versão CLI, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

O exemplo seguinte implementa o cluster OpenShift e todos os recursos relacionados num grupo de recursos chamado openshiftrg, com um nome de implantação do myOpenShiftCluster. O modelo é referenciado diretamente a partir do repo GitHub, e um ficheiro de parâmetros locais nomeado azuredeploy.parameters.jsno ficheiro é usado.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A colocação demora pelo menos 60 minutos a ser concluída, com base no número total de nós implantados e opções configuradas. O Bastion DNS FQDN e URL da consola OpenShift imprimem-se ao terminal quando a implementação terminar.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Se não quiser amarrar a linha de comando à espera que a implantação esteja concluída, adicione `--no-wait` como uma das opções para a implementação do grupo. A saída da implantação pode ser recuperada a partir do portal Azure na secção de implantação do grupo de recursos.

## <a name="connect-to-the-openshift-cluster"></a>Ligue-se ao cluster OpenShift

Quando a colocação terminar, recupere a ligação da secção de saída da implantação. Ligue a consola OpenShift ao seu navegador utilizando o **URL da consola OpenShift**. Você também pode SSH para o anfitrião do Bastião. Segue-se um exemplo em que o nome de utilizador de administração é clusteradmin e o público de bastião IP DNS FQDN é bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar os recursos

Utilize o comando de eliminação do [grupo az](/cli/azure/group) para remover o grupo de recursos, o cluster OpenShift e todos os recursos relacionados quando já não são necessários.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Passos seguintes

- [Tarefas pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Resolução de problemas OpenShift implantação em Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Começando com plataforma de recipientes openshift](https://docs.openshift.com)
