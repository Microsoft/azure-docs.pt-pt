---
title: Quickstart - Criar um ponto final privado Azure usando o Azure CLI
description: Saiba mais sobre o ponto final privado do Azure neste Quickstart
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 8f2e21bddf0701ee6ce45af8012c853064e23168
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021760"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Quickstart: Criar um ponto final privado usando o Azure CLI

Private Endpoint é o bloco de construção fundamental para private link em Azure. Permite que os recursos azure, como máquinas virtuais (VMs), comuniquem em privado com os Recursos de Ligação Privada. Neste Quickstart, você aprenderá a criar um VM numa rede virtual, um servidor em Base de Dados SQL com um Endpoint privado usando o Azure CLI. Em seguida, pode aceder ao VM de forma segura para aceder e aceder de forma segura ao recurso de ligação privada (um servidor privado na Base de Dados SQL neste exemplo).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se decidir instalar e utilizar o Azure CLI localmente, este quickstart requer que utilize a versão 2.0.28 do Azure CLI ou posterior. Para encontrar a sua versão instalada, `az --version` corra. Consulte [o Azure CLI](/cli/azure/install-azure-cli) para instalar ou atualizar informações.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de poder criar qualquer recurso, tem de criar um grupo de recursos para acolher a Rede Virtual. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na localização *central-oeste:*

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
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

Tome nota do endereço IP público do VM. Utilizará este endereço para ligar ao VM a partir da internet no próximo passo.

## <a name="create-a-server-in-sql-database"></a>Criar um servidor na Base de Dados SQL

Crie um servidor na Base de Dados SQL com o servidor az sql criar comando. Lembre-se que o nome do seu servidor deve ser único em todo o Azure, por isso substitua o valor do espaço reservado em suportes pelo seu próprio valor único:

```azurecli-interactive
# Create a server in the resource group
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

O ID do servidor é semelhante ao  ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` De si utilizará o ID do servidor no próximo passo.

## <a name="create-the-private-endpoint"></a>Criar o Ponto Final Privado

Crie um ponto final privado para o servidor lógico SQL na sua Rede Virtual:

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Configure a Zona Privada dNS

Crie uma Zona DNS Privada para o domínio da Base de Dados SQL e crie uma ligação de associação com a Rede Virtual.

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

Ligue-se ao *myVm* VM da internet da seguinte forma:

1. Na barra de pesquisa do portal, entre no *myVm.*

1. Selecione o botão **Ligar**. Depois de selecionar o botão **Ligar,** **ligue-se à máquina virtual.**

1. Selecione **Download RDP File**. O Azure cria um ficheiro Remote Desktop *(.rdp)* e transfere-o para o computador.

1. Abra o ficheiro download.rdp*.

    1. Se lhe for pedido, selecione **Ligar**.

    1. Introduza o nome de utilizador e a palavra-passe especificado ao criar o VM.

        > [!NOTE]
        > Pode ser necessário selecionar **Mais escolhas**  >  **Utilize uma conta diferente,** para especificar as credenciais que inseriu quando criou o VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber um aviso de certificado, selecione **Sim** ou **Continue**.

1. Assim que o ambiente de trabalho vM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.  

## <a name="access-sql-database-privately-from-the-vm"></a>Aceder à Base de Dados SQL em privado a partir do VM

Nesta secção, irá ligar-se à Base de Dados SQL a partir do VM utilizando o Ponto Final Privado.

1. No Ambiente de Trabalho Remoto do *myVM,* abra a PowerShell.
2. Insira nslookup myserver.database.windows.net

   Receberá uma mensagem semelhante a esta:

    ```
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  myserver.database.windows.net
    ```

3. Instale o Estúdio de Gestão de Servidores SQL
4. No Connect ao servidor, insira ou selecione estas informações:

   - Tipo de servidor: Selecione Motor de Base de Dados.
   - Nome do servidor: Selecione myserver.database.windows.net
   - Nome de utilizador: Introduza um nome de utilizador fornecido durante a criação.
   - Palavra-passe: Introduza uma palavra-passe fornecida durante a criação.
   - Lembre-se da palavra-passe: Selecione Sim.

5. Selecione **Ligar**.
6. Consulte **as Bases** de Dados do menu esquerdo.
7. (Opcionalmente) Criar ou consultar informações a partir da *minha base de dados*
8. Feche a ligação remota de ambiente de trabalho ao *myVm*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não é necessário, pode utilizar o grupo AZ eliminar para remover o grupo de recursos e todos os recursos que tem:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [o Link Privado Azure](private-link-overview.md)
