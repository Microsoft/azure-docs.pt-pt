---
title: Guia para a configuração de uma máquina de modelo do Windows [ Microsoft Docs
description: Passos genéricos para preparar uma máquina de modelo seletiva do Windows em Serviços de Laboratório.  Estes passos incluem definir o horário do Windows Update, instalar o OneDrive e instalar o Office.
services: lab-services
documentationcenter: na
author: EMaher
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 11/21/2019
ms.author: enewman
ms.openlocfilehash: c1aaf588f61b329fa3b838b8a92f3e287897315b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521177"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Guia para configurar uma máquina de modelo do Windows em Serviços de Laboratório Azure

Se está a configurar uma máquina de modelos Windows 10 para serviços de laboratório azure, aqui estão algumas boas práticas e dicas a considerar. Os passos de configuração abaixo são todos opcionais.  No entanto, estes passos preparatórios podem ajudar a tornar os seus alunos mais produtivos, minimizar as interrupções do tempo das aulas e garantir que estão a usar as mais recentes tecnologias.

>[!IMPORTANT]
>Este artigo contém snippets PowerShell para simplificar o processo de modificação do modelo da máquina.  Para todos os scripts PowerShell mostrados, você vai querer executá-los no Windows PowerShell com privilégios de administrador. No Windows 10, uma forma rápida de o fazer é clicar no menu Iniciar e escolher o "Windows PowerShell (Administrador)".

## <a name="install-and-configure-onedrive"></a>Instalar e configurar o OneDrive

Para proteger os dados dos estudantes de se perderem se uma máquina virtual for recriada, recomendamos que os estudantes voltem os seus dados até à nuvem.  O Microsoft OneDrive pode ajudar os alunos a proteger os seus dados.  

### <a name="install-onedrive"></a>Instalar o OneDrive

Para descarregar e instalar manualmente o OneDrive, consulte as páginas de descarregamento do [OneDrive](https://onedrive.live.com/about/download/) ou [OneDrive para o Negócio.](https://onedrive.live.com/about/business/)

Também pode utilizar o seguinte script PowerShell.  Irá automaticamente descarregar e instalar a versão mais recente do OneDrive.  Assim que o cliente OneDrive estiver instalado, execute o instalador.  No nosso exemplo, `/allUsers` utilizamos o interruptor para instalar o OneDrive para todos os utilizadores da máquina. Também usamos `/silent` o interruptor para instalar silenciosamente o OneDrive.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>Personalizações OneDrive

Existem [muitas personalizações que podem ser feitas ao OneDrive](https://docs.microsoft.com/onedrive/use-group-policy). Vamos cobrir algumas das personalizações mais comuns.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Mover silenciosamente as pastas conhecidas do Windows para o OneDrive

Pastas como Documentos, Downloads e Imagens são frequentemente usadas para armazenar ficheiros de estudantes. Para garantir que estas pastas estão incorporadas no OneDrive, recomendamos que mude estas pastas para o OneDrive.

Se estiver numa máquina que não esteja a utilizar o Ative Directory, os utilizadores podem mover manualmente essas pastas para o OneDrive assim que autenticarem para o OneDrive.

1. Explorador de ficheiros aberto
2. Clique na pasta Documentos, Downloads ou Imagens.
3. Vá ao Properties > Localização.  Mova a pasta para uma nova pasta no diretório OneDrive.

Se a sua máquina virtual estiver ligada ao Diretório Ativo, pode definir a máquina de modelos para solicitar automaticamente aos seus alunos que movam as pastas conhecidas para o OneDrive.  

Primeiro terá de recuperar a sua identificação do inquilino do escritório.  Para mais instruções, [consulte o seu Gabinete 365 Tenant ID](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).  Também pode obter o Id do Inquilino do Office 365 utilizando o seguinte PowerShell.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Assim que tiver o id do Office 365 Tenant, desloque o OneDrive para solicitar a mudança de pastas conhecidas para o OneDrive utilizando o seguinte PowerShell.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>Utilize ficheiros OneDrive a pedido

Os estudantes podem ter muitos ficheiros dentro das suas contas OneDrive. Para ajudar a poupar espaço na máquina e reduzir o tempo de descarregamento, recomendamos que todos os ficheiros armazenados na conta OneDrive do aluno estejam a pedido.  Os ficheiros a pedido só descarregam quando um utilizador acede ao ficheiro.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Inscreva-se silenciosamente nos utilizadores do OneDrive

O OneDrive pode ser configurado para iniciar sessão automaticamente com as credenciais do Windows do utilizador registado.  O inscrição automática é útil para as aulas onde o aluno se inscreve com as suas credenciais escolares 365.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>Desative o tutorial que aparece no final da configuração do OneDrive

Esta definição permite evitar que o tutorial seja lançado num navegador web no final da Configuração OneDrive.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Detete o tamanho máximo de um ficheiro que deve ser descarregado automaticamente

Esta definição é utilizada em conjunto com o sessão silenciosa dos utilizadores para o cliente sincronizado OneDrive com as suas credenciais Windows em dispositivos que não têm ficheiros OneDrive on-demand ativados. Qualquer utilizador que tenha um OneDrive maior do que o limiar especificado (em MB) será solicitado a escolher as pastas que pretende sincronizar antes que o cliente de sincronização OneDrive (OneDrive.exe) descarregue os ficheiros.  No nosso exemplo, "1111-2222-3333-4444" é o Office 365 tenant ID e 0005000 estabelece um limiar de 5 GB.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-office-365"></a>Instalar e configurar o Microsoft Office 365

### <a name="install-microsoft-office-365"></a>Instale o Microsoft Office 365

Se a sua máquina de modelo precisar de Office, recomendamos a instalação do Office através da Ferramenta de Implantação de [Escritórios (ODT)](https://www.microsoft.com/download/details.aspx?id=49117 ). Você precisará criar um ficheiro de configuração reutilizável usando o Serviço de Configuração de [Clientes Office 365](https://config.office.com/) para escolher qual arquitetura, quais as funcionalidades que você precisará do Office, e quantas vezes atualiza.

1. Vá ao Serviço de Configuração de [Clientes do Office 365](https://config.office.com/) e descarregue o seu próprio ficheiro de configuração.
2. Descarregue [a ferramenta de implantação](https://www.microsoft.com/download/details.aspx?id=49117)do escritório .  O ficheiro descarregado `setup.exe`será.
3. Corra `setup.exe /download configuration.xml` para descarregar componentes do Office.
4. Corra `setup.exe /configure configuration.xml` para instalar componentes do Office.

### <a name="change-the-microsoft-office-365-update-channel"></a>Alterar o canal de atualização do Microsoft Office 365

Utilizando a ferramenta de configuração do escritório, pode definir a frequência com que o Office recebe atualizações. No entanto, se precisar de modificar a frequência com que o Office recebe atualizações após a instalação, pode alterar o URL do canal de atualização. Atualizar os endereços URL do canal podem ser encontrados no [Change the Office 365 ProPlus update channel for devices in your organization](https://docs.microsoft.com/deployoffice/change-update-channels). O exemplo abaixo mostra como definir o Office 365 para usar o Canal de Atualização Mensal.

```powershell
# Update to the Office 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Instalar e configurar atualizações

### <a name="install-the-latest-windows-updates"></a>Instale as últimas Atualizações do Windows

Recomendamos que instale as mais recentes atualizações da Microsoft na máquina de modelos para fins de segurança antes de publicar o modelo VM.  Também potencialmente evita que os alunos sejam perturbados no seu trabalho quando as atualizações são executadas em momentos inesperados.

1. **Definições** de lançamento a partir do Menu Iniciar
2. Clique em **Atualizar** & Segurança
3. Clique em **Verificar para obter atualizações**
4. As atualizações serão descarregadas e instaladas.

Também pode utilizar o PowerShell para atualizar a máquina do modelo.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Algumas atualizações podem exigir que a máquina seja reiniciada.  Será solicitado se for necessário um reboot.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Instale as últimas atualizações para aplicações da Microsoft Store

Recomendamos que todas as aplicações da Microsoft Store sejam atualizadas para as suas versões mais recentes.  Aqui estão as instruções para atualizar manualmente as aplicações a partir da Microsoft Store.  

1. Lançar aplicação **da Microsoft Store.**
2. Clique na elipse (...) ao lado da foto do utilizador no canto superior da aplicação.
3. Selecione **Baixar** e atualizar a partir do menu suspenso.
4. Clique no botão **de atualização Get.**

Também pode utilizar o PowerShell para atualizar as aplicações da Microsoft Store que já estão instaladas.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Parar atualizações automáticas do Windows

Depois de atualizar o Windows para a versão mais recente, poderá considerar parar as Atualizações do Windows.  As atualizações automáticas podem potencialmente interferir com a hora de aula programada.  Se o seu curso for mais longo, considere pedir aos alunos que verifiquem manualmente as atualizações ou que estabeleçam atualizações automáticas por um tempo fora do horário de aulas programado.  Para obter mais informações sobre as opções de personalização para o Windows Update, consulte as [definições adicionais](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings)de Atualização do Windows .

As atualizações automáticas do Windows podem ser interrompidas utilizando o seguinte script PowerShell.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Instale pacotes de línguaestrangeira

Se precisar de idiomas adicionais instalados na máquina virtual, pode adicioná-los através da Microsoft Store.

1. Lançar Microsoft Store
2. Pesquisar por "pacote de idiomas"
3. Escolha o idioma para instalar

Se já estiver ligado ao modelo VM, utilize o [atalho "Instalar o pacote de idiomas"](ms-settings:regionlanguage?activationSource=SMC-IA-4027670) para aceder diretamente à página de definições apropriada.

## <a name="remove-unneeded-built-in-apps"></a>Remover aplicações incorporadas desnecessárias

O Windows 10 vem com muitas aplicações incorporadas que podem não ser necessárias para a sua classe em particular. Para simplificar a imagem da máquina para os alunos, é melhor desinstalar algumas aplicações da sua máquina de modelos.  Para ver uma lista de aplicações instaladas, utilize o cmdlet PowerShell. `Get-AppxPackage`  O exemplo abaixo mostra todas as aplicações instaladas que podem ser removidas.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Para remover uma aplicação, utilize o cmdlet Remove-Appx.  O exemplo abaixo mostra como remover tudo o que está relacionado com xbox.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Instalar aplicações comuns relacionadas com o ensino

Instale outras aplicações comumente utilizadas para o ensino através da aplicação Windows Store. As sugestões incluem aplicações como [a aplicação Microsoft Whiteboard,](https://www.microsoft.com/store/productId/9MSPC6MP8FM4) [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)e Minecraft [Education Edition.](https://education.minecraft.net/) Estas aplicações devem ser instaladas manualmente através da Windows Store ou através dos respetivos websites no modelo VM.

## <a name="conclusion"></a>Conclusão

Este artigo mostrou-lhe passos opcionais para preparar o seu Modelo De VM do Windows para uma classe eficaz.  As medidas incluem a instalação do OneDrive e a instalação do Office 365, a instalação das atualizações para windows e a instalação de atualizações para aplicações da Microsoft Store.  Também discutimos como definir atualizações para uma programação que funciona melhor para a sua aula.  

## <a name="next-steps"></a>Passos seguintes
Veja o artigo sobre como controlar o comportamento de encerramento do Windows para ajudar na gestão de custos: [Guia para controlar o comportamento](how-to-windows-shutdown.md) de encerramento do Windows
