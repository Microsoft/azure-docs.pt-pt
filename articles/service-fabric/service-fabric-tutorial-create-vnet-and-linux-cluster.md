---
title: Criar um cluster do Service Fabric do Linux no Azure | Microsoft Docs
description: Saiba como implementar um cluster do Service Fabric do Linux numa rede virtual do Azure existente, com a CLI do Azure.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 2ba157d7bf2e6effbaf7ab129dbbbfd1ca8b9667
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598847"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Implantar um cluster do Linux Service Fabric em uma rede virtual do Azure

Neste artigo, você aprenderá a implantar um cluster do Linux Service Fabric em uma [VNET (rede virtual) do Azure](../virtual-network/virtual-networks-overview.md) usando CLI do Azure e um modelo. Quando tiver terminado, terá um cluster em execução na cloud, no qual poderá implementar aplicações. Para criar um cluster do Windows com o PowerShell, veja [Criar um cluster do Windows seguro no Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instale a [CLI do Service Fabric](service-fabric-cli.md)
* Instalar a [CLI do Azure](/cli/azure/install-azure-cli)
* Para saber os principais conceitos de clusters, leia [visão geral dos clusters do Azure](service-fabric-azure-clusters-overview.md)
* [Planeje e prepare-se](service-fabric-cluster-azure-deployment-preparation.md) para uma implantação de cluster de produção.

Os procedimentos a seguir criam um cluster de sete nós Service Fabric. Para calcular o custo incorrido pela execução de um cluster do Service Fabric no Azure, utilize a [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>Transferir e explorar o modelo

Transfira os seguintes ficheiros de modelos do Resource Manager:

* [AzureDeploy. JSON][template]
* [AzureDeploy. Parameters. JSON][parameters]

Este modelo implanta um cluster seguro de sete máquinas virtuais e três tipos de nós em uma rede virtual.  Outros modelos de exemplo podem ser encontrados no [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). O [AzureDeploy. JSON][template] implanta um número de recursos, incluindo o seguinte.

### <a name="service-fabric-cluster"></a>Cluster do Service Fabric

No recurso **Microsoft.ServiceFabric/clusters**, é implementado um cluster Linux com as seguintes características:

* três tipos de nó
* cinco nós no tipo de nó primário (configurável nos parâmetros de modelo), um nó em cada um dos outros tipos de nó
* SO: Ubuntu 16, 4 LTS (configurável nos parâmetros de modelo)
* protegido por certificado (configurável nos parâmetros do modelo)
* O [serviço DNS](service-fabric-dnsservice.md) está ativado
* [Nível de durabilidade](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) de Bronze (configurável nos parâmetros do modelo)
* [Nível de fiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) de Prata (configurável nos parâmetros do modelo)
* ponto de extremidade de conexão do cliente: 19000 (configurável nos parâmetros do modelo)
* Ponto de extremidade do gateway HTTP: 19080 (configurável nos parâmetros do modelo)

### <a name="azure-load-balancer"></a>Balanceador de carga do Azure

No recurso **Microsoft.Network/loadBalancers**, é configurado um balanceador de carga e são configuradas pesquisas e regras para as seguintes portas:

* ponto de extremidade de conexão do cliente: 19000
* Ponto de extremidade do gateway HTTP: 19080
* porta do aplicativo: 80
* porta do aplicativo: 443

### <a name="virtual-network-and-subnet"></a>Rede virtual e sub-rede

Os nomes da rede virtual e sub-rede são declarados nos parâmetros do modelo.  Os espaços de endereços da rede virtual e da sub-rede também são declarados nos parâmetros do modelo e configurados no recurso **Microsoft.Network/virtualNetworks**:

* espaço de endereço de rede virtual: 10.0.0.0/16
* Espaço de endereço de sub-rede Service Fabric: 10.0.2.0/24

Se forem necessárias quaisquer outras portas da aplicação, terá de ajustar o recurso Microsoft.Network/loadBalancers para permitir o tráfego nas mesmas.

## <a name="set-template-parameters"></a>Definir parâmetros de modelo

O arquivo de parâmetros [AzureDeploy.][parameters] Parameters declara muitos valores usados para implantar o cluster e os recursos associados. Alguns dos parâmetros que poderá ser necessário modificar para a sua implementação:

|Parâmetro|Valor de exemplo|Notas|
|---|---||
|adminUserName|vmadmin| O nome de utilizador administrador para as VMs do cluster. |
|adminPassword|Password#1234| A palavra-passe de utilizador administrador para as VMs do cluster.|
|clusterName|mysfcluster123| O nome do cluster. |
|localização|southcentralus| A localização do cluster. |
|certificateThumbprint|| <p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado.</p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o valor do thumbprint SHA-1 do certificado. Por exemplo, “6190390162C988701DB5676EB81083EA608DCCF3”. </p>|
|certificateUrlValue|| <p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado.</p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o URL do certificado. Por exemplo, "https:\//mykeyvault.Vault.Azure.net:443/Secrets/myCertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado.</p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o valor no cofre de origem. Por exemplo, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Implementar a rede virtual e o cluster

Em seguida, configure a topologia de rede e implemente o cluster do Service Fabric. O modelo do Gerenciador de recursos [AzureDeploy. JSON][template] cria uma rede virtual (VNET) e uma sub-rede para Service Fabric. O modelo também implementa um cluster com a segurança do certificado ativada.  Para clusters de produção, utilize um certificado de uma autoridade de certificação (AC) como o certificado de cluster. Um certificado autoassinado pode ser utilizado para proteger clusters de teste.

O modelo neste artigo implanta um cluster que usa a impressão digital do certificado para identificar o certificado do cluster.  Dois certificados não podem ter a mesma impressão digital, o que torna o gerenciamento de certificados mais difícil. Alternar um cluster implantado do uso de impressões digitais de certificado para usar nomes comuns de certificado torna o gerenciamento de certificados muito mais simples.  Para saber como atualizar o cluster para usar nomes comuns de certificado para o gerenciamento de certificados, leia [alterar cluster para gerenciamento de nome comum de certificado](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-using-an-existing-certificate"></a>Criar um cluster através de um certificado existente

O script seguinte utiliza o comando [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) e o modelo para implementar um novo cluster protegido por um certificado existente. O comando também cria um novo cofre de chaves no Azure e carrega o certificado.

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

O script seguinte utiliza o comando [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) e o modelo para implementar um novo cluster no Azure. O comando também cria um novo cofre de chaves no Azure, adiciona um novo certificado autoassinado ao cofre de chaves e baixa o arquivo de certificado localmente.

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>Ligar ao cluster seguro

Ligue ao cluster com o comando `sfctl cluster select` da CLI do Service Fabric e a sua chave.  Tenha em atenção, utilize apenas a opção **--no-verify** para um certificado autoassinado.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Certifique-se de que está ligado e que o cluster está em bom estado de funcionamento, com o comando `sfctl cluster health`.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Limpar recursos

Se não passar imediatamente para o artigo seguinte, poderá [eliminar o cluster](service-fabric-cluster-delete.md) para evitar incorrer em custos.

## <a name="next-steps"></a>Passos Seguintes

Saiba como [dimensionar um cluster](service-fabric-tutorial-scale-cluster.md).

O modelo neste artigo implanta um cluster que usa a impressão digital do certificado para identificar o certificado do cluster.  Dois certificados não podem ter a mesma impressão digital, o que torna o gerenciamento de certificados mais difícil. Alternar um cluster implantado do uso de impressões digitais de certificado para usar nomes comuns de certificado torna o gerenciamento de certificados muito mais simples.  Para saber como atualizar o cluster para usar nomes comuns de certificado para o gerenciamento de certificados, leia [alterar cluster para gerenciamento de nome comum de certificado](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json
