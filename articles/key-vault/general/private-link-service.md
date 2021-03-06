---
title: Integrar no Serviço Azure Private Link
description: Saiba como integrar o Azure Key Vault com o Azure Private Link Service
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: e5ddffb17c8f5acf16cf89dd58c634b6e404bf7b
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749551"
---
# <a name="integrate-key-vault-with-azure-private-link"></a>Integrar o Key Vault no Azure Private Link

O Azure Private Link Service permite-lhe aceder aos Serviços Azure (por exemplo, Azure Key Vault, Azure Storage e Azure Cosmos DB) e a Azure acolheu serviços de cliente/parceiro sobre um Ponto Final Privado na sua rede virtual.

Um Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura a um serviço alimentado pela Azure Private Link. O ponto final privado utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN, ou endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acessos.

Para mais informações, consulte [o que é a Azure Private Link?](../../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

Para integrar um cofre-chave com a Azure Private Link, necessitará do seguinte:

- Um cofre.
- Uma rede virtual Azure.
- Uma sub-rede na rede virtual.
- Permissões de proprietário ou contribuinte para o cofre chave e a rede virtual.

O seu ponto final privado e rede virtual devem estar na mesma região. Quando selecionar uma região para o ponto final privado utilizando o portal, irá filtrar automaticamente apenas redes virtuais que se encontrem nessa região. O seu cofre pode estar numa região diferente.

O seu ponto final privado utiliza um endereço IP privado na sua rede virtual.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

## <a name="establish-a-private-link-connection-to-key-vault-using-the-azure-portal"></a>Estabeleça uma ligação privada ao Key Vault utilizando o portal Azure 

Em primeiro lugar, crie uma rede virtual seguindo os passos na [Criar uma rede virtual utilizando o portal Azure](../../virtual-network/quick-create-portal.md)

Em seguida, pode criar um novo cofre de chaves ou estabelecer uma ligação privada a um cofre de chaves existente.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Crie um novo cofre chave e estabeleça uma ligação de ligação privada

Pode criar um novo cofre com o [portal Azure](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md)ou [Azure PowerShell](../general/quick-create-powershell.md).

Depois de configurar o básico do cofre da chave, selecione o separador de rede e siga estes passos:

1. Selecione o botão de rádio Private Endpoint no separador 'Rede'.
1. Clique no botão "+ adicionar" para adicionar um ponto final privado.

    ![Screenshot que mostra o separador 'Networking' na página 'Criar cofre de chaves'.](../media/private-link-service-1.png)
 
1. No campo "Localização" da Lâmina de Ponto Final Privado, selecione a região em que a sua rede virtual está localizada. 
1. No campo "Nome", crie um nome descritivo que lhe permitirá identificar este ponto final privado. 
1. Selecione a rede virtual e a sub-rede deseja que este ponto final privado seja criado a partir do menu suspenso. 
1. Deixe inalterada a opção "integrar-se com a zona privada DNS".  
1. Selecione "Ok".

    ![Screenshot que mostra a página 'Criar ponto final privado' com definições selecionadas.](../media/private-link-service-8.png)
 
Agora poderá ver o ponto final privado configurado. Tem agora a opção de eliminar e editar este ponto final privado. Selecione o botão "Rever + Criar" e crie o cofre de chaves. Levará 5 a 10 minutos para a colocação estar completa. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Estabeleça uma ligação privada a um cofre de chaves existente

Se já tiver um cofre chave, pode criar uma ligação de ligação privada seguindo estes passos:

1. Inicie sessão no portal do Azure. 
1. Na barra de pesquisa, escreva em "cofres-chave"
1. Selecione o cofre de chaves da lista à qual pretende adicionar um ponto final privado.
1. Selecione o separador "Networking" em Definições
1. Selecione o separador de ligações de ponto final privado no topo da página
1. Selecione o botão "+ Ponto final privado" na parte superior da página.

    ![Screenshot que mostra o botão '+ Ponto final privado' na página 'Networking'.](../media/private-link-service-3.png)
    ![Screenshot que mostra o separador 'Básicos' na página 'Criar um ponto final privado (Pré-visualização).](../media/private-link-service-4.png)

Pode optar por criar um ponto final privado para qualquer recurso Azure na utilização desta lâmina. Pode utilizar os menus suspensos para selecionar um tipo de recurso e selecionar um recurso no seu diretório, ou pode ligar-se a qualquer recurso Azure utilizando um ID de recurso. Deixe inalterada a opção "integrar-se com a zona privada DNS".  

![Screenshot que mostra a adição de um ponto final privado usando a lâmina atual. ](../media/private-link-service-3.png)
 ![ Screenshot que mostra um exemplo da página 'Criar um ponto final privado (Pré-visualização).](../media/private-link-service-4.png)

Quando criar um ponto final privado, a ligação tem de ser aprovada. Se o recurso para o qual está a criar um ponto final privado estiver no seu diretório, poderá aprovar o pedido de ligação desde que tenha permissões suficientes; se estiver a ligar-se a um recurso Azure noutro diretório, deve esperar que o proprietário desse recurso aprove o seu pedido de ligação.

Existem quatro estados de provisionamento:

| Prestação de serviços | Estado de ponto final privado do consumidor de serviço | Descrição |
|--|--|--|
| Nenhum | Pendente | A ligação é criada manualmente e está pendente de aprovação do proprietário do recurso Private Link. |
| Aprovar | Aprovado | A ligação foi aprovada automaticamente ou manualmente e está pronta a ser utilizada. |
| Rejeitar | Rejeitado | A ligação foi rejeitada pelo proprietário de recursos de ligação privada. |
| Remover | Desligado | A ligação foi removida pelo proprietário do recurso de ligação privada, o ponto final privado torna-se informativo e deve ser eliminado para limpeza. |

### <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-the-azure-portal"></a>Como gerir uma ligação privada ao Key Vault utilizando o portal Azure 

1. Inicie sessão no portal do Azure.
1. Na barra de pesquisa, escreva em "cofres-chave"
1. Selecione o cofre de chaves que pretende gerir.
1. Selecione o separador "Networking".
1. Se houver alguma ligação pendente, verá uma ligação listada com "Pendente" no estado de provisionamento. 
1. Selecione o ponto final privado que deseja aprovar
1. Selecione o botão de aprovação.
1. Se houver alguma ligação de ponto final privado que pretenda rejeitar, seja um pedido pendente ou uma ligação existente, selecione a ligação e clique no botão "Rejeitar".

    ![Imagem](../media/private-link-service-7.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

## <a name="establish-a-private-link-connection-to-key-vault-using-cli-initial-setup"></a>Estabelecer uma ligação privada ao Key Vault utilizando o CLI (Configuração Inicial)

```azurecli
az login                                                         # Login to Azure CLI
az account set --subscription {SUBSCRIPTION ID}                  # Select your Azure Subscription
az group create -n {RESOURCE GROUP} -l {REGION}                  # Create a new Resource Group
az provider register -n Microsoft.KeyVault                       # Register KeyVault as a provider
az keyvault create -n {VAULT NAME} -g {RG} -l {REGION}           # Create a Key Vault
az keyvault update -n {VAULT NAME} -g {RG} --default-action deny # Turn on Key Vault Firewall
az network vnet create -g {RG} -n {vNet NAME} -location {REGION} # Create a Virtual Network

    # Create a Subnet
az network vnet subnet create -g {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}

    # Disable Virtual Network Policies
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true

    # Create a Private DNS Zone
az network private-dns zone create --resource-group {RG} --name privatelink.vaultcore.azure.net

    # Link the Private DNS Zone to the Virtual Network
az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.vaultcore.azure.net --name {dnsZoneLinkName} --registration-enabled true

```

### <a name="create-a-private-endpoint-automatically-approve"></a>Criar um ponto final privado (aprovar automaticamente) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/{KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION}
```

### <a name="create-a-private-endpoint-manually-request-approval"></a>Criar um ponto final privado (Aprovação de pedido manual) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/{KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```

### <a name="manage-private-link-connections"></a>Gerir ligações de ligações privadas

```azurecli
# Show Connection Status
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}

# Approve a Private Link Connection Request
az keyvault private-endpoint-connection approve --approval-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}

# Deny a Private Link Connection Request
az keyvault private-endpoint-connection reject --rejection-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}

# Delete a Private Link Connection Request
az keyvault private-endpoint-connection delete --resource-group {RG} --vault-name {KEY VAULT NAME} --name {PRIVATE LINK CONNECTION NAME}
```

### <a name="add-private-dns-records"></a>Adicionar registos dns privados
```azurecli
# Determine the Private Endpoint IP address
az network private-endpoint show -g {RG} -n {PE NAME}      # look for the property networkInterfaces then id; the value must be placed on {PE NIC} below.
az network nic show --ids {PE NIC}                         # look for the property ipConfigurations then privateIpAddress; the value must be placed on {NIC IP} below.

# https://docs.microsoft.com/en-us/azure/dns/private-dns-getstarted-cli#create-an-additional-dns-record
az network private-dns zone list -g {RG}
az network private-dns record-set a add-record -g {RG} -z "privatelink.vaultcore.azure.net" -n {KEY VAULT NAME} -a {NIC IP}
az network private-dns record-set list -g {RG} -z "privatelink.vaultcore.azure.net"

# From home/public network, you wil get a public IP. If inside a vnet with private zone, nslookup will resolve to the private ip.
nslookup {KEY VAULT NAME}.vault.azure.net
nslookup {KEY VAULT NAME}.privatelink.vaultcore.azure.net
```

---

## <a name="validate-that-the-private-link-connection-works"></a>Validar que a ligação de ligação privada funciona

Deve validar que os recursos dentro da mesma sub-rede do recurso de ponto final privado estão a ligar-se ao cofre-chave sobre um endereço IP privado, e que têm a integração de zonas de DNS privadas correta.

Primeiro, crie uma máquina virtual seguindo os passos na [Criar uma máquina virtual Windows no portal Azure](../../virtual-machines/windows/quick-create-portal.md)

No separador "Networking":

1. Especifique a rede virtual e a sub-rede. Pode criar uma nova rede virtual ou selecionar uma existente. Se selecionar um existente, certifique-se de que a região corresponde.
1. Especifique um recurso IP público.
1. No "grupo de segurança da rede NIC", selecione "Nenhum".
1. No "Equilíbrio de carga", selecione "Não".

Abra a linha de comando e executar o seguinte comando:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Se executar o comando de procuração ns para resolver o endereço IP de um cofre chave sobre um ponto final público, verá um resultado que se parece com este:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Se executar o comando de procuração ns para resolver o endereço IP de um cofre chave sobre um ponto final privado, verá um resultado que se parece com este:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="troubleshooting-guide"></a>Guia de Resolução de Problemas

* Verifique se o ponto final privado está no estado aprovado. 
    1. Pode verificar e corrigir esta situação no portal do Azure. Abra o recurso do Key Vault e clique na opção Rede. 
    2. Em seguida, selecione o separador de ligações de ponto final privado. 
    3. Certifique-se de que o estado de ligação é aprovado e que o estado de provisionamento é bem sucedido. 
    4. Você também pode navegar para o recurso de ponto final privado e rever as mesmas propriedades lá, e verificar duas vezes que a rede virtual corresponde à que você está usando.

* Verifique se tem um recurso privado da Zona DNS. 
    1. Você deve ter um recurso privado dns Zone com o nome exato: privatelink.vaultcore.azure.net. 
    2. Para aprender a configurar isto, consulte o seguinte link. [Zonas privadas de DNS](../../dns/private-dns-privatednszone.md)
    
* Verifique se a Zona DNS Privado não está ligada à Rede Virtual. Este pode ser o problema se ainda estiver a receber o endereço IP público devolvido. 
    1. Se o DNS da Zona Privada não estiver ligado à rede virtual, a consulta DNS originária da rede virtual devolverá o endereço IP público do cofre-chave. 
    2. Navegue para o recurso Private DNS Zone no portal Azure e clique na opção de links de rede virtual. 
    4. A rede virtual que irá executar chamadas para o cofre-chave deve estar listada. 
    5. Se não estiver lá, adicione. 
    6. Para etapas detalhadas, consulte o seguinte documento [Link Virtual Network to Private DNS Zone](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)

* Verifique se a Zona Privada de DNS não está a perder um registo A para o cofre da chave. 
    1. Navegue para a página Privada dns Zone. 
    2. Clique em Descrição geral e verifique se há um registo A com o nome simples do cofre de chaves (ou seja, fabrikam). Não especifique nenhum sufixo.
    3. Verifique a ortografia e crie ou corrija o registo A. Pode utilizar um TTL de 3600 (1 hora). 
    4. Certifique-se de que especifica o endereço IP privado correto. 
    
* Verifique se o registo A tem o endereço IP correto. 
    1. Pode confirmar o endereço IP abrindo o recurso Private Endpoint no portal Azure.
    2. Aceda ao recurso Microsoft.Network/privateEndpoints, no portal do Azure (não no recurso do Key Vault)
    3. Na página geral procure a interface da Rede e clique nesse link. 
    4. O link mostrará a visão geral do recurso NIC, que contém o endereço IP privado da propriedade. 
    5. Verifique se este é o endereço IP correto especificado no registo A.

## <a name="limitations-and-design-considerations"></a>Limitações e Considerações de Design

> [!NOTE]
> O número de cofres-chave com pontos finais privados ativados por subscrição é um limite ajustável. O limite abaixo indicado é o limite de predefinição. Se pretender solicitar um aumento limite para o seu serviço, por favor envie um e-mail para akv-privatelink@microsoft.com . Aprovaremos estes pedidos caso a caso.

**Preços**: Para obter informações sobre preços, consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).

**Limitações**: O Ponto Final Privado para a Azure Key Vault só está disponível nas regiões públicas de Azure.

**Número máximo de pontos finais privados por porta-chaves:** 64.

**Número predefinido de cofres-chave com pontos de final privados por subscrição:** 400.

Para mais informações, consulte [o serviço Azure Private Link: Limitações](../../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [o Azure Private Link](../../private-link/private-link-service-overview.md)
- Saiba mais sobre [o Azure Key Vault](overview.md)
