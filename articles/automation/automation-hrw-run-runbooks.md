---
title: Executar runbooks na Hybrid Runbook Worker de automação do Azure
description: Este artigo fornece informações sobre a execução de runbooks em computadores em seu datacenter local ou provedor de nuvem com a função Hybrid Runbook Worker.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 01/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c10262e50fff2903d7caf242304145a2ab93dbcd
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970607"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Executando runbooks em um Hybrid Runbook Worker

Não há nenhuma diferença na estrutura de runbooks que são executados na automação do Azure e runbooks que são executados em um Hybrid Runbook Worker. Os Runbooks que você usa com cada um provavelmente diferem significativamente. Essa diferença ocorre porque os runbooks direcionados a um Hybrid Runbook Worker normalmente gerenciam recursos no próprio computador local ou em recursos no ambiente local onde ele é implantado. Os Runbooks na automação do Azure normalmente gerenciam recursos na nuvem do Azure.

Ao criar runbooks para serem executados em um Hybrid Runbook Worker, você deve editar e testar os runbooks dentro do computador que hospeda o Hybrid Worker. O computador host tem todos os módulos do PowerShell e o acesso à rede que você precisa para gerenciar e acessar os recursos locais. Depois que um runbook é testado no computador do Hybrid Worker, você pode carregá-lo no ambiente de automação do Azure onde ele está disponível para ser executado no Hybrid Worker. É importante saber que os trabalhos executados na conta sistema local para Windows ou uma conta `nxautomation` de usuário especial no Linux. No Linux, isso significa que você deve garantir que a `nxautomation` conta tenha acesso ao local onde você armazena seus módulos. Ao usar o cmdlet [install-Module]() , especifique **AllUsers** para o `-Scope` parâmetro para confirmar que a `naxautomation` conta tem acesso.

Para obter mais informações sobre o PowerShell no Linux, consulte [problemas conhecidos do PowerShell em plataformas que não são do Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Iniciando um runbook no Hybrid Runbook Worker

[Iniciar um runbook na automação do Azure](automation-starting-a-runbook.md) descreve métodos diferentes para iniciar um runbook. Hybrid Runbook Worker adiciona uma opção **RunOn** onde você pode especificar o nome de um grupo de Hybrid runbook Worker. Quando um grupo é especificado, o runbook é recuperado e executado por um dos trabalhadores nesse grupo. Se essa opção não for especificada, ela será executada na automação do Azure normalmente.

Ao iniciar um runbook no portal do Azure, você verá uma opção **executar em, na** qual poderá selecionar **Azure** ou **Hybrid Worker**. Se você selecionar **Hybrid Worker**, poderá selecionar o grupo em uma lista suspensa.

Use o parâmetro **RunOn** . Você pode usar o seguinte comando para iniciar um runbook chamado Test-runbook em um grupo de Hybrid Runbook Worker chamado myhíbridos usando o Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> O parâmetro **RunOn** foi adicionado ao cmdlet **Start-AzureAutomationRunbook** na versão 0.9.1 do Microsoft Azure PowerShell. Você deve [baixar a versão mais recente](https://azure.microsoft.com/downloads/) se tiver uma instalada anteriormente. Você só precisa instalar essa versão em uma estação de trabalho na qual você está iniciando o runbook do PowerShell. Você não precisa instalá-lo no computador de trabalho, a menos que pretenda iniciar runbooks desse computador "

## <a name="runbook-permissions"></a>Permissões de runbook

Os runbooks em execução em um Hybrid Runbook Worker não podem usar o mesmo método que normalmente é usado para autenticação de runbooks nos recursos do Azure, já que eles acessam recursos que não estão no Azure. O runbook pode fornecer sua própria autenticação para recursos locais ou pode configurar a autenticação usando [identidades gerenciadas para recursos](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager
)do Azure. Você também pode especificar uma conta Executar como para fornecer um contexto de usuário para todos os runbooks.

### <a name="runbook-authentication"></a>Autenticação de runbook

Por padrão, os runbooks são executados no contexto da conta do sistema local para Windows e uma conta `nxautomation` de usuário especial para o Linux no computador local, para que eles devam fornecer sua própria autenticação aos recursos que acessam.

Você pode usar [](automation-credentials.md) os ativos de credencial e [certificado](automation-certificates.md) em seu runbook com cmdlets que permitem especificar credenciais para que você possa se autenticar em diferentes recursos. O exemplo a seguir mostra uma parte de um runbook que reinicia um computador. Ele recupera as credenciais de um ativo de credencial e o nome do computador de um ativo variável e, em seguida, usa esses valores com o cmdlet Restart-Computer.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Você também pode usar [InlineScript](automation-powershell-workflow.md#inlinescript), que permite executar blocos de código em outro computador com credenciais, que são especificadas pelo [parâmetro comum PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Conta Executar como

Por padrão, o Hybrid runbook Worker usa o sistema local para Windows e uma conta `nxautomation` de usuário especial para o Linux executar runbooks. Em vez de fazer com que os runbooks forneçam sua própria autenticação para recursos locais, você pode especificar uma conta **runas** para um grupo do Hybrid Worker. Você especifica um [ativo](automation-credentials.md) de credencial que tem acesso aos recursos locais e todos os runbooks são executados sob essas credenciais quando executados em um Hybrid runbook Worker no grupo.

O nome de usuário para a credencial deve estar em um dos seguintes formatos:

* Domain
* username@domain
* nome de usuário (para contas locais para o computador local)

Use o procedimento a seguir para especificar uma conta RunAs para um grupo do Hybrid Worker:

1. Crie um [ativo](automation-credentials.md) de credencial com acesso a recursos locais.
2. Abra a conta de automação no portal do Azure.
3. Selecione o bloco **grupos de Hybrid Worker** e, em seguida, selecione o grupo.
4. Selecione **todas as configurações** e, em seguida, **configurações do grupo do Hybrid Worker**.
5. Altere **Executar como** de **padrão** para **personalizado**.
6. Selecione a credencial e clique em **salvar**.

### <a name="managed-identities-for-azure-resources"></a>Identidades gerenciadas para recursos do Azure

Hybrid runbook Workers em execução em máquinas virtuais do Azure podem usar identidades gerenciadas para recursos do Azure para autenticar os recursos do Azure. Há muitos benefícios em usar identidades gerenciadas para recursos do Azure em contas Executar como.

* Não é necessário exportar o certificado executar como e, em seguida, importá-lo para Hybrid Runbook Worker
* Não é necessário renovar o certificado usado pela conta Executar como
* Não é necessário manipular o objeto de conexão executar como em seu código de runbook

Para usar uma identidade gerenciada para recursos do Azure em um Hybrid runbook Worker, você precisa concluir as seguintes etapas:

1. Criar uma VM do Azure
2. [Configurar identidades gerenciadas para recursos do Azure em sua VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. [Conceder à sua VM acesso a um grupo de recursos no Gerenciador de recursos](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
4. [Obter um token de acesso usando a identidade gerenciada atribuída pelo sistema da VM](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager)
5. [Instale o Hybrid runbook Worker do Windows](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) na máquina virtual.

Depois que as etapas anteriores forem concluídas, você `Connect-AzureRmAccount -Identity` poderá usar o no runbook para autenticar os recursos do Azure. Essa configuração reduz a necessidade de usar uma conta Executar como e gerenciar o certificado para a conta Executar como.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

### <a name="runas-script"></a>Conta Executar como de automação

Como parte do processo de compilação automatizado para a implantação de recursos no Azure, você pode precisar de acesso a sistemas locais para dar suporte a uma tarefa ou a um conjunto de etapas em sua sequência de implantação. Para dar suporte à autenticação no Azure usando a conta Executar como, você precisa instalar o certificado da conta Executar como.

O runbook do PowerShell a seguir, **Export-RunAsCertificateToHybridWorker**, exporta o certificado executar como da sua conta de automação do Azure e baixa e importa para o repositório de certificados do computador local em um trabalhador híbrido, que está conectado para a mesma conta. Depois que essa etapa for concluída, ela verificará se o trabalho pode se autenticar com êxito no Azure usando a conta Executar como.

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
AUTHOR: Azure Automation Team
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
> **Add-AzureRmAccount** agora é um alias para **Connect-AzureRmAccount**. Ao pesquisar os itens de biblioteca, se você não vir **Connect-AzureRMAccount**, poderá usar **Add-AzureRMAccount**ou poderá atualizar seus módulos na sua conta de automação.

Salve o runbook *Export-RunAsCertificateToHybridWorker* em seu computador com uma `.ps1` extensão. Importe-o para sua conta de automação e edite o runbook, alterando o `$Password` valor da variável com sua própria senha. Publique e, em seguida, execute o runbook. Direcione o grupo de Hybrid Worker que executará e autenticará runbooks usando a conta Executar como. O fluxo de trabalho relata a tentativa de importar o certificado para o repositório do computador local e segue com várias linhas. Esse comportamento depende de quantas contas de automação você define em sua assinatura e se a autenticação é bem-sucedida.

## <a name="job-behavior"></a>Comportamento do trabalho

Os trabalhos são tratados um pouco diferentes em Hybrid runbook Workers do que quando são executados em áreas restritas do Azure. Uma diferença importante é que não há limite para a duração do trabalho em Hybrid runbook Workers. Os Runbooks executados nas áreas restritas do Azure são limitados a 3 horas devido à [Fair share](automation-runbook-execution.md#fair-share). Para um runbook de execução longa, você deseja garantir que ele seja resiliente à possível reinicialização. Por exemplo, se o computador que hospeda as reinicializações do Hybrid Worker. Se o computador host do Hybrid Worker reiniciar, qualquer trabalho de runbook em execução será reiniciado desde o início ou do último ponto de verificação para runbooks do fluxo de trabalho do PowerShell. Depois que um trabalho de runbook é reiniciado mais de três vezes, ele é suspenso.

## <a name="run-only-signed-runbooks"></a>Executar apenas Runbooks assinados

Hybrid runbook workers podem ser configurados para executar apenas runbooks assinados com algumas configurações. A seção a seguir descreve como configurar seus Hybrid runbook Workers para executar o [Windows Hybrid runbook Worker](#windows-hybrid-runbook-worker) e [Linux](#linux-hybrid-runbook-worker) assinados Hybrid runbook Worker

> [!NOTE]
> Depois de configurar um Hybrid Runbook Worker para executar apenas runbooks assinados, os runbooks que **não** foram assinados não serão executados no trabalho.

### <a name="windows-hybrid-runbook-worker"></a>Hybrid Runbook Worker do Windows

#### <a name="create-signing-certificate"></a>Criar certificado de autenticação

O exemplo a seguir cria um certificado autoassinado que pode ser usado para assinar runbooks. O exemplo cria o certificado e o exporta. O certificado é importado para os Hybrid runbook Workers mais tarde. A impressão digital também é retornada, esse valor é usado posteriormente para fazer referência ao certificado.

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

#### <a name="configure-the-hybrid-runbook-workers"></a>Configurar os Hybrid runbook Workers

Copie o certificado criado para cada Hybrid Runbook Worker em um grupo. Execute o script a seguir para importar o certificado e configurar o Hybrid Worker para usar a validação de assinatura em runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

#### <a name="sign-your-runbooks-using-the-certificate"></a>Assinar seus Runbooks usando o certificado

Com os Hybrid runbook Workers configurados para usar apenas runbooks assinados, você deve assinar runbooks que serão usados no Hybrid Runbook Worker. Use o PowerShell de exemplo a seguir para assinar seus runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Quando o runbook tiver sido assinado, ele deverá ser importado para sua conta de automação e publicado com o bloco de assinatura. Para saber como importar runbooks, consulte importando [um runbook de um arquivo para a automação do Azure](manage-runbooks.md#import-a-runbook).

### <a name="linux-hybrid-runbook-worker"></a>Hybrid Runbook Worker Linux

Para assinar runbooks em um Hybrid Runbook Worker do Linux, sua Hybrid Runbook Worker precisa ter o executável [GPG](https://gnupg.org/index.html) presente no computador.

#### <a name="create-a-gpg-keyring-and-keypair"></a>Criar um keyring GPG e um par de chaves

Para criar o keyring e o par de chaves, você precisará usar a `nxautomation`conta de Hybrid runbook Worker.

Use `sudo` para entrar como a `nxautomation` conta.

```bash
sudo su – nxautomation
```

Depois de usar `nxautomation` a conta, gere o par de chaves GPG.

```bash
sudo gpg --generate-key
```

O GPG orientará você pelas etapas para criar o par de chaves. Você precisará fornecer um nome, um endereço de email, uma hora de expiração, uma frase secreta e aguardar uma entropia suficiente no computador para que a chave seja gerada.

Como o diretório GPG foi gerado com sudo, você precisa alterar seu proprietário para `nxautomation`.

Execute o comando a seguir para alterar o proprietário.

```bash
sudo chown -R nxautomation ~/.gnupg
```

#### <a name="make-the-keyring-available-the-hybrid-runbook-worker"></a>Disponibilizar o keyring para o Hybrid Runbook Worker

Depois que o token de toque for criado, você precisará disponibilizá-lo para o Hybrid Runbook Worker. Modifique o arquivo `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf` de configurações para incluir o exemplo a seguir na seção`[worker-optional]`

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

#### <a name="verify-signature-validation-is-on"></a>Verificar se a validação da assinatura está ativada

Se a validação de assinatura tiver sido desabilitada no computador, você precisará ativá-la. Execute o comando a seguir para habilitar a validação de assinatura. Substituindo `<LogAnalyticsworkspaceId>` pela sua ID do espaço de trabalho.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

#### <a name="sign-a-runbook"></a>Assinar um runbook

Após a configuração da validação da assinatura, você pode usar o seguinte comando para assinar um runbook:

```bash
gpg –-clear-sign <runbook name>
```

O runbook assinado terá o nome `<runbook name>.asc`.

O runbook assinado agora pode ser carregado na automação do Azure e pode ser executado como um runbook regular.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os diferentes métodos que podem ser usados para iniciar um runbook, consulte [iniciando um runbook na automação do Azure](automation-starting-a-runbook.md).
* Para entender as diferentes maneiras de trabalhar com runbooks do PowerShell na automação do Azure usando o editor de texto, consulte [editando um runbook na automação do Azure](automation-edit-textual-runbook.md)
* Se seus runbooks não estiverem sendo concluídos com êxito, examine o guia de solução de problemas em [falhas de execução do runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte os [documentos do PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview).
