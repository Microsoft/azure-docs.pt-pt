---
title: Gerir certificado na Automação Azure
description: Os certificados são armazenados de forma segura na Automatização Azure para que os livros de execução ou as configurações dSC possam aceder-lhes para autenticar contra os recursos azure e de terceiros. Este artigo explica os detalhes dos certificados e como trabalhar com eles tanto na autoria textual como gráfica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732834"
---
# <a name="manage-certificates-in-azure-automation"></a>Gerir certificados na Automação Azure

Os certificados são armazenados de forma segura na Automatização Azure para que possam ser acedidos através de livros de execução ou configurações DSC utilizando a atividade [Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) para recursos do Gestor de Recursos Azure. O armazenamento seguro de certificadopermite criar livros de execução e configurações DSC que utilizam certificados para autenticação ou adicioná-los a recursos Azure ou de terceiros.

Os ativos seguros na Automatização Azure incluem credenciais, certificados, ligações e variáveis encriptadas. Estes ativos são encriptados e armazenados na Automatização Azure utilizando uma chave única que é gerada para cada conta de automação. Esta chave está armazenada num cofre de chaves gerido pelo sistema. Antes de armazenar um ativo seguro, a chave é carregada a partir do Cofre chave e depois usada para encriptar o ativo. Este processo é gerido pela Azure Automation.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md).

## <a name="az-powershell-cmdlets"></a>Cmdlets Az PowerShell

Para a Az, os cmdlets na tabela seguinte são usados para criar e gerir ativos credenciais de automação com o Windows PowerShell. Eles enviam como parte do [módulo Az.Automation,](/powershell/azure/overview)que está disponível para uso em livros de automação e configurações DSC.

|Cmdlet |Descrição|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Envia um certificado de serviço para o serviço de nuvem especificado.|
|[Get-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|Recupera informações sobre um certificado para usar numa configuração de livro de execução ou DSC. Só é possível recuperar o `Get-AutomationCertificate` certificado utilizando a atividade.|
|[Novo Certificado de Automação](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|Cria um novo certificado na Azure Automation.|
|[Remover-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|Remove um certificado da Azure Automation.|
|[Set-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|Define as propriedades para um certificado existente, incluindo o upload do ficheiro de certificado e a definição da palavra-passe para um ficheiro **.pfx.**|

## <a name="activities"></a>Atividades

As atividades na tabela seguinte são utilizadas para aceder a certificados em configurações de livro de execução e DSC.

| Atividades | Descrição |
|:---|:---|
|`Get-AutomationCertificate`|Obtém um certificado para usar numa configuração de livro de execução ou DSC. Devolve um objeto [System.Security.Cryptography.X509Certificates.X509Certificate2.](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)|

> [!NOTE] 
> Deve evitar a utilização `Name` de `Get-AutomationCertificate` variáveis no parâmetro de uma configuração de livro de execução ou DSC. A utilização de variáveis neste parâmetro complica a descoberta de dependências entre os livros de execução ou as configurações de DSC e as variáveis de Automação no momento do design.

## <a name="python-2-functions"></a>Funções Python 2

A função na tabela seguinte é utilizada para aceder a certificados num livro de execução Python 2.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_certificate` | Recupera informações sobre um ativo de certificado. |

> [!NOTE]
> Deve importar `automationassets` o módulo no início do seu livro de execução Python para aceder às funções de ativo.

## <a name="creating-a-new-certificate"></a>Criação de um novo certificado

Quando cria um novo certificado, faz o upload de um ficheiro .cer ou .pfx para a Azure Automation. Se marcar o certificado como exportável, então pode transferi-lo para fora da loja de certificados Azure Automation. Se não for exportável, só pode ser utilizado para assinar dentro do livro de execução ou da configuração DSC. A Azure Automation requer que o certificado tenha o fornecedor **Microsoft Enhanced RSA e AES Cryptographic Provider**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Criar um novo certificado com o portal Azure

1. Na sua conta de Automação, clique em **Ativos** para abrir a página De ativos.
2. Selecione **Certificados** para abrir a página Certificados.
3. Clique Em **adicionar um certificado** no topo da página.
4. Digite um nome para o certificado no campo **Nome.**
5. Para procurar um ficheiro **.cer** ou **.pfx,** clique em **Selecionar um ficheiro** sob o upload de um ficheiro de **certificado**. Se selecionar um ficheiro **.pfx,** especifique uma palavra-passe e indique se pode ser exportado.
6. Clique em **Criar** para salvar o novo ativo do certificado.

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

## <a name="using-a-certificate"></a>Utilização de um certificado

Para usar um certificado, use a `Get-AutomationCertificate` atividade. Não é possível utilizar o [CmDLet Get-AzAutomationCertificate,](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) uma vez que devolve informações sobre o ativo do certificado, mas não o próprio certificado.

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

Adicione `Get-AutomationCertificate` uma atividade a um livro de execução gráfico clicando no certificado no painel da Biblioteca e selecionando **Adicionar à tela**.

![Adicione certificado à tela](../media/certificates/automation-certificate-add-to-canvas.png)

A imagem que se segue mostra um exemplo de utilização de um certificado num livro gráfico. Este é o mesmo que o exemplo anterior que mostra como adicionar um certificado a um serviço de nuvem a partir de um livro textual.

![Exemplo de autoria gráfica](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Exemplo python 2

O exemplo que se segue mostra como aceder a certificados em livros de execução Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre trabalhar com links para controlar o fluxo lógico de atividades realizadas pelo seu livro de corridas, consulte [Links na autoria gráfica](../automation-graphical-authoring-intro.md#links-and-workflow). 
