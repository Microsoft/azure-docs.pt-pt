---
title: Gerir ligações na Azure Automation
description: Este artigo diz como gerir as ligações da Azure Automation a serviços ou aplicações externas e como trabalhar com eles em livros de recortes.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: 5f6494eb72084c683ddbb8b27a49acdb1fbc2bfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99054268"
---
# <a name="manage-connections-in-azure-automation"></a>Gerir ligações na Azure Automation

Um ativo de ligação Azure Automation contém as informações listadas abaixo. Estas informações são necessárias para a ligação a um serviço externo ou aplicação a partir de um livro de execução ou configuração DSC. 

* Informação necessária para a autenticação, como nome de utilizador e senha
* Informações de ligação, tais como URL ou porta

O ativo de ligação mantém todas as propriedades para a ligação a uma determinada aplicação, tornando desnecessário criar múltiplas variáveis. Pode editar os valores para uma ligação num só local e pode passar o nome de uma ligação a um livro de execução ou configuração DSC num único parâmetro. O livro de execução ou configuração acede às propriedades para uma ligação utilizando o `Get-AutomationConnection` cmdlet interno.

Quando criar uma ligação, deve especificar um tipo de ligação. O tipo de ligação é um modelo que define um conjunto de propriedades. Pode adicionar um tipo de ligação à Azure Automation utilizando um módulo de integração com um ficheiro de metadados. Também é possível criar um tipo de ligação utilizando a [API AZure Automation](/previous-versions/azure/reference/mt163818(v=azure.100)) se o módulo de integração incluir um tipo de ligação e for importado para a sua conta Demôm automação. 

>[!NOTE]
>Os ativos seguros na Azure Automation incluem credenciais, certificados, conexões e variáveis encriptadas. Estes ativos são encriptados e armazenados na Azure Automation utilizando uma chave única que é gerada para cada conta Dem automação. A Azure Automation armazena a chave no Key Vault gerido pelo sistema. Antes de armazenar um ativo seguro, a Automatização carrega a chave do Key Vault e utiliza-a para encriptar o ativo. 

## <a name="connection-types"></a>Tipos de ligação

A Azure Automation disponibiliza os seguintes tipos de ligação incorporados:

* `Azure` - Representa uma ligação usada para gerir recursos clássicos.
* `AzureServicePrincipal` - Representa uma ligação utilizada pela conta Azure Run As.
* `AzureClassicCertificate` - Representa uma ligação utilizada pela conta clássica Azure Run As.

Na maioria dos casos, não precisa de criar um recurso de ligação porque é criado quando cria uma [conta Run As](automation-security-overview.md).

## <a name="powershell-cmdlets-to-access-connections"></a>Cmdlets PowerShell para aceder a ligações

Os cmdlets na tabela seguinte criam e gerem as ligações automation com a PowerShell. Eles enviam como parte dos [módulos Az.](shared-resources/modules.md#az-modules)

|Cmdlet|Descrição|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection)|Recupera informações sobre uma ligação.|
|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)|Cria uma ligação nova.|
|[Remover-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection)|Remove uma ligação existente.|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue)|Define o valor de um determinado campo para uma ligação existente.|

## <a name="internal-cmdlets-to-access-connections"></a>Cmdlets internos para aceder a ligações

O cmdlet interno na tabela seguinte é utilizado para aceder a ligações nos seus runbooks e configurações DSC. Este cmdlet vem com o módulo `Orchestrator.AssetManagement.Cmdlets` global. Para obter mais informações, consulte [as cmdlets internas.](shared-resources/modules.md#internal-cmdlets)

|Cmdlet Interno|Description|
|---|---|
|`Get-AutomationConnection` | Recupera os valores dos diferentes campos na ligação e devolve-os como um [haxixe.](/powershell/module/microsoft.powershell.core/about/about_hash_tables) Em seguida, pode utilizar este haxixe com os comandos apropriados na configuração do runbook ou DSC.|

>[!NOTE]
>Evite utilizar variáveis com o `Name` parâmetro de `Get-AutomationConnection` . A utilização de variáveis neste caso pode complicar a descoberta de dependências entre runbooks ou configurações DSC e ativos de conexão no momento do design.

## <a name="python-functions-to-access-connections"></a>Funções python para aceder a conexões

A função na tabela a seguir é utilizada para aceder a ligações num livro de bordo Python 2 e 3. Os livros de 100 python estão atualmente em pré-visualização.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_connection` | Recupera uma ligação. Devolve um dicionário com as propriedades da ligação. |

> [!NOTE]
> Deve importar o `automationassets` módulo no topo do seu livro de bordo Python para aceder às funções do ativo.

## <a name="create-a-new-connection"></a>Criar uma nova ligação

### <a name="create-a-new-connection-with-the-azure-portal"></a>Criar uma nova ligação com o portal Azure

Para criar uma nova ligação no portal Azure:

1. A partir da sua conta de Automação, clique em Conexões em **Recursos Partilhados.** 
2. Clique **+ Adicionar uma ligação** na página 'Ligações'.
4. No campo **Tipo** no painel De Nova Ligação, selecione o tipo de ligação a criar. As tuas escolhas `Azure` `AzureServicePrincipal` são, `AzureClassicCertificate` e. 
5. O formulário apresenta propriedades para o tipo de ligação que escolheu. Preencha o formulário e clique em **Criar** para salvar a nova ligação.

### <a name="create-a-new-connection-with-windows-powershell"></a>Criar uma nova ligação com o Windows PowerShell

Crie uma nova ligação com o Windows PowerShell utilizando o `New-AzAutomationConnection` cmdlet. Este cmdlet tem um `ConnectionFieldValues` parâmetro que espera um valore de definição de haxixe para cada uma das propriedades definidas pelo tipo de ligação.

Pode utilizar os seguintes comandos de exemplo como alternativa à criação da conta Run As a partir do portal para criar um novo ativo de ligação.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Ao criar a sua conta Automation, inclui vários módulos globais por padrão, juntamente com o tipo de ligação `AzureServicePrincipal` para criar o ativo de `AzureRunAsConnection` ligação. Se tentar criar um novo ativo de ligação para ligar a um serviço ou aplicação com um método de autenticação diferente, a operação falha porque o tipo de ligação ainda não está definido na sua conta Dem automação. Para obter mais informações sobre a criação do seu próprio tipo de ligação para um módulo personalizado, consulte [Adicionar um tipo de ligação](#add-a-connection-type).

## <a name="add-a-connection-type"></a>Adicionar um tipo de ligação

Se o seu runbook ou configuração DSC se ligar a um serviço externo, deve definir um tipo de ligação num [módulo personalizado](shared-resources/modules.md#custom-modules) chamado módulo de integração. Este módulo inclui um ficheiro de metadados que especifica propriedades do tipo de ligação e é nomeado **&lt; MóduloName &gt;-Automation.js,** localizado na pasta do módulo do seu ficheiro **de .zip** comprimido. Este ficheiro contém os campos de uma ligação que é necessária para ligar ao sistema ou serviço que o módulo representa. Utilizando este ficheiro, pode definir os nomes de campo, tipos de dados, estado de encriptação e estado opcional para o tipo de ligação. 

O exemplo a seguir é um modelo no formato de ficheiro **.json** que define o nome de utilizador e as propriedades de senha para um tipo de ligação personalizado chamado `MyModuleConnection` :

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

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Obtenha uma ligação em um runbook ou configuração DSC

Recupere uma ligação num livro de bordo ou na configuração DSC com o `Get-AutomationConnection` cmdlet interno. Este cmdlet é preferido sobre o `Get-AzAutomationConnection` cmdlet, uma vez que recupera os valores de ligação em vez de informações sobre a ligação.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

O exemplo a seguir mostra como utilizar a conta Run As para autenticar com os recursos do Azure Resource Manager no seu runbook. Utiliza um ativo de ligação que representa a conta Run As, que faz referência ao principal do serviço baseado em certificados.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

# <a name="python"></a>[Python](#tab/python2)

O exemplo a seguir mostra como autenticar usando a ligação Run As num livro de bordo Python 2 e 3.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resource manager """
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

---

### <a name="graphical-runbook-examples"></a>Exemplos gráficos de runbook

Pode adicionar uma atividade para o `Get-AutomationConnection` cmdlet interno a um runbook gráfico. Clique com o botão direito na ligação no painel da Biblioteca do editor gráfico e **selecione Adicionar à tela**.

![adicionar a tela](media/automation-connections/connection-add-canvas.png)

A imagem a seguir mostra um exemplo de utilização de um objeto de ligação num livro de bordo gráfico. Este exemplo utiliza o `Constant value` conjunto de dados para a `Get RunAs Connection` atividade, que utiliza um objeto de ligação para autenticação. Uma [ligação de gasoduto](automation-graphical-authoring-intro.md#use-links-for-workflow) é usada aqui, uma vez que o conjunto de `ServicePrincipalCertificate` parâmetros está à espera de um único objeto.

![obter ligações](media/automation-connections/automation-get-connection-object.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os cmdlets utilizados para aceder às ligações, consulte [Gerir módulos na Azure Automation](shared-resources/modules.md).
* Para obter informações gerais sobre os runbooks, consulte [a execução do Runbook na Azure Automation](automation-runbook-execution.md).
* Para obter detalhes sobre as configurações do DSC, consulte a [visão geral da configuração do estado](automation-dsc-overview.md).