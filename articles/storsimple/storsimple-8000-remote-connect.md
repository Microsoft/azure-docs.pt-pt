---
title: Ligue-se remotamente ao seu dispositivo StorSimple
description: Explica como configurar o seu dispositivo para gestão remota e como ligar-se ao Windows PowerShell para StorSimple via HTTP ou HTTPS.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70d0246debc532260d287104bacea2f15c1b94d2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254784"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Ligue-se remotamente ao seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral

Pode ligar-se remotamente ao seu dispositivo através do Windows PowerShell. Quando se liga desta forma, não se vê um menu. (Só se vê um menu se utilizar a consola em série do dispositivo para se ligar.) Com o remo do Windows PowerShell, liga-se a um espaço de execução específico. Também pode especificar a linguagem de exibição.

Para obter mais informações sobre a utilização do Windows PowerShell para gerir o seu dispositivo, vá utilizar o [Windows PowerShell para o StorSimple para administrar o seu dispositivo StorSimple](storsimple-8000-windows-powershell-administration.md).

Este tutorial explica como configurar o seu dispositivo para gestão remota e, em seguida, como ligar ao Windows PowerShell para storSimple. Pode utilizar HTTP ou HTTPS para ligar remotamente através do Windows PowerShell. No entanto, quando estiver a decidir como ligar-se ao Windows PowerShell para o StorSimple, considere as seguintes informações:

* A ligação direta à consola em série do dispositivo é segura, mas ligar-se à consola em série sobre os interruptores de rede não é. Tenha cuidado com o risco de segurança ao ligar-se à consola série do dispositivo sobre os interruptores de rede.
* Ligar-se através de uma sessão HTTP pode oferecer mais segurança do que ligar através da consola em série através da rede. Embora este não seja o método mais seguro, é aceitável em redes fidedignas.
* Ligar através de uma sessão HTTPS com um certificado auto-assinado é a opção mais segura e recomendada.

Pode ligar-se remotamente à interface Windows PowerShell. No entanto, o acesso remoto ao seu dispositivo StorSimple através da interface Windows PowerShell não está ativado por defeito. Primeiro, tem de ativar a gestão remota do dispositivo e, em seguida, no cliente que é utilizado para aceder ao seu dispositivo.

Os passos descritos neste artigo foram realizados num sistema de anfitriões que executa o Windows Server 2012 R2.

## <a name="connect-through-http"></a>Conecte-se através de HTTP

A ligação ao Windows PowerShell para o StorSimple através de uma sessão HTTP oferece mais segurança do que ligar através da consola em série do seu dispositivo StorSimple. Embora este não seja o método mais seguro, é aceitável em redes fidedignas.

Pode utilizar o portal Azure ou a consola em série para configurar a gestão remota. Selecione entre os seguintes procedimentos:

* Utilize o portal Azure para permitir a gestão remota sobre http
* [Utilize a consola em série para permitir a gestão remota em HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Depois de ativar a gestão remota, utilize o seguinte procedimento para preparar o cliente para uma ligação remota.

* [Prepare o cliente para uma ligação remota](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Utilize o portal Azure para permitir a gestão remota sobre http

Execute os seguintes passos no portal Azure para permitir a gestão remota em HTTP.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Para permitir a gestão remota através do portal Azure

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Selecione **Dispositivos** e, em seguida, selecione e clique no dispositivo que pretende configurar para uma gestão remota. Vá para **as definições do Dispositivo > Segurança**.
2. Na lâmina de **definições** de Segurança, clique em **Gestão Remota**.
3. Na lâmina de **gestão remota,** coloque a ativação da **gestão remota** até **sim**.
4. Pode agora optar por ligar através de HTTP. (O predefinido é ligar-se ao HTTPS.) Certifique-se de que http é selecionado.
   
   > [!NOTE]
   > A ligação através de HTTP é aceitável apenas em redes fidedignas.
   
5. Clique em **Guardar** e quando solicitado para confirmação, selecione **Sim**.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Utilize a consola em série para permitir a gestão remota em HTTP
Execute os seguintes passos na consola em série do dispositivo para permitir a gestão remota.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para permitir a gestão remota através da consola em série do dispositivo
1. No menu de consola em série, selecione a opção 1. Para obter mais informações sobre a utilização da consola em série no dispositivo, vá a [Connect to Windows PowerShell para StorSimple via consola de série do dispositivo](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. No momento, escreva: `Enable-HcsRemoteManagement –AllowHttp`
3. É notificado sobre as vulnerabilidades de segurança da utilização do HTTP para se ligar ao dispositivo. Quando solicitado, confirme digitando **Y**.
4. Verifique se http está ativado por dactilografia: `Get-HcsSystem`
5. Verifique se o campo **RemoteManagementMode** mostra **HttpsAndHttpEnabled**. A seguinte ilustração mostra estas configurações em PuTTY.
   
     ![Serial HTTPS e HTTP habilitados](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Prepare o cliente para uma ligação remota
Execute os seguintes passos no cliente para permitir a gestão remota.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Para preparar o cliente para uma ligação remota
1. Inicie uma sessão do Windows PowerShell como administrador. Se utilizar um cliente do Windows 10, por padrão, o serviço de Gestão Remota do Windows está definido manualmente. Pode ter de iniciar o serviço digitando:

    `Start-Service WinRM`
    
2. Digite o seguinte comando para adicionar o endereço IP do dispositivo StorSimple à lista de anfitriões fidedignos do cliente:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Substitua <*device_ip*> com o endereço IP do seu dispositivo; Por exemplo: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Digite o seguinte comando para guardar as credenciais do dispositivo numa variável: 
   
    ```
    $cred = Get-Credential
    ```
    
4. Na caixa de diálogo que aparece:
   
   1. Digite o nome do utilizador neste formato: *device_ip\SSAdmin*.
   2. Digite a palavra-passe do administrador do dispositivo que foi definida quando o dispositivo foi configurado com o assistente de configuração. A palavra-passe padrão é *Password1*.
5. Inicie uma sessão do Windows PowerShell no dispositivo digitando este comando:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Para criar uma sessão do Windows PowerShell para utilização com o dispositivo virtual StorSimple, acomode o parâmetro `–Port` e especifique a porta pública que configuraem em Remoting para O Aparelho Virtual StorSimple.
   
   
Neste ponto, deverá ter uma sessão remota ativa do Windows PowerShell para o dispositivo.
   
![PowerShell remoing usando HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Conecte-se através de HTTPS

Ligar-se ao Windows PowerShell para StorSimple através de uma sessão HTTPS é o método mais seguro e recomendado de ligação remota ao seu dispositivo Microsoft Azure StorSimple. Os seguintes procedimentos explicam como configurar a consola em série e os computadores clientes para que possa utilizar https para ligar ao Windows PowerShell para storSimple.

Pode utilizar o portal Azure ou a consola em série para configurar a gestão remota. Selecione entre os seguintes procedimentos:

* Utilize o portal Azure para permitir a gestão remota sobre HTTPS
* [Utilize a consola em série para permitir a gestão remota sobre HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Depois de ativar a gestão remota, utilize os seguintes procedimentos para preparar o hospedeiro para uma gestão remota e ligue-se ao dispositivo a partir do hospedeiro remoto.

* [Prepare o anfitrião para gestão remota](#prepare-the-host-for-remote-management)
* [Ligue-se ao dispositivo a partir do hospedeiro remoto](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Utilize o portal Azure para permitir a gestão remota sobre HTTPS

Execute os seguintes passos no portal Azure para permitir a gestão remota em HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Para permitir a gestão remota sobre HTTPS do portal Azure

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Selecione **Dispositivos** e, em seguida, selecione e clique no dispositivo que pretende configurar para uma gestão remota. Vá para **as definições do Dispositivo > Segurança**.
2. Na lâmina de **definições** de Segurança, clique em **Gestão Remota**.
3. Defina **Ativar Gestão Remota** como **Sim**.
4. Agora pode optar por ligar-se usando HTTPS. (O predefinido é ligar-se ao HTTPS.) Certifique-se de que https é selecionado.
5. Clique... e, em seguida, clique em descarregar certificado de **gestão remota**. Especifique uma localização para guardar este ficheiro. Tem de instalar este certificado no cliente ou no computador anfitrião que utilizará para se ligar ao dispositivo.
6. Clique em **Guardar** e, em **seguida,** clique em Sim quando solicitado para confirmação.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Utilize a consola em série para permitir a gestão remota sobre HTTPS

Execute os seguintes passos na consola em série do dispositivo para permitir a gestão remota.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para permitir a gestão remota através da consola em série do dispositivo
1. No menu de consola em série, selecione a opção 1. Para obter mais informações sobre a utilização da consola em série no dispositivo, vá a [Connect to Windows PowerShell para StorSimple via consola de série do dispositivo](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. No momento, escreva:
   
     `Enable-HcsRemoteManagement`
   
    Isto deve ativar HTTPS no seu dispositivo.
3. Verifique se https foi ativado por dactilografia: 
   
     `Get-HcsSystem`
   
    Certifique-se de que o campo **RemoteManagementMode** mostra **HttpsEnabled**. A seguinte ilustração mostra estas configurações em PuTTY.
   
     ![Serial HTTPS ativado](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. A partir da saída do `Get-HcsSystem`, copie o número de série do dispositivo e guarde-o para posterior utilização.
   
   > [!NOTE]
   > O número de série mapeia o nome NC no certificado.
   
5. Obtenha um certificado de gestão remota digitando: 
   
     `Get-HcsRemoteManagementCert`
   
    Aparecerá um certificado semelhante ao seguinte.
   
    ![Obtenha certificado de gestão remota](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Copie as informações no certificado do **certificado -----BEGIN CERTIFICATE-----** para **-----END CERTIFICATE-----** num editor de texto como o Notepad, e guarde-as como um ficheiro .cer. (Irá copiar este ficheiro para o seu anfitrião remoto quando preparar o hospedeiro.)
   
   > [!NOTE]
   > Para gerar um novo certificado, utilize o `Set-HcsRemoteManagementCert` cmdlet.
   
### <a name="prepare-the-host-for-remote-management"></a>Prepare o anfitrião para gestão remota

Para preparar o computador hospedeiro para uma ligação remota que utilize uma sessão HTTPS, execute os seguintes procedimentos:

* [Importar o ficheiro .cer para o armazém raiz do cliente ou do anfitrião remoto](#to-import-the-certificate-on-the-remote-host).
* [Adicione os números de série do dispositivo ao ficheiro dos anfitriões no seu hospedeiro remoto](#to-add-device-serial-numbers-to-the-remote-host).

Cada um dos procedimentos anteriores é descrito abaixo.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Para importar o certificado no hospedeiro remoto
1. Clique no ficheiro .cer e selecione **Instalar o certificado**. Isto começa o Certificado De Importar Feiticeiro.
   
    ![Certificado Importador Assistente 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. Para **armazenar a localização,** selecione **Local Machine**, e, em seguida, clique em **Seguinte**.
3. Selecione **Coloque todos os certificados na seguinte loja**e, em seguida, clique em **Navegar**. Navegue até à loja de raízes do seu anfitrião remoto e, em seguida, clique em **Next**.
   
    ![Certificado Importador Assistente 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Clique em **Concluir**. Uma mensagem que lhe diz que a importação foi bem sucedida aparece.
   
    ![Certificado Importador Assistente 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Para adicionar números de série do dispositivo ao hospedeiro remoto
1. Inicie o Bloco de Notas como administrador e, em seguida, abra o ficheiro dos anfitriões localizado no \Windows\System32\Drivers\etc.
2. Adicione as seguintes três entradas ao ficheiro dos anfitriões: **endereço DATA 0 IP,** **Endereço IP fixo 0**, e endereço IP fixo do **controlador 1**.
3. Introduza o número de série do dispositivo que guardou anteriormente. Mapeie isto para o endereço IP, como mostrado na imagem seguinte. Para o Controlador 0 e controlador 1, anexar o **Controlador0** e **o Controlador1** no final do número de série (nome NC).
   
    ![Adicionar nome CN ao arquivo de anfitriões](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Guarde o ficheiro dos anfitriões.

### <a name="connect-to-the-device-from-the-remote-host"></a>Ligue-se ao dispositivo a partir do hospedeiro remoto

Utilize o Windows PowerShell e o SSL para introduzir uma sessão SSAdmin no seu dispositivo a partir de um anfitrião ou cliente remoto. A sessão SSAdmin mapeia a opção 1 no menu de consola seleção em [série](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) do seu dispositivo.

Execute o seguinte procedimento no computador a partir do qual pretende efetuar a ligação remota do Windows PowerShell.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Para introduzir uma sessão SSAdmin no dispositivo utilizando o Windows PowerShell e sSL
1. Inicie uma sessão do Windows PowerShell como administrador. Se utilizar um cliente do Windows 10, por padrão, o serviço de Gestão Remota do Windows está definido manualmente. Pode ter de iniciar o serviço digitando:

    `Start-Service WinRM`

2. Adicione o endereço IP do dispositivo aos anfitriões de confiança do cliente digitando:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Onde <*device_ip*> é o endereço IP do seu dispositivo; Por exemplo: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Para criar uma nova credencial, escreva:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Onde <*IP do dispositivo alvo*> é o endereço IP de DATA 0 para o seu dispositivo; por exemplo, **10.126.173.90,** como mostrado na imagem anterior do ficheiro dos anfitriões. Além disso, forneça a palavra-passe do administrador para o seu dispositivo.
4. Criar uma sessão digitando:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Para o parâmetro -ComputerName no cmdlet, forneça o*número de série do dispositivo-alvo*>. Este número de série foi mapeado para o endereço IP de DATA 0 no ficheiro dos anfitriões no seu anfitrião remoto; por exemplo, **SHX0991003G44MT,** como mostrado na imagem seguinte.
5. Escreva:
   
     `Enter-PSSession $session`
6. Terá de esperar alguns minutos e, em seguida, estará ligado ao seu dispositivo via HTTPS através do SSL. Vê uma mensagem que indica que está ligado ao seu dispositivo.
   
    ![PowerShell remoing usando HTTPS e SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a utilização do Windows PowerShell para administrar o seu dispositivo StorSimple](storsimple-8000-windows-powershell-administration.md).
* Saiba mais sobre [a utilização do serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

