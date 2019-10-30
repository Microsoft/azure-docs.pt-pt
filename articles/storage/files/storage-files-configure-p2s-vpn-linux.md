---
title: Configurar uma VPN ponto a site (P2S) no Linux para uso com os arquivos do Azure | Microsoft Docs
description: Como configurar uma VPN ponto a site (P2S) no Linux para uso com os arquivos do Azure
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b988435fc6928d52321cb427e2412e7ca81680d2
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73141746"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Configurar uma VPN ponto a site (P2S) no Linux para uso com os arquivos do Azure
Você pode usar uma conexão VPN ponto a site (P2S) para montar seus compartilhamentos de arquivos do Azure via SMB de fora do Azure, sem abrir a porta 445. Uma conexão VPN ponto a site é uma conexão VPN entre o Azure e um cliente individual. Para usar uma conexão VPN P2S com os arquivos do Azure, uma conexão VPN P2S precisará ser configurada para cada cliente que deseja se conectar. Se você tiver muitos clientes que precisam se conectar aos compartilhamentos de arquivos do Azure de sua rede local, você poderá usar uma conexão VPN de site a site (S2S) em vez de uma conexão ponto a site para cada cliente. Para saber mais, confira [Configurar uma VPN site a site para uso com os arquivos do Azure](storage-files-configure-s2s-vpn.md).

É altamente recomendável que você leia a [visão geral de rede de arquivos do Azure](storage-files-networking-overview.md) antes de continuar com este artigo para obter uma discussão completa das opções de rede disponíveis para os arquivos do Azure.

O artigo fornece detalhes sobre as etapas para configurar uma VPN ponto a site no Linux para montar compartilhamentos de arquivos do Azure diretamente no local. Se você pretende rotear Sincronização de Arquivos do Azure tráfego por uma VPN, consulte [definindo configurações de proxy e firewall de sincronização de arquivos do Azure](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Pré-requisitos
- A versão mais recente do CLI do Azure. Para obter mais informações sobre como instalar o CLI do Azure, consulte [instalar a CLI do Azure PowerShell](https://docs.microsoft.com/cli/azure/install-azure-cli) e selecionar o sistema operacional. Se preferir usar o módulo Azure PowerShell no Linux, você pode, no entanto, as instruções a seguir são apresentadas para CLI do Azure.

- Um compartilhamento de arquivos do Azure que você deseja montar no local. Você pode usar um compartilhamento de arquivos [Standard](storage-how-to-create-file-share.md) ou [Premium do Azure](storage-how-to-create-premium-fileshare.md) com a VPN ponto a site.

## <a name="install-required-software"></a>Instalar o software necessário
O gateway de rede virtual do Azure pode fornecer conexões VPN usando vários protocolos VPN, incluindo IPsec e OpenVPN. Este guia mostra como usar o IPsec e usa o pacote strongSwan para fornecer o suporte no Linux. 

> Verificado com o Ubuntu 18,10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Implantar uma rede virtual 
Para acessar o compartilhamento de arquivos do Azure e outros recursos do Azure do local por meio de uma VPN ponto a site, você deve criar uma rede virtual ou VNet. A conexão VPN P2S que você criará automaticamente é uma ponte entre seu computador Linux local e essa rede virtual do Azure.

O script a seguir criará uma rede virtual do Azure com três sub-redes: uma para o ponto de extremidade de serviço da sua conta de armazenamento, uma para o ponto de extremidade particular da sua conta de armazenamento, que é necessário para acessar a conta de armazenamento local sem criar a personalizada roteamento para o IP público da conta de armazenamento que pode ser alterado e outro para o gateway de rede virtual que fornece o serviço de VPN. 

Lembre-se de substituir `<region>`, `<resource-group>`e `<desired-vnet-name>` pelos valores apropriados para o seu ambiente.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="restrict-the-storage-account-to-the-virtual-network"></a>Restringir a conta de armazenamento à rede virtual
Por padrão, quando você cria uma conta de armazenamento, você pode acessá-la de qualquer lugar do mundo, desde que você tenha o meio de autenticar sua solicitação (por exemplo, com sua identidade de Active Directory ou com a chave da conta de armazenamento). Para restringir o acesso a essa conta de armazenamento para a rede virtual que você acabou de criar, você precisa criar um conjunto de regras de rede que permita o acesso dentro da rede virtual e negue todos os outros acessos.

Restringir a conta de armazenamento à rede virtual requer o uso de um ponto de extremidade de serviço. O ponto de extremidade de serviço é uma construção de rede pela qual o IP público/DNS pode ser acessado somente de dentro da rede virtual. Como não há garantia de que o endereço IP público permaneça o mesmo, queremos usar um ponto de extremidade privado em vez de um ponto de extremidade de serviço para a conta de armazenamento, no entanto, não é possível restringir a conta de armazenamento, a menos que um ponto de extremidade de serviço também seja exposto.

Lembre-se de substituir `<storage-account-name>` pela conta de armazenamento que você deseja acessar.

```bash
storageAccountName="<storage-account-name>"

az storage account network-rule add \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --subnet $serviceEndpointSubnet > /dev/null

az storage account update \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" > /dev/null
```

## <a name="create-a-private-endpoint-preview"></a>Criar um ponto de extremidade privado (visualização)
A criação de um ponto de extremidade privado para sua conta de armazenamento dá à sua conta de armazenamento um endereço IP dentro do espaço de endereço IP de sua rede virtual. Quando você monta o compartilhamento de arquivos do Azure do local usando esse endereço IP privado, as regras de roteamento definidas de forma automática pela instalação de VPN rotearão sua solicitação de montagem para a conta de armazenamento por meio da VPN. 

```bash
zoneName="privatelink.file.core.windows.net"

storageAccount=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "id" | tr -d '"')

az resource update \
    --ids $privateEndpointSubnet \
    --set properties.privateEndpointNetworkPolicies=Disabled > /dev/null

az network private-endpoint create \
    --resource-group $resourceGroupName \
    --name "$storageAccountName-PrivateEndpoint" \
    --location $region \
    --subnet $privateEndpointSubnet \
    --private-connection-resource-id $storageAccount \
    --group-ids "file" \
    --connection-name "privateEndpointConnection" > /dev/null

az network private-dns zone create \
    --resource-group $resourceGroupName \
    --name $zoneName > /dev/null

az network private-dns link vnet create \
    --resource-group $resourceGroupName \
    --zone-name $zoneName \
    --name "$virtualNetworkName-link" \
    --virtual-network $virtualNetworkName \
    --registration-enabled false > /dev/null

networkInterfaceId=$(az network private-endpoint show \
    --name "$storageAccountName-PrivateEndpoint" \
    --resource-group $resourceGroupName \
    --query 'networkInterfaces[0].id' | tr -d '"')
 
storageAccountPrivateIP=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query "properties.ipConfigurations[0].properties.privateIPAddress" | tr -d '"')

fqdnQuery="properties.ipConfigurations[0].properties.privateLinkConnectionProperties.fqdns[0]"
fqdn=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query $fqdnQuery | tr -d '"')

az network private-dns record-set a create \
    --name $storageAccountName \
    --zone-name $zoneName \
    --resource-group $resourceGroupName > /dev/null
```

## <a name="create-certificates-for-vpn-authentication"></a>Criar certificados para autenticação de VPN
Para que as conexões VPN de seus computadores Linux locais sejam autenticadas para acessar sua rede virtual, você deve criar dois certificados: um certificado raiz, que será fornecido para o gateway de máquina virtual e um certificado de cliente, que será assinado com o certificado raiz. O script a seguir cria os certificados necessários.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>Implantar o gateway de rede virtual
O gateway de rede virtual do Azure é o serviço ao qual suas máquinas Linux locais se conectarão. A implantação desse serviço requer dois componentes básicos: um IP público que identificará o gateway para seus clientes onde quer que estejam no mundo e um certificado raiz criado anteriormente que será usado para autenticar seus clientes.

Lembre-se de substituir `<desired-vpn-name-here>` pelo nome que você deseja para esses recursos.

> [!Note]  
> A implantação do gateway de rede virtual do Azure pode levar até 45 minutos. Enquanto esse recurso estiver sendo implantado, esse script de bash bloqueará a conclusão da implantação. Isto era esperado.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>Configurar o cliente VPN
O gateway de rede virtual do Azure criará um pacote para download com os arquivos de configuração necessários para inicializar a conexão VPN no computador Linux local. O script a seguir coloca os certificados que você criou no local correto e configura o arquivo de `ipsec.conf` com os valores corretos do arquivo de configuração no pacote que pode ser baixado.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Montar o compartilhamento de arquivos do Azure
Agora que você configurou sua VPN ponto a site, você pode montar o compartilhamento de arquivos do Azure. O exemplo a seguir montará o compartilhamento de forma não persistente. Para montar de forma persistente, consulte [usar um compartilhamento de arquivos do Azure com o Linux](storage-how-to-use-files-linux.md). 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>Ver também
- [Visão geral da rede de arquivos do Azure](storage-files-networking-overview.md)
- [Configurar uma VPN ponto a site (P2S) no Windows para uso com os arquivos do Azure](storage-files-configure-p2s-vpn-windows.md)
- [Configurar uma VPN S2S (site a site) para uso com os arquivos do Azure](storage-files-configure-s2s-vpn.md)