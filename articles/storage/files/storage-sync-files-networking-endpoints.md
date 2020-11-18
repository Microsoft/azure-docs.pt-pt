---
title: Configurar pontos finais da rede Azure File Sync Microsoft Docs
description: Saiba como configurar os pontos finais da rede Azure File Sync.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 5/11/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 02d9e65f5422b7b12900d051f01c1d6f55e8685b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844681"
---
# <a name="configuring-azure-file-sync-network-endpoints"></a>Configurar pontos finais de rede do Azure File Sync
A Azure Files e Azure File Sync fornecem dois tipos principais de pontos finais para aceder a ações de ficheiros Azure: 
- Pontos finais públicos, que têm um endereço IP público e podem ser acedidos a partir de qualquer parte do mundo.
- Pontos finais privados, que existem dentro de uma rede virtual e têm um endereço IP privado a partir do espaço de endereço dessa rede virtual.

Tanto para os Ficheiros Azure como para o Azure File Sync, os objetos de gestão Azure, a conta de armazenamento e o Serviço de Sincronização de Armazenamento, respectivamente, controlam os pontos finais públicos e privados. A conta de armazenamento é uma construção de gestão que representa um conjunto partilhado de armazenamento no qual você pode implementar várias partilhas de arquivos, bem como outros recursos de armazenamento, tais como recipientes blob ou filas. O Storage Sync Service é uma construção de gestão que representa servidores registados, que são servidores de ficheiros Windows com uma relação de confiança estabelecida com o Azure File Sync, e grupos de sincronização, que definem a topologia da relação de sincronização. 

Este artigo centra-se em como configurar os pontos finais de rede tanto para os Ficheiros Azure como para o Azure File Sync. Para saber mais sobre como configurar os pontos finais de rede para aceder diretamente às ações de ficheiros Azure, em vez de caching no local com o Azure File Sync, consulte [os pontos finais da rede de Ficheiros Azure.](storage-files-networking-endpoints.md)

Recomendamos que se leia [considerações de networking do Azure File Sync](storage-sync-files-networking-overview.md) antes de ler este como orientar.

## <a name="prerequisites"></a>Pré-requisitos 
Este artigo assume que:
- Tem uma assinatura Azure. Se ainda não tem uma subscrição, então crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Já criou uma partilha de ficheiros Azure numa conta de armazenamento à qual gostaria de ligar a partir do local. Para aprender a criar uma partilha de ficheiros Azure, consulte [Criar uma partilha de ficheiros Azure](storage-how-to-create-file-share.md).
- Já criou um Serviço de Sincronização de Armazenamento e registou o seu servidor de ficheiros Windows com ele. Para aprender a implementar o Azure File Sync, consulte [o Implementing Azure File Sync](storage-sync-files-deployment-guide.md).

Além disso,
- Se pretender utilizar o Azure PowerShell, [instale a versão mais recente](/powershell/azure/install-az-ps).
- Se pretender utilizar o Azure CLI, [instale a versão mais recente](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-the-private-endpoints"></a>Criar os pontos finais privados
Ao criar um ponto final privado para um recurso Azure, são utilizados os seguintes recursos:

- **Um ponto final privado**: Um recurso Azure que representa o ponto final privado para a conta de armazenamento ou o Serviço de Sincronização de Armazenamento. Pode pensar nisto como um recurso que liga o seu recurso Azure e uma interface de rede.
- **Uma interface de rede (NIC)**: A interface de rede que mantém um endereço IP privado dentro da rede/sub-rede virtual especificada. Este é exatamente o mesmo recurso que é implantado quando se implanta uma máquina virtual, no entanto, em vez de ser atribuído a um VM, é propriedade do ponto final privado.
- **Uma zona privada de DNS**: Se nunca implementou um ponto final privado para esta rede virtual antes, uma nova zona privada de DNS será implantada para a sua rede virtual. Será também criado um registo de DNS Para o recurso Azure nesta zona de DNS. Se já implementou um ponto final privado nesta rede virtual, será adicionado um novo recorde A para o recurso Azure à zona de DNS existente. A implantação de uma zona de DNS é opcional, no entanto altamente recomendada para simplificar a gestão de DNS necessária.

> [!Note]  
> Este artigo utiliza os sufixos DNS para as regiões Públicas do Azure, `core.windows.net` para contas de armazenamento e para `afs.azure.net` Serviços de Sincronização de Armazenamento. Este comentário também se aplica às nuvens soberanas de Azure, como a nuvem do Governo dos EUA Azure - basta substituir os sufixos apropriados para o seu ambiente.

### <a name="create-the-storage-account-private-endpoint"></a>Criar o ponto final privado da conta de armazenamento
# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

Se tiver uma máquina virtual dentro da sua rede virtual, ou tiver configurado o reencaminhamento de DNS como descrito no [reencaminhamento de DNS para Ficheiros Azure,](storage-files-networking-dns.md)pode testar que o seu ponto final privado foi configurado corretamente executando os seguintes comandos da PowerShell, da linha de comando ou do terminal (funciona para Windows, Linux ou macOS). Deve substituir `<storage-account-name>` pelo nome da conta de armazenamento adequada:

```
nslookup <storage-account-name>.file.core.windows.net
```

Se tudo tiver funcionado com sucesso, deverá ver a seguinte saída, onde `192.168.0.5` está o endereço IP privado do ponto final privado na sua rede virtual (saída mostrada para Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

Se tiver uma máquina virtual dentro da sua rede virtual, ou tiver configurado o reencaminhamento de DNS como descrito na [configuração do encaminhamento de DNS para ficheiros Azure,](storage-files-networking-dns.md)pode testar que o seu ponto final privado foi configurado corretamente com os seguintes comandos:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Se tudo funcionou com sucesso, deverá ver a seguinte saída, onde `192.168.0.5` está o endereço IP privado do ponto final privado na sua rede virtual:

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
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]

Se tiver uma máquina virtual dentro da sua rede virtual, ou tiver configurado o reencaminhamento de DNS como descrito na [configuração do encaminhamento de DNS para ficheiros Azure,](storage-files-networking-dns.md)pode testar que o seu ponto final privado foi configurado corretamente com os seguintes comandos:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Se tudo funcionou com sucesso, deverá ver a seguinte saída, onde `192.168.0.5` está o endereço IP privado do ponto final privado na sua rede virtual:

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

### <a name="create-the-storage-sync-private-endpoint"></a>Criar o ponto final privado de sincronização de armazenamento
> [!Important]  
> Para utilizar pontos finais privados no recurso Storage Sync Service, tem de utilizar a versão 10.1 ou superior do agente Azure File Sync. As versões do agente antes do 10.1 não suportam pontos finais privados no Serviço de Sincronização de Armazenamento. Todas as versões anteriores do agente suportam pontos finais privados no recurso da conta de armazenamento.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navegue para o **Private Link** digitando Private *Link* na barra de pesquisa no topo do portal Azure. Na tabela de conteúdos para o Private Link Center, selecione **Privatepoints**, e depois **+ Adicionar** para criar um novo ponto final privado.

[![Uma imagem do centro de ligação privada](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-0.png)](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-0.png#lightbox)

O assistente resultante tem várias páginas para completar.

Na lâmina **Basics,** selecione o grupo de recursos, nome e região pretendidos para o seu ponto final privado. Estes podem ser o que quiser, eles não têm de corresponder ao Serviço de Sincronização de Armazenamento de forma alguma, embora tenha de criar o ponto final privado na mesma região que a rede virtual em que pretende criar o ponto final privado.

![Uma imagem da secção Basics da secção de pontos finais privados](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-1.png)

Na lâmina **de recurso,** selecione o botão de rádio **para Ligar a um recurso Azure no meu diretório**. Sob o **tipo de recurso,** selecione **Microsoft.StorageSync/storageSyncServices** para o tipo de recurso. 

A lâmina **de configuração** permite-lhe selecionar a rede virtual específica e a sub-rede a que gostaria de adicionar o seu ponto final privado. Selecione a mesma rede virtual que usou para a conta de armazenamento acima. A lâmina de configuração também contém as informações para criar/atualizar a zona privada de DNS.

Clique **em Rever + criar** para criar o ponto final privado.

Pode testar que o seu ponto final privado foi configurado corretamente executando os seguintes comandos a partir de PowerShell. 

```powershell
$privateEndpointResourceGroupName = "<your-private-endpoint-resource-group>"
$privateEndpointName = "<your-private-endpoint-name>"

Get-AzPrivateEndpoint `
        -ResourceGroupName $privateEndpointResourceGroupName `
        -Name $privateEndpointName `
        -ErrorAction Stop | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty Id | `
    ForEach-Object { Get-AzNetworkInterface -ResourceId $_ } | `
    Select-Object -ExpandProperty IpConfigurations | `
    Select-Object -ExpandProperty PrivateLinkConnectionProperties | `
    Select-Object -ExpandProperty Fqdns | `
    ForEach-Object { Resolve-DnsName -Name $_ } | `
    Format-List
```

Se tudo tiver funcionado corretamente, deverá ver a seguinte saída `192.168.1.4` `192.168.1.5` onde, `192.168.1.6` e são os `192.168.1.7` endereços IP privados atribuídos ao ponto final privado:

```Output
Name     : mysssmanagement.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : mysssmanagement.westus2.privatelink.afs.azure.net


Name       : mysssmanagement.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.4

Name     : myssssyncp.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : myssssyncp.westus2.privatelink.afs.azure.net


Name       : myssssyncp.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.5

Name     : myssssyncs.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : myssssyncs.westus2.privatelink.afs.azure.net


Name       : myssssyncs.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.6

Name     : mysssmonitoring.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : mysssmonitoring.westus2.privatelink.afs.azure.net


Name       : mysssmonitoring.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.7

```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para criar um ponto final privado para o seu Serviço de Sincronização de Armazenamento, primeiro terá de obter uma referência ao seu Serviço de Sincronização de Armazenamento. Lembre-se de substituir `<storage-sync-service-resource-group>` e `<storage-sync-service>` pelos valores corretos para o seu ambiente. Os seguintes comandos PowerShell assumem que você está usando já povoou as informações de rede virtual de cima. 

```powershell
$storageSyncServiceResourceGroupName = "<storage-sync-service-resource-group>"
$storageSyncServiceName = "<storage-sync-service>"

$storageSyncService = Get-AzStorageSyncService `
        -ResourceGroupName $storageSyncServiceResourceGroupName `
        -Name $storageSyncServiceName `
        -ErrorAction SilentlyContinue

if ($null -eq $storageSyncService) {
    $errorMessage = "Storage Sync Service $storageSyncServiceName not found "
    $errorMessage += "in resource group $storageSyncServiceResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}
```

Para criar um ponto final privado, tem de criar uma ligação de serviço de ligação privada ao Serviço de Sincronização de Armazenamento. A ligação de ligação privada é uma entrada para a criação do ponto final privado.

```PowerShell 
# Disable private endpoint network policies
$subnet.PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork = $virtualNetwork | `
    Set-AzVirtualNetwork -ErrorAction Stop

# Create a private link service connection to the storage account.
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name "$storageSyncServiceName-Connection" `
        -PrivateLinkServiceId $storageSyncService.ResourceId `
        -GroupId "Afs" `
        -ErrorAction Stop

# Create a new private endpoint.
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $storageSyncServiceResourceGroupName `
        -Name "$storageSyncServiceName-PrivateEndpoint" `
        -Location $virtualNetwork.Location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -ErrorAction Stop
```

A criação de uma zona de DNS privada Azure permite que os nomes dos anfitriões do Serviço de Sincronização de Armazenamento, tais `mysssmanagement.westus2.afs.azure.net` como, resolvam os IPs privados corretos para o Serviço de Sincronização de Armazenamento dentro da rede virtual. Embora opcional do ponto de vista da criação de um ponto final privado, é expressamente necessário que o agente Azure File Sync aceda ao Serviço de Sincronização de Armazenamento. 

```powershell
# Get the desired Storage Sync Service suffix (afs.azure.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
$azureEnvironment = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

switch($azureEnvironment) {
    "AzureCloud" {
        $storageSyncSuffix = "afs.azure.net"
    }

    "AzureUSGovernment" {
        $storageSyncSuffix = "afs.azure.us"
    }
    
    default {
        Write-Error 
                -Message "The Azure environment $_ is not currently supported by Azure File Sync." `
                -ErrorAction Stop
    }
}

# For public cloud, this will generate the following DNS suffix:
# privatelink.afs.azure.net
$dnsZoneName = "privatelink.$storageSyncSuffix"

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
Agora que tem uma referência à zona privada de DNS, tem de criar um registo A para o seu Serviço de Sincronização de Armazenamento.

```PowerShell 
$privateEndpointIpFqdnMappings = $privateEndpoint | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty Id | `
    ForEach-Object { Get-AzNetworkInterface -ResourceId $_ } | `
    Select-Object -ExpandProperty IpConfigurations | `
    ForEach-Object { 
        $privateIpAddress = $_.PrivateIpAddress; 
        $_ | `
            Select-Object -ExpandProperty PrivateLinkConnectionProperties | `
            Select-Object -ExpandProperty Fqdns | `
            Select-Object `
                @{ 
                    Name = "PrivateIpAddress"; 
                    Expression = { $privateIpAddress } 
                }, `
                @{ 
                    Name = "FQDN"; 
                    Expression = { $_ } 
                } 
    }

foreach($ipFqdn in $privateEndpointIpFqdnMappings) {
    $privateDnsRecordConfig = New-AzPrivateDnsRecordConfig `
        -IPv4Address $ipFqdn.PrivateIpAddress
    
    $dnsEntry = $ipFqdn.FQDN.Substring(0, 
        $ipFqdn.FQDN.IndexOf(".", $ipFqdn.FQDN.IndexOf(".") + 1))

    New-AzPrivateDnsRecordSet `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -Name $dnsEntry `
            -RecordType A `
            -ZoneName $dnsZoneName `
            -Ttl 600 `
            -PrivateDnsRecords $privateDnsRecordConfig `
            -ErrorAction Stop | `
        Out-Null
}
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para criar um ponto final privado para o seu Serviço de Sincronização de Armazenamento, primeiro terá de obter uma referência ao seu Serviço de Sincronização de Armazenamento. Lembre-se de substituir `<storage-sync-service-resource-group>` e `<storage-sync-service>` pelos valores corretos para o seu ambiente. Os seguintes comandos da CLI assumem que você está usando já povoou as informações de rede virtual de cima. 

```bash
storageSyncServiceResourceGroupName="<storage-sync-service-resource-group>"
storageSyncServiceName="<storage-sync-service>"

storageSyncService=$(az resource show \
        --resource-group $storageSyncServiceResourceGroupName \
        --name $storageSyncServiceName \
        --resource-type "Microsoft.StorageSync/storageSyncServices" \
        --query "id" | \
    tr -d '"')

storageSyncServiceRegion=$(az resource show \
        --resource-group $storageSyncServiceResourceGroupName \
        --name $storageSyncServiceName \
        --resource-type "Microsoft.StorageSync/storageSyncServices" \
        --query "location" | \
    tr -d '"')
```

Para criar um ponto final privado, tem primeiro de garantir que a política de rede de pontos finais privada da sub-rede está definida para desativar. Então pode criar um ponto final privado com o `az network private-endpoint create` comando.

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
        --resource-group $storageSyncServiceResourceGroupName \
        --name "$storageSyncServiceName-PrivateEndpoint" \
        --location $region \
        --subnet $subnet \
        --private-connection-resource-id $storageSyncService \
        --group-id "Afs" \
        --connection-name "$storageSyncServiceName-Connection" \
        --query "id" | \
    tr -d '"')
```

A criação de uma zona de DNS privada Azure permite que os nomes dos anfitriões do Serviço de Sincronização de Armazenamento, tais `mysssmanagement.westus2.afs.azure.net` como, resolvam os IPs privados corretos para o Serviço de Sincronização de Armazenamento dentro da rede virtual. Embora opcional do ponto de vista da criação de um ponto final privado, é expressamente necessário que o agente Azure File Sync aceda ao Serviço de Sincronização de Armazenamento. 

```bash
# Get the desired storage account suffix (afs.azure.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
azureEnvironment=$(az cloud show \
        --query "name" |
    tr -d '"')

storageSyncSuffix=""
if [ $azureEnvironment == "AzureCloud" ]
then
    storageSyncSuffix="afs.azure.net"
elif [ $azureEnvironment == "AzureUSGovernment" ]
then
    storageSyncSuffix="afs.azure.us"
else
    echo "Unsupported Azure environment $azureEnvironment."
fi

# For public cloud, this will generate the following DNS suffix:
# privatelinke.afs.azure.net.
dnsZoneName="privatelink.$storageSyncSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
possibleDnsZones=""
possibleDnsZones=$(az network private-dns zone list \
        --query "[?name == '$dnsZoneName'].id" \
        --output tsv)

dnsZone=""
possibleDnsZone=""
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
        echo "1" > /dev/null
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
            --resource-group $virtualNetworkResourceGroupName \
            --zone-name $dnsZoneName \
            --name "$virtualNetworkName-DnsLink" \
            --virtual-network $virtualNetwork \
            --registration-enabled false \
            --output none
    
    dnsZoneResourceGroup=$virtualNetworkResourceGroupName
fi
```

Agora que tem uma referência à zona privada de DNS, tem de criar um registo A para o seu Serviço de Sincronização de Armazenamento.

```bash
privateEndpointNIC=$(az network private-endpoint show \
        --ids $privateEndpoint \
        --query "networkInterfaces[0].id" | \
    tr -d '"')

privateIpAddresses=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[].privateIpAddress" \
        --output tsv) 

hostNames=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[].privateLinkConnectionProperties.fqdns[]" \
        --output tsv)

i=0
for privateIpAddress in $privateIpAddresses
do
    j=0
    targetHostName=""
    for hostName in $hostNames
    do
        if [ $i == $j ]
        then
            targetHostName=$hostName
            break
        fi

        j=$(expr $j + 1)
    done

    endpointName=$(echo $targetHostName | \
        cut -c1-$(expr $(expr index $targetHostName ".") - 1))

    az network private-dns record-set a create \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --name "$endpointName.$storageSyncServiceRegion" \
        --output none
    
    az network private-dns record-set a add-record \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --record-set-name "$endpointName.$storageSyncServiceRegion" \
        --ipv4-address $privateIpAddress \
        --output none

    i=$(expr $i + 1)
done
```
---

## <a name="restrict-access-to-the-public-endpoints"></a>Restringir o acesso aos pontos finais públicos
Pode restringir o acesso aos pontos finais públicos tanto da conta de armazenamento como dos Serviços de Sincronização de Armazenamento. Restringir o acesso ao ponto final público proporciona segurança adicional, garantindo que os pacotes de rede só são aceites a partir de locais aprovados. 

### <a name="restrict-access-to-the-storage-account-public-endpoint"></a>Restringir o acesso à conta de armazenamento ponto final público
A restrição de acesso ao ponto final público é feita utilizando as definições de firewall da conta de armazenamento. Em geral, a maioria das políticas de firewall para uma conta de armazenamento restringirá o acesso em rede a uma ou mais redes virtuais. Existem duas abordagens para restringir o acesso a uma conta de armazenamento a uma rede virtual:

- [Crie um ou mais pontos finais privados para a conta de armazenamento](#create-the-storage-account-private-endpoint) e desative o acesso ao ponto final público. Isto garante que apenas o tráfego originário das redes virtuais pretendidas pode aceder às ações de ficheiros Azure dentro da conta de armazenamento.
- Restringir o ponto final público a uma ou mais redes virtuais. Isto funciona utilizando uma capacidade da rede virtual chamada *pontos finais de serviço*. Quando restringe o tráfego a uma conta de armazenamento através de um ponto final de serviço, ainda está a aceder à conta de armazenamento através do endereço IP público.

#### <a name="disable-access-to-the-storage-account-public-endpoint"></a>Desativar o acesso à conta de armazenamento no ponto final público
Quando o acesso ao ponto final público é desativado, a conta de armazenamento ainda pode ser acedida através dos seus pontos finais privados. Caso contrário, os pedidos válidos para o ponto final público da conta de armazenamento serão rejeitados. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-storage-account-public-endpoint-to-specific-virtual-networks"></a>Restringir o acesso à conta de armazenamento ponto final público a redes virtuais específicas
Quando restringe a conta de armazenamento a redes virtuais específicas, está a permitir pedidos para o ponto final público a partir das redes virtuais especificadas. Isto funciona utilizando uma capacidade da rede virtual chamada *pontos finais de serviço*. Isto pode ser usado com ou sem pontos finais privados.

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

### <a name="disable-access-to-the-storage-sync-service-public-endpoint"></a>Desativar o acesso ao ponto final público do Serviço de Sincronização de Armazenamento
O Azure File Sync permite-lhe restringir o acesso a redes virtuais específicas apenas através de pontos finais privados; O Azure File Sync não suporta pontos finais de serviço para restringir o acesso ao ponto final público a redes virtuais específicas. Isto significa que os dois estados para o ponto final público do Serviço de Sincronização de Armazenamento estão ativados e desativados.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Isto não é possível através do portal Azure. Por favor, selecione as instruções do separador Azure PowerShell ou Azure CLI para obter instruções sobre como desativar o ponto de final público do Serviço de Sincronização de Armazenamento. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para desativar o acesso ao ponto final público do Serviço de Sincronização de Armazenamento, definiremos a `incomingTrafficPolicy` propriedade no Serviço de Sincronização de Armazenamento para `AllowVirtualNetworksOnly` . Se quiser permitir o acesso ao ponto final público do Serviço de Sincronização de Armazenamento, definido `incomingTrafficPolicy` `AllowAllTraffic` para. Lembre-se de substituir `<storage-sync-service-resource-group>` e `<storage-sync-service>` . .

```powershell
$storageSyncServiceResourceGroupName = "<storage-sync-service-resource-group>"
$storageSyncServiceName = "<storage-sync-service>"

$storageSyncService = Get-AzResource `
        -ResourceGroupName $storageSyncServiceResourceGroupName `
        -ResourceName $storageSyncServiceName `
        -ResourceType "Microsoft.StorageSync/storageSyncServices"

$storageSyncService.Properties.incomingTrafficPolicy = "AllowVirtualNetworksOnly"
$storageSyncService = $storageSyncService | Set-AzResource -Confirm:$false -Force
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para desativar o acesso ao ponto final público do Serviço de Sincronização de Armazenamento, definiremos a `incomingTrafficPolicy` propriedade no Serviço de Sincronização de Armazenamento para `AllowVirtualNetworksOnly` . Se quiser permitir o acesso ao ponto final público do Serviço de Sincronização de Armazenamento, definido `incomingTrafficPolicy` `AllowAllTraffic` para. Lembre-se de substituir `<storage-sync-service-resource-group>` e `<storage-sync-service>` . .

```bash
storageSyncServiceResourceGroupName="<storage-sync-service-resource-group>"
storageSyncServiceName="<storage-sync-service>"

az resource update \
        --resource-group $storageSyncServiceResourceGroupName \
        --name $storageSyncServiceName \
        --resource-type "Microsoft.StorageSync/storageSyncServices" \
        --set "properties.incomingTrafficPolicy=AllowVirtualNetworksOnly" \
        --output none
```
---

## <a name="see-also"></a>Ver também
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Implementar o Azure File Sync](storage-sync-files-deployment-guide.md)
