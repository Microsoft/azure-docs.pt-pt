---
title: Configure uma VPN ponto-a-local (P2S) no Linux para utilização com ficheiros Azure | Microsoft Docs
description: Como configurar uma VPN ponto-a-local (P2S) no Linux para utilização com ficheiros Azure
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 74422318718e318a00d7bd7ebaf8e4093ef75aa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629279"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Configure uma VPN ponto-a-local (P2S) no Linux para utilização com ficheiros Azure
Pode utilizar uma ligação VPN Ponto-a-Local (P2S) para montar as suas ações de ficheiroS Azure sobre SMB de fora de Azure, sem abrir a porta 445. Uma ligação VPN ponto-a-local é uma ligação VPN entre Azure e um cliente individual. Para utilizar uma ligação P2S VPN com ficheiros Azure, uma ligação P2S VPN terá de ser configurada para cada cliente que queira ligar. Se tiver muitos clientes que precisam de se ligar às suas ações de ficheiroS Azure a partir da sua rede no local, pode utilizar uma ligação VPN Site-to-Site (S2S) em vez de uma ligação Ponto-a-Local para cada cliente. Para saber mais, consulte [configurar uma VPN site-to-site para utilização com ficheiros Azure](storage-files-configure-s2s-vpn.md).

Recomendamos vivamente que leia [a visão geral do Azure Files](storage-files-networking-overview.md) antes de continuar com este artigo para uma discussão completa das opções de networking disponíveis para ficheiros Azure.

O artigo detalha os passos para configurar uma VPN point-to-site no Linux para montar ações de ficheiros Azure diretamente no local. Se procura encaminhar o tráfego de Azure File Sync por uma VPN, consulte [as configurações de configuração do proxy e firewall do Azure File Sync](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Pré-requisitos
- A versão mais recente do Azure CLI. Para obter mais informações sobre como instalar o Azure CLI, consulte [instalar o Azure PowerShell CLI](/cli/azure/install-azure-cli) e selecione o seu sistema operativo. Se preferir utilizar o módulo Azure PowerShell no Linux, pode, no entanto, as instruções abaixo são apresentadas para Azure CLI.

- Uma partilha de ficheiros Azure que gostaria de montar no local. As ações de ficheiros Azure são implantadas dentro de contas de armazenamento, que são construções de gestão que representam um conjunto partilhado de armazenamento no qual você pode implementar várias ações de arquivo, bem como outros recursos de armazenamento, tais como recipientes blob ou filas. Pode saber mais sobre como implementar ações de ficheiros Azure e contas de armazenamento na [Create a azure file share](storage-how-to-create-file-share.md).

- Um ponto final privado para a conta de armazenamento que contém a partilha de ficheiros Azure que pretende montar no local. Para saber mais sobre como criar um ponto final privado, consulte [os pontos finais da rede De Configuração Azure Files](storage-files-networking-endpoints.md?tabs=azure-cli). 

## <a name="install-required-software"></a>Instalar o software necessário
O gateway de rede virtual Azure pode fornecer ligações VPN usando vários protocolos VPN, incluindo IPsec e OpenVPN. Este guia mostra como usar o IPsec e usa o pacote strongSwan para fornecer o suporte no Linux. 

> Verificado com Ubuntu 18.10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Implementar uma rede virtual 
Para aceder à sua partilha de ficheiros Azure e outros recursos Azure a partir de instalações através de uma VPN Ponto-a-Local, tem de criar uma rede virtual, ou VNet. A ligação P2S VPN que irá criar automaticamente é uma ponte entre a sua máquina Linux no local e esta rede virtual Azure.

O seguinte script criará uma rede virtual Azure com três sub-redes: uma para o ponto final de serviço da sua conta de armazenamento, uma para o ponto final privado da sua conta de armazenamento, que é necessária para aceder à conta de armazenamento no local sem criar encaminhamento personalizado para o IP público da conta de armazenamento que pode mudar, e outro para o seu gateway de rede virtual que fornece o serviço VPN. 

Lembre-se de substituir `<region>` , e com os `<resource-group>` `<desired-vnet-name>` valores apropriados para o seu ambiente.

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

## <a name="create-certificates-for-vpn-authentication"></a>Criar certificados para autenticação VPN
Para que as ligações VPN das suas máquinas Linux sejam autenticadas para aceder à sua rede virtual, deve criar dois certificados: um certificado de raiz, que será fornecido ao porta de entrada da máquina virtual, e um certificado de cliente, que será assinado com o certificado raiz. O seguinte script cria os certificados necessários.

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

## <a name="deploy-virtual-network-gateway"></a>Implementar gateway de rede virtual
O gateway de rede virtual Azure é o serviço ao qual as suas máquinas Linux no local se irão ligar. A implementação deste serviço requer dois componentes básicos: um IP público que identificará a porta de entrada para os seus clientes onde quer que estejam no mundo e um certificado de raiz que criou anteriormente que será usado para autenticar os seus clientes.

Lembre-se de substituir `<desired-vpn-name-here>` pelo nome que gostaria por estes recursos.

> [!Note]  
> A implantação do gateway de rede virtual Azure pode demorar até 45 minutos. Enquanto este recurso está a ser implementado, este script de script bash bloqueará para a implementação ser concluída.
>
> As ligações P2S IKEv2/OpenVPN não são suportadas com o **SKU Básico.** Este script utiliza o **SKU VpnGw1** para o gateway de rede virtual, em conformidade.

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

## <a name="configure-the-vpn-client"></a>Configure o cliente VPN
O gateway de rede virtual Azure criará um pacote transferível com ficheiros de configuração necessários para inicializar a ligação VPN na sua máquina Linux no local. O seguinte script colocará os certificados criados no local correto e configurará o `ipsec.conf` ficheiro com os valores corretos do ficheiro de configuração no pacote transferível.

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

## <a name="mount-azure-file-share"></a>Partilha de ficheiros mount Azure
Agora que configurar a sua VPN Ponto-a-Local, pode montar a sua partilha de ficheiros Azure. O exemplo a seguir irá montar a parte sem insistência. Para montar persistentemente, consulte [Use uma partilha de ficheiros Azure com o Linux](storage-how-to-use-files-linux.md). 

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
- [Visão geral da rede de ficheiros Azure](storage-files-networking-overview.md)
- [Configure uma VPN ponto-a-local (P2S) no Windows para utilização com ficheiros Azure](storage-files-configure-p2s-vpn-windows.md)
- [Configure uma VPN site-to-site (S2S) para utilização com ficheiros Azure](storage-files-configure-s2s-vpn.md)