---
title: Instalar o Office numa imagem VHD principal - Azure
description: Como instalar e personalizar o Office numa imagem mestra de pré-visualização de área de Trabalho Virtual do Windows para o Azure.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/02/2019
ms.author: v-chjenk
ms.openlocfilehash: cb9edbb508ddd993dcefbf69eb06b4f0d4156485
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66742556"
---
# <a name="install-office-on-a-master-vhd-image"></a>Instalar o Office numa imagem principal de VHD

Este artigo mostra-lhe como instalar o Office 365 ProPlus, OneDrive e outros aplicativos comuns numa imagem de disco rígido virtual (VHD) principal para carregamento para o Azure. Se os utilizadores precisam para aceder a determinados aplicativos de linha de negócio (LOB), recomendamos que instalá-las depois de concluir as instruções neste artigo.

Este artigo pressupõe que já criou uma máquina virtual (VM). Caso contrário, veja [preparar e personalizar uma imagem VHD principal](set-up-customize-master-image.md#create-a-vm)

Este artigo também presume que elevou acesso na VM, se ele está aprovisionado no Azure ou o Gestor de Hyper-V. Caso contrário, veja [elevar o acesso para gerir todos os grupos de gestão e de subscrição do Azure](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin).

>[!NOTE]
>Estas instruções destinam-se uma configuração específica de pré-visualização de área de Trabalho Virtual do Windows que pode ser utilizada com os processos existentes da sua organização.

## <a name="install-office-in-shared-computer-activation-mode"></a>Instalar o Office no modo de ativação de computadores partilhados

Ativação de computadores partilhados permite-lhe implementar num computador na sua organização que é acedido por vários utilizadores do Office 365 ProPlus. Para obter mais informações sobre a ativação de computadores partilhados, consulte [descrição geral da ativação de computadores partilhados para o Office 365 ProPlus](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus).

Utilize o [ferramenta de implantação do Office](https://www.microsoft.com/download/details.aspx?id=49117) para instalar o Office. Sessão multi do Windows 10 Enterprise só suporta as seguintes versões do Office:
- Office 365 ProPlus
- Office 365 empresas que vem com uma subscrição do Microsoft 365 empresas

A ferramenta de implementação do Office requer um ficheiro XML de configuração. Para personalizar o exemplo a seguir, veja a [opções de configuração para a ferramenta de implementação do Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Este exemplo de configuração XML que fornecemos fará os seguintes procedimentos:

- Instalar o Office do canal Insiders e entrega de atualizações do canal Insiders quando eles são executados.
- Arquitetura de utilização x64.
- Desative as atualizações automáticas.
- Instale o Visio e o projeto.
- Remova quaisquer instalações existentes do Office e migrar as respetivas definições.
- Ative a ativação de computadores partilhados.

>[!NOTE]
>Funcionalidade de pesquisa de estêncil no Visio não funciona na área de Trabalho Virtual do Windows durante a configuração de pré-visualização.

Eis o que não fazer esta configuração de exemplo XML:

- Instalação do Skype para empresas
- Instale o OneDrive no modo de usuário. Para obter mais informações, consulte [instalar o OneDrive no modo de por máquina](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Ativação do computador partilhada pode ser configurada através de objetos de diretiva de grupo (GPOs) ou as definições de registo. O GPO está localizado em **configuração do computador\\políticas\\modelos administrativos\\(máquina) do Microsoft Office 2016\\definições de licenciamento**

A ferramenta de implementação do Office contém setup.exe. Para instalar o Office, execute o seguinte comando numa linha de comandos:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Configuração. XML de exemplo

O seguinte exemplo XML irá instalar a versão de especialistas.

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

## <a name="install-onedrive-in-per-machine-mode"></a>Instalar o OneDrive no modo de por máquina

O OneDrive é normalmente instalada por utilizador. Neste ambiente, deve ser instalado por máquina.

Eis como instalar o OneDrive no modo de por máquina:

1. Primeiro, crie uma localização para colocar o instalador do OneDrive. Uma pasta de disco local ou [\\\\unc] (file://unc) local está bem.

2. Transferir OneDriveSetup.exe à sua localização em etapas com esta ligação: <https://aka.ms/OneDriveWVD-Installer>

3. Se tiver instalado o office com o OneDrive omitindo  **\<ExcludeApp ID = "OneDrive" /\>** , desinstalar todas as instalações existentes do OneDrive por usuário de linha de comandos elevada, executando o seguinte comando:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Execute este comando numa linha de comandos elevada para definir o **AllUsersInstall** valor de registo:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Execute este comando para instalar o OneDrive no modo de por máquina:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Execute este comando para configurar o OneDrive para iniciar o início de sessão de todos os utilizadores:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Ativar **silenciosamente configurar conta de utilizador** ao executar o comando seguinte.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Redirecionar e mover conhecido pastas para o OneDrive, executando o seguinte comando do Windows.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>As equipes e o Skype

Área de Trabalho Virtual do Windows não suporta o Skype para empresas e Equipes.

## <a name="next-steps"></a>Passos Seguintes

Agora que adicionou Office à imagem, pode continuar a personalizar a imagem VHD principal. Ver [preparar e personalizar uma imagem VHD principal](set-up-customize-master-image.md).
