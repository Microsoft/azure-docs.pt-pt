---
title: Recursos de ligações na Automatização do Azure
description: Os ativos de ligação na Automatização Azure contêm as informações necessárias para se ligarem a um serviço ou aplicação externo a partir de um livro de execução ou de uma configuração DSC. Este artigo explica os detalhes das ligações e como trabalhar com eles tanto na autoria textual como gráfica.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: b6276153921feb0e6f27194d36d1c32c1d0ffb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75940816"
---
# <a name="connection-assets-in-azure-automation"></a>Recursos de ligações na Automatização do Azure

Um ativo de ligação automation contém as informações necessárias para se ligar a um serviço ou aplicação externo a partir de um livro de execução ou de uma configuração DSC. Isto pode incluir informações necessárias para a autenticação, tais como um nome de utilizador e uma senha, além de informações de ligação, como um URL ou uma porta. O valor de uma ligação é manter todas as propriedades para ligar a uma determinada aplicação em um ativo em oposição à criação de múltiplas variáveis. O utilizador pode editar os valores para uma ligação num só local, e pode passar o nome de uma ligação a um livro de execução ou configuração DSC num único parâmetro. As propriedades para uma ligação podem ser acedidas na configuração do livro de execução ou DSC com a atividade **Get-AutomationConnection.**

Quando criar uma ligação, deve especificar um tipo de *ligação*. O tipo de ligação é um modelo que define um conjunto de propriedades. A ligação define valores para cada imóvel definido no seu tipo de ligação. Os tipos de ligação são adicionados à Automação Azure em módulos de integração ou criados com a API de [Automação Azure](/previous-versions/azure/reference/mt163818(v=azure.100)) se o módulo de integração incluir um tipo de ligação e for importado para a sua conta De automação. Caso contrário, terá de criar um ficheiro de metadados para especificar um tipo de ligação automation. Para mais informações sobre este aspeto, consulte Módulos de [Integração](automation-integration-modules.md).

>[!NOTE]
>Os ativos seguros na Automatização Azure incluem credenciais, certificados, ligações e variáveis encriptadas. Estes ativos são encriptados e armazenados na Automatização Azure utilizando uma chave única que é gerada para cada conta de automação. Esta chave está armazenada num cofre gerido pelo sistema. Antes de armazenar um ativo seguro, a chave é carregada a partir do Cofre chave e depois usada para encriptar o ativo. Este processo é gerido pela Azure Automation.

## <a name="connection-types"></a>Tipos de ligação

Existem três tipos de ligações incorporadas disponíveis na Automação Azure:

* **Azure** - Esta ligação pode ser usada para gerir recursos clássicos.
* **AzureClassicCertificate** - Esta ligação é utilizada pela conta **AzureClassicRunAs.**
* **AzureServicePrincipal** - Esta ligação é utilizada pela conta **AzureRunAs.**

Na maioria dos casos, não é necessário criar um recurso de ligação porque é criado quando cria uma [conta RunAs](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Cmdlets do Windows PowerShell

Os cmdlets na tabela seguinte são utilizados para criar e gerir ligações de Automação com o Windows PowerShell. Eles enviam como parte do [módulo Azure PowerShell](/powershell/azure/overview), que está disponível para uso em livros de automação e configurações DSC.

|Cmdlet|Descrição|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Recupera uma ligação. Inclui uma tabela de hash com os valores dos campos da ligação.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Cria uma ligação nova.|
|[Remover-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Remove uma ligação existente.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Define o valor de um determinado campo para uma ligação existente.|

## <a name="activities"></a>Atividades

As atividades na tabela seguinte são utilizadas para aceder a ligações numa configuração de livro de execução ou DSC.

|Atividades|Descrição|
|---|---|
|Get-AutomationConnection | Tem uma ligação para usar. Devolve uma tabela de hash com as propriedades da ligação.|

>[!NOTE]
>Deve evitar a utilização de variáveis com o parâmetro de nome da **Get-AutomationConnection,** uma vez que isso pode complicar a descoberta de dependências entre livros de execução ou configurações dSC e ativos de ligação no momento do design.


## <a name="python2-functions"></a>Funções Python2

A função na tabela a seguir é utilizada para aceder a ligações num livro python2.

| Função | Descrição |
|:---|:---|
| automaçãoactivos.get_automation_connection | Recupera uma ligação. Devolve um dicionário com as propriedades da ligação. |

> [!NOTE]
> Deve importar o módulo de "automação" no topo do seu livro de execução Python para aceder às funções do ativo.

## <a name="creating-a-new-connection"></a>Criar uma nova ligação

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Para criar uma nova ligação com o portal Azure

1. Na sua conta de automação, clique na parte **Do Ativo** para abrir a lâmina **Do Ativo.**
2. Clique na parte **Ligações** para abrir a lâmina de **Ligações.**
3. Clique **Em adicionar uma ligação** na parte superior da lâmina.
4. No **dropdown do Tipo,** selecione o tipo de ligação que pretende criar. O formulário apresentará as propriedades para esse tipo em particular.
5. Complete o formulário e clique **em Criar** para salvar a nova ligação.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Para criar uma nova ligação com o Windows PowerShell

Crie uma nova ligação com o Windows PowerShell utilizando o cmdlet [New-AzureRmAutomationConnection.](/powershell/module/azurerm.automation/new-azurermautomationconnection) Este cmdlet tem um parâmetro chamado **ConnectionFieldValues** que espera uma tabela de [hash](https://technet.microsoft.com/library/hh847780.aspx) definindo valores para cada uma das propriedades definidas pelo tipo de ligação.

Se estiver familiarizado com a [Execução](automation-sec-configure-azure-runas-account.md) de Automação Como conta para autenticar os livros de execução utilizando o diretor de serviço, o script PowerShell, fornecido como uma alternativa para criar a conta Run As a partir do portal, cria um novo ativo de ligação utilizando os seguintes comandos de amostra.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

É possível utilizar o script para criar o ativo de ligação porque quando cria a sua conta Automation, inclui automaticamente vários módulos globais por padrão, juntamente com o tipo de ligação **AzureServicePrincipal** para criar o ativo de ligação **AzureRunAsConnection.** Isto é importante ter em mente, porque se tentar criar um novo ativo de ligação para ligar a um serviço ou aplicação com um método de autenticação diferente, falhará porque o tipo de ligação ainda não está definido na sua conta Desmito. Para obter mais informações sobre como criar o seu próprio tipo de ligação para o seu costume ou módulo a partir da [PowerShell Gallery,](https://www.powershellgallery.com)consulte Módulos de [Integração](automation-integration-modules.md)

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Utilizando uma ligação numa configuração de livro de execução ou DSC

Obtém uma ligação numa configuração de livro de execução ou DSC com o cmdlet **Get-AutomationConnection.** Não é possível utilizar a atividade [Get-AzureRmAutomationConnection.](/powershell/module/azurerm.automation/get-azurermautomationconnection) Esta atividade recupera os valores dos diferentes campos na ligação e devolve-os como uma tabela de [hash,](https://go.microsoft.com/fwlink/?LinkID=324844)que pode ser usada com os comandos apropriados na configuração do livro de execução ou DSC.

### <a name="textual-runbook-sample"></a>Amostra de livro textual

Os seguintes comandos de amostra mostram como usar a conta Run As mentioned anteriormente, para autenticar com os recursos do Gestor de Recursos Azure no seu livro de execução. Utiliza o ativo de ligação que representa a conta Run As, que faz referência ao principal de serviço baseado em certificados, e não credenciais.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount** é agora um pseudónimo para **Connect-AzureRMAccount**. Ao pesquisar os itens da sua biblioteca, se não vir **o Connect-AzureRMAccount,** pode utilizar **add-AzureRmAccount,** ou pode atualizar os seus módulos na sua Conta de Automação.

### <a name="graphical-runbook-samples"></a>Amostras gráficas de livro

Adicione uma atividade **get-automationConnection** a um livro de execução gráfico clicando corretamente na ligação no painel da **Biblioteca** do editor gráfico e selecionando **Adicionar à tela**.

![adicionar a tela](media/automation-connections/connection-add-canvas.png)

A imagem que se segue mostra um exemplo de utilização de uma ligação num livro gráfico. Este é o mesmo exemplo acima mostrado para autenticar usando a conta Run As com um livro textual. Este exemplo utiliza o conjunto de dados de **valor constante** para a atividade **get RunAs Connection** que utiliza um objeto de ligação para autenticação. Uma [ligação](automation-graphical-authoring-intro.md#links-and-workflow) de gasoduto é utilizada aqui, uma vez que o conjunto de parâmetros ServicePrincipalCertificate está à espera de um único objeto.

![obter ligações](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Amostra de livro python2

A amostra que se segue mostra como autenticar utilizando o Run As ligação num livro de execução Python2.

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

- Reveja [links na autoria gráfica](automation-graphical-authoring-intro.md#links-and-workflow) para entender como direcionar e controlar o fluxo de lógica nos seus livros de execução.

- Para saber mais sobre a utilização da Azure Automation de módulos PowerShell e as melhores práticas para criar os seus próprios módulos PowerShell para funcionar como Módulos de Integração dentro da Automação Azure, consulte Módulos de [Integração.](automation-integration-modules.md)