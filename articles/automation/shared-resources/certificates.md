---
title: Recursos de certificados na Automatização do Azure
description: Os certificados estão seguros na Automação Azure para que possam ser acedidos através de livros de execução ou configurações DSC para autenticar contra os recursos Azure e de terceiros.  Este artigo explica os detalhes dos certificados e como trabalhar com eles tanto na autoria textual como gráfica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a66f73e028594cf90f1fa1765910a3df3adbad1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849484"
---
# <a name="certificate-assets-in-azure-automation"></a>Recursos de certificados na Automatização do Azure

Os certificados são armazenados de forma segura na Automatização Azure para que possam ser acedidos através de livros de execução ou configurações DSC utilizando a atividade **Get-AzureRmAutomationCertificate** para recursos do Gestor de Recursos Azure. Esta capacidade permite-lhe criar livros de execução e configurações DSC que utilizam certificados para autenticação ou os adicionam a recursos Azure ou de terceiros.

>[!NOTE]
>Os ativos seguros na Automatização Azure incluem credenciais, certificados, ligações e variáveis encriptadas. Estes ativos são encriptados e armazenados na Automatização Azure utilizando uma chave única que é gerada para cada conta de automação. Esta chave está armazenada num cofre gerido pelo sistema. Antes de armazenar um ativo seguro, a chave é carregada a partir do Cofre chave e depois usada para encriptar o ativo. Este processo é gerido pela Azure Automation.

## <a name="azurerm-powershell-cmdlets"></a>Cmdlets De PowerShell AzureRM

Para o AzureRM, os cmdlets na tabela seguinte são utilizados para criar e gerir ativos credenciais de automação com o Windows PowerShell. Eles enviam como parte do [módulo De automação AzureRM,que](/powershell/azure/overview)está disponível para utilização em livros de automação e configurações DSC.

|Cmdlets|Descrição|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Recupera informações sobre um certificado para usar numa configuração de livro de execução ou DSC. Só é possível recuperar o certificado da atividade get-AutomationCertificate.|
|[Novo Certificado de Automação AzureRm](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Cria um novo certificado na Azure Automation.|
[Remover-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Remove um certificado da Azure Automation.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Define as propriedades para um certificado existente, incluindo o upload do ficheiro de certificado e a definição da palavra-passe para um .pfx.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Envia um certificado de serviço para o serviço de nuvem especificado.|

## <a name="activities"></a>Atividades

As atividades na tabela seguinte são utilizadas para aceder a certificados em configurações de livro de execução e DSC.

| Atividades | Descrição |
|:---|:---|
|Get-AutomationCertificate|Obtém um certificado para usar numa configuração de livro de execução ou DSC. Devolve um objeto [System.Security.Cryptography.X509Certificates.X509Certificate2.](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)|

> [!NOTE] 
> Deve evitar a utilização de variáveis no "Nome parâmetro de **Get-AutomationCertificate"** num livro de execução ou configuração DSC, uma vez que complica a descoberta de dependências entre livros de execução ou configuração DSC, e variáveis de Automação no momento do design.

## <a name="python2-functions"></a>Funções Python2

A função na tabela seguinte é utilizada para aceder a certificados num livro python2.

| Função | Descrição |
|:---|:---|
| automação.get_automation_certificate | Recupera informações sobre um ativo de certificado. |

> [!NOTE]
> Deve importar o módulo de **automação** no início do seu livro de execução Python para aceder às funções do ativo.

## <a name="creating-a-new-certificate"></a>Criação de um novo certificado

Quando cria um novo certificado, faz o upload de um ficheiro .cer ou .pfx para a Azure Automation. Se marcar o certificado como exportável, então pode transferi-lo para fora da loja de certificados Azure Automation. Se não for exportável, só pode ser utilizado para assinar dentro do livro de execução ou da configuração DSC. A Azure Automation requer que o certificado tenha o fornecedor: **Microsoft Enhanced RSA e AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Para criar um novo certificado com o portal Azure

1. Na sua conta De automação, clique no azulejo **Do Ativo** para abrir a página **De ativos.**
2. Clique no azulejo dos **Certificados** para abrir a página **Certificados.**
3. Clique Em **adicionar um certificado** no topo da página.
4. Digite um nome para o certificado na caixa de **nomes.**
5. Para procurar um ficheiro .cer ou .pfx, clique em **Selecionar um ficheiro** sob o upload de um ficheiro de **certificado**. Se selecionar um ficheiro .pfx, especifique uma palavra-passe e se pode ser exportado.
6. Clique em **Criar** para salvar o novo ativo do certificado.

### <a name="to-create-a-new-certificate-with-powershell"></a>Para criar um novo certificado com a PowerShell

O exemplo que se segue demonstra como criar um novo certificado de Automação e marcá-lo exportável. Isto importa um ficheiro .pfx existente.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Crie um novo certificado com modelo de Gestor de Recursos

O exemplo que se segue demonstra como implementar um certificado na sua Conta de Automação utilizando um modelo de Gestor de Recursos através do PowerShell:

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
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Utilização de um certificado

Para utilizar um certificado, utilize a atividade **Get-AutomationCertificate.** Não é possível utilizar o [Cemte Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) uma vez que devolve informações sobre o ativo do certificado, mas não o próprio certificado.

### <a name="textual-runbook-sample"></a>Amostra de livro textual

O seguinte código de amostra mostra como adicionar um certificado a um serviço de nuvem num livro de execução. Nesta amostra, a palavra-passe é recuperada a partir de uma variável de automatização encriptada.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Amostra de livro gráfico

Adicione um **Get-AutomationCertificate** a um livro de execução gráfico clicando no certificado no painel da Biblioteca e selecionando **Adicionar à tela**.

![Adicione certificado à tela](../media/certificates/automation-certificate-add-to-canvas.png)

A imagem que se segue mostra um exemplo de utilização de um certificado num livro gráfico. Este é o mesmo que o exemplo anterior que mostra como adicionar um certificado a um serviço de nuvem a partir de um livro textual.

![Exemplo de autoria gráfica](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Amostra Python2

A amostra que se segue mostra como aceder a certificados em livros de execução Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre trabalhar com links para controlar o fluxo lógico de atividades que o seu livro de corridas foi projetado para realizar, consulte [Links na autoria gráfica](../automation-graphical-authoring-intro.md#links-and-workflow). 
