---
title: Private Link - Azure CLI - Base de Dados Azure para MariaDB
description: Saiba como configurar link privado para Base de Dados Azure para MariaDB do Azure CLI
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: c28c5494c1cff2c198a94ea6b92003ae74ee2c8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371805"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-cli"></a>Criar e gerir link privado para base de dados Azure para MariaDB usando o CLI

Um Private Endpoint é o bloco de construção fundamental para ligação privada em Azure. Permite que os recursos azure, como as Máquinas Virtuais (VMs), comuniquem privadamente com recursos de ligação privada. Neste artigo, você aprenderá a usar o Azure CLI para criar um VM em uma Rede Virtual Azure e uma Base de Dados Azure para servidor MariaDB com um ponto final privado Azure.

> [!NOTE]
> Esta funcionalidade está disponível em todas as regiões do Azure onde a Base de Dados Azure para MariaDB suporta níveis de preços otimizados para fins gerais e memória.

## <a name="prerequisites"></a>Pré-requisitos

Para passar por este guia de como guiar, você precisa:

- Uma [Base de Dados Azure para servidor MariaDB.](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se decidir instalar e utilizar o Azure CLI localmente, este quickstart requer que utilize a versão 2.0.28 do Azure CLI ou posterior. Para encontrar a sua `az --version`versão instalada, corra. Consulte [o Azure CLI](/cli/azure/install-azure-cli) para instalar ou atualizar informações.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de poder criar qualquer recurso, tem de criar um grupo de recursos para acolher a Rede Virtual. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na localização *da Europa Ocidental:*

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Criar uma Rede Virtual
Criar uma Rede Virtual com a [z network vnet criar](/cli/azure/network/vnet). Este exemplo cria uma rede virtual padrão chamada *myVirtualNetwork* com uma subnet chamada *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Desativar as políticas de ponto final privado de subnet 
O Azure utiliza recursos para uma subrede dentro de uma rede virtual, pelo que precisa de criar ou atualizar a subrede para desativar as políticas de rede de pontos finais privados. Atualize uma configuração de sub-rede chamada *mySubnet* com [aaz rede vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Crie a VM 
Crie um VM com az vm criar. Quando solicitado, forneça uma palavra-passe para ser usado como credenciais de entrada para o VM. Este exemplo cria um VM chamado *myVm:* 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Note o endereço IP público do VM. Utilizará este endereço para ligar ao VM a partir da internet no próximo passo.

## <a name="create-an-azure-database-for-mariadb-server"></a>Criar um Azure Database for MariaDB Server 
Crie uma Base de Dados Azure para MariaDB com o servidor az mariadb criar comando. Lembre-se que o nome do seu Servidor MariaDB deve ser único em todo o Azure, por isso substitua o valor do espaço reservado em parênteses pelo seu próprio valor único: 

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

Note que o ID do ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMariaDB/servers/servername.``` Servidor MariaDB é semelhante ao de Você utilizará o ID do Servidor MariaDB no próximo passo. 

## <a name="create-the-private-endpoint"></a>Criar o Ponto Final Privado 
Crie um ponto final privado para o servidor MariaDB na sua Rede Virtual: 
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

## <a name="configure-the-private-dns-zone"></a>Configure a Zona Privada dNS 
Crie uma Zona DNS Privada para o domínio do servidor MariDB e crie uma ligação de associação com a Rede Virtual. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mariadb.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mariadb.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MariaDB name 
 
 
#Create DNS records 
az network private-dns record-set a create --name mydemoserver --zone-name privatelink.mariadb.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name mydemoserver --zone-name privatelink.mariadb.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> O FQDN na definição de DNS do cliente não resolve o IP privado configurado. Terá de configurar uma zona DNS para o FQDN configurado, como mostrado [aqui](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Ligue-se ao *myVm* VM da internet da seguinte forma:

1. Na barra de pesquisa do portal, entre no *myVm.*

1. Selecione o botão **Ligar**. Depois de selecionar o botão **Ligar,** **ligue-se à máquina virtual.**

1. Selecione **Download RDP File**. O Azure cria um ficheiro Remote Desktop *(.rdp)* e transfere-o para o computador.

1. Abra o ficheiro *download.rdp.*

    1. Se lhe for pedido, selecione **Ligar**.

    1. Introduza o nome de utilizador e a palavra-passe especificado ao criar o VM.

        > [!NOTE]
        > Pode ser necessário selecionar **Mais escolhas** > **Utilize uma conta diferente,** para especificar as credenciais que inseriu quando criou o VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber um aviso de certificado, selecione **Sim** ou **Continue**.

1. Assim que o ambiente de trabalho vM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.  

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>Aceda ao servidor MariaDB em privado a partir do VM

1. No Ambiente de Trabalho Remoto do *myVM,* abra a PowerShell.

2. Introduza  `nslookup mydemoserver.privatelink.mariadb.database.azure.com`. 

    Receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. Teste a ligação de ligação privada para o servidor MariaDB utilizando qualquer cliente disponível. No exemplo abaixo usei a [bancada mySQL](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) para fazer a operação.

4. Em **nova ligação,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome da Ligação| Selecione o nome de ligação à sua escolha.|
    | Nome de anfitrião | Selecione *mydemoserver.privatelink.mariadb.database.azure.com* |
    | Nome de utilizador | Introduza o *username@servername* nome de utilizador como o que é fornecido durante a criação do servidor MariaDB. |
    | Palavra-passe | Introduza uma palavra-passe fornecida durante a criação do servidor MariaDB. |
    ||

5. Selecione **ligação de teste** ou **OK**.

6. (Opcionalmente) Consulte as bases de dados do menu esquerdo e crie ou consulte informações a partir da base de dados MariaDB

8. Feche a ligação remota do ambiente de trabalho ao myVm.

## <a name="clean-up-resources"></a>Limpar recursos 
Quando já não é necessário, pode utilizar o grupo AZ eliminar para remover o grupo de recursos e todos os recursos que tem: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [o que é o ponto final privado do Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
