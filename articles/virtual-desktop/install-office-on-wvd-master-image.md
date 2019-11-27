---
title: Instalar o Office em uma imagem VHD mestre – Azure
description: Como instalar e personalizar o Office em uma imagem mestra de área de trabalho virtual do Windows para o Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
ms.openlocfilehash: 059748f6f08b1c73d56aa3a127aa785f55eb63ee
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539140"
---
# <a name="install-office-on-a-master-vhd-image"></a>Instalar o Office numa imagem principal de VHD

Este artigo mostra como instalar o Office 365 ProPlus, o OneDrive e outros aplicativos comuns em uma imagem de VHD (disco rígido virtual) mestre para carregar no Azure. Se os usuários precisarem acessar determinados aplicativos de LOB (linha de negócios), recomendamos que você os instale depois de concluir as instruções neste artigo.

Este artigo pressupõe que você já criou uma VM (máquina virtual). Caso contrário, consulte [preparar e personalizar uma imagem mestre VHD](set-up-customize-master-image.md#create-a-vm)

Este artigo também pressupõe que você tenha acesso elevado na VM, seja provisionado no Azure ou no Gerenciador do Hyper-V. Caso contrário, consulte [elevar o acesso para gerenciar todos os grupos de gerenciamento e assinatura do Azure](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin).

>[!NOTE]
>Essas instruções são para uma configuração específica da área de trabalho virtual do Windows que pode ser usada com os processos existentes da sua organização.

## <a name="install-office-in-shared-computer-activation-mode"></a>Instalar o Office no modo de ativação do computador compartilhado

A ativação de computador compartilhado permite implantar o Office 365 ProPlus em um computador em sua organização que é acessado por vários usuários. Para obter mais informações sobre a ativação do computador compartilhado, consulte [visão geral da ativação do computador compartilhado para o Office 365 ProPlus](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus).

Use a [ferramenta de implantação do Office](https://www.microsoft.com/download/details.aspx?id=49117) para instalar o Office. O Windows 10 Enterprise Multi-Session dá suporte apenas às seguintes versões do Office:
- Office 365 ProPlus
- Office 365 Business que vem com uma assinatura Microsoft 365 Business

A ferramenta de implantação do Office requer um arquivo XML de configuração. Para personalizar o exemplo a seguir, consulte as [Opções de configuração para a ferramenta de implantação do Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Este XML de configuração de exemplo que fornecemos fará o seguinte:

- Instale o Office do canal mensal e forneça atualizações do canal mensal quando elas forem executadas.
- Use a arquitetura x64.
- Desabilite as atualizações automáticas.
- Remova todas as instalações existentes do Office e migre suas configurações.
- Habilite a ativação do computador compartilhado.

>[!NOTE]
>O recurso de pesquisa de estêncil do Visio pode não funcionar conforme o esperado na área de trabalho virtual do Windows.

Veja o que este XML de configuração de exemplo não fará:

- Instalar o Skype for Business
- Instale o OneDrive no modo por usuário. Para saber mais, confira [instalar o onedrive no modo por máquina](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>A ativação do computador compartilhado pode ser configurada por meio de objetos de Política de Grupo (GPOs) ou configurações do registro. O GPO está localizado em **configuração do computador\\políticas\\Modelos Administrativos\\Microsoft Office 2016 (máquina)\\configurações de licenciamento**

A ferramenta de implantação do Office contém setup. exe. Para instalar o Office, execute o seguinte comando em uma linha de comando:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Exemplo de Configuration. xml

O exemplo de XML a seguir instalará a versão mensal.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="Monthly">
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
  <Logging Level=" Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>A equipe do Office recomenda usar a instalação de 64 bits para o parâmetro **OfficeClientEdition** .

Depois de instalar o Office, você pode atualizar o comportamento padrão do Office. Execute os comandos a seguir individualmente ou em um arquivo em lotes para atualizar o comportamento.

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

## <a name="install-onedrive-in-per-machine-mode"></a>Instalar o OneDrive no modo por máquina

O OneDrive normalmente é instalado por usuário. Nesse ambiente, ele deve ser instalado por computador.

Veja como instalar o OneDrive no modo por máquina:

1. Primeiro, crie um local para preparar o instalador do OneDrive. Uma pasta de disco local ou um local de [\\\\UNC] (file://unc) está bem.

2. Baixe o OneDriveSetup. exe em seu local de preparação com este link: <https://aka.ms/OneDriveWVD-Installer>

3. Se você instalou o Office com o OneDrive omitindo **\<ID ExcludeApp = "onedrive"/\>** , desinstale todas as instalações existentes do onedrive por usuário em um prompt de comando elevado executando o seguinte comando:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Execute este comando em um prompt de comando elevado para definir o valor do registro **AllUsersInstall** :

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Execute este comando para instalar o OneDrive no modo por máquina:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Execute este comando para configurar o OneDrive para iniciar na entrada para todos os usuários:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Habilite **Configurar silenciosamente a conta de usuário** executando o comando a seguir.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Redirecione e mova as pastas conhecidas do Windows para o OneDrive executando o comando a seguir.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Equipes e Skype

A área de trabalho virtual do Windows não dá suporte ao Skype for Business e às equipes.

## <a name="next-steps"></a>Passos Seguintes

Agora que você adicionou o Office à imagem, você pode continuar a personalizar a imagem do VHD mestre. Consulte [preparar e personalizar uma imagem VHD mestre](set-up-customize-master-image.md).
