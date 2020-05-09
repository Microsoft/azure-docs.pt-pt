---
title: Gerir ligações na Automação Azure
description: Os ativos de ligação na Automatização Azure contêm as informações necessárias para se ligarem a um serviço ou aplicação externo a partir de um livro de execução ou de uma configuração DSC. Este artigo explica os detalhes das ligações e como trabalhar com eles tanto na autoria textual como gráfica.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: 1a50c5d3d6e068054cfc8381b220d38471a7eb6c
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996580"
---
# <a name="manage-connections-in-azure-automation"></a>Gerir ligações na Automação Azure

Um ativo de ligação à Automação Azure contém as informações listadas abaixo. Estas informações são necessárias para a ligação a um serviço ou aplicação externo a partir de um livro de execução ou de uma configuração DSC. 

* Informações necessárias para a autenticação, tais como nome de utilizador e senha
* Informações de ligação, tais como URL ou porta

O ativo de ligação mantém todas as propriedades para a ligação a uma determinada aplicação, tornando desnecessário criar várias variáveis. Pode editar os valores para uma ligação num só local, e pode passar o nome de uma ligação a um livro de execução ou configuração DSC num único parâmetro. O livro de execução ou configuração acede às propriedades para uma ligação utilizando o cmdlet interno. `Get-AutomationConnection`

Quando cria uma ligação, deve especificar um tipo de ligação. O tipo de ligação é um modelo que define um conjunto de propriedades. Pode adicionar um tipo de ligação à Automatização Azure utilizando um módulo de integração com um ficheiro de metadados. Também é possível criar um tipo de ligação utilizando a API de [Automação Azure](/previous-versions/azure/reference/mt163818(v=azure.100)) se o módulo de integração incluir um tipo de ligação e for importado para a sua conta Deautomação. 

>[!NOTE]
>Os ativos seguros na Automatização Azure incluem credenciais, certificados, ligações e variáveis encriptadas. Estes ativos são encriptados e armazenados na Automatização Azure utilizando uma chave única que é gerada para cada conta De Automação. A Azure Automation armazena a chave no Cofre chave gerido pelo sistema. Antes de armazenar um ativo seguro, a Automation carrega a chave a partir do Key Vault e depois usa-a para encriptar o ativo. 

## <a name="connection-types"></a>Tipos de ligação

A Azure Automation disponibiliza os seguintes tipos de ligação incorporados:

* `Azure`- Representa uma ligação usada para gerir recursos clássicos.
* `AzureServicePrincipal`- Representa uma ligação utilizada pela conta Azure Run As.
* `AzureClassicCertificate`- Representa uma ligação utilizada pelo clássico Azure Run Como conta.

Na maioria dos casos, não é necessário criar um recurso de ligação porque é criado quando cria uma [conta Run As](manage-runas-account.md).

## <a name="powershell-cmdlets-to-access-connections"></a>PowerShell cmdlets para aceder a ligações

Os cmdlets na tabela a seguir criam e gerem as ligações de Automação com a PowerShell. Eles enviam como parte dos [módulos Az.](shared-resources/modules.md#az-modules)

|Cmdlet|Descrição|
|---|---|
|[Get-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Recupera informações sobre uma ligação.|
|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Cria uma ligação nova.|
|[Remover-AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Remove uma ligação existente.|
|[Set-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Define o valor de um determinado campo para uma ligação existente.|

## <a name="internal-cmdlets-to-access-connections"></a>Cmdlets internos para aceder a ligações

O cmdlet interno na tabela seguinte é utilizado para aceder a ligações nos seus livros de execução e configurações DSC. Este cmdlet vem com `Orchestrator.AssetManagement.Cmdlets`o módulo global. Para mais informações, consulte [cmdlets internos](shared-resources/modules.md#internal-cmdlets).

|Cmdlet Interno|Descrição|
|---|---|
|`Get-AutomationConnection` | Recupera os valores dos diferentes campos na ligação e devolve-os como um [hashtable](https://go.microsoft.com/fwlink/?LinkID=324844). Em seguida, pode utilizar este hashtable com os comandos apropriados na configuração do livro de execução ou DSC.|

>[!NOTE]
>Evite utilizar variáveis `Name` com `Get-AutomationConnection`o parâmetro de . A utilização de variáveis neste caso pode complicar a descoberta de dependências entre os livros de execução ou as configurações de DSC e os ativos de conexão no momento do projeto.

## <a name="python-2-functions-to-access-connections"></a>Funções Python 2 para aceder a ligações

A função na tabela a seguir é utilizada para aceder a ligações num livro de execução Python 2.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_connection` | Recupera uma ligação. Devolve um dicionário com as propriedades da ligação. |

> [!NOTE]
> Tem de `automationassets` importar o módulo no topo do seu livro de execução Python para aceder às funções de ativo.

## <a name="create-a-new-connection"></a>Criar uma nova ligação

### <a name="create-a-new-connection-with-the-azure-portal"></a>Criar uma nova ligação com o portal Azure

Para criar uma nova ligação no portal Azure:

1. Na sua conta de Automação, clique em **Ligações** em **Recursos Partilhados**.
2. Clique **+ Adicione uma ligação** na página Ligações.
4. No campo **Tipo** no painel new connection, selecione o tipo de ligação para criar. As tuas `Azure` `AzureServicePrincipal`escolhas `AzureClassicCertificate`são, e. 
5. O formulário apresenta propriedades para o tipo de ligação que escolheu. Complete o formulário e clique **em Criar** para salvar a nova ligação.

### <a name="create-a-new-connection-with-windows-powershell"></a>Criar uma nova ligação com o Windows PowerShell

Crie uma nova ligação `New-AzAutomationConnection` com o Windows PowerShell utilizando o cmdlet. Este cmdlet `ConnectionFieldValues` tem um parâmetro que espera um valore sinuoso definindo valores para cada uma das propriedades definidas pelo tipo de ligação.

Pode utilizar os seguintes comandos de exemplo como alternativa à criação da conta Run As a partir do portal para criar um novo ativo de ligação.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Ao criar a sua conta De automatização, inclui vários `AzureServicePrincipal` módulos `AzureRunAsConnection` globais por padrão, juntamente com o tipo de ligação para criar o ativo de ligação. Se tentar criar um novo ativo de ligação para ligar a um serviço ou aplicação com um método de autenticação diferente, a operação falha porque o tipo de ligação ainda não está definido na sua conta Automation. Para obter mais informações sobre a criação do seu próprio tipo de ligação para um módulo personalizado, consulte [Adicionar um tipo de ligação](#add-a-connection-type).

## <a name="add-a-connection-type"></a>Adicione um tipo de ligação

Se a configuração do seu livro de execução ou DSC se ligar a um serviço externo, deve definir um tipo de ligação num [módulo personalizado](shared-resources/modules.md#custom-modules) chamado módulo de integração. Este módulo inclui um ficheiro de metadados que especifica as propriedades do tipo de ligação e é nomeado ** &lt;MóduloName&gt;-Automation.json**, localizado na pasta do módulo do seu ficheiro **.zip** comprimido. Este ficheiro contém os campos de uma ligação que são necessárias para ligar ao sistema ou serviço que o módulo representa. Utilizando este ficheiro, pode definir os nomes de campo, tipos de dados, estado de encriptação e estado opcional para o tipo de ligação. 

O exemplo seguinte é um modelo no formato de ficheiro **.json** que `MyModuleConnection`define o nome do utilizador e as propriedades da palavra-passe para um tipo de ligação personalizado chamado:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Obtenha uma ligação numa configuração de livro de execução ou DSC

Recupere uma ligação numa configuração de `Get-AutomationConnection` rés-do-guia ou DSC com o cmdlet interno. Este cmdlet é `Get-AzAutomationConnection` preferido sobre o cmdlet, uma vez que recupera os valores de ligação em vez de informações sobre a ligação. 

### <a name="textual-runbook-example"></a>Exemplo de livro textual

O exemplo que se segue mostra como usar a conta Run As para autenticar com os recursos do Gestor de Recursos Azure no seu livro de execução. Utiliza um ativo de ligação que representa a conta Run As, que faz referência ao diretor de serviço baseado em certificados.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

### <a name="graphical-runbook-examples"></a>Exemplos de livro gráfico

Pode adicionar uma atividade `Get-AutomationConnection` para o cmdlet interno a um livro gráfico. Clique na ligação no painel da Biblioteca do editor gráfico e selecione **Adicionar à tela**.

![adicionar a tela](media/automation-connections/connection-add-canvas.png)

A imagem que se segue mostra um exemplo de utilização de um objeto de ligação num livro gráfico. Este exemplo `Constant value` utiliza o `Get RunAs Connection` conjunto de dados para a atividade, que utiliza um objeto de ligação para autenticação. Uma [ligação](automation-graphical-authoring-intro.md#links-and-workflow) de gasoduto `ServicePrincipalCertificate` é utilizada aqui, uma vez que o conjunto de parâmetros está à espera de um único objeto.

![obter ligações](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-example"></a>Exemplo de livro de python 2

O exemplo que se segue mostra como autenticar usando o Run As ligação num livro de execução Python 2.

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

* Para saber mais sobre os cmdlets utilizados para aceder a ligações, consulte [Gerir módulos em Automação Azure](shared-resources/modules.md).
* Para obter informações gerais sobre livros de execução, consulte a execução do Livro de [Corridas na Automação Azure.](automation-runbook-execution.md)
* Para mais detalhes sobre as configurações do DSC, consulte a visão geral da [Configuração do Estado](automation-dsc-overview.md).