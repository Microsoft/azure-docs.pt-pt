---
title: 'Ligar um computador a uma rede virtual do Azure com o ponto a Site e autenticação de certificados nativa do Azure: Portal do Azure | Documentos da Microsoft'
description: Ligar com segurança os clientes Windows, Mac OS X e Linux para uma rede virtual do Azure com P2S e autoassinados ou emitidos certificados de AC. Este artigo utiliza o portal do Azure.
services: vpn-gateway
author: cherylmc
tags: azure-resource-manager
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/24/2018
ms.author: cherylmc
ms.openlocfilehash: fa406ac2f1e0b89a1161660a49f2a4cb6f6d6c32
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117324"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Configure uma ligação de ponto a Site a uma VNet com a autenticação de certificados nativa do Azure: Portal do Azure

Este artigo ajuda-o a ligar de forma segura clientes individuais com Windows, Linux ou Mac OS X para uma VNet do Azure. As ligações VPN Ponto a Site são úteis quando pretende ligar à VNet a partir de uma localização remota, como em teletrabalho em casa ou numa conferência. Pode também utilizar P2S em vez de uma VPN Site a Site, quando são poucos os clientes que precisam de ligar a uma VNet. As ligações Ponto a Site não precisam de nenhum dispositivo VPN ou endereço IP destinado ao público. A P2S cria a ligação VPN através de SSTP (Secure Socket Tunneling Protocol) ou de IKEv2. Para obter mais informações sobre VPN Ponto a Site, veja [Acerca da VPN Ponto a Site](point-to-site-about.md).

![Ligar um computador a uma VNet do Azure - diagrama da ligação Ponto a Site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)


## <a name="architecture"></a>Arquitetura

As ligações de autenticação de certificados nativos do Azure Ponto a Site utilizam os seguintes itens, que irá configurar neste exercício:

* Um gateway de VPN RouteBased.
* A chave pública (ficheiro .cer) de um certificado de raiz que é carregado para o Azure. Assim que o certificado estiver carregado, é considerado um certificado fidedigno e é utilizado para autenticação.
* Um certificado de cliente que é gerado a partir do certificado de raiz. O certificado de cliente instalado em cada computador cliente que vai ligar à VNet. Este certificado é utilizado para autenticação de cliente.
* Uma configuração de cliente VPN. Os ficheiros de configuração de cliente VPN contêm as informações necessárias para o cliente se ligar à VNet. Os ficheiros configuram o cliente VPN existente que é nativo ao sistema operativo. Cada cliente que estabelece ligação tem de ser configurado com as definições dos ficheiros de configuração.

#### <a name="example"></a>Valores de exemplo

Pode utilizar os valores seguintes para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo:

* **Nome da VNet:** VNet1
* **Espaço de endereços:** 192.168.0.0/16<br>Para este exemplo, utilizamos apenas um espaço de endereços. Pode ter mais do que um espaço de endereços para a sua VNet.
* **Nome da sub-rede:** FrontEnd
* **Intervalo de endereços da sub-rede:** 192.168.1.0/24
* **Subscrição:** Se tiver mais de uma subscrição, certifique-se de que está a utilizar correta.
* **Grupo de recursos:** TestRG
* **Localização:** EUA Leste
* **GatewaySubnet:** 192.168.200.0/24<br>
* **Servidor DNS:** (opcional) endereço IP do servidor DNS que pretende utilizar para a resolução de nomes.
* **Nome do gateway de rede virtual:** VNet1GW
* **Tipo de gateway:** VPN
* **Tipo de VPN:** Baseado na rota
* **Nome do endereço IP público:** VNet1GWpip
* **Tipo de ligação:** Ponto a site
* **Conjunto de endereços de cliente:** 172.16.201.0/24<br>Os clientes VPN que se ligam à VNet através desta ligação Ponto a Site recebem um endereço IP a partir do conjunto de endereços de cliente especificado.

## <a name="createvnet"></a>1. Criar uma rede virtual

Antes de começar, verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial).
[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-p2s-vnet-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>2. Adicionar uma sub-rede do gateway

Antes de ligar a rede virtual a um gateway, primeiro tem de criar a sub-rede do gateway para a rede virtual à qual pretende ligar. Os serviços do gateway utilizam os endereços IP especificados na sub-rede de gateway. Se possível, crie uma sub-rede do gateway com um bloco CIDR de /28 ou /27, para fornecer endereços IP suficientes para contemplar requisitos de configuração futuros adicionais.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-p2s-rm-portal-include.md)]

## <a name="dns"></a>3. Especificar um servidor DNS (opcional)

Depois de criar a rede virtual, pode adicionar o endereço IP de um servidor DNS, para lidar com a resolução de nomes. O servidor DNS é opcional para esta configuração, mas é necessário se pretender a resolução de nomes. A especificação de um valor não cria um novo servidor DNS. O endereço IP do servidor DNS que especificar deve ser um servidor DNS que possa resolver os nomes dos recursos a que se está a ligar. Para este exemplo, foi utilizado um endereço IP privado, mas é provável que este não seja o endereço IP do servidor DNS. Certifique-se de que utiliza os seus próprios valores. O valor que especificar será utilizado pelos recursos que implementa na VNet, não pela ligação P2S ou o cliente VPN.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>4. Criar um gateway de rede virtual

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

>[!NOTE]
>A SKU Básica não suporta a autenticação IKEv2 ou RADIUS. Se estiver a planear de ter um Mac, os clientes se conectam à sua rede virtual, não utilize o SKU básico.
>

## <a name="generatecert"></a>5. Gerar certificados

Os certificados são utilizados pelo Azure para autenticar clientes que se ligam a uma VNet através de uma ligação VPN Ponto a Site. Quando conseguir obter um certificado de raiz, [carregue](#uploadfile) as informações da chave pública do certificado de raiz para o Azure. O certificado de raiz é então considerado "fidedigno" pelo Azure para a ligação através do P2S para a rede virtual. Também pode gerar certificados de cliente a partir do certificado de raiz fidedigna e, em seguida, instalá-los em cada computador do cliente. O certificado de cliente é utilizado para autenticar o cliente quando é iniciada uma ligação à VNet. 

### <a name="getcer"></a>1. Obter o ficheiro .cer para o certificado de raiz

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generateclientcert"></a>2. Gerar um certificado de cliente

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="addresspool"></a>6. Adicionar o conjunto de endereços de cliente

O conjunto de endereços de cliente é um conjunto de endereços IP privados que especificar. Os clientes que se ligam através da VPN Ponto a Site recebem dinamicamente um endereço IP deste intervalo. Utilize um intervalo de endereços IP privados que não se sobreponha à localização no local a partir da qual se ligou ou à VNet à qual se quer ligar.

1. Assim que o gateway da rede virtual for criado, navegue até à secção **Definições** da página do gateway de rede virtual. Na secção **Definições**, clique em **Configuração ponto a site**.

   ![Página Ponto a Site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/gatewayblade.png) 
2. Clique em **Configurar agora** para abrir a página de configuração.

   ![Configurar agora](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configurenow.png)
3. Na página de configuração **Ponto a site**, na caixa **Conjunto de endereços**, adicione o intervalo de endereços IP privados que pretende utilizar. Os clientes VPN recebem dinamicamente um endereço IP do intervalo que especificou. Clique em **Guardar** para validar e guardar a definição.

   ![Conjunto de endereços de cliente](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/addresspool.png)

   >[!NOTE]
   >Se não vir o Tipo de túnel ou o Tipo de autenticação no portal desta página, o gateway está a utilizar a SKU Básica. A SKU Básica não suporta a autenticação IKEv2 ou RADIUS.
   >

## <a name="tunneltype"></a>7. Configurar o tipo de túnel

Pode selecionar o tipo de túnel. As duas opções de túnel são SSTP e IKEv2. O cliente strongSwan no Android e Linux e o cliente VPN IKEv2 nativo em dispositivos iOS e OSX utilizarão apenas o túnel IKEv2 para estabelecer a ligação. Os clientes Windows, primeiro, experimentam o IKEv2 e, se não conseguirem estabelecer a ligação, voltam ao SSTP. Pode optar por ativar uma ou ambas as opções. Selecione as caixas de verificação que a sua solução requer.

![Tipo de túnel](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/tunneltype.png)

## <a name="authenticationtype"></a>8. Configurar tipo de autenticação

Selecione **Certificado do Azure**.

  ![Tipo de túnel](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authenticationtype.png)

## <a name="uploadfile"></a>9. Carregar os dados do certificado público do certificado de raiz

Pode carregar certificados de raiz fidedigna adicionais até um total de 20. Assim que os dados do certificado público forem carregados, o Azure pode utilizá-lo para autenticar clientes que tenham instalado um certificado de cliente gerado a partir do certificado de raiz fidedigna. Pode carregar as informações da chave pública do certificado de raiz para o Azure.

1. Os certificados são adicionados na página **Configuração ponto a site** na secção **Certificado de raiz**.
2. Certifique-se de que exportou o certificado de raiz como um ficheiro X.509 codificado com Base-64 (.cer). Tem de exportar o certificado neste formato para poder abrir o certificado com o editor de texto.
3. Abra o certificado com um editor de texto, como o Bloco de Notas. Quando copiar os dados de certificado, certifique-se de que copia o texto como uma linha contínua sem símbolos de retorno ou avanços de linha. Poderá ter de modificar a sua vista no editor de texto para "Mostrar Símbolo/Mostrar todos os carateres" para ver os símbolos de retorno ou avanços de linha. Copie apenas a secção seguinte como uma linha contínua:

   ![Dados de certificado](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png)
4. Cole os dados de certificado no campo **Dados de Certificado Público**. **Nomeie** o certificado e, em seguida, clique em **Guardar**. Pode adicionar até 20 certificados de raiz fidedigna.

   ![Carregamento de certificados](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png)
5. Clique em **Guardar** no início da página para guardar todas as definições de configuração.

   ![Guardar](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png)

## <a name="installclientcert"></a>10. Instalar um certificado de cliente exportado

Se quiser criar uma ligação P2S a partir de um computador cliente sem ser o utilizado para gerar os certificados de cliente, tem de instalar um certificado de cliente. Ao instalar um certificado de cliente, vai precisar da palavra-passe que foi criada quando o certificado de cliente foi exportado.

Certifique-se de que o certificado de cliente foi exportado como um ficheiro. pfx, juntamente com a cadeia de certificados inteira (que é a predefinição). Caso contrário, as informações do certificado de raiz não estão presentes no computador do cliente e o cliente não conseguirá autenticar corretamente.

Para obter os passos de instalação, veja [Instalar um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="clientconfig"></a>11. Gerar e instalar o pacote de configuração do cliente VPN

Os ficheiros de configuração de cliente VPN contêm as definições para configurar dispositivos para ligar a uma VNet através de uma ligação P2S. Para obter instruções sobre como gerar e instalar ficheiros de configuração de cliente VPN, veja [Criar e instalar ficheiros de configuração de cliente VPN para configurações P2S da autenticação de certificados nativa do Azure](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="connect"></a>12. Ligar ao Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Para ligar a partir de um cliente VPN do Windows

>[!NOTE]
>Tem de ter direitos de Administrador no computador cliente Windows a partir do qual está a ligar.
>
>

1. Para se ligar à sua VNet, no computador cliente, navegue até às ligações VPN e localize a ligação VPN que criou. Tem o mesmo nome da sua rede virtual. Clique em **Ligar**. Poderá aparecer uma mensagem pop-up que se refere à utilização do certificado. Clique em **Continuar** para utilizar privilégios elevados.

2. Na página de estado da **Ligação**, clique em **Ligar** para iniciar a ligação. Se vir um ecrã **Selecionar Certificado**, verifique se o certificado de cliente apresentado é aquele que pretende utilizar para se ligar. Se não for, utilize a seta para baixo para selecionar o certificado correto e clique em **OK**.

   ![O cliente VPN liga-se ao Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)
3. A ligação é estabelecida.

   ![Ligação estabelecida](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)

#### <a name="troubleshoot-windows-p2s-connections"></a>Resolução de problemas com ligações P2S do Windows

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Para ligar a partir de um cliente VPN do Mac

Na caixa de diálogo Rede, localize o perfil de cliente que quer utilizar, especifique as definições do [VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac) e, em seguida, clique em **Ligar**.

Verifique [instale - Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac) para obter instruções detalhadas. Se estiver a ter problemas em ligar, certifique-se de que o gateway de rede virtual não está a utilizar um SKU básico. SKU básico não é suportado para os clientes Mac.

  ![Ligação do Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>Para verificar a ligação

Estas instruções aplicam-se aos clientes Windows.

1. Para verificar se a ligação VPN está ativa, abra uma linha de comandos elevada e execute *ipconfig/all*.
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

## <a name="connectVM"></a>Ligar a uma máquina virtual

Estas instruções aplicam-se aos clientes Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="add"></a>Para adicionar ou remover certificados de raiz fidedigna

Pode adicionar e remover certificados de raiz fidedigna do Azure. Quando remove um certificado de raiz, os clientes que tenham um certificado gerado a partir dessa raiz não conseguirão autenticar-se e, por conseguinte, não conseguirão estabelecer ligação. Se quiser que um cliente faça a autenticação e estabeleça ligação, terá de instalar um novo certificado de cliente gerado a partir de um certificado de raiz considerado fidedigno (carregado) no Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Para adicionar um certificado de raiz fidedigna

Pode adicionar até 20 ficheiros .cer de certificado de raiz fidedigna ao Azure. Para obter instruções, veja a secção [Carregar um certificado de raiz fidedigna](#uploadfile) neste artigo.

### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado de raiz fidedigna

1. Para remover um certificado de raiz fidedigna, navegue para a página **Configuração ponto a site** do gateway de rede virtual.
2. Na secção **Certificado de raiz** da página, localize o certificado que pretende remover.
3. Clique nas reticências junto ao certificado e, em seguida, clique em “Remover”.

## <a name="revokeclient"></a>Para revogar um certificado de cliente

Pode revogar certificados de cliente. A lista de revogação de certificado permite-lhe negar seletivamente a conectividade de Ponto a Site, com base em certificados de cliente individuais. Isto é diferente da remoção de um certificado de raiz fidedigna. Se remover um certificado de raiz .cer fidedigno do Azure, revoga o acesso a todos os certificados de cliente gerados/assinados pelo certificado de raiz revogado. A revogação de um certificado de cliente, em vez do certificado de raiz, permite que os outros certificados que foram gerados a partir do certificado de raiz continuem a ser utilizados para autenticação.

A prática comum é utilizar o certificado de raiz para gerir o acesso nos níveis de equipa ou organização e utilizar certificados de cliente revogados para controlo de acesso detalhado dos utilizadores individuais.

### <a name="revoke-a-client-certificate"></a>Revogar um certificado de cliente

Pode revogar um certificado de cliente, ao adicionar o thumbprint à lista de revogação.

1. Obtenha o thumbprint do certificado de cliente. Para obter mais informações, veja [Como obter o Thumbprint de um Certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie as informações para um editor de texto e remova todos os espaços, para que seja uma cadeia contínua.
3. Navegue até à página **Configuração ponto a site** do gateway de rede virtual. Esta é a mesma página que utilizou para [carregar um certificado de raiz fidedigna](#uploadfile).
4. Na secção **Certificados revogados**, introduza um nome amigável para o certificado (não tem de ser o CN do certificado).
5. Copie e cole a cadeia de thumbprint para o campo **Thumbprint**.
6. O thumbprint valida e é adicionado automaticamente à lista de revogação. Verá uma mensagem no ecrã a indicar que a lista está a atualizar. 
7. Depois de concluída a atualização, o certificado já não pode ser utilizado para ligar. Os clientes que se tentarem ligar com este certificado irão receber uma mensagem a indicar que o certificado já não é válido.

## <a name="faq"></a>FAQ Ponto a Site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Passos Seguintes
Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/). Para compreender melhor o funcionamento em rede e as máquinas virtuais, veja [Descrição geral da rede VM do Azure e Linux](../virtual-machines/linux/azure-vm-network-overview.md).

Para obter informações de resolução de problemas P2S, consulte [Resolução de problemas de ligações ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
