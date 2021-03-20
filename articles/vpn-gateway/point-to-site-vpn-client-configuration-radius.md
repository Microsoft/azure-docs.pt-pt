---
title: 'Gateway Azure VPN: Criar & instalar ficheiros de configuração de clientes VPN - ligações P2S RADIUS'
description: Crie ficheiros de configuração de clientes Windows, OS X e Linux VPN para ligações que utilizem a autenticação RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: e6d811e19bb19c8c8bf96764cfcca2b1294f4a85
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91440058"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Criar e instalar ficheiros de configuração do cliente VPN para autenticação P2S RADIUS

Para se ligar a uma rede virtual por ponto a ponto (P2S), é necessário configurar o dispositivo cliente a que irá ligar. Pode criar ligações P2S VPN a partir de dispositivos clientes Windows, OS X e Linux. 

Quando está a utilizar a autenticação RADIUS, existem múltiplas opções de autenticação: nome de utilizador/autenticação de palavra-passe, autenticação de certificados e outros tipos de autenticação. A configuração do cliente VPN é diferente para cada tipo de autenticação. Para configurar o cliente VPN, utilize ficheiros de configuração do cliente que contenham as definições necessárias. Este artigo ajuda-o a criar e instalar a configuração do cliente VPN para o tipo de autenticação RADIUS que pretende utilizar.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

O fluxo de trabalho de configuração para a autenticação P2S RADIUS é o seguinte:

1. [Configurar o gateway Azure VPN para a conectividade P2S](point-to-site-how-to-radius-ps.md).
2. [Configurar o seu servidor RADIUS para autenticação](point-to-site-how-to-radius-ps.md#radius). 
3. **Obtenha a configuração do cliente VPN para a opção de autenticação à sua escolha e use-a para configurar o cliente VPN** (este artigo).
4. [Complete a sua configuração P2S e ligue- .](point-to-site-how-to-radius-ps.md)

>[!IMPORTANT]
>Se houver alterações na configuração VPN ponto-a-local depois de gerar o perfil de configuração do cliente VPN, como o tipo de protocolo VPN ou o tipo de autenticação, deve gerar e instalar uma nova configuração do cliente VPN nos dispositivos dos seus utilizadores.
>
>

Para utilizar as secções deste artigo, decida primeiro que tipo de autenticação pretende utilizar: nome de utilizador/palavra-passe, certificado ou outros tipos de autenticação. Cada secção tem passos para Windows, OS X e Linux (passos limitados disponíveis neste momento).


## <a name="usernamepassword-authentication"></a><a name="adeap"></a>Nome de utilizador/autenticação de palavra-passe

Pode configurar a autenticação do nome de utilizador/palavra-passe para utilizar o Ative Directory ou não utilizar o Ative Directory. Em qualquer dos cenários, certifique-se de que todos os utilizadores de ligação têm credenciais de nome de utilizador/palavra-passe que podem ser autenticadas através do RADIUS.

Ao configurar a autenticação do nome de utilizador/palavra-passe, só pode criar uma configuração para o EAP-MSCHAPv2 protocolo de autenticação de nome de utilizador/palavra-passe. Nos comandos, `-AuthenticationMethod` `EapMSChapv2` está.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="usernamefiles"></a> 1. Gerar ficheiros de configuração de clientes VPN

Pode gerar os ficheiros de configuração do cliente VPN utilizando o portal Azure ou utilizando o Azure PowerShell.

#### <a name="azure-portal"></a>Portal do Azure

1. Navegue até ao portal de rede virtual.
2. Clique **na configuração ponto-a-local**.
3. Clique **em Baixar o cliente VPN.**
4. Selecione o cliente e preencha qualquer informação que seja solicitada.
5. Clique **em Baixar** para gerar o ficheiro .zip.
6. O ficheiro .zip será descarregado, normalmente para a sua pasta Downloads.

#### <a name="azure-powershell"></a>Azure PowerShell

Gere ficheiros de configuração do cliente VPN para utilização com autenticação de nome de utilizador/palavra-passe. Pode gerar os ficheiros de configuração do cliente VPN utilizando o seguinte comando:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Executar o comando devolve uma ligação. Copie e cole o link para um navegador web para descarregar **VpnClientConfiguration.zip**. Desaperte o ficheiro para ver as seguintes pastas: 
 
* **WindowsAmd64** e **WindowsX86**: Estas pastas contêm os pacotes de instaladores Windows 64-bit e 32 bits, respectivamente. 
* **Genérico**: Esta pasta contém informações gerais que utiliza para criar a sua própria configuração de cliente VPN. Não precisa desta pasta para configurações de autenticação de nome de utilizador/palavra-passe.
* **Mac**: Se configurar o IKEv2 quando criou o gateway de rede virtual, vê uma pasta chamada **Mac** que contém um ficheiro **móvel configurado.** Usa este ficheiro para configurar clientes Mac.

Se já criou ficheiros de configuração do cliente, pode recuperá-los utilizando o `Get-AzVpnClientConfiguration` cmdlet. Mas se escoar quaisquer alterações na configuração P2S VPN, como o tipo de protocolo VPN ou o tipo de autenticação, a configuração não é atualizada automaticamente. Tem de executar o `New-AzVpnClientConfiguration` cmdlet para criar um novo download de configuração.

Para recuperar ficheiros de configuração do cliente previamente gerados, utilize o seguinte comando:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a> 2. Configures clientes VPN

Pode configurar os seguintes clientes VPN:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux usando strongSwan](#adlinuxcli)
 
#### <a name="windows-vpn-client-setup"></a><a name="adwincli"></a>Configuração do cliente Windows VPN

Pode utilizar o mesmo pacote de configuração de cliente VPN em cada computador cliente Windows, desde que a versão corresponda à arquitetura do cliente. Para a lista de sistemas operativos clientes suportados, consulte as [FAQ](vpn-gateway-vpn-faq.md#P2S).

Utilize os seguintes passos para configurar o cliente VPN do Windows nativo para autenticação de certificado:

1. Selecione os ficheiros de configuração do cliente VPN que correspondem à arquitetura do computador Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote de instaladores **VPNClientSetupAmd64.** Para uma arquitetura de processador de 32 bits, escolha o pacote de instaladores **VpnClientSetupX86.** 
2. Para instalar o pacote, clique duas vezes. Se vir um pop-up smartScreen, selecione **Mais informações**  >  **Run de qualquer maneira**.
3. No computador cliente, navegue pelas **Definições de Rede** e selecione **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga. 

#### <a name="mac-os-x-vpn-client-setup"></a><a name="admaccli"></a>Configuração do cliente VPN Mac (OS X)

1. Selecione o ficheiro **móvel VpnClientSetup** e envie-o para cada um dos utilizadores. Pode utilizar e-mail ou outro método.

2. Localize o ficheiro **deconfig móvel** no Mac.

   ![Localização do ficheiro mobileconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Passo Opcional - Se pretender especificar um DNS personalizado, adicione as seguintes linhas ao ficheiro **mobileconfig:**

   ```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        </array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
   ```
4. Clique duas vezes no perfil para o instalar e selecione **Continue**. O nome do perfil é o mesmo que o nome da sua rede virtual.

   ![Mensagem de instalação](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. **Selecione Continue** a confiar no remetente do perfil e proceda à instalação.

   ![Mensagem de confirmação](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Durante a instalação do perfil, tem a opção de especificar o nome de utilizador e a palavra-passe para a autenticação VPN. Não é obrigatório inserir esta informação. Se o fizer, a informação é guardada e automaticamente utilizada quando inicia uma ligação. **Selecione Instalar** para prosseguir.

   ![Nome de utilizador e caixas de senha para VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Introduza um nome de utilizador e senha para os privilégios necessários para instalar o perfil no seu computador. Selecione **OK**.

   ![Nome de utilizador e caixas de senha para instalação de perfil](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. Depois de instalado o perfil, é visível na caixa de diálogo **de Perfis.** Também pode abrir esta caixa de diálogo mais tarde a partir de **Preferências** do Sistema .

   ![Caixa de diálogo "Perfis"](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. Para aceder à ligação VPN, abra a caixa de diálogo **de Rede** a partir de **Preferências** do Sistema .

   ![Ícones nas preferências do sistema](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. A ligação VPN aparece como **IkeV2-VPN**. Pode alterar o nome atualizando o ficheiro **mobileconfig.**

    ![Detalhes para a ligação VPN](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Selecione **Definições de autenticação**. Selecione **Username** na lista e introduza as suas credenciais. Se introduziu as credenciais mais cedo, então o **nome de utilizador** é automaticamente escolhido na lista e o nome de utilizador e a palavra-passe são pré-povoados. Selecione **OK** para guardar as definições.

    ![Screenshot que mostra as "Definições de autenticação" com "Nome de utilizador" selecionado.](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. De volta à caixa de diálogo **'Rede',** **selecione Aplicar** para guardar as alterações. Para iniciar a ligação, selecione **Connect**.

#### <a name="linux-vpn-client-setup-through-strongswan"></a><a name="adlinuxcli"></a>Configuração de clientes Linux VPN através do strongSwan

As seguintes instruções foram criadas através do strongSwan 5.5.1 em Ubuntu 17.0.4. Os ecrãs reais podem ser diferentes, dependendo da sua versão de Linux e strongSwan.

1. Abra o **Terminal** para instalar **o strongSwan** e o seu Gestor de Rede executando o comando no exemplo. Se receber um erro relacionado `libcharon-extra-plugins` com, substitua-o por `strongswan-plugin-eap-mschapv2` .

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Selecione o ícone **'Gestor de rede'** (seta para cima/seta para baixo) e selecione **Ligações de Edição**.

   ![Seleção "Editar Ligações" no Network Manager](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Selecione o botão **Adicionar** para criar uma nova ligação.

   ![Botão "Adicionar" para uma ligação](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Selecione **IPsec/IKEv2 (strongswan)** do menu suspenso e, em seguida, **selecione Criar**. Pode mudar o nome da sua ligação neste passo.

   ![Selecionando o tipo de ligação](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Abra o ficheiro **VpnSettings.xml** da pasta **Genérica** dos ficheiros de configuração do cliente descarregados. Encontre a etiqueta chamada `VpnServer` e copie o nome, começando `azuregateway` com e terminando com `.cloudapp.net` .

   ![Conteúdo do ficheiro VpnSettings.xml](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Cole este nome no campo **Address** da sua nova ligação VPN na secção **Gateway.** Em seguida, selecione o ícone da pasta no final do campo **Certificate,** navegue na pasta **Genérico** e selecione o ficheiro **VpnServerRoot.**
7. Na secção **Cliente** da ligação, selecione **EAP** para **Autenticação,** e introduza o seu nome de utilizador e senha. Poderá ter de selecionar o ícone de bloqueio no direito de guardar esta informação. Em seguida, **selecione Save**.

   ![Definições de ligação de edição](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Selecione o ícone **do Gestor de Rede** (seta para cima/seta para baixo) e paire sobre as **ligações VPN**. Vê a ligação VPN que criou. Para iniciar a ligação, selecione-a.

   ![Ligação "VPN Radius" no Gestor de Rede](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certificate-authentication"></a><a name="certeap"></a>Autenticação de certificados
 
Pode criar ficheiros de configuração de clientes VPN para autenticação de certificado RADIUS que utilize o protocolo EAP-TLS. Normalmente, um certificado emitido pela empresa é usado para autenticar um utilizador para VPN. Certifique-se de que todos os utilizadores de ligação têm um certificado instalado nos seus dispositivos e que o seu servidor RADIUS pode validar o certificado.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Nos comandos, `-AuthenticationMethod` `EapTls` está. Durante a autenticação do certificado, o cliente valida o servidor RADIUS validando o seu certificado. `-RadiusRootCert` é o ficheiro .cer que contém o certificado raiz que é usado para validar o servidor RADIUS.

Cada dispositivo cliente VPN requer um certificado de cliente instalado. Às vezes, um dispositivo Windows tem vários certificados de cliente. Durante a autenticação, isto pode resultar numa caixa de diálogo pop-up que lista todos os certificados. O utilizador deve então escolher o certificado a utilizar. O certificado correto pode ser filtrado especificando o certificado raiz a que o certificado do cliente deve ser acorrentado. 

`-ClientRootCert` é o ficheiro .cer que contém o certificado raiz. É um parâmetro opcional. Se o dispositivo a que pretende ligar tiver apenas um certificado de cliente, não tem de especificar este parâmetro.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="certfiles"></a>1. Gerar ficheiros de configuração de clientes VPN

Gere ficheiros de configuração do cliente VPN para utilização com autenticação de certificado. Pode gerar os ficheiros de configuração do cliente VPN utilizando o seguinte comando:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Executar o comando devolve uma ligação. Copie e cole o link para um navegador web para descarregar VpnClientConfiguration.zip. Desaperte o ficheiro para ver as seguintes pastas:

* **WindowsAmd64** e **WindowsX86**: Estas pastas contêm os pacotes de instaladores Windows 64-bit e 32 bits, respectivamente. 
* **GenericDevice**: Esta pasta contém informações gerais que são usadas para criar a sua própria configuração de cliente VPN.

Se já criou ficheiros de configuração do cliente, pode recuperá-los utilizando o `Get-AzVpnClientConfiguration` cmdlet. Mas se escoar quaisquer alterações na configuração P2S VPN, como o tipo de protocolo VPN ou o tipo de autenticação, a configuração não é atualizada automaticamente. Tem de executar o `New-AzVpnClientConfiguration` cmdlet para criar um novo download de configuração.

Para recuperar ficheiros de configuração do cliente previamente gerados, utilize o seguinte comando:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a> 2. Configures clientes VPN

Pode configurar os seguintes clientes VPN:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (suportado, ainda sem etapas de artigo)

#### <a name="windows-vpn-client-setup"></a><a name="certwincli"></a>Configuração do cliente Windows VPN

1. Selecione um pacote de configuração e instale-o no dispositivo cliente. Para uma arquitetura de processador de 64 bits, escolha o pacote de instaladores **VPNClientSetupAmd64.** Para uma arquitetura de processador de 32 bits, escolha o pacote de instaladores **VpnClientSetupX86.** Se vir um pop-up smartScreen, selecione **Mais informações**  >  **Run de qualquer maneira**. Também pode guardar o pacote para instalar noutros computadores cliente.
2. Cada cliente requer um certificado de cliente para autenticação. Instale o certificado do cliente. Para obter informações sobre certificados de cliente, consulte [os certificados de Cliente para ponto a local.](vpn-gateway-certificates-point-to-site.md) Para instalar um certificado que foi gerado, consulte [instalar um certificado nos clientes windows](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. No computador cliente, navegue pelas **Definições de Rede** e selecione **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga.

#### <a name="mac-os-x-vpn-client-setup"></a><a name="certmaccli"></a>Configuração do cliente VPN Mac (OS X)

Tem de criar um perfil separado para cada dispositivo Mac que se conecte à rede virtual Azure. Isto porque estes dispositivos exigem que o certificado de autenticação do utilizador seja especificado no perfil. A pasta **Genérica** tem toda a informação necessária para criar um perfil:

* **VpnSettings.xml** contém configurações importantes, tais como endereço de servidor e tipo de túnel.
* **VpnServerRoot.cer** contém o certificado de raiz necessário para validar o gateway VPN durante a configuração da ligação P2S.
* **RadiusServerRoot.cer** contém o certificado de raiz necessário para validar o servidor RADIUS durante a autenticação.

Utilize os seguintes passos para configurar o cliente VPN nativo num Mac para autenticação de certificado:

1. Importe os certificados de raiz **VpnServerRoot** e **RadiusServerRoot** para o seu Mac. Copie cada ficheiro para o seu Mac, clique duas vezes nele e, em seguida, **selecione Adicionar**.

   ![Adicionar o certificado VpnServerRoot](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Adicionar o certificado RadiusServerRoot](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Cada cliente requer um certificado de cliente para autenticação. Instale o certificado do cliente no dispositivo cliente.
3. Abra a caixa de diálogo **de rede** em **preferências de rede**. Selecione **+** para criar um novo perfil de ligação ao cliente VPN para uma ligação P2S à rede virtual Azure.

   O valor **da Interface** é **VPN,** e o valor **do tipo VPN** é **IKEv2**. Especifique um nome para o perfil na caixa **'Nome de Serviço'** e, em seguida, selecione **Criar** para criar o perfil de ligação ao cliente VPN.

   ![Informações sobre interface e nome de serviço](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. Na pasta **Genérico,** a partir do ficheiro **VpnSettings.xml,** copie o valor da etiqueta **VpnServer.** Cole este valor nas caixas **de endereço do servidor** e **iD remoto** do perfil. Deixe a caixa **de identificação local** em branco.

   ![Informações do servidor](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Selecione **Definições de autenticação** e selecione **Certificado**. 

   ![Definições de autenticação](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Clique **em Selecionar** para escolher o certificado que pretende utilizar para autenticação.

   ![Selecionando um certificado para autenticação](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Escolha Uma Identidade** apresenta uma lista de certificados para escolher. Selecione o certificado adequado e, em seguida, **selecione Continue**.

   ![Lista de "Escolha uma Identidade"](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. Na caixa **de identificação local,** especifique o nome do certificado (a partir do passo 6). Neste exemplo, é **ikev2Client.com.** Em seguida, **selecione** o botão Aplicar para guardar as alterações.

   ![Caixa "ID local"](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. Na caixa de diálogo **'Rede',** **selecione Aplicar** para guardar todas as alterações. Em seguida, **selecione Connect** para iniciar a ligação P2S à rede virtual Azure.

## <a name="working-with-other-authentication-types-or-protocols"></a><a name="otherauth"></a>Trabalhar com outros tipos de autenticação ou protocolos

Para utilizar um tipo de autenticação diferente (por exemplo, OTP), ou para utilizar um protocolo de autenticação diferente (como PEAP-MSCHAPv2 em vez de EAP-MSCHAPv2), tem de criar o seu próprio perfil de configuração do cliente VPN. Para criar o perfil, precisa de informações como o endereço IP de gateway de rede virtual, tipo de túnel e rotas de túneis divididos. Pode obter esta informação utilizando os seguintes passos:

1. Utilize o `Get-AzVpnClientConfiguration` cmdlet para gerar a configuração do cliente VPN para EapMSChapv2.

2. Desaperte o ficheiro VpnClientConfiguration.zip e procure a pasta **GenericDevice.** Ignore as pastas que contêm os instaladores do Windows para arquiteturas de 64 bits e 32 bits.
 
3. A pasta **GenericDevice** contém um ficheiro XML chamado **VpnSettings**. Este ficheiro contém todas as informações necessárias:

   * **VPNServer**: FQDN do gateway Azure VPN. Esta é a morada a que o cliente se liga.
   * **VPNType**: Tipo de túnel que utiliza para ligar.
   * **Rotas**: Rotas que tem de configurar no seu perfil para que apenas o tráfego que está ligado à rede virtual Azure seja enviado pelo túnel P2S.
   
   A pasta **GenericDevice** também contém um ficheiro .cer chamado **VpnServerRoot**. Este ficheiro contém o certificado de raiz necessário para validar o gateway Azure VPN durante a configuração da ligação P2S. Instale o certificado em todos os dispositivos que se ligarão à rede virtual Azure.

## <a name="next-steps"></a>Passos seguintes

Volte ao artigo para completar a [sua configuração P2S](point-to-site-how-to-radius-ps.md).

Para obter informações sobre resolução de problemas P2S, consulte [as ligações ponto-a-local de resolução de problemas](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
