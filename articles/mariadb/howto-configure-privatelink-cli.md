---
title: Ligação Privada - Azure CLI - Base de Dados Azure para MariaDB
description: Saiba como configurar link privado para Azure Database for MariaDB da Azure CLI
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 55f375c83affea8585ec7ebf881a80315ff7a38c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361327"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-cli"></a>Criar e gerir o Link Privado para Azure Database para MariaDB utilizando o CLI

Um Ponto Final Privado é o bloco de construção fundamental para a ligação privada em Azure. Permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem privadamente com recursos de ligação privada. Neste artigo, você aprenderá a usar o CLI Azure para criar um VM em uma Rede Virtual Azure e uma Base de Dados Azure para servidor MariaDB com um ponto final privado Azure.

> [!NOTE]
> A funcionalidade de ligação privada só está disponível para a Base de Dados Azure para servidores MariaDB nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor de base de dados está num destes níveis de preços.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Precisa de uma [base de dados Azure para o servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md). 

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo requer a versão 2.0.28 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

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
O Azure implementa recursos para uma sub-rede dentro de uma rede virtual, pelo que é necessário criar ou atualizar a sub-rede para desativar as políticas privadas de rede de pontos [finais](../private-link/disable-private-endpoint-network-policy.md). Atualize uma configuração de sub-rede chamada *mySubnet* com [atualização da sub-rede de rede az:](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)

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

## <a name="create-an-azure-database-for-mariadb-server"></a>Criar um Azure Database for MariaDB Server 
Crie uma Base de Dados Azure para MariaDB com o servidor az mariadb criar comando. Lembre-se que o nome do seu Servidor MariaDB deve ser único em todo o Azure, por isso substitua o valor do espaço reservado nos parênteses pelo seu próprio valor único: 

```azurecli-interactive
# Create a server in the resource group 
az mariadb server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

> [!NOTE]
> Em alguns casos, a Base de Dados Azure para MariaDB e a sub-rede VNet estão em diferentes subscrições. Nestes casos, deve assegurar as seguintes configurações:
> - Certifique-se de que ambas as subscrições têm o fornecedor de recursos **Microsoft.DBforMariaDB** registado. Para mais informações, consulte [o gestor de recursos-registo][resource-manager-portal]

## <a name="create-the-private-endpoint"></a>Criar o Ponto Final Privado 
Crie um ponto final privado para o servidor MariaDB na sua Rede Virtual: 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforMariaDB/servers" --query "id" -o tsv) \    
    --group-id mariadbServer \  
    --connection-name myConnection  
 ```


## <a name="configure-the-private-dns-zone"></a>Configure a Zona Privada de DNS 
Crie uma Zona PRIVADA de DNS para o domínio do servidor MariDB e crie uma ligação de associação com a Rede Virtual. 
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
az network private-dns record-set a add-record --record-set-name mydemoserver --zone-name privatelink.mariadb.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> O FQDN na definição de DNS do cliente não se resolve com o IP privado configurado. Terá de configurar uma zona DNS para o FQDN configurado, como mostrado [aqui.](../dns/dns-operations-recordsets-portal.md)

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

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>Aceda ao servidor MariaDB em privado a partir do VM

1. No Ambiente de Trabalho Remoto do *myVM,* abra o PowerShell.

2. Introduza  `nslookup mydemoserver.privatelink.mariadb.database.azure.com`. 

    Irá receber uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. Teste a ligação de ligação privada para o servidor MariaDB utilizando qualquer cliente disponível. No exemplo abaixo, usei a [bancada mySQL workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) para fazer a operação.

4. Em **Nova ligação,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome da Ligação| Selecione o nome de ligação da sua escolha.|
    | Hostname (Nome do anfitrião) | Selecione *mydemoserver.privatelink.mariadb.database.azure.com* |
    | Nome de utilizador | Insira o nome de utilizador como *username@servername* o fornecido durante a criação do servidor MariaDB. |
    | Palavra-passe | Introduza uma palavra-passe fornecida durante a criação do servidor MariaDB. |
    ||

5. Selecione **a ligação de teste** ou **OK**.

6. (Opcionalmente) Pesquise bases de dados a partir do menu esquerdo e Crie ou consulte informações da base de dados MariaDB

8. Feche a ligação remota do ambiente de trabalho ao myVm.

## <a name="clean-up-resources"></a>Limpar os recursos 
Quando já não for necessário, pode utilizar o grupo AZ para remover o grupo de recursos e todos os recursos que tem: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [o que é o ponto final privado do Azure](../private-link/private-endpoint-overview.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
