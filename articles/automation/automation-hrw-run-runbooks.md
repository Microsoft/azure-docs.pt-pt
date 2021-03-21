---
title: Executar os runbooks da Azure Automation em um trabalhador de runbook híbrido
description: Este artigo descreve como executar livros em máquinas no seu datacenter local ou outro fornecedor de nuvem com o Trabalhador de Runbook Híbrido.
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.openlocfilehash: 6d1f504458aed440464015a34479d75992fe5c45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149380"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Executar runbooks numa Função de Trabalho de Runbook Híbrida

Os runbooks que funcionam num [Trabalhador De Runbook Híbrido](automation-hybrid-runbook-worker.md) normalmente gerem recursos no computador local ou contra recursos no ambiente local onde o trabalhador é implantado. Os runbooks na Azure Automation normalmente gerem recursos na nuvem Azure. Apesar de serem usados de forma diferente, os livros que funcionam na Azure Automation e os runbooks que funcionam num Trabalhador De Runbook Híbrido são idênticos na estrutura.

Quando autores de um livro de bordo para executar um Trabalhador de Runbook Híbrido, deve editar e testar o livro de execução na máquina que acolhe o trabalhador. A máquina hospedeira tem todos os módulos PowerShell e acesso à rede necessários para gerir os recursos locais. Uma vez testado o livro de execução na máquina hybrid Runbook Worker, pode então carregá-lo para o ambiente Azure Automation, onde pode ser executado no trabalhador.

## <a name="plan-runbook-job-behavior"></a>Plan runbook comportamento de trabalho

A Azure Automation lida com empregos em Trabalhadores De Runbook Híbridos diferentes dos empregos geridos em caixas de areia Azure. Se tiver um livro de corridas de longa duração, certifique-se de que é resistente a um possível reinício. Para mais detalhes sobre o comportamento do trabalho, consulte os [trabalhos do Trabalhador de Runbook Híbrido.](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs)

Jobs for Hybrid Runbook Workers são executados sob a conta **do Sistema** local no Windows, ou a conta **de nxautomation** no Linux. Para o Linux, verifique se a conta **de nxautomation** tem acesso ao local onde os módulos do runbook estão armazenados. Quando utilizar o cmdlet [do Módulo de Instalação, certifique-se](/powershell/module/powershellget/install-module) de especificar AllUsers para o `Scope` parâmetro para garantir que a conta de **nxautomation** tem acesso. Para obter mais informações sobre powerShell sobre Linux, consulte [Questões Conhecidas para PowerShell em plataformas não-Windows](/powershell/scripting/whats-new/what-s-new-in-powershell-70).

## <a name="configure-runbook-permissions"></a>Configure permissões de livro de correr

Defina permissões para que o seu runbook seja executado no Trabalhador de Runbook Híbrido das seguintes formas:

* Que o livro de bordo forneça a sua própria autenticação aos recursos locais.
* Configure a autenticação utilizando [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
* Especifique uma conta Run As para fornecer um contexto de utilizador para todos os livros.

### <a name="use-runbook-authentication-to-local-resources"></a>Utilize a autenticação de runbook para recursos locais

Se preparar um livro de aplicação que forneça a sua própria autenticação aos recursos, utilize ativos [credenciais](./shared-resources/credentials.md) e [certificados](./shared-resources/certificates.md) no seu runbook. Existem vários cmdlets que permitem especificar credenciais para que o livro possa autenticar diferentes recursos. O exemplo a seguir mostra uma parte de um livro de bordo que reinicia um computador. Obtém credenciais de um ativo credencial e o nome do computador a partir de um ativo variável e, em seguida, utiliza estes valores com o `Restart-Computer` cmdlet.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Também pode utilizar uma atividade [InlineScript.](automation-powershell-workflow.md#use-inlinescript) `InlineScript` permite executar blocos de código em outro computador com credenciais.

### <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Utilize a autenticação de runbook com identidades geridas

Os trabalhadores híbridos runbook em máquinas virtuais Azure podem usar identidades geridas para autenticar os recursos da Azure. A utilização de identidades geridas para recursos Azure em vez de Run As contas proporciona benefícios porque não precisa de:

* Exporte o Run As certificate e, em seguida, importe-o para o Trabalhador De Runbook Híbrido.
* Renovar o certificado utilizado pela conta Run As.
* Manuseie o run Como objeto de ligação no seu código de execução.

Siga os próximos passos para utilizar uma identidade gerida para os recursos da Azure num Trabalhador De Runbook Híbrido:

1. Criar um Azure VM.
1. Configure identidades geridas para os recursos da Azure no VM. Consulte [as identidades geridas para os recursos da Azure num VM utilizando o portal Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
1. Dê ao VM acesso a um grupo de recursos no Gestor de Recursos. Consulte a [Utilização de uma identidade gerida pelo sistema Windows VM para aceder ao Gestor de Recursos](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
1. Instale o Trabalhador de Runbook Híbrido no VM. Consulte [implementar um trabalhador de runbook híbrido do Windows](automation-windows-hrw-install.md) ou implementar um trabalhador de [runbook híbrido Linux](automation-linux-hrw-install.md).
1. Atualize o livro de execução para utilizar o [cmdlet Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) com o `Identity` parâmetro para autenticar os recursos do Azure. Esta configuração reduz a necessidade de utilizar uma conta Run As e executar a gestão de conta associada.

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > `Connect-AzAccount -Identity` funciona para um Trabalhador de Runbook Híbrido utilizando uma identidade atribuída ao sistema e uma única identidade atribuída ao utilizador. Se utilizar várias identidades atribuídas ao utilizador no Trabalhador do Runbook Híbrido, o seu livro de execução deve especificar o `AccountId` parâmetro `Connect-AzAccount` para selecionar uma identidade específica atribuída ao utilizador.

### <a name="use-runbook-authentication-with-run-as-account"></a>Utilize a autenticação do runbook com a conta Run As

Em vez de ter o seu runbook fornecer a sua própria autenticação aos recursos locais, pode especificar uma conta Run As para um grupo híbrido de runbook worker. Para especificar uma conta Run As, tem de definir um [ativo credencial](./shared-resources/credentials.md) que tenha acesso aos recursos locais. Estes recursos incluem lojas de certificados e todos os runbooks executados sob estas credenciais em um Trabalhador De Runbook Híbrido no grupo.

- O nome de utilizador da credencial deve estar num dos seguintes formatos:

   * domínio\username
   * username@domain
   * nome de utilizador (para contas locais para o computador no local)

- Para utilizar o runbook PowerShell **Export-RunAsCertificateToHybridWorker,** é necessário instalar os módulos Az para Automatização Azure na máquina local.

#### <a name="use-a-credential-asset-to-specify-a-run-as-account"></a>Utilize um ativo credencial para especificar uma conta Run As

Utilize o seguinte procedimento para especificar uma conta Run As para um grupo híbrido de trabalhador runbook:

1. Criar um [ativo credencial](./shared-resources/credentials.md) com acesso a recursos locais.
1. Abra a conta Automation no portal Azure.
1. Selecione **Grupos operários híbridos** e, em seguida, selecione o grupo específico.
1. Selecione **Todas as definições**, seguidas de **configurações do grupo de trabalhadores híbridos**.
1. Alterar o valor de **Run As** from **Predefinição** para **Personalizado**.
1. Selecione a credencial e clique em **Guardar**.

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>Instalar Executar Como certificado de conta

Como parte do seu processo automatizado de construção para implantação de recursos em Azure, poderá necessitar de acesso a sistemas no local para suportar uma tarefa ou conjunto de passos na sua sequência de implementação. Para fornecer autenticação contra a Azure utilizando a conta Run As, tem de instalar o certificado de conta Run As.

>[!NOTE]
>Este manual powerShell não funciona atualmente em máquinas Linux. Funciona apenas em máquinas Windows.
>

O seguinte runbook PowerShell, chamado **Export-RunAsCertificateToHybridWorker,** exporta o certificado Run As da sua conta Azure Automation. O runbook descarrega e importa o certificado para a loja de certificados de máquina local num Trabalhador De Runbook Híbrido que está ligado à mesma conta. Uma vez concluído esse passo, o livro de recortes verifica se o trabalhador pode autenticar com sucesso a Azure utilizando a conta Run As.

>[!NOTE]
>Este livro de aplicação PowerShell não foi concebido ou destina-se a ser executado fora da sua conta Demôm automação como um script na máquina-alvo.
>

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
>Para os livros powerShell, `Add-AzAccount` e `Add-AzureRMAccount` são pseudónimos para `Connect-AzAccount` . Ao pesquisar os itens da sua biblioteca, se não `Connect-AzAccount` vir, pode utilizar `Add-AzAccount` ou atualizar os seus módulos na sua conta Demôm automação.

Para terminar a preparação da conta Run As:

1. Guarde o livro **de bordo DoRas-RunAsCertificateToHybridWorker** para o seu computador com uma extensão **.ps1.**
1. Importe-o na sua conta de Automação.
1. Edite o runbook, alterando o valor da `Password` variável para a sua própria senha.
1. Publique o livro de corridas.
1. Executa o runbook, direcionando o grupo híbrido Runbook Worker que executa e autentica os runbooks usando a conta Run As. 
1. Examine o fluxo de trabalho para verificar se relata a tentativa de importação do certificado para a loja de máquinas local, seguida de várias linhas. Este comportamento depende de quantas contas de Automação define na sua subscrição e do grau de sucesso da autenticação.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Trabalhe com livros assinados em um trabalhador de runbook híbrido do Windows

Pode configurar um Trabalhador de Runbook Híbrido do Windows para executar apenas livros de execução assinados.

> [!IMPORTANT]
> Depois de configurar um Trabalhador de Runbook Híbrido para executar apenas livros de execução assinados, os livros de execução não assinados não executam no trabalhador.

### <a name="create-signing-certificate"></a>Criar certificado de assinatura

O exemplo a seguir cria um certificado auto-assinado que pode ser usado para assinar livros. Este código cria o certificado e exporta-o para que o Trabalhador Do Runbook Híbrido possa importá-lo mais tarde. A impressão digital também é devolvida para posterior utilização na referência ao certificado.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Certificado de importação e trabalhadores de configuração para validação de assinaturas

Copie o certificado que criou para cada Trabalhador De Runbook Híbrido de um grupo. Executar o seguinte roteiro para importar o certificado e configurar os trabalhadores para usar a validação de assinatura em runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Assine os seus runbooks usando o certificado

Com os Trabalhadores de Runbook Híbridos configurados para usar apenas livros de execução assinados, você deve assinar livros de execução que devem ser usados no Trabalhador de Runbook Híbrido. Utilize o seguinte código PowerShell para assinar estes livros.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Quando um livro de bordo tiver sido assinado, deve importá-lo na sua conta Demôm automação e publicá-lo com o bloco de assinaturas. Para aprender a importar livros, consulte [Importar um livro de recortes.](manage-runbooks.md#import-a-runbook)

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Trabalhe com livros assinados num Trabalhador de Runbook Híbrido Linux

Para poder trabalhar com livros assinados, um Trabalhador de Runbook Híbrido Linux deve ter o [GPG](https://gnupg.org/index.html) executável na máquina local.

> [!IMPORTANT]
> Depois de configurar um Trabalhador de Runbook Híbrido para executar apenas livros de execução assinados, os livros de execução não assinados não executam no trabalhador.

Executará os seguintes passos para completar esta configuração:

* Crie um porta-chaves GPG e um keypair
* Disponibilize o porta-chaves para o Trabalhador de Runbook Híbrido
* Verifique se a validação da assinatura está em
* Assine um livro de corridas

### <a name="create-a-gpg-keyring-and-keypair"></a>Crie um porta-chaves GPG e um keypair

Para criar o porta-chaves GPG e o keypair, utilize a conta de [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux)Hybrid Runbook Worker .

1. Utilize o pedido de sudo para iniciar scontabilidade de **nxautomation.**

    ```bash
    sudo su – nxautomation
    ```

1. Uma vez que esteja a utilizar **a nxautomação,** gere o keypair GPG. A GPG guia-o através dos degraus. Deve fornecer nome, endereço de e-mail, tempo de validade e frase de passe. Depois espera-se até que haja entropia suficiente na máquina para que a chave seja gerada.

    ```bash
    sudo gpg --generate-key
    ```

1. Como o diretório GPG foi gerado com sudo, você deve mudar o seu proprietário para **nxautomation** usando o seguinte comando.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Disponibilize o porta-chaves para o Trabalhador de Runbook Híbrido

Uma vez criado o porta-chaves, disponibilize-o para o Trabalhador de Runbook Híbrido. Modificar as definições arquivar **casa/nxautomation/state/worker.conf** para incluir o seguinte código de exemplo na secção de `[worker-optional]` ficheiros .

```bash
gpg_public_keyring_path = /home/nxautomation/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Verifique se a validação da assinatura está em

Se a validação da assinatura tiver sido desativada na máquina, deve ligá-la executando o seguinte comando sudo. Substitua-o pelo `<LogAnalyticsworkspaceId>` seu ID do espaço de trabalho.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Assine um livro de corridas

Depois de configurar a validação da assinatura, utilize o seguinte comando GPG para assinar o livro de execução.

```bash
gpg –-clear-sign <runbook name>
```

O livro assinado chama-se **<runbook name> .asc**.

Pode agora fazer o upload do livro de formulários assinado para a Azure Automation e executá-lo como um runbook regular.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Inicie um livro de corridas em um trabalhador de runbook híbrido

[Iniciar um livro de recortes na Azure Automation](start-runbooks.md) descreve diferentes métodos para iniciar um livro de corridas. Iniciar um runbook num Trabalhador de Runbook Híbrido utiliza uma opção **Run on** que permite especificar o nome de um grupo híbrido de runbook worker. Quando um grupo é especificado, um dos trabalhadores desse grupo recupera e executa o livro de corridas. Se o seu runbook não especificar esta opção, a Azure Automation executa o livro de aplicação como de costume.

Quando inicia um livro de bordo no portal Azure, é-lhe apresentado **opção Run on** para o qual pode selecionar **Azure** ou **Hybrid Worker**. Se selecionar **o Trabalhador Híbrido,** pode escolher o grupo híbrido Runbook Worker a partir de um dropdown.

Ao iniciar um livro de execução utilizando o PowerShell, utilize o `RunOn` parâmetro com o [cmdlet Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook) O exemplo a seguir utiliza o Windows PowerShell para iniciar um livro de testes chamado **Test-Runbook** num grupo híbrido de trabalho de runbook chamado MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="logging"></a>Registo

Para ajudar a resolver problemas com os seus runbooks em execução num trabalhador híbrido, os registos são armazenados localmente no seguinte local:

* No Windows `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` para o registo detalhado do processo de execução do trabalho. Os eventos de estado de trabalho de alto nível são escritos para o registo de **eventos de aplicações e serviços\Microsoft-Automation\Operations** event log.

* No Linux, os registos híbridos do utilizador podem ser encontrados em `/home/nxautomation/run/worker.log` , e os registos de trabalhadores do sistema podem ser encontrados em `/var/opt/microsoft/omsagent/run/automationworker/worker.log` .

## <a name="next-steps"></a>Passos seguintes

* Se os seus livros não estiverem a concluir com sucesso, reveja o guia de resolução de problemas para [falhas na execução do livro](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)de corridas .
* Para obter mais informações sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte [PowerShell Docs](/powershell/scripting/overview).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
