---
title: Gerir certificados na Automação Azure
description: A Azure Automation armazena certificados de forma segura, para que os livros de execução ou as configurações dSC possam aceder-lhes para autenticar contra o Azure e recursos de terceiros. Este artigo explica os detalhes dos certificados e como trabalhar com eles tanto na autoria textual como gráfica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2793679fb4588d00ea4e37340b19183398cb9d90
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864322"
---
# <a name="manage-certificates-in-azure-automation"></a>Gerir certificados na Automação Azure

A Azure Automation armazena certificados de acesso seguro para acesso através de livros de execução e configurações DSC, utilizando o [cmdlet Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) para os recursos do Gestor de Recursos Azure. O armazenamento seguro de certificados permite-lhe criar livros de execução e configurações DSC que utilizam certificados para autenticação, ou adicioná-los a recursos Azure ou de terceiros.

>[!NOTE]
>Os ativos seguros na Automatização Azure incluem credenciais, certificados, ligações e variáveis encriptadas. Estes ativos são encriptados e armazenados na Automação utilizando uma chave única que é gerada para cada conta de Automação. A automação armazena a chave no serviço Key Vault gerido pelo sistema. Antes de armazenar um ativo seguro, a Automation carrega a chave a partir do Key Vault e, em seguida, usa-a para encriptar o ativo. 

>[!NOTE]
>Este artigo mostra-lhe como utilizar o módulo Azure PowerShell Az. Ainda pode utilizar o módulo AzureRM. Para saber mais sobre o módulo Az e compatibilidade AzureRM, consulte [A introdução do novo módulo Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md).

## <a name="powershell-cmdlets-to-access-certificates"></a>PowerShell cmdlets para aceder a certificados

Os cmdlets na tabela seguinte criam e gerem certificados de Automação com powerShell. Eles enviam como parte dos [módulos Az.](modules.md#az-modules)

|Cmdlet |Descrição|
| --- | ---|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Recupera informações sobre um certificado para usar numa configuração de livro de execução ou DSC. Só é possível recuperar o certificado `Get-AutomationCertificate` utilizando o cmdlet interno.|
|[Novo Certificado de Automação](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Cria um novo certificado em Automação.|
|[Remover-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Remove um certificado da Automação.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Define as propriedades para um certificado existente, incluindo o upload do ficheiro de certificado e a definição da palavra-passe para um ficheiro **.pfx.**|

O cmdlet [Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate) também pode ser utilizado para fazer o upload de um certificado de serviço para o serviço de nuvem especificado.

## <a name="internal-cmdlets-to-access-certificates"></a>Cmdlets internos para aceder a certificados

O cmdlet interno na tabela seguinte é utilizado para aceder a certificados nos seus livros de execução. Este cmdlet vem com `Orchestrator.AssetManagement.Cmdlets`o módulo global. Para mais informações, consulte [cmdlets internos](modules.md#internal-cmdlets).

| Cmdlet Interno | Descrição |
|:---|:---|
|`Get-AutomationCertificate`|Obtém um certificado para usar numa configuração de livro de execução ou DSC. Devolve um objeto [System.Security.Cryptography.X509Certificates.X509Certificate2.](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)|

> [!NOTE] 
> Deve evitar a utilização `Name` de `Get-AutomationCertificate` variáveis no parâmetro de uma configuração de livro de execução ou DSC. Tais variáveis podem complicar a descoberta de dependências entre livros de execução ou configurações de DSC e variáveis de Automação no momento do design.

## <a name="python-2-functions-to-access-certificates"></a>Funções Python 2 para aceder a certificados

Utilize a função na tabela seguinte para aceder aos certificados num livro de execução Python 2.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_certificate` | Recupera informações sobre um ativo de certificado. |

> [!NOTE]
> Deve importar `automationassets` o módulo no início do seu livro de execução Python para aceder às funções de ativo.

## <a name="create-a-new-certificate"></a>Criar um novo certificado

Quando cria um novo certificado, faz o upload de um ficheiro .cer ou .pfx para a Automação. Se marcar o certificado como exportável, então pode transferi-lo para fora da loja de certificados Automation. Se não for exportável, só pode ser utilizado para assinar dentro do livro de execução ou da configuração DSC. A automatização requer que o certificado tenha o fornecedor **Microsoft Enhanced RSA e AES Cryptographic Provider**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Criar um novo certificado com o portal Azure

1. Na sua conta de Automação, selecione**Certificados** > de **Ativos** > **Adicione um certificado**.
1. No campo **Nome,** digite um nome para o certificado.
1. Para procurar um ficheiro **.cer** ou **.pfx,** sob **o upload de um ficheiro de certificado,** escolha **Selecione um ficheiro**. Se selecionar um ficheiro **.pfx,** especifique uma palavra-passe e indique se pode ser exportado.
1. Selecione **Criar** para salvar o novo ativo do certificado.

### <a name="create-a-new-certificate-with-powershell"></a>Criar um novo certificado com a PowerShell

O exemplo que se segue demonstra como criar um novo certificado de Automação e marcá-lo exportável. Este exemplo importa um ficheiro **.pfx** existente.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Crie um novo certificado com um modelo de Gestor de Recursos

O exemplo que se segue demonstra como implementar um certificado na sua conta Automation utilizando um modelo de Gestor de Recursos através do PowerShell:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>Obter um certificado

Para obter um certificado, `Get-AutomationCertificate` utilize o cmdlet interno. Não é possível utilizar o [CmDLet Get-AzAutomationCertificate,](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) porque devolve informações sobre o ativo do certificado, mas não o próprio certificado.

### <a name="textual-runbook-example"></a>Exemplo de livro textual

O exemplo que se segue mostra como adicionar um certificado a um serviço de nuvem num livro de corridas. Nesta amostra, a palavra-passe é recuperada a partir de uma variável de automatização encriptada.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>Exemplo de livro de execução gráfico

Adicione uma atividade `Get-AutomationCertificate` para o cmdlet interno a um livro de execução gráfico clicando no certificado no painel da Biblioteca e selecionando **Adicionar à tela**.

![Screenshot de adicionar um certificado à tela](../media/certificates/automation-certificate-add-to-canvas.png)

A imagem que se segue mostra um exemplo de utilização de um certificado num livro gráfico. 

![Screenshot de um exemplo de autoria gráfica](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Exemplo python 2

O exemplo que se segue mostra como aceder a certificados em livros de execução Python 2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os cmdlets usados para aceder a certificados, consulte [Gerir módulos em Automação Azure](modules.md).
* Para obter informações gerais sobre livros de execução, consulte a execução do Livro de [Corridas na Automação Azure.](../automation-runbook-execution.md)
* Para mais detalhes sobre as configurações do DSC, consulte a visão geral da [Configuração do Estado](../automation-dsc-overview.md). 
