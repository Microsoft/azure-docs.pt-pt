---
title: 'Criar e instalar ficheiros de configuração de cliente de P2S VPN para autenticação de certificados do Azure: Azure | Microsoft Docs'
description: Criar e instalar o Windows, Linux, o Linux (strongSwan) e a VPN do Mac OS X arquivos de configuração de cliente para autenticação de certificados P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/13/2019
ms.author: cherylmc
ms.openlocfilehash: 58936fa85567dcac624b15e95bbd84e68e0ae117
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58009890"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Criar e instalar os ficheiros de configuração de cliente VPN para configurações P2S da autenticação de certificados nativa do Azure

Ficheiros de configuração de cliente VPN estão contidos num arquivo zip. Ficheiros de configuração fornecem as definições necessárias para um clientes nativos do Windows, a VPN IKEv2 do Mac ou Linux ligar a uma VNet através de ligações de ponto a Site que utilizam a autenticação de certificados nativa do Azure.

Ficheiros de configuração de cliente são específicos para a configuração de VPN para a VNet. Se existirem quaisquer alterações à configuração de VPN ponto a Site depois de gerar os ficheiros de configuração de cliente VPN, como o tipo de protocolo VPN ou o tipo de autenticação, certifique-se de que gerar novos ficheiros de configuração de cliente VPN para os seus dispositivos de utilizador. 

* Para obter mais informações sobre ligações Ponto a Site, veja [Acerca da VPN Ponto a Site](point-to-site-about.md).
* Para obter instruções de OpenVPN, consulte [OpenVPN configurar para P2S](vpn-gateway-howto-openvpn.md) e [OpenVPN configurar clientes](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate"></a>Gerar ficheiros de configuração de cliente VPN

Antes de começar, certifique-se de que todos os utilizadores ligados têm um certificado válido instalado no dispositivo do utilizador. Para obter mais informações sobre como instalar um certificado de cliente, consulte [instale um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

Pode gerar arquivos de configuração de cliente com o PowerShell, ou com o portal do Azure. Qualquer um dos métodos devolve o ficheiro zip do mesmo. Deszipe o ficheiro para ver as seguintes pastas:

  * **WindowsAmd64** e **WindowsX86**, que contêm os pacotes de instalador do Windows de 32 bits e 64 bits, respectivamente. O **WindowsAmd64** pacote de instalador é para todos os clientes Windows de 64 bits, não apenas Amd de suporte.
  * **Genérico**, que contém informações gerais, utilizadas para criar sua própria configuração do cliente VPN. A pasta genérica é fornecida se IKEv2 ou SSTP + IKEv2 foi configurada no gateway. Se apenas SSTP estiver configurado, a pasta genérica não está presente.

### <a name="zipportal"></a>Gerar arquivos com o portal do Azure

1. No portal do Azure, navegue para o gateway de rede virtual para a rede virtual que pretende ligar a.
2. Na página do gateway de rede virtual, clique em **configuraçãopontoasiteda**.
3. Na parte superior da página de configuração ponto a site, clique em **transferir cliente VPN**. Demora alguns minutos para o cliente do pacote de configuração para gerar.
4. O browser indica que um ficheiro de zip de configuração do cliente está disponível. Ele é o mesmo nome como o seu gateway. Deszipe o ficheiro para ver as pastas.

### <a name="zipps"></a>Gerar arquivos com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Ao gerar a configuração de cliente VPN ficheiros, o valor para '-AuthenticationMethod' é 'EapTls'. Gere os arquivos de configuração de cliente VPN com o seguinte comando:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Copie o URL para o browser para transferir o ficheiro zip e, em seguida, Descomprima o ficheiro para ver as pastas.

## <a name="installwin"></a>Windows

Pode utilizar o mesmo pacote de configuração de cliente VPN em cada computador de cliente do Windows, desde que a versão corresponda à arquitetura do cliente. Para obter a lista de sistemas operativos cliente suportados, consulte a secção ponto a Site a [FAQ do Gateway de VPN](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Tem de ter direitos de administrador no computador cliente Windows do qual pretende ligar.
>
>

Utilize os seguintes passos para configurar o cliente de VPN do Windows nativo para autenticação de certificados:

1. Selecione os ficheiros de configuração do cliente VPN que correspondem à arquitetura do computador Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador "VpnClientSetupAmd64". Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador "VpnClientSetupX86". 
2. Faça duplo clique no pacote para instalá-lo. Se vir um pop-up SmartScreen, clique em **Mais informações** e, em seguida, em **Executar mesmo assim**.
3. No computador cliente, navegue para **Definições de Rede** e clique em **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga. 
4. Antes de tentar ligar, certifique-se de que instalou um certificado de cliente no computador cliente. Se utilizar o tipo de autenticação de certificados nativa do Azure, é preciso um certificado de cliente para a autenticação. Para obter mais informações sobre como gerar certificados, consulte [gerar certificados](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Para obter informações sobre como instalar um certificado de cliente, consulte [instale um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>Mac (OS X)

 Tem de configurar manualmente o cliente VPN IKEv2 nativo em cada Mac que irá ligar ao Azure. O Azure não fornece o ficheiro mobileconfig para a autenticação de certificados nativa do Azure. O **genérico** contém todas as informações que precisa para a configuração. Se não vir a pasta Generic na sua transferência, é provável que o IKEv2 não tenha sido selecionado como um tipo de túnel. Depois de selecionar IKEv2, gere novamente o ficheiro zip para obter a pasta Generic.<br>A pasta Generic contém os seguintes ficheiros:

* **Vpnsettings**, que contém configurações importantes, como o tipo de endereço e túnel do servidor. 
* **VpnServerRoot.cer**, que contém o certificado de raiz necessário para validar o Gateway de VPN do Azure durante a configuração de ligação de P2S.

Utilize os seguintes passos para configurar o cliente VPN nativo no Mac para autenticação de certificados. Tem de concluir estes passos em cada Mac que irá ligar ao Azure:

1. Importar os **VpnServerRoot** certificado de raiz no seu Mac. Isso pode ser feito copiando o arquivo ao longo para Mac e duas vezes no mesmo. Clique em **adicionar** para importar.

   ![Adicionar certificado](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Fazer duplo clique no certificado não pode ser apresentado a **adicionar** caixa de diálogo, mas o certificado está instalado no arquivo correto. Pode verificar o certificado na keychain início de sessão sob a categoria de certificados.
    >
  
2. Certifique-se de que tem instalado um certificado de cliente que foi emitido pelo certificado de raiz que carregou para o Azure quando tiver configurado as definições de P2S. Isso é diferente do VPNServerRoot que instalou no passo anterior. O certificado de cliente é utilizado para autenticação e é necessário. Para obter mais informações sobre como gerar certificados, consulte [gerar certificados](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Para obter informações sobre como instalar um certificado de cliente, consulte [instale um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Abra o **rede** caixa de diálogo sob **preferências de rede** e clique em **'+'** para criar um novo perfil de ligação de cliente VPN para uma ligação de P2S à VNet do Azure.

   O **Interface** valor é "VPN" e **tipo de VPN** valor é "IKEv2". Especifique um nome para o perfil no **nome do serviço** campo, em seguida, clique em **criar** para criar o perfil de ligação de cliente VPN.

   ![rede](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. Na **genérico** pasta, da **Vpnsettings** ficheiro, copie o **VpnServer** valor de etiqueta. Cole este valor na **endereço do servidor** e **remoto ID** campos do perfil.

   ![informações do servidor](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Clique em **definições de autenticação** e selecione **certificado**. 

   ![definições de autenticação](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. Clique em **selecione...** para escolher o certificado de cliente que pretende utilizar para autenticação. Este é o certificado que instalou no passo 2.

   ![certificado](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Escolha uma identidade** apresenta uma lista de certificados para a sua escolha. Selecione o certificado adequado, em seguida, clique em **continuar**.

   ![identidade](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. Na **Local ID** campo, especifique o nome do certificado (a partir do passo 6). Neste exemplo, é "ikev2Client.com". Em seguida, clique em **aplicar** botão para guardar as alterações.

   ![aplicar](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. Sobre o **rede** caixa de diálogo, clique em **aplicar** para guardar todas as alterações. Em seguida, clique em **Connect** para iniciar a ligação de P2S à VNet do Azure.

## <a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="extract-the-key-and-certificate"></a>Extrair a chave e o certificado

Para strongSwan, terá de extrair a chave e o certificado a partir do certificado de cliente (ficheiro. pfx) e salvá-los em ficheiros. pem individuais.
Siga os passos abaixo:

1. Transferir e instalar o OpenSSL partir [OpenSSL](https://www.openssl.org/source/).
2. Abra uma janela da linha de comandos e altere o diretório onde instalou OpenSSL, por exemplo, "c:\OpenSLL-Win64\bin\'.
3. Execute o seguinte comando para extrair a chave privada e guarde-o para um novo arquivo chamado 'privatekey.pem' a partir do seu certificado de cliente:

   ```
   C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nocerts -out privatekey.pem -nodes
   ```
4. Agora, execute o seguinte comando para extrair o certificado público e guarde-o para um novo arquivo:

   ```
   C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nokeys -out publiccert.pem -nodes
   ```

### <a name="install"></a>Instalar e configurar

As instruções seguintes foram criadas por meio de strongSwan 5.5.1 no Ubuntu 17.0.4. Ubuntu 16.0.10 não suporta strongSwan GUI. Se pretender utilizar o Ubuntu 16.0.10, terá de utilizar o [linha de comandos](#linuxinstallcli). Os exemplos abaixo podem não corresponder ao telas que vir, dependendo da versão do Linux e strongSwan.

1. Abra o **Terminal** para instalar **strongSwan** e o seu Gestor de rede ao executar o comando no exemplo. Se receber um erro que está relacionado à *libcharon-adicionais-plug-ins*, substitua-o por 'strongswan-Plug-in-eap-mschapv2'.

   ```
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Selecione o **Gestor de rede** ícone (up-seta/seta para baixo), em seguida, selecione **Editar ligações**.

   ![Editar ligações](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Clique nas **adicionar** botão para criar uma nova ligação.

   ![Adicionar uma ligação](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Selecione **(strongswan) de IPsec/IKEv2** no menu de lista pendente e, em seguida, clique **criar**. Pode mudar o nome da sua ligação neste passo.

   ![Escolha um tipo de ligação](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Abra o **Vpnsettings** ficheiro a partir a **genérico** pasta contida nos arquivos de configuração de cliente transferidos. Localize a marca chamada **VpnServer** e copie o nome, a partir do "azuregateway" e terminando com ". cloudapp.net".

   ![nome de cópia](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Colar este nome para o **endereço** campo de sua nova ligação VPN na **Gateway** secção. Em seguida, selecione o ícone de pasta no final da **certificado** campo, navegue para o **genérica** pasta e selecione o **VpnServerRoot** ficheiro.
7. Na **cliente** secção da ligação, para **autenticação**, selecione **chave privada do certificado**. Para **certificado** e **chave privada**, escolha o certificado e a chave privada que foram criados anteriormente. Na **opções**, selecione **peça um endereço IP interno**. Em seguida, clique em **adicionar**.

   ![Peça um endereço IP interno](./media/point-to-site-vpn-client-configuration-azure-cert/inneripreq.png)
8. Clique nas **Gestor de rede** ícone (up-seta/seta para baixo) e coloque o cursor sobre **ligações VPN**. Veja a ligação de VPN que criou. Clique para iniciar a ligação.

## <a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>Instalar strongSwan

Pode utilizar os seguintes comandos da CLI, ou utilize os passos de strongSwan no [GUI](#install) instalar strongSwan.

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

### <a name="install-and-configure"></a>Instalar e configurar

1. Transferir o pacote do VPNClient a partir do portal do Azure.
2. Extraia o ficheiro.
3. Partir do **genérico** pasta, copiar ou mover o VpnServerRoot.cer para /etc/ipsec.d/cacerts.
4. Copiar ou mover cp client.p12 para /etc/ipsec.d/private/. Esse arquivo é o certificado de cliente para o Gateway de VPN do Azure.
5. Abra o ficheiro Vpnsettings e copie o <VpnServer> valor. Irá utilizar este valor no próximo passo.
6. Ajustar os valores no exemplo abaixo, em seguida, adicionar o exemplo para a configuração de /etc/ipsec.conf.
  
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
6. Adicione o seguinte ao */etc/ipsec.secrets*.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Execute os seguintes comandos:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Passos Seguintes

Devolver o artigo para [concluir a configuração de P2S](vpn-gateway-howto-point-to-site-rm-ps.md).

Para resolver problemas de ligações de P2S, consulte os artigos seguintes:

  * [Resolução de problemas de ligações de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Resolver problemas de ligações de VPN de clientes de VPN do Mac OS X](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
