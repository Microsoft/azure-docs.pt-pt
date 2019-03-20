---
title: Instalar o PowerShell para o Azure Stack | Documentos da Microsoft
description: Saiba como instalar o PowerShell para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 02/08/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 4e623c6a2423d2e61334932d0c40f05e548d3c38
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109870"
---
# <a name="install-powershell-for-azure-stack"></a>Instalar o PowerShell para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Para trabalhar com a sua cloud, tem de instalar módulos do PowerShell do Azure Stack compatíveis. Compatibilidade é ativada através de um recurso chamado *perfis de API*.

Os perfis de API fornecem uma forma de gerenciar as diferenças de versão entre o Azure e o Azure Stack. Um perfil da versão de API é um conjunto de módulos do PowerShell do Azure Resource Manager com as versões de API específicas. Cada plataforma de cloud tem um conjunto de perfis de versão de API suportados. Por exemplo, o Azure Stack suporta uma versão de perfil específico, tal como **2018-03-01-híbrida**. Ao instalar um perfil, são instalados os módulos do PowerShell do Azure Resource Manager que correspondem para o perfil especificado.

Pode instalar o Azure Stack módulos do PowerShell compatíveis na Internet conectados, parcialmente ligado ou desligado cenários. Este artigo explica as instruções detalhadas para instalar o PowerShell para o Azure Stack para estes cenários.

## <a name="1-verify-your-prerequisites"></a>1. Verifique os pré-requisitos

Antes de começar a utilizar com o Azure Stack e o PowerShell, tem de ter os seguintes pré-requisitos:

- **PowerShell versão 5.0** para verificar a sua versão, execute **$PSVersionTable.PSVersion** e comparar o **principais** versão. Se não tiver PowerShell 5.0, siga os [instalar o Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

  > [!Note]
  > PowerShell 5.0 requer uma máquina Windows.

- **Executar o Powershell num prompt de comando elevado**.
  Tem de executar o PowerShell com privilégios administrativos.

- **Acesso de galeria do PowerShell** precisa de acesso para o [galeria do PowerShell](https://www.powershellgallery.com). A galeria é o repositório central para o conteúdo do PowerShell. O **PowerShellGet** módulo contém cmdlets para descobrir, instalar, atualizar e publicar artefactos de PowerShell, como módulos, recursos de DSC, funcionalidades de função e scripts a partir da galeria do PowerShell e outros privados repositórios. Se estiver a utilizar o PowerShell num cenário de desligado, tem de recuperar os recursos de uma máquina com uma ligação à Internet e armazená-las numa localização acessível para o seu computador desconectado.


## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Validar a acessibilidade de galeria do PowerShell

Valide se PSGallery está registado como um repositório.

> [!Note]  
> Este passo requer acesso à Internet.

Abra uma linha de comandos elevada do PowerShell e execute os seguintes cmdlets:

```PowerShell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

Se não estiver registado no repositório, abra uma sessão elevada do PowerShell e execute o seguinte comando:

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Desinstalar as versões existentes dos módulos do Azure Stack do PowerShell

Antes de instalar a versão necessária, certifique-se de que desinstale qualquer instalados anteriormente módulos do Azure Stack do AzureRM PowerShell. Pode desinstalá-las através de um dos seguintes dois métodos:

1. Para desinstalar os módulos AzureRM PowerShell existentes, feche todas as sessões ativas do PowerShell e execute os seguintes cmdlets:

    ```PowerShell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```
    Se encontrou um erro, como "o módulo já está em utilização", feche as sessões do PowerShell que estão a utilizar os módulos e volte a executar o script acima.

2. Eliminar todas as pastas que começam com `Azure` ou `Azs.` partir do `C:\Program Files\WindowsPowerShell\Modules` e `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` pastas. A eliminar essas pastas remove quaisquer módulos existentes do PowerShell.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Ligado: Instalar o PowerShell para o Azure Stack com ligação à Internet

O Azure Stack requer os **2018-03-01-híbrida** perfil da versão de API para a versão do Azure Stack 1808 ou posterior. O perfil está disponível ao instalar o **AzureRM.Bootstrapper** módulo. Além disso, para os módulos AzureRM, deve também instalar os módulos específicos de pilha do Azure PowerShell. O perfil da versão de API e módulos do Azure Stack do PowerShell necessárias variam consoante a versão do Azure Stack está a executar.

Instalação tem três passos:

1. Instalar o Azure Stack do PowerShell dependendo da versão do Azure Stack
2. Ativar funcionalidades de armazenamento adicional
3. Confirmar a instalação do PowerShell

### <a name="install-azure-stack-powershell"></a>Instalar o PowerShell para o Azure Stack

Execute o seguinte script do PowerShell para instalar esses módulos em sua estação de trabalho de desenvolvimento:

- O Azure Stack 1901 ou posterior:

    ```PowerShell
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Install-Module AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.0
    ```

    > [!Note]  
    > A versão do módulo do Azure Stack 1.7.0 é uma versão de alteração de última hora. Para migrar a partir do Azure Stack 1.6.0, consulte a [guia de migração](https://aka.ms/azspshmigration170).
    > O versão do módulo 2.4.0 do AzureRm vem com uma alteração de última hora para o cmdlet Remove-AzureRmStorageAccount. Este cmdlet espera - Force prameter seja especificado para remover a conta de armazenamento sem confirmação.
- Azure Stack 1811:

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

- O Azure Stack 1810 ou anterior:

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

> [!Note]  
> Para atualizar o Azure PowerShell a partir do **2017-03-09-perfil** para **2018-03-01-híbrida**, consulte a [guia de migração](https://github.com/azure/azure-powershell/blob/AzureRM/documentation/migration-guides/Stack/migration-guide.2.3.0.md).

### <a name="enable-additional-storage-features"></a>Ativar funcionalidades de armazenamento adicional

Para facilitar a usar os recursos de armazenamento adicional (mencionados na seção ligada), download e instale os seguintes pacotes.

```PowerShell
# Install the Azure.Storage module version 4.5.0
Install-Module -Name Azure.Storage -RequiredVersion 4.5.0 -Force -AllowClobber

# Install the AzureRm.Storage module version 5.0.4
Install-Module -Name AzureRM.Storage -RequiredVersion 5.0.4 -Force -AllowClobber

# Remove incompatible storage module installed by AzureRM.Storage
Uninstall-Module Azure.Storage -RequiredVersion 4.6.1 -Force

# Load the modules explicitly specifying the versions
Import-Module -Name Azure.Storage -RequiredVersion 4.5.0
Import-Module -Name AzureRM.Storage -RequiredVersion 5.0.4
```

### <a name="confirm-the-installation-of-powershell"></a>Confirmar a instalação do PowerShell

Confirme a instalação executando o seguinte comando:

```PowerShell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Se a instalação for bem-sucedida, os módulos AzureRM e AzureStack são apresentados no resultado.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Desligado: Instalar o PowerShell sem uma ligação à Internet

Num cenário de desligado, tem de transferir primeiro os módulos do PowerShell para um computador que tenha conectividade à Internet e, em seguida, transferi-las para o Development Kit do Azure Stack para a instalação.

Inicie sessão computador com ligação à Internet e utilize os seguintes scripts para transferir os pacotes do Azure Resource Manager e AzureStack, dependendo da versão do Azure Stack.

Instalação tem quatro etapas:

1. Instalar o Azure Stack do PowerShell para um computador ligado à
2. Ativar funcionalidades de armazenamento adicional
3. Os pacotes do PowerShell para a sua estação de trabalho desligada de transporte
4. Confirmar a instalação do PowerShell


### <a name="install-azure-stack-powershell"></a>Instalar o PowerShell para o Azure Stack

- O Azure Stack 1901 ou posterior.

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.4.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.0
    ```

    > [!Note]  
    > A versão do módulo do Azure Stack 1.7.0 é uma alteração de última hora. Para migrar a partir de AzureStack 1.6.0, consulte a [guia de migração](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack).


  - O Azure Stack 1811 ou anterior.

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
    ```

  - O Azure Stack 1809 ou anterior.

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ```

    > [!NOTE]
    > Em máquinas sem ligação à Internet, recomendamos executar o cmdlet seguinte para desativar a recolha de dados de telemetria. Pode ocorrer uma degradação do desempenho do cmldets sem desativar a recolha de dados de telemetria. Isto é aplicável apenas para as máquinas sem ligação à internet
    > ```PowerShell
    > Disable-AzureRmDataCollection
    > ```

### <a name="enable-additional-storage-features"></a>Ativar funcionalidades de armazenamento adicional

Para facilitar a usar os recursos de armazenamento adicional (mencionados na seção ligada), download e instale os seguintes pacotes.

```PowerShell
$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azure.Storage -Path $Path -Force -RequiredVersion 4.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRm.Storage -Path $Path -Force -RequiredVersion 5.0.4
```

### <a name="add-your-packages-to-your-workstation"></a>Adicionar os pacotes para a estação de trabalho

1. Copie os pacotes transferidos para um dispositivo USB.

2. Inicie sessão na estação de trabalho desligada e copie os pacotes do dispositivo USB num local na estação de trabalho.

3. Agora, registe-se nesta localização, como o repositório de padrão e instalar os módulos AzureRM e AzureStack partir este repositório:

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

### <a name="confirm-the-installation-of-powershell"></a>Confirmar a instalação do PowerShell

Confirme a instalação executando o seguinte comando:

```PowerShell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Configurar o PowerShell para utilizar um servidor proxy

Em cenários que necessitam de um servidor proxy para aceder à Internet, primeiro tem de configurar o PowerShell para utilizar um servidor de proxy existente:

1. Abra uma linha de comandos elevada do PowerShell.
2. Execute os seguintes comandos:

   ```PowerShell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>Passos Seguintes

 - [Transferir ferramentas do Azure Stack a partir do GitHub](azure-stack-powershell-download.md)
 - [Configurar o ambiente do PowerShell do utilizador do Azure Stack](user/azure-stack-powershell-configure-user.md)
 - [Configurar o ambiente do PowerShell da operadora do Azure Stack](azure-stack-powershell-configure-admin.md)
 - [Gerir perfis de versão de API no Azure Stack](user/azure-stack-version-profiles.md)
