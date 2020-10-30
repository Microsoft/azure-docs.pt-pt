---
title: 'Ligue-se a um VNet utilizando a autenticação de certificado de & P2S: portal'
titleSuffix: Azure VPN Gateway
description: Ligue os clientes Windows, Mac OS X e Linux de forma segura a uma rede virtual Azure utilizando certificados P2S e auto-assinados ou emitidos por CA. Este artigo utiliza o portal do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: fd95de8033fc5a986ac30677a4272336b1e17244
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041513"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Configure uma ligação VPN ponto-a-local a um VNet utilizando a autenticação de certificado azure nativo: portal Azure

Este artigo ajuda-o a ligar de forma segura clientes individuais que executam o Windows, Linux ou Mac OS X a um Azure VNet. As ligações VPN Ponto a Site são úteis quando pretende ligar à VNet a partir de uma localização remota, como em teletrabalho em casa ou numa conferência. Pode também utilizar P2S em vez de uma VPN Site a Site, quando são poucos os clientes que precisam de ligar a uma VNet. As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público. A P2S cria a ligação VPN através de SSTP (Secure Socket Tunneling Protocol) ou de IKEv2. Para obter mais informações sobre VPN Ponto a Site, veja [Acerca da VPN Ponto a Site](point-to-site-about.md).

![Ligar um computador a uma VNet do Azure - diagrama da ligação Ponto a Site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)

## <a name="architecture"></a>Arquitetura

As ligações de autenticação de certificados nativos do Azure Ponto a Site utilizam os seguintes itens, que irá configurar neste exercício:

* Um gateway de VPN RouteBased.
* A chave pública (ficheiro .cer) de um certificado de raiz que é carregado para o Azure. Assim que o certificado estiver carregado, é considerado um certificado fidedigno e é utilizado para autenticação.
* Um certificado de cliente que é gerado a partir do certificado de raiz. O certificado de cliente instalado em cada computador cliente que vai ligar à VNet. Este certificado é utilizado para autenticação de cliente.
* Uma configuração de cliente VPN. Os ficheiros de configuração de cliente VPN contêm as informações necessárias para o cliente se ligar à VNet. Os ficheiros configuram o cliente VPN existente que é nativo ao sistema operativo. Cada cliente que estabelece ligação tem de ser configurado com as definições dos ficheiros de configuração.

#### <a name="example-values"></a><a name="example"></a>Valores de exemplo

Pode utilizar os valores seguintes para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo:

* **Nome da VNet:** VNet1
* **Espaço do endereço:** 10.1.0.0/16<br>Para este exemplo, utilizamos apenas um espaço de endereços. Pode ter mais do que um espaço de endereços para a sua VNet.
* **Nome da sub-rede:** FrontEnd
* **Intervalo de endereços da sub-rede:** 10.1.0.0/24
* **Subscrição:** se tiver várias subscrições, verifique se tem a correta.
* **Grupo de Recursos:** TestRG1
* **Localização:** Leste dos EUA
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Nome do gateway de rede virtual:** VNet1GW
* **Tipo de gateway:** VPN
* **Tipo de VPN:** baseado na rota
* **Nome do endereço IP público:** VNet1GWpip
* **Tipo de ligação:** Ponto a site
* **Piscina de endereços do cliente:** 172.16.201.0/24<br>Os clientes VPN que se ligam à VNet através desta ligação Ponto a Site recebem um endereço IP a partir do conjunto de endereços de cliente especificado.

## <a name="1-create-a-virtual-network"></a><a name="createvnet"></a>1. Criar uma rede virtual

Antes de começar, verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. Criar um portal de rede virtual

Neste passo, vai criar o gateway de rede virtual da VNet. Criar um gateway, muitas vezes, pode demorar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

>[!NOTE]
>O Gateway Basic SKU não suporta a autenticação IKEv2 ou RADIUS. Se planeia ter clientes Mac ligados à sua rede virtual, não utilize o SKU Básico.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="3-generate-certificates"></a><a name="generatecert"></a>3. Gerar certificados

Os certificados são utilizados pelo Azure para autenticar clientes que se ligam a uma VNet através de uma ligação VPN Ponto a Site. Quando conseguir obter um certificado de raiz, [carregue](#uploadfile) as informações da chave pública do certificado de raiz para o Azure. O certificado de raiz é então considerado "fidedigno" pelo Azure para a ligação através do P2S para a rede virtual. Também pode gerar certificados de cliente a partir do certificado de raiz fidedigna e, em seguida, instalá-los em cada computador do cliente. O certificado de cliente é utilizado para autenticar o cliente quando é iniciada uma ligação à VNet. 

### <a name="1-root-certificate"></a><a name="getcer"></a>1. Certificado de raiz

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="2-client-certificate"></a><a name="generateclientcert"></a>2. Certificado de cliente

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="4-add-the-client-address-pool"></a><a name="addresspool"></a>4. Adicione o conjunto de endereços do cliente

O conjunto de endereços de cliente é um conjunto de endereços IP privados que especificar. Os clientes que se ligam através da VPN Ponto a Site recebem dinamicamente um endereço IP deste intervalo. Utilize um intervalo de endereços IP privados que não se sobreponha à localização no local a partir da qual se ligou ou à VNet à qual se quer ligar. Se configurar vários protocolos e o SSTP for um dos protocolos, então o conjunto de endereços configurado é dividido entre os protocolos configurados de forma igual.

1. Assim que o gateway da rede virtual for criado, navegue até à secção **Definições** da página do gateway de rede virtual. Na secção **Definições,** selecione **configuração ponto-a-local** . Selecione **Configurar agora** para abrir a página de configuração.

   ![Página Ponto a Site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-configure.png "Configuração ponto-a-local agora")
2. Na página **de configuração ponto-a-local,** pode configurar uma variedade de configurações. Se não vir o tipo de túnel ou tipo de autenticação nesta página, o seu portal está a utilizar o SKU Básico. A SKU Básica não suporta a autenticação IKEv2 ou RADIUS. Se pretender utilizar estas definições, tem de eliminar e recriar o gateway utilizando um gateway diferente SKU.

   [![Página de configuração ponto-a-local](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/certificate-settings-address.png "especificar piscina de endereços")](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/certificate-settings-expanded.png#lightbox)
3. Na caixa de **piscina Address,** adicione o intervalo de endereço IP privado que pretende utilizar. Os clientes VPN recebem dinamicamente um endereço IP do intervalo que especificou. A máscara mínima de sub-rede é de 29 bits para configuração ativa/passiva e 28 bits para configuração ativa/ativa.
4. Mova-se para a secção seguinte para configurar o tipo de túnel.

## <a name="5-configure-tunnel-type"></a><a name="tunneltype"></a>5. Tipo de túnel de configuração

Pode selecionar o tipo de túnel. As opções do túnel são OpenVPN, SSTP e IKEv2.

* O cliente strongSwan no Android e Linux e o cliente VPN IKEv2 nativo em dispositivos iOS e OSX utilizarão apenas o túnel IKEv2 para estabelecer a ligação.
* Os clientes do Windows tentam o IKEv2 primeiro e, se isso não se ligar, voltam a ser SSTP.
* Pode utilizar o cliente OpenVPN para ligar ao tipo de túnel OpenVPN.

![Tipo de túnel](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/tunnel.png "especificar o tipo de túnel")

## <a name="6-configure-authentication-type"></a><a name="authenticationtype"></a>6. Tipo de autenticação configurada

Para **tipo de autenticação** , selecione **certificado Azure** .

  ![Tipo de autenticação](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authentication-type.png "especificar tipo de autenticação")

## <a name="7-upload-the-root-certificate-public-certificate-data"></a><a name="uploadfile"></a>7. Carregar os dados do certificado de origem do certificado público

Pode carregar certificados de raiz fidedigna adicionais até um total de 20. Assim que os dados do certificado público forem carregados, o Azure pode utilizá-lo para autenticar clientes que tenham instalado um certificado de cliente gerado a partir do certificado de raiz fidedigna. Pode carregar as informações da chave pública do certificado de raiz para o Azure.

1. Os certificados são adicionados na página **Configuração ponto a site** na secção **Certificado de raiz** .
2. Certifique-se de que exportou o certificado de raiz como um ficheiro X.509 codificado com Base-64 (.cer). Tem de exportar o certificado neste formato para poder abrir o certificado com o editor de texto.
3. Abra o certificado com um editor de texto, como o Bloco de Notas. Quando copiar os dados de certificado, certifique-se de que copia o texto como uma linha contínua sem símbolos de retorno ou avanços de linha. Poderá ter de modificar a sua vista no editor de texto para "Mostrar Símbolo/Mostrar todos os carateres" para ver os símbolos de retorno ou avanços de linha. Copie apenas a secção seguinte como uma linha contínua:

   ![Dados de certificado](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png "copiar dados de certificados de raiz")
4. Cole os dados de certificado no campo **Dados de Certificado Público** . **Nomeie** o certificado e, em seguida, **selecione Guardar** . Pode adicionar até 20 certificados de raiz fidedigna.

   ![Dados de certificado de pasta](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png "dados de certificado de pasta")
5. **Selecione Guardar** no topo da página para guardar todas as definições de configuração.

   ![Guardar configuração](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png "salvar configuração")

## <a name="8-install-an-exported-client-certificate"></a><a name="installclientcert"></a>8. Instalar um certificado de cliente exportado

Se quiser criar uma ligação P2S a partir de um computador cliente sem ser o utilizado para gerar os certificados de cliente, tem de instalar um certificado de cliente. Ao instalar um certificado de cliente, vai precisar da palavra-passe que foi criada quando o certificado de cliente foi exportado.

Certifique-se de que o certificado de cliente foi exportado como um ficheiro. pfx, juntamente com a cadeia de certificados inteira (que é a predefinição). Caso contrário, as informações do certificado de raiz não estão presentes no computador do cliente e o cliente não conseguirá autenticar corretamente.

Para obter os passos de instalação, veja [Instalar um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="9-generate-and-install-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>9. Gerar e instalar o pacote de configuração do cliente VPN

Os ficheiros de configuração de cliente VPN contêm as definições para configurar dispositivos para ligar a uma VNet através de uma ligação P2S. Para obter instruções sobre como gerar e instalar ficheiros de configuração de cliente VPN, veja [Criar e instalar ficheiros de configuração de cliente VPN para configurações P2S da autenticação de certificados nativa do Azure](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. Ligar-se ao Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Para ligar a partir de um cliente VPN do Windows

[!INCLUDE [Connect from a Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Para ligar a partir de um cliente VPN do Mac

A partir da caixa de diálogo 'Rede', localiza o perfil do cliente que pretende utilizar, especifica as definições do [VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac)e, em seguida, selecione **Connect** .

Consulte [a Instalação - Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac) para obter instruções detalhadas. Se tiver problemas em ligar, verifique se o gateway de rede virtual não está a utilizar um SKU Básico. A SKU básica não é suportada para clientes Mac.

  ![Ligação do Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png "Ligar")

## <a name="to-verify-your-connection"></a><a name="verify"></a>Para verificar a ligação

Estas instruções aplicam-se aos clientes Windows.

1. Para verificar se a ligação VPN está ativa, abra uma linha de comandos elevada e execute *ipconfig/all* .
2. Veja os resultados. Repare que o endereço IP que recebeu é um dos endereços dentro do Conjunto de Endereços de Cliente de VPN de Ponto a Site que especificou na configuração. Os resultados são semelhantes a este exemplo:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Ligar a uma máquina virtual

Estas instruções aplicam-se aos clientes Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Certifique-se de que o pacote de configuração de clientes VPN gerado depois dos endereços IP do servidor DNS foi especificado para a VNet. Se atualizou os endereços IP do servidor DNS, gere e instale um novo pacote de configuração de cliente VPN.

* Utilize “ipconfig” para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador a partir do qual se está a ligar. Se o endereço IP estiver no âmbito do intervalo de endereços da VNet a que se está a ligar, ou no âmbito do intervalo de endereços do seu VPNClientAddressPool, tal trata-se de um espaço de endereços sobreposto. Quando o seu espaço de endereços se sobrepõe desta forma, o tráfego de rede não chega ao Azure e permanece na rede local.

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Para adicionar ou remover certificados de raiz fidedigna

Pode adicionar e remover certificados de raiz fidedigna do Azure. Quando remove um certificado de raiz, os clientes que tenham um certificado gerado a partir dessa raiz não conseguirão autenticar-se e, por conseguinte, não conseguirão estabelecer ligação. Se quiser que um cliente faça a autenticação e estabeleça ligação, terá de instalar um novo certificado de cliente gerado a partir de um certificado de raiz considerado fidedigno (carregado) no Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Para adicionar um certificado de raiz fidedigna

Pode adicionar até 20 ficheiros .cer de certificado de raiz fidedigna ao Azure. Para obter instruções, veja a secção [Carregar um certificado de raiz fidedigna](#uploadfile) neste artigo.

### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado de raiz fidedigna

1. Para remover um certificado de raiz fidedigna, navegue para a página **Configuração ponto a site** do gateway de rede virtual.
2. Na secção **Certificado de raiz** da página, localize o certificado que pretende remover.
3. Selecione a elipse ao lado do certificado e, em seguida, selecione 'Remover'.

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>Revogar um certificado de cliente

Pode revogar certificados de cliente. A lista de revogação de certificado permite-lhe negar seletivamente a conectividade de Ponto a Site, com base em certificados de cliente individuais. Isto é diferente da remoção de um certificado de raiz fidedigna. Se remover um certificado de raiz .cer fidedigno do Azure, revoga o acesso a todos os certificados de cliente gerados/assinados pelo certificado de raiz revogado. A revogação de um certificado de cliente, em vez do certificado de raiz, permite que os outros certificados que foram gerados a partir do certificado de raiz continuem a ser utilizados para autenticação.

A prática comum é utilizar o certificado de raiz para gerir o acesso nos níveis de equipa ou organização e utilizar certificados de cliente revogados para controlo de acesso detalhado dos utilizadores individuais.

### <a name="revoke-a-client-certificate"></a>Revogar um certificado de cliente

Pode revogar um certificado de cliente, ao adicionar o thumbprint à lista de revogação.

1. Obtenha o thumbprint do certificado de cliente. Para obter mais informações, veja [Como obter o Thumbprint de um Certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie as informações para um editor de texto e remova todos os espaços, para que seja uma cadeia contínua.
3. Navegue até à página **Configuração ponto a site** do gateway de rede virtual. Esta é a mesma página que utilizou para [carregar um certificado de raiz fidedigna](#uploadfile).
4. Na secção **Certificados revogados** , introduza um nome amigável para o certificado (não tem de ser o CN do certificado).
5. Copie e cole a cadeia de thumbprint para o campo **Thumbprint** .
6. O thumbprint valida e é adicionado automaticamente à lista de revogação. Verá uma mensagem no ecrã a indicar que a lista está a atualizar. 
7. Depois de concluída a atualização, o certificado já não pode ser utilizado para ligar. Os clientes que se tentarem ligar com este certificado irão receber uma mensagem a indicar que o certificado já não é válido.

## <a name="point-to-site-faq"></a><a name="faq"></a>FAQ ponto-a-local

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Passos seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/). Para compreender melhor o funcionamento em rede e as máquinas virtuais, veja [Descrição geral da rede VM do Azure e Linux](../virtual-machines/linux/azure-vm-network-overview.md).

Para obter informações de resolução de problemas P2S, consulte [Resolução de problemas de ligações ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
