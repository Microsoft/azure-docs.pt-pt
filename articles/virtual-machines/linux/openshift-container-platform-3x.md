---
title: Implementar plataforma de contentores OpenShift 3.11 em Azure
description: Implementar plataforma de contentores OpenShift 3.11 em Azure.
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
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: 7fd1c381ecd2b7dba4c77a025cb0332ace4147bf
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673669"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Implementar plataforma de contentores OpenShift 3.11 em Azure

Pode utilizar um dos vários métodos para implantar a Plataforma de Contentores OpenShift 3.11 em Azure:

- Pode implantar manualmente os componentes de infraestrutura Azure necessários e, em seguida, seguir a documentação da Plataforma de [Contentores OpenShift](https://docs.openshift.com/container-platform).
- Também pode utilizar um modelo de Gestor de [Recursos](https://github.com/Microsoft/openshift-container-platform/) existente que simplifica a implementação do cluster openshift container platform.
- Outra opção é utilizar a [oferta do Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)

Para todas as opções, é necessária uma subscrição do Chapéu Vermelho. Durante a implementação, a instância Red Hat Enterprise Linux está registada na subscrição do Red Hat e anexa da Id pool que contém os direitos para a Plataforma de Contentores OpenShift.
Certifique-se de que tem um nome de utilizador válido do Red Hat Subscription Manager (RHSM), palavra-passe e ID de Pool. Você pode usar uma chave de ativação, id org e ID de piscina. Pode verificar esta informação https://access.redhat.cominserindo-se em .


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Implementar utilizando o modelo 3.11 do Gestor de Recursos da Plataforma de Contentores OpenShift

### <a name="private-clusters"></a>Clusters Privados

A implantação de clusters OpenShift privados requer mais do que apenas não ter um IP público associado ao master load balancer (consola web) ou ao equilibrador de carga de infravermelhos (router).  Um cluster privado geralmente usa um servidor DNS personalizado (não o DNS de padrão Do Azure), um nome de domínio personalizado (como contoso.com) e rede virtual pré-definida( s).  Para clusters privados, é necessário configurar a sua rede virtual com todas as subredes apropriadas e definições de servidorDNs com antecedência.  Em seguida, utilize a **referência mastersubnet ,** **infra-subnetReference existente,** a referência existente **cnsSubnet ,** e a referência **nonodeSubnet** existente para especificar a subnet existente para utilização pelo cluster.

Se for selecionado um mestrado privado **(masterClusterType**=private), é necessário especificar um IP privado estático para **masterPrivateClusterIp**.  Este IP será atribuído na parte frontal do master load balancer.  O PERÍODO DEVE estar dentro do CIDR para a sub-rede principal e não para ser utilizado.  **MasterClusterDnsType** deve ser definido como "personalizado" e o nome DNS principal deve ser fornecido para **masterClusterDns**.  O nome DNS deve mapear para o IP privado estático e será utilizado para aceder à consola nos principais nódosos.

Se for selecionado um router privado **(routerClusterType**=privado), é necessário especificar um IP privado estático para **routerPrivateClusterIp**.  Este IP será atribuído na parte frontal do equilibrador de infra-carga.  O PERÍODO DEVE estar dentro do CIDR para a sub-rede de infra-rede e não em uso.  **o encaminhamentoSubDomainType** deve ser definido como "personalizado" e o nome DNS wildcard para encaminhamento deve ser fornecido para **o encaminhamentoSubDomain**.  

Se forem selecionados mestres privados e router privado, o nome de domínio personalizado também deve ser introduzido para **nome de domínio**

Depois de uma implantação bem sucedida, o Nó bastião é o único nó com um IP público em que se pode sh.d.r.i.  Mesmo que os nós mestres estejam configurados para acesso público, não estão expostos para acesso ssh.

Para implementar utilizando o modelo de Gestor de Recursos, utilize um ficheiro de parâmetros para fornecer os parâmetros de entrada. Para personalizar ainda mais a implementação, bifurque o repo GitHub e altere os itens apropriados.

Algumas opções comuns de personalização incluem, mas não se limitam a:

- Tamanho Bastião VM (variável em azuredeploy.json)
- Convenções de nomeação (variáveis em azuredeploy.json)
- Especificidades do cluster OpenShift, modificados através de ficheiros anfitriões (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Configure o ficheiro de parâmetros

O modelo da Plataforma de [Contentores OpenShift](https://github.com/Microsoft/openshift-container-platform) tem vários ramos disponíveis para diferentes versões da Plataforma de Contentores OpenShift.  Com base nas suas necessidades, pode implantar-se diretamente a partir do repo ou pode bifurcar o repo e fazer alterações personalizadas nos modelos ou scripts antes de ser implementado.

Utilize `appId` o valor do diretor de serviço `aadClientId` que criou anteriormente para o parâmetro.

O exemplo seguinte mostra um ficheiro de parâmetros chamado azuredeploy.parameters.json com todas as inputs necessárias.

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

Substitua os parâmetros com as suas informações específicas.

As versões diferentes podem ter parâmetros diferentes, por isso verifique os parâmetros necessários para o ramo que utiliza.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Arquivo Parameters.json explicado

| Propriedade | Descrição | Opções válidas | Valor Predefinido |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL para artefactos (json, scripts, etc.) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Região de Azure vai mobilizar recursos para |  |  |
| `masterVmSize` | Tamanho do Master VM. Selecione entre um dos tamanhos de VM permitidos listados no ficheiro azuredeploy.json |  | Standard_E2s_v3 |
| `infraVmSize` | Tamanho do Infra VM. Selecione entre um dos tamanhos de VM permitidos listados no ficheiro azuredeploy.json |  | Standard_D4s_v3 |
| `nodeVmSize` | Tamanho do VM do Nó da Aplicação. Selecione entre um dos tamanhos de VM permitidos listados no ficheiro azuredeploy.json |  | Standard_D4s_v3 |
| `cnsVmSize` | Tamanho do Recipiente Armazenagem Nativa (CNS) VM do nó. Selecione entre um dos tamanhos de VM permitidos listados no ficheiro azuredeploy.json |  | Standard_E4s_v3 |
| `osImageType` | A imagem RHEL a utilizar. galeria padrão: On-Demand; marketplace: imagem de terceiros | galeria padrão <br> marketplace | galeria padrão |
| `marketplaceOsImage` | Se `osImageType` for o mercado, insira os valores adequados para "editor", "oferta", 'sku', 'versão' da oferta de mercado. Este parâmetro é um tipo de objeto |  |  |
| `storageKind` | O tipo de armazenamento a ser utilizado  | gerido<br> sem gestão | gerido |
| `openshiftClusterPrefix` | Prefixo de cluster usado para configurar nomes de anfitriões para todos os nós.  Entre 1 e 20 caracteres |  | mycluster |
| `minoVersion` | A versão menor da Plataforma de Contentores OpenShift 3.11 para implementar |  | 69 |
| `masterInstanceCount` | Número de nós masters para implantar | 1, 3, 5 | 3 |
| `infraInstanceCount` | Número de nós de infravermelhos para implantar | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Número de nós para implantar | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Número de nós do SNC a implantar | 3, 4 | 3 |
| `osDiskSize` | Tamanho do disco OS para o VM (em GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Tamanho do disco de dados para anexar aos nós para o volume do Docker (em GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Tamanho do disco de dados para anexar aos nódosos CNS para utilização por glusterfs (em GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Nome de utilizador de administrador para o login osso (VM) e o utilizador inicial OpenShift |  | ocpadmin |
| `enableMetrics` | Ativar métricas. As métricas requerem mais recursos para selecionar o tamanho adequado para infra VM | true <br> false | false |
| `enableLogging` | Ativar a exploração madeireira. a cápsula de pesquisa elástica requer 8 GB de RAM, por isso selecione o tamanho adequado para infra VM | true <br> false | false |
| `enableCNS` | Ativar o armazenamento nativo do contentor | true <br> false | false |
| `rhsmUsernameOrOrgId` | Nome de utilizador do Gestor de Assinatura sacar chapéu vermelho ou ID da organização |  |  |
| `rhsmPoolId` | O Red Hat Subscription Manager Pool ID que contém os seus direitos OpenShift para nódeos de computação |  |  |
| `rhsmBrokerPoolId` | O Red Hat Subscription Manager Pool ID que contém os seus direitos OpenShift para masters e porsídeos de infravermelhos. Se você não tiver diferentes iDs de piscina, insira o mesmo ID da piscina que 'rhsmPoolId' |  |
| `sshPublicKey` | Copie a sua Chave Pública SSH aqui |  |  |
| `keyVaultSubscriptionId` | O ID de subscrição da subscrição que contém o Cofre chave |  |  |
| `keyVaultResourceGroup` | O nome do Grupo de Recursos que contém o Cofre chave |  |  |
| `keyVaultName` | O nome do Cofre Chave que criou |  |  |
| `enableAzure` | Ativar o Fornecedor de Nuvem Azure | true <br> false | true |
| `aadClientId` | Id do cliente do Cliente do Diretório Ativo Azure também conhecido como ID de aplicação para diretor de serviço |  |  |
| `domainName` | Nome do nome de domínio personalizado a utilizar (se aplicável). Definido para "nenhum" se não implantar cluster totalmente privado |  | nenhuma |
| `masterClusterDnsType` | Tipo de domínio para consola web OpenShift. 'padrão' utilizará a etiqueta DNS de IP público master infra. 'costume' permite-lhe definir o seu próprio nome | predefinição <br> costume | predefinição |
| `masterClusterDns` | O nome DNS personalizado para usar para aceder à consola web OpenShift se tiver selecionado 'personalizado' para`masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Se for definido para 'nipio', `routingSubDomain` utilizará nip.io.  Use 'custom' se tiver o seu próprio domínio que pretende utilizar para encaminhamento | nipio <br> costume | nipio |
| `routingSubDomain` | O nome DNS wildcard que pretende utilizar para encaminhamento se tiver selecionado 'personalizado' para`routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Selecione se utiliza uma Rede Virtual existente ou cria uma nova Rede Virtual | existente <br> novo | novo |
| `virtualNetworkResourceGroupName` | Nome do Grupo de Recursos para a nova Rede Virtual se tiver selecionado 'novo' para`virtualNetworkNewOrExisting` |  | recursosGroup().nome |
| `virtualNetworkName` | O nome da nova Rede Virtual para criar se tiver selecionado 'novo' para`virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Prefixo de endereço da nova rede virtual |  | 10.0.0.0/14 |
| `masterSubnetName` | O nome da sub-rede principal |  | mastersubnet |
| `masterSubnetPrefix` | CIDR usado para a sub-rede principal - precisa ser um subconjunto do endereçoPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | O nome da sub-rede de infra-rede |  | infrasubnet |
| `infraSubnetPrefix` | CIDR utilizado para a sub-rede de infra-rede - precisa ser um subconjunto do endereçoPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | O nome da sub-rede do nó |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR utilizado para a sub-rede do nó - precisa ser um subconjunto do endereçoPrefix |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Referência completa à sub-rede existente para os narizes-mestre. Não é necessário se criar novo vNet / Subnet |  |  |
| `existingInfraSubnetReference` | Referência completa à sub-rede existente para os nódosos de infravermelhos. Não é necessário se criar novo vNet / Subnet |  |  |
| `existingCnsSubnetReference` | Referência completa à sub-rede existente para os nódosos CNS. Não é necessário se criar novo vNet / Subnet |  |  |
| `existingNodeSubnetReference` | Referência completa à subnet existente para os nódosos de cálculo. Não é necessário se criar novo vNet / Subnet |  |  |
| `masterClusterType` | Especifique se o cluster usa nós de mestre privadoou público. Se for em privado, os nós mestres não serão expostos à Internet através de um IP público. Em vez disso, usará o IP privado especificado no`masterPrivateClusterIp` | público <br> privado | público |
| `masterPrivateClusterIp` | Se forem selecionados os nós de mestrado privados, deve especificar-se um endereço IP privado para utilização pelo equilibrista interno de carga para os nós principais. Este IP estático deve estar dentro do bloco CIDR para a sub-rede principal e ainda não está a ser utilizado. Se forem selecionados nós de mestrado público, este valor não será utilizado, mas ainda deve ser especificado |  | 10.1.0.200 |
| `routerClusterType` | Especifique se o cluster utiliza nós de infravermelhos privados ou públicos. Se for em privado, os nós de infravermelhos não serão expostos à Internet através de um IP público. Em vez disso, usará o IP privado especificado no`routerPrivateClusterIp` | público <br> privado | público |
| `routerPrivateClusterIp` | Se forem selecionados os nódos os infravermelhos privados, deve especificar-se um endereço IP privado para utilização pelo equilibrador interno de carga para os nós de infravermelhos. Este IP estático deve estar dentro do bloco CIDR para a sub-rede e ainda não está a ser utilizado. Se os nós de infravermelhos públicos forem selecionados, este valor não será utilizado, mas ainda deve ser especificado |  | 10.2.0.200 |
| `routingCertType` | Utilize um certificado personalizado para o domínio de encaminhamento ou o certificado por defeito auto-assinado - siga as instruções na secção **Certificados Personalizados** | auto-assinado <br> costume | auto-assinado |
| `masterCertType` | Utilize um certificado personalizado para domínio principal ou o certificado por defeito auto-assinado - siga as instruções na secção **Certificados Personalizados** | auto-assinado <br> costume | auto-assinado |

<br>

### <a name="deploy-using-azure-cli"></a>Implementar com a CLI do Azure

> [!NOTE] 
> O seguinte comando requer Azure CLI 2.0.8 ou posterior. Pode verificar a versão CLI com o `az --version` comando. Para atualizar a versão CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

O exemplo seguinte implanta o cluster OpenShift e todos os recursos relacionados num grupo de recursos chamado openshiftrg, com um nome de implementação do myOpenShiftCluster. O modelo é referenciado diretamente a partir do repo GitHub, e um ficheiro de parâmetros locais chamado azuredeploy.parameters.parson file é usado.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

A implementação leva pelo menos 60 minutos para ser concluída, com base no número total de nós implantados e opções configuradas. O Bastião DNS FQDN e o URL da consola OpenShift imprimem-se no terminal quando a implementação terminar.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Se não quiser ligar a linha de comando à espera `--no-wait` que a implantação esteja concluída, adicione como uma das opções para a implantação do grupo. A saída da implantação pode ser recuperada do portal Azure na secção de implantação do grupo de recursos.

## <a name="connect-to-the-openshift-cluster"></a>Ligue-se ao cluster OpenShift

Quando a implantação terminar, recupere a ligação da secção de saída da implantação. Conecte-se à consola OpenShift com o seu navegador utilizando o URL da **Consola OpenShift**. Você também pode SSH para o anfitrião bastião. Segue-se um exemplo em que o nome de utilizador do administrador é clusteradmin e o bastião público IP DNS FQDN é bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar recursos

Use o [grupo AZ eliminar](/cli/azure/group) o comando para remover o grupo de recursos, o cluster OpenShift, e todos os recursos relacionados quando já não são necessários.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Passos seguintes

- [Tarefas de pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Implantação openShift de resolução de problemas em Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Começar com plataforma de contentores OpenShift](https://docs.openshift.com)
