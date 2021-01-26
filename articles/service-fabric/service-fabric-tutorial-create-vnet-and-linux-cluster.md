---
title: Criar um cluster de tecido de serviço Linux em Azure
description: Saiba como implementar um cluster do Service Fabric do Linux numa rede virtual do Azure existente, com a CLI do Azure.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 77cc49c1b79e5c24e78a67a69493aa0b0059d565
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791076"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Implementar um cluster de tecido de serviço Linux numa rede virtual Azure

Neste artigo aprende-se a implantar um cluster de Tecido de Serviço Linux numa [rede virtual Azure (VNET)](../virtual-network/virtual-networks-overview.md) utilizando o Azure CLI e um modelo. Quando tiver terminado, terá um cluster em execução na cloud, no qual poderá implementar aplicações. Para criar um cluster do Windows com o PowerShell, veja [Criar um cluster do Windows seguro no Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instalar o [Tecido de Serviço CLI](service-fabric-cli.md)
* Instale o [Azure CLI](/cli/azure/install-azure-cli)
* Para aprender os conceitos-chave dos clusters, leia [a visão geral dos clusters Azure](service-fabric-azure-clusters-overview.md)
* [Planeie e prepare-se](service-fabric-cluster-azure-deployment-preparation.md) para uma implantação de clusters de produção.

Os seguintes procedimentos criam um cluster de tecido de serviço de sete nós. Para calcular o custo incorrido pela execução de um cluster do Service Fabric no Azure, utilize a [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>Transferir e explorar o modelo

Transfira os seguintes ficheiros de modelos do Resource Manager:

Para Ubuntu 16.04 LTS:

* [AzureDeploy.json][template]
* [AzureDeploy.Parameters.js][parameters]

Para Ubuntu 18.04 LTS:

* [AzureDeploy.json][template2]
* [AzureDeploy.Parameters.js][parameters2]

Para Ubuntu 18.04 LTS a diferença entre os dois modelos são 
* o atributo **vmImageSku** está a ser definido para "18.04-LTS"
* cada nó de **mão-de-mão Versão** sendo definido para 1.1
* Recursos Microsoft.ServiceFabric/clusters
   - **apiVersão** sendo definido para "2019-03-01" ou superior
   - **vmImage** propriedade sendo definida para "Ubuntu18_04"

Este modelo implementa um cluster seguro de sete máquinas virtuais e três tipos de nós numa rede virtual.  Outros modelos de exemplo podem ser encontrados no [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). O [AzureDeploy.json][template] implementa vários recursos, incluindo os seguintes.

### <a name="service-fabric-cluster"></a>Cluster do Service Fabric

No recurso **Microsoft.ServiceFabric/clusters**, é implementado um cluster Linux com as seguintes características:

* três tipos de nó
* cinco nós no tipo de nó primário (configurável nos parâmetros do modelo), um nó em cada um dos outros tipos de nós
* OS: (Ubuntu 16.04 LTS / Ubuntu 18.04 LTS) (configurável nos parâmetros do modelo)
* protegido por certificado (configurável nos parâmetros do modelo)
* O [serviço DNS](service-fabric-dnsservice.md) está ativado
* [Nível de durabilidade](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) de Bronze (configurável nos parâmetros do modelo)
* [Nível de fiabilidade](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) de Prata (configurável nos parâmetros do modelo)
* ponto final de ligação de cliente: 19000 (configurável nos parâmetros do modelo)
* ponto final de gateway HTTP: 19080 (configurável nos parâmetros do modelo)

### <a name="azure-load-balancer"></a>Balanceador de carga do Azure

No recurso **Microsoft.Network/loadBalancers**, é configurado um balanceador de carga e são configuradas pesquisas e regras para as seguintes portas:

* ponto final de ligação de cliente: 19000
* ponto final de gateway HTTP: 19080
* porta da aplicação: 80
* porta da aplicação: 443

### <a name="virtual-network-and-subnet"></a>Rede virtual e sub-rede

Os nomes da rede virtual e sub-rede são declarados nos parâmetros do modelo.  Os espaços de endereços da rede virtual e da sub-rede também são declarados nos parâmetros do modelo e configurados no recurso **Microsoft.Network/virtualNetworks**:

* espaço de endereços de rede virtual: 10.0.0.0/16
* espaço de endereços de sub-rede do Service Fabric: 10.0.2.0/24

Se forem necessárias quaisquer outras portas da aplicação, terá de ajustar o recurso Microsoft.Network/loadBalancers para permitir o tráfego nas mesmas.

## <a name="set-template-parameters"></a>Definir parâmetros de modelo

O ficheiro **AzureDeploy.Parameters** declara muitos valores usados para implantar o cluster e recursos associados. Alguns dos parâmetros que poderá ser necessário modificar para a sua implementação:

|Parâmetro|Valor de exemplo|Notas|
|---|---||
|adminUserName|vmadmin| O nome de utilizador administrador para as VMs do cluster. |
|adminPassword|Password#1234| A palavra-passe de utilizador administrador para as VMs do cluster.|
|clusterName|mysfcluster123| O nome do cluster. |
|localização|southcentralus| A localização do cluster. |
|certificateThumbprint|| <p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado.</p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o valor do thumbprint SHA-1 do certificado. Por exemplo, “6190390162C988701DB5676EB81083EA608DCCF3”. </p>|
|certificateUrlValue|| <p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado.</p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o URL do certificado. Por exemplo, "https: \/ /mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado.</p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o valor no cofre de origem. Por exemplo, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Implementar a rede virtual e o cluster

Em seguida, configure a topologia de rede e implemente o cluster do Service Fabric. O modelo do Resource Manager **AzureDeploy.json** cria uma rede virtual (VNET) e uma sub-rede para o Service Fabric. O modelo também implementa um cluster com a segurança do certificado ativada.  Para clusters de produção, utilize um certificado de uma autoridade de certificação (AC) como o certificado de cluster. Um certificado autoassinado pode ser utilizado para proteger clusters de teste.

O modelo deste artigo implementa um cluster que utiliza a impressão digital do certificado para identificar o certificado de cluster.  Não há dois certificados que possam ter a mesma impressão digital, o que dificulta a gestão de certificados. Mudar um cluster implantado de usar impressões digitais de certificado para usar nomes comuns de certificado torna a gestão de certificados muito mais simples.  Para aprender a atualizar o cluster para utilizar os nomes comuns do certificado para a gestão de certificados, leia [o cluster de alteração para a gestão de certificados de nome comum](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-using-an-existing-certificate"></a>Criar um cluster através de um certificado existente

O script seguinte utiliza o comando [az sf cluster create](/cli/azure/sf/cluster) e o modelo para implementar um novo cluster protegido por um certificado existente. O comando também cria um novo cofre de chaves no Azure e carrega o certificado.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Criar um cluster ao utilizar um novo certificado autoassinado

O script seguinte utiliza o comando [az sf cluster create](/cli/azure/sf/cluster) e o modelo para implementar um novo cluster no Azure. O comando também cria um novo cofre chave em Azure, adiciona um novo certificado auto-assinado ao cofre de chaves, e descarrega o arquivo de certificado localmente.

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json \
   --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password \
   --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>Ligar ao cluster seguro

Ligue ao cluster com o comando `sfctl cluster select` da CLI do Service Fabric e a sua chave.  Tenha em atenção, utilize apenas a opção **--no-verify** para um certificado autoassinado.

```console
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Certifique-se de que está ligado e que o cluster está em bom estado de funcionamento, com o comando `sfctl cluster health`.

```console
sfctl cluster health
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se não passar imediatamente para o artigo seguinte, poderá [eliminar o cluster](./service-fabric-tutorial-delete-cluster.md) para evitar incorrer em custos.

## <a name="next-steps"></a>Próximos passos

Aprenda a [escalar um Cluster.](service-fabric-tutorial-scale-cluster.md)

O modelo deste artigo implementa um cluster que utiliza a impressão digital do certificado para identificar o certificado de cluster.  Não há dois certificados que possam ter a mesma impressão digital, o que dificulta a gestão de certificados. Mudar um cluster implantado de usar impressões digitais de certificado para usar nomes comuns de certificado torna a gestão de certificados muito mais simples.  Para aprender a atualizar o cluster para utilizar os nomes comuns do certificado para a gestão de certificados, leia [o cluster de alteração para a gestão de certificados de nome comum](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json
[template2]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-1804-3-NodeTypes-Secure/AzureDeploy.json
[parameters2]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-1804-3-NodeTypes-Secure/AzureDeploy.Parameters.json
