---
title: Link privado para o método de instalação da CLI do banco de dados do Azure para MariaDB (versão prévia)
description: Saiba como configurar o link privado para o banco de dados do Azure para MariaDB do CLI do Azure
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 5891662a2176fe110c4fec844d2335a8f1c9d500
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75974210"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-preview-using-cli"></a>Criar e gerenciar o link privado para o banco de dados do Azure para MariaDB (versão prévia) usando a CLI

Um ponto de extremidade privado é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado. Neste artigo, você aprenderá a usar o CLI do Azure para criar uma VM em uma rede virtual do Azure e um banco de dados do Azure para MariaDB Server com um ponto de extremidade privado do Azure.

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure em que o banco de dados do Azure para MariaDB dá suporte a tipos de preço Uso Geral e com otimização de memória.

## <a name="prerequisites"></a>Pré-requisitos

Para percorrer este guia de instruções, você precisa de:

- Um [banco de dados do Azure para o servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você decidir instalar e usar CLI do Azure localmente, este guia de início rápido exigirá que você use CLI do Azure versão 2.0.28 ou posterior. Para localizar sua versão instalada, execute `az --version`. Consulte [instalar CLI do Azure](/cli/azure/install-azure-cli) para obter informações de instalação ou atualização.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para poder criar qualquer recurso, você precisa criar um grupo de recursos para hospedar a rede virtual. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *MyResource* Group no local *westeurope* :

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Criar uma Rede Virtual
Crie uma rede virtual com [AZ Network vnet Create](/cli/azure/network/vnet). Este exemplo cria uma rede virtual padrão chamada *myVirtualNetwork* com uma sub-rede chamada *mysubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Desabilitar políticas de ponto de extremidade privado de sub-rede 
O Azure implanta recursos em uma sub-rede em uma rede virtual, portanto, você precisa criar ou atualizar a sub-rede para desabilitar as políticas de rede de ponto de extremidade privadas. Atualize uma configuração de sub-rede chamada *mysubnet* com [AZ Network vnet subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Crie a VM 
Crie uma VM com AZ VM Create. Quando solicitado, forneça uma senha a ser usada como as credenciais de entrada para a VM. Este exemplo cria uma VM chamada *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Anote o endereço IP público da VM. Você usará esse endereço para se conectar à VM da Internet na próxima etapa.

## <a name="create-an-azure-database-for-mariadb-server"></a>Criar uma Azure Database for MariaDB server 
Crie um banco de dados do Azure para MariaDB com o comando AZ MariaDB Server CREATE. Lembre-se de que o nome do seu servidor MariaDB deve ser exclusivo no Azure, portanto, substitua o valor do espaço reservado entre colchetes com seu próprio valor exclusivo: 

```azurecli-interactive
# Create a logical server in the resource group 
az mariadb server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Observe que a ID do servidor MariaDB é semelhante a ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMariaDB/servers/servername.``` você usará a ID do servidor MariaDB na próxima etapa. 

## <a name="create-the-private-endpoint"></a>Criar o ponto de extremidade privado 
Crie um ponto de extremidade privado para o servidor MariaDB em sua rede virtual: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MariaDB Server ID>" \  
    --group-ids mariadbServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Configurar a zona de DNS privado 
Crie uma zona de DNS privado para o domínio do servidor MariDB e crie um link de associação com a rede virtual. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MariaDB name 
 
 
#Create DNS records 
az network private-dns record-set a create --name mydemoserver --zone-name privatelink.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name mydemoserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Conecte-se à VM *myVm* da Internet da seguinte maneira:

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Ligar**. Depois de selecionar o botão **conectar** , **Conecte-se à máquina virtual** é aberto.

1. Selecione **transferir ficheiro RDP**. O Azure cria um arquivo protocolo RDP ( *. rdp*) e o baixa em seu computador.

1. Abra o arquivo. rdp * baixado.

    1. Se lhe for pedido, selecione **Ligar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Talvez seja necessário selecionar **mais escolhas** > **usar uma conta diferente**, para especificar as credenciais inseridas quando você criou a VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se você receber um aviso de certificado, selecione **Sim** ou **continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para a área de trabalho local.  

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>Acessar o servidor MariaDB de forma privada da VM

1. No Área de Trabalho Remota do *myVM*, abra o PowerShell.

2. Introduza  `nslookup mydemoserver.mariadb.privatelink.database.azure.com`. 

    Você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoserver.mariadb.privatelink.database.azure.com
    Address:  10.1.3.4

3. Test the private link connection for the MariaDB server using any available client. In the example below I have used [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) to do the operation.

4. In **New connection**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Connection Name| Select the connection name of your choice.|
    | Hostname | Select *mydemoserver.mariadb.privatelink.database.azure.com* |
    | Username | Enter username as *username@servername* which is provided during the MariaDB server creation. |
    | Password | Enter a password provided during the MariaDB server creation. |
    ||

5. Select **Test Connection** or **OK**.

6. (Optionally) Browse databases from left menu and Create or query information from the MariaDB database

8. Close the remote desktop connection to myVm.

## Clean up resources 
When no longer needed, you can use az group delete to remove the resource group and all the resources it has: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [o que é o ponto de extremidade privado do Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)