---
title: 'Azure VPN Gateway: Criar & instalar ficheiros de config de cliente VPN - ligações P2S RADIUS'
description: Crie ficheiros de configuração de clientes Windows, Mac OS X e Linux VPN para ligações que utilizem a autenticação RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 69517d69a26364cf1cc950d7aaa849522decacf1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732742"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Criar e instalar ficheiros de configuração do cliente VPN para autenticação P2S RADIUS

Para se ligar a uma rede virtual sobre o ponto ao local (P2S), precisa de configurar o dispositivo cliente a que se vai ligar. Pode criar ligações VPN P2S a partir de dispositivos de cliente Windows, Mac OS X e Linux. 

Quando estiver a utilizar a autenticação RADIUS, existem múltiplas opções de autenticação: autenticação de username/password, autenticação de certificado e outros tipos de autenticação. A configuração do cliente VPN é diferente para cada tipo de autenticação. Para configurar o cliente VPN, utiliza ficheiros de configuração do cliente que contenham as definições necessárias. Este artigo ajuda-o a criar e instalar a configuração do cliente VPN para o tipo de autenticação RADIUS que pretende utilizar.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

O fluxo de trabalho de configuração para autenticação P2S RADIUS é o seguinte:

1. [Instale a porta de entrada Azure VPN para a conectividade P2S](point-to-site-how-to-radius-ps.md).
2. [Configurar o servidor RADIUS para autenticação](point-to-site-how-to-radius-ps.md#radius). 
3. **Obtenha a configuração do cliente VPN para a opção de autenticação à sua escolha e use-a para configurar o cliente VPN** (este artigo).
4. [Complete a configuração p2S e ligue](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Se houver alterações na configuração VPN ponto-a-site depois de gerar o perfil de configuração do cliente VPN, como o tipo de protocolo VPN ou o tipo de autenticação, deve gerar e instalar uma nova configuração de cliente VPN nos dispositivos dos seus utilizadores.
>
>

Para utilizar as secções deste artigo, decida primeiro qual o tipo de autenticação que pretende utilizar: nome de utilizador/palavra-passe, certificado ou outros tipos de autenticação. Cada secção tem passos para Windows, Mac OS X e Linux (passos limitados disponíveis neste momento).


## <a name="usernamepassword-authentication"></a><a name="adeap"></a>Autenticação de nome de utilizador/palavra-passe

Pode configurar a autenticação do nome de utilizador/palavra-passe para utilizar o Diretório Ativo ou não utilizar o Diretório Ativo. Em qualquer dos cenários, certifique-se de que todos os utilizadores de ligação têm credenciais de username/password que podem ser autenticadas através do RADIUS.

Quando configura o nome de utilizador/a autenticação password, só pode criar uma configuração para o protocolo de autenticação de nome de utilizador/palavra-passe EAP-MSCHAPv2. Nos comandos, `-AuthenticationMethod` `EapMSChapv2`é.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="usernamefiles"></a>1. Gerar ficheiros de configuração de clientes VPN

Pode gerar os ficheiros de configuração do cliente VPN utilizando o portal Azure, ou utilizando o Azure PowerShell.

#### <a name="azure-portal"></a>Portal do Azure

1. Navegue para o portal da rede virtual.
2. Clique na **configuração ponto-a-site**.
3. Clique em **Baixar o cliente VPN**.
4. Selecione o cliente e preencha qualquer informação solicitada.
5. Clique em **Baixar** para gerar o ficheiro .zip.
6. O ficheiro .zip será descarregado, normalmente para a sua pasta Downloads.

#### <a name="azure-powershell"></a>Azure PowerShell

Gere ficheiros de configuração de cliente VPN para utilização com autenticação de username/password. Pode gerar os ficheiros de configuração do cliente VPN utilizando o seguinte comando:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
A execução do comando devolve um link. Copie e cole o link para um navegador web para baixar **VpnClientConfiguration.zip**. Desapertar o ficheiro para visualizar as seguintes pastas: 
 
* **WindowsAmd64** e **WindowsX86**: Estas pastas contêm os pacotes de instaladores windows 64 e 32 bits, respectivamente. 
* **Genérico**: Esta pasta contém informações gerais que utiliza para criar a sua própria configuração de cliente VPN. Não precisa desta pasta para configurações de autenticação de nome de utilizador/palavra-passe.
* **Mac**: Se configurar o IKEv2 quando criou o portal de rede virtual, vê uma pasta chamada **Mac** que contém um ficheiro **de mobileconfig.** Usa este ficheiro para configurar clientes Mac.

Se já criou ficheiros de configuração do `Get-AzVpnClientConfiguration` cliente, pode recuperá-los utilizando o cmdlet. Mas se fizer alterações na configuração p2S VPN, como o tipo de protocolo VPN ou o tipo de autenticação, a configuração não é atualizada automaticamente. Tem de `New-AzVpnClientConfiguration` executar o cmdlet para criar um novo download de configuração.

Para recuperar ficheiros de configuração de clientes anteriormente gerados, utilize o seguinte comando:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a>2. Configure clientes VPN

Pode configurar os seguintes clientes VPN:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux usando o strongSwan](#adlinuxcli)
 
#### <a name="windows-vpn-client-setup"></a><a name="adwincli"></a>Configuração do cliente Do Windows VPN

Pode utilizar o mesmo pacote de configuração de cliente VPN em cada computador cliente do Windows, desde que a versão corresponda à arquitetura para o cliente. Para a lista de sistemas operativos do cliente que são suportados, consulte as [FAQ](vpn-gateway-vpn-faq.md#P2S).

Utilize os seguintes passos para configurar o cliente VPN nativo do Windows para autenticação de certificado:

1. Selecione os ficheiros de configuração do cliente VPN que correspondem à arquitetura do computador Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote instalador **VpnClientSetupAmd64.** Para uma arquitetura de processador de 32 bits, escolha o pacote instalador **VpnClientSetupX86.** 
2. Para instalar a embalagem, clique duas vezes. Se vir um pop-up SmartScreen, selecione **Mais informações** > **Run de qualquer forma**.
3. No computador cliente, navegue para **as Definições de Rede** e selecione **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga. 

#### <a name="mac-os-x-vpn-client-setup"></a><a name="admaccli"></a>Configuração do cliente VpN Mac (OS X)

1. Selecione o ficheiro **vpnClientSetup mobileconfig** e envie-o para cada um dos utilizadores. Pode usar e-mail ou outro método.

2. Localize o ficheiro **de móvel config** no Mac.

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
4. Clique duas vezes no perfil para instalá-lo e selecione **Continuar**. O nome do perfil é o mesmo que o nome da sua rede virtual.

   ![Mensagem de instalação](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Selecione **Continuar** a confiar no remetente do perfil e prossiga com a instalação.

   ![Mensagem de confirmação](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Durante a instalação do perfil, tem a opção de especificar o nome de utilizador e a palavra-passe para autenticação VPN. Não é obrigatório introduzir esta informação. Se o fizer, a informação é guardada e automaticamente utilizada quando inicia uma ligação.Selecione **Instalar** para prosseguir.

   ![Username e caixas de senha para VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Introduza um nome de utilizador e uma palavra-passe para os privilégios necessários para instalar o perfil no seu computador. Selecione **OK**.

   ![Username e caixas de palavra-passe para instalação de perfil](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. Depois de instalado o perfil, é visível na caixa de diálogo **perfis.** Também pode abrir esta caixa de diálogo mais tarde a partir das **Preferências**do Sistema .

   ![Caixa de diálogo "Perfis"](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. Para aceder à ligação VPN, abra a caixa de diálogo da **Rede** a partir de **Preferências**do Sistema .

   ![Ícones nas preferências do sistema](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. A ligação VPN aparece como **IkeV2-VPN**. Pode alterar o nome atualizando o ficheiro **mobileconfig.**

    ![Detalhes para a ligação VPN](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. **Selecione Definições de Autenticação**. Selecione **username** na lista e introduza as suas credenciais. Se inseriu as credenciais mais cedo, então o **username** é automaticamente escolhido na lista e o nome de utilizador e a palavra-passe estão pré-povoados. Selecione **OK** para guardar as definições.

    ![Definições de autenticação](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. De volta à caixa de diálogo **da Rede,** selecione **Aplicar** para guardar as alterações. Para iniciar a ligação, selecione **Connect**.

#### <a name="linux-vpn-client-setup-through-strongswan"></a><a name="adlinuxcli"></a>Configuração do cliente Da PVpN linux através do strongSwan

As seguintes instruções foram criadas através do strongSwan 5.5.1 em Ubuntu 17.0.4. Os ecrãs reais podem ser diferentes, dependendo da sua versão de Linux e strongSwan.

1. Abra o **Terminal** para instalar **o StrongSwan** e o seu Gestor de Rede executando o comando no exemplo. Se receber um erro `libcharon-extra-plugins`relacionado, substitua-o por `strongswan-plugin-eap-mschapv2`.

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Selecione o ícone **do Gestor** de Rede (seta/seta para baixo) e selecione **Ligações de Edição**.

   ![Seleção "Editar Ligações" no Gestor de Rede](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Selecione o botão **Adicionar** para criar uma nova ligação.

   ![Botão "Adicionar" para uma ligação](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Selecione **IPsec/IKEv2 (strongswan)** a partir do menu suspenso e, em seguida, selecione **Criar**. Pode mudar o nome da sua ligação neste passo.

   ![Selecionando o tipo de ligação](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Abra o ficheiro **VpnSettings.xml** a partir da pasta **Genérica** dos ficheiros de configuração do cliente descarregados. Encontre a `VpnServer` etiqueta chamada e copie o nome, começando com `azuregateway` e terminando com `.cloudapp.net`.

   ![Conteúdo do ficheiro VpnSettings.xml](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Colá-lo no campo **Address** da sua nova ligação VPN na secção **Gateway.** Em seguida, selecione o ícone da pasta na extremidade do campo **Certificado,** navegue para a pasta **Genérica** e selecione o ficheiro **VpnServerRoot.**
7. Na secção **Cliente** da ligação, selecione **EAP** para **Autenticação**e introduza o seu nome de utilizador e senha. Pode ter de selecionar o ícone de bloqueio à direita para guardar esta informação. Em seguida, selecione **Guardar**.

   ![Definições de ligação de edição](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Selecione o ícone **do Gestor** de Rede (seta/seta) e paire sobre **ligações VPN**. Vê a ligação VPN que criou. Para iniciar a ligação, selecione-a.

   ![Ligação "VPN Radius" no Gestor de Rede](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certificate-authentication"></a><a name="certeap"></a>Autenticação de certificados
 
Pode criar ficheiros de configuração de cliente VPN para autenticação de certificado RADIUS que utiliza o protocolo EAP-TLS. Normalmente, um certificado emitido pela empresa é usado para autenticar um utilizador para VPN. Certifique-se de que todos os utilizadores de ligação têm um certificado instalado nos seus dispositivos e que o seu servidor RADIUS pode validar o certificado.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Nos comandos, `-AuthenticationMethod` `EapTls`é. Durante a autenticação do certificado, o cliente valida o servidor RADIUS validando o seu certificado. `-RadiusRootCert`é o ficheiro .cer que contém o certificado de raiz que é usado para validar o servidor RADIUS.

Cada dispositivo cliente VPN requer um certificado de cliente instalado. Por vezes, um dispositivo Windows tem vários certificados de cliente. Durante a autenticação, isto pode resultar numa caixa de diálogo pop-up que lista todos os certificados. O utilizador deve então escolher o certificado para utilizar. O certificado correto pode ser filtrado especificando o certificado de raiz a que o certificado de cliente deve ser acorrentado. 

`-ClientRootCert`é o ficheiro .cer que contém o certificado de raiz. É um parâmetro opcional. Se o dispositivo de que pretende ligar tiver apenas um certificado de cliente, não tem de especificar este parâmetro.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="certfiles"></a>1. Gerar ficheiros de configuração de clientes VPN

Gere ficheiros de configuração de cliente VPN para utilização com autenticação de certificado. Pode gerar os ficheiros de configuração do cliente VPN utilizando o seguinte comando:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

A execução do comando devolve um link. Copie e cole o link para um navegador web para baixar VpnClientConfiguration.zip. Desapertar o ficheiro para visualizar as seguintes pastas:

* **WindowsAmd64** e **WindowsX86**: Estas pastas contêm os pacotes de instaladores windows 64 e 32 bits, respectivamente. 
* **GenericDevice**: Esta pasta contém informações gerais que são usadas para criar a sua própria configuração de cliente VPN.

Se já criou ficheiros de configuração do `Get-AzVpnClientConfiguration` cliente, pode recuperá-los utilizando o cmdlet. Mas se fizer alterações na configuração p2S VPN, como o tipo de protocolo VPN ou o tipo de autenticação, a configuração não é atualizada automaticamente. Tem de `New-AzVpnClientConfiguration` executar o cmdlet para criar um novo download de configuração.

Para recuperar ficheiros de configuração de clientes anteriormente gerados, utilize o seguinte comando:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a>2. Configure clientes VPN

Pode configurar os seguintes clientes VPN:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (suportado, ainda não há passos de artigo)

#### <a name="windows-vpn-client-setup"></a><a name="certwincli"></a>Configuração do cliente Do Windows VPN

1. Selecione um pacote de configuração e instale-o no dispositivo cliente. Para uma arquitetura de processador de 64 bits, escolha o pacote instalador **VpnClientSetupAmd64.** Para uma arquitetura de processador de 32 bits, escolha o pacote instalador **VpnClientSetupX86.** Se vir um pop-up SmartScreen, selecione **Mais informações** > **Run de qualquer forma**. Também pode guardar o pacote para instalar noutros computadores cliente.
2. Cada cliente requer um certificado de cliente para autenticação. Instale o certificado de cliente. Para obter informações sobre certificados de cliente, consulte [os certificados do Cliente para o ponto-a-local](vpn-gateway-certificates-point-to-site.md). Para instalar um certificado que foi gerado, consulte [Instalar um certificado nos clientes do Windows](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. No computador cliente, navegue para **as Definições de Rede** e selecione **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga.

#### <a name="mac-os-x-vpn-client-setup"></a><a name="certmaccli"></a>Configuração do cliente VpN Mac (OS X)

Deve criar um perfil separado para cada dispositivo Mac que se conectem à rede virtual Azure. Isto porque estes dispositivos requerem que o certificado de utilizador para autenticação seja especificado no perfil. A pasta **Genérica** tem toda a informação necessária para criar um perfil:

* **VpnSettings.xml** contém definições importantes, tais como endereço do servidor e tipo de túnel.
* **VpnServerRoot.cer** contém o certificado de raiz necessário para validar o gateway VPN durante a configuração da ligação P2S.
* **RadiusServerRoot.cer** contém o certificado de raiz necessário para validar o servidor RADIUS durante a autenticação.

Utilize os seguintes passos para configurar o cliente VPN nativo num Mac para autenticação de certificado:

1. Importe os certificados de raiz **VpnServerRoot** e **RadiusServerRoot** para o seu Mac. Copie cada ficheiro para o seu Mac, clique duas vezes nele e, em seguida, **selecione Adicionar**.

   ![Adicionar o certificado VpnServerRoot](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Adicionar o certificado RadiusServerRoot](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Cada cliente requer um certificado de cliente para autenticação. Instale o certificado de cliente no dispositivo cliente.
3. Abra a caixa de diálogo da **Rede** em função das **Preferências**de Rede . Selecione **+** para criar um novo perfil de ligação ao cliente VPN para uma ligação P2S à rede virtual Azure.

   O valor **da Interface** é **VPN,** e o valor **vpn tipo** é **IKEv2**. Especifique um nome para o perfil na caixa nome de **serviço** e, em seguida, selecione **Criar** para criar o perfil de ligação ao cliente VPN.

   ![Informações sobre interface e nome de serviço](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. Na pasta **Genérica,** a partir do ficheiro **VpnSettings.xml,** copie o valor da etiqueta **VpnServer.** Colar este valor no **Endereço do Servidor** e caixas de **IDENTIFICAção Remotas** do perfil. Deixe a caixa **de identificação local** em branco.

   ![Informações do servidor](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. **Selecione Definições de Autenticação,** e selecione **Certificado**. 

   ![Definições de autenticação](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Clique **em Selecionar** para escolher o certificado que pretende utilizar para autenticação.

   ![Selecionar um certificado para autenticação](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Escolha Uma Identidade** apresenta uma lista de certificados para escolher. Selecione o certificado adequado e, em seguida, selecione **Continuar**.

   ![Lista "Escolha Uma Identidade"](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. Na caixa **de identificação local,** especifique o nome do certificado (a partir do passo 6). Neste exemplo, é **ikev2Client.com.** Em seguida, selecione o botão **Aplicar** para guardar as alterações.

   ![Caixa de identificação local](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. Na caixa de diálogo **da Rede,** selecione **Aplicar** para guardar todas as alterações. Em seguida, selecione **Connect** para iniciar a ligação P2S à rede virtual Azure.

## <a name="working-with-other-authentication-types-or-protocols"></a><a name="otherauth"></a>Trabalhar com outros tipos ou protocolos de autenticação

Para utilizar um tipo de autenticação diferente (por exemplo, OTP), ou para utilizar um protocolo de autenticação diferente (como PEAP-MSCHAPv2 em vez de EAP-MSCHAPv2), deve criar o seu próprio perfil de configuração do cliente VPN. Para criar o perfil, necessita de informações como o endereço IP de gateway da rede virtual, tipo de túnel e rotas de túneis divididos. Pode obter esta informação utilizando os seguintes passos:

1. Utilize `Get-AzVpnClientConfiguration` o cmdlet para gerar a configuração do cliente VPN para EapMSChapv2.

2. Desinexifique o ficheiro VpnClientConfiguration.zip e procure a pasta **GenericDevice.** Ignore as pastas que contêm os instaladores do Windows para arquiteturas de 64 bits e 32 bits.
 
3. A pasta **GenericDevice** contém um ficheiro XML chamado **VpnSettings**. Este ficheiro contém todas as informações necessárias:

   * **VpnServer**: FQDN do gateway Azure VPN. Este é o endereço a que o cliente se liga.
   * **VpnType**: Tipo de túnel que utiliza para ligar.
   * **Percursos**: Rotas que tem de configurar no seu perfil para que apenas o tráfego que está destinado à rede virtual Azure seja enviado sobre o túnel P2S.
   
   A pasta **GenericDevice** também contém um ficheiro .cer chamado **VpnServerRoot**. Este ficheiro contém o certificado de raiz necessário para validar o gateway Azure VPN durante a configuração da ligação P2S. Instale o certificado em todos os dispositivos que se ligarão à rede virtual Azure.

## <a name="next-steps"></a>Passos seguintes

Volte ao artigo para completar a [sua configuração P2S](point-to-site-how-to-radius-ps.md).

Para obter informações sobre resolução de problemas p2S, consulte [ligações ponto-a-local do Troubleshooting Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
