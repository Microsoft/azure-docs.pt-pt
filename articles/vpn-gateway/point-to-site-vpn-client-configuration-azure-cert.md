---
title: 'Criar & instalar ficheiros de configuração do cliente P2S VPN: autenticação de certificados'
titleSuffix: Azure VPN Gateway
description: Crie e instale ficheiros de configuração de clientes Windows, Linux, Linux (strongSwan) e macOS X VPN para autenticação de certificado P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: 517b006b013bddbe4e7e7a3d44be74dfa36cc154
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042606"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Criar e instalar os ficheiros de configuração do cliente VPN para configurações P2S de autenticação de certificado nativa do Azure

Os ficheiros de configuração do cliente VPN estão contidos num ficheiro zip. Os ficheiros de configuração fornecem as definições necessárias para que um cliente nativo do Windows, Mac IKEv2 VPN ou Linux se conecte a uma rede virtual sobre ligações Point-to-Site que utilizam a autenticação de certificados Azure nativos.

Os ficheiros de configuração do cliente são específicos da configuração VPN para a rede virtual. Se houver alterações na configuração VPN ponto-a-local depois de gerar os ficheiros de configuração do cliente VPN, como o tipo de protocolo VPN ou o tipo de autenticação, certifique-se de gerar novos ficheiros de configuração do cliente VPN para os seus dispositivos de utilizador. 

* Para obter mais informações sobre ligações Ponto a Site, veja [Acerca da VPN Ponto a Site](point-to-site-about.md).
* Para obter instruções openVPN, consulte [Configure OpenVPN para clientes P2S](vpn-gateway-howto-openvpn.md) e [Configure OpenVPN](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Gerar ficheiros de configuração de clientes VPN

Antes de começar, certifique-se de que todos os utilizadores de ligação têm um certificado válido instalado no dispositivo do utilizador. Para obter mais informações sobre a instalação de um certificado de cliente, consulte [instalar um certificado de cliente.](point-to-site-how-to-vpn-client-install-azure-cert.md)

Pode gerar ficheiros de configuração do cliente utilizando o PowerShell ou utilizando o portal Azure. Qualquer um dos métodos devolve o mesmo ficheiro zip. Desaperte o ficheiro para ver as seguintes pastas:

  * **WindowsAmd64** e **WindowsX86** , que contêm os pacotes de instaladores Windows 32 bits e 64 bits, respectivamente. O pacote de instaladores **WindowsAmd64** destina-se a todos os clientes Windows suportados com 64 bits, e não apenas a Amd.
  * **Genérico** , que contém informações gerais usadas para criar a sua própria configuração de cliente VPN. A pasta Genérica é fornecida se o IKEv2 ou o SSTP+IKEv2 estiverem configurados no gateway. Se apenas o SSTP estiver configurado, a pasta Genérica não está presente.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Gerar ficheiros utilizando o portal Azure

1. No portal Azure, navegue para o portal de rede virtual para a rede virtual a que pretende ligar.
2. Na página de gateway de rede virtual, clique na **configuração ponto-a-local** .

   ![baixar portal do cliente](./media/point-to-site-vpn-client-configuration-azure-cert/client-configuration-portal.png)
3. No topo da página de configuração ponto-a-local, clique em **Baixar o cliente VPN** . Leva alguns minutos para o pacote de configuração do cliente gerar.
4. O seu navegador indica que está disponível um ficheiro zip de configuração do cliente. Tem o mesmo nome que o seu portal. Desaperte o ficheiro para visualizar as pastas.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Gerar ficheiros usando o PowerShell


1. Ao gerar ficheiros de configuração de clientes VPN, o valor para '-AutenticaçãoMethod' é 'EapTls'. Gerei os ficheiros de configuração do cliente VPN utilizando o seguinte comando:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Copie o URL para o seu navegador para descarregar o ficheiro zip e, em seguida, desaperte o ficheiro para visualizar as pastas.

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 Tem de configurar manualmente o cliente VPN IKEv2 nativo em cada Mac que irá ligar ao Azure. O Azure não fornece o ficheiro mobileconfig para a autenticação de certificados nativa do Azure. O **Genérico** contém toda a informação que precisa para a configuração. Se não vir a pasta Generic na sua transferência, é provável que o IKEv2 não tenha sido selecionado como um tipo de túnel. Note que o gateway VPN Basic SKU não suporta iKEv2. Depois de selecionar IKEv2, gere novamente o ficheiro zip para obter a pasta Generic.<br>A pasta Generic contém os seguintes ficheiros:

* **VpnSettings.xml** , que contém configurações importantes como endereço de servidor e tipo de túnel. 
* **VpnServerRoot.cer,** que contém o certificado de raiz necessário para validar o Gateway Azure VPN durante a configuração da ligação P2S.

Utilize os seguintes passos para configurar o cliente VPN nativo no Mac para autenticação de certificado. Você tem que completar estes passos em cada Mac que irá ligar-se a Azure:

1. Importe o certificado **raiz VpnServerRoot** para o seu Mac. Isto pode ser feito copiando o ficheiro para o seu Mac e clicando duas vezes nele. Clique **em Adicionar** à importação.

   ![adicionar certificado](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Clicar duas vezes no certificado pode não apresentar o diálogo **Add,** mas o certificado está instalado na loja correta. Pode consultar o certificado no teclado de login na categoria de certificados.
    >
  
2. Verifique se instalou um certificado de cliente que foi emitido pelo certificado raiz que carregou para a Azure quando o configurar definições P2S. Isto é diferente do VPNServerRoot que instalou no passo anterior. O certificado de cliente é utilizado para a autenticação e é necessário. Para obter mais informações sobre a geração de certificados, consulte ['Gerar Certificados'.](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert) Para obter informações sobre como instalar um certificado de cliente, consulte [instalar um certificado de cliente.](point-to-site-how-to-vpn-client-install-azure-cert.md)
3. Abra o diálogo **de Rede** em **Preferências de Rede** e clique em **'+'** para criar um novo perfil de ligação ao cliente VPN para uma ligação P2S à rede virtual Azure.

   O valor **da Interface** é 'VPN' e o valor do **tipo VPN** é 'IKEv2'. Especifique um nome para o perfil no campo **Nome de Serviço** e, em seguida, clique em **Criar** para criar o perfil de ligação ao cliente VPN.

   ![O screenshot mostra a janela 'Rede' com a opção de selecionar uma interface, selecionar o tipo VPN e introduzir um nome de serviço.](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. Na pasta **Genérico,** a partir do ficheiro **VpnSettings.xml,** copie o valor da etiqueta **VpnServer.** Cole este valor nos campos **de endereço do servidor** e **iD remoto** do perfil.

   ![informação do servidor](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Clique em **Definições de Autenticação** e selecione **Certificado** . Para **Catalina,** clique em **Nenhum** e, em seguida, **certificado**

   ![definições de autenticação](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)

   * Para Catalina, selecione **Nenhum** e, em seguida, **Certificado** . **Selecione** o certificado correto:
   
   ![A screenshot mostra a janela 'Rede' sem nenhum selecionado para Definições de Autenticação e Certificado selecionados.](./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png)

6. Clique **em Selecionar...** para escolher o certificado de cliente que pretende utilizar para autenticação. Este é o certificado que instalou no Passo 2.

   ![A screenshot mostra a janela 'Rede' com Definições de Autenticação, onde pode selecionar um certificado.](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Escolha Uma Identidade** apresenta uma lista de certificados para escolher. Selecione o certificado adequado e, em seguida, clique em **Continuar** .

   ![A screenshot mostra a caixa de diálogo Escolha uma identidade onde pode selecionar o certificado adequado.](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. No campo **de Identificação Local,** especifique o nome do certificado (a partir do passo 6). Neste exemplo, é "ikev2Client.com". Em seguida, clique **em Aplicar** o botão para guardar as alterações.

   ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. No diálogo **'Rede',** clique **em Aplicar** para guardar todas as alterações. Em seguida, clique em **Ligar** para iniciar a ligação P2S à rede virtual Azure.

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>Instalar strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Gerar certificados

Se ainda não tiver gerado certificados, utilize os seguintes passos:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Instalar e configurar

As seguintes instruções foram criadas em Ubuntu 18.0.4. Ubuntu 16.0.10 não suporta o strongSwan GUI. Se quiser utilizar ubuntu 16.0.10, terá de utilizar a [linha de comando](#linuxinstallcli). Os exemplos abaixo podem não corresponder aos ecrãs que vê, dependendo da sua versão de Linux e strongSwan.

1. Abra o **Terminal** para instalar **o strongSwan** e o seu Gestor de Rede executando o comando no exemplo.

   ```
   sudo apt install network-manager-strongswan
   ```
2. Selecione **Definições** e, em seguida, selecione **Rede** .

   ![editar ligações](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Clique no **+** botão para criar uma nova ligação.

   ![adicionar uma ligação](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Selecione **IPsec/IKEv2 (strongSwan)** do menu e clique duas vezes. Pode nomear a sua ligação neste passo.

   ![escolher um tipo de ligação](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Abra o ficheiro **VpnSettings.xml** da pasta **Genérica** contida nos ficheiros de configuração do cliente descarregados. Encontre a etiqueta chamada **VpnServer** e copie o nome, começando por 'azuregateway' e terminando com '.cloudapp.net'.

   ![nome de cópia](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Cole este nome no campo **Address** da sua nova ligação VPN na secção **Gateway.** Em seguida, selecione o ícone da pasta no final do campo **Certificate,** navegue na pasta **Genérico** e selecione o ficheiro **VpnServerRoot.**
7. Na secção **Cliente** da ligação, para **autenticação,** selecione **Certificado/tecla privada** . Para **Certificado** e **Chave Privada,** escolha o certificado e a chave privada que foram criados anteriormente. Em **Opções** , **selecione Solicite um endereço IP interno** . Em seguida, clique em **Adicionar** .

   ![solicitar um endereço IP interior](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. Ligue a **ligação** .

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>Instalar strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Gerar certificados

Se ainda não tiver gerado certificados, utilize os seguintes passos:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Instalar e configurar

1. Descarregue o pacote VPNClient do portal Azure.
2. Extraia o Arquivo.
3. A partir da pasta **Genérica,** copie ou mova o VpnServerRoot.cer para /etc/ipsec.d/cacerts.
4. Copiar ou mover o cliente cp.p12 para /etc/ipsec.d/private/. Este ficheiro é certificado de cliente da Azure VPN Gateway.
5. Abra VpnSettings.xml arquivo e copie o `<VpnServer>` valor. Usará este valor no próximo passo.
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
6. Adicione o seguinte a */etc/ipsec.secrets* .

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

Para resolver problemas nas ligações P2S, consulte os seguintes artigos:

  * [Resolução de problemas Ligações ponto-a-local do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Ligações VPN de resolução de problemas de clientes macOS X VPN](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
