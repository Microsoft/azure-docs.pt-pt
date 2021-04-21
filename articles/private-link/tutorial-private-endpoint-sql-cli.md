---
title: 'Tutorial: Ligue-se a um servidor Azure SQL utilizando um Azure Private Endpoint - Azure CLI'
description: Use este tutorial para aprender a criar um servidor Azure SQL com um ponto final privado usando Azure CLI
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: a8fafeaaf974893c9a1a71115912f2a7b019ddd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771826"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>Tutorial: Ligue-se a um servidor Azure SQL utilizando um Azure Private Endpoint - Azure CLI

O ponto final privado Azure é o bloco de construção fundamental para private link em Azure. Permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem com os recursos de Private Link em privado.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma rede virtual e um hospedeiro de bastião.
> * Cria uma máquina virtual.
> * Crie um servidor Azure SQL e um ponto final privado.
> * Teste a conectividade com o ponto final privado do servidor SQL.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Inscreva-se no portal Azure e verifique se a sua subscrição está ativa executando `az login` .
* Verifique a sua versão do Azure CLI numa janela de terminal ou comando em funcionamento `az --version` . Para a versão mais recente, consulte as [últimas notas de lançamento](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Se não tiver a versão mais recente, atualize a sua instalação seguindo o [guia de instalação do seu sistema operativo ou plataforma.](/cli/azure/install-azure-cli)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Criar um grupo de recursos com [a criação de grupo az:](/cli/azure/group#az_group_create)

* Nomeado **CreateSQLEndpointTutorial-rg**. 
* No **local leste.**

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
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
* No grupo de recursos **CreateSQLEndpointTutorial-rg.**
* Localização do **eastus.**

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Atualize a sub-rede para desativar as políticas privadas de rede de pontos finais para o ponto final privado com [a atualização da sub-rede de rede Az:](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Utilize [a rede az public-ip criar](/cli/azure/network/public-ip#az_network_public_ip_create) para criar um endereço IP público para o anfitrião do bastião:

* Crie um endereço IP público redundante de zona padrão chamado **myBastionIP**.
* In **CreateSQLEndpointTutorial-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

Utilize [a sub-rede vnet](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) de rede az para criar uma sub-rede de bastião:

* Denominado **AzureBastionSubnet**.
* Prefixo de endereço de **10.0.1.0/24**.
* Na rede virtual **myVNet.**
* No grupo de recursos **CreateSQLEndpointTutorial-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Utilize [o bastião da rede Az](/cli/azure/network/bastion#az_network_bastion_create) para criar um hospedeiro de bastião:

* Chamado **myBastionHost.**
* In **CreateSQLEndpointTutorial-rg**.
* Associado ao IP público **myBastionIP**.
* Associado à rede virtual **myVNet.**
* Na localização **leste.**

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
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
* In **CreateSQLEndpointTutorial-rg**.
* Na rede **myVNet.**
* Na sub-rede **myBackendSubnet**.
* Imagem do servidor **Win2019Datacenter**.

```azurecli-interactive
az vm create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-an-azure-sql-server"></a>Criar um servidor Azure SQL

Nesta secção, irá criar um servidor SQL e uma base de dados.

Utilize [o servidor az sql criar](/cli/azure/sql/server#az_sql_server_create) para criar um servidor SQL:

* Substitua **\<sql-server-name>** pelo seu nome de servidor único.
* **\<your-password>** Substitua-a pela sua palavra-passe.
* In **CreateSQLEndpointTutorial-rg**.
* Na região **leste.**

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

Use [az sql db criar](/cli/azure/sql/db#az_sql_db_create) para criar uma base de dados:

* Nomeou **a minhaDataBase.**
* In **CreateSQLEndpointTutorial-rg**.
* Substitua **\<sql-server-name>** pelo seu nome de servidor único.

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>Criar ponto final privado

Nesta secção, criará o ponto final privado.

Utilize [a lista de servidores az sql](/cli/azure/sql/server#az_sql_server_list) para colocar o ID de recurso do servidor SQL numa variável de concha.

Utilize [o ponto de terminação da rede Az](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) para criar o ponto final e a ligação:

* Chamado **myPrivateEndpoint**.
* No grupo de recursos **CreateSQLEndpointTutorial-rg**.
* Na rede virtual **myVNet.**
* Na sub-rede **myBackendSubnet**.
* Conexão chamada **myConnection**.

```azurecli-interactive
id=$(az sql server list \
    --resource-group CreateSQLEndpointTutorial-rg \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-ids sqlServer \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Configure a zona privada do DNS

Nesta secção, você vai criar e configurar a zona privada de DNS usando [a zona privada de dns da rede Az criar](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

Você usará [a rede az rede privada-dns link vnet criar](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) para criar a ligação de rede virtual para a zona dns.

Você vai criar um grupo de zona dns com [rede az-end dns-zone-group criar](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Zona chamada **privatelink.database.windows.net**
* Na rede virtual **myVNet.**
* No grupo de recursos **CreateSQLEndpointTutorial-rg**.
* Link DNS nomeado **myDNSLink**.
* Associado ao **myPrivateEndpoint**.
* Grupo de zona chamado **MyZoneGroup**.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name "privatelink.database.windows.net"

az network private-dns link vnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --zone-name "privatelink.database.windows.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreateSQLEndpointTutorial-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="test-connectivity-to-private-endpoint"></a>Testar conectividade com o ponto final privado

Nesta secção, utilizará a máquina virtual que criou no passo anterior para ligar ao servidor SQL através do ponto final privado.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) 
 
2. Selecione **grupos** de recursos no painel de navegação à esquerda.

3. Selecione **CreateSQLEndpointTutorial-rg**.

4. Selecione **myVM**.

5. Na página geral do **myVM,** selecione **Connect** e, em **seguida, Bastion**.

6. Selecione o botão **azul Use Bastion.**

7. Introduza o nome de utilizador e a palavra-passe que introduziu durante a criação da máquina virtual.

8. Abra o Windows PowerShell no servidor depois de ligar.

9. Introduza `nslookup <sqlserver-name>.database.windows.net`. **\<sqlserver-name>** Substitua-o pelo nome do servidor SQL que criou nos passos anteriores.  Receberá uma mensagem semelhante à que é exibida abaixo:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Um endereço IP privado de **10.0.0.5** é devolvido para o nome do servidor SQL.  Este endereço encontra-se na sub-rede da rede virtual que criou anteriormente.


10. Instale [o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) no **myVM**.

11. Open **SQL Server Management Studio**.

12. Em **Ligar ao servidor,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Tipo de servidor | Selecione **Motor de Base de Dados**.|
    | Nome do servidor | Insira **\<sql-server-name> .database.windows.net** |
    | Autenticação | Selecione **SQL Server Authentication** (Autenticação do SQL Server). |
    | Nome de utilizador | Introduza o nome de utilizador que inseriu durante a criação do servidor |
    | Palavra-passe | Introduza a palavra-passe que introduziu durante a criação do servidor |
    | Memorizar palavra-passe | Selecione **Yes** (Sim). |

13. Selecione **Ligar**.

14. Procure bases de dados a partir do menu esquerdo.

15. (Opcionalmente) Criar ou consultar informações a partir da **mysqldatabase**.

16. Feche a ligação do bastião ao **myVM**. 

## <a name="clean-up-resources"></a>Limpar os recursos 

Quando terminar de usar o ponto final privado, o servidor SQL e o VM, elimine o grupo de recursos e todos os recursos que contém: 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um:

* Rede virtual e hospedeiro de bastião.
* Máquina virtual.
* Servidor Azure SQL com ponto final privado.

Utilizou a máquina virtual para testar a conectividade de forma segura para o servidor SQL através do ponto final privado.

Como próximo passo, poderá também estar interessado na aplicação Web com conectividade privada ao cenário de arquitetura **de base de dados Azure SQL,** que liga uma aplicação web fora da rede virtual ao ponto final privado de uma base de dados.
> [!div class="nextstepaction"]
> [Aplicativo web com conectividade privada para base de dados Azure SQL](/azure/architecture/example-scenario/private-web-app/private-web-app)