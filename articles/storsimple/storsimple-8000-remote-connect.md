---
title: Ligue-se remotamente ao seu dispositivo StorSimple
description: Explica como configurar o seu dispositivo para gestão remota e como ligar-se ao Windows PowerShell para StorSimple via HTTP ou HTTPS.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1da688dfb00b26ca6b561d5aa0fb548c221381c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514569"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Ligue-se remotamente ao seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral

Pode ligar-se remotamente ao seu dispositivo através do Windows PowerShell. Quando se liga desta forma, não se vê um menu. (Só se vê um menu se utilizar a consola em série no dispositivo para ligar.) Com o remoting do Windows PowerShell, liga-se a um espaço de funcionação específico. Também pode especificar o idioma de exibição.

Para obter mais informações sobre a utilização do Windows PowerShell para gerir o seu dispositivo, vá ao [Windows PowerShell para storSimple para administrar o seu dispositivo StorSimple](storsimple-8000-windows-powershell-administration.md).

Este tutorial explica como configurar o seu dispositivo para gestão remota e, em seguida, como ligar-se ao Windows PowerShell para storSimple. Pode utilizar HTTP ou HTTPS para ligar remotamente através do Windows PowerShell. No entanto, quando estiver a decidir como ligar-se ao Windows PowerShell para storSimple, considere as seguintes informações:

* A ligação direta à consola em série do dispositivo é segura, mas a ligação à consola em série sobre os interruptores de rede não é. Tenha cuidado com o risco de segurança ao ligar-se à consola em série do dispositivo sobre os interruptores de rede.
* A ligação através de uma sessão HTTP pode oferecer mais segurança do que ligar através da consola em série através da rede. Embora este não seja o método mais seguro, é aceitável em redes fidedignas.
* Ligar através de uma sessão HTTPS com um certificado auto-assinado é a opção mais segura e recomendada.

Pode ligar-se remotamente à interface Windows PowerShell. No entanto, o acesso remoto ao seu dispositivo StorSimple através da interface Windows PowerShell não é ativado por padrão. Primeiro deve ativar a gestão remota do dispositivo e, em seguida, no cliente que é utilizado para aceder ao seu dispositivo.

Os passos descritos neste artigo foram realizados num sistema de anfitrião que executa o Windows Server 2012 R2.

## <a name="connect-through-http"></a>Conecte-se através de HTTP

A ligação ao Windows PowerShell para StorSimple através de uma sessão HTTP oferece mais segurança do que a ligação através da consola em série do seu dispositivo StorSimple. Embora este não seja o método mais seguro, é aceitável em redes fidedignas.

Pode utilizar o portal Azure ou a consola em série para configurar a gestão remota. Selecione dos seguintes procedimentos:

* Utilize o portal Azure para permitir a gestão remota em HTTP
* [Utilize a consola em série para permitir a gestão remota em HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Depois de ativar a gestão remota, utilize o seguinte procedimento para preparar o cliente para uma ligação remota.

* [Preparar o cliente para a ligação remota](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Utilize o portal Azure para permitir a gestão remota em HTTP

Execute os seguintes passos no portal Azure para permitir a gestão remota em HTTP.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Para permitir a gestão remota através do portal Azure

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Selecione **Dispositivos** e, em seguida, selecione e clique no dispositivo que pretende configurar para uma gestão remota. Aceda às **definições do Dispositivo > Segurança**.
2. Na lâmina de **definições de segurança,** clique em **Gestão Remota**.
3. Na lâmina **de gestão remota,** desatabilizar a **gestão remota** para **sim**.
4. Pode agora optar por ligar através de HTTP. (O padrão é ligar sobre HTTPS.) Certifique-se de que HTTP está selecionado.
   
   > [!NOTE]
   > A ligação através de HTTP é aceitável apenas em redes fidedignas.
   
5. Clique **em Guardar** e quando solicitado para confirmação, selecione **Sim**.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Utilize a consola em série para permitir a gestão remota em HTTP
Execute os seguintes passos na consola em série do dispositivo para permitir uma gestão remota.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para permitir a gestão remota através da consola em série do dispositivo
1. No menu da consola em série, selecione a opção 1. Para obter mais informações sobre a utilização da consola em série no dispositivo, vá [ao Connect to Windows PowerShell para StorSimple via consola em série do dispositivo](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Na solicitação, escreva: `Enable-HcsRemoteManagement –AllowHttp`
3. É notificado sobre as vulnerabilidades de segurança da utilização do HTTP para se ligar ao dispositivo. Quando solicitado, confirme digitando **Y**.
4. Verifique se HTTP está ativado digitando: `Get-HcsSystem`
5. Verifique se o campo **RemoteManagementMode** mostra **HttpsAndHttpEnabled**. A seguinte ilustração mostra estas definições em PuTTY.
   
     ![HTTPS em série e HTTP habilitado](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Preparar o cliente para a ligação remota
Execute os seguintes passos no cliente para permitir a gestão remota.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Para preparar o cliente para a ligação remota
1. Inicie uma sessão Do Windows PowerShell como administrador. Se utilizar um cliente Windows 10, por predefinição, o serviço de Gestão Remota do Windows está definido para manual. Poderá ser necessário iniciar o serviço digitando:

    `Start-Service WinRM`
    
2. Digite o seguinte comando para adicionar o endereço IP do dispositivo StorSimple à lista de anfitriões fidedignos do cliente:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Substitua <*device_ip*> pelo endereço IP do seu dispositivo; Por exemplo: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Digite o seguinte comando para guardar as credenciais do dispositivo numa variável: 
   
    ```
    $cred = Get-Credential
    ```
    
4. Na caixa de diálogo que aparece:
   
   1. Digite o nome de utilizador neste formato: *device_ip\SSAdmin*.
   2. Digite a palavra-passe do administrador do dispositivo que foi definida quando o dispositivo foi configurado com o assistente de configuração. A palavra-passe predefinida é *Palavra-passe1*.
5. Inicie uma sessão Do Windows PowerShell no dispositivo digitando este comando:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Para criar uma sessão Windows PowerShell para utilização com o dispositivo virtual StorSimple, apedte o `–Port` parâmetro e especifique a porta pública que configuraste em Remoting para O Aparelho Virtual StorSimple.
   
   
Neste momento, deverá ter uma sessão de PowerShell remota ativa para o dispositivo.
   
![PowerShell remoting usando HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Conecte-se através do HTTPS

Ligar ao Windows PowerShell para storSimple através de uma sessão HTTPS é o método mais seguro e recomendado de ligação remota ao seu dispositivo Microsoft Azure StorSimple. Os seguintes procedimentos explicam como configurar a consola em série e os computadores clientes para que possa utilizar HTTPS para ligar ao Windows PowerShell para storSimple.

Pode utilizar o portal Azure ou a consola em série para configurar a gestão remota. Selecione dos seguintes procedimentos:

* Utilize o portal Azure para permitir a gestão remota em HTTPS
* [Utilize a consola em série para permitir a gestão remota em HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Depois de ativar a gestão remota, utilize os seguintes procedimentos para preparar o anfitrião para uma gestão remota e ligar-se ao dispositivo a partir do anfitrião remoto.

* [Prepare o anfitrião para a gestão remota](#prepare-the-host-for-remote-management)
* [Ligue-se ao dispositivo a partir do anfitrião remoto](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Utilize o portal Azure para permitir a gestão remota em HTTPS

Execute os seguintes passos no portal Azure para permitir a gestão remota sobre HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Para permitir a gestão remota sobre HTTPS a partir do portal Azure

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Selecione **Dispositivos** e, em seguida, selecione e clique no dispositivo que pretende configurar para uma gestão remota. Aceda às **definições do Dispositivo > Segurança**.
2. Na lâmina de **definições de segurança,** clique em **Gestão Remota**.
3. Defina **Ativar Gestão Remota** como **Sim**.
4. Agora pode optar por ligar através do HTTPS. (O padrão é ligar sobre HTTPS.) Certifique-se de que HTTPS está selecionado.
5. Clique... e, em seguida, clique em **Baixar Certificado de Gestão Remota**. Especifique uma localização para guardar este ficheiro. Tem de instalar este certificado no cliente ou no computador anfitrião que utilizará para se ligar ao dispositivo.
6. Clique **em Guardar** e, em seguida, clique em **Sim** quando solicitado para confirmação.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Utilize a consola em série para permitir a gestão remota em HTTPS

Execute os seguintes passos na consola em série do dispositivo para permitir uma gestão remota.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para permitir a gestão remota através da consola em série do dispositivo
1. No menu da consola em série, selecione a opção 1. Para obter mais informações sobre a utilização da consola em série no dispositivo, vá [ao Connect to Windows PowerShell para StorSimple via consola em série do dispositivo](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Na solicitação, escreva:
   
     `Enable-HcsRemoteManagement`
   
    Isto deve ativar HTTPS no seu dispositivo.
3. Verifique se https foi ativado digitando: 
   
     `Get-HcsSystem`
   
    Certifique-se de que o campo **RemoteManagementMode** mostra **httpsEnabled**. A seguinte ilustração mostra estas definições em PuTTY.
   
     ![HTTPS em série ativado](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. A partir da saída `Get-HcsSystem` de , copie o número de série do dispositivo e guarde-o para posterior utilização.
   
   > [!NOTE]
   > O número de série mapeia o nome CN no certificado.
   
5. Obtenha um certificado de gestão remota digitando: 
   
     `Get-HcsRemoteManagementCert`
   
    Aparecerá um certificado semelhante ao seguinte.
   
    ![Obtenha certificado de gestão remota](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Copie as informações no certificado a partir de **-----BEGIN CERTIFICATE-----** **para -----END CERTIFICATE-----** num editor de texto como o Notepad, e guarde-as como um ficheiro .cer. (Copiará este ficheiro para o seu anfitrião remoto quando preparar o anfitrião.)
   
   > [!NOTE]
   > Para gerar um novo certificado, utilize o `Set-HcsRemoteManagementCert` cmdlet.
   
### <a name="prepare-the-host-for-remote-management"></a>Prepare o anfitrião para a gestão remota

Para preparar o computador anfitrião para uma ligação remota que utilize uma sessão HTTPS, execute os seguintes procedimentos:

* [Importe o ficheiro .cer na loja raiz do cliente ou anfitrião remoto](#to-import-the-certificate-on-the-remote-host).
* [Adicione os números de série do dispositivo ao ficheiro hostis no seu anfitrião remoto](#to-add-device-serial-numbers-to-the-remote-host).

Cada um dos procedimentos anteriores, é descrito abaixo.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Para importar o certificado no anfitrião remoto
1. Clique com o botão direito no ficheiro .cer e selecione **o certificado de instalação**. Isto inicia o Certificado De Importação De Feiticeiro.
   
    ![Certificado Import Wizard 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. Para **localizar a Loja**, selecione Máquina **Local**e, em seguida, clique em **Seguinte**.
3. Selecione **Coloque todos os certificados na seguinte loja**e, em seguida, clique em **procurar**. Navegue até à loja de raiz do seu anfitrião remoto e, em seguida, clique em **Seguinte**.
   
    ![Certificado Import Wizard 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Clique em **Concluir**. Uma mensagem que diz que a importação foi bem sucedida aparece.
   
    ![Certificado Import Wizard 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Para adicionar números de série do dispositivo ao anfitrião remoto
1. Inicie o Notepad como administrador e, em seguida, abra o ficheiro hostis localizado em \Windows\System32\Drivers\etc.
2. Adicione as seguintes três entradas no ficheiro dos anfitriões: **endereço IP DATA 0**, **Endereço IP Fixo do Controlador 0**e Endereço IP Fixo do **Controlador 1**.
3. Introduza o número de série do dispositivo que guardou anteriormente. Mapeeia isto para o endereço IP como mostrado na imagem seguinte. Para o Controlador 0 e o Controlador 1, o controlador de apêndice **0** e **o Controlador1** no final do número de série (nome CN).
   
    ![Adicionar nome CN ao ficheiro de anfitriões](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Guarde o ficheiro dos anfitriões.

### <a name="connect-to-the-device-from-the-remote-host"></a>Ligue-se ao dispositivo a partir do anfitrião remoto

Utilize o Windows PowerShell e o TLS para introduzir uma sessão SSAdmin no seu dispositivo a partir de um anfitrião ou cliente remoto. O SSAdmin mapeia a opção 1 no menu [de consola em série](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) do seu dispositivo.

Execute o seguinte procedimento no computador a partir do qual pretende efetuar a ligação remota Do Windows PowerShell.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-tls"></a>Para entrar numa sessão SSAdmin no dispositivo utilizando o Windows PowerShell e o TLS
1. Inicie uma sessão Do Windows PowerShell como administrador. Se utilizar um cliente Windows 10, por predefinição, o serviço de Gestão Remota do Windows está definido para manual. Poderá ser necessário iniciar o serviço digitando:

    `Start-Service WinRM`

2. Adicione o endereço IP do dispositivo aos anfitriões fidedignos do cliente digitando:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Onde <*device_ip*> é o endereço IP do seu dispositivo; Por exemplo: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Para criar uma nova credencial, escreva:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Quando <*IP do dispositivo-alvo*> é o endereço IP dos DADOS 0 para o seu dispositivo; por exemplo, **10.126.173.90** como mostrado na imagem anterior do ficheiro dos anfitriões. Além disso, forneça a palavra-passe do administrador para o seu dispositivo.
4. Crie uma sessão digitando:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Para o parâmetro -ComputerName no cmdlet, forneça o número de série <*do dispositivo alvo*>. Este número de série foi mapeado para o endereço IP de DATA 0 no ficheiro hostis no seu anfitrião remoto; por exemplo, **SHX0991003G44MT,** como mostrado na imagem seguinte.
5. Escreva:
   
     `Enter-PSSession $session`
6. Terá de esperar alguns minutos e, em seguida, será ligado ao seu dispositivo através de HTTPS sobre TLS. Vê uma mensagem que indica que está ligado ao seu dispositivo.
   
    ![PowerShell remoting usando HTTPS e TLS](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a utilização do Windows PowerShell para administrar o seu dispositivo StorSimple](storsimple-8000-windows-powershell-administration.md).
* Saiba mais sobre [a utilização do serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

