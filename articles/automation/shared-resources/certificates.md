---
title: Ativos de certificado na automação do Azure
description: Os certificados estão com segurança na automação do Azure para que possam ser acessados por runbooks ou configurações DSC para autenticação no Azure e em recursos de terceiros.  Este artigo explica os detalhes dos certificados e como trabalhar com eles na criação gráfica e textual.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a66f73e028594cf90f1fa1765910a3df3adbad1a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849484"
---
# <a name="certificate-assets-in-azure-automation"></a>Ativos de certificado na automação do Azure

Os certificados são armazenados com segurança na automação do Azure para que possam ser acessados por runbooks ou configurações DSC usando a atividade **Get-AzureRmAutomationCertificate** para recursos Azure Resource Manager. Esse recurso permite que você crie runbooks e configurações DSC que usam certificados para autenticação ou os adiciona ao Azure ou a recursos de terceiros.

>[!NOTE]
>Os ativos seguros na automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é armazenada em um Key Vault gerenciado pelo sistema. Antes de armazenar um ativo seguro, a chave é carregada de Key Vault e, em seguida, usada para criptografar o ativo. Esse processo é gerenciado pela automação do Azure.

## <a name="azurerm-powershell-cmdlets"></a>Cmdlets do PowerShell do AzureRM

Para AzureRM, os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de credenciais de automação com o Windows PowerShell. Eles são fornecidos como parte do [módulo AzureRM. Automation](/powershell/azure/overview), que está disponível para uso em Runbooks de automação e configurações DSC.

|Cmdlets|Descrição|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Recupera informações sobre um certificado a ser usado em um runbook ou configuração DSC. Você só pode recuperar o certificado em si da atividade Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Cria um novo certificado na automação do Azure.|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Remove um certificado da automação do Azure.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Define as propriedades de um certificado existente, incluindo o carregamento do arquivo de certificado e a definição da senha para um. pfx.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Carrega um certificado de serviço para o serviço de nuvem especificado.|

## <a name="activities"></a>Atividades

As atividades na tabela a seguir são usadas para acessar certificados em um runbook e configurações DSC.

| Atividades | Descrição |
|:---|:---|
|Get-AutomationCertificate|Obtém um certificado a ser usado em uma configuração de runbook ou DSC. Retorna um objeto [System. Security. Cryptography. X509Certificates. X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) .|

> [!NOTE] 
> Evite usar variáveis no parâmetro – Name de **Get-AutomationCertificate** em um runbook ou configuração DSC, pois ela complica a descoberta de dependências entre runbooks ou configuração DSC e variáveis de automação em tempo de design.

## <a name="python2-functions"></a>Funções Python2

A função na tabela a seguir é usada para acessar certificados em um runbook do Python2.

| Função | Descrição |
|:---|:---|
| automationassets.get_automation_certificate | Recupera informações sobre um ativo de certificado. |

> [!NOTE]
> Você deve importar o módulo **automationassets** no início do seu runbook do Python para acessar as funções de ativo.

## <a name="creating-a-new-certificate"></a>Criando um novo certificado

Ao criar um novo certificado, você carrega um arquivo. cer ou. pfx para a automação do Azure. Se você marcar o certificado como exportável, poderá transferi-lo para fora do repositório de certificados da automação do Azure. Se não for exportável, ele só poderá ser usado para assinatura dentro do runbook ou da configuração DSC. A automação do Azure exige que o certificado tenha o provedor: **Microsoft Enhanced RSA e AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Para criar um novo certificado com o portal do Azure

1. Na sua conta de automação, clique no bloco **ativos** para abrir a página **ativos** .
2. Clique no bloco **certificados** para abrir a página **certificados** .
3. Clique em **Adicionar um certificado** na parte superior da página.
4. Escreva um nome para o certificado no **nome** caixa.
5. Para procurar um arquivo. cer ou. pfx, clique em **selecionar um arquivo** em **carregar um arquivo de certificado**. Se você selecionar um arquivo. pfx, especifique uma senha e se ela pode ser exportada.
6. Clique em **criar** para salvar o novo ativo de certificado.

### <a name="to-create-a-new-certificate-with-powershell"></a>Para criar um novo certificado com o PowerShell

O exemplo a seguir demonstra como criar um novo certificado de automação e marcá-lo como exportável. Isso importa um arquivo. pfx existente.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Criar um novo certificado com o modelo do Resource Manager

O exemplo a seguir demonstra como implantar um certificado em sua conta de automação usando um modelo do Resource Manager por meio do PowerShell:

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

## <a name="using-a-certificate"></a>Usando um certificado

Para usar um certificado, use a atividade **Get-AutomationCertificate** . Você não pode usar o cmdlet [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) , pois ele retorna informações sobre o ativo de certificado, mas não o próprio certificado.

### <a name="textual-runbook-sample"></a>Exemplo de runbook textual

O código de exemplo a seguir mostra como adicionar um certificado a um serviço de nuvem em um runbook. Neste exemplo, a senha é recuperada de uma variável de automação criptografada.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Exemplo de runbook gráfico

Você adiciona um **Get-AutomationCertificate** a um runbook gráfico clicando com o botão direito do mouse no certificado no painel Biblioteca e selecionando **Adicionar à tela**.

![Adicionar certificado à tela](../media/certificates/automation-certificate-add-to-canvas.png)

A imagem a seguir mostra um exemplo de como usar um certificado em um runbook gráfico. Isso é o mesmo que o exemplo anterior que mostra como adicionar um certificado a um serviço de nuvem de um runbook textual.

![Exemplo de criação gráfica](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Exemplo de Python2

O exemplo a seguir mostra como acessar certificados em runbooks Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre como trabalhar com links para controlar o fluxo lógico de atividades que seu runbook foi projetado para executar, consulte [links na criação gráfica](../automation-graphical-authoring-intro.md#links-and-workflow). 
