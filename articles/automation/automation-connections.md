---
title: Ativos de conexão na automação do Azure
description: Os ativos de conexão na automação do Azure contêm as informações necessárias para se conectar a um serviço ou aplicativo externo de um runbook ou configuração DSC. Este artigo explica os detalhes das conexões e como trabalhar com elas na criação de texto e gráfica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 01/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 90b2234607ad120c43e241fe4ae5222fe285803e
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001655"
---
# <a name="connection-assets-in-azure-automation"></a>Ativos de conexão na automação do Azure

Um ativo de conexão de automação contém as informações necessárias para se conectar a um serviço ou aplicativo externo de um runbook ou configuração DSC. Isso pode incluir informações necessárias para autenticação, como um nome de usuário e senha, além de informações de conexão, como uma URL ou uma porta. O valor de uma conexão é manter todas as propriedades para se conectar a um aplicativo específico em um ativo, em vez de criar várias variáveis. O usuário pode editar os valores para uma conexão em um único lugar, e você pode passar o nome de uma conexão para um runbook ou uma configuração DSC em um só parâmetro. As propriedades de uma conexão podem ser acessadas no runbook ou na configuração DSC com a atividade **Get-AutomationConnection** .

Ao criar uma conexão, você deve especificar um *tipo de conexão*. O tipo de conexão é um modelo que define um conjunto de propriedades. A conexão define os valores para cada propriedade definida em seu tipo de conexão. Os tipos de conexão são adicionados à automação do Azure nos módulos de integração ou criados com a [API de automação do Azure](/previous-versions/azure/reference/mt163818(v=azure.100)) se o módulo de integração inclui um tipo de conexão e é importado para sua conta de automação. Caso contrário, será necessário criar um arquivo de metadados para especificar um tipo de conexão de automação.  Para obter mais informações sobre isso, consulte [módulos de integração](automation-integration-modules.md).

>[!NOTE]
>Os ativos seguros na automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é armazenada em um Key Vault gerenciado pelo sistema. Antes de armazenar um ativo seguro, a chave é carregada de Key Vault e, em seguida, usada para criptografar o ativo. Esse processo é gerenciado pela automação do Azure.

## <a name="connection-types"></a>Tipos de ligação

Há três tipos de conexões internas disponíveis na automação do Azure:

* **Azure** -esta conexão pode ser usada para gerenciar recursos clássicos.
* **AzureClassicCertificate** -essa conexão é usada pela conta do **AzureClassicRunAs** .
* **AzureServicePrincipal** -essa conexão é usada pela conta do **AzureRunAs** .

Na maioria dos casos, não é necessário criar um recurso de conexão conforme ele é criado quando você cria uma [conta executar](manage-runas-account.md)como.

## <a name="windows-powershell-cmdlets"></a>Cmdlets do Windows PowerShell

Os cmdlets na tabela a seguir são usados para criar e gerenciar conexões de automação com o Windows PowerShell. Eles são fornecidos como parte do [módulo Azure PowerShell](/powershell/azure/overview) que está disponível para uso em Runbooks de automação e configurações DSC.

|Cmdlet|Descrição|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Recupera uma conexão. Inclui uma tabela de hash com os valores dos campos da conexão.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Cria uma nova ligação.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Remova uma ligação existente.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Define o valor de um determinado campo para uma ligação existente.|

## <a name="activities"></a>Atividades

As atividades na tabela a seguir são usadas para acessar conexões em um runbook ou configuração DSC.

|Atividades|Descrição|
|---|---|
|[Get-AutomationConnection](/powershell/module/servicemanagement/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Obtém uma conexão a ser usada. Retorna uma tabela de hash com as propriedades da conexão.|

>[!NOTE]
>Evite usar variáveis com o parâmetro – Name de **Get-AutomationConnection** , pois isso pode complicar a descoberta de dependências entre runbooks ou configurações DSC e ativos de conexão em tempo de design.


## <a name="python2-functions"></a>Funções Python2
A função na tabela a seguir é usada para acessar conexões em um runbook do Python2.

| Função | Descrição |
|:---|:---|
| automationassets.get_automation_connection | Recupera uma conexão. Retorna um dicionário com as propriedades da conexão. |

> [!NOTE]
> Você deve importar o módulo "automationassets" na parte superior do seu runbook do Python para acessar as funções de ativo.

## <a name="creating-a-new-connection"></a>Criando uma nova conexão

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Para criar uma nova conexão com o portal do Azure

1. Na sua conta de automação, clique na parte **ativos** para abrir a folha **ativos** .
2. Clique na parte **conexões** para abrir a folha **conexões** .
3. Clique em **Adicionar uma conexão** na parte superior da folha.
4. Na lista suspensa **tipo** , selecione o tipo de conexão que você deseja criar. O formulário apresentará as propriedades desse tipo específico.
5. Preencha o formulário e clique em **criar** para salvar a nova conexão.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Para criar uma nova conexão com o Windows PowerShell

Crie uma nova conexão com o Windows PowerShell usando o cmdlet [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) . Esse cmdlet tem um parâmetro chamado **ConnectionFieldValues** que espera uma [tabela de hash](https://technet.microsoft.com/library/hh847780.aspx) definindo valores para cada uma das propriedades definidas pelo tipo de conexão.

Se você estiver familiarizado com a [conta Executar como](automation-sec-configure-azure-runas-account.md) da automação para autenticar runbooks usando a entidade de serviço, o script do PowerShell, fornecido como uma alternativa para criar a conta Executar como do portal, criará um novo ativo de conexão usando o os comandos de exemplo a seguir.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Você pode usar o script para criar o ativo de conexão porque, quando você cria sua conta de automação, ele inclui automaticamente vários módulos globais por padrão junto com o tipo de conexão **AzureServicePrincipal** para criar oAtivo de conexão AzureRunAsConnection.  Isso é importante para ter em mente, porque se você tentar criar um novo ativo de conexão para se conectar a um serviço ou aplicativo com um método de autenticação diferente, ele falhará porque o tipo de conexão ainda não está definido em sua conta de automação.  Para obter mais informações sobre como criar seu próprio tipo de conexão para seu módulo personalizado ou de [Galeria do PowerShell](https://www.powershellgallery.com), consulte [módulos de integração](automation-integration-modules.md)

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Usando uma conexão em um runbook ou configuração DSC

Você recupera uma conexão em um runbook ou configuração DSC com o cmdlet **Get-AutomationConnection** .  Você não pode usar a atividade [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) .  Essa atividade recupera os valores dos diferentes campos na conexão e os retorna como uma tabela de [hash](https://go.microsoft.com/fwlink/?LinkID=324844) que pode ser usada com os comandos apropriados no runbook ou na configuração DSC.

### <a name="textual-runbook-sample"></a>Exemplo de runbook textual

Os comandos de exemplo a seguir mostram como usar a conta Executar como mencionada anteriormente, para autenticar com Azure Resource Manager recursos em seu runbook.  Ele usa o ativo de conexão que representa a conta Executar como, que faz referência à entidade de serviço baseada em certificado, não às credenciais.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount** agora é um alias para **Connect-AzureRmAccount**. Ao pesquisar os itens de biblioteca, se você não vir **Connect-AzureRMAccount**, poderá usar **Add-AzureRMAccount**ou poderá atualizar seus módulos na sua conta de automação.

### <a name="graphical-runbook-samples"></a>Exemplos de runbook gráfico

Você adiciona uma atividade **Get-AutomationConnection** a um runbook gráfico clicando com o botão direito do mouse na conexão no painel Biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![Adicionar à tela](media/automation-connections/connection-add-canvas.png)

A imagem a seguir mostra um exemplo de como usar uma conexão em um runbook gráfico.  Este é o mesmo exemplo mostrado acima para autenticação usando a conta Executar como com um runbook textual.  Este exemplo usa o conjunto de dados de **valor constante** para a atividade **obter conexão runas** que usa um objeto de conexão para autenticação.  Um [link de pipeline](automation-graphical-authoring-intro.md#links-and-workflow) é usado aqui, pois o conjunto de parâmetros ServicePrincipalCertificate está esperando um único objeto.

![obter conexões](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Exemplo de runbook do Python2
O exemplo a seguir mostra como autenticar usando a conexão executar como em um runbook do Python2.

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
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

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
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>Passos seguintes

- Examine os [links na criação gráfica](automation-graphical-authoring-intro.md#links-and-workflow) para entender como direcionar e controlar o fluxo de lógica em seus runbooks.

- Para saber mais sobre o uso da automação do Azure de módulos do PowerShell e as práticas recomendadas para criar seus próprios módulos do PowerShell para trabalhar como módulos de integração na automação do Azure, consulte [módulos de integração](automation-integration-modules.md).

