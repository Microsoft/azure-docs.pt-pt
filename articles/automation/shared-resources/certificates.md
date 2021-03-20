---
title: Gerir certificados em Azure Automation
description: Este artigo diz como trabalhar com certificados de acesso por runbooks e configurações DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: cbf9eb6c97dcceeca5e86e8bef47a39fb685792f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97734815"
---
# <a name="manage-certificates-in-azure-automation"></a>Gerir certificados em Azure Automation

A Azure Automation armazena certificados de forma segura para acesso por runbooks e configurações DSC, utilizando o cmdlet [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) para recursos do Azure Resource Manager. O armazenamento seguro de certificados permite-lhe criar runbooks e configurações DSC que usam certificados para autenticação, ou adicioná-los a recursos Azure ou de terceiros.

>[!NOTE]
>Os ativos seguros na Azure Automation incluem credenciais, certificados, conexões e variáveis encriptadas. Estes ativos são encriptados e armazenados na Automação utilizando uma chave única que é gerada para cada conta Dem automação. A automatização armazena a chave no serviço Key Vault gerido pelo sistema. Antes de armazenar um ativo seguro, a Automatização carrega a chave do Key Vault e, em seguida, utiliza-a para encriptar o ativo.

## <a name="powershell-cmdlets-to-access-certificates"></a>Cmdlets PowerShell para aceder a certificados

Os cmdlets na tabela seguinte criam e gerem certificados de Automação com PowerShell. Eles enviam como parte dos [módulos Az.](modules.md#az-modules)

|Cmdlet |Descrição|
| --- | ---|
|[Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate)|Recupera informações sobre um certificado para utilizar num livro de bordo ou configuração DSC. Só é possível recuperar o certificado utilizando o `Get-AutomationCertificate` cmdlet interno.|
|[Novo AzAutomationCertificato](/powershell/module/Az.Automation/New-AzAutomationCertificate)|Cria um novo certificado em Automação.|
|[Remover-AzAutomationCertificato](/powershell/module/Az.Automation/Remove-AzAutomationCertificate)|Remove um certificado da Automação.|
|[Set-AzAutomationCertificato](/powershell/module/Az.Automation/Set-AzAutomationCertificate)|Define as propriedades para um certificado existente, incluindo o upload do ficheiro de certificado e a definição da palavra-passe para um ficheiro **.pfx.**|

O [cmdlet Add-AzureCertificate](/powershell/module/servicemanagement/azure.service/add-azurecertificate) também pode ser utilizado para carregar um certificado de serviço para o serviço de nuvem especificado.

## <a name="internal-cmdlets-to-access-certificates"></a>Cmdlets internos para aceder a certificados

O cmdlet interno na tabela seguinte é utilizado para aceder a certificados nos seus runbooks. Este cmdlet vem com o módulo `Orchestrator.AssetManagement.Cmdlets` global. Para obter mais informações, consulte [as cmdlets internas.](modules.md#internal-cmdlets)

| Cmdlet Interno | Description |
|:---|:---|
|`Get-AutomationCertificate`|Obtém um certificado para usar num livro de bordo ou configuração DSC. De retorna um objeto [System.Security.Cryptography.X509Certificates.X509Certificate2.](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)|

> [!NOTE]
> Deve evitar a utilização de variáveis no `Name` parâmetro de um livro de `Get-AutomationCertificate` execução ou configuração DSC. Tais variáveis podem complicar a descoberta de dependências entre runbooks ou configurações DSC e variáveis de automação no tempo de design.

## <a name="python-functions-to-access-certificates"></a>Funções python para aceder a certificados

Utilize a função na tabela seguinte para aceder aos certificados num livro de bordo Python 2 e 3. Os livros de 100 python estão atualmente em pré-visualização.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_certificate` | Recupera informações sobre um ativo de certificado. |

> [!NOTE]
> Deve importar o `automationassets` módulo no início do seu livro de bordo Python para aceder às funções do ativo.

## <a name="create-a-new-certificate"></a>Criar um novo certificado

Quando criar um novo certificado, faça o upload de um ficheiro .cer ou .pfx para a Automação. Se marcar o certificado como exportável, poderá transferi-lo para fora da loja de certificados Automation. Se não for exportável, só pode ser utilizado para a assinatura dentro do runbook ou da configuração DSC. A automatização requer que o certificado tenha o fornecedor **Microsoft Enhanced RSA e AES Cryptographic Provider**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Criar um novo certificado com o portal Azure

1. Na sua conta de Automação, no painel esquerdo selecione Certificados em **Recursos** **Partilhados.**
1. Na página **certificados,** **selecione Adicionar um certificado**.
1. No campo **Nome,** escreva um nome para o certificado.
1. Para procurar um ficheiro **.cer** ou **.pfx,** no **upload de um ficheiro de certificado,** escolha **Selecione um ficheiro**. Se selecionar um ficheiro **.pfx,** especifique uma palavra-passe e indique se pode ser exportado.
1. Selecione **Criar** para guardar o novo ativo de certificado.

### <a name="create-a-new-certificate-with-powershell"></a>Criar um novo certificado com a PowerShell

O exemplo a seguir demonstra como criar um novo certificado de Automação e marcá-lo exportável. Este exemplo importa um ficheiro **.pfx** existente.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Crie um novo certificado com um modelo de Gestor de Recursos

O exemplo a seguir demonstra como implantar um certificado na sua conta de Automação utilizando um modelo de Gestor de Recursos através do PowerShell:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path and filename>'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>' #A name of your choosing
$ResourceGroupName = '<resource group name>' #The one that holds your automation account
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
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName $ResourceGroupName -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>Obtenha um certificado

Para obter um certificado, utilize o `Get-AutomationCertificate` cmdlet interno. Não pode utilizar o [cmdlet Get-AzAutomationCertificate,](/powershell/module/Az.Automation/Get-AzAutomationCertificate) porque devolve informações sobre o ativo do certificado, mas não sobre o certificado em si.

### <a name="textual-runbook-examples"></a>Exemplos de runbook textual

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

O exemplo a seguir mostra como adicionar um certificado a um serviço de nuvem num livro de bordo. Nesta amostra, a palavra-passe é obtida a partir de uma variável de automação encriptada.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

# <a name="python-2"></a>[Python 2](#tab/python2)

O exemplo a seguir mostra como aceder a certificados em livros python 2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

# <a name="python-3"></a>[Python 3](#tab/python3)

O exemplo a seguir mostra como aceder a certificados em livros de televisão Python 3 (pré-visualização).

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print (cert)
```

---

### <a name="graphical-runbook-example"></a>Exemplo de runbook gráfico

Adicione uma atividade para o `Get-AutomationCertificate` cmdlet interno a um runbook gráfico clicando à direita no certificado no painel da Biblioteca e selecionando **Adicionar à tela**.

![Screenshot de adicionar um certificado à tela](../media/certificates/automation-certificate-add-to-canvas.png)

A imagem a seguir mostra um exemplo de utilização de um certificado num livro gráfico.

![Screenshot de um exemplo de autoria gráfica](../media/certificates/graphical-runbook-add-certificate.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os cmdlets utilizados para aceder a certificados, consulte [Gerir módulos na Azure Automation.](modules.md)
* Para obter informações gerais sobre os runbooks, consulte [a execução do Runbook na Azure Automation](../automation-runbook-execution.md).
* Para obter detalhes sobre as configurações do DSC, consulte [a visão geral da configuração do Estado da Automação Azure](../automation-dsc-overview.md).
