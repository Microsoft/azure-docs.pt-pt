---
title: Criar um ponto de extremidade privado do Azure usando CLI do Azure | Microsoft Docs
description: Saiba mais sobre o ponto de extremidade privado do Azure
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 46c08f48efc00c1e4d88ceccb680c0cfd1671b2f
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203570"
---
# <a name="create-a-private-endpoint-using-azure-cli"></a>Criar um ponto de extremidade privado usando CLI do Azure
O ponto de extremidade privado é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado. Neste guia de início rápido, você aprenderá a criar uma VM em uma rede virtual, um servidor de banco de dados SQL com um ponto de extremidade privado usando CLI do Azure. Em seguida, você pode acessar a VM para e acessar com segurança o recurso de link privado (um servidor de banco de dados SQL do Azure privado neste exemplo). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você decidir instalar e usar CLI do Azure localmente, este guia de início rápido exigirá que você use CLI do Azure versão 2.0.28 ou posterior. Para localizar a versão instalada, execute `az --version`. Consulte [instalar CLI do Azure](/cli/azure/install-azure-cli) para obter informações de instalação ou atualização.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para poder criar qualquer recurso, você precisa criar um grupo de recursos para hospedar a rede virtual. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *MyResource* Group no local *westcentralus* :

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma rede virtual com [AZ Network vnet Create](/cli/azure/network/vnet). Este exemplo cria uma rede virtual padrão chamada *myVirtualNetwork* com uma sub-rede chamada *mysubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>Desabilitar políticas de ponto de extremidade privado de sub-rede 
O Azure implanta recursos em uma sub-rede em uma rede virtual, portanto, você precisa criar ou atualizar a sub-rede para desabilitar as políticas de rede de ponto de extremidade privadas. Atualize uma configuração de sub-rede denominada * mysubnet * * com [AZ Network vnet subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Crie a VM 
Crie uma VM com AZ VM Create. Quando solicitado, forneça uma senha a ser usada como as credenciais de entrada para a VM. Este exemplo cria uma VM chamada *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Anote o endereço IP público da VM. Você usará esse endereço para se conectar à VM da Internet na próxima etapa.

## <a name="create-a-sql-database-server"></a>Criar um servidor de banco de dados SQL 
Crie um servidor de banco de dados SQL com o comando AZ SQL Server CREATE. Lembre-se de que o nome do seu SQL Server deve ser exclusivo no Azure, portanto, substitua o valor do espaço reservado entre colchetes com seu próprio valor exclusivo: 

```azurecli-interactive
# Create a logical server in the resource group 
az sql server create \ 
    --name "myserver"\ 
    --resource-group myResourceGroup \ 
    --location WestUS \ 
    --admin-user "sqladmin" \ 
    --admin-password "CHANGE_PASSWORD_1" 
 
# Create a database in the server with zone redundancy as false 
az sql db create \ 
    --resource-group myResourceGroup  \ 
    --server myserver \ 
    --name mySampleDatabase \ 
    --sample-name AdventureWorksLT \ 
    --edition GeneralPurpose \ 
    --family Gen4 \ 
    --capacity 1 
```

Observe que a ID de SQL Server é ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` semelhante a você usará a ID de SQL Server na próxima etapa. 

## <a name="create-the-private-endpoint"></a>Criar o ponto de extremidade privado 
Crie um ponto de extremidade privado para o servidor do banco de dados SQL em sua rede virtual: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<SQL Server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```
## <a name="configure-the-private-dns-zone"></a>Configurar a zona de DNS privado 
Crie uma zona de DNS privado para o domínio do servidor do banco de dados SQL e crie um link de associação com a rede virtual. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.windows.net" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.windows.net"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the SQL server name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
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
        > Talvez seja necessário selecionar **mais escolhas** > **usar uma conta diferente**, para especificar as credenciais que você inseriu quando criou a VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se você receber um aviso de certificado, selecione **Sim** ou **continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para a área de trabalho local.  

## <a name="access-dql-database-server-privately-from-the-vm"></a>Acessar o servidor de banco de dados DQL de forma privada da VM

Nesta seção, você se conectará ao servidor do banco de dados SQL da VM usando o ponto de extremidade privado.

 1. No Área de Trabalho Remota do *myVM*, abra o PowerShell.
 2. Insira nslookup MyServer.Database.Windows.NET  você receberá uma mensagem semelhante a esta: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. Instalar SQL Server Management Studio 
 4. Em conectar ao servidor, insira ou selecione estas informações: Tipo de servidor: Selecione Mecanismo de Banco de Dados.
 Nome do servidor: Selecione myserver.database.windows.net nome de usuário: Insira um nome de usuário fornecido durante a criação.
 La Insira uma senha fornecida durante a criação.
 Lembrar senha: Selecione Sim.
 
 5. Selecione **conectar**.
 6. Procurar **bancos de dados** no menu à esquerda.
 7. Opcionalmente Criar ou consultar informações de *MyDatabase*
 8. Feche a conexão de área de trabalho remota para *myVm*.

## <a name="clean-up-resources"></a>Limpar recursos 
Quando não for mais necessário, você pode usar AZ Group Delete para remover o grupo de recursos e todos os recursos que ele tem: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [link privado do Azure](private-link-overview.md)
 
