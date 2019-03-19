---
title: Executar runbooks na função de trabalho do Runbook do Azure automatização híbrida
description: Este artigo fornece informações sobre a execução de runbooks em máquinas no seu local datacenter ou o fornecedor de cloud com a função de trabalho de Runbook híbrida.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fab886de55cc524390093f7e7913c79f7af3fe78
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57858151"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Runbooks em execução numa função de trabalho de Runbook híbrida

Não existe nenhuma diferença na estrutura de runbooks executados na automatização do Azure e de runbooks executados numa função de trabalho de Runbook híbrida. Os Runbooks que utiliza com cada um provavelmente diferir significativamente. Essa diferença é porque os runbooks direcionados para uma função de trabalho de Runbook híbrida, normalmente, gerir os recursos no computador local em si, ou em relação a recursos no ambiente local em que é implementada. Os Runbooks na automatização do Azure, normalmente, gerir os recursos na cloud do Azure.

Quando criar runbooks para executar numa função de trabalho de Runbook híbrida, deve editar e testar runbooks dentro da máquina que aloja a função de trabalho híbrida. A máquina de anfitrião tem todos os módulos do PowerShell e o acesso à rede que tem de gerir e aceder os recursos locais. Depois de um runbook é testado no computador de trabalho híbrida, em seguida, pode carregá-lo para o ambiente de automatização do Azure onde está disponível para ser executada na função de trabalho híbrida. É importante saber que as tarefas que a conta Sistema Local de execução para Windows ou uma conta de utilizador especial `nxautomation` no Linux. Esse comportamento pode introduzir diferenças sutis durante a criação de runbooks para uma função de trabalho de Runbook híbrida. Estas alterações devem ser revistas ao escrever seus runbooks.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Iniciar um runbook no Runbook Worker híbrido

[Iniciar um Runbook na automatização do Azure](automation-starting-a-runbook.md) descreve os diferentes métodos para iniciar um runbook. Runbook Worker híbrido adiciona uma **RunOn** opção em que pode especificar o nome de um grupo de trabalho de Runbook híbrida. Quando é especificado um grupo, o runbook é recuperado e executado por uma das funções de trabalho nesse grupo. Se esta opção não for especificada, em seguida, ele é executado na automatização do Azure como normal.

Quando inicia um runbook no portal do Azure, é apresentada com um **executar no** opção onde pode selecionar **Azure** ou **função de trabalho híbrida**. Se selecionou **função de trabalho híbrida**, em seguida, pode selecionar o grupo numa lista suspensa.

Utilize o **RunOn** parâmetro. Pode utilizar o seguinte comando para iniciar um runbook denominado Test-Runbook num grupo de trabalho de Runbook híbrida com o nome MyHybridGroup com o Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> O **RunOn** parâmetro foi adicionado para o **início AzureAutomationRunbook** cmdlet na versão 0.9.1 do Microsoft Azure PowerShell. Deve [transferir a versão mais recente](https://azure.microsoft.com/downloads/) se tiver um anterior instalado. Só tem de instalar esta versão numa estação de trabalho onde que estiver a iniciar o runbook do PowerShell. Não é necessário instalá-lo no computador de trabalho, a menos que pretende iniciar runbooks a partir desse computador"

## <a name="runbook-permissions"></a>Permissões do Runbook

Runbooks em execução numa função de trabalho de Runbook híbrida não pode utilizar o mesmo método que é normalmente utilizado para autenticação de runbooks nos recursos do Azure, uma vez que estão a aceder aos recursos não no Azure. O runbook pode fornecer sua própria autenticação a recursos locais, ou pode configurar a autenticação com [geridos identidades para recursos do Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager
). Também pode especificar uma conta RunAs para fornecer um contexto de utilizador para todos os runbooks.

### <a name="runbook-authentication"></a>Autenticação de Runbook

Por predefinição, os runbooks são executados no contexto da conta do sistema Local para o Windows e uma conta de utilizador especial `nxautomation` para Linux no computador no local, portanto, devem fornecer sua própria autenticação para recursos que acedem.

Pode usar [credencial](automation-credentials.md) e [certificado](automation-certificates.md) ativos no seu runbook com cmdlets que permitem-lhe especificar as credenciais para se autenticar em recursos diferentes. O exemplo seguinte mostra uma parte de um runbook que reinicia um computador. Ele obtém credenciais de um recurso de credencial e o nome do computador de um recurso de variável e, em seguida, usa esses valores com o cmdlet Restart-Computer.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Também pode utilizar [InlineScript](automation-powershell-workflow.md#inlinescript), que permite a execução de blocos de código noutro computador com as credenciais, que são especificadas pela [parâmetro comum de PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Conta RunAs

Por predefinição a função de trabalho de Runbook híbrida utiliza o sistema Local para o Windows e uma conta de utilizador especial `nxautomation` para Linux executar runbooks. Em vez de precisar fornecer sua própria autenticação a recursos locais de runbooks, pode especificar uma **RunAs** de conta para um grupo de trabalho híbrida. Especifica um [recurso de credencial](automation-credentials.md) que tem acesso a recursos locais e execute todos os runbooks com estas credenciais, quando em execução numa função de trabalho de Runbook híbrida no grupo.

O nome de utilizador para a credencial tem de ser um dos seguintes formatos:

* domínio \ nomedeutilizador
* username@domain
* nome de utilizador (para contas locais no computador no local)

Utilize o procedimento seguinte para especificar uma conta RunAs para um grupo de trabalho híbrida:

1. Criar uma [recurso de credencial](automation-credentials.md) com acesso a recursos locais.
2. Abra a conta de automatização no portal do Azure.
3. Selecione o **grupos de trabalho híbrido** mosaico e, em seguida, selecione o grupo.
4. Selecione **todas as definições** e, em seguida **definições do grupo de trabalho híbrida**.
5. Alteração **Run** partir **predefinido** para **personalizado**.
6. Selecione a credencial e clique em **guardar**.

### <a name="managed-identities-for-azure-resources"></a>Identidades geridas para recursos do Azure

Os Runbook Workers híbridos em execução em máquinas virtuais do Azure pode utilizar identidades geridas para recursos do Azure para se autenticarem nos recursos do Azure. Há muitos benefícios à utilização de identidades geridas para recursos do Azure através de contas Run as.

* Não é necessário exportar o certificado de Run As e, em seguida, importe-o para o Runbook Worker híbrido
* Não é necessário para renovar o certificado utilizado pela conta Run As
* Não é necessário para processar o objeto de ligação Run As no seu código de runbook

Para utilizar uma identidade gerida para recursos do Azure numa função de trabalho de Runbook híbrida, terá de concluir os seguintes passos:

1. Criar uma VM do Azure
2. [Configurar identidades geridas para recursos do Azure na sua VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. [Conceder o acesso à sua VM a um grupo de recursos no Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
4. [Obter um token de acesso com a identidade gerida de atribuído de sistema da VM](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager)
5. [Instalar a função de trabalho de Runbook do Windows híbrida](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) na máquina virtual.

Depois dos passos anteriores estiverem concluídos, pode usar `Connect-AzureRmAccount -Identity` no runbook para se autenticarem nos recursos do Azure. Esta configuração reduz a necessidade de utilizar uma conta Run As e gerir o certificado para a conta Run As.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

### <a name="runas-script"></a>Conta Run As de automatização

Como parte do seu processo de criação automatizado para implementar recursos no Azure, poderá precisar de acesso a sistemas no local para oferecer suporte a uma tarefa ou um conjunto de passos numa sequência de implementação. Para suportar a autenticação no Azure com a conta Run As, terá de instalar o certificado da conta Run As.

O runbook do PowerShell seguinte, **Export-RunAsCertificateToHybridWorker**, exporta o certificado de Run As da sua conta de automatização do Azure e transfere e importa-lo para o arquivo de certificados do computador local num híbrido função de trabalho, que está ligada à mesma conta. Quando esse passo é concluído, ele verifica que a função de trabalho pode autenticar com êxito para o Azure com a conta Run As.

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
> **Add-AzureRmAccount** agora é um alias para **Connect-AzureRMAccount**. Quando pesquisar a sua biblioteca de itens, se não vir **Connect-AzureRMAccount**, pode utilizar **Add-AzureRmAccount**, ou pode atualizar os módulos na conta de automatização.

Guardar a *Export-RunAsCertificateToHybridWorker* runbook para o seu computador com um `.ps1` extensão. Importá-lo para a sua conta de automatização e editar o runbook, alterando o valor da variável `$Password` com sua própria palavra-passe. Publicar e, em seguida, executar o runbook. Destino o grupo de função de trabalho híbrida que será executado e autenticar runbooks com a conta Run As. O fluxo de trabalho relatórios a tentativa de importar o certificado para o arquivo do computador local e segue com várias linhas. Este comportamento depende de quantas contas de automatização que define na sua subscrição e se a autenticação é efetuada com êxito.

## <a name="job-behavior"></a>Comportamento de tarefa

Tarefas são processadas um pouco diferente sobre os Runbook Workers híbridos são quando executados em áreas de segurança do Azure. Uma das principais diferenças é que não existe nenhum limite na duração de tarefa nos Runbook Workers híbridos. Runbooks foi executado no Azure áreas de segurança estão limitadas a 3 horas devido [justa](automation-runbook-execution.md#fair-share). Para um runbook de execução longa, pretende certificar-se de que é resiliente para reiniciar o possível. Por exemplo, se a máquina que aloja a híbrida reinícios de função de trabalho. Se reiniciar a máquina de anfitrião de trabalho híbrida, em seguida, qualquer tarefa de runbook em execução reinicia desde o início ou a partir do último ponto de verificação para runbooks de fluxo de trabalho do PowerShell. Depois de um runbook tarefa for reiniciada mais de 3 vezes e, em seguida, está suspenso.

## <a name="run-only-signed-runbooks"></a>Execute apenas assinado Runbooks

Os Runbook Workers híbridos pode ser configurados para ser executado apenas assinado runbooks com a configuração. A seguinte secção descreve como configurar as funções de trabalho de Runbook híbridas para executar assinado [Runbook Worker híbrido Windows](#windows-hybrid-runbook-worker) e [Runbook Worker híbrido Linux](#linux-hybrid-runbook-worker)

> [!NOTE]
> Assim que tiver configurado uma função de trabalho de Runbook híbrida para executar apenas os runbooks assinados, os runbooks que têm **não** foi assinado irão falhar executar na função de trabalho.

### <a name="windows-hybrid-runbook-worker"></a>Runbook Worker híbrido Windows

#### <a name="create-signing-certificate"></a>Criar certificado de assinatura

O exemplo seguinte cria um certificado autoassinado que pode ser utilizado na assinatura de runbooks. O exemplo cria o certificado e exporta-lo. O certificado é importado para as funções de trabalho de Runbook híbridas mais tarde. O thumbprint é retornado, que este valor é utilizado mais tarde para referenciar o certificado.

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

#### <a name="configure-the-hybrid-runbook-workers"></a>Configurar funções de trabalho de Runbook híbridas

Copie o certificado que criou para cada função de trabalho de Runbook híbrida de um grupo. Execute o seguinte script para importar o certificado e configurar a função de trabalho híbrida para utilizar a validação da assinatura em runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

#### <a name="sign-your-runbooks-using-the-certificate"></a>Inscrever-se os Runbooks com o certificado

O Runbook híbrido funções de trabalho configuradas para utilizar apenas para iniciar sessão runbooks, tem de iniciar runbooks que estão a ser utilizada na função de trabalho de Runbook híbrida. Utilize o seguinte exemplo do PowerShell para assinar seus runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Quando o runbook foi assinado, tem de ser importado para a sua conta de automatização e publicada com o bloco de assinaturas. Para saber como importar runbooks, consulte [importar um runbook a partir de um ficheiro para automatização do Azure](manage-runbooks.md#import-a-runbook).

### <a name="linux-hybrid-runbook-worker"></a>Runbook Worker híbrido Linux

Para iniciar runbooks numa função de trabalho do Runbook de híbrida do Linux, tem de ter a função de trabalho de Runbook híbrida do [GPG](https://gnupg.org/index.html) executável presente na máquina.

#### <a name="create-a-gpg-keyring-and-keypair"></a>Criar um porta-chaves GPG e um par de chaves

Para criar a porta-chaves e um par de chaves terá de utilizar a conta do Runbook Worker híbrido `nxautomation`.

Uso `sudo` para iniciar sessão como o `nxautomation` conta.

```bash
sudo su – nxautomation
```

Uma vez utilizando o `nxautomation` de contas, gerar o par de chaves de gpg.

```bash
sudo gpg --generate-key
```

GPG irá guiá-lo pelos passos para criar o par de chaves. Terá de fornecer um nome, um endereço de e-mail, hora de expiração, frase de acesso e aguarde pela suficiente entropia no computador para a chave a ser gerado.

Uma vez que o diretório GPG foi gerado com sudo, tem de alterar o proprietário para `nxautomation`. 

Execute o seguinte comando para alterar o proprietário.

```bash
sudo chown -R nxautomation ~/.gnupg
```

#### <a name="make-the-keyring-available-the-hybrid-runbook-worker"></a>Disponibilizar a porta-chaves a função de trabalho de Runbook híbrida

Assim que a porta-chaves for criado, precisará para disponibilizá-lo para a função de trabalho de Runbook híbrida. Modificar o arquivo de configurações `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf` para incluir o exemplo a seguir na seção `[worker-optional]`

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

#### <a name="verify-signature-validation-is-on"></a>Verifique se a validação de assinatura está ativada

Se a validação da assinatura foi desabilitada no computador, terá de ativá-la. Execute o seguinte comando para ativar a validação de assinatura. Substituir `<LogAnalyticsworkspaceId>` com o ID de área de trabalho.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

#### <a name="sign-a-runbook"></a>Iniciar um runbook

Assim que a validação da assinatura estiver configurada, pode utilizar o seguinte comando para iniciar um runbook:

```bash
gpg –-clear-sign <runbook name>
```

O runbook assinado terá o nome `<runbook name>.asc`.

O runbook assinado agora pode ser carregado para a automatização do Azure e pode ser executado como um runbook regular.

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre os diferentes métodos que podem ser utilizados para iniciar um runbook, consulte [a partir de um Runbook na automatização do Azure](automation-starting-a-runbook.md).
* Para compreender as diferentes formas de trabalhar com runbooks do PowerShell na automatização do Azure com o editor de texto, veja [editar um Runbook na automatização do Azure](automation-edit-textual-runbook.md)
* Se os runbooks não são concluir com êxito, reveja o guia de resolução de problemas na [falhas de execução do runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).