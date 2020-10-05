---
title: Quickstart - Criar um ponto final privado Azure usando O Azure CLI
description: Saiba mais sobre o ponto final privado da Azure neste Quickstart
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: e7c098ba06086781306960f76978aac9e4fa06bc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87502669"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Quickstart: Criar um ponto final privado usando O Azure CLI

Private Endpoint é o bloco de construção fundamental para Private Link em Azure. Permite que os recursos da Azure, como máquinas virtuais (VMs), comuniquem em privado com os Recursos de Ligação Privada. Neste Quickstart, aprenderá a criar um VM numa rede virtual, um servidor na Base de Dados SQL com um Ponto Final Privado utilizando o Azure CLI. Em seguida, pode aceder ao VM e aceder de forma segura ao recurso de ligação privada (um servidor privado na Base de Dados SQL neste exemplo).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se decidir instalar e utilizar o Azure CLI localmente, este quickstart requer que utilize a versão 2.0.28 ou posterior do Azure CLI. Para encontrar a sua versão instalada, corra `az --version` . Consulte [o Azure CLI](/cli/azure/install-azure-cli) para obter informações de instalação ou atualização.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar qualquer recurso, tem de criar um grupo de recursos para acolher a Rede Virtual. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na localização *westcentralus:*

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
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

O Azure implementa recursos para uma sub-rede dentro de uma rede virtual, pelo que é necessário criar ou atualizar a sub-rede para desativar as políticas privadas de rede de pontos finais. Atualize uma configuração de sub-rede chamada *mySubnet* com [atualização da sub-rede de rede az:](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update)

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

Tome nota do endereço IP público do VM. Utilizará este endereço para ligar ao VM a partir da internet no passo seguinte.

## <a name="create-a-server-in-sql-database"></a>Criar um servidor na Base de Dados SQL

Crie um servidor na Base de Dados SQL com o servidor az sql criar comando. Lembre-se que o nome do seu servidor deve ser único em todo o Azure, por isso substitua o valor do espaço reservado nos parênteses pelo seu próprio valor único:

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

O ID do servidor é semelhante ao  ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` de You utilizará o ID do servidor no passo seguinte.

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

## <a name="configure-the-private-dns-zone"></a>Configure a Zona Privada de DNS

Crie uma Zona Privada de DNS para domínio SQL Database, crie uma ligação de associação com a Rede Virtual e crie um Grupo de Zonas DNS para associar o ponto final privado à Zona Privada de DNS. 

```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \
   --name  "privatelink.database.windows.net"
az network private-dns link vnet create --resource-group myResourceGroup \
   --zone-name  "privatelink.database.windows.net"\
   --name MyDNSLink \
   --virtual-network myVirtualNetwork \
   --registration-enabled false
az network private-endpoint dns-zone-group create \
   --resource-group myResourceGroup \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Ligue-se ao *myVm VM* da internet da seguinte forma:

1. Na barra de pesquisa do portal, introduza *myVm*.

1. Selecione o botão **Ligar**. Depois de selecionar o botão **Ligar,** **o Connect à máquina virtual** abre-se.

1. Selecione **Transferir Ficheiro RDP**. O Azure cria um ficheiro Remote Desktop Protocol *(.rdp)* e transfere-o para o computador.

1. Abra o ficheiro .rdp* descarregado.

    1. Se lhe for pedido, selecione **Ligar**.

    1. Introduza o nome de utilizador e a palavra-passe que especificou ao criar o VM.

        > [!NOTE]
        > Poderá ter de selecionar **Mais escolhas**  >  **Utilize uma conta diferente**, para especificar as credenciais que introduziu quando criou o VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se recebeu um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Assim que o ambiente de trabalho em VM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.  

## <a name="access-sql-database-privately-from-the-vm"></a>Acesso SQL Base de Dados privada a partir do VM

Nesta secção, irá ligar-se à Base de Dados SQL a partir do VM utilizando o Ponto Final Privado.

1. No Ambiente de Trabalho Remoto do *myVM,* abra o PowerShell.
2. Insira myserver.database.windows.net nslookup

   Irá receber uma mensagem semelhante a esta:

    ```
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  myserver.database.windows.net
    ```

3. Instalar o SQL Server Management Studio
4. Em Ligar ao servidor, insira ou selecione estas informações:

   - Tipo de servidor: Selecione o motor da base de dados.
   - Nome do servidor: Selecione myserver.database.windows.net
   - Nome de utilizador: Introduza um nome de utilizador fornecido durante a criação.
   - Senha: Introduza uma palavra-passe fornecida durante a criação.
   - Lembre-se da palavra-passe: Selecione Sim.

5. Selecione **Ligar**.
6. Procure bases **de dados a** partir do menu esquerdo.
7. (Opcionalmente) Criar ou consultar informações a partir da minha base de *dados*
8. Feche a ligação remota do ambiente de trabalho ao *myVm*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o grupo AZ para remover o grupo de recursos e todos os recursos que tem:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o Azure Private Link](private-link-overview.md)
