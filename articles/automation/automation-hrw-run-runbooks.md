---
title: Run Azure Automation run book em um trabalhador de runbook híbrido
description: Este artigo diz como executar livros de corridas em máquinas no seu datacenter local ou fornecedor de nuvem com o Hybrid Runbook Worker.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: 23b7808f1262ab0829821817e03164b2ba98be4c
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713799"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Executar runbooks numa Função de Trabalho de Runbook Híbrida

Os livros de execução que funcionam num [Trabalhador De Runbook Híbrido](automation-hybrid-runbook-worker.md) normalmente gerem recursos no computador local ou contra recursos no ambiente local onde o trabalhador é implantado. Os livros de corridas em Automação Azure normalmente gerem recursos na nuvem Azure. Apesar de serem usados de forma diferente, os livros que funcionam na Azure Automation e os livros que funcionam num Trabalhador de Runbook Híbrido são idênticos na estrutura.

Quando você é autor de um livro de corridas para executar em um Trabalhador de Runbook Híbrido, você deve editar e testar o livro de execução na máquina que acolhe o trabalhador. A máquina hospedeira tem todos os módulos PowerShell e acesso à rede necessários para gerir os recursos locais. Uma vez testado o livro de execução na máquina Hybrid Runbook Worker, pode então carregá-lo para o ambiente De automação Azure, onde pode ser executado sobre o trabalhador. 

## <a name="plan-runbook-job-behavior"></a>Planeie comportamento de trabalho de livro de corridas

A Azure Automation trata de trabalhos em Trabalhadores de Livro Híbrido de forma um pouco diferente dos empregos executados em caixas de areia Azure. Se tiver um livro de corridas de longa duração, certifique-se de que é resistente a um possível recomeço. Para mais detalhes sobre o comportamento do trabalho, consulte os empregos do Trabalhador do [Livro de Corridas Híbridos.](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs)

Lembre-se que os empregos para os Trabalhadores híbridos do Livro de Corridas são executados sob a conta **do Sistema** local no Windows ou na conta **de nxautomation** no Linux. Para o Linux, certifique-se de que a conta **de nxautomation** tem acesso ao local onde os módulos de caderneta estão armazenados. Quando utilizar o cmdlet de [Módulo de Instalação, certifique-se](/powershell/module/powershellget/install-module) de especificar todos os Utilizadores para o `Scope` parâmetro para garantir que a conta de **automação** tem acesso. Para obter mais informações sobre o PowerShell no Linux, consulte [Questões Conhecidas para PowerShell em plataformas não Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="set-up-runbook-permissions"></a>Configurar permissões de livro de corridas

Defina permissões para que o seu livro de execução seja executado no Trabalhador do Livro de Corridas Híbrido sintetiza ndo as seguintes formas:

* O livro de execução fornece a sua própria autenticação aos recursos locais.
* Configure a autenticação utilizando [identidades geridas para os recursos Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). 
* Especifique uma conta Run As para fornecer um contexto de utilizador para todos os livros de execução.

## <a name="use-runbook-authentication-to-local-resources"></a>Utilize a autenticação do livro de corridas para os recursos locais

Se preparar um livro de execução que forneça a sua própria autenticação aos recursos, utilize ativos [credenciais](automation-credentials.md) e [certificados](automation-certificates.md) no seu livro de execução. Existem vários cmdlets que lhe permitem especificar credenciais para que o livro de execução possa autenticar a diferentes recursos. O exemplo que se segue mostra uma parte de um livro que reinicia um computador. Recupera credenciais de um ativo credencial e o nome do computador a partir de um ativo variável e depois utiliza estes valores com o `Restart-Computer` cmdlet.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Também pode utilizar uma atividade [InlineScript.](automation-powershell-workflow.md#use-inlinescript) `InlineScript`permite-lhe executar blocos de código noutro computador com credenciais.

## <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Utilize a autenticação do livro de corridas com identidades geridas

Os trabalhadores híbridos do livro de corridas em máquinas virtuais Azure podem usar identidades geridas para autenticar recursos Azure. Utilizando identidades geridas para recursos Azure em vez de Run As contas proporcionam benefícios porque não precisa:

* Exportar o certificado Run As e, em seguida, importá-lo para o Trabalhador do Livro De Execução Híbrido.
* Renovar o certificado utilizado pela conta Run As.
* Manuseie o executar Como objeto de ligação no seu código de rumir.

Siga os próximos passos para utilizar uma identidade gerida para os recursos Azure num Trabalhador de Livro Híbrido:

1. Crie um VM Azure.
2. Configure identidades geridas para os recursos Azure no VM. Consulte [as identidades geridas pela Configure para os recursos Azure num VM utilizando o portal Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Dê ao VM acesso a um grupo de recursos em Gestor de Recursos. Consulte [a Utilização](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)de uma identidade gerida atribuída ao sistema Windows VM para aceder ao Gestor de Recursos .
4. Instale o Trabalhador do Livro Híbrido no VM. Consulte a implementação de um trabalhador do [livro de corridas híbrido windows](automation-windows-hrw-install.md) ou implante um trabalhador de [resta híbrido Linux](automation-linux-hrw-install.md).
5. Atualize o livro de execução para utilizar o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) com o `Identity` parâmetro para autenticar os recursos do Azure. Esta configuração reduz a necessidade de utilizar uma conta Run As e realizar a gestão da conta associada.

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > `Connect-AzAccount -Identity`Trabalha para um Trabalhador de RaquinaDo Híbrido utilizando uma identidade atribuída ao sistema e uma única identidade atribuída ao utilizador. Se utilizar várias identidades atribuídas ao utilizador no Hybrid Runbook Worker, o seu livro de execução deve especificar o parâmetro para selecionar uma identidade específica atribuída ao `AccountId` `Connect-AzAccount` utilizador.

## <a name="use-runbook-authentication-with-run-as-account"></a>Utilize a autenticação do livro de corridas com conta Run As

Em vez de ter o seu livro de execução fornecer a sua própria autenticação aos recursos locais, pode especificar uma conta Run As para um grupo híbrido runbook worker. Para isso, deve definir um [ativo credencial](automation-credentials.md) que tenha acesso aos recursos locais. Estes recursos incluem lojas de certificados e todos os livros de execução executados sob estas credenciais em um Trabalhador de Runbook Híbrido no grupo.

O nome do utilizador da credencial deve estar num dos seguintes formatos:

* nome de utilizador de domínio\
* username@domain
* nome de utilizador (para contas locais para o computador no local)

Utilize o seguinte procedimento para especificar uma Corrida Como conta para um grupo híbrido de trabalhadores de runbook:

1. Criar um [ativo credencial](automation-credentials.md) com acesso aos recursos locais.
2. Abra a conta Automation no portal Azure.
3. Selecione **Grupos de Trabalhadores Híbridos**e, em seguida, selecione o grupo específico.
4. Selecione **Todas as definições**, seguidas por configurações do **grupo de trabalhadores híbridos**.
5. Alterar o valor da **execução de** **predefinição** para **personalizado**.
6. Selecione a credencial e clique **em Guardar**.

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>Instalar Executar Como certificado de conta

Como parte do seu processo automatizado de construção para a implantação de recursos no Azure, poderá necessitar de acesso a sistemas no local para suportar uma tarefa ou conjunto de passos na sua sequência de implantação. Para fornecer a autenticação contra o Azure utilizando a conta Run As, tem de instalar o certificado de conta Run As.

O seguinte livro de execução powerShell, chamado **Export-RunAsCertificateToHybridWorker,** exporta o Certificado De Execução Como certificado da sua conta Azure Automation. O livro de execução descarrega e importa o certificado para a loja de certificados de máquina local num Trabalhador híbrido do Runbook que está ligado à mesma conta. Uma vez concluído esse passo, o livro de execução verifica que o trabalhador pode autenticar com sucesso o Azure usando a conta Run As.

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
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

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

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>Para os livros de execução da PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` são pseudónimos para `Connect-AzAccount` . Ao pesquisar os itens da sua biblioteca, se não vir `Connect-AzAccount` , pode `Add-AzAccount` usar, ou pode atualizar os seus módulos na sua conta Deautomação.

Para terminar de preparar a Conta Run As:

1. Guarde o livro de execução **Export-RunAsCertificateToHybridWorker** para o seu computador com uma extensão **.ps1.**
2. Importe-o para a sua conta de Automação.
3. Editar o livro de execução, alterando o valor da `Password` variável para a sua própria senha. 
4. Publique o livro de corridas.
5. Executar o livro de corridas, visando o grupo Hybrid Runbook Worker que executa e autentica os livros de execução usando a conta Run As. 
6. Examine o fluxo de trabalho para ver se relata a tentativa de importar o certificado para a loja de máquinas local, seguida de várias linhas. Este comportamento depende de quantas contas de Automação define na sua subscrição e do grau de sucesso da autenticação.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Trabalhe com livros de execução assinados num Trabalhador de Runbook Híbrido windows

Pode configurar um Trabalhador do Livro Híbrido do Windows para executar apenas livros de execução assinados. 

> [!IMPORTANT]
> Depois de configurar um Trabalhador híbrido do runbook para executar apenas livros de execução assinados, os livros de execução não assinados não executam o trabalhador.

### <a name="create-signing-certificate"></a>Criar certificado de assinatura

O exemplo seguinte cria um certificado auto-assinado que pode ser usado para a assinatura de livros de execução. Este código cria o certificado e exporta-o para que o Trabalhador do Livro Híbrido possa importá-lo mais tarde. A impressão digital também é devolvida para posterior utilização na referência ao certificado.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Certificado de importação e trabalhadores configurados para validação de assinaturas

Copie o certificado que criou a cada Trabalhador Híbrido de Runbook em grupo. Executar o seguinte guião para importar o certificado e configurar os trabalhadores para usar a validação de assinatura seletivas em livros de execução.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Assine os seus livros de execução usando o certificado

Com os Trabalhadores híbridos do Runbook configurados para utilizar apenas livros de execução assinados, deve assinar livros de execução que devem ser utilizados no Trabalhador do Livro Híbrido. Utilize o seguinte código PowerShell para assinar estes livros de execução.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Quando um livro de execução tiver sido assinado, deve importá-lo para a sua conta Deautomação e publicá-lo com o bloco de assinaturas. Para aprender a importar livros de execução, consulte importar um livro de [corridas.](manage-runbooks.md#import-a-runbook)

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Trabalhe com livros de execução assinados num Trabalhador de Runbook Híbrido Linux

Para poder trabalhar com livros de corridas assinados, um Trabalhador do Livro híbrido Linux deve ter o [GPG](https://gnupg.org/index.html) executável na máquina local.

> [!IMPORTANT]
> Depois de configurar um Trabalhador híbrido do runbook para executar apenas livros de execução assinados, os livros de execução não assinados não executam o trabalhador.

### <a name="create-a-gpg-keyring-and-keypair"></a>Criar um teclado GPG e um teclado

Para criar o teclado GPG e o teclado, utilize a [conta de nxautomação hybrid](automation-runbook-execution.md#log-analytics-agent-for-linux)Runbook Worker .

1. Utilize a aplicação sudo para iniciar sessão como conta **de nxautomation.**

    ```bash
    sudo su – nxautomation
    ```

2. Uma vez que esteja a utilizar **a nxautomation,** gere o par de chaves GPG. A GPG guia-o através dos degraus. Deve fornecer nome, endereço de e-mail, tempo de validade e palavra-passe. Depois espera-se até que haja entropia suficiente na máquina para que a chave seja gerada.

    ```bash
    sudo gpg --generate-key
    ```

3. Como o diretório GPG foi gerado com o sudo, você deve mudar o seu proprietário para **nxautomation** usando o seguinte comando.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Disponibilize o porta-chaves ao Trabalhador híbrido do livro de corridas

Uma vez criado o porta-chaves, disponibilize-o ao Trabalhador híbrido do livro de corridas. Modificar o ficheiro de definições **/var/opt/microsoft/omsagent/state/automaworker/diy/worker.conf** para incluir o seguinte código de exemplo na secção de ficheiros `[worker-optional]` .

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Verifique se a validação da assinatura está em

Se a validação da assinatura tiver sido desativada na máquina, deve ligá-la executando o seguinte comando sudo. `<LogAnalyticsworkspaceId>`Substitua-a com o seu ID do espaço de trabalho.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Assine um livro de corridas

Depois de configurar a validação de assinaturas, utilize o seguinte comando GPG para assinar o livro de execução.

```bash
gpg –-clear-sign <runbook name>
```

O livro de execução assinado chama-se ** <runbook name> .asc**.

Agora pode enviar o livro de execução assinado para a Azure Automation e executá-lo como um livro regular.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Inicie um livro de corridas em um trabalhador híbrido do livro de corridas

[Inicie um livro de execução na Azure Automation](start-runbooks.md) descreve diferentes métodos para iniciar um livro de execução. O arranque de um livro de corridas num Trabalhador de Runbook Híbrido usa uma **opção Run** que lhe permite especificar o nome de um grupo híbrido runbook worker. Quando um grupo é especificado, um dos trabalhadores desse grupo recupera e dirige o livro de corridas. Se o seu livro de execução não especificar esta opção, a Azure Automation executa o livro de execução como de costume.

Quando inicia um livro de corridas no portal Azure, é-lhe apresentado o **Run em** opção para o qual pode selecionar O Trabalhador **Azure** ou **Híbrido**. Se selecionar **O Trabalhador Híbrido,** pode escolher o grupo Hybrid Runbook Worker a partir de uma queda.

Ao iniciar um livro de execução utilizando o PowerShell, utilize o `RunOn` parâmetro com o cmdlet [Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) O exemplo seguinte utiliza o Windows PowerShell para iniciar um livro de corridas chamado **Test-Runbook** num grupo híbrido de runbook worker chamado MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="next-steps"></a>Próximos passos

* Se os seus livros não estiverem a completar com sucesso, reveja o guia de resolução de problemas para falhas na [execução de livros](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)de corridas .
* Para obter mais informações sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte os [Docs PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
