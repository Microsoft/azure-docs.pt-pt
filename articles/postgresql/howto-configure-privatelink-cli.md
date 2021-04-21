---
title: Link Privado - Azure CLI - Base de Dados Azure para PostgreSQL - Servidor Único
description: Saiba como configurar link privado para Azure Database para PostgreSQL- Servidor único da Azure CLI
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 410da33b531524f4a6458df13a89807fedd739a2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773338"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-cli"></a>Criar e gerir o Link Privado para Azure Database para PostgreSQL - Servidor único usando CLI

Um Ponto Final Privado é o bloco de construção fundamental para a ligação privada em Azure. Permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem privadamente com recursos de ligação privada. Neste artigo, você aprenderá a usar o CLI Azure para criar um VM em uma Rede Virtual Azure e uma Base de Dados Azure para servidor Single PostgreSQL com um ponto final privado Azure.

> [!NOTE]
> A funcionalidade de ligação privada só está disponível para a Base de Dados Azure para servidores PostgreSQL nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor de base de dados está num destes níveis de preços.

## <a name="prerequisites"></a>Pré-requisitos

Para passar por este guia, precisa:

- Uma [base de dados Azure para servidor e base de dados PostgreSQL](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se decidir instalar e utilizar o Azure CLI localmente, este quickstart requer que utilize a versão 2.0.28 ou posterior do Azure CLI. Para encontrar a sua versão instalada, corra `az --version` . Consulte [o Azure CLI](/cli/azure/install-azure-cli) para obter informações de instalação ou atualização.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar qualquer recurso, tem de criar um grupo de recursos para acolher a Rede Virtual. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na localização *westeurope:*

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Criar uma Rede Virtual
Criar uma Rede Virtual com [a rede Az vnet criar.](/cli/azure/network/vnet) Este exemplo cria uma Rede Virtual padrão chamada *myVirtualNetwork* com uma sub-rede chamada *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Desativar as políticas de ponto final privado da sub-rede 
O Azure implementa recursos para uma sub-rede dentro de uma rede virtual, pelo que é necessário criar ou atualizar a sub-rede para desativar as políticas privadas de rede de pontos [finais](../private-link/disable-private-endpoint-network-policy.md). Atualize uma configuração de sub-rede chamada *mySubnet* com [atualização da sub-rede de rede az:](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Criar a VM 
Criar um VM com az vm criar. Quando solicitado, forneça uma palavra-passe para ser usada como credenciais de entrada para o VM. Este exemplo cria um VM chamado *myVm:* 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```

 Note o endereço IP público do VM. Utilizará este endereço para ligar ao VM a partir da internet no passo seguinte.

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Criar uma base de dados Azure para PostgreSQL - Servidor Único 
Crie uma Base de Dados Azure para PostgreSQL com o servidor az postgres criar comando. Lembre-se que o nome do seu Servidor PostgreSQL deve ser único em todo o Azure, por isso substitua o valor do espaço reservado pelos seus próprios valores únicos que utilizou acima: 

```azurecli-interactive
# Create a server in the resource group 
az postgres server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

## <a name="create-the-private-endpoint"></a>Criar o Ponto Final Privado 
Crie um ponto final privado para o servidor PostgreSQL na sua Rede Virtual: 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforPostgreSQL/servers" --query "id" -o tsv) \    
    --group-id postgresqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Configure a Zona Privada de DNS 
Crie uma Zona PRIVADA de DNS para o domínio do servidor PostgreSQL e crie uma ligação de associação com a Rede Virtual. 

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
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> O FQDN na definição de DNS do cliente não resolve para o IP privado configurado. Terá de configurar uma zona DNS para o FQDN configurado, como mostrado [aqui.](../dns/dns-operations-recordsets-portal.md)

> [!NOTE]
> Em alguns casos, a Base de Dados Azure para PostgreSQL e a sub-rede VNet estão em diferentes subscrições. Nestes casos, deve assegurar as seguintes configurações:
> - Certifique-se de que ambas as subscrições têm o fornecedor de recursos **Microsoft.DBforPostgreSQL** registado. Para obter mais informações, consulte os [fornecedores de recursos.](../azure-resource-manager/management/resource-providers-and-types.md)

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Ligue-se ao *myVm VM* da internet da seguinte forma:

1. Na barra de pesquisa do portal, introduza *myVm*.

1. Selecione o botão **Ligar**. Depois de selecionar o botão **Ligar,** **o Connect à máquina virtual** abre-se.

1. Selecione **Transferir Ficheiro RDP**. O Azure cria um ficheiro Remote Desktop Protocol *(.rdp)* e transfere-o para o computador.

1. Abra o *ficheiro.rdp descarregado.*

    1. Se lhe for pedido, selecione **Ligar**.

    1. Introduza o nome de utilizador e a palavra-passe que especificou ao criar o VM.

        > [!NOTE]
        > Poderá ter de selecionar **Mais escolhas**  >  **Utilize uma conta diferente**, para especificar as credenciais que introduziu quando criou o VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se recebeu um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Assim que o ambiente de trabalho em VM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.  

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Aceda ao servidor PostgreSQL em privado a partir do VM

1. No Ambiente de Trabalho Remoto do *myVM,* abra o PowerShell.

2. Introduza  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`. 

   Irá receber uma mensagem semelhante a esta:

   ```azurepowershell
   Server:  UnKnown
   Address:  168.63.129.16
   Non-authoritative answer:
   Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
   Address:  10.1.3.4
   ```

3. Teste a ligação de ligação privada para o servidor PostgreSQL utilizando qualquer cliente disponível. O exemplo a seguir utiliza [o estúdio Azure Data](/sql/azure-data-studio/download) para fazer a operação.

4. Em **Nova ligação,** insira ou selecione estas informações:

   | Definição | Valor |
   | ------- | ----- |
   | Tipo de servidor| Selecione **PostgresQL**.|
   | Nome do servidor| Selecione *mydemopostgresserver.privatelink.postgres.database.azure.com* |
   | Nome de utilizador | Introduza o nome de utilizador como username@servername o fornecido durante a criação do servidor PostgreSQL. |
   |Palavra-passe |Introduza uma palavra-passe fornecida durante a criação do servidor PostgreSQL. |
   |SSL|**Selecione Necessário**.|
   ||

5. Selecione Ligar.

6. Procure bases de dados a partir do menu esquerdo.

7. (Opcionalmente) Criar ou consultar informações a partir do servidor postgreSQL.

8. Feche a ligação remota do ambiente de trabalho ao myVm.

## <a name="clean-up-resources"></a>Limpar os recursos 
Quando já não for necessário, pode utilizar o grupo AZ para remover o grupo de recursos e todos os recursos que tem: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o que é o ponto final privado do Azure](../private-link/private-endpoint-overview.md)
