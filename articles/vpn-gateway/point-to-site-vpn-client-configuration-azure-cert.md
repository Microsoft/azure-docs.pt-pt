---
title: 'Criar e instalar arquivos de configuração de cliente VPN P2S para autenticação de certificado do Azure: Azure'
description: Crie e instale arquivos de configuração de cliente de VPN Windows, Linux, Linux (strongSwan) e Mac OS X para autenticação de certificado P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/13/2019
ms.author: cherylmc
ms.openlocfilehash: 07d48ed195f74c9aef5d34de1925ab8fc2a3ae21
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899900"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Criar e instalar arquivos de configuração de cliente VPN para configurações nativas de P2S de autenticação de certificado do Azure

Os arquivos de configuração de cliente VPN estão contidos em um arquivo zip. Os arquivos de configuração fornecem as configurações necessárias para que os clientes nativos do Windows, Mac IKEv2 VPN ou Linux se conectem a uma VNet em conexões ponto a site que usam a autenticação de certificado nativa do Azure.

Os arquivos de configuração do cliente são específicos para a configuração de VPN para a VNet. Se houver alterações na configuração de VPN ponto a site depois de gerar os arquivos de configuração de cliente VPN, como o tipo de protocolo VPN ou o tipo de autenticação, certifique-se de gerar novos arquivos de configuração de cliente VPN para seus dispositivos de usuário. 

* Para obter mais informações sobre ligações Ponto a Site, veja [Acerca da VPN Ponto a Site](point-to-site-about.md).
* Para obter instruções de OpenVPN, consulte [Configurar OpenVPN para P2S](vpn-gateway-howto-openvpn.md) e [configurar clientes OpenVPN](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate"></a>Gerar ficheiros de configuração de cliente VPN

Antes de começar, certifique-se de que todos os usuários conectados tenham um certificado válido instalado no dispositivo do usuário. Para obter mais informações sobre como instalar um certificado de cliente, consulte [instalar um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

Você pode gerar arquivos de configuração do cliente usando o PowerShell ou usando o portal do Azure. O método retorna o mesmo arquivo zip. Descompacte o arquivo para exibir as seguintes pastas:

  * **WindowsAmd64** e **WindowsX86**, que contêm os pacotes do instalador do Windows 32 bits e 64 bits, respectivamente. O pacote do instalador do **WindowsAmd64** é para todos os clientes do Windows de 64 bits com suporte, não apenas para o AMD.
  * **Genérico**, que contém informações gerais usadas para criar sua própria configuração de cliente VPN. A pasta genérica será fornecida se IKEv2 ou SSTP + IKEv2 tiver sido configurado no gateway. Se apenas o SSTP estiver configurado, a pasta genérica não estará presente.

### <a name="zipportal"></a>Gerar arquivos usando o portal do Azure

1. No portal do Azure, navegue até o gateway de rede virtual para a rede virtual à qual você deseja se conectar.
2. Na página gateway de rede virtual, clique em **configuração ponto a site**.
3. Na parte superior da página de configuração ponto a site, clique em **baixar cliente VPN**. Leva alguns minutos para que o pacote de configuração do cliente seja gerado.
4. Seu navegador indica que um arquivo zip de configuração do cliente está disponível. Ele tem o mesmo nome que o seu gateway. Descompacte o arquivo para exibir as pastas.

### <a name="zipps"></a>Gerar arquivos usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Ao gerar arquivos de configuração de cliente VPN, o valor de '-AuthenticationMethod ' é ' EapTls '. Gere os arquivos de configuração de cliente VPN usando o seguinte comando:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Copie a URL para o navegador para baixar o arquivo zip e descompacte o arquivo para exibir as pastas.

## <a name="installwin"></a>Windows

Você pode usar o mesmo pacote de configuração de cliente VPN em cada computador cliente com Windows, desde que a versão corresponda à arquitetura do cliente. Para obter a lista de sistemas operacionais cliente com suporte, consulte a seção ponto a site das [perguntas frequentes sobre o gateway de VPN](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Você deve ter direitos de administrador no computador cliente do Windows do qual deseja se conectar.
>
>

Use as etapas a seguir para configurar o cliente VPN do Windows nativo para autenticação de certificado:

1. Selecione os ficheiros de configuração do cliente VPN que correspondem à arquitetura do computador Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador "VpnClientSetupAmd64". Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador "VpnClientSetupX86". 
2. Faça duplo clique no pacote para instalá-lo. Se vir um pop-up SmartScreen, clique em **Mais informações** e, em seguida, em **Executar mesmo assim**.
3. No computador cliente, navegue para **Definições de Rede** e clique em **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga. 
4. Antes de tentar ligar, certifique-se de que instalou um certificado de cliente no computador cliente. Se utilizar o tipo de autenticação de certificados nativa do Azure, é preciso um certificado de cliente para a autenticação. Para obter mais informações sobre como gerar certificados, consulte [Generate Certificates](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Para obter informações sobre como instalar um certificado de cliente, consulte [instalar um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>Mac (OS X)

 Tem de configurar manualmente o cliente VPN IKEv2 nativo em cada Mac que irá ligar ao Azure. O Azure não fornece o ficheiro mobileconfig para a autenticação de certificados nativa do Azure. O **genérico** contém todas as informações de que você precisa para a configuração. Se não vir a pasta Generic na sua transferência, é provável que o IKEv2 não tenha sido selecionado como um tipo de túnel. Observe que o SKU básico do gateway de VPN não oferece suporte a IKEv2. Depois de selecionar IKEv2, gere novamente o ficheiro zip para obter a pasta Generic.<br>A pasta Generic contém os seguintes ficheiros:

* **VpnSettings. xml**, que contém configurações importantes, como o endereço do servidor e o tipo de túnel. 
* **VpnServerRoot. cer**, que contém o certificado raiz necessário para validar o gateway de VPN do Azure durante a configuração de conexão do P2S.

Use as etapas a seguir para configurar o cliente VPN nativo no Mac para autenticação de certificado. Você precisa concluir estas etapas em cada Mac que se conectará ao Azure:

1. Importe o certificado raiz do **VpnServerRoot** para seu Mac. Isso pode ser feito copiando o arquivo para o Mac e clicando duas vezes nele. Clique em **Adicionar** para importar.

   ![Adicionar certificado](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Clicar duas vezes no certificado pode não exibir a caixa de diálogo **Adicionar** , mas o certificado é instalado no repositório correto. Você pode verificar o certificado no conjunto de chaves de logon na categoria certificados.
    >
  
2. Verifique se você instalou um certificado de cliente que foi emitido pelo certificado raiz que você carregou no Azure quando definiu as configurações de P2S. Isso é diferente do VPNServerRoot que você instalou na etapa anterior. O certificado do cliente é usado para autenticação e é necessário. Para obter mais informações sobre como gerar certificados, consulte [Generate Certificates](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Para obter informações sobre como instalar um certificado de cliente, consulte [instalar um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Abra a caixa de diálogo **rede** em **preferências de rede** e clique em **' + '** para criar um novo perfil de conexão de cliente VPN para uma conexão P2S com a VNet do Azure.

   O valor da **interface** é ' VPN ' e o valor do **tipo de VPN** é ' IKEv2 '. Especifique um nome para o perfil no campo **nome do serviço** e clique em **criar** para criar o perfil de conexão de cliente VPN.

   ![rede](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. Na pasta **genérica** , no arquivo **VpnSettings. xml** , copie o valor da marca **VpnServer** . Cole esse valor nos campos **endereço do servidor** e **ID remota** do perfil.

   ![informações do servidor](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Clique em **configurações de autenticação** e selecione **certificado**. 

   ![configurações de autenticação](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. Clique em **selecionar...** para escolher o certificado de cliente que você deseja usar para autenticação. Esse é o certificado que você instalou na etapa 2.

   ![certificado](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Escolher uma identidade** exibe uma lista de certificados para você escolher. Selecione o certificado apropriado e clique em **continuar**.

   ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. No campo **ID local** , especifique o nome do certificado (da etapa 6). Neste exemplo, é "ikev2Client.com". Em seguida, clique no botão **aplicar** para salvar as alterações.

   ![aplicar](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. Na caixa de diálogo **rede** , clique em **aplicar** para salvar todas as alterações. Em seguida, clique em **conectar** para iniciar a conexão P2S com a VNet do Azure.

## <a name="linuxgui"></a>Linux (GUI strongSwan)

### <a name="installstrongswan"></a>Instalar o strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="genlinuxcerts"></a>Gerar certificados

Se você ainda não gerou certificados, use as seguintes etapas:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install"></a>Instalar e configurar

As instruções a seguir foram criadas no Ubuntu 18.0.4. O Ubuntu 16.0.10 não dá suporte à GUI strongSwan. Se você quiser usar o Ubuntu 16.0.10, precisará usar a linha de [comando](#linuxinstallcli). Os exemplos abaixo podem não corresponder às telas que você vê, dependendo da sua versão do Linux e do strongSwan.

1. Abra o **terminal** para instalar o **StrongSwan** e seu Gerenciador de rede executando o comando no exemplo.

   ```
   sudo apt install network-manager-strongswan
   ```
2. Selecione **configurações** e, em seguida, selecione **rede**.

   ![editar conexões](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Clique no **+** botão para criar uma nova conexão.

   ![Adicionar uma conexão](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Selecione **IPSec/IKEv2 (strongSwan)** no menu e clique duas vezes em. Você pode nomear sua conexão nesta etapa.

   ![escolher um tipo de conexão](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Abra o arquivo **VpnSettings. xml** da pasta **genérica** contida nos arquivos de configuração do cliente baixados. Localize a marca chamada **VpnServer** e copie o nome, começando com ' azuregateway ' e terminando com '. cloudapp.net '.

   ![copiar nome](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Cole esse nome no campo **endereço** da nova conexão VPN na seção **Gateway** . Em seguida, selecione o ícone de pasta no final do campo **certificado** , navegue até a pasta **genérica** e selecione o arquivo **VpnServerRoot** .
7. Na seção **cliente** da conexão, para **autenticação**, selecione **certificado/chave privada**. Para **certificado** e **chave privada**, escolha o certificado e a chave privada que foram criados anteriormente. Em **Opções**, selecione **solicitar um endereço IP interno**. Em seguida, clique em **adicionar**.

   ![solicitar um endereço IP interno](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. Ative a conexão.

## <a name="linuxinstallcli"></a>Linux (CLI do strongSwan)

### <a name="install-strongswan"></a>Instalar o strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Gerar certificados

Se você ainda não gerou certificados, use as seguintes etapas:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Instalar e configurar

1. Baixe o pacote VPNClient do portal do Azure.
2. Extraia o arquivo.
3. Na pasta **genérica** , copie ou mova o VpnServerRoot. cer para/etc/IPSec.d/cacerts.
4. Copiar ou mover CP Client. p12 para/etc/IPSec.d/Private/. Este arquivo é um certificado de cliente para o gateway de VPN do Azure.
5. Abra o arquivo VpnSettings. xml e copie `<VpnServer>` o valor. Você usará esse valor na próxima etapa.
6. Ajuste os valores no exemplo abaixo e, em seguida, adicione o exemplo à configuração/etc/IPSec.conf.
  
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
6. Adicione o seguinte a */etc/IPSec.Secrets*.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Execute os seguintes comandos:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Passos seguintes

Retorne ao artigo para [concluir a configuração do P2S](vpn-gateway-howto-point-to-site-rm-ps.md).

Para solucionar problemas de conexões do P2S, consulte os seguintes artigos:

  * [Solucionando problemas de conexões ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Solucionar problemas de conexões VPN de clientes VPN Mac OS X](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
