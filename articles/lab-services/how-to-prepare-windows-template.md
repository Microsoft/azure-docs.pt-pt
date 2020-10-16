---
title: Guia para configurar uma máquina de modelos Windows Microsoft Docs
description: Passos genéricos para preparar uma máquina de modelos Windows em Serviços de Laboratório.  Estes passos incluem a definição do calendário de atualização do Windows, a instalação do OneDrive e a instalação do Office.
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: cf1b9db8de2c0f2c852a41d1e30343c5cef1b20b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91396693"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Guia para configurar uma máquina de modelos Windows nos serviços do Azure Lab

Se está a configurar uma máquina de modelos Windows 10 para serviços Azure Lab, aqui estão algumas boas práticas e dicas a ter em conta. Os passos de configuração abaixo são todos opcionais.  No entanto, estes passos preparatórios podem ajudar a tornar os seus alunos mais produtivos, minimizar as interrupções de tempo de aula e garantir que estão a usar as mais recentes tecnologias.

>[!IMPORTANT]
>Este artigo contém snippets PowerShell para simplificar o processo de modificação do modelo da máquina.  Para todos os scripts PowerShell mostrados, você vai querer executá-los no Windows PowerShell com privilégios de administrador. No Windows 10, uma forma rápida de o fazer é clicar com o botão direito no Menu Iniciar e escolher o "Windows PowerShell (Administrador)".

## <a name="install-and-configure-onedrive"></a>Instalar e configurar o OneDrive

Para proteger os dados dos estudantes de se perderem se uma máquina virtual for reposta, recomendamos que os alunos voltem aos seus dados até à nuvem.  O Microsoft OneDrive pode ajudar os estudantes a proteger os seus dados.  

### <a name="install-onedrive"></a>Instalar o OneDrive

Para descarregar e instalar manualmente o OneDrive, consulte as páginas de descarregamento [oneDrive](https://onedrive.live.com/about/download/) ou [OneDrive para business](https://onedrive.live.com/about/business/) download.

Também pode utilizar o seguinte script PowerShell.  Irá descarregar e instalar automaticamente a versão mais recente do OneDrive.  Assim que o cliente OneDrive estiver instalado, execute o instalador.  No nosso exemplo, utilizamos o interruptor para instalar o `/allUsers` OneDrive para todos os utilizadores na máquina. Também usamos o interruptor para instalar silenciosamente o `/silent` OneDrive.

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

Existem [muitas personalizações que podem ser feitas ao OneDrive.](https://docs.microsoft.com/onedrive/use-group-policy) Vamos cobrir algumas das personalizações mais comuns.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Mover silenciosamente as pastas conhecidas do Windows para o OneDrive

Pastas como Documentos, Downloads e Imagens são frequentemente usadas para armazenar ficheiros de estudantes. Para garantir que estas pastas estão ressarçadas para o OneDrive, recomendamos que mova estas pastas para o OneDrive.

Se estiver numa máquina que não esteja a utilizar o Ative Directory, os utilizadores podem mover manualmente essas pastas para o OneDrive assim que autenticarem para o OneDrive.

1. Explorador de arquivos aberto
2. Clique com o botão direito na pasta Documentos, Downloads ou Imagens.
3. Vá a Propriedades > Localização.  Mover a pasta para uma nova pasta no diretório OneDrive.

Se a sua máquina virtual estiver ligada ao Ative Directory, pode configurar a máquina de modelos para solicitar automaticamente aos seus alunos que transcam as pastas conhecidas para o OneDrive.  

Primeiro, tens de recuperar a identificação da tua organização.  Para mais instruções, consulte [o seu ID de organização Microsoft 365](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).  Também pode obter o ID da organização usando o seguinte PowerShell.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Assim que tiver o ID da sua organização, desloque o OneDrive para solicitar a deslocação de pastas conhecidas para o OneDrive utilizando o seguinte PowerShell.

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

Os alunos podem ter muitos ficheiros nas suas contas OneDrive. Para ajudar a economizar espaço na máquina e reduzir o tempo de descarregamento, recomendamos que todos os ficheiros armazenados na conta OneDrive do aluno estejam a pedido.  Os ficheiros on-demand só descarregam quando um utilizador acede ao ficheiro.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Assine silenciosamente nos utilizadores para o OneDrive

O OneDrive pode ser configurado para iniciar automaticamente o login com as credenciais do Windows do utilizador registado no utilizador.  O acesso automático é útil para as aulas onde o aluno assina com as suas credenciais escolares.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>Desative o tutorial que aparece no final da configuração do OneDrive

Esta definição permite-lhe evitar que o tutorial seja lançado num navegador web no final da Configuração OneDrive.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Desfie o tamanho máximo de um ficheiro que será descarregado automaticamente

Esta definição é utilizada em conjunto com o sinal silencioso dos utilizadores para o cliente de sincronização OneDrive com as suas credenciais Windows em dispositivos que não têm ficheiros OneDrive on-demand ativados. Qualquer utilizador que tenha um OneDrive maior do que o limiar especificado (em MB) será solicitado a escolher as pastas que pretende sincronizar antes que o cliente de sincronização OneDrive (OneDrive.exe) descarregue os ficheiros.  No nosso exemplo, "1111-2222-3333-4444" é o ID da organização e 0005000 fixa um limiar de 5 GB.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-365"></a>Instalar e configurar a Microsoft 365

### <a name="install-microsoft-365"></a>Instalar o Microsoft 365

Se a máquina de modelo precisar do Office, recomendamos a instalação do Office através da [Ferramenta de Implantação do Office (ODT)](https://www.microsoft.com/download/details.aspx?id=49117). Terá de criar um ficheiro de configuração reutilizável utilizando o [Microsoft 365 Apps Admin Center](https://config.office.com/) para escolher qual a arquitetura, quais as funcionalidades que vai precisar do Office e com que frequência atualiza.

1. Vá ao [Microsoft 365 Apps Admin Center](https://config.office.com/) e descarregue o seu próprio ficheiro de configuração.
2. Baixar [ferramenta de implementação do office](https://www.microsoft.com/download/details.aspx?id=49117).  O ficheiro descarregado será `setup.exe` .
3. Corra `setup.exe /download configuration.xml` para descarregar componentes do Office.
4. Corra `setup.exe /configure configuration.xml` para instalar componentes do Office.

### <a name="change-the-microsoft-365-update-channel"></a>Alterar o canal de atualização Microsoft 365

Utilizando a Ferramenta de Configuração do Office, pode definir com que frequência o Office recebe atualizações. No entanto, se precisar de modificar a frequência com que o Office recebe atualizações após a instalação, pode alterar o URL do canal de atualização. Os endereços URL do canal de atualização podem ser encontrados no [alterar o canal de atualização de aplicações microsoft 365 para dispositivos na sua organização](https://docs.microsoft.com/deployoffice/change-update-channels). O exemplo abaixo mostra como definir o Microsoft 365 para usar o Canal de Atualização Mensal.

```powershell
# Update to the Microsoft 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Instalar e configurar atualizações

### <a name="install-the-latest-windows-updates"></a>Instale as últimas Atualizações do Windows

Recomendamos que instale as atualizações mais recentes da Microsoft na máquina do modelo para fins de segurança antes de publicar o VM do modelo.  Também potencialmente evita que os alunos sejam perturbados no seu trabalho quando as atualizações são executadas em momentos inesperados.

1. **Definições** de lançamento a partir do menu iniciar
2. Clique em **Atualização** & Segurança
3. Clique **em Verificar se há atualizações**
4. As atualizações serão transferidas e instaladas.

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

Recomendamos que todas as aplicações da Microsoft Store sejam atualizadas para as suas versões mais recentes.  Aqui estão as instruções para atualizar manualmente as aplicações da Microsoft Store.  

1. Lançar a aplicação **Microsoft Store.**
2. Clique na elipse (...) ao lado da foto do utilizador no canto superior da aplicação.
3. Selecione **Download** e atualizações do menu suspenso.
4. Clique no botão **de atualização.**

Também pode utilizar o PowerShell para atualizar as aplicações da Microsoft Store que já estão instaladas.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Parar atualizações automáticas do Windows

Depois de atualizar o Windows para a versão mais recente, poderá considerar parar as Atualizações do Windows.  As atualizações automáticas podem potencialmente interferir com o tempo de aula programado.  Se o seu curso for mais longo, considere pedir aos alunos que verifiquem manualmente as atualizações ou que estabeleçam atualizações automáticas para um tempo fora do horário de aula programado.  Para obter mais informações sobre as opções de personalização para o Windows Update, consulte as [definições adicionais](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings)de Atualização do Windows .

As atualizações automáticas do Windows podem ser interrompidas utilizando o seguinte script PowerShell.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Instalar pacotes de línguas estrangeiras

Se precisar de idiomas adicionais instalados na máquina virtual, pode adicioná-los através da Microsoft Store.

1. Lançar Loja microsoft
2. Procure por "pacote de linguagem"
3. Escolha o idioma para instalar

Se já estiver ligado ao modelo VM, utilize o atalho "Instalar pacote de idiomas" `ms-settings:regionlanguage?activationSource=SMC-IA-4027670` () para aceder diretamente à página de definições apropriadas.

## <a name="remove-unneeded-built-in-apps"></a>Remover aplicações incorporadas não precisas

O Windows 10 vem com muitas aplicações incorporadas que podem não ser necessárias para a sua classe em particular. Para simplificar a imagem da máquina para os alunos, é melhor desinstalar algumas aplicações da sua máquina de modelo.  Para ver uma lista de aplicações instaladas, utilize o `Get-AppxPackage` cmdlet PowerShell.  O exemplo abaixo mostra todas as aplicações instaladas que podem ser removidas.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Para remover uma aplicação, utilize o Remove-Appx cmdlet.  O exemplo abaixo mostra como remover tudo o que xBox está relacionado.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Instalar aplicações comuns relacionadas com o ensino

Instale outras aplicações comumente utilizadas para o ensino através da aplicação Windows Store. As sugestões incluem aplicações como [a app Microsoft Whiteboard,](https://www.microsoft.com/store/productId/9MSPC6MP8FM4) [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)e Minecraft [Education Edition.](https://education.minecraft.net/) Estas aplicações devem ser instaladas manualmente através da Windows Store ou através dos respetivos websites no modelo VM.

## <a name="conclusion"></a>Conclusão

Este artigo mostrou-lhe passos opcionais para preparar o seu VM modelo windows para uma classe eficaz.  Os passos incluem instalar o OneDrive e instalar o Microsoft 365, instalar as atualizações para o Windows e instalar atualizações para aplicações da Microsoft Store.  Também discutimos como definir atualizações para um horário que funciona melhor para a sua aula.  

## <a name="next-steps"></a>Passos seguintes
Consulte o artigo sobre como controlar o comportamento de paragem do Windows para ajudar na gestão de custos: [Guia para controlar o comportamento de paragem do Windows](how-to-windows-shutdown.md)
