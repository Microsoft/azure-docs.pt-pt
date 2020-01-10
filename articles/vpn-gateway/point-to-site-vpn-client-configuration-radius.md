---
title: 'Gateway de VPN do Azure: criar & instalar arquivos de configuração de cliente VPN-conexões RADIUS P2S'
description: Crie arquivos de configuração de cliente VPN Windows, Mac OS X e Linux para conexões que usam autenticação RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/27/2019
ms.author: cherylmc
ms.openlocfilehash: 36343a37e2f6515d6ed7a98ea325d6f00fdc02e9
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834016"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Criar e instalar arquivos de configuração de cliente VPN para autenticação RADIUS P2S

Para se conectar a uma rede virtual por meio de ponto a site (P2S), você precisa configurar o dispositivo do cliente do qual você se conectará. Você pode criar conexões VPN P2S de dispositivos de cliente Windows, Mac OS X e Linux. 

Quando você está usando a autenticação RADIUS, há várias opções de autenticação: autenticação de nome de usuário/senha, autenticação de certificado e outros tipos de autenticação. A configuração do cliente VPN é diferente para cada tipo de autenticação. Para configurar o cliente VPN, você usa os arquivos de configuração do cliente que contêm as configurações necessárias. Este artigo ajuda você a criar e instalar a configuração de cliente VPN para o tipo de autenticação RADIUS que você deseja usar.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

O fluxo de trabalho de configuração para autenticação RADIUS P2S é o seguinte:

1. [Configure o gateway de VPN do Azure para conectividade P2S](point-to-site-how-to-radius-ps.md).
2. [Configure o servidor RADIUS para autenticação](point-to-site-how-to-radius-ps.md#radius). 
3. **Obtenha a configuração de cliente VPN para a opção de autenticação de sua escolha e use-a para configurar o cliente VPN** (este artigo).
4. [Conclua a configuração do P2S e conecte-se](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Se houver alterações na configuração de VPN ponto a site depois de gerar o perfil de configuração de cliente VPN, como o tipo de protocolo VPN ou o tipo de autenticação, você deverá gerar e instalar uma nova configuração de cliente VPN nos dispositivos dos usuários.
>
>

Para usar as seções deste artigo, primeiro decida qual tipo de autenticação você deseja usar: nome de usuário/senha, certificado ou outros tipos de autenticação. Cada seção tem etapas para Windows, Mac OS X e Linux (etapas limitadas disponíveis no momento).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="adeap"></a>Autenticação de nome de usuário/senha

Você pode configurar a autenticação de nome de usuário/senha para usar o Active Directory ou não usar Active Directory. Com qualquer um dos cenários, certifique-se de que todos os usuários conectados tenham credenciais de nome de usuário/senha que possam ser autenticados por meio do RADIUS.

Ao configurar a autenticação de nome de usuário/senha, você só pode criar uma configuração para o protocolo de autenticação de nome de usuário/senha EAP-MSCHAPv2. Nos comandos, `-AuthenticationMethod` é `EapMSChapv2`.

### <a name="usernamefiles"></a>1. gerar arquivos de configuração de cliente VPN

Você pode gerar os arquivos de configuração de cliente VPN usando o portal do Azure ou usando Azure PowerShell.

#### <a name="azure-portal"></a>Portal do Azure

1. Navegue até o gateway de rede virtual.
2. Clique em **configuração ponto a site**.
3. Clique em **baixar cliente VPN**.
4. Selecione o cliente e preencha todas as informações solicitadas.
5. Clique em **baixar** para gerar o arquivo. zip.
6. O arquivo. zip será baixado, normalmente para a pasta downloads.

#### <a name="azure-powershell"></a>Azure PowerShell

Gerar arquivos de configuração de cliente VPN para uso com autenticação de nome de usuário/senha. Você pode gerar os arquivos de configuração de cliente VPN usando o seguinte comando:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
A execução do comando retorna um link. Copie e cole o link em um navegador da Web para baixar **VpnClientConfiguration. zip**. Descompacte o arquivo para exibir as seguintes pastas: 
 
* **WindowsAmd64** e **WindowsX86**: essas pastas contêm os pacotes do instalador do Windows 64 bits e 32 bits, respectivamente. 
* **Genérico**: essa pasta contém informações gerais que você usa para criar sua própria configuração de cliente VPN. Você não precisa dessa pasta para as configurações de autenticação de nome de usuário/senha.
* **Mac**: se você configurou o IKEv2 ao criar o gateway de rede virtual, verá uma pasta chamada **Mac** que contém um arquivo **mobileconfig** . Você usa esse arquivo para configurar clientes Mac.

Se você já tiver criado os arquivos de configuração do cliente, poderá recuperá-los usando o cmdlet `Get-AzVpnClientConfiguration`. Mas se você fizer alterações na configuração de VPN do P2S, como o tipo de protocolo VPN ou o tipo de autenticação, a configuração não será atualizada automaticamente. Você deve executar o cmdlet `New-AzVpnClientConfiguration` para criar um novo download de configuração.

Para recuperar os arquivos de configuração de cliente gerados anteriormente, use o seguinte comando:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="setupusername"></a>2. configurar clientes VPN

Você pode configurar os seguintes clientes VPN:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux usando strongSwan](#adlinuxcli)
 
#### <a name="adwincli"></a>Instalação do cliente VPN do Windows

Você pode usar o mesmo pacote de configuração de cliente VPN em cada computador cliente com Windows, desde que a versão corresponda à arquitetura do cliente. Para obter a lista de sistemas operacionais cliente com suporte, consulte as [perguntas frequentes](vpn-gateway-vpn-faq.md#P2S).

Use as etapas a seguir para configurar o cliente VPN do Windows nativo para autenticação de certificado:

1. Selecione os ficheiros de configuração do cliente VPN que correspondem à arquitetura do computador Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador do **VpnClientSetupAmd64** . Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador do **VpnClientSetupX86** . 
2. Para instalar o pacote, clique duas vezes nele. Se você vir um pop-up do SmartScreen, selecione **mais informações** > **executar mesmo assim**.
3. No computador cliente, navegue até **configurações de rede** e selecione **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga. 

#### <a name="admaccli"></a>Instalação do cliente VPN Mac (OS X)

1. Selecione o arquivo **VpnClientSetup mobileconfig** e envie-o para cada um dos usuários. Você pode usar o email ou outro método.

2. Localize o arquivo **mobileconfig** no Mac.

   ![Local do arquivo mobileconfig](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Etapa opcional-se você quiser especificar um DNS personalizado, adicione as seguintes linhas ao arquivo **mobileconfig** :

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
4. Clique duas vezes no perfil para instalá-lo e selecione **continuar**. O nome do perfil é o mesmo que o nome da sua rede virtual.

   ![Mensagem de instalação](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Selecione **continuar** para confiar no remetente do perfil e prosseguir com a instalação.

   ![Mensagem de confirmação](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Durante a instalação do perfil, você tem a opção de especificar o nome de usuário e a senha para autenticação de VPN. Não é obrigatório inserir essas informações. Se você fizer isso, as informações serão salvas e usadas automaticamente quando você iniciar uma conexão. Selecione **instalar** para continuar.

   ![Caixas de nome de usuário e senha para VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Insira um nome de usuário e senha para os privilégios necessários para instalar o perfil em seu computador. Selecione **OK**.

   ![Caixas de nome de usuário e senha para instalação de perfil](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. Depois que o perfil é instalado, ele fica visível na caixa de diálogo **perfis** . Você também pode abrir essa caixa de diálogo mais tarde em **preferências do sistema**.

   ![Caixa de diálogo "perfis"](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. Para acessar a conexão VPN, abra a caixa de diálogo **rede** em **preferências do sistema**.

   ![Ícones em preferências do sistema](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. A conexão VPN aparece como **IkeV2-VPN**. Você pode alterar o nome atualizando o arquivo **mobileconfig** .

    ![Detalhes da conexão VPN](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Selecione **configurações de autenticação**. Selecione **nome de usuário** na lista e insira suas credenciais. Se você tiver inserido as credenciais anteriormente, o **nome de usuário** será escolhido automaticamente na lista, e o nome de usuário e a senha serão preenchidos previamente. Selecione **OK** para salvar as configurações.

    ![definições de autenticação](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. De volta à caixa de diálogo **rede** , selecione **aplicar** para salvar as alterações. Para iniciar a conexão, selecione **conectar**.

#### <a name="adlinuxcli"></a>Instalação do cliente VPN do Linux por meio do strongSwan

As instruções a seguir foram criadas por meio de strongSwan 5.5.1 no Ubuntu 17.0.4. As telas reais podem ser diferentes, dependendo da sua versão do Linux e do strongSwan.

1. Abra o **terminal** para instalar o **StrongSwan** e seu Gerenciador de rede executando o comando no exemplo. Se você receber um erro relacionado a `libcharon-extra-plugins`, substitua-o por `strongswan-plugin-eap-mschapv2`.

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Selecione o ícone **Gerenciador de rede** (seta para cima/seta para baixo) e selecione **Editar conexões**.

   ![Seleção de "Editar conexões" no Gerenciador de rede](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Selecione o botão **Adicionar** para criar uma nova conexão.

   ![Botão "Adicionar" para uma conexão](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Selecione **IPSec/IKEv2 (strongSwan)** no menu suspenso e, em seguida, selecione **criar**. Você pode renomear a conexão nesta etapa.

   ![Selecionando o tipo de conexão](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Abra o arquivo **VpnSettings. xml** na pasta **genérica** dos arquivos de configuração do cliente baixados. Localize a marca chamada `VpnServer` e copie o nome, começando com `azuregateway` e terminando com `.cloudapp.net`.

   ![Conteúdo do arquivo VpnSettings. xml](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Cole esse nome no campo **endereço** da nova conexão VPN na seção **Gateway** . Em seguida, selecione o ícone de pasta no final do campo **certificado** , navegue até a pasta **genérica** e selecione o arquivo **VpnServerRoot** .
7. Na seção **cliente** da conexão, selecione **EAP** para **autenticação**e insira seu nome de usuário e senha. Talvez seja necessário selecionar o ícone de cadeado à direita para salvar essas informações. Em seguida, selecione **Guardar**.

   ![Editando configurações de conexão](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Selecione o ícone **Gerenciador de rede** (seta para cima/seta para baixo) e passe o mouse sobre **conexões VPN**. Você verá a conexão VPN que você criou. Para iniciar a conexão, selecione-a.

   ![Conexão de "VPN RADIUS" no Gerenciador de rede](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>Autenticação de certificado
 
Você pode criar arquivos de configuração de cliente VPN para autenticação de certificado RADIUS que usa o protocolo EAP-TLS. Normalmente, um certificado emitido pela empresa é usado para autenticar um usuário para VPN. Verifique se todos os usuários que estão se conectando têm um certificado instalado em seus dispositivos e se o servidor RADIUS pode validar o certificado.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Nos comandos, `-AuthenticationMethod` é `EapTls`. Durante a autenticação do certificado, o cliente valida o servidor RADIUS validando seu certificado. `-RadiusRootCert` é o arquivo. cer que contém o certificado raiz usado para validar o servidor RADIUS.

Cada dispositivo cliente VPN requer um certificado de cliente instalado. Às vezes, um dispositivo Windows tem vários certificados de cliente. Durante a autenticação, isso pode resultar em uma caixa de diálogo pop-up que lista todos os certificados. O usuário deve, então, escolher o certificado a ser usado. O certificado correto pode ser filtrado especificando-se o certificado raiz ao qual o certificado do cliente deve se encadear. 

`-ClientRootCert` é o arquivo. cer que contém o certificado raiz. É um parâmetro opcional. Se o dispositivo do qual você deseja se conectar tiver apenas um certificado de cliente, você não precisará especificar esse parâmetro.

### <a name="certfiles"></a>1. gerar arquivos de configuração de cliente VPN

Gerar arquivos de configuração de cliente VPN para uso com autenticação de certificado. Você pode gerar os arquivos de configuração de cliente VPN usando o seguinte comando:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

A execução do comando retorna um link. Copie e cole o link em um navegador da Web para baixar VpnClientConfiguration. zip. Descompacte o arquivo para exibir as seguintes pastas:

* **WindowsAmd64** e **WindowsX86**: essas pastas contêm os pacotes do instalador do Windows 64 bits e 32 bits, respectivamente. 
* **GenericDevice**: essa pasta contém informações gerais que são usadas para criar sua própria configuração de cliente VPN.

Se você já tiver criado os arquivos de configuração do cliente, poderá recuperá-los usando o cmdlet `Get-AzVpnClientConfiguration`. Mas se você fizer alterações na configuração de VPN do P2S, como o tipo de protocolo VPN ou o tipo de autenticação, a configuração não será atualizada automaticamente. Você deve executar o cmdlet `New-AzVpnClientConfiguration` para criar um novo download de configuração.

Para recuperar os arquivos de configuração de cliente gerados anteriormente, use o seguinte comando:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="setupusername"></a>2. configurar clientes VPN

Você pode configurar os seguintes clientes VPN:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (com suporte, ainda não há etapas do artigo)

#### <a name="certwincli"></a>Instalação do cliente VPN do Windows

1. Selecione um pacote de configuração e instale-o no dispositivo cliente. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador do **VpnClientSetupAmd64** . Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador do **VpnClientSetupX86** . Se você vir um pop-up do SmartScreen, selecione **mais informações** > **executar mesmo assim**. Também pode guardar o pacote para instalar noutros computadores cliente.
2. Cada cliente requer um certificado de cliente para autenticação. Instale o certificado do cliente. Para obter informações sobre certificados de cliente, consulte [certificados de cliente para ponto a site](vpn-gateway-certificates-point-to-site.md). Para instalar um certificado que foi gerado, consulte [instalar um certificado em clientes Windows](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. No computador cliente, navegue até **configurações de rede** e selecione **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga.

#### <a name="certmaccli"></a>Instalação do cliente VPN Mac (OS X)

Você deve criar um perfil separado para cada dispositivo Mac que se conecta à rede virtual do Azure. Isso ocorre porque esses dispositivos exigem que o certificado do usuário para a autenticação seja especificado no perfil. A pasta **genérico** tem todas as informações necessárias para criar um perfil:

* O **VpnSettings. xml** contém configurações importantes, como endereço do servidor e tipo de túnel.
* **VpnServerRoot. cer** contém o certificado raiz necessário para validar o gateway de VPN durante a configuração de conexão do P2S.
* **RadiusServerRoot. cer** contém o certificado raiz necessário para validar o servidor RADIUS durante a autenticação.

Use as etapas a seguir para configurar o cliente VPN nativo em um Mac para autenticação de certificado:

1. Importe os certificados raiz **VpnServerRoot** e **RadiusServerRoot** para o seu Mac. Copie cada arquivo para seu Mac, clique duas vezes nele e, em seguida, selecione **Adicionar**.

   ![Adicionando o certificado VpnServerRoot](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Adicionando o certificado RadiusServerRoot](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Cada cliente requer um certificado de cliente para autenticação. Instale o certificado do cliente no dispositivo cliente.
3. Abra a caixa de diálogo **rede** em **preferências de rede**. Selecione **+** para criar um novo perfil de conexão de cliente VPN para uma conexão P2S com a rede virtual do Azure.

   O valor da **interface** é **VPN**e o valor do **tipo de VPN** é **IKEv2**. Especifique um nome para o perfil na caixa **nome do serviço** e, em seguida, selecione **criar** para criar o perfil de conexão de cliente VPN.

   ![Informações de nome de interface e serviço](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. Na pasta **genérica** , no arquivo **VpnSettings. xml** , copie o valor da marca **VpnServer** . Cole esse valor nas caixas **endereço do servidor** e **ID remota** do perfil. Deixe a caixa **ID local** em branco.

   ![Informações do servidor](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Selecione **configurações de autenticação**e selecione **certificado**. 

   ![definições de autenticação](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Clique em **selecionar** para escolher o certificado que você deseja usar para autenticação.

   ![Selecionando um certificado para autenticação](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Escolher uma identidade** exibe uma lista de certificados para você escolher. Selecione o certificado apropriado e, em seguida, selecione **continuar**.

   ![Lista "escolher uma identidade"](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. Na caixa **ID local** , especifique o nome do certificado (da etapa 6). Neste exemplo, é **ikev2Client.com**. Em seguida, selecione o botão **aplicar** para salvar as alterações.

   ![Caixa "ID local"](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. Na caixa de diálogo **rede** , selecione **aplicar** para salvar todas as alterações. Em seguida, selecione **conectar** para iniciar a conexão P2S com a rede virtual do Azure.

## <a name="otherauth"></a>Trabalhando com outros tipos de autenticação ou protocolos

Para usar um tipo de autenticação diferente (por exemplo, OTP) ou para usar um protocolo de autenticação diferente (como PEAP-MSCHAPv2 em vez de EAP-MSCHAPv2), você deve criar seu próprio perfil de configuração de cliente VPN. Para criar o perfil, você precisa de informações como o endereço IP do gateway de rede virtual, o tipo de túnel e as rotas de túnel dividido. Você pode obter essas informações usando as seguintes etapas:

1. Use o cmdlet `Get-AzVpnClientConfiguration` para gerar a configuração de cliente VPN para EapMSChapv2.

2. Descompacte o arquivo VpnClientConfiguration. zip e procure a pasta **GenericDevice** Ignore as pastas que contêm os instaladores do Windows para as arquiteturas de 64 bits e 32 bits.
 
3. A pasta **GenericDevice** contém um arquivo XML chamado **VpnSettings**. Esse arquivo contém todas as informações necessárias:

   * **VpnServer**: FQDN do gateway de VPN do Azure. Esse é o endereço ao qual o cliente se conecta.
   * **VpnType**: tipo de túnel que você usa para se conectar.
   * **Rotas**: rotas que você precisa configurar em seu perfil para que somente o tráfego associado à rede virtual do Azure seja enviado pelo túnel P2S.
   
   A pasta **GenericDevice** também contém um arquivo. cer chamado **VpnServerRoot**. Esse arquivo contém o certificado raiz necessário para validar o gateway de VPN do Azure durante a configuração de conexão do P2S. Instale o certificado em todos os dispositivos que serão conectados à rede virtual do Azure.

## <a name="next-steps"></a>Passos seguintes

Retorne ao artigo para [concluir a configuração do P2S](point-to-site-how-to-radius-ps.md).

Para obter informações de solução de problemas do P2S, consulte [Solucionando problemas de conexões ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
