---
title: Quickstart - Criar um Azure Private Endpoint usando Azure CLI
description: Utilize este quickstart para aprender a criar um Ponto Final Privado utilizando o Azure CLI.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: f74a143859f0a6629c88f0dcb61a97697f49d0be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889234"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Quickstart: Criar um ponto final privado utilizando O Azure CLI

Começa com o Azure Private Link utilizando um Private Endpoint para ligar de forma segura a uma aplicação web Azure.

Neste arranque rápido, você vai criar um ponto final privado para uma aplicação web Azure e implementar uma máquina virtual para testar a ligação privada.  

Os pontos finais privados podem ser criados para diferentes tipos de serviços Azure, tais como Azure SQL e Azure Storage.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Uma App Web Azure com um plano de serviço de aplicações **premiumV2** ou superior implementado na sua subscrição Azure.  
    * Para obter mais informações e um exemplo, consulte [Quickstart: Crie uma aplicação web Core ASP.NET em Azure](../app-service/quickstart-dotnetcore.md). 
    * Para um tutorial detalhado sobre a criação de uma aplicação web e um ponto final, consulte [Tutorial: Conecte-se a uma aplicação web utilizando um Azure Private Endpoint](tutorial-private-endpoint-webapp-portal.md).
* Inscreva-se no portal Azure e verifique se a sua subscrição está ativa executando `az login` .
* Verifique a sua versão do Azure CLI numa janela de terminal ou comando em funcionamento `az --version` . Para a versão mais recente, consulte as [últimas notas de lançamento](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Se não tiver a versão mais recente, atualize a sua instalação seguindo o [guia de instalação do seu sistema operativo ou plataforma.](/cli/azure/install-azure-cli)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Criar um grupo de recursos com [a criação de grupo az:](/cli/azure/group#az_group_create)

* Denominado **CreatePrivateEndpointQs-rg**. 
* No **local leste.**

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Criar uma rede virtual e hospedeiro de bastião

Nesta secção, você vai criar uma rede virtual, sub-rede e hospedeiro de bastião. 

O hospedeiro de bastião será utilizado para ligar de forma segura à máquina virtual para testar o ponto final privado.

Criar uma rede virtual com [vnet de rede az criar](/cli/azure/network/vnet#az_network_vnet_create)

* Chamado **myVNet.**
* Prefixo de endereço de **10.0.0.0/16**.
* Subnet chamado **myBackendSubnet**.
* Prefixo de sub-rede de **10.0.0.0/24**.
* No grupo de recursos **CreatePrivateEndpointQS-rg.**
* Localização do **eastus.**

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Atualize a sub-rede para desativar as políticas privadas de rede de pontos finais para o ponto final privado com [a atualização da sub-rede de rede Az:](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Utilize [a rede az public-ip criar](/cli/azure/network/public-ip#az-network-public-ip-create) para criar um endereço IP público para o anfitrião do bastião:

* Crie um endereço IP público redundante de zona padrão chamado **myBastionIP**.
* In **CreatePrivateEndpointQs-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

Utilize [a sub-rede vnet](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) de rede az para criar uma sub-rede de bastião:

* Denominado **AzureBastionSubnet**.
* Prefixo de endereço de **10.0.1.0/24**.
* Na rede virtual **myVNet.**
* No grupo de recursos **CreatePrivateEndpointQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Utilize [o bastião da rede Az](/cli/azure/network/bastion#az-network-bastion-create) para criar um hospedeiro de bastião:

* Chamado **myBastionHost.**
* In **CreatePrivateEndpointQs-rg**.
* Associado ao IP público **myBastionIP**.
* Associado à rede virtual **myVNet.**
* Na localização **leste.**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Pode levar alguns minutos para o anfitrião do Bastião Azure ser implantado.

## <a name="create-test-virtual-machine"></a>Criar máquina virtual de teste

Nesta secção, irá criar uma máquina virtual que será usada para testar o ponto final privado.

Criar um VM com [az vm criar](/cli/azure/vm#az_vm_create). Quando solicitado, forneça uma palavra-passe para ser usada como credenciais para o VM:

* Chamado **myVM.**
* In **CreatePrivateEndpointQs-rg**.
* Na rede **myVNet.**
* Na sub-rede **myBackendSubnet**.
* Imagem do servidor **Win2019Datacenter**.

```azurecli-interactive
az vm create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-private-endpoint"></a>Criar ponto final privado

Nesta secção, criará o ponto final privado.

Utilize [a lista az webapp](/cli/azure/webapp#az_webapp_list) para colocar o ID de recursos da aplicação Web que criou anteriormente numa variável de concha.

Utilize [o ponto de terminação da rede Az](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) para criar o ponto final e a ligação:

* Chamado **myPrivateEndpoint**.
* No grupo de recursos **CreatePrivateEndpointQS-rg**.
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* Conexão chamada **myConnection**.
* O seu **\<webapp-resource-group-name>** webapp.

```azurecli-interactive
id=$(az webapp list \
    --resource-group <webapp-resource-group-name> \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-id sites \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Configure a zona privada do DNS

Nesta secção, você vai criar e configurar a zona privada de DNS usando [a zona privada de dns da rede Az criar](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

Você usará [a rede az rede privada-dns link vnet criar](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) para criar a ligação de rede virtual para a zona dns.

Você vai criar um grupo de zona dns com [rede az-end dns-zone-group criar](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Zona chamada **privatelink.azurewebsites.net**
* Na rede virtual **myVNet.**
* No grupo de recursos **CreatePrivateEndpointQS-rg**.
* Link DNS nomeado **myDNSLink**.
* Associado ao **myPrivateEndpoint**.
* Grupo de zona chamado **MyZoneGroup**.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name "privatelink.azurewebsites.net"

az network private-dns link vnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --zone-name "privatelink.azurewebsites.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreatePrivateEndpointQS-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.azurewebsites.net" \
   --zone-name webapp
```

## <a name="test-connectivity-to-private-endpoint"></a>Testar conectividade com o ponto final privado

Nesta secção, utilizará a máquina virtual que criou no passo anterior para ligar ao servidor SQL através do ponto final privado.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) 
 
2. Selecione **grupos** de recursos no painel de navegação à esquerda.

3. Selecione **CreatePrivateEndpointQs-rg**.

4. Selecione **myVM**.

5. Na página geral do **myVM,** selecione **Connect** e, em **seguida, Bastion**.

6. Selecione o botão **azul Use Bastion.**

7. Introduza o nome de utilizador e a palavra-passe que introduziu durante a criação da máquina virtual.

8. Abra o Windows PowerShell no servidor depois de ligar.

9. Introduza `nslookup <your-webapp-name>.azurewebsites.net`. **\<your-webapp-name>** Substitua-o pelo nome da aplicação web que criou nos passos anteriores.  Receberá uma mensagem semelhante à que é exibida abaixo:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Um endereço IP privado de **10.0.0.5** é devolvido para o nome da aplicação web.  Este endereço encontra-se na sub-rede da rede virtual que criou anteriormente.

10. Na ligação de bastião ao **myVM,** abra o Internet Explorer.

11. Introduza o url da sua aplicação web, **https:// \<your-webapp-name> .azurewebsites.net**.

12. Receberá a página de aplicações web padrão se a sua aplicação não tiver sido implementada:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Página de aplicativo web padrão." border="true":::

13. Feche a ligação ao **myVM**.

## <a name="clean-up-resources"></a>Limpar os recursos 
Quando terminar de usar o ponto final privado e o VM, utilize [o grupo AZ para remover](/cli/azure/group#az_group_delete) o grupo de recursos e todos os recursos que tem:

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um:

* Rede virtual e hospedeiro de bastião.
* Máquina virtual.
* Ponto final privado para uma App Web Azure.

Usou a máquina virtual para testar a conectividade de forma segura para a aplicação web em todo o ponto final privado.

Para obter mais informações sobre os serviços que suportam um ponto final privado, consulte:
> [!div class="nextstepaction"]
> [Disponibilidade de Ligação Privada](private-link-overview.md#availability)
