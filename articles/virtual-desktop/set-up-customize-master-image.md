---
title: Preparar e personalizar uma imagem VHD principal - Azure
description: Como preparar, personalizar e carregar uma imagem mestre da pré-visualização de área de Trabalho Virtual do Windows para o Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: ccea3ebae4bcc19410cfb5537a7140f69b04c4e7
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438789"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Preparar e personalizar uma imagem VHD principal

Este artigo lhe dirá como preparar uma imagem de disco rígido virtual (VHD) principal para carregamento para o Azure, incluindo como criar máquinas virtuais (VMs) e instalar e configurar o software nos mesmos. Estas instruções destinam-se uma configuração específica de pré-visualização de área de Trabalho Virtual do Windows que pode ser utilizada com os processos existentes da sua organização.

## <a name="create-a-vm"></a>Criar uma VM

Sessão multi do Windows 10 Enterprise está disponível na Galeria de imagens do Azure. Existem duas opções para personalizar esta imagem.

A primeira opção é aprovisionar uma máquina virtual (VM) no Azure ao seguir as instruções em [criar uma VM a partir de uma imagem gerida](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)e, em seguida, avançar diretamente para [Software preparo e instalação](set-up-customize-master-image.md#software-preparation-and-installation).

A segunda opção é criar a imagem localmente por transferência da imagem, aprovisionamento de uma VM de Hyper-V e personalizá-la de acordo com as suas necessidades, o que Abordaremos na secção seguinte.

### <a name="local-image-creation"></a>Criação de imagem local

Depois de baixar a imagem para uma localização local, abra **Gestor de Hyper-V** para criar uma VM com o VHD que acabou de criar. Segue-se a versão simple, mas pode encontrar instruções mais detalhadas [criar uma máquina virtual no Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v).

Para criar uma VM com o VHD copiado:

1. Abra o **Assistente de Nova Máquina Virtual**.

2. Na página Especificar geração, selecione **geração 1**.

    ![Uma captura de ecrã da página Especificar geração. Selecionar a opção "Geração 1".](media/a41174fd41302a181e46385e1e701975.png)

3. Em tipo de ponto de verificação, desative os pontos de verificação, desmarcando a caixa de verificação.

    ![Uma captura de ecrã da secção de tipo de ponto de verificação da página pontos de verificação.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Também pode executar o seguinte cmdlet do PowerShell para desativar os pontos de verificação.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Disco fixo

Se criar uma VM a partir de um VHD existente, cria um disco dinâmico por padrão. Ela pode ser alterada num disco fixo, selecionando **editar disco...**  conforme mostrado na imagem seguinte. Para obter instruções mais detalhadas, consulte [preparar um VHD do Windows ou o VHDX para carregar para o Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

![Uma captura de ecrã da opção Editar disco.](media/35772414b5a0f81f06f54065561d1414.png)

Também pode executar o seguinte cmdlet do PowerShell para alterar o disco num disco fixo.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Instalação e preparação de software

Esta secção abrange como preparar e instalar o Office 365 ProPlus, OneDrive, FSLogix, o Windows Defender e outras aplicações comuns. Se os utilizadores precisam para aceder a determinados aplicativos de LOB, recomendamos que instalá-las depois de concluir as instruções nesta secção.

Esta secção assume que elevou acesso na VM, se ele está aprovisionado no Azure ou o Gestor de Hyper-V.

### <a name="install-office-in-shared-computer-activation-mode"></a>Instalar o Office no modo de ativação de computadores partilhados

Utilize o [ferramenta de implantação do Office](https://www.microsoft.com/download/details.aspx?id=49117) para instalar o Office. Sessão multi do Windows 10 Enterprise só suporta Office 365 ProPlus, não Office Perpetual de 2019.

A ferramenta de implementação do Office requer um ficheiro XML de configuração. Para personalizar o exemplo a seguir, veja a [opções de configuração para a ferramenta de implementação do Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Este exemplo de configuração XML que fornecemos fará os seguintes procedimentos:

- Instalar o Office do canal Insiders e entrega de atualizações do canal Insiders quando eles são executados.
- Arquitetura de utilização x64.
- Desative as atualizações automáticas.
- Instale o Visio e o projeto.
- Remova quaisquer instalações existentes do Office e migrar as respetivas definições.
- Ative computador partilhado de licenciamento para a operação num ambiente de servidor de terminal.

Eis o que não fazer esta configuração de exemplo XML:

- Instalação do Skype para empresas
- Instale o OneDrive no modo de usuário. Para obter mais informações, consulte [instalar o OneDrive no modo de por máquina](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Licenciamento de computador partilhado pode ser configurado através de objetos de diretiva de grupo (GPOs) ou as definições de registo. O GPO está localizado em **configuração do computador\\políticas\\modelos administrativos\\(máquina) do Microsoft Office 2016\\definições de licenciamento**

A ferramenta de implementação do Office contém setup.exe. Para instalar o Office, execute o seguinte comando numa linha de comandos:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Configuração. XML de exemplo

O seguinte exemplo XML irá instalar a versão de especialistas, também conhecido como Insiders rápida ou principal de especialistas.

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>A equipe do Office recomenda o uso de instalação de 64 bits para o **OfficeClientEdition** parâmetro.

Depois de instalar o Office, pode atualizar o comportamento de Office padrão. Execute os seguintes comandos individualmente ou num arquivo em lotes para atualizar o comportamento.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

Pode desativar as atualizações automáticas manualmente.

Para desativar as atualizações automáticas:

1. Instalar o Office 365 ao seguir as instruções em [Software preparo e instalação](set-up-customize-master-image.md#software-preparation-and-installation).
2. Instalar quaisquer aplicativos adicionais ao seguir as instruções em [configurar o contentor de perfil de utilizador (FSLogix)](set-up-customize-master-image.md#set-up-user-profile-container-fslogix), [configurar o Windows Defender](set-up-customize-master-image.md#configure-windows-defender), e [outros aplicativos e do Registro configuração](set-up-customize-master-image.md#other-applications-and-registry-configuration).
3. Desative o serviço de atualização automática Windows na local VM.
4. Open **Editor de políticas de Grupo Local\\modelos administrativos\\componentes do Windows\\Windows Update**.
5. Com o botão direito **configurar a atualização automática** e defina-a **desativado**.

Também pode executar o seguinte comando num prompt de comando para desativar as atualizações automáticas.

```batch
reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

Execute este comando para especificar um esquema de início para Windows 10 PCs.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="install-onedrive-in-per-machine-mode"></a>Instalar o OneDrive no modo de por máquina

O OneDrive é normalmente instalada por utilizador. Neste ambiente, deve ser instalado por máquina.

Eis como instalar o OneDrive no modo de por máquina:

1. Primeiro, crie uma localização para colocar o instalador do OneDrive. Uma pasta de disco local ou [\\\\unc] (file://unc) local está bem.

2. Transferir OneDriveSetup.exe à sua localização em etapas com esta ligação: <https://aka.ms/OneDriveWVD-Installer>

3. Se tiver instalado o office com o OneDrive omitindo  **\<ExcludeApp ID = "OneDrive" /\>**, desinstalar todas as instalações existentes do OneDrive por usuário de linha de comandos elevada, executando o seguinte comando:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Execute este comando numa linha de comandos elevada para definir o **AllUsersInstall** valor de registo:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Execute este comando para instalar o OneDrive no modo de por máquina:

    ```batch
    Run "[staged location]\OneDriveSetup.exe /allusers"
    ```

6. Execute este comando para configurar o OneDrive para iniciar o início de sessão de todos os utilizadores:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Ativar **silenciosamente configurar conta de utilizador** ao executar o comando seguinte.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Redirecionar e mover conhecido pastas para o OneDrive, executando o seguinte comando do Windows.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

### <a name="teams-and-skype"></a>As equipes e o Skype

Área de Trabalho Virtual do Windows não suporta oficialmente Skype para empresas e Equipes.

### <a name="set-up-user-profile-container-fslogix"></a>Configurar o contentor de perfil de utilizador (FSLogix)

Para incluir o contentor de FSLogix como parte da imagem, siga as instruções em [configurar uma partilha do perfil de utilizador para um conjunto de anfitrião](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Pode testar a funcionalidade do contentor com FSLogix [este guia de introdução](https://docs.fslogix.com/display/20170529/Profile+Containers+-+Quick+Start).

### <a name="configure-windows-defender"></a>Configurar o Windows Defender

Se o Windows Defender está configurado na VM, certifique-se de que tenha configurado para não análise todo o conteúdo dos ficheiros VHD e VHDX durante o anexo do mesmo.

Esta configuração apenas remove a análise de ficheiros VHD e VHDX durante o anexo, mas não afetará a análise em tempo real.

Para obter mais instruções sobre como configurar o Windows Defender no Windows Server, consulte [exclusões de configurar o antivírus do Windows Defender no Windows Server](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus).

Para saber mais sobre como configurar o Windows Defender para excluir determinados arquivos da análise, veja [configurar e validar as exclusões com base na localização do ficheiro de extensão e a pasta](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus).

### <a name="configure-session-timeout-policies"></a>Configurar as políticas de tempo limite da sessão

As políticas de sessão remoto podem ser impostas em nível de diretiva de grupo, uma vez que todas as VMs num conjunto de anfitriões fazem parte do mesmo grupo de segurança.

Para configurar as políticas de sessão remoto:

1. Navegue para **modelos administrativos** > **componentes do Windows** > **serviços de ambiente de trabalho remoto**  >  **Anfitrião de sessões de ambiente de trabalho remoto** > **limites de tempo de sessão**.
2. No painel no lado direito, selecione o **limite de tempo de conjunto para sessões de serviços de ambiente de trabalho remoto de Active Directory mas Inativos** política.
3. Depois de aparece a janela modal, alterar a opção de política da **não configurado** ao **ativado** para ativar a política.
4. No menu suspenso sob a opção de política, definir o período de tempo para **4 horas**.

Também pode configurar as políticas de sessão remoto manualmente ao executar os comandos seguintes:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fResetBroken /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxConnectionTime /t REG_DWORD /d 600000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxDisconnectionTime /t REG_DWORD /d 5000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxIdleTime /t REG_DWORD /d 5000 /f
```

### <a name="set-up-time-zone-redirection"></a>Configurar o redirecionamento de fuso horário

Redirecionamento de fuso horário possam ser aplicado no nível de diretiva de grupo, uma vez que todas as VMs num conjunto de anfitriões fazem parte do mesmo grupo de segurança.

Para redirecionar os fusos horários:

1. No servidor do Active Directory, abra a **consola de gestão de políticas de grupo**.
2. Expanda o seu domínio e objetos de política de grupo.
3. Com o botão direito a **objeto de diretiva de grupo** que criou para a definições de política de grupo e selecione **editar**.
4. Na **Editor de gerenciamento de diretiva de grupo**, navegue até à **configuração do computador** > **políticas** > **administração Modelos** > **componentes do Windows** > **horizonte Ver serviços de RDSH** > **sessão de área de trabalho remoto Host** > **dispositivo e o redirecionamento de recursos**.
5. Ativar a **permitir o redireccionamento de fuso horário** definição.

Também pode executar este comando na imagem mestra para redirecionar os fusos horários:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Desativar o sentido do armazenamento

Para o anfitrião de sessões de área de Trabalho Virtual do Windows que utilizam o Windows 10 Enterprise ou sessão multi do Windows 10 Enterprise, recomendamos desativar o sentido de armazenamento. Pode desativar o sentido do armazenamento no menu de definições sob **armazenamento**, conforme mostrado na captura de ecrã seguinte:

![Uma captura de ecrã do menu de armazenamento em definições. A opção de "Senso de armazenamento" está desativada.](media/storagesense.png)

Também pode alterar a configuração com o registro, executando o seguinte comando:

```batch
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Incluir suporte de idioma adicionais

Este artigo não abrange como configurar o suporte idiomático e regional. Para obter mais informações, veja os artigos seguintes:

- [Adicionar idiomas às imagens do Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/add-language-packs-to-windows)
- [Funcionalidades a pedido](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities)
- [Funcionalidades de idioma e região a pedido (FOD)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-language-fod)

### <a name="other-applications-and-registry-configuration"></a>Outras aplicações e a configuração do registo

Esta secção abrange a aplicação e a configuração do sistema operativo. Toda a configuração nesta secção é feito por meio de entradas do Registro que podem ser executadas por linha de comandos e ferramentas regedit.

>[!NOTE]
>Pode implementar as práticas recomendadas numa configuração com objetos de política geral (GPOs) ou importações de registo. O administrador pode escolher qualquer uma das opções com base nos requisitos da sua organização.

Para comentários hub coleção de dados telemétricos numa sessão de multi do Windows 10 Enterprise, execute este comando:

```batch
HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection "AllowTelemetry"=dword:00000003
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection /v AllowTelemetry /d 3
```

Execute o seguinte comando para corrigir Watson falhas:

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Introduza os seguintes comandos para o editor de registo para corrigir o suporte de resolução do 5 k. Tem de executar os comandos antes de poder ativar a pilha de lado a lado.

```batch
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp]
"MaxMonitors"=dword:00000004
"MaxXResolution"=dword:00001400
"MaxYResolution"=dword:00000b40

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs]
"MaxMonitors"=dword:00000004
"MaxXResolution"=dword:00001400
"MaxYResolution"=dword:00000b40
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Preparar a imagem para carregamento para o Azure

Depois de concluída a configuração e todos os aplicativos instalados, siga as instruções em [preparar um VHD do Windows ou o VHDX para carregar para o Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) para preparar a imagem.

Depois de preparar a imagem para carregamento, certificar-se de que a VM permanece no estado desligado ou desalocado.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Carregar imagem mestra para uma conta de armazenamento no Azure

Esta secção aplica-se apenas quando a imagem mestra foi criada localmente.

As instruções seguintes indicam como carregar sua imagem mestre para uma conta de armazenamento do Azure. Se ainda não tiver uma conta de armazenamento do Azure, siga as instruções em [este artigo](https://code.visualstudio.com/tutorials/static-website/create-storage) para criar um.

1. Converta a imagem de VM (VHD) como Fixed, se ainda não o fez. Se não converter a imagem como Fixed, não é possível criar com êxito a imagem.

2. Carrega o VHD para um contentor de BLOBs na sua conta de armazenamento. Pode carregar rapidamente com o [ferramenta do Explorador de armazenamento](https://azure.microsoft.com/features/storage-explorer/). Para saber mais sobre a ferramenta do Explorador de armazenamento, veja [este artigo](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

    ![Uma captura de ecrã da janela de pesquisa da ferramenta do Explorador de armazenamento do Azure da Microsoft. A caixa de verificação "Carregar ficheiros. vhd ou vhdx como blobs de páginas (recomendados)" está selecionada.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Em seguida, aceda ao portal do Azure no seu browser e procure "Imagens". A pesquisa deverá levá-lo aos **criar imagem** página, conforme mostrado na captura de ecrã seguinte:

    ![Uma captura de ecrã da página de criação de imagem do portal do Azure, preenchidos com valores de exemplo para a imagem.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Depois de criar a imagem, verá uma notificação, como o mostrado na captura de ecrã seguinte:

    ![Uma captura de ecrã da notificação "imagem criada com êxito".](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que possui uma imagem, pode criar ou atualizar conjuntos de anfitrião. Para saber mais sobre como criar e atualizar conjuntos de anfitrião, veja os artigos seguintes:

- [Criar um conjunto de anfitrião com um modelo Azure Resource Manager](create-host-pools-arm-template.md)
- [Tutorial: Criar um conjunto de anfitriões com o Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Criar um conjunto de anfitriões com o PowerShell](create-host-pools-powershell.md)
- [Configurar uma partilha do perfil de utilizador para um conjunto de anfitrião](create-host-pools-user-profile.md)
- [Configurar o método de balanceamento de carga de área de Trabalho Virtual do Windows](configure-host-pool-load-balancing.md)
