---
title: Ativos de certificado na automatização do Azure
description: Certificados podem ser armazenados em segurança na automatização do Azure para que possam ser acedidos por runbooks ou configurações de DSC para autenticação no Azure e recursos de terceiros.  Este artigo explica os detalhes de certificados e como trabalhar com os mesmos na criação de texto e gráficos.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 372df0bc6ea540b4b2c030ec71acc6889e7c7d19
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56874091"
---
# <a name="certificate-assets-in-azure-automation"></a>Ativos de certificado na automatização do Azure

Certificados podem ser armazenados em segurança na automatização do Azure para que possam ser acedidos por runbooks ou configurações de DSC com o **Get-AzureRmAutomationCertificate** atividade para os recursos do Azure Resource Manager. Esta capacidade permite-lhe criar runbooks e configurações de DSC que utilizam certificados para autenticação ou adiciona-as para o Azure ou recursos de terceiros.

>[!NOTE]
>Os elementos seguros na automatização do Azure incluem as credenciais, certificados, ligações e variáveis encriptadas. Esses ativos são encriptados e armazenados na automatização do Azure com uma chave exclusiva que é gerada para cada conta de automatização. Esta chave é armazenada num sistema gerido Key Vault. Antes de armazenar um recurso seguro, a chave é carregada a partir do Key Vault e, em seguida, utilizada para encriptar o elemento. Este processo é gerido pela automatização do Azure.

## <a name="azurerm-powershell-cmdlets"></a>Cmdlets do AzureRM PowerShell
Para AzureRM, os cmdlets na tabela seguinte são utilizados para criar e gerir recursos de credencial da automatização com o Windows PowerShell. Eles são fornecidos como parte do [módulo do Azurerm](/powershell/azure/overview) que está disponível para utilização nos runbooks de automatização e configurações de DSC.

|Cmdlets|Descrição|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Obtém informação sobre um certificado a utilizar num runbook ou configuração DSC. O certificado propriamente dito apenas pode ser obtida na atividade de Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Cria um novo certificado para a automatização do Azure.|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Remove um certificado da automatização do Azure.|Cria um novo certificado para a automatização do Azure.
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Define as propriedades de um certificado existente, incluindo o carregamento do ficheiro de certificado e definir a palavra-passe para um ficheiro. pfx.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Carrega um certificado de serviço para o serviço cloud especificado.|

## <a name="activities"></a>Atividades
As atividades na tabela seguinte são utilizadas para aceder a certificados num runbook e configurações de DSC.

| Atividades | Descrição |
|:---|:---|
|Get-AutomationCertificate|Obtém um certificado a utilizar num runbook ou configuração DSC. Devolve um [System.Security.Cryptography.X509Certificates.X509Certificate2](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate2.aspx) objeto.|

> [!NOTE] 
> Deve evitar utilizar variáveis no parâmetro – Name de **Get-AutomationCertificate** num runbook ou configuração DSC pois complica a deteção de dependências entre runbooks ou configuração de DSC e variáveis de automatização no tempo de design.

## <a name="python2-functions"></a>Funções do Python2

A função na tabela a seguir é usada para acessar certificados num Python2 runbook.

| Função | Descrição |
|:---|:---|
| automationassets.get_automation_certificate | Obtém informação sobre um recurso de certificado. |

> [!NOTE]
> Tem de importar os **automationassets** módulo no início do seu runbook de Python para acessar as funções de recurso.

## <a name="creating-a-new-certificate"></a>Criar um novo certificado

Quando cria um novo certificado, vai carregar um ficheiro. cer ou. pfx para automatização do Azure. Se marcar o certificado como exportável, pode transferi-la fora do arquivo de certificados de automatização do Azure. Se não é exportável, em seguida, ele só pode ser utilizado para iniciar sessão dentro do runbook ou configuração DSC. A automatização do Azure necessita do certificado para que o fornecedor: **Fornecedor de criptografia RSA e AES de avançada do Microsoft**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Para criar um novo certificado com o portal do Azure

1. A partir da sua conta de automatização, clique nas **ativos** mosaico para abrir o **ativos** painel.
1. Clique nas **certificados** mosaico para abrir o **certificados** painel.
1. Clique em **adicionar um certificado** na parte superior do painel.
1. Escreva um nome para o certificado no **nome** caixa.
1. Procure um ficheiro. cer ou. pfx, clique em **selecionar um ficheiro** sob **carregar um ficheiro de certificado**. Se selecionar um ficheiro. pfx, especifique uma palavra-passe e se é permitido para ser exportada.
1. Clique em **criar** para guardar o novo recurso do certificado.

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Para criar um novo certificado com o Windows PowerShell

O exemplo seguinte demonstra como criar um novo certificado de automatização e marcá-la exportável. Esta ação importa um ficheiro. pfx existente.

```powershell-interactive
$certName = 'MyCertificate'
$certPath = '.\MyCert.pfx'
$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup
```

## <a name="using-a-certificate"></a>Utilizar um certificado

Para utilizar um certificado, utilize o **Get-AutomationCertificate** atividade. Não é possível utilizar o [Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate?view=azurermps-6.6.0) cmdlet, uma vez que ele retorna informações sobre o recurso de certificado, mas não o certificado propriamente dito.

### <a name="textual-runbook-sample"></a>Exemplo de textual runbook

O código de exemplo seguinte mostra como adicionar um certificado a um serviço em nuvem num runbook. Neste exemplo, a palavra-passe é recuperada de uma variável de automatização encriptados.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Exemplo de runbook gráfico

Adicionar uma **Get-AutomationCertificate** para um runbook gráfico clicando com o botão direito do certificado no painel de biblioteca do editor gráfico e selecionando **adicionar à tela**.

![Adicionar certificado à tela](media/automation-certificates/automation-certificate-add-to-canvas.png)

A imagem seguinte mostra um exemplo do uso de um certificado num runbook gráfico. Este é o mesmo do exemplo anterior para adicionar um certificado para um serviço em nuvem a partir de um runbook textual.

![Exemplo de criação de gráficos](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Exemplo do Python2
O exemplo a seguir mostra como acessar certificados nos runbooks do Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert 
```

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre como trabalhar com ligações para controlar o fluxo lógico de atividades do runbook é criado para executar, veja [Links na criação de gráficos](automation-graphical-authoring-intro.md#links-and-workflow). 

