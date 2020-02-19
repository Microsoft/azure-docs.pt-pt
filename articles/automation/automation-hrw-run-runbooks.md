---
title: Executar livros de corridas no Azure Automation Hybrid Runbook Worker
description: Este artigo fornece informações sobre a execução de livros de execução em máquinas no seu datacenter local ou fornecedor de nuvem com o papel de Trabalhador de Runbook Híbrido.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: 29b8a32989b5a1d60792fb5678f7ba8a9f12daba
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443810"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Running runbooks em um trabalhador de runbook híbrido

Não há diferença na estrutura dos livros de execução que funcionam na Automação Azure e em livros de execução que funcionam num Trabalhador híbrido do Livro. Os livros que usa com cada um provavelmente diferem significativamente. Esta diferença deve-se ao facto de os livros de corridas que visam um Trabalhador de Runbook Híbrido normalmente gerem recursos no próprio computador local ou contra recursos no ambiente local onde é implantado. Os livros de corridas em Automação Azure normalmente gerem recursos na nuvem Azure.

Quando você é autor de livros de corridas para executar em um Trabalhador de Runbook Híbrido, você deve editar e testar os livros dentro da máquina que acolhe o trabalhador Híbrido. A máquina hospedeira tem todos os módulos PowerShell e acesso à rede que precisa para gerir e aceder aos recursos locais. Uma vez testado um livro de corridas na máquina de trabalhadores Híbridos, pode então carregá-lo para o ambiente Azure Automation, onde está disponível para funcionar no trabalhador híbrido. É importante saber que os empregos que funcionam sob a conta do Sistema Local para o Windows ou uma conta de utilizador especial `nxautomation` no Linux. No Linux, isto significa que deve garantir que a conta `nxautomation` tem acesso ao local onde armazena os seus módulos. Quando utilizar o cmdlet de [Módulo de Instalação,](/powershell/module/powershellget/install-module) especifique **todos os Utilizadores** no parâmetro `-Scope` para confirmar que a conta `nxautomation` tem acesso.

Para obter mais informações sobre o PowerShell no Linux, consulte [Questões Conhecidas para PowerShell em plataformas não Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Iniciar um livro de corridas no Trabalhador híbrido do livro de corridas

O início de um Livro de [Corridas em Automação Azure](automation-starting-a-runbook.md) descreve diferentes métodos para iniciar um livro de execução. O Trabalhador do Livro de Corridas Híbrido adiciona uma opção **RunOn** onde pode especificar o nome de um Grupo de Trabalhadores de Runbook Híbrido. Quando um grupo é especificado, então o livro de execução é recuperado e gerido por um dos trabalhadores desse grupo. Se esta opção não for especificada, então é executada na Automação Azure normalmente.

Quando inicia um livro de corridas no portal Azure, é-lhe apresentado um **Run on** option onde pode selecionar O Trabalhador **Azure** ou **Híbrido**. Se selecionar **O Trabalhador Híbrido,** pode selecionar o grupo a partir de uma queda.

Utilize o parâmetro **RunOn.** Pode utilizar o seguinte comando para iniciar um livro de execução chamado Test-Runbook num Grupo de Trabalhadores híbridos chamado MyHybridGroup utilizando o Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> O parâmetro **RunOn** foi adicionado ao **cmdlet Start-AzureAutomationRunbook** na versão 0.9.1 da Microsoft Azure PowerShell. Deverá [descarregar a versão mais recente](https://azure.microsoft.com/downloads/) se tiver uma anterior instalada. Basta instalar esta versão numa estação de trabalho onde está a iniciar o livro de corridas da PowerShell. Não precisa de o instalar no computador do trabalhador a menos que pretenda iniciar os livros a partir desse computador"

## <a name="runbook-permissions"></a>Permissões de livro de corridas

Os livros de corridas que funcionam num Trabalhador de Runbook Híbrido não podem usar o mesmo método que é normalmente usado para livros de execução autenticando recursos Azure, uma vez que estão a aceder a recursos não em Azure. O livro de execução pode fornecer a sua própria autenticação aos recursos locais, ou pode configurar a autenticação usando [identidades geridas para os recursos Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Também pode especificar uma conta RunAs para fornecer um contexto de utilizador para todos os livros de execução.

### <a name="runbook-authentication"></a>Autenticação do livro de corridas

Por padrão, os livros de execução funcionam no contexto da conta do Sistema Local para windows e uma conta de utilizador especial `nxautomation` para o Linux no computador no local, pelo que devem fornecer a sua própria autenticação aos recursos a que acedem.

Pode utilizar ativos [credenciais](automation-credentials.md) e [certificados](automation-certificates.md) no seu livro de execução com cmdlets que lhe permitem especificar credenciais para que possa autenticar diferentes recursos. O exemplo que se segue mostra uma parte de um livro que reinicia um computador. Recupera credenciais de um ativo credencial e o nome do computador a partir de um ativo variável e, em seguida, utiliza estes valores com o cmdlet Restart-Computer.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Também pode utilizar o [InlineScript](automation-powershell-workflow.md#inlinescript), que lhe permite executar blocos de código noutro computador com credenciais, que são especificadas pelo [parâmetro comum PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Conta RunAs

Por padrão, o Hybrid Runbook Worker utiliza o Sistema Local para Windows e uma conta de utilizador especial `nxautomation` para o Linux executar livros de execução. Em vez de ter livros de execução fornecer a sua própria autenticação aos recursos locais, pode especificar uma conta **RunAs** para um grupo de trabalhadores híbridos. Especifica um [ativo credencial](automation-credentials.md) que tem acesso a recursos locais, incluindo lojas de certificados e todos os livros de execução executados sob estas credenciais quando se executa um Trabalhador de Runbook Híbrido no grupo.

O nome do utilizador da credencial deve estar num dos seguintes formatos:

* nome de utilizador de domínio\
* username@domain
* nome de utilizador (para contas locais para o computador no local)

Utilize o seguinte procedimento para especificar uma conta RunAs para um grupo de trabalhadores híbridos:

1. Criar um [ativo credencial](automation-credentials.md) com acesso aos recursos locais.
2. Abra a conta Automation no portal Azure.
3. Selecione o azulejo **dos Grupos De Trabalho Híbrido** e, em seguida, selecione o grupo.
4. Selecione **todas as definições** e, em **seguida, configurações do grupo de trabalhadores híbridos**.
5. Alterar **o executar de** **padrão** para **personalizado**.
6. Selecione a credencial e clique **em Guardar**.

### <a name="managed-identities-for-azure-resources"></a>Identidades Geridas para recursos azure

Os trabalhadores híbridos do Livro de Corridas que funcionam em máquinas virtuais Azure podem usar identidades geridas para os recursos do Azure autenticar em recursos Azure. Existem muitos benefícios em usar identidades geridas para recursos Azure sobre contas Run As.

* Não há necessidade de exportar o Certificado Run As e, em seguida, importá-lo para Hybrid Runbook Worker
* Não há necessidade de renovar o certificado usado pela conta Run As
* Não há necessidade de lidar com o Run As connection object no seu código de resta

Para utilizar uma identidade gerida para os recursos Azure num trabalhador híbrido do Livro de Corridas, é necessário completar os seguintes passos:

1. Criar um VM Azure
2. [Configure identidades geridas para recursos Azure no seu VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. [Conceda o seu acesso vM a um grupo](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) de recursos em tutorial-windows-vm-access-arm.md#get-an-access-token-using-the-vms-system-assigned-identity-and-use-it-to-call-azure-resource-manager)
4. [Instale o Trabalhador](automation-windows-hrw-install.md) do Livro Híbrido do Windows na máquina virtual.

Uma vez concluídos os passos anteriores, pode utilizar `Connect-AzureRmAccount -Identity` no livro de execução para autenticar os recursos do Azure. Esta configuração reduz a necessidade de utilizar uma Conta Run As e gerir o certificado para a conta Run As.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

> [!NOTE]
> `Connect-AzureRMAccount -Identity` trabalha para um Trabalhador de Runbook Híbrido utilizando um sistema de identidade atribuída e uma identidade atribuída a um único utilizador. Se necessitar de utilizar várias identidades atribuídas ao utilizador na HRW, deve especificar o parâmetro `-AccountId` para selecionar a identidade específica atribuída ao utilizador.

### <a name="runas-script"></a>Execução de automação Como conta

Como parte do seu processo automatizado de construção para a implantação de recursos no Azure, poderá necessitar de acesso a sistemas no local para suportar uma tarefa ou conjunto de passos na sua sequência de implantação. Para suportar a autenticação contra o Azure utilizando a conta Run As, é necessário instalar o certificado de conta Run As.

O seguinte livro de execução powerShell, **Export-RunAsCertificateToHybridWorker,** exporta o Certificado De Execução Como certificado da sua conta Azure Automation e descarrega e importa para a loja de certificados de máquina local num trabalhador híbrido, que está ligado à mesma conta. Uma vez concluído esse passo, verifica-se que o trabalhador pode autenticar com sucesso o Azure utilizando a conta Run As.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

> [!IMPORTANT]
> **Add-AzureRmAccount** é agora um pseudónimo para **Connect-AzureRMAccount**. Ao pesquisar os itens da sua biblioteca, se não vir **o Connect-AzureRMAccount,** pode utilizar **add-AzureRmAccount,** ou pode atualizar os seus módulos na sua Conta de Automação.

Guarde o livro de execução *Export-RunAsCertificateToHybridWorker* para o seu computador com uma extensão `.ps1`. Importe-o na sua conta Automation e edite o livro de execução, alterando o valor da variável `$Password` com a sua própria senha. Publique e, em seguida, executar o livro de execução. Direcione o grupo Hybrid Worker que executará e autenticará os livros de execução utilizando a conta Run As. O fluxo de emprego relata a tentativa de importar o certificado para a loja de máquinas local, e segue com várias linhas. Este comportamento depende de quantas contas de Automação define na sua subscrição e se a autenticação for bem sucedida.

## <a name="job-behavior"></a>Comportamento de trabalho

Os empregos são tratados de forma ligeiramente diferente nos Trabalhadores híbridos do livro de corridas do que quando funcionam em caixas de areia Azure. Uma diferença fundamental é que não há limite para a duração do trabalho nos trabalhadores híbridos. Os livros de corridas executados em caixas de areia Azure estão limitados a 3 horas por causa de [uma quota justa.](automation-runbook-execution.md#fair-share) Para um livro de corridas de longa duração, você deve ter certeza de que é resistente a um possível recomeço. Por exemplo, se a máquina que acolhe o trabalhador híbrido reiniciar. Se a máquina de hospedar do trabalhador híbrido reiniciar, então qualquer trabalho de resta em execução reinicia desde o início, ou a partir do último ponto de verificação para os livros de execução powerShell Workflow. Depois de um trabalho de rés-do-livro ser reiniciado mais de 3 vezes, então é suspenso.

## <a name="run-only-signed-runbooks"></a>Executar apenas livros de execução assinados

Os Trabalhadores híbridos do Runbook podem ser configurados para executar apenas livros de execução assinados com alguma configuração. A secção seguinte descreve como configurar os seus Trabalhadores híbridos do livro de corridas para executar o [Windows Hybrid Runbook Worker](#windows-hybrid-runbook-worker) e o [Linux Hybrid Runbook Worker](#linux-hybrid-runbook-worker)

> [!NOTE]
> Uma vez configurado um Trabalhador do Livro Híbrido para executar apenas livros de execução assinados, os livros de execução que **não** tenham sido assinados não serão executados no trabalhador.

### <a name="windows-hybrid-runbook-worker"></a>Trabalhador de runbook híbrido windows

#### <a name="create-signing-certificate"></a>Criar certificado de assinatura

O exemplo seguinte cria um certificado auto-assinado que pode ser usado para a assinatura de livros de execução. A amostra cria o certificado e exporta-o. O certificado é importado para os Trabalhadores híbridos do livro de corridas mais tarde. A impressão digital também é devolvida, este valor é usado mais tarde para fazer referência ao certificado.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

#### <a name="configure-the-hybrid-runbook-workers"></a>Configure os trabalhadores híbridos do livro de corridas

Copie o certificado criado a cada Trabalhador Híbrido do Livro de Corridas em grupo. Executar o seguinte script para importar o certificado e configurar o Trabalhador Híbrido para utilizar validação de assinatura em livros de execução.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

#### <a name="sign-your-runbooks-using-the-certificate"></a>Assine os seus Livros de Execução usando o certificado

Com os trabalhadores híbridos do Runbook configurados para utilizar apenas livros de execução assinados, deve assinar livros de execução que devem ser utilizados no Trabalhador do Livro de Corridas Híbrido. Utilize a seguinte amostra PowerShell para assinar os seus livros de execução.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Quando o livro de execução tiver sido assinado, este deve ser importado para a sua Conta de Automação e publicado com o bloco de assinaturas. Para aprender a importar livros de execução, consulte [importar um livro de execução de um ficheiro para a Automação Azure.](manage-runbooks.md#import-a-runbook)

### <a name="linux-hybrid-runbook-worker"></a>Trabalhador de runbook híbrido linux

Para assinar livros de execução num Trabalhador de Runbook Híbrido Linux, o seu Trabalhador de Runbook Híbrido precisa de ter o [GPG](https://gnupg.org/index.html) executado na máquina.

#### <a name="create-a-gpg-keyring-and-keypair"></a>Criar um teclado GPG e um teclado

Para criar o teclado e o par de chaves, terá de utilizar a conta Hybrid Runbook Worker `nxautomation`.

Use `sudo` para iniciar sessão como a conta `nxautomation`.

```bash
sudo su – nxautomation
```

Uma vez que utilize a conta `nxautomation`, gere o par de chaves gpg.

```bash
sudo gpg --generate-key
```

O GPG irá guiá-lo através dos passos para criar o par de chaves. Terá de fornecer um nome, um endereço de e-mail, tempo de validade, frase de passe e esperar por uma entropia suficiente na máquina para que a chave seja gerada.

Como o diretório GPG foi gerado com o sudo, é preciso mudar o seu proprietário para `nxautomation`.

Executar o seguinte comando para mudar o proprietário.

```bash
sudo chown -R nxautomation ~/.gnupg
```

#### <a name="make-the-keyring-available-the-hybrid-runbook-worker"></a>Disponibilize o porta-chaves do Trabalhador do Livro Híbrido

Assim que o teclado for criado, terá de o disponibilizar ao Trabalhador híbrido do livro. Modificar o ficheiro de definições `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf` para incluir o seguinte exemplo na secção `[worker-optional]`

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

#### <a name="verify-signature-validation-is-on"></a>Verificar se a validação de assinatura está em

Se a validação de assinatura sou desativada na máquina, terá de ligá-la. Executar o seguinte comando para permitir a validação da assinatura. Substituir `<LogAnalyticsworkspaceId>` com o seu ID do espaço de trabalho.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

#### <a name="sign-a-runbook"></a>Assine um livro de corridas

Uma vez configurada a validação da assinatura, pode utilizar o seguinte comando para assinar um livro de execução:

```bash
gpg –-clear-sign <runbook name>
```

O livro de execução assinado terá o nome `<runbook name>.asc`.

O livro de execução assinado pode agora ser enviado para a Azure Automation, e pode ser executado como um livro regular.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os diferentes métodos que podem ser usados para iniciar um livro de corridas, consulte [Iniciar um Livro de Corridas em Automação Azure](automation-starting-a-runbook.md).
* Para compreender as diferentes formas de trabalhar com os livros powerShell na Azure Automation usando o editor textual, consulte [Editar um Livro de Corridas em Automação Azure](automation-edit-textual-runbook.md)
* Se os seus livros não estiverem a completar com sucesso, reveja o guia de resolução de problemas sobre falhas de [execução de livros](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)de corridas .
* Para obter mais informações sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte os [Docs PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
