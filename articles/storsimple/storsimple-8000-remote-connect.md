---
title: Conectar-se remotamente ao seu dispositivo StorSimple
description: Explica como configurar seu dispositivo para gerenciamento remoto e como se conectar a Windows PowerShell para StorSimple via HTTP ou HTTPS.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70d0246debc532260d287104bacea2f15c1b94d2
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277285"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Conectar remotamente ao seu dispositivo StorSimple série 8000

## <a name="overview"></a>Visão geral

Você pode se conectar remotamente ao seu dispositivo por meio do Windows PowerShell. Ao se conectar dessa forma, você não verá um menu. (Você verá um menu somente se usar o console serial no dispositivo para se conectar.) Com a comunicação remota do Windows PowerShell, você se conecta a um runspace específico. Você também pode especificar o idioma de exibição.

Para obter mais informações sobre como usar a comunicação remota do Windows PowerShell para gerenciar seu dispositivo, acesse [usar Windows PowerShell para StorSimple para administrar seu dispositivo StorSimple](storsimple-8000-windows-powershell-administration.md).

Este tutorial explica como configurar seu dispositivo para gerenciamento remoto e como se conectar ao Windows PowerShell para StorSimple. Você pode usar HTTP ou HTTPS para se conectar remotamente por meio do Windows PowerShell. No entanto, quando você estiver decidindo como se conectar ao Windows PowerShell para StorSimple, considere as seguintes informações:

* Conectar-se diretamente ao console serial do dispositivo é seguro, mas conectar-se ao console serial por meio de comutadores de rede não é. Tenha cuidado com o risco de segurança ao se conectar ao console serial do dispositivo por meio de comutadores de rede.
* A conexão por meio de uma sessão HTTP pode oferecer mais segurança do que a conexão por meio do console serial pela rede. Embora esse não seja o método mais seguro, ele é aceitável em redes confiáveis.
* Conectar-se por meio de uma sessão HTTPS com um certificado autoassinado é a opção mais segura e recomendada.

Você pode se conectar remotamente à interface do Windows PowerShell. No entanto, o acesso remoto ao seu dispositivo StorSimple por meio da interface do Windows PowerShell não está habilitado por padrão. Você deve habilitar o gerenciamento remoto no dispositivo primeiro e, em seguida, no cliente que é usado para acessar seu dispositivo.

As etapas descritas neste artigo foram executadas em um sistema de host que executa o Windows Server 2012 R2.

## <a name="connect-through-http"></a>Conectar por meio de HTTP

Conectar-se a Windows PowerShell para StorSimple por meio de uma sessão HTTP oferece mais segurança do que conectar-se por meio do console serial do seu dispositivo StorSimple. Embora esse não seja o método mais seguro, ele é aceitável em redes confiáveis.

Você pode usar o portal do Azure ou o console serial para configurar o gerenciamento remoto. Selecione um dos seguintes procedimentos:

* Usar o portal do Azure para habilitar o gerenciamento remoto via HTTP
* [Usar o console serial para habilitar o gerenciamento remoto via HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Depois de habilitar o gerenciamento remoto, use o procedimento a seguir para preparar o cliente para uma conexão remota.

* [Preparar o cliente para conexão remota](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Usar o portal do Azure para habilitar o gerenciamento remoto via HTTP

Execute as etapas a seguir no portal do Azure para habilitar o gerenciamento remoto via HTTP.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Para habilitar o gerenciamento remoto por meio do portal do Azure

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Selecione **dispositivos** e, em seguida, selecione e clique no dispositivo que deseja configurar para gerenciamento remoto. Vá para **configurações do dispositivo > segurança**.
2. Na folha **configurações de segurança** , clique em **gerenciamento remoto**.
3. Na folha **gerenciamento remoto** , defina **habilitar gerenciamento remoto** como **Sim**.
4. Pode agora optar por ligar através de HTTP. (O padrão é conectar-se via HTTPS.) Verifique se o HTTP está selecionado.
   
   > [!NOTE]
   > A ligação através de HTTP é aceitável apenas em redes fidedignas.
   
5. Clique em **salvar** e, quando a confirmação for solicitada, selecione **Sim**.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Usar o console serial para habilitar o gerenciamento remoto via HTTP
Execute as etapas a seguir no console serial do dispositivo para habilitar o gerenciamento remoto.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para habilitar o gerenciamento remoto por meio do console serial do dispositivo
1. No menu do console serial, selecione a opção 1. Para obter mais informações sobre como usar o console serial no dispositivo, acesse [conectar-se a Windows PowerShell para StorSimple por meio do console serial do dispositivo](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. No prompt, digite: `Enable-HcsRemoteManagement –AllowHttp`
3. Você é notificado sobre as vulnerabilidades de segurança do uso de HTTP para se conectar ao dispositivo. Quando solicitado, confirme digitando **Y**.
4. Verifique se o HTTP está habilitado digitando: `Get-HcsSystem`
5. Verifique se o campo **RemoteManagementMode** mostra **HttpsAndHttpEnabled**. A ilustração a seguir mostra essas configurações em reemitidamente.
   
     ![HTTPS serial e HTTP habilitados](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Preparar o cliente para conexão remota
Execute as etapas a seguir no cliente para habilitar o gerenciamento remoto.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Para preparar o cliente para conexão remota
1. Inicie uma sessão do Windows PowerShell como administrador. Se você estiver usando um cliente do Windows 10, por padrão, o Serviço Gerenciamento Remoto do Windows será definido como manual. Talvez seja necessário iniciar o serviço digitando:

    `Start-Service WinRM`
    
2. Digite o seguinte comando para adicionar o endereço IP do dispositivo StorSimple à lista de hosts confiáveis do cliente:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Substitua <*device_ip*> pelo endereço IP do seu dispositivo; por exemplo: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Digite o seguinte comando para salvar as credenciais do dispositivo em uma variável: 
   
    ```
    $cred = Get-Credential
    ```
    
4. Na caixa de diálogo que aparece:
   
   1. Digite o nome de usuário neste formato: *device_ip \ssadmin*.
   2. Digite a senha de administrador do dispositivo que foi definida quando o dispositivo foi configurado com o assistente de instalação. A senha padrão é *password1*.
5. Inicie uma sessão do Windows PowerShell no dispositivo digitando este comando:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Para criar uma sessão do Windows PowerShell para uso com o dispositivo virtual StorSimple, acrescente o `–Port` parâmetro e especifique a porta pública que você configurou na comunicação remota para o dispositivo virtual StorSimple.
   
   
Neste ponto, você deve ter uma sessão remota do Windows PowerShell ativa para o dispositivo.
   
![Comunicação remota do PowerShell usando HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Conectar-se por meio de HTTPS

Conectar-se a Windows PowerShell para StorSimple por meio de uma sessão HTTPS é o método mais seguro e recomendado de se conectar remotamente ao seu dispositivo de Microsoft Azure StorSimple. Os procedimentos a seguir explicam como configurar o console serial e os computadores cliente para que você possa usar HTTPS para se conectar ao Windows PowerShell para StorSimple.

Você pode usar o portal do Azure ou o console serial para configurar o gerenciamento remoto. Selecione um dos seguintes procedimentos:

* Usar o portal do Azure para habilitar o gerenciamento remoto via HTTPS
* [Usar o console serial para habilitar o gerenciamento remoto via HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Depois de habilitar o gerenciamento remoto, use os procedimentos a seguir para preparar o host para um gerenciamento remoto e conectar-se ao dispositivo do host remoto.

* [Preparar o host para gerenciamento remoto](#prepare-the-host-for-remote-management)
* [Conectar-se ao dispositivo do host remoto](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Usar o portal do Azure para habilitar o gerenciamento remoto via HTTPS

Execute as etapas a seguir no portal do Azure para habilitar o gerenciamento remoto via HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Para habilitar o gerenciamento remoto via HTTPS do portal do Azure

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Selecione **dispositivos** e, em seguida, selecione e clique no dispositivo que deseja configurar para gerenciamento remoto. Vá para **configurações do dispositivo > segurança**.
2. Na folha **configurações de segurança** , clique em **gerenciamento remoto**.
3. Defina **Ativar Gestão Remota** como **Sim**.
4. Agora você pode optar por se conectar usando HTTPS. (O padrão é conectar-se via HTTPS.) Certifique-se de que HTTPS esteja selecionado.
5. Clique em... e clique em **baixar certificado de gerenciamento remoto**. Especifique um local para salvar esse arquivo. Você precisa instalar esse certificado no computador host ou cliente que será usado para se conectar ao dispositivo.
6. Clique em **salvar** e em **Sim** quando a confirmação for solicitada.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Usar o console serial para habilitar o gerenciamento remoto via HTTPS

Execute as etapas a seguir no console serial do dispositivo para habilitar o gerenciamento remoto.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para habilitar o gerenciamento remoto por meio do console serial do dispositivo
1. No menu do console serial, selecione a opção 1. Para obter mais informações sobre como usar o console serial no dispositivo, acesse [conectar-se a Windows PowerShell para StorSimple por meio do console serial do dispositivo](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. No prompt, digite:
   
     `Enable-HcsRemoteManagement`
   
    Isso deve habilitar o HTTPS em seu dispositivo.
3. Verifique se o HTTPS foi habilitado digitando: 
   
     `Get-HcsSystem`
   
    Certifique-se de que o campo **RemoteManagementMode** mostra **HttpsEnabled**. A ilustração a seguir mostra essas configurações em reemitidamente.
   
     ![HTTPS serial habilitado](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Na saída de `Get-HcsSystem`, copie o número de série do dispositivo e salve-o para uso posterior.
   
   > [!NOTE]
   > O número de série é mapeado para o nome CN no certificado.
   
5. Obtenha um certificado de gerenciamento remoto digitando: 
   
     `Get-HcsRemoteManagementCert`
   
    Um certificado semelhante ao seguinte será exibido.
   
    ![Obter certificado de gerenciamento remoto](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Copie as informações no certificado de **-----iniciar o-----de certificado** para **----------de certificado final** em um editor de texto, como o bloco de notas, e salve-o como um arquivo. cer. (Você vai copiar esse arquivo para o host remoto ao preparar o host.)
   
   > [!NOTE]
   > Para gerar um novo certificado, use o cmdlet `Set-HcsRemoteManagementCert`.
   
### <a name="prepare-the-host-for-remote-management"></a>Preparar o host para gerenciamento remoto

Para preparar o computador host para uma conexão remota que usa uma sessão HTTPS, execute os seguintes procedimentos:

* [Importe o arquivo. cer para o repositório raiz do cliente ou host remoto](#to-import-the-certificate-on-the-remote-host).
* [Adicione os números de série do dispositivo ao arquivo de hosts no host remoto](#to-add-device-serial-numbers-to-the-remote-host).

Cada um dos procedimentos anteriores é descrito abaixo.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Para importar o certificado no host remoto
1. Clique com o botão direito do mouse no arquivo. cer e selecione **Instalar certificado**. Essa ação iniciará o Assistente para importação de certificados.
   
    ![Assistente de importação de certificados 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. Em **local do repositório**, selecione **computador local**e clique em **Avançar**.
3. Selecione **Coloque todos os certificados no repositório a seguir**e clique em **procurar**. Navegue até o repositório raiz do seu host remoto e clique em **Avançar**.
   
    ![Assistente de importação de certificados 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Clique em **Concluir**. Uma mensagem que informa que a importação foi bem-sucedida é exibida.
   
    ![Assistente de importação de certificados 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Para adicionar números de série do dispositivo ao host remoto
1. Inicie o bloco de notas como administrador e, em seguida, abra o arquivo de hosts localizado em \Windows\System32\Drivers\etc.
2. Adicione as três entradas a seguir ao arquivo de hosts: **endereço IP data 0**, **endereço IP fixo do controlador 0**e **endereço IP fixo do controlador 1**.
3. Insira o número de série do dispositivo que você salvou anteriormente. Mapeie isso para o endereço IP, conforme mostrado na imagem a seguir. Para o controlador 0 e o controlador 1, acrescente **Controller0** e **Controller1** ao final do número de série (nome CN).
   
    ![Adicionando o nome CN ao arquivo hosts](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Salve o arquivo de hosts.

### <a name="connect-to-the-device-from-the-remote-host"></a>Conectar-se ao dispositivo do host remoto

Use o Windows PowerShell e o SSL para inserir uma sessão SSAdmin em seu dispositivo a partir de um host ou cliente remoto. A sessão SSAdmin é mapeada para a opção 1 no menu do [console serial](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) do seu dispositivo.

Execute o procedimento a seguir no computador do qual você deseja fazer a conexão remota do Windows PowerShell.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Para inserir uma sessão SSAdmin no dispositivo usando o Windows PowerShell e o SSL
1. Inicie uma sessão do Windows PowerShell como administrador. Se você estiver usando um cliente do Windows 10, por padrão, o Serviço Gerenciamento Remoto do Windows será definido como manual. Talvez seja necessário iniciar o serviço digitando:

    `Start-Service WinRM`

2. Adicione o endereço IP do dispositivo aos hosts confiáveis do cliente digitando:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Onde <*device_ip*> é o endereço IP do seu dispositivo; por exemplo: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Para criar uma nova credencial, digite:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Onde <*IP do dispositivo de destino*> é o endereço IP de data 0 para seu dispositivo; por exemplo, **10.126.173.90** conforme mostrado na imagem anterior do arquivo de hosts. Além disso, forneça a senha de administrador para seu dispositivo.
4. Crie uma sessão digitando:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Para o parâmetro-ComputerName no cmdlet, forneça o <*número de série do dispositivo de destino*>. Esse número de série foi mapeado para o endereço IP de DATA 0 no arquivo de hosts em seu host remoto; por exemplo, **SHX0991003G44MT** conforme mostrado na imagem a seguir.
5. Tipo:
   
     `Enter-PSSession $session`
6. Você precisará aguardar alguns minutos e, em seguida, será conectado ao seu dispositivo via HTTPS sobre SSL. Você verá uma mensagem que indica que você está conectado ao seu dispositivo.
   
    ![Comunicação remota do PowerShell usando HTTPS e SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como [usar o Windows PowerShell para administrar seu dispositivo StorSimple](storsimple-8000-windows-powershell-administration.md).
* Saiba mais sobre como [usar o serviço de Device Manager do storsimple para administrar seu dispositivo storsimple](storsimple-8000-manager-service-administration.md).

