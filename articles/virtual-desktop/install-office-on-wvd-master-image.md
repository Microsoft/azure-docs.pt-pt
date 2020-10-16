---
title: Instalação Office em uma imagem master VHD - Azure
description: Como instalar e personalizar o Office numa imagem principal do Windows Virtual Desktop para o Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6369013d605ae538ad611a28a90e9c099bb7d80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326053"
---
# <a name="install-office-on-a-master-vhd-image"></a>Instalar o Office numa imagem principal de VHD

Este artigo diz-lhe como instalar aplicações microsoft 365 para empresas, OneDrive, e outras aplicações comuns numa imagem de disco rígido virtual principal (VHD) para upload para Azure. Se os seus utilizadores precisarem de aceder a determinadas aplicações de linha de negócios (LOB), recomendamos que as instale depois de completar as instruções deste artigo.

Este artigo pressupõe que já criou uma máquina virtual (VM). Caso contrário, consulte [Preparar e personalizar uma imagem master VHD](set-up-customize-master-image.md#create-a-vm)

Este artigo também assume que você tem acesso elevado no VM, seja ele provisionado em Azure ou Hyper-V Manager. Caso contrário, consulte [o acesso da Elevate para gerir todos os grupos de subscrição e gestão da Azure.](../role-based-access-control/elevate-access-global-admin.md)

>[!NOTE]
>Estas instruções são para uma configuração virtual do Windows que pode ser usada com os processos existentes da sua organização.

## <a name="install-office-in-shared-computer-activation-mode"></a>Instalar o Office no modo de ativação do computador partilhado

A ativação partilhada por computador permite-lhe implementar aplicações microsoft 365 para empresa para um computador na sua organização que é acedido por vários utilizadores. Para obter mais informações sobre a ativação partilhada do computador, consulte [a visão geral da ativação partilhada do computador para as Aplicações microsoft 365](/deployoffice/overview-shared-computer-activation).

Utilize a [Ferramenta de Implantação do Office](https://www.microsoft.com/download/details.aspx?id=49117) para instalar o Office. O Windows 10 Enterprise suporta apenas as seguintes versões do Office:

   - Microsoft 365 Apps para empresa
   - Microsoft 365 Apps para negócios que vem com uma subscrição Microsoft 365 Business Premium

A Ferramenta de Implementação do Office requer um ficheiro XML de configuração. Para personalizar a amostra seguinte, consulte as [Opções de Configuração para a Ferramenta de Implantação do Office](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/).

Esta configuração de amostra XML que fornecemos fará as seguintes coisas:

   - Instale o Office a partir do Canal Empresarial Mensal e entregue atualizações do Canal Empresarial Mensal.
   - Use a arquitetura x64.
   - Desative as atualizações automáticas.
   - Remova quaisquer instalações existentes do Office e migrar as suas definições.
   - Ativar a ativação partilhada do computador.

>[!NOTE]
>A funcionalidade de pesquisa de stencil da Visio pode não funcionar como esperado no Windows Virtual Desktop.

Aqui está o que esta configuração de amostra XML não vai fazer:

   - Instalar o Skype para negócios
   - Instale o OneDrive no modo por utilizador. Para saber mais, consulte [instalar o OneDrive no modo por máquina](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>A ativação partilhada do computador pode ser configurada através de objetos de política de grupo (GPOs) ou definições de registo. O GPO está localizado em **Configurações de Configuração de Computador \\ \\ Modelos Administrativos \\ Microsoft Office 2016 (Máquina) \\ Definições de licenciamento**

A Ferramenta de Implantação do Office contém setup.exe. Para instalar o Office, executar o seguinte comando numa linha de comando:

```cmd
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>configuration.xml de amostra

A amostra XML seguinte instalará o lançamento mensal do Canal Empresarial.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="MonthlyEnterprise">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level="Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>A equipa do Office recomenda a utilização de 64 bits de instalação para o parâmetro **OfficeClientEdition.**

Depois de instalar o Office, pode atualizar o comportamento padrão do Office. Executar os seguintes comandos individualmente ou num ficheiro de lote para atualizar o comportamento.

```cmd
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

## <a name="install-onedrive-in-per-machine-mode"></a>Instale o OneDrive no modo por máquina

O OneDrive é normalmente instalado por utilizador. Neste ambiente, deve ser instalado por máquina.

Eis como instalar o OneDrive no modo por máquina:

1. Em primeiro lugar, crie uma localização para encenar o instalador OneDrive. Uma pasta de disco local ou a \\ \\ localização [unc] (file://unc) está bem.

2. Faça o download OneDriveSetup.exe para a sua localização encenada com este link: <https://aka.ms/OneDriveWVD-Installer>

3. Se instalou um escritório com o OneDrive omitindo, **\<ExcludeApp ID="OneDrive" /\>** desinstale quaisquer instalações OneDrive por utilizador existentes a partir de um pedido de comando elevado executando o seguinte comando:

    ```cmd
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Executar este comando a partir de um pedido de comando elevado para definir o valor do registo **AllUsersInstall:**

    ```cmd
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Executar este comando para instalar o OneDrive no modo por máquina:

    ```cmd
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Executar este comando para configurar o OneDrive para iniciar o início do súmis para todos os utilizadores:

    ```cmd
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Ativar **a conta de utilizador de configurar silenciosamente** executando o seguinte comando.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Redirecione e mova as pastas conhecidas do Windows para o OneDrive executando o seguinte comando.

    ```cmd
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="microsoft-teams-and-skype-for-business"></a>Microsoft Teams e Skype para Negócios

O Windows Virtual Desktop não suporta o Skype para o Negócios.

Para obter ajuda na instalação do Microsoft Teams, consulte [utilizar as Equipas da Microsoft no ambiente de trabalho virtual do Windows](teams-on-wvd.md). A otimização dos meios de comunicação para equipas do Microsoft no Windows Virtual Desktop está disponível na pré-visualização.

## <a name="next-steps"></a>Passos seguintes

Agora que adicionou o Office à imagem, pode continuar a personalizar a sua imagem principal de VHD. Consulte [Preparar e personalizar uma imagem master VHD](set-up-customize-master-image.md).
