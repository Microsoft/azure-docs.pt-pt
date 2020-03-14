---
title: 'Criar e instalar ficheiros de configuração do cliente P2S VPN: autenticação de certificado'
titleSuffix: Azure VPN Gateway
description: Crie e instale ficheiros de configuração de clientes Windows, Linux, Linux (strongSwan) e Mac OS X VPN para autenticação de certificado P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/04/2020
ms.author: cherylmc
ms.openlocfilehash: d15efee635e131d658cd650b7f80eb9e670a0dea
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279419"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Criar e instalar ficheiros de configuração de cliente VPN para configurações de autenticação de certificado saqueado nativo Do mato P2S

Os ficheiros de configuração do cliente VPN estão contidos num ficheiro zip. Os ficheiros de configuração fornecem as definições necessárias para um windows nativo, clientes Mac IKEv2 VPN ou Linux para se conectarem a uma rede virtual sobre ligações Point-to-Site que utilizam a autenticação de certificado sacerdote nativo Azure.

Os ficheiros de configuração do cliente são específicos da configuração VPN para a rede virtual. Se houver alterações na configuração VPN ponto-a-site depois de gerar os ficheiros de configuração do cliente VPN, como o tipo de protocolo VPN ou o tipo de autenticação, certifique-se de que gera novos ficheiros de configuração do cliente VPN para os seus dispositivos de utilizador. 

* Para obter mais informações sobre ligações Ponto a Site, veja [Acerca da VPN Ponto a Site](point-to-site-about.md).
* Para instruções OpenVPN, consulte [Configure OpenVPN para clientes P2S](vpn-gateway-howto-openvpn.md) e [Configure OpenVPN](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate"></a>Gerar ficheiros de configuração de cliente VPN

Antes de começar, certifique-se de que todos os utilizadores de ligação têm um certificado válido instalado no dispositivo do utilizador. Para obter mais informações sobre a instalação de um certificado de cliente, consulte [Instalar um certificado de cliente.](point-to-site-how-to-vpn-client-install-azure-cert.md)

Pode gerar ficheiros de configuração do cliente utilizando o PowerShell ou utilizando o portal Azure. Qualquer um dos métodos devolve o mesmo ficheiro zip. Desapertar o ficheiro para visualizar as seguintes pastas:

  * **WindowsAmd64** e **WindowsX86**, que contêm os pacotes de instaladores windows 32 e 64 bits, respectivamente. O pacote de instalação **WindowsAmd64** destina-se a todos os clientes Windows suportados de 64 bits, e não apenas amd.
  * **Genérico**, que contém informações gerais usadas para criar a sua própria configuração de cliente VPN. A pasta Genérica é fornecida se IKEv2 ou SSTP+IKEv2 foram configurados no portal. Se apenas o SSTP estiver configurado, então a pasta Genérica não está presente.

### <a name="zipportal"></a>Gerar ficheiros usando o portal Azure

1. No portal Azure, navegue para a porta de entrada de rede virtual para a rede virtual a que pretende ligar.
2. Na página de gateway da rede virtual, clique na **configuração ponto-a-site**.

   ![descarregar portal de cliente](./media/point-to-site-vpn-client-configuration-azure-cert/client-configuration-portal.png)
3. No topo da página de configuração Ponto-a-site, clique em Baixar o **cliente VPN**. Leva alguns minutos para o pacote de configuração do cliente gerar.
4. O seu navegador indica que está disponível um ficheiro zip de configuração do cliente. Chama-se o mesmo nome que o seu portal. Desinsero o ficheiro para ver as pastas.

### <a name="zipps"></a>Gerar ficheiros usando powerShell


1. Ao gerar ficheiros de configuração de clientes VPN, o valor para 'AutenticaçãoMétodo' é 'EapTls'. Gere os ficheiros de configuração do cliente VPN utilizando o seguinte comando:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Copie o URL para o seu navegador para descarregar o ficheiro zip e, em seguida, desaperte o ficheiro para visualizar as pastas.

## <a name="installwin"></a>Janelas

Pode utilizar o mesmo pacote de configuração de cliente VPN em cada computador cliente do Windows, desde que a versão corresponda à arquitetura para o cliente. Para a lista de sistemas operativos do cliente suportados, consulte a secção Ponto-a-Local do [VPN Gateway FAQ](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Tem de ter direitos de Administrador no computador cliente do Windows a partir do qual pretende ligar.
>
>

Utilize os seguintes passos para configurar o cliente VPN nativo do Windows para autenticação de certificado:

1. Selecione os ficheiros de configuração do cliente VPN que correspondem à arquitetura do computador Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador "VpnClientSetupAmd64". Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador "VpnClientSetupX86". 
2. Faça duplo clique no pacote para instalá-lo. Se vir um pop-up SmartScreen, clique em **Mais informações** e, em seguida, em **Executar mesmo assim**.
3. No computador cliente, navegue para **Definições de Rede** e clique em **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga. 
4. Antes de tentar ligar, certifique-se de que instalou um certificado de cliente no computador cliente. Se utilizar o tipo de autenticação de certificados nativa do Azure, é preciso um certificado de cliente para a autenticação. Para obter mais informações sobre a geração de certificados, consulte [Gerar Certificados](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Para obter informações sobre como instalar um certificado de cliente, consulte [Instalar um certificado de cliente.](point-to-site-how-to-vpn-client-install-azure-cert.md)

## <a name="installmac"></a>Mac (OS X)

 Tem de configurar manualmente o cliente VPN IKEv2 nativo em cada Mac que irá ligar ao Azure. O Azure não fornece o ficheiro mobileconfig para a autenticação de certificados nativa do Azure. O **Genérico** contém toda a informação que precisa para a configuração. Se não vir a pasta Generic na sua transferência, é provável que o IKEv2 não tenha sido selecionado como um tipo de túnel. Note que o Gateway Basic SKU vpn não suporta o IKEv2. Depois de selecionar IKEv2, gere novamente o ficheiro zip para obter a pasta Generic.<br>A pasta Generic contém os seguintes ficheiros:

* **VpnSettings.xml**, que contém definições importantes como endereço do servidor e tipo de túnel. 
* **VpnServerRoot.cer,** que contém o certificado de raiz necessário para validar o Gateway VPN Azure durante a configuração da ligação P2S.

Utilize os seguintes passos para configurar o cliente VPN nativo no Mac para autenticação de certificado. Tens de completar estes passos em cada Mac que se ligará ao Azure:

1. Importe o certificado raiz **VpnServerRoot** para o seu Mac. Isto pode ser feito copiando o ficheiro para o seu Mac e clicando duas vezes nele. Clique em **Adicionar** a importar.

   ![adicionar certificado](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Clicar duas vezes no certificado pode não apresentar o diálogo **Add,** mas o certificado está instalado na loja correta. Pode consultar o certificado no porta-chaves de login na categoria de certificados.
    >
  
2. Verifique se instalou um certificado de cliente que foi emitido pelo certificado de raiz que carregou para o Azure quando configurar as definições do P2S. Isto é diferente do VPNServerRoot que instalou no passo anterior. O certificado de cliente é utilizado para autenticação e é necessário. Para obter mais informações sobre a geração de certificados, consulte [Gerar Certificados](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Para obter informações sobre como instalar um certificado de cliente, consulte [Instalar um certificado de cliente.](point-to-site-how-to-vpn-client-install-azure-cert.md)
3. Abra o diálogo **da Rede** em **preferências** de rede e clique **em '+'** para criar um novo perfil de ligação ao cliente VPN para uma ligação P2S à rede virtual Azure.

   O valor **da Interface** é 'VPN' e o valor do **tipo VPN** é 'IKEv2'. Especifique um nome para o perfil no campo Nome de **Serviço** e, em seguida, clique em **Criar** para criar o perfil de ligação ao cliente VPN.

   ![rede](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. Na pasta **Genérica,** a partir do ficheiro **VpnSettings.xml,** copie o valor da etiqueta **VpnServer.** Colar este valor nos campos de **Endereço de Servidor** e ID **remoto** do perfil.

   ![informação do servidor](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Clique em **Definições de Autenticação** e selecione **Certificado**. Para **Catalina,** clique **em Nenhum** e, em seguida, **certificado**

   ![definições de autenticação](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)

   * Para Catalina, selecione **Nenhum** e, em seguida, **Certificado**. **Selecione** o certificado correto:
   
   ![catalina](./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png)

6. Clique em **Selecionar...** para escolher o certificado de cliente que pretende utilizar para autenticação. Este é o certificado que instalou no Passo 2.

   ![certificado](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Escolha Uma Identidade** apresenta uma lista de certificados para escolher. Selecione o certificado adequado e, em seguida, clique em **Continuar**.

   ![identidade](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. No campo **de identificação local,** especifique o nome do certificado (a partir do passo 6). Neste exemplo, é "ikev2Client.com". Em seguida, clique no botão **Aplicar** para guardar as alterações.

   ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. No diálogo da **Rede,** clique **em Aplicar** para guardar todas as alterações. Em seguida, clique em **Ligar** para iniciar a ligação P2S à rede virtual Azure.

## <a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="installstrongswan"></a>Instale o strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="genlinuxcerts"></a>Gerar certificados

Se ainda não tiver gerado certificados, utilize os seguintes passos:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install"></a>Instalar e configurar

As seguintes instruções foram criadas em Ubuntu 18.0.4. Ubuntu 16.0.10 não suporta forte Swan GUI. Se quiser utilizar ubuntu 16.0.10, terá de utilizar a linha de [comando](#linuxinstallcli). Os exemplos abaixo podem não corresponder aos ecrãs que vê, dependendo da sua versão de Linux e strongSwan.

1. Abra o **Terminal** para instalar **o StrongSwan** e o seu Gestor de Rede executando o comando no exemplo.

   ```
   sudo apt install network-manager-strongswan
   ```
2. Selecione **Definições,** então selecione **A Rede**.

   ![editar ligações](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Clique no botão **+** para criar uma nova ligação.

   ![adicionar uma ligação](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Selecione **IPsec/IKEv2 (strongSwan)** a partir do menu e clique duas vezes. Pode nomear a sua ligação neste passo.

   ![escolher um tipo de ligação](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Abra o ficheiro **VpnSettings.xml** da pasta **Genérica** contida nos ficheiros de configuração do cliente descarregados. Encontre a etiqueta chamada **VpnServer** e copie o nome, começando com 'azuregateway' e terminando com '.cloudapp.net'.

   ![nome da cópia](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Colá-lo no campo **Address** da sua nova ligação VPN na secção **Gateway.** Em seguida, selecione o ícone da pasta na extremidade do campo **Certificado,** navegue para a pasta **Genérica** e selecione o ficheiro **VpnServerRoot.**
7. Na secção **Cliente** da ligação, para **autenticação,** selecione **Certificado/chave privada**. Para **certificado** e **chave privada,** escolha o certificado e a chave privada que foram criadas anteriormente. Em **Opções,** selecione **Solicitar um endereço IP interior**. Em seguida, clique em **Adicionar**.

   ![solicitar um endereço IP interior](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. Ligue a **ligação**.

## <a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>Instale o strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Gerar certificados

Se ainda não tiver gerado certificados, utilize os seguintes passos:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Instalar e configurar

1. Descarregue o pacote VPNClient do portal Azure.
2. Extrai o Arquivo.
3. Da pasta **Genérica,** copie ou mova o VpnServerRoot.cer para /etc/ipsec.d/cacerts.
4. Copiar ou mover cliente cp.p12 para /etc/ipsec.d/private/. Este ficheiro é certificado de cliente para Azure VPN Gateway.
5. Abra o ficheiro VpnSettings.xml e copie o valor `<VpnServer>`. Usará este valor no próximo passo.
6. Ajuste os valores no exemplo abaixo e, em seguida, adicione o exemplo à configuração /etc/ipsec.conf.
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
6. Adicione o seguinte a */etc/ipsec.secrets*.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Execute os seguintes comandos:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Passos seguintes

Volte ao artigo para completar a [sua configuração P2S](vpn-gateway-howto-point-to-site-rm-ps.md).

Para resolver as ligações P2S, consulte os seguintes artigos:

  * [Resolução de problemas Ligações ponto-a-local do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Ligações VPN de suposições problemáticas dos clientes Mac OS X VPN](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
