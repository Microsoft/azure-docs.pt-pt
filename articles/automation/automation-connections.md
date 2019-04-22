---
title: Ativos de ligação na automatização do Azure
description: Ativos de ligação na automatização do Azure contêm as informações necessárias para ligar a uma aplicação ou serviço externo a partir de um runbook ou configuração DSC. Este artigo explica os detalhes de ligações e como trabalhar com os mesmos na criação de texto e gráficos.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e7dccc4a396d4cf8af1062057c4c3ce6efe978ed
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58918030"
---
# <a name="connection-assets-in-azure-automation"></a>Ativos de ligação na automatização do Azure

Um recurso de ligação da automatização contém as informações necessárias para ligar a uma aplicação ou serviço externo a partir de um runbook ou configuração DSC. Isto pode incluir as informações necessárias para autenticação, como um nome de utilizador e palavra-passe, além das informações de ligação, como um URL ou uma porta. O valor de uma conexão é manter todas as propriedades para ligar a uma determinada aplicação num recurso em vez de criar diversas variáveis. O utilizador pode editar os valores de uma ligação num único local, e pode passar o nome de uma ligação para um runbook ou configuração DSC num único parâmetro. As propriedades de uma ligação podem ser acedidas no runbook ou configuração DSC com o **Get-AutomationConnection** atividade. 

Quando cria uma ligação, tem de especificar um *tipo de ligação*. O tipo de ligação é um modelo que define um conjunto de propriedades. A ligação define valores para cada propriedade definida no seu tipo de ligação. Tipos de ligação são adicionados ao automatização do Azure em módulos de integração ou criados com o [do Azure da API de automação](/previous-versions/azure/reference/mt163818(v=azure.100)) se o módulo de integração inclui um tipo de ligação e é importado para sua conta de automatização. Caso contrário, terá de criar um ficheiro de metadados para especificar um tipo de ligação de automatização.  Para obter mais informações sobre isso, consulte [módulos de integração](automation-integration-modules.md).  

>[!NOTE]
>Os elementos seguros na automatização do Azure incluem as credenciais, certificados, ligações e variáveis encriptadas. Esses ativos são encriptados e armazenados na automatização do Azure com uma chave exclusiva que é gerada para cada conta de automatização. Esta chave é armazenada num sistema gerido Key Vault. Antes de armazenar um recurso seguro, a chave é carregada a partir do Key Vault e, em seguida, utilizada para encriptar o elemento. Este processo é gerido pela automatização do Azure.

## <a name="connection-types"></a>Tipos de ligação

Existem três tipos de ligações incorporadas disponíveis na automatização do Azure:

* **Azure** -esta ligação pode ser utilizada para gerir recursos clássicos.
* **AzureClassicCertificate** -esta ligação é utilizada pela **AzureClassicRunAs** conta.
* **AzureServicePrincipal** -esta ligação é utilizada pela **AzureRunAs** conta.

Na maioria dos casos não é necessário criar um recurso de ligação, já que é criado quando cria um [conta RunAs](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Cmdlets do Windows PowerShell

Os cmdlets na tabela seguinte são utilizados para criar e gerir ligações de automatização com o Windows PowerShell. Eles são fornecidos como parte do [módulo do Azure PowerShell](/powershell/azure/overview) que está disponível para utilização nos runbooks de automatização e configurações de DSC.

|Cmdlet|Descrição|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Obtém uma ligação. Inclui uma tabela de hash com os valores dos campos da ligação.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Cria uma nova ligação.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Remova uma ligação existente.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Define o valor de um determinado campo para uma ligação existente.|

## <a name="activities"></a>Atividades

As atividades na tabela seguinte são utilizadas para aceder a ligações num runbook ou configuração DSC.

|Atividades|Descrição|
|---|---|
|[Get-AutomationConnection](/powershell/module/servicemanagement/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Obtém uma ligação a utilizar. Devolve uma tabela de hash com as propriedades da ligação.|

>[!NOTE] 
>Deve evitar utilizar variáveis com o parâmetro – Name de **Get-AutomationConnection** , uma vez que isso pode dificultar a deteção de dependências entre runbooks ou configurações de DSC e ativos de ligação em tempo de design.

 
## <a name="python2-functions"></a>Funções do Python2 
A função na tabela seguinte é utilizada para aceder a ligações num Python2 runbook. 

| Função | Descrição | 
|:---|:---| 
| automationassets.get_automation_connection | Obtém uma ligação. Retorna um dicionário com as propriedades da ligação. | 

> [!NOTE] 
> Tem de importar o módulo "automationassets" na parte superior do seu runbook de Python para acessar as funções de recurso.

## <a name="creating-a-new-connection"></a>Criar uma nova ligação

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Para criar uma nova ligação com o portal do Azure

1. A partir da sua conta de automatização, clique nas **ativos** parte para abrir o **ativos** painel.
2. Clique nas **conexões** parte para abrir o **ligações** painel.
3. Clique em **adicionar uma ligação** na parte superior do painel.
4. Na **tipo** lista pendente, selecione o tipo de ligação que pretende criar. O formulário irá apresentar as propriedades para esse tipo específico.
5. Preencha o formulário e clique em **criar** para guardar a nova ligação.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Para criar uma nova ligação com o Windows PowerShell

Criar uma nova ligação com o Windows PowerShell através da [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) cmdlet. Este cmdlet tem um parâmetro denominado **ConnectionFieldValues** que espera que um [tabela de hash](https://technet.microsoft.com/library/hh847780.aspx) definir valores para cada uma das propriedades definidas pelo tipo de ligação.

Se estiver familiarizado com a automação [conta Run As](automation-sec-configure-azure-runas-account.md) para autenticar runbooks com o principal de serviço, o script do PowerShell, fornecido como uma alternativa para criar a conta Run As no portal, cria uma nova ligação recurso com os seguintes comandos de exemplo.  

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Pode utilizar o script para criar o recurso de ligação, porque quando criar a sua conta de automatização, ele automaticamente inclui vários módulos global por predefinição, juntamente com o tipo de ligação **AzureServicePrincipal** para criar a **AzureRunAsConnection** recurso de ligação.  Isso é importante ter em mente, porque se está tentando criar um novo elemento de ligação para ligar a um serviço ou aplicação com um método de autenticação diferentes, ela falhará porque o tipo de ligação já não está definido na sua conta de automatização.  Para obter mais informações sobre como criar seu próprio tipo de ligação para o seu personalizado ou o módulo a partir da [galeria do PowerShell](https://www.powershellgallery.com), consulte [módulos de integração](automation-integration-modules.md)
  
## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Utilizar uma ligação num runbook ou configuração DSC

Recupera uma ligação num runbook ou configuração DSC com o **Get-AutomationConnection** cmdlet.  Não é possível utilizar o [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) atividade.  Esta atividade obtém os valores dos campos diferentes na ligação e devolve-los como um [tabela de hash](https://go.microsoft.com/fwlink/?LinkID=324844) que, em seguida, podem ser utilizadas com os comandos apropriados no runbook ou configuração DSC.

### <a name="textual-runbook-sample"></a>Exemplo de textual runbook

Comandos de exemplo seguintes mostram como utilizar a conta Run As mencionado anteriormente, para autenticar com recursos do Azure Resource Manager no runbook.  Ele usa o recurso de ligação que representa a conta Run As, que faz referência a baseada em certificado principal de serviço, não as credenciais.  

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection 
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount** agora é um alias para **Connect-AzureRMAccount**. Quando pesquisar a sua biblioteca de itens, se não vir **Connect-AzureRMAccount**, pode utilizar **Add-AzureRmAccount**, ou pode atualizar os módulos na conta de automatização.

### <a name="graphical-runbook-samples"></a>Exemplos de runbook gráfico

Adicionar uma **Get-AutomationConnection** atividade para um runbook gráfico clicar na ligação no painel de biblioteca de editor gráfico e selecionando **adicionar à tela**.

![Adicionar à tela](media/automation-connections/connection-add-canvas.png)

A imagem seguinte mostra um exemplo de uso de uma ligação num runbook gráfico.  Este é o mesmo exemplo mostrado acima, para autenticação com a conta Run As com um runbook textual.  Este exemplo utiliza a **valor constante** conjunto de dados para o **obter ligação de RunAs** atividade que utiliza um objeto de conexão para autenticação.  R [ligação de pipeline](automation-graphical-authoring-intro.md#links-and-workflow) é utilizado aqui, uma vez que o conjunto de parâmetros ServicePrincipalCertificate está esperando um único objeto.

![obter as ligações](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Exemplo de runbook Python2
O exemplo a seguir mostra como autenticar a ligação Run As a utilizar num Python2 runbook.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
  """ Returns credentials to authenticate against Azure resoruce manager """
  from OpenSSL import crypto
  from msrestazure import azure_active_directory
  import adal

  # Get the Azure Automation Run As service principal certificate
  cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
  pks12_cert = crypto.load_pkcs12(cert)
  pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

  # Get Run As connection information for the Azure Automation service principal
  application_id = runas_connection["ApplicationId"]
  thumbprint = runas_connection["CertificateThumbprint"]
  tenant_id = runas_connection["TenantId"]

  # Authenticate with service principal certificate
  resource = "https://management.core.windows.net/"
  authority_url = ("https://login.microsoftonline.com/" + tenant_id)
  context = adal.AuthenticationContext(authority_url)
  return azure_active_directory.AdalAuthentication(
    lambda: context.acquire_token_with_client_certificate(
      resource,
      application_id,
      pem_pkey,
      thumbprint)
  )

# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>Passos Seguintes

- Revisão [Links na criação de gráficos](automation-graphical-authoring-intro.md#links-and-workflow) para compreender como direcionar e controlar o fluxo de lógica nos runbooks.  

- Para saber mais sobre a utilização da automatização do Azure de módulos do PowerShell e práticas recomendadas para a criação de seus próprios módulos do PowerShell para trabalhar como módulos de integração na automatização do Azure, veja [módulos de integração](automation-integration-modules.md).  

