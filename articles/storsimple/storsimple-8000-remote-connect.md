---
title: Ligar remotamente ao dispositivo StorSimple | Documentos da Microsoft
description: Explica como configurar o seu dispositivo para a gestão remota e como se pode ligar para o Windows PowerShell para StorSimple através de HTTP ou HTTPS.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05bec60f4c56c98e9b910b50e858656a2e5554b2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60631878"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Ligar remotamente ao seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral

Pode ligar remotamente ao seu dispositivo através do Windows PowerShell. Quando liga desta forma, não verá um menu. (Veja um menu apenas se utilizar a consola de série do dispositivo para ligar.) Com a comunicação remota do Windows PowerShell, ligue para um espaço de execução específico. Também pode especificar o idioma de apresentação.

Para obter mais informações sobre como utilizar a comunicação remota do Windows PowerShell para gerir o seu dispositivo, aceda a [utilize o Windows PowerShell para StorSimple administrar o seu dispositivo StorSimple](storsimple-8000-windows-powershell-administration.md).

Este tutorial explica como configurar o seu dispositivo para a gestão remota e, em seguida, como se pode ligar para o Windows PowerShell para StorSimple. Pode utilizar HTTP ou HTTPS para ligar remotamente através do Windows PowerShell. No entanto, quando estiver a decidir como ligar ao Windows PowerShell para StorSimple, considere as seguintes informações:

* Ligar diretamente a consola de série do dispositivo é seguro, mas é ligar-se à consola de série através de comutadores de rede não. Tenha cuidado o risco de segurança ao ligar a consola de série do dispositivo através de comutadores de rede.
* Ligar através de uma sessão HTTP pode oferecer mais segurança do que se conectam através da consola de série através da rede. Embora não seja o método mais seguro, é aceitável em redes fidedignas.
* Ligar através de uma sessão HTTPS com um certificado autoassinado é a mais segura e a opção recomendada.

Pode ligar remotamente à interface do Windows PowerShell. No entanto, o acesso remoto para o seu dispositivo StorSimple através da interface do Windows PowerShell não está ativado por predefinição. Tem de ativar a gestão remota no dispositivo pela primeira vez e, em seguida, no cliente que é utilizado para aceder ao seu dispositivo.

Os passos descritos neste artigo foram executados num sistema anfitrião com o Windows Server 2012 R2.

## <a name="connect-through-http"></a>Se conectam por HTTP

Ligar para o Windows PowerShell para StorSimple através de uma sessão HTTP oferece mais segurança do que se conectam através da consola de série do dispositivo StorSimple. Embora não seja o método mais seguro, é aceitável em redes fidedignas.

Pode utilizar o portal do Azure ou da consola de série para configurar a gestão remota. Selecione os seguintes procedimentos:

* Utilizar o portal do Azure para ativar a gestão remota através de HTTP
* [Utilize a consola de série para ativar a gestão remota através de HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Depois de ativar a gestão remota, utilize o procedimento seguinte para preparar o cliente para uma ligação remota.

* [Preparar o cliente para a ligação remota](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Utilizar o portal do Azure para ativar a gestão remota através de HTTP

Execute os seguintes passos no portal do Azure para ativar a gestão remota através de HTTP.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Para ativar a gestão remota através do portal do Azure

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Selecione **dispositivos** e, em seguida, selecione e clique no dispositivo que pretende configurar para a gestão remota. Aceda a **definições do dispositivo > segurança**.
2. Na **definições de segurança** painel, clique em **gerenciamento remoto**.
3. Na **gestão remota** painel, defina **ativar a gestão remota** para **Sim**.
4. Pode agora optar por ligar através de HTTP. (A predefinição é ligar através de HTTPS.) Certifique-se de que o HTTP estiver selecionado.
   
   > [!NOTE]
   > A ligação através de HTTP é aceitável apenas em redes fidedignas.
   
5. Clique em **salvar** e quando lhe for pedida confirmação, selecione **Sim**.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Utilize a consola de série para ativar a gestão remota através de HTTP
Execute os passos seguintes na consola de série do dispositivo para ativar a gestão remota.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para ativar a gestão remota através da consola de série do dispositivo
1. No menu da consola de série, selecione a opção 1. Para obter mais informações sobre como utilizar a consola de série do dispositivo, aceda a [ligue-se ao Windows PowerShell para StorSimple através da consola de série do dispositivo](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Na linha de comandos, escreva: `Enable-HcsRemoteManagement –AllowHttp`
3. Será notificado sobre as vulnerabilidades de segurança da utilização de HTTP para ligar ao dispositivo. Quando lhe for pedido, confirme, escrevendo **Y**.
4. Certifique-se de que o HTTP está ativado, digitando: `Get-HcsSystem`
5. Certifique-se de que o **RemoteManagementMode** campo mostra **HttpsAndHttpEnabled**. A ilustração seguinte mostra essas configurações no PuTTY.
   
     ![Serial HTTPS e HTTP ativada](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Preparar o cliente para a ligação remota
Execute os seguintes passos no cliente para ativar a gestão remota.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Para preparar o cliente para a ligação remota
1. Inicie uma sessão do Windows PowerShell como administrador. Se utilizar um cliente do Windows 10, por predefinição, o serviço Gestão remota do Windows está definido como manual. Poderá ter de iniciar o serviço digitando:

    `Start-Service WinRM`
    
2. Escreva o seguinte comando para adicionar o endereço IP do dispositivo StorSimple a lista de anfitriões fidedigna do cliente:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Substitua <*device_ip*> com o endereço IP do seu dispositivo; por exemplo: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Escreva o seguinte comando para guardar as credenciais de dispositivo numa variável: 
   
    ```
    $cred = Get-Credential
    ```
    
4. Na caixa de diálogo que aparece:
   
   1. Escreva o nome de utilizador neste formato: *device_ip\SSAdmin*.
   2. Digite a senha de administrador do dispositivo que foi definida quando o dispositivo foi configurado com o Assistente de configuração. A palavra-passe predefinido é *Password1*.
5. Inicie uma sessão do Windows PowerShell no dispositivo ao escrever este comando:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Para criar uma sessão do Windows PowerShell para utilização com o dispositivo virtual StorSimple, acrescente o `–Port` parâmetro e especificar a porta pública que configurou na comunicação remota para o StorSimple Virtual Appliance.
   
   
Neste ponto, deve ter uma sessão do Windows PowerShell remota ativa para o dispositivo.
   
![Comunicação remota do PowerShell através de HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Ligar através de HTTPS

Ligar para o Windows PowerShell para StorSimple através de uma sessão HTTPS é o método mais seguro e recomendado de ligar remotamente ao seu dispositivo do Microsoft Azure StorSimple. Os procedimentos seguintes explicam como configurar os computadores de cliente e a consola de série, para que possa utilizar HTTPS para ligar ao Windows PowerShell para StorSimple.

Pode utilizar o portal do Azure ou da consola de série para configurar a gestão remota. Selecione os seguintes procedimentos:

* Utilizar o portal do Azure para ativar a gestão remota através de HTTPS
* [Utilize a consola de série para ativar a gestão remota através de HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Depois de ativar a gestão remota, utilize os procedimentos seguintes para preparar o anfitrião para um gestão remota e ligar ao dispositivo a partir do anfitrião remoto.

* [Preparar o anfitrião para a gestão remota](#prepare-the-host-for-remote-management)
* [Ligar ao dispositivo a partir do anfitrião remoto](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Utilizar o portal do Azure para ativar a gestão remota através de HTTPS

Execute os seguintes passos no portal do Azure para ativar a gestão remota através de HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Para ativar a gestão remota através de HTTPS do portal do Azure

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Selecione **dispositivos** e, em seguida, selecione e clique no dispositivo que pretende configurar para a gestão remota. Aceda a **definições do dispositivo > segurança**.
2. Na **definições de segurança** painel, clique em **gerenciamento remoto**.
3. Defina **Ativar Gestão Remota** como **Sim**.
4. Agora, pode escolher ligar através de HTTPS. (A predefinição é ligar através de HTTPS.) Certifique-se de que o HTTPS estiver selecionado.
5. Clique em... e, em seguida, clique em **Transferir certificado de gestão remota**. Especifique uma localização para guardar este ficheiro. Tem de instalar este certificado no computador cliente ou anfitrião que irá utilizar para ligar ao dispositivo.
6. Clique em **salvar** e, em seguida, clique em **Sim** quando lhe for pedida confirmação.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Utilize a consola de série para ativar a gestão remota através de HTTPS

Execute os passos seguintes na consola de série do dispositivo para ativar a gestão remota.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para ativar a gestão remota através da consola de série do dispositivo
1. No menu da consola de série, selecione a opção 1. Para obter mais informações sobre como utilizar a consola de série do dispositivo, aceda a [ligue-se ao Windows PowerShell para StorSimple através da consola de série do dispositivo](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console).
2. Na linha de comandos, escreva:
   
     `Enable-HcsRemoteManagement`
   
    Isso deve ativar o HTTPS no seu dispositivo.
3. Certifique-se de que o HTTPS foi ativado ao escrever: 
   
     `Get-HcsSystem`
   
    Certifique-se de que o **RemoteManagementMode** campo mostra **HttpsEnabled**. A ilustração seguinte mostra essas configurações no PuTTY.
   
     ![Serial ativadas por HTTPS](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. A partir da saída de `Get-HcsSystem`, copie o número de série do dispositivo e guarde-o para utilização posterior.
   
   > [!NOTE]
   > O número de série é mapeado para o nome CN do certificado.
   
5. Obter um certificado de gestão remota ao escrever: 
   
     `Get-HcsRemoteManagementCert`
   
    Será apresentado um certificado semelhante ao seguinte.
   
    ![Obter o certificado de gestão remota](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Copie as informações no certificado do **---BEGIN CERTIFICATE---** ao **---END CERTIFICATE---** num editor de texto, como o bloco de notas e guarde-o como um ficheiro. cer. (Irá copiar este ficheiro para o seu anfitrião remoto quando preparar o anfitrião.)
   
   > [!NOTE]
   > Para gerar um novo certificado, utilize o `Set-HcsRemoteManagementCert` cmdlet.
   
### <a name="prepare-the-host-for-remote-management"></a>Preparar o anfitrião para a gestão remota

Para preparar o computador anfitrião para uma ligação remota que utiliza uma sessão HTTPS, execute os seguintes procedimentos:

* [Importar o ficheiro. cer para o arquivo de raiz do cliente ou anfitrião remoto](#to-import-the-certificate-on-the-remote-host).
* [Adicionar os números de série do dispositivo para o ficheiro hosts no seu anfitrião remoto](#to-add-device-serial-numbers-to-the-remote-host).

Cada um dos procedimentos anteriores, está descrito abaixo.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Para importar o certificado no anfitrião remoto
1. O ficheiro. cer com o botão direito e selecione **instalar certificado**. Esta ação inicia o Assistente para importar certificados.
   
    ![Assistente para importar certificados 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. Para **Store localização**, selecione **máquina Local**e, em seguida, clique em **seguinte**.
3. Selecione **colocar todos os certificados no seguinte arquivo**e, em seguida, clique em **procurar**. Navegue para o arquivo de raiz do seu anfitrião remoto e, em seguida, clique em **seguinte**.
   
    ![Assistente para importar certificados 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Clique em **Concluir**. É apresentada uma mensagem a indicar que a importação foi concluída com êxito.
   
    ![Assistente para importar certificados 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Para adicionar números de série do dispositivo para o anfitrião remoto
1. Inicie o bloco de notas como administrador e, em seguida, abra o ficheiro hosts localizado em \Windows\System32\Drivers\etc.
2. Adicione as seguintes entradas de três ao seu ficheiro de anfitriões: **DATA 0 endereço IP**, **endereço IP fixo do controlador 0**, e **endereço IP fixo do controlador 1**.
3. Introduza o número de série do dispositivo que guardou anteriormente. Mapeá-la para o endereço IP conforme mostrado na imagem seguinte. Para o controlador 0 e controlador 1, acrescentar **Controller0** e **Controller1** no final do número de série (nome CN).
   
    ![Adicionar nome CN para o ficheiro de anfitriões](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Guarde o ficheiro de anfitriões.

### <a name="connect-to-the-device-from-the-remote-host"></a>Ligar ao dispositivo a partir do anfitrião remoto

Utilize o Windows PowerShell e o SSL para introduzir uma sessão de SSAdmin no seu dispositivo a partir de um anfitrião remoto ou o cliente. A sessão de SSAdmin mapeia para a opção 1 na [consola de série](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) menu do seu dispositivo.

Execute o seguinte procedimento no computador do qual pretende estabelecer a ligação remota do Windows PowerShell.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Para introduzir uma sessão de SSAdmin no dispositivo com o Windows PowerShell e SSL
1. Inicie uma sessão do Windows PowerShell como administrador. Se utilizar um cliente do Windows 10, por predefinição, o serviço Gestão remota do Windows está definido como manual. Poderá ter de iniciar o serviço digitando:

    `Start-Service WinRM`

2. Adicione o endereço IP do dispositivo para anfitriões fidedignos do cliente ao escrever:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Em que <*device_ip*> é o endereço IP do seu dispositivo, por exemplo: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Para criar uma nova credencial, escreva:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Em que <*IP do dispositivo de destino*> é o endereço IP de dados 0 para o seu dispositivo; por exemplo, **10.126.173.90** conforme mostrado na imagem anterior do ficheiro de anfitriões. Além disso, forneça a palavra-passe de administrador para o seu dispositivo.
4. Crie uma sessão ao escrever:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Para o parâmetro - ComputerName no cmdlet, forneça a <*número de série do dispositivo de destino*>. Este número de série foi mapeado para o endereço IP de dados 0 no ficheiro de anfitriões no seu anfitrião remoto; Por exemplo, **SHX0991003G44MT** conforme mostrado na imagem seguinte.
5. Escreva:
   
     `Enter-PSSession $session`
6. Terá de aguardar alguns minutos e, em seguida, será conectado ao seu dispositivo através de HTTPS através de SSL. Verá uma mensagem que indica que está ligado ao seu dispositivo.
   
    ![Comunicação remota do PowerShell através de HTTPS e SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [usando o Windows PowerShell para administrar o seu dispositivo StorSimple](storsimple-8000-windows-powershell-administration.md).
* Saiba mais sobre [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

