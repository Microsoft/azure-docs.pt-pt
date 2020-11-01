---
title: 'Ligue-se a um VNet a partir de um computador - P2S VPN e autenticação de certificado Azure nativo: PowerShell'
description: Ligue os clientes Windows e macOS de forma segura à rede virtual Azure utilizando certificados P2S e auto-assinados ou emitidos pela AC. Este artigo utiliza o PowerShell.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: b6df7aa919721576aad10d6a476be976ef81df7d
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145876"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Configure uma ligação VPN ponto-a-local a um VNet utilizando a autenticação de certificado azure nativo: PowerShell

Este artigo ajuda-o a ligar de forma segura clientes individuais que executam Windows, Linux ou macOS a um Azure VNet. As ligações VPN ponto a local são úteis quando pretende ligar-se ao seu VNet a partir de um local remoto, tal como quando está a fazer telecomusagem a partir de casa ou de uma conferência. Pode também utilizar P2S em vez de uma VPN Site a Site, quando são poucos os clientes que precisam de ligar a uma VNet. As ligações ponto-a-local não requerem um dispositivo VPN ou um endereço IP virado para o público. A P2S cria a ligação VPN através de SSTP (Secure Socket Tunneling Protocol) ou de IKEv2.

:::image type="content" source="./media/vpn-gateway-how-to-point-to-site-rm-ps/point-to-site-diagram.png" alt-text="Ligue de um computador a um Azure VNet - diagrama de ligação ponto-a-local":::

Para obter mais informações sobre a VPN ponto-a-local, consulte [Sobre a VPN ponto a local.](point-to-site-about.md) Para criar esta configuração utilizando o portal Azure, consulte [configurar uma VPN ponto a local utilizando o portal Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Pré-requisitos

Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

### <a name="azure-powershell"></a>Azure PowerShell

>[!IMPORTANT]
> Muitos dos passos deste artigo podem usar a Azure Cloud Shell. No entanto, não pode usar a Cloud Shell para gerar certificados. Além disso, para carregar a chave pública do certificado de raiz, deve utilizar o Azure PowerShell localmente, ou o portal Azure.
>

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-sign-in"></a><a name="signin"></a>1. Iniciar sção

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="2-declare-variables"></a><a name="declare"></a>2. Declarar variáveis

Usamos variáveis para este artigo para que você possa facilmente alterar os valores para aplicar ao seu próprio ambiente sem ter que mudar os exemplos em si. Declare as variáveis que quer utilizar. Pode utilizar a seguinte amostra, substituindo os valores pelos seus próprios valores quando necessário. Se fechar a sessão PowerShell/Cloud Shell em qualquer ponto durante o exercício, basta copiar e colar novamente os valores para re declarar as variáveis.

```azurepowershell-interactive
$VNetName  = "VNet1"
$FESubName = "FrontEnd"
$GWSubName = "GatewaySubnet"
$VNetPrefix = "10.1.0.0/16"
$FESubPrefix = "10.1.0.0/24"
$GWSubPrefix = "10.1.255.0/27"
$VPNClientAddressPool = "172.16.201.0/24"
$RG = "TestRG1"
$Location = "EastUS"
$GWName = "VNet1GW"
$GWIPName = "VNet1GWpip"
$GWIPconfName = "gwipconf"
$DNS = "10.2.1.4"
```

## <a name="3-configure-a-vnet"></a><a name="ConfigureVNet"></a>3. Configurar um VNet

1. Crie um grupo de recursos.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```

1. Crie as configurações da sub-rede para a rede virtual, nomeando-as *FrontEnd* e *GatewaySubnet* . Estes prefixos têm de fazer parte do espaço de endereços da VNet que declarou.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```

1. Criar a rede virtual.

   Neste exemplo, o parâmetro de servidor -DnsServer é opcional. A especificação de um valor não cria um novo servidor DNS. O endereço IP do servidor DNS que especificar deve ser um servidor DNS que consiga resolver os nomes dos recursos a que se está a ligar a partir da sua VNet. Este exemplo utiliza um endereço IP privado, mas é provável que este não seja o endereço IP do servidor DNS. Certifique-se de que utiliza os seus próprios valores. O valor que especificar será utilizado pelos recursos que implementa na VNet, não pela ligação P2S ou o cliente VPN.

   ```azurepowershell-interactive
       New-AzVirtualNetwork `
      -ResourceGroupName $RG `
      -Location $Location `
      -Name $VNetName `
      -AddressPrefix $VNetPrefix `
      -Subnet $fesub, $gwsub `
      -DnsServer $DNS
   ```

1. Especifique as variáveis para a rede virtual que criou.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```

1. Um gateway de VPN deve ter um endereço IP público. Primeira, requeira o recurso de endereço IP e, em seguida, faça referência ao mesmo ao criar o gateway de rede virtual. O endereço IP é dinamicamente atribuído ao recurso quando o gateway de VPN é criado. O Gateway de VPN, atualmente, apenas suporta a alocação de endereços IP públicos *dinâmicos* . Não é possível pedir uma atribuição de endereço IP Público Estático. No entanto, isto não significa que o endereço IP é alterado depois de ser atribuído ao gateway de VPN. O endereço IP Público só é alterado quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN.

   Solicite um endereço IP público atribuído de forma dinâmica.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="4-create-the-vpn-gateway"></a><a name="creategateway"></a>4. Criar o portal VPN

Neste passo, configura e cria o portal de rede virtual para o seu VNet.

* O -GatewayType tem de ser **Vpn** e o -VpnType tem de ser **RouteBased** .
* O -VpnClientProtocol é utilizado para especificar os tipos de túnel que quer ativar. As opções do túnel são **OpenVPN, SSTP** e **IKEv2.** Pode optar por ativar um deles ou qualquer combinação suportada. Se pretender ativar vários tipos, especifique os nomes separados por uma vírgula. A OpenVPN e a SSTP não podem ser ativadas em conjunto. O cliente strongSwan no Android e Linux e o cliente VPN IKEv2 nativo em dispositivos iOS e OSX utilizarão apenas o túnel IKEv2 para estabelecer a ligação. Os clientes Windows, primeiro, experimentam o IKEv2 e, se não conseguirem estabelecer a ligação, voltam ao SSTP. Pode utilizar o cliente OpenVPN para ligar ao tipo de túnel OpenVPN.
* O gateway de rede virtual 'Basic' SKU não suporta a autenticação IKEv2, OpenVPN ou RADIUS. Se está a planear que os clientes Mac se conectem à sua rede virtual, não utilize o SKU Básico.
* Um gateway de VPN pode demorar até 45 minutos a concluir, consoante a [sku do gateway](vpn-gateway-about-vpn-gateway-settings.md) que selecionar. Este exemplo utiliza IKEv2.

1. Configure e crie o gateway de rede virtual da sua VNet. Leva aproximadamente 45 minutos para a porta de entrada criar.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
   -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
   -VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
   ```

1. Uma vez criado o seu gateway, pode vê-lo usando o seguinte exemplo. Se fechar o PowerShell ou tiver esgotado o tempo enquanto o seu portal estava a ser criado, pode [declarar novamente as suas variáveis.](#declare)

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroup $RG
   ```

## <a name="5-add-the-vpn-client-address-pool"></a><a name="addresspool"></a>5. Adicione o conjunto de endereços de cliente VPN

Depois do gateway VPN acabar de criar, pode adicionar o conjunto de endereços de cliente VPN. O conjunto de endereços do cliente VPN é o intervalo a partir do qual os clientes VPN recebem um endereço IP quando ligam. Utilize um intervalo de endereços IP privados que não se sobreponha à localização no local onde irá estabelecer ligação ou com a VNet a que pretende ligar.

Neste exemplo, o conjunto de endereços de cliente VPN é declarado como uma [variável](#declare) num passo anterior.

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="6-generate-certificates"></a><a name="Certificates"></a>6. Gerar certificados

>[!IMPORTANT]
> Não é possível gerar certificados usando a Azure Cloud Shell. Deve utilizar um dos métodos descritos nesta secção. Se quiser utilizar o PowerShell, tem de o instalar localmente.
>

Os certificados são usados pela Azure para autenticar clientes VPN para VPNs ponto a local. Carrega as informações da chave pública do certificado de raiz para o Azure. A chave pública é então considerada "fidedigna". Os certificados de cliente têm de ser gerados a partir do certificado de raiz fidedigna e, em seguida, instalado em cada computador cliente no arquivo de Certificados Atuais do Utilizador/Pessoais. O certificado é utilizado para autenticar o cliente quando é iniciada uma ligação à VNet. 

Se utilizar certificados autoassinados, eles têm de ser criados com parâmetros específicos. Pode criar um certificado autoassinado com as instruções para [PowerShell e Windows 10](vpn-gateway-certificates-point-to-site.md) ou, se não tiver o Windows 10, pode utilizar o [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). É importante que siga as etapas nas instruções ao gerar os certificados de raiz autoassinados e os certificados de cliente. Caso contrário, os certificados que gerar não serão compatíveis com ligações P2S e irá receber um erro de ligação.

### <a name="root-certificate"></a><a name="cer"></a>Certificado de raiz

1. [!INCLUDE [Root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

1. Depois de criar o certificado raiz, [exporte](vpn-gateway-certificates-point-to-site.md#cer) os dados do certificado público (não a chave privada) como um ficheiro X.509 codificado por Base64.cer.

### <a name="client-certificate"></a><a name="generate"></a>Certificado de cliente

1. [!INCLUDE [Generate a client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

1. Depois de criar [export](vpn-gateway-certificates-point-to-site.md#clientexport) certificado de cliente, exporte-o. O certificado de cliente será distribuído para os computadores clientes que se ligarão.

## <a name="7-upload-the-root-certificate-public-key-information"></a><a name="upload"></a>7. Faça o upload do certificado de raiz informações de chaves públicas

Verifique se o gateway de VPN acabou de criar. Assim que estiver pronto, pode carregar o ficheiro .cer (que contém as informações da chave pública) para um certificado de raiz fidedigna no Azure. Depois de o ficheiro .cer ser carregado, o Azure pode utilizá-lo para autenticar clientes que tenham instalado um certificado de cliente gerado a partir do certificado de raiz fidedigna. Pode carregar ficheiros de certificado de raiz fidedigna adicionais - até um total de 20 - mais tarde, se necessário.

>[!NOTE]
> Não é possível carregar o ficheiro .cer usando a Azure Cloud Shell. Pode utilizar o PowerShell localmente no seu computador, ou pode utilizar os passos do [portal Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
>

1. Declare a variável para o nome de certificado, substituindo o valor pelo seu próprio valor.

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```

1. Substitua o caminho do ficheiro pelo seu e, em seguida, execute os cmdlets.

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Carregar informações de chaves públicas para o Azure. Uma vez que a informação do certificado é carregada, a Azure considera que se trata de um certificado de raiz fidedigno. Ao fazer o upload, certifique-se de que está a executar o PowerShell localmente no seu computador ou, em vez disso, pode utilizar os passos do [portal Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Não pode fazer upload usando a Azure Cloud Shell.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64
   ```

## <a name="8-install-an-exported-client-certificate"></a><a name="clientcertificate"></a>8. Instalar um certificado de cliente exportado

Os seguintes passos ajudam a instalar-se num cliente Windows. Para obter mais clientes e mais informações, consulte [instalar um certificado de cliente.](point-to-site-how-to-vpn-client-install-azure-cert.md)

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

Certifique-se de que o certificado de cliente foi exportado como um ficheiro. pfx, juntamente com a cadeia de certificados inteira (que é a predefinição). Caso contrário, as informações do certificado de raiz não estão presentes no computador do cliente e o cliente não conseguirá autenticar corretamente.

## <a name="9-configure-the-vpn-client"></a><a name="clientconfig"></a>9. Configurar o cliente VPN

Nesta secção, configura o cliente nativo para que o seu computador se conecte ao portal de rede virtual. Por exemplo, quando se vai às definições de VPN no seu computador Windows, pode adicionar ligações VPN. Uma ligação ponto-a-local requer configurações de configuração específicas. Estes passos ajudam-no a criar um pacote com as definições específicas que o seu cliente VPN nativo precisa para ser capaz de se conectar à rede virtual através de uma ligação ponto-a-local.

Pode utilizar os seguintes exemplos rápidos para gerar e instalar o pacote de configuração do cliente. Para obter mais informações sobre o conteúdo do pacote e instruções adicionais prestes a gerar e instalar ficheiros de configuração do cliente VPN, consulte [Criar e instalar ficheiros de configuração do cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md).

Se precisar de declarar as suas variáveis de novo, pode encontrá-las [aqui.](#declare)

### <a name="to-generate-configuration-files"></a>Para gerar ficheiros de configuração

```azurepowershell-interactive
$profile=New-AzVpnClientConfiguration -ResourceGroupName $RG -Name $GWName -AuthenticationMethod "EapTls"

$profile.VPNProfileSASUrl
```

### <a name="to-install-the-client-configuration-package"></a>Para instalar o pacote de configuração do cliente

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. Ligar-se ao Azure

### <a name="windows-vpn-client"></a>Cliente VPN do Windows

[!INCLUDE [Connect from Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [Client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="mac-vpn-client"></a>Cliente Mac VPN

Na caixa de diálogo Rede, localize o perfil de cliente que quer utilizar e, em seguida, clique em **Ligar** .
Consulte [a Instalação - Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac) para obter instruções detalhadas. Se tiver problemas em ligar, verifique se o gateway de rede virtual não está a utilizar um SKU Básico. A SKU básica não é suportada para clientes Mac.

  ![Ligação do Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-a-connection"></a><a name="verify"></a>Para verificar uma ligação

Estas instruções aplicam-se aos clientes Windows.

1. Para verificar se a ligação VPN está ativa, abra uma linha de comandos elevada e execute *ipconfig/all* .
2. Veja os resultados. Note que o endereço IP que recebeu é um dos endereços dentro do Pool de Endereços de Cliente VPN ponto a local que especificou na sua configuração. Os resultados são semelhantes a este exemplo:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.13(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Ligar a uma máquina virtual

Estas instruções aplicam-se aos clientes Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Certifique-se de que o pacote de configuração de clientes VPN gerado depois dos endereços IP do servidor DNS foi especificado para a VNet. Se atualizou os endereços IP do servidor DNS, gere e instale um novo pacote de configuração de cliente VPN.

* Utilize “ipconfig” para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador a partir do qual se está a ligar. Se o endereço IP estiver no âmbito do intervalo de endereços da VNet a que se está a ligar, ou no âmbito do intervalo de endereços do seu VPNClientAddressPool, tal trata-se de um espaço de endereços sobreposto. Quando o seu espaço de endereços se sobrepõe desta forma, o tráfego de rede não chega ao Azure e permanece na rede local.

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>Para adicionar ou remover um certificado de raiz

Pode adicionar e remover certificados de raiz fidedigna do Azure. Quando remove um certificado de raiz, os clientes que têm um certificado gerado a partir do certificado de raiz não podem autenticar e não serão capazes de ligar. Se quiser que um cliente faça a autenticação e estabeleça ligação, terá de instalar um novo certificado de cliente gerado a partir de um certificado de raiz considerado fidedigno (carregado) no Azure. Estes passos requerem cmdlets Azure PowerShell instalados localmente no seu computador (não Azure Cloud Shell). Também pode utilizar o portal Azure para adicionar certificados de raiz.

**Para adicionar:**

Pode adicionar até 20 ficheiros .cer de certificado de raiz ao Azure. Os seguintes passos ajudam a adicionar um certificado de raiz. 

1. Prepare o ficheiro. cer para carregar:

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Carregue o ficheiro. Só pode carregar um ficheiro de cada vez.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64_3
   ```

1. Para verificar se o ficheiro de certificado foi carregado:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

**Para remover:**

1. Declare as variáveis. Modifique as variáveis no exemplo para corresponder ao certificado que pretende remover.

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```

1. Remova o certificado.

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```

1. Utilize o exemplo seguinte para verificar se o certificado foi removido com êxito.

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-or-reinstate-a-client-certificate"></a><a name="revoke"></a>Revogar ou repor um certificado de cliente

Pode revogar certificados de cliente. A lista de revogação de certificados permite-lhe negar seletivamente a conectividade ponto-a-local com base em certificados individuais de cliente. Isto é diferente da remoção de um certificado de raiz fidedigna. Se remover um certificado de raiz .cer fidedigno do Azure, revoga o acesso a todos os certificados de cliente gerados/assinados pelo certificado de raiz revogado. A revogação de um certificado de cliente, em vez do certificado de raiz, permite que os outros certificados que foram gerados a partir do certificado de raiz continuem a ser utilizados para autenticação.

A prática comum é utilizar o certificado de raiz para gerir o acesso nos níveis de equipa ou organização e utilizar certificados de cliente revogados para controlo de acesso detalhado dos utilizadores individuais.

**Para revogar:**

1. Obtenha o thumbprint do certificado de cliente. Para obter mais informações, veja [Como obter o Thumbprint de um Certificado](https://msdn.microsoft.com/library/ms734695.aspx).

1. Copie as informações para um editor de texto e remova todos os espaços, para que seja uma cadeia contínua. Esta cadeia está declarada como uma variável no próximo passo.

1. Declare as variáveis. Certifique-se de que declara o thumbprint que obteve no passo anterior.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Adicione o thumbprint à lista de certificados revogados. Vê "Êxito" quando o thumbprint for adicionado.

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```

1. Certifique-se de que o thumbprint foi adicionado à lista de revogação de certificados.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

1. Depois de o thumbprint ser adicionado, o certificado já não pode ser utilizado para ligar. Os clientes que se tentarem ligar com este certificado irão receber uma mensagem a indicar que o certificado já não é válido.

**Para reintegrar:**

Pode restabelecer um certificado de cliente, removendo o thumbprint da lista de certificados de cliente revogados.

1. Declare as variáveis. Certifique-se de que declara o thumbprint correto para o certificado que pretende restabelecer.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Remova o thumbprint do certificado da lista de revogação de certificados.

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```

1. Verifique se o thumbprint é removido da lista revogada.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>FAQ ponto-a-local

Para obter informações adicionais ponto a local, consulte as [FAQ ponto a local do Gateway VPN](vpn-gateway-vpn-faq.md#P2S)

## <a name="next-steps"></a>Passos seguintes

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/). Para compreender melhor o funcionamento em rede e as máquinas virtuais, veja [Descrição geral da rede VM do Azure e Linux](../virtual-machines/linux/azure-vm-network-overview.md).

Para obter informações de resolução de problemas P2S, consulte [Resolução de problemas de ligações ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
