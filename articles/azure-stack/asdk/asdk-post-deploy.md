---
title: Publicar as configurações de implementação para o Azure Stack Development Kit (ASDK) | Documentos da Microsoft
description: Descreve as alterações de configuração recomendada para fazer depois de instalar o Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/10/2018
ms.openlocfilehash: b1b11dc27b279173ede4498ca353aea4018ea8f9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102439"
---
# <a name="post-asdk-installation-configuration-tasks"></a>Pós-instalação de ASDK tarefas de configuração

Após [instalar o Azure Stack Development Kit (ASDK)](asdk-install.md), deve fazer algumas alterações de configuração de pós-instalação recomendada enquanto a sessão iniciada como AzureStack\AzureStackAdmin no computador anfitrião ASDK. 

## <a name="install-azure-stack-powershell"></a>Instalar o PowerShell para o Azure Stack

Módulos do Azure Stack compatíveis do PowerShell do Azure são necessários para trabalhar com o Azure Stack.

Comandos do PowerShell para o Azure Stack são instalados através da galeria do PowerShell. Para registar o repositório de PSGallery, abra uma sessão elevada do PowerShell e execute o seguinte comando:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Pode utilizar perfis de versão de API para especificar os módulos AzureRM compatíveis do Azure Stack.  Os perfis de versão de API fornecem uma forma de gerenciar as diferenças de versão entre o Azure e o Azure Stack. Um perfil da versão de API é um conjunto de módulos AzureRM PowerShell em versões de API específicas. O **AzureRM.Bootstrapper** módulo que está disponível através da galeria do PowerShell fornece cmdlets do PowerShell que são necessárias para trabalhar com perfis de versão de API.

Pode instalar o módulo mais recente do Azure Stack do PowerShell com ou sem ligação à Internet para o computador do anfitrião ASDK:

> [!IMPORTANT]
> Antes de instalar a versão necessária, certifique-se de que [desinstalar quaisquer módulos existentes do Azure PowerShell](../azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules).

- **Com uma ligação à internet** do computador anfitrião ASDK. Execute o seguinte script do PowerShell para instalar estes módulos na sua instalação do kit de desenvolvimento:

- O Azure Stack 1901 ou posterior:

    ```PowerShell
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Install-Module AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.0
    ```

    > [!Note]  
    > A versão do módulo do Azure Stack 1.7.0 é uma alteração de última hora. Para migrar a partir do Azure Stack 1.6.0, consulte a [guia de migração](https://aka.ms/azspshmigration170).

  - Azure Stack 1811:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.6.0.
    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

  - O Azure Stack 1810 ou anterior:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.5.0.
    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

  Se a instalação for bem-sucedida, os módulos AzureRM e AzureStack são apresentados no resultado.

- **Sem uma ligação à internet** do computador anfitrião ASDK. Num cenário de desligado, primeiro tem de transferir os módulos do PowerShell para uma máquina que tenha conectividade à internet com os seguintes comandos do PowerShell:

  ```PowerShell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  Em seguida, copie os pacotes transferidos para o computador ASDK e registe-se a localização como o repositório de padrão e instalar os módulos AzureRM e AzureStack partir este repositório:

    ```PowerShell  
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

    Install-Module AzureRM -Repository $RepoName

    Install-Module AzureStack -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Baixe as ferramentas do Azure Stack

[Ferramentas de AzureStack](https://github.com/Azure/AzureStack-Tools) é um repositório do GitHub que aloja os módulos do PowerShell para gerir e implementar recursos no Azure Stack. Para obter essas ferramentas, clone o repositório do GitHub ou transfira a pasta de ferramentas de AzureStack executando o seguinte script:

  ```PowerShell
  # Change directory to the root directory.
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip -DestinationPath . -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Validar a instalação de ASDK

Para garantir que a implementação de ASDK foi concluída com êxito, pode utilizar o cmdlet Test-AzureStack seguindo estes passos:

1. Inicie sessão como AzureStack\AzureStackAdmin, no computador anfitrião ASDK.
2. Abra o PowerShell como administrador (não ISE do PowerShell).
3. Execute: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Execute: `Test-AzureStack`

Os testes demorar alguns minutos a concluir. Se a instalação foi concluída com êxito, a saída é parecido com:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Se tiver ocorrido uma falha, siga os passos de resolução de problemas para obter ajuda.

## <a name="reset-the-password-expiration-policy"></a>Repor a política de expiração de palavra-passe 

Para certificar-se de que a palavra-passe para o anfitrião do kit de desenvolvimento não expira antes de terminar o período de avaliação, siga estes passos depois de implementar o ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Para alterar a política de expiração de palavra-passe a partir do Powershell

A partir de uma consola elevada do Powershell, execute o comando:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Para alterar manualmente a política de expiração de palavra-passe

1. No anfitrião do kit de desenvolvimento, abra **gerenciamento de diretiva de grupo** (GPMC. MMC) e navegue para **gerenciamento de diretiva de grupo** – **floresta: azurestack** – **domínios** – **azurestack**.
2. Com o botão direito **política de domínio predefinida** e clique em **editar**.
3. No Editor diretiva de grupo gestão, navegue até **configuração do computador** – **políticas** – **definições do Windows** – **configurações de segurança**– **Políticas de conta** – **política de palavra-passe**.
4. No painel da direita, faça duplo clique **idade de palavra-passe máximo**.
5. No **idade de palavra-passe máximo propriedades** caixa de diálogo, alteração a **palavra-passe irá expirar dentro de** valor **180**e, em seguida, clique em **OK**.

![Console de gerenciamento de diretiva de grupo](media/asdk-post-deploy/gpmc.png)

## <a name="enable-multi-tenancy"></a>Ativar multi-inquilinos

Para Implantações com o Azure AD, terá [ativar multi-inquilinos](../azure-stack-enable-multitenancy.md#enable-multi-tenancy) para a sua instalação ASDK.

> [!NOTE]
> Quando as contas de utilizador ou administrador de domínios que não seja utilizada para registar o Azure Stack são utilizadas para iniciar sessão portal do Azure Stack, o nome de domínio utilizado para registar o Azure Stack deve ser anexado ao portal do url. Por exemplo, se o Azure Stack foi registado com fabrikam.onmicrosoft.com e a conta de utilizador iniciar sessão está admin@contoso.com, o url a utilizar para iniciar sessão no portal de utilizador seria: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Passos Seguintes

[Registe o ASDK com o Azure](asdk-register.md)
