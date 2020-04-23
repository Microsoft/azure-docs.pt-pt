---
title: Gerir ligações na Automação Azure
description: Os ativos de ligação na Automatização Azure contêm as informações necessárias para se ligarem a um serviço ou aplicação externo a partir de um livro de execução ou de uma configuração DSC. Este artigo explica os detalhes das ligações e como trabalhar com eles tanto na autoria textual como gráfica.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 39a41a60f4cabe995ebd458c4b906438d1e31bde
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097120"
---
# <a name="manage-connections-in-azure-automation"></a>Gerir ligações na Automação Azure

Um ativo de ligação automation contém as informações necessárias para se ligar a um serviço ou aplicação externo a partir de um livro de execução ou de uma configuração DSC. Isto pode incluir informações necessárias para a autenticação, tais como um nome de utilizador e uma senha, além de informações de ligação, como um URL ou uma porta. O valor de uma ligação é manter todas as propriedades para ligar a uma determinada aplicação em um ativo em oposição à criação de múltiplas variáveis. O utilizador pode editar os valores para uma ligação num só local, e pode passar o nome de uma ligação a um livro de execução ou configuração DSC num único parâmetro. As propriedades para uma ligação podem ser acedidas na `Get-AutomationConnection` configuração do livro de execução ou DSC com a atividade.

Quando cria uma ligação, deve especificar um tipo de ligação. O tipo de ligação é um modelo que define um conjunto de propriedades. A ligação define valores para cada imóvel definido no seu tipo de ligação. Os tipos de ligação são adicionados à Automação Azure em módulos de integração ou criados com a API de [Automação Azure](/previous-versions/azure/reference/mt163818(v=azure.100)) se o módulo de integração incluir um tipo de ligação e for importado para a sua conta De automação. Caso contrário, deve criar um ficheiro de metadados para especificar um tipo de ligação automation. Para mais informações sobre este aspeto, consulte Módulos de [Integração](automation-integration-modules.md).

>[!NOTE]
>Os ativos seguros na Automatização Azure incluem credenciais, certificados, ligações e variáveis encriptadas. Estes ativos são encriptados e armazenados na Automatização Azure utilizando uma chave única que é gerada para cada conta De Automação. Esta chave está armazenada num cofre gerido pelo sistema. Antes de armazenar um ativo seguro, a chave é carregada a partir do Cofre chave e depois usada para encriptar o ativo. Este processo é gerido pela Azure Automation.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="connection-types"></a>Tipos de ligação

Existem três tipos de ligações incorporadas disponíveis na Automação Azure:

* **Azure** - Esta ligação pode ser usada para gerir recursos clássicos.
* **AzureClassicCertificate** - Esta ligação é utilizada pela conta **AzureClassicRunAs.**
* **AzureServicePrincipal** - Esta ligação é utilizada pela conta **AzureRunAs.**

Na maioria dos casos, não é necessário criar um recurso de ligação porque é criado quando cria uma [conta Run As](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Cmdlets do Windows PowerShell

Os cmdlets na tabela seguinte são utilizados para criar e gerir ligações de Automação com o Windows PowerShell. Eles enviam como parte do [módulo Azure PowerShell](/powershell/azure/overview), que está disponível para uso em livros de automação e configurações DSC.

|Cmdlet|Descrição|
|---|---|
|[Get-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Recupera uma ligação. Inclui um hashtable com os valores dos campos de ligação.|
|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Cria uma ligação nova.|
|[Remover-AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Remove uma ligação existente.|
|[Set-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Define o valor de um determinado campo para uma ligação existente.|

## <a name="activities"></a>Atividades

As atividades na tabela seguinte são utilizadas para aceder a ligações numa configuração de livro de execução ou DSC.

|Atividades|Descrição|
|---|---|
|`Get-AutomationConnection` | Tem uma ligação para usar. Devolve um hashtable com as propriedades da ligação.|

>[!NOTE]
>Evite utilizar variáveis `Name` com `Get-AutomationConnection`o parâmetro de . A utilização deste parâmetro pode complicar a descoberta de dependências entre os livros de execução ou as configurações de DSC e os ativos de ligação no momento do projeto.

## <a name="python-2-functions"></a>Funções Python 2

A função na tabela a seguir é utilizada para aceder a ligações num livro de execução Python 2.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_connection` | Recupera uma ligação. Devolve um dicionário com as propriedades da ligação. |

> [!NOTE]
> Tem de `automationassets` importar o módulo no topo do seu livro de execução Python para aceder às funções de ativo.

## <a name="creating-a-new-connection"></a>Criar uma nova ligação

### <a name="create-a-new-connection-with-the-azure-portal"></a>Criar uma nova ligação com o portal Azure

1. Na sua conta De automação, clique na parte **Do Ativo** para abrir a lâmina **Do Ativo.**
2. Clique na parte **Ligações** para abrir a lâmina de **Ligações.**
3. Clique **Em adicionar uma ligação** na parte superior da lâmina.
4. No **dropdown do Tipo,** selecione o tipo de ligação que pretende criar. O formulário apresentará as propriedades para esse tipo em particular.
5. Complete o formulário e clique **em Criar** para salvar a nova ligação.

### <a name="create-a-new-connection-with-windows-powershell"></a>Criar uma nova ligação com o Windows PowerShell

Crie uma nova ligação `New-AzAutomationConnection` com o Windows PowerShell utilizando o cmdlet. Este cmdlet tem um `ConnectionFieldValues` parâmetro nomeado que espera um valore [sinuoso](https://technet.microsoft.com/library/hh847780.aspx) definindo valores para cada uma das propriedades definidas pelo tipo de ligação.

Pode utilizar os seguintes comandos de exemplo como alternativa à criação da conta Run As a partir do portal para criar um novo ativo de ligação.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Ao criar a sua conta De automatização, inclui vários `AzureServicePrincipal` módulos `AzureRunAsConnection` globais por padrão, juntamente com o tipo de ligação para criar o ativo de ligação. Se tentar criar um novo ativo de ligação para ligar a um serviço ou aplicação com um método de autenticação diferente, a operação falha porque o tipo de ligação ainda não está definido na sua conta Automation. Para obter mais informações sobre a criação do seu próprio tipo de ligação para um módulo personalizado [da PowerShell Gallery,](https://www.powershellgallery.com) consulte módulos de [Integração.](automation-integration-modules.md)

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Utilizando uma ligação numa configuração de livro de execução ou DSC

Recupere uma ligação numa configuração de `Get-AutomationConnection` rés-do-guia ou DSC com o cmdlet. Não pode usar `Get-AzAutomationConnection` a atividade. Esta atividade recupera os valores dos diferentes campos na ligação e devolve-os como um [hashtable](https://go.microsoft.com/fwlink/?LinkID=324844). Este hashtable pode então ser utilizado com os comandos apropriados na configuração do livro de execução ou DSC.

### <a name="textual-runbook-sample"></a>Amostra de livro textual

Os seguintes comandos de amostra mostram como usar a conta Run As mentioned anteriormente, para autenticar com os recursos do Gestor de Recursos Azure no seu livro de execução. Utiliza o ativo de ligação que representa a conta Run As, que faz referência ao principal de serviço baseado em certificados, e não credenciais.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> Para livros de execução powerShell não gráficos, `Add-AzAccount` e `Add-AzureRMAccount` são pseudónimos de [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Pode utilizar estes cmdlets ou pode [atualizar os seus módulos](automation-update-azure-modules.md) na sua conta Automation para as versões mais recentes. Pode precisar de atualizar os seus módulos mesmo que tenha acabado de criar uma nova conta Automation.

### <a name="graphical-runbook-samples"></a>Amostras gráficas de livro

Adicione uma `Get-AutomationConnection` atividade a um livro de execução gráfico clicando na ligação no painel da Biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![adicionar a tela](media/automation-connections/connection-add-canvas.png)

A imagem que se segue mostra um exemplo de utilização de uma ligação num livro gráfico. Este é o mesmo exemplo acima indicado para autenticar usando a conta Run As com um livro textual. Este exemplo `Constant value` utiliza o `Get RunAs Connection` conjunto de dados para a atividade que utiliza um objeto de ligação para autenticação. Uma [ligação](automation-graphical-authoring-intro.md#links-and-workflow) de gasoduto `ServicePrincipalCertificate` é utilizada aqui, uma vez que o conjunto de parâmetros está à espera de um único objeto.

![obter ligações](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Amostra de livro python 2

A amostra que se segue mostra como autenticar utilizando o Run As ligação num livro de execução Python 2.

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
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Para saber mais sobre a utilização da Azure Automation de módulos PowerShell e as melhores práticas para criar os seus próprios módulos PowerShell para funcionar como Módulos de Integração dentro da Automação Azure, consulte Módulos de [Integração.](automation-integration-modules.md)