---
title: Configurar pontos finais da rede Azure Files [ Microsoft Docs
description: Uma visão geral das opções de networking para Ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cc487e8def180735606aa010651dde40ef93908e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80082508"
---
# <a name="configuring-azure-files-network-endpoints"></a>Configurar pontos finais da rede Azure Files
O Azure Files fornece dois tipos principais de pontos finais para aceder a ações de ficheiros Azure: 
- Pontos finais públicos, que têm um endereço IP público e podem ser acedidos a partir de qualquer parte do mundo.
- Pontos finais privados, que existem dentro de uma rede virtual e têm um endereço IP privado a partir do espaço de endereço dessa rede virtual.

Existem pontos finais públicos e privados na conta de armazenamento Azure. Uma conta de armazenamento é uma construção de gestão que representa um conjunto partilhado de armazenamento no qual você pode implementar várias ações de arquivo, bem como outros recursos de armazenamento, tais como recipientes de bolhas ou filas.

Este artigo centra-se na forma de configurar os pontos finais de uma conta de armazenamento para aceder diretamente à partilha de ficheiros Azure. A maior parte dos detalhes fornecidos neste documento também se aplica à forma como o Azure File Sync opera com pontos finais públicos e privados para a conta de armazenamento, no entanto, para mais informações sobre considerações de networking para uma implementação de Sincronização de Ficheiros Azure, consulte [configurar as definições](storage-sync-files-firewall-and-proxy.md)de proxy e firewall do Ficheiro Azure.

Recomendamos que leia as considerações de [networking do Azure Files](storage-files-networking-overview.md) antes de ler isto como orientar.

## <a name="prerequisites"></a>Pré-requisitos
- Este artigo assume que já criou uma subscrição azure. Se ainda não tem uma subscrição, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Este artigo assume que já criou uma partilha de ficheiros Azure numa conta de armazenamento à qual gostaria de se ligar a partir do local. Para aprender a criar uma partilha de ficheiros Azure, consulte [Create a Azure file share](storage-how-to-create-file-share.md).
- Se pretende utilizar o Azure PowerShell, [instale a versão mais recente.](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Se pretender utilizar o Azure CLI, [instale a versão mais recente.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-private-endpoint"></a>Criar um ponto final privado
A criação de um ponto final privado para a sua conta de armazenamento resultará na implantação dos seguintes recursos Azure:

- **Um ponto final privado**: Um recurso Azure que representa o ponto final privado da conta de armazenamento. Pode pensar nisto como um recurso que liga uma conta de armazenamento e uma interface de rede.
- **Uma interface de rede (NIC)**: A interface de rede que mantém um endereço IP privado dentro da rede virtual/sub-rede especificada. Este é exatamente o mesmo recurso que é implantado quando você implanta uma máquina virtual, no entanto, em vez de ser atribuído a um VM, é propriedade do ponto final privado.
- **Uma zona privada de DNS**: Se nunca tiver implantado um ponto final privado para esta rede virtual antes, uma nova zona privada de DNS será implantada para a sua rede virtual. Será também criado um registo DNS Um registo para a conta de armazenamento nesta zona dNS. Se já implementou um ponto final privado nesta rede virtual, será adicionado um novo registo A para a conta de armazenamento à zona DNS existente. A implementação de uma zona DNS é opcional, por muito recomendada que seja, e necessária se estiver a montar as suas ações de ficheiro Azure com um diretor de serviço aD ou utilizar a API FileREST.

> [!Note]  
> Este artigo utiliza a conta de armazenamento DNS sufixo para as regiões públicas de Azure, `core.windows.net`. Este comentário também se aplica às nuvens soberanas do Azure, como a nuvem do Governo dos EUA azure e a nuvem azure china - apenas substitua os sufixos apropriados para o seu ambiente. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navegue para a conta de armazenamento para a qual gostaria de criar um ponto final privado. Na tabela de conteúdos para a conta de armazenamento, selecione **ligações de ponto final privado,** e depois **+ ponto final privado** para criar um novo ponto final privado. 

![Uma imagem do item de ligações de ponto final privado na tabela de conta de armazenamento do conteúdo](media/storage-files-networking-endpoints/create-private-endpoint-0.png)

O assistente resultante tem várias páginas para completar.

Na lâmina **Basics,** selecione o grupo de recursos, nome e região desejados para o seu ponto final privado. Estes podem ser o que você quiser, eles não têm que combinar a conta de armazenamento em de qualquer maneira, embora você deve criar o ponto final privado na mesma região que a rede virtual que você deseja criar o ponto final privado em.

![Uma imagem da secção Basics da secção de ponto final criar](media/storage-files-networking-endpoints/create-private-endpoint-1.png)

Na lâmina **De recurso,** selecione o botão de rádio para **Ligar a um recurso Azure no meu diretório**. No **tipo de Recurso,** selecione **Microsoft.Storage/storageAccounts** for the resource type. O campo **Derecursos** é a conta de armazenamento com a partilha de ficheiros Azure a que pretende ligar. O subrecurso alvo é **ficheiro,** uma vez que se trata de Ficheiros Azure.

A lâmina de **Configuração** permite-lhe selecionar a rede virtual específica e a sub-rede a que gostaria de adicionar o seu ponto final privado. Selecione a rede virtual que criou acima. Deve selecionar uma sub-rede distinta da sub-rede que adicionou o seu ponto final de serviço acima. A lâmina de configuração também contém as informações para criar/atualizar a zona Privada DNS. Recomendamos a `privatelink.file.core.windows.net` utilização da zona predefinida.

![Uma imagem da secção de Configuração](media/storage-files-networking-endpoints/create-private-endpoint-2.png)

Clique em **Rever + criar** para criar o ponto final privado. 

Se tiver uma máquina virtual dentro da sua rede virtual, ou tiver configurado o reencaminhadod DNS como descrito [aqui,](storage-files-networking-dns.md)pode testar que o seu ponto final privado foi configurado corretamente executando os seguintes comandos da PowerShell, da linha de comando ou do terminal (funciona para Windows, Linux ou macOS). Deve substituir `<storage-account-name>` pelo nome da conta de armazenamento apropriada:

```
nslookup <storage-account-name>.file.core.windows.net
```

Se tudo tiver funcionado com sucesso, deverá `192.168.0.5` ver a seguinte saída, onde está o endereço IP privado do ponto final privado na sua rede virtual (saída mostrada para Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para criar um ponto final privado para a sua conta de armazenamento, primeiro precisa de obter uma referência à sua conta de armazenamento e à subnet de rede virtual à qual pretende adicionar o ponto final privado. Substitua, `<vnet-resource-group-name>` `<storage-account-resource-group-name>` `<storage-account-name>` `<vnet-name>`, `<vnet-subnet-name>` , e abaixo:

```PowerShell
$storageAccountResourceGroupName = "<storage-account-resource-group-name>"
$storageAccountName = "<storage-account-name>"
$virtualNetworkResourceGroupName = "<vnet-resource-group-name>"
$virtualNetworkName = "<vnet-name>"
$subnetName = "<vnet-subnet-name>"

# Get storage account reference, and throw error if it doesn't exist
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name $storageAccountName `
        -ErrorAction SilentlyContinue

if ($null -eq $storageAccount) {
    $errorMessage = "Storage account $storageAccountName not found "
    $errorMessage += "in resource group $storageAccountResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}

# Get virtual network reference, and throw error if it doesn't exist
$virtualNetwork = Get-AzVirtualNetwork `
        -ResourceGroupName $virtualNetworkResourceGroupName `
        -Name $virtualNetworkName `
        -ErrorAction SilentlyContinue

if ($null -eq $virtualNetwork) {
    $errorMessage = "Virtual network $virtualNetworkName not found "
    $errorMessage += "in resource group $virtualNetworkResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}

# Get reference to virtual network subnet, and throw error if it doesn't exist
$subnet = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq $subnetName }

if ($null -eq $subnet) {
    Write-Error `
            -Message "Subnet $subnetName not found in virtual network $virtualNetworkName." `
            -ErrorAction Stop
}
```

Para criar um ponto final privado, deve criar uma ligação de serviço de ligação privada à conta de armazenamento. A ligação de serviço de ligação privada é uma entrada para a criação do ponto final privado. 

```PowerShell
# Disable private endpoint network policies
$subnet.PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork | Out-Null

# Create a private link service connection to the storage account.
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name "$storageAccountName-Connection" `
        -PrivateLinkServiceId $storageAccount.Id `
        -GroupId "file"

# Create a new private endpoint.
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name "$storageAccountName-PrivateEndpoint" `
        -Location $virtualNetwork.Location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -ErrorAction Stop
```

A criação de uma zona Privada de DNS Azure `storageaccount.file.core.windows.net` permite o nome original da conta de armazenamento, como resolver o IP privado dentro da rede virtual. Embora opcional na perspetiva da criação de um ponto final privado, é explicitamente necessário para a montagem da partilha de ficheiros Azure utilizando um diretor de utilizador ad ou aceder através da API REST.  

```PowerShell
# Get the desired storage account suffix (core.windows.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
$storageAccountSuffix = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

# For public cloud, this will generate the following DNS suffix:
# privatelink.file.core.windows.net.
$dnsZoneName = "privatelink.file.$storageAccountSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
$dnsZone = Get-AzPrivateDnsZone | `
    Where-Object { $_.Name -eq $dnsZoneName } | `
    Where-Object {
        $privateDnsLink = Get-AzPrivateDnsVirtualNetworkLink `
                -ResourceGroupName $_.ResourceGroupName `
                -ZoneName $_.Name `
                -ErrorAction SilentlyContinue
        
        $privateDnsLink.VirtualNetworkId -eq $virtualNetwork.Id
    }

if ($null -eq $dnsZone) {
    # No matching DNS zone attached to virtual network, so create new one.
    $dnsZone = New-AzPrivateDnsZone `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -Name $dnsZoneName `
            -ErrorAction Stop

    $privateDnsLink = New-AzPrivateDnsVirtualNetworkLink `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -ZoneName $dnsZoneName `
            -Name "$virtualNetworkName-DnsLink" `
            -VirtualNetworkId $virtualNetwork.Id `
            -ErrorAction Stop
}
```

Agora que tem uma referência à zona privada de DNS, tem de criar um registo A para a sua conta de armazenamento.

```PowerShell
$privateEndpointIP = $privateEndpoint | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object @{ 
        Name = "NetworkInterfaces"; 
        Expression = { Get-AzNetworkInterface -ResourceId $_.Id } 
    } | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty IpConfigurations | `
    Select-Object -ExpandProperty PrivateIpAddress

$privateDnsRecordConfig = New-AzPrivateDnsRecordConfig `
        -IPv4Address $privateEndpointIP

New-AzPrivateDnsRecordSet `
        -ResourceGroupName $virtualNetworkResourceGroupName `
        -Name $storageAccountName `
        -RecordType A `
        -ZoneName $dnsZoneName `
        -Ttl 600 `
        -PrivateDnsRecords $privateDnsRecordConfig `
        -ErrorAction Stop | `
    Out-Null
```

Se tiver uma máquina virtual dentro da sua rede virtual, ou tiver configurado o encaminhamento de DNS como descrito [aqui,](storage-files-networking-dns.md)pode testar que o seu ponto final privado foi configurado corretamente com os seguintes comandos:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.File) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Se tudo tiver funcionado com sucesso, deve `192.168.0.5` ver a seguinte saída, onde está o endereço IP privado do ponto final privado na sua rede virtual:

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para criar um ponto final privado para a sua conta de armazenamento, primeiro precisa de obter uma referência à sua conta de armazenamento e à subnet de rede virtual à qual pretende adicionar o ponto final privado. Substitua, `<vnet-resource-group-name>` `<storage-account-resource-group-name>` `<storage-account-name>` `<vnet-name>`, `<vnet-subnet-name>` , e abaixo:

```bash
storageAccountResourceGroupName="<storage-account-resource-group-name>"
storageAccountName="<storage-account-name>"
virtualNetworkResourceGroupName="<vnet-resource-group-name>"
virtualNetworkName="<vnet-name>"
subnetName="<vnet-subnet-name>"

# Get storage account ID 
storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

# Get virtual network ID
virtualNetwork=$(az network vnet show \
        --resource-group $virtualNetworkResourceGroupName \
        --name $virtualNetworkName \
        --query "id" | \
    tr -d '"')

# Get subnet ID
subnet=$(az network vnet subnet show \
        --resource-group $virtualNetworkResourceGroupName \
        --vnet-name $virtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

Para criar um ponto final privado, primeiro deve garantir que a política de rede de pontos finais privados da subnet está definida para desativar. Então pode criar um ponto `az network private-endpoint create` final privado com o comando

```bash
# Disable private endpoint network policies
az network vnet subnet update \
        --ids $subnet \
        --disable-private-endpoint-network-policies \
        --output none

# Get virtual network location
region=$(az network vnet show \
        --ids $virtualNetwork \
        --query "location" | \
    tr -d '"')

# Create a private endpoint
privateEndpoint=$(az network private-endpoint create \
        --resource-group $virtualNetworkResourceGroupName \
        --name "$storageAccountName-PrivateEndpoint" \
        --location $region \
        --subnet $subnet \
        --private-connection-resource-id $storageAccount \
        --group-ids "file" \
        --connection-name "$storageAccountName-Connection" \
        --query "id" | \
    tr -d '"')
```

A criação de uma zona Privada de DNS Azure `storageaccount.file.core.windows.net` permite o nome original da conta de armazenamento, como resolver o IP privado dentro da rede virtual. Embora opcional na perspetiva da criação de um ponto final privado, é explicitamente necessário para a montagem da partilha de ficheiros Azure utilizando um diretor de utilizador ad ou aceder através da API REST.  

```bash
# Get the desired storage account suffix (core.windows.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
storageAccountSuffix=$(az cloud show \
        --query "suffixes.storageEndpoint" | \
    tr -d '"')

# For public cloud, this will generate the following DNS suffix:
# privatelink.file.core.windows.net.
dnsZoneName="privatelink.file.$storageAccountSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
possibleDnsZones=$(az network private-dns zone list \
        --query "[?name == '$dnsZoneName'].id" \
        --output tsv)

for possibleDnsZone in $possibleDnsZones
do
    possibleResourceGroupName=$(az resource show \
            --ids $possibleDnsZone \
            --query "resourceGroup" | \
        tr -d '"')
    
    link=$(az network private-dns link vnet list \
            --resource-group $possibleResourceGroupName \
            --zone-name $dnsZoneName \
            --query "[?virtualNetwork.id == '$virtualNetwork'].id" \
            --output tsv)
    
    if [ -z $link ]
    then
        1 > /dev/null
    else 
        dnsZoneResourceGroup=$possibleResourceGroupName
        dnsZone=$possibleDnsZone
        break
    fi  
done

if [ -z $dnsZone ]
then
    # No matching DNS zone attached to virtual network, so create a new one
    dnsZone=$(az network private-dns zone create \
            --resource-group $virtualNetworkResourceGroupName \
            --name $dnsZoneName \
            --query "id" | \
        tr -d '"')
    
    az network private-dns link vnet create \
            --resource-group $resourceGroupName \
            --zone-name $zoneName \
            --name "$virtualNetworkName-DnsLink" \
            --virtual-network $virtualNetwork \
            --registration-enabled false \
            --output none
fi
```

Agora que tem uma referência à zona privada de DNS, tem de criar um registo A para a sua conta de armazenamento.

```bash
privateEndpointNIC=$(az network private-endpoint show \
        --ids $privateEndpoint \
        --query "networkInterfaces[0].id" | \
    tr -d '"')

privateEndpointIP=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[0].privateIpAddress" | \
    tr -d '"')

az network private-dns record-set a create \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --name $storageAccountName \
        --output none

az network private-dns record-set a add-record \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --record-set-name $storageAccountName \
        --ipv4-address $privateEndpointIP \
        --output none
```

Se tiver uma máquina virtual dentro da sua rede virtual, ou tiver configurado o encaminhamento de DNS como descrito [aqui,](storage-files-networking-dns.md)pode testar que o seu ponto final privado foi configurado corretamente com os seguintes comandos:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.File" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Se tudo tiver funcionado com sucesso, deve `192.168.0.5` ver a seguinte saída, onde está o endereço IP privado do ponto final privado na sua rede virtual:

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

## <a name="restrict-access-to-the-public-endpoint"></a>Restringir o acesso ao ponto final público
Pode restringir o acesso ao ponto final do público utilizando as definições de firewall da conta de armazenamento. Em geral, a maioria das políticas de firewall para uma conta de armazenamento restringirá o acesso em rede a uma ou mais redes virtuais. Existem duas abordagens para restringir o acesso a uma conta de armazenamento a uma rede virtual:

- [Crie um ou mais pontos finais privados para a conta](#create-a-private-endpoint) de armazenamento e restrinja todo o acesso ao ponto final público. Isto garante que apenas o tráfego originário das redes virtuais desejadas pode aceder às ações de ficheiro sintetizadores do Azure dentro da conta de armazenamento.
- Restringir o ponto final público a uma ou mais redes virtuais. Isto funciona utilizando uma capacidade da rede virtual chamada *pontos finais*de serviço . Quando restringe o tráfego a uma conta de armazenamento através de um ponto final de serviço, ainda está a aceder à conta de armazenamento através do endereço IP público.

### <a name="restrict-all-access-to-the-public-endpoint"></a>Restringir todo o acesso ao ponto final do público
Quando todo o acesso ao ponto final público é restrito, a conta de armazenamento ainda pode ser acedida através dos pontos finais privados. Caso contrário, os pedidos válidos ao ponto final da conta de armazenamento serão rejeitados. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navegue na conta de armazenamento para a qual deseja restringir todo o acesso ao ponto final público. Na tabela de conteúdos para a conta de armazenamento, selecione **Firewalls e redes virtuais**.

Na parte superior da página, selecione o botão de rádio **das redes Selecionadas.** Isto irá desocultar uma série de configurações para controlar a restrição do ponto final público. Check **Allow trust serviços microsoft para aceder a esta conta de serviço** para permitir que serviços de primeira parte confiáveis da Microsoft, como O Sincronizado de Ficheiros Azure, acedam à conta de armazenamento.

![Screenshot das Firewalls e lâmina de redes virtuais com os limites apropriados no lugar](media/storage-files-networking-endpoints/restrict-public-endpoint-0.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
O seguinte comando PowerShell negará todo o tráfego para o ponto final da conta de armazenamento. Note que este `-Bypass` comando tem `AzureServices`o parâmetro definido para . Isto permitirá que serviços de primeira parte confiáveis, como o Azure File Sync, acedam à conta de armazenamento através do ponto final público.

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
O seguinte comando CLI negará todo o tráfego para o ponto final da conta de armazenamento. Note que este `-bypass` comando tem `AzureServices`o parâmetro definido para . Isto permitirá que serviços de primeira parte confiáveis, como o Azure File Sync, acedam à conta de armazenamento através do ponto final público.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```
---

### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Restringir o acesso ao ponto final público a redes virtuais específicas
Ao restringir a conta de armazenamento a redes virtuais específicas, está a permitir pedidos ao ponto final público a partir das redes virtuais especificadas. Isto funciona utilizando uma capacidade da rede virtual chamada *pontos finais*de serviço . Isto pode ser usado com ou sem pontos finais privados.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navegue na conta de armazenamento para a qual deseja restringir o ponto final público a redes virtuais específicas. Na tabela de conteúdos para a conta de armazenamento, selecione **Firewalls e redes virtuais**. 

Na parte superior da página, selecione o botão de rádio **das redes Selecionadas.** Isto irá desocultar uma série de configurações para controlar a restrição do ponto final público. Clique **+Adicione a rede virtual existente** para selecionar a rede virtual específica que deve ser autorizada a aceder à conta de armazenamento através do ponto final público. Isto exigirá a seleção de uma rede virtual e uma sub-rede para essa rede virtual. 

Check **Allow trust serviços microsoft para aceder a esta conta de serviço** para permitir que serviços de primeira parte confiáveis da Microsoft, como O Sincronizado de Ficheiros Azure, acedam à conta de armazenamento.

![Screenshot das Firewalls e da lâmina de redes virtuais com uma rede virtual específica permitida a aceder à conta de armazenamento através do ponto final público](media/storage-files-networking-endpoints/restrict-public-endpoint-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para restringir o acesso ao ponto final da conta de armazenamento a redes virtuais específicas utilizando pontos finais de serviço, precisamos primeiro de recolher informações sobre a conta de armazenamento e a rede virtual. `<storage-account-resource-group>`Preencha, `<storage-account-name>` `<vnet-resource-group-name>`, `<vnet-name>`e `<subnet-name>` recolha desta informação.

```PowerShell
$storageAccountResourceGroupName = "<storage-account-resource-group>"
$storageAccountName = "<storage-account-name>"
$restrictToVirtualNetworkResourceGroupName = "<vnet-resource-group-name>"
$restrictToVirtualNetworkName = "<vnet-name>"
$subnetName = "<subnet-name>"

$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name $storageAccountName `
        -ErrorAction Stop

$virtualNetwork = Get-AzVirtualNetwork `
        -ResourceGroupName $restrictToVirtualNetworkResourceGroupName `
        -Name $restrictToVirtualNetworkName `
        -ErrorAction Stop

$subnet = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq $subnetName }

if ($null -eq $subnet) {
    Write-Error `
            -Message "Subnet $subnetName not found in virtual network $restrictToVirtualNetworkName." `
            -ErrorAction Stop
}
```

Para que o tráfego da rede virtual seja autorizado pelo tecido da rede Azure a chegar ao ponto `Microsoft.Storage` final da conta de armazenamento, a subnet da rede virtual deve ter o ponto final do serviço exposto. Os seguintes comandos PowerShell `Microsoft.Storage` adicionarão o ponto final de serviço à sub-rede se ainda não estiver lá.

```PowerShell
$serviceEndpoints = $subnet | `
    Select-Object -ExpandProperty ServiceEndpoints | `
    Select-Object -ExpandProperty Service

if ($serviceEndpoints -notcontains "Microsoft.Storage") {
    if ($null -eq $serviceEndpoints) {
        $serviceEndpoints = @("Microsoft.Storage")
    } elseif ($serviceEndpoints -is [string]) {
        $serviceEndpoints = @($serviceEndpoints, "Microsoft.Storage")
    } else {
        $serviceEndpoints += "Microsoft.Storage"
    }

    $virtualNetwork = $virtualNetwork | Set-AzVirtualNetworkSubnetConfig `
            -Name $subnetName `
            -AddressPrefix $subnet.AddressPrefix `
            -ServiceEndpoint $serviceEndpoints `
            -WarningAction SilentlyContinue `
            -ErrorAction Stop | `
        Set-AzVirtualNetwork `
            -ErrorAction Stop
}
```

O passo final para restringir o tráfego à conta de armazenamento é criar uma regra de rede e adicionar ao conjunto de regras de rede da conta de armazenamento.

```PowerShell
$networkRule = $storageAccount | Add-AzStorageAccountNetworkRule `
    -VirtualNetworkResourceId $subnet.Id `
    -ErrorAction Stop

$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -VirtualNetworkRule $networkRule `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para restringir o acesso ao ponto final da conta de armazenamento a redes virtuais específicas utilizando pontos finais de serviço, precisamos primeiro de recolher informações sobre a conta de armazenamento e a rede virtual. `<storage-account-resource-group>`Preencha, `<storage-account-name>` `<vnet-resource-group-name>`, `<vnet-name>`e `<subnet-name>` recolha desta informação.

```bash
storageAccountResourceGroupName="<storage-account-resource-group>"
storageAccountName="<storage-account-name>"
restrictToVirtualNetworkResourceGroupName="<vnet-resource-group-name>"
restrictToVirtualNetworkName="<vnet-name>"
subnetName="<subnet-name>"

storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

virtualNetwork=$(az network vnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --name $restrictToVirtualNetworkName \
        --query "id" | \
    tr -d '"')

subnet=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

Para que o tráfego da rede virtual seja autorizado pelo tecido da rede Azure a chegar ao ponto `Microsoft.Storage` final da conta de armazenamento, a subnet da rede virtual deve ter o ponto final do serviço exposto. Os seguintes comandos CLI `Microsoft.Storage` adicionarão o ponto final de serviço à sub-rede se ainda não estiver lá.

```bash
serviceEndpoints=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "serviceEndpoints[].service" \
        --output tsv)

foundStorageServiceEndpoint=false
for serviceEndpoint in $serviceEndpoints
do
    if [ $serviceEndpoint = "Microsoft.Storage" ]
    then
        foundStorageServiceEndpoint=true
    fi
done

if [ $foundStorageServiceEndpoint = false ] 
then
    serviceEndpointList=""

    for serviceEndpoint in $serviceEndpoints
    do
        serviceEndpointList+=$serviceEndpoint
        serviceEndpointList+=" "
    done
    
    serviceEndpointList+="Microsoft.Storage"

    az network vnet subnet update \
            --ids $subnet \
            --service-endpoints $serviceEndpointList \
            --output none
fi
```

O passo final para restringir o tráfego à conta de armazenamento é criar uma regra de rede e adicionar ao conjunto de regras de rede da conta de armazenamento.

```bash
az storage account network-rule add \
        --resource-group $storageAccountResourceGroupName \
        --account-name $storageAccountName \
        --subnet $subnet \
        --output none

az storage account update \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --bypass "AzureServices" \
        --default-action "Deny" \
        --output none
```

---

## <a name="see-also"></a>Consulte também
- [Considerações de networking de Ficheiros Azure](storage-files-networking-overview.md)
- [Configurar o reencaminhamento de DNS para Ficheiros do Azure](storage-files-networking-dns.md)
- [Configurar VPN S2S para Ficheiros Azure](storage-files-configure-s2s-vpn.md)