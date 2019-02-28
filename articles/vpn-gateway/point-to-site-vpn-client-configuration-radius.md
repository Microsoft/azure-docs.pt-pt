---
title: 'Criar e instalar os ficheiros de configuração de cliente VPN para ligações de P2S RADIUS: PowerShell: Azure | Microsoft Docs'
description: Crie clientes Windows, Mac OS X e Linux VPN arquivos de configuração para ligações que utilizam a autenticação RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/27/2019
ms.author: cherylmc
ms.openlocfilehash: f59a871297189cfd5082b55a3dbdfd3156a4e501
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985709"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Criar e instalar os ficheiros de configuração de cliente VPN para autenticação P2S RADIUS

Para ligar a uma rede virtual através de ponto a site (P2S), terá de configurar o dispositivo de cliente onde irá estabelecer ligação. Pode criar ligações VPN de P2S a partir de dispositivos de cliente de Linux, Mac OS X e Windows. 

Quando estiver a utilizar a autenticação RADIUS, existem várias opções de autenticação: autenticação de nome de utilizador/palavra-passe, autenticação de certificados e outros tipos de autenticação. A configuração do cliente VPN é diferente para cada tipo de autenticação. Para configurar o cliente VPN, utilize ficheiros de configuração de cliente que contêm as definições necessárias. Este artigo ajuda-o a criar e instalar a configuração de cliente VPN para o tipo de autenticação RADIUS que pretende utilizar.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

O fluxo de trabalho de configuração para autenticação P2S RADIUS é o seguinte:

1. [Configurar o gateway de VPN do Azure para a conetividade P2S](point-to-site-how-to-radius-ps.md).
2. [Configurar o servidor RADIUS para autenticação](point-to-site-how-to-radius-ps.md#radius). 
3. **Obter a configuração do cliente VPN para a opção de autenticação à sua escolha e utilizá-lo para configurar o cliente VPN** (Este artigo).
4. [Concluir a configuração de P2S e ligue-se](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Se existirem quaisquer alterações à configuração de VPN ponto a site depois de gerar o perfil de configuração de cliente VPN, como o tipo de protocolo VPN ou o tipo de autenticação, tem de gerar e instalar uma nova configuração de cliente VPN em dispositivos dos seus utilizadores.
>
>

Para utilizar as secções neste artigo, primeiro decidir que tipo de autenticação que pretende utilizar: nome de usuário/senha, certificado ou outros tipos de autenticação. Cada secção inclui passos para Windows, Mac OS X e Linux (limitados passos disponíveis neste momento).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="adeap"></a>Autenticação de nome de utilizador/palavra-passe

Pode configurar a autenticação de nome de utilizador/palavra-passe para utilizar o Active Directory ou utiliza o Active Directory. Com qualquer um dos cenários, certifique-se de que todos os utilizadores ligados têm as credenciais de nome de utilizador/palavra-passe que podem ser autenticadas através de RADIUS.

Quando configurar a autenticação de nome de utilizador/palavra-passe, só pode criar uma configuração para o protocolo de autenticação de nome de utilizador/palavra-passe do EAP-MSCHAPv2. Os comandos `-AuthenticationMethod` é `EapMSChapv2`.

### <a name="usernamefiles"></a> 1. Gerar ficheiros de configuração de cliente VPN

Pode gerar os arquivos de configuração de cliente VPN com o portal do Azure ou com o Azure PowerShell.

#### <a name="azure-portal"></a>Portal do Azure

1. Navegue para o gateway de rede virtual.
2. Clique em **configuraçãopontoaSiteda**.
3. Clique em **transferir cliente VPN**.
4. Selecione o cliente e preencha quaisquer informações solicitadas.
5. Clique em **transferir** para gerar o ficheiro. zip.
6. O ficheiro. zip irá transferir, normalmente, para a pasta transferências.

#### <a name="azure-powershell"></a>Azure PowerShell

Gere ficheiros de configuração de cliente VPN para utilização com a autenticação de nome de utilizador/palavra-passe. Pode gerar os arquivos de configuração de cliente VPN com o seguinte comando:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Executar o comando devolve uma ligação. Copie e cole a ligação para um navegador da web para transferir **VpnClientConfiguration.zip**. Deszipe o ficheiro para ver as seguintes pastas: 
 
* **WindowsAmd64** e **WindowsX86**: Essas pastas contêm os pacotes de instalador do Windows de 64 bits e 32 bits, respectivamente. 
* **Genérico**: Esta pasta contém informações gerais que utilizar para criar sua própria configuração de cliente VPN. Esta pasta não é necessário para configurações de autenticação de nome de utilizador/palavra-passe.
* **Mac**: Se tiver configurado o IKEv2 quando criou o gateway de rede virtual, verá uma pasta denominada **Mac** que contém um **mobileconfig** ficheiro. Utilize este ficheiro para configurar os clientes Mac.

Se já criou cliente ficheiros de configuração, pode recuperá-los utilizando o `Get-AzVpnClientConfiguration` cmdlet. Mas, se fizer alterações à sua configuração de VPN de P2S, como o tipo de protocolo VPN ou o tipo de autenticação, a configuração não é atualizada automaticamente. Tem de executar o `New-AzVpnClientConfiguration` cmdlet para criar um novo download de configuração.

Para obter os ficheiros de configuração de cliente gerado anteriormente, utilize o seguinte comando:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="setupusername"></a> 2. Configurar os clientes VPN

Pode configurar os seguintes clientes VPN:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux utilizar strongSwan](#adlinuxcli)
 
#### <a name="adwincli"></a>Configuração de cliente de VPN do Windows

Pode utilizar o mesmo pacote de configuração de cliente VPN em cada computador de cliente do Windows, desde que a versão corresponda à arquitetura do cliente. Para obter a lista de sistemas operativos cliente suportados, consulte a [FAQ](vpn-gateway-vpn-faq.md#P2S).

Utilize os seguintes passos para configurar o cliente de VPN do Windows nativo para autenticação de certificados:

1. Selecione os ficheiros de configuração do cliente VPN que correspondem à arquitetura do computador Windows. Para uma arquitetura de processador de 64 bits, escolha o **VpnClientSetupAmd64** pacote do instalador. Para uma arquitetura de processador de 32 bits, escolha o **VpnClientSetupX86** pacote do instalador. 
2. Para instalar o pacote, clique duas vezes nele. Se vir um pop-up do SmartScreen, selecione **mais informações** > **executar mesmo assim**.
3. No computador cliente, navegue até **definições de rede** e selecione **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga. 

#### <a name="admaccli"></a>Instalação de cliente VPN do Mac (OS X)

1. Selecione o **VpnClientSetup mobileconfig** de ficheiros e enviá-lo a cada um dos utilizadores. Pode utilizar o e-mail ou outro método.

2. Localize a **mobileconfig** ficheiros no Mac.

   ![Localização do ficheiro mobileconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Passo opcional - se de que pretende especificar um DNS personalizado, adicione as seguintes linhas para o **mobileconfig** ficheiro:

  ```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        <array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
  ```
4. Faça duplo clique no perfil para instalá-lo e selecione **continuar**. O nome de perfil é igual ao nome da sua rede virtual.

   ![Mensagem de instalação](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Selecione **continuar** para o remetente do perfil de confiança e continuar com a instalação.

   ![Mensagem de confirmação](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Durante a instalação do perfil, tem a opção para especificar o nome de utilizador e palavra-passe para a autenticação do VPN. Não é obrigatório para introduzir estas informações. Se o fizer, as informações são guardadas e utilizadas automaticamente quando iniciar uma ligação. Selecione **instalar** para continuar.

   ![Caixas de nome de utilizador e palavra-passe para VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Introduza um nome de utilizador e palavra-passe para os privilégios necessários para instalar o perfil no seu computador. Selecione **OK**.

   ![Caixas de nome de utilizador e palavra-passe para a instalação do perfil](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. Depois do perfil é instalado, é visível na **perfis** caixa de diálogo. Também é possível abrir a caixa de diálogo mais tarde a partir **preferências do sistema**.

   ![Caixa de diálogo "Perfis"](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. Para aceder a ligação VPN, abra a **rede** caixa de diálogo de **preferências do sistema**.

   ![Ícones em preferências do sistema](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. A ligação VPN é apresentado como **IkeV2 VPN**. Pode alterar o nome ao atualizar o **mobileconfig** ficheiro.

   ![Detalhes da ligação VPN](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Selecione **definições de autenticação**. Selecione **nome de utilizador** na lista e introduzir as suas credenciais. Se introduziu as credenciais anteriormente, em seguida, **nome de utilizador** automaticamente é escolhida na lista e o nome de utilizador e palavra-passe é pré-preenchidas. Selecione **OK** para guardar as definições.

   ![Definições de autenticação](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. De volta a **rede** caixa de diálogo, selecione **aplicar** para guardar as alterações. Para iniciar a ligação, selecione **Connect**.

#### <a name="adlinuxcli"></a>Configuração de cliente de VPN do Linux através de strongSwan

As instruções seguintes foram criadas por meio de strongSwan 5.5.1 no Ubuntu 17.0.4. Telas reais podem ser diferentes, dependendo da versão do Linux e strongSwan.

1. Abra o **Terminal** para instalar **strongSwan** e o seu Gestor de rede ao executar o comando no exemplo. Se receber um erro que está relacionado à `libcharon-extra-plugins`, substitua-o por `strongswan-plugin-eap-mschapv2`.

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Selecione o **Gestor de rede** ícone (up-seta/seta para baixo) e selecione **Editar ligações**.

   ![Seleção de "Editar ligações" no Gestor de rede](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Selecione o **adicionar** botão para criar uma nova ligação.

   ![Botão "Adicionar" para uma ligação](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Selecione **(strongswan) de IPsec/IKEv2** no menu de lista pendente e, em seguida, selecione **criar**. Pode mudar o nome da sua ligação neste passo.

   ![Selecionar o tipo de ligação](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Abra o **Vpnsettings** ficheiro a partir a **genérico** pasta dos ficheiros de configuração de cliente transferidos. Localize a marca chamada `VpnServer` e copie o nome, a partir `azuregateway` e terminando `.cloudapp.net`.

   ![Conteúdo do arquivo Vpnsettings](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Colar este nome para o **endereço** campo de sua nova ligação VPN na **Gateway** secção. Em seguida, selecione o ícone de pasta no final da **certificado** campo, navegue para o **genérica** pasta e selecione o **VpnServerRoot** ficheiro.
7. Na **cliente** secção a ligação, selecione **EAP** para **autenticação**e introduza o nome de utilizador e palavra-passe. Poderá ter de selecionar o ícone de cadeado no lado direito para guardar estas informações. Em seguida, selecione **Guardar**.

   ![Editar definições de ligação](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Selecione o **Gestor de rede** ícone (up-seta/seta para baixo) e coloque o cursor sobre **ligações VPN**. Veja a ligação de VPN que criou. Para iniciar a ligação, selecione-o.

   ![Ligação de "VPN Radius" no Gestor de rede](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>Autenticação de certificados
 
É possível criar o cliente VPN a arquivos de configuração para autenticação de certificados RADIUS, que utiliza o protocolo EAP-TLS. Normalmente, um certificado emitido pela empresa é utilizado para autenticar um usuário para VPN. Certifique-se de que todos os utilizadores ligados têm um certificado instalado nos respetivos dispositivos e que o servidor RADIUS pode validar o certificado.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Os comandos `-AuthenticationMethod` é `EapTls`. Durante a autenticação de certificado, o cliente valida o servidor RADIUS ao validar o seu certificado. `-RadiusRootCert` é o ficheiro. cer que contém o certificado de raiz que é usado para validar o servidor RADIUS.

Cada dispositivo de cliente VPN requer um certificado de cliente instalado. Às vezes, um dispositivo do Windows tem vários certificados de cliente. Durante a autenticação, isso pode resultar numa caixa de diálogo pop-up que lista todos os certificados. O utilizador, em seguida, tem de escolher o certificado a utilizar. O certificado correto pode ser filtrado, especificando o certificado de raiz que o certificado de cliente deve estar ligado a. 

`-ClientRootCert` é o ficheiro. cer que contém o certificado de raiz. É um parâmetro opcional. Se o dispositivo que pretende ligar a partir de tiver apenas um certificado de cliente, não tem de especificar este parâmetro.

### <a name="certfiles"></a>1. Gerar ficheiros de configuração de cliente VPN

Gere ficheiros de configuração de cliente VPN para utilização com a autenticação de certificado. Pode gerar os arquivos de configuração de cliente VPN com o seguinte comando:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Executar o comando devolve uma ligação. Copie e cole a ligação para um navegador da web para transferir VpnClientConfiguration.zip. Deszipe o ficheiro para ver as seguintes pastas:

* **WindowsAmd64** e **WindowsX86**: Essas pastas contêm os pacotes de instalador do Windows de 64 bits e 32 bits, respectivamente. 
* **GenericDevice**: Esta pasta contém informações gerais, que são utilizadas para criar sua própria configuração de cliente VPN.

Se já criou cliente ficheiros de configuração, pode recuperá-los utilizando o `Get-AzVpnClientConfiguration` cmdlet. Mas, se fizer alterações à sua configuração de VPN de P2S, como o tipo de protocolo VPN ou o tipo de autenticação, a configuração não é atualizada automaticamente. Tem de executar o `New-AzVpnClientConfiguration` cmdlet para criar um novo download de configuração.

Para obter os ficheiros de configuração de cliente gerado anteriormente, utilize o seguinte comando:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="setupusername"></a> 2. Configurar os clientes VPN

Pode configurar os seguintes clientes VPN:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (suportado, nenhum artigo passos ainda)

#### <a name="certwincli"></a>Configuração de cliente de VPN do Windows

1. Selecione um pacote de configuração e instale-o no dispositivo cliente. Para uma arquitetura de processador de 64 bits, escolha o **VpnClientSetupAmd64** pacote do instalador. Para uma arquitetura de processador de 32 bits, escolha o **VpnClientSetupX86** pacote do instalador. Se vir um pop-up do SmartScreen, selecione **mais informações** > **executar mesmo assim**. Também pode guardar o pacote para instalar noutros computadores cliente.
2. Cada cliente necessita de um certificado de cliente para autenticação. Instale o certificado de cliente. Para obter informações sobre certificados de cliente, consulte [certificados de cliente para ligações ponto a site](vpn-gateway-certificates-point-to-site.md). Para instalar um certificado que foi gerado, consulte [instale um certificado em clientes Windows](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. No computador cliente, navegue até **definições de rede** e selecione **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga.

#### <a name="certmaccli"></a>Instalação de cliente VPN do Mac (OS X)

Tem de criar um perfil separado para cada dispositivo Mac que se liga a rede virtual do Azure. Isto acontece porque esses dispositivos requerem o certificado de utilizador para autenticação seja especificado no perfil. O **genérico** pasta tem todas as informações necessárias para criar um perfil:

* **Vpnsettings** contém configurações importantes, como o tipo de endereço e túnel do servidor.
* **VpnServerRoot.cer** contém o certificado de raiz que é necessário para validar o gateway de VPN durante a configuração de ligação de P2S.
* **RadiusServerRoot.cer** contém o certificado de raiz que é necessário para validar o servidor RADIUS durante a autenticação.

Utilize os seguintes passos para configurar o cliente VPN nativo no Mac para autenticação de certificados:

1. Importar os **VpnServerRoot** e **RadiusServerRoot** raiz certificados para o seu Mac. Copiar cada ficheiro para o seu Mac, clique duas vezes nele e, em seguida, selecione **adicionar**.

   ![A adicionar o certificado de VpnServerRoot](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![A adicionar o certificado de RadiusServerRoot](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Cada cliente necessita de um certificado de cliente para autenticação. Instale o certificado de cliente no dispositivo cliente.
3. Abra o **rede** caixa de diálogo sob **preferências de rede**. Selecione **+** para criar um novo perfil de ligação de cliente VPN para uma ligação de P2S para a rede virtual do Azure.

   O **Interface** valor é **VPN**e o **tipo de VPN** valor é **IKEv2**. Especifique um nome para o perfil no **nome do serviço** caixa e, em seguida, selecione **criar** para criar o perfil de ligação de cliente VPN.

   ![Informações de nome de interface e o serviço](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. Na **genérico** pasta, da **Vpnsettings** ficheiro, copie o **VpnServer** valor de etiqueta. Cole este valor na **endereço do servidor** e **remoto ID** caixas do perfil. Deixe o **Local ID** caixa em branco.

   ![Informações do servidor](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Selecione **definições de autenticação**e selecione **certificado**. 

   ![Definições de autenticação](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Clique em **selecione** para escolher o certificado que pretende utilizar para autenticação.

   ![Selecionar um certificado para autenticação](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Escolha uma identidade** apresenta uma lista de certificados para a sua escolha. Selecione o certificado adequado e, em seguida, selecione **continuar**.

   ![Lista de "Escolher uma identidade"](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. Na **Local ID** caixa, especifique o nome do certificado (a partir do passo 6). Neste exemplo, ele possui **ikev2Client.com**. Em seguida, selecione o **aplicar** botão para guardar as alterações.

   ![Caixa de "Local ID"](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. Na **rede** caixa de diálogo, selecione **aplicar** para guardar todas as alterações. Em seguida, selecione **Connect** para iniciar a ligação de P2S para a rede virtual do Azure.

## <a name="otherauth"></a>Trabalhar com outros tipos de autenticação ou protocolos

Para utilizar um tipo de autenticação diferentes (por exemplo, OTP), ou para utilizar um protocolo de autenticação diferentes (por exemplo, o PEAP-MSCHAPv2 em vez de EAP-MSCHAPv2), tem de criar seu próprio perfil de configuração de cliente VPN. Para criar o perfil, precisa de informações como o endereço IP do gateway de rede virtual, o tipo de túnel e rotas de divisão do túnel. Pode obter estas informações ao utilizar os seguintes passos:

1. Utilize o `Get-AzVpnClientConfiguration` cmdlet para gerar a configuração de cliente VPN para EapMSChapv2.

2. Deszipe o ficheiro de VpnClientConfiguration.zip e procure o **GenericDevice** pasta. Ignore as pastas que contêm os programas de instalação do Windows para arquiteturas de 64 bits e 32 bits.
 
3. O **GenericDevice** pasta contém um arquivo XML denominado **VpnSettings**. Esse arquivo contém todas as informações necessárias:

   * **VpnServer**: FQDN do gateway de VPN do Azure. Este é o endereço que o cliente se liga a.
   * **VpnType**: Tipo de túnel que utilizar para ligar.
   * **Rotas**: Rotas que tem de configurar no seu perfil, para que apenas o tráfego que está vinculado a rede virtual do Azure é enviado através do túnel de P2S.
   
   O **GenericDevice** pasta também contém um ficheiro. cer, chamado **VpnServerRoot**. Este ficheiro contém o certificado de raiz que é necessário para validar o gateway de VPN do Azure durante a configuração de ligação de P2S. Instale o certificado em todos os dispositivos que irão ligar à rede virtual do Azure.

## <a name="next-steps"></a>Passos Seguintes

Devolver o artigo para [concluir a configuração de P2S](point-to-site-how-to-radius-ps.md).

Para P2S resolução de problemas de informações, consulte [ligações de ponto a site de resolução de problemas do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
