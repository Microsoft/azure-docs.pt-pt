---
title: Link privado para base de dados Azure para postgreSQL - Método de configuração CLI do servidor único (pré-visualização)
description: Saiba como configurar link privado para Base de Dados Azure para PostgreSQL- Servidor único do Azure CLI
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 0eacf0f65346247d5fda5b26ead924a8cfd94dd9
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562093"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-preview-using-cli"></a>Criar e gerir link privado para base de dados Azure para PostgreSQL - Servidor único (Pré-visualização) utilizando cli

Um Private Endpoint é o bloco de construção fundamental para ligação privada em Azure. Permite que os recursos azure, como as Máquinas Virtuais (VMs), comuniquem privadamente com recursos de ligação privada. Neste artigo, você aprenderá a usar o Azure CLI para criar um VM em uma Rede Virtual Azure e uma Base de Dados Azure para postgreSQL single servidor com um ponto final privado Azure.

> [!NOTE]
> Esta funcionalidade está disponível em todas as regiões do Azure onde a Base de Dados Azure para o servidor Single PostgreSQL suporta os níveis de preços otimizados para fins gerais e memória.

## <a name="prerequisites"></a>Pré-requisitos

Para passar por este guia de como guiar, você precisa:

- Uma Base de Dados Azure para servidor e base de [dados PostgreSQL](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se decidir instalar e utilizar o Azure CLI localmente, este quickstart requer que utilize a versão 2.0.28 do Azure CLI ou posterior. Para encontrar a sua versão instalada, execute `az --version`. Consulte [o Azure CLI](/cli/azure/install-azure-cli) para instalar ou atualizar informações.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

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

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Criar uma Base de Dados Azure para PostgreSQL - Servidor único 
Crie uma Base de Dados Azure para PostgreSQL com o servidor az postgres criar comando. Lembre-se que o nome do seu Servidor PostgreSQL deve ser único em todo o Azure, por isso substitua o valor do espaço reservado em parênteses pelo seu próprio valor único: 

```azurecli-interactive
# Create a logical server in the resource group 
az postgres server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Note que o ID do Servidor PostgreSQL é semelhante ao ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/servername.``` Utilizará o ID do Servidor PostgreSQL no próximo passo. 

## <a name="create-the-private-endpoint"></a>Criar o Ponto Final Privado 
Crie um ponto final privado para o servidor PostgreSQL na sua Rede Virtual: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<PostgreSQL Server ID>" \  
    --group-ids postgresqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Configure a Zona Privada dNS 
Crie uma Zona DNS Privada para o domínio do servidor PostgreSQL e crie uma ligação de associação com a Rede Virtual. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.postgres.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.postgres.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for PostgreSQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.postgres.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
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
        > Poderá ter de selecionar **Mais escolhas** > **Utilize uma conta diferente,** para especificar as credenciais que inseriu quando criou o VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber um aviso de certificado, selecione **Sim** ou **Continue**.

1. Assim que o ambiente de trabalho vM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.  

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Aceda ao servidor PostgreSQL em privado a partir do VM

1. No Ambiente de Trabalho Remoto do *myVM,* abra a PowerShell.

2. Introduza  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`. 

    Receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4
    ```

3. Teste a ligação de ligação privada para o servidor PostgreSQL utilizando qualquer cliente disponível. No exemplo abaixo usei o [estúdio Azure Data](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15) para fazer a operação.

4. Em **nova ligação,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Tipo de servidor| Selecione **PostgreSQL**.|
    | Nome do servidor| Selecione *mydemopostgresserver.privatelink.postgres.database.azure.com* |
    | Nome de utilizador | Introduza o nome de utilizador como username@servername que é fornecido durante a criação do servidor PostgreSQL. |
    |Palavra-passe |Introduza uma palavra-passe fornecida durante a criação do servidor PostgreSQL. |
    |SSL|Selecione **Necessário**.|
    ||

5. Selecione Connect.

6. Consulte as bases de dados do menu esquerdo.

7. (Opcionalmente) Criar ou consultar informações a partir do servidor postgreSQL.

8. Feche a ligação remota do ambiente de trabalho ao myVm.

## <a name="clean-up-resources"></a>Limpar recursos 
Quando já não é necessário, pode utilizar o grupo AZ eliminar para remover o grupo de recursos e todos os recursos que tem: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o que é o ponto final privado do Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
