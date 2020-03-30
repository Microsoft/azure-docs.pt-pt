---
title: Refresh Azure Analysis Services modelos com Automação Azure [ Microsoft Docs
description: Este artigo descreve como codificar atualizações de modelos para serviços de análise azure utilizando a Automação Azure.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a79123d57f80474e1871ef68f9a92ea9417089ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572359"
---
# <a name="refresh-with-azure-automation"></a>Atualizar com a Automatização do Azure

Ao utilizar os livros de automação azure e powershell, pode realizar operações automatizadas de atualização de dados nos seus modelos tabular De Análise Azure.  

O exemplo neste artigo utiliza os [módulos PowerShell SqlServer](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Uma amostra PowerShell Runbook, que demonstra refrescar um modelo é fornecida mais tarde neste artigo.  

## <a name="authentication"></a>Autenticação

Todas as chamadas devem ser autenticadas com um símbolo de Diretório Ativo Azure (OAuth 2) válido.  O exemplo deste artigo utilizará um Diretor de Serviço (SPN) para autenticar os Serviços de Análise Azure.

Para saber mais sobre a criação de um Diretor de Serviço, consulte Criar um diretor de [serviço utilizando o portal Azure.](../active-directory/develop/howto-create-service-principal-portal.md)

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> O exemplo que se segue pressupõe que a firewall dos Serviços de Análise Azure está desativada. Se a firewall estiver ativada, o endereço IP público do iniciador de pedido terá de ser listado na firewall.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Instale módulos SqlServer na galeria PowerShell.

1. Na sua Conta de Automação Azure, clique **em Módulos,** em **seguida, navegue na galeria**.

2. Na barra de pesquisa, procure o **SqlServer**.

    ![Módulos de Pesquisa](./media/analysis-services-refresh-azure-automation/1.png)

3. Selecione SqlServer e, em seguida, clique **em Importar**.
 
    ![Módulo de Importação](./media/analysis-services-refresh-azure-automation/2.png)

4. Clique em **OK**.
 
### <a name="create-a-service-principal-spn"></a>Criar um Diretor de Serviço (SPN)

Para aprender sobre a criação de um Diretor de Serviço, consulte Criar um diretor de [serviço utilizando o portal Azure](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Configure permissões nos Serviços de Análise Do Azure
 
O Diretor de Serviço que cria deve ter permissões de administrador de servidor no servidor. Para saber mais, consulte [Adicionar um principal de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Desenhe o Livro de Automação Azure

1. Na Conta de Automação, crie um recurso **credenciais** que será utilizado para armazenar de forma segura o Diretor de Serviço.

    ![Criar credenciais](./media/analysis-services-refresh-azure-automation/6.png)

2. Introduza os detalhes para a credencial.  Para o **nome utilizador**, introduza o **ClienteId SPN,** para a **Palavra-Passe,** introduza o **Segredo SPN**.

    ![Criar credenciais](./media/analysis-services-refresh-azure-automation/7.png)

3. Importar o Livro de Execução da Automação

    ![Livro de Recorridos de Importação](./media/analysis-services-refresh-azure-automation/8.png)

4. Procure o ficheiro **Refresh-Model.ps1,** forneça um **Nome** e **descrição,** e clique em **Criar**.

    ![Livro de Recorridos de Importação](./media/analysis-services-refresh-azure-automation/9.png)

5. Quando o Livro de Execução for criado, entrará automaticamente no modo de edição.  Selecione **Publicar**.

    ![Publicar Livro de Execução](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > O recurso credencial que foi criado anteriormente é recuperado pelo livro de execução utilizando o comando **Get-AutomationPSCredential.**  Este comando é então passado para o comando **Invoca-ProcessASADatabase** PowerShell para realizar a autenticação aos Serviços de Análise Azure.

6. Teste o livro de execução clicando **em Iniciar**.

    ![Inicie o Livro de Corridas](./media/analysis-services-refresh-azure-automation/11.png)

7. Preencha os parâmetros **DATABASENAME,** **ANALYSISSERVER**e **REFRESHTYPE** e, em seguida, clique **ok**. O parâmetro **WEBHOOKDATA** não é necessário quando o Livro de Execução é executado manualmente.

    ![Inicie o Livro de Corridas](./media/analysis-services-refresh-azure-automation/12.png)

Se o Livro de Execução for executado com sucesso, receberá uma saída como a seguinte:

![Corrida bem sucedida](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Use um Livro de Execução de Automação Azure autossuficiente

O Livro de Execução pode ser configurado para desencadear a atualização do modelo azure Analysis Services numa base programada.

Isto pode ser configurado da seguinte forma:

1. No Livro de Execução de Automatização, clique em **Horários,** em **seguida, adicionar um Horário**.
 
    ![Criar horários](./media/analysis-services-refresh-azure-automation/14.png)

2. Clique em **Agenda** > **Criar um novo horário**e, em seguida, preencher os detalhes.

    ![Configurar horários](./media/analysis-services-refresh-azure-automation/15.png)

3. Clique em **Criar**.

4. Preencha os parâmetros para o horário. Estes serão usados cada vez que o Livro de Execução dispara. O parâmetro **WEBHOOKDATA** deve ficar em branco quando estiver em funcionamento através de um horário.

    ![Configurar parâmetros](./media/analysis-services-refresh-azure-automation/16.png)

5. Clique em **OK**.

## <a name="consume-with-data-factory"></a>Consumir com Fábrica de Dados

Para consumir o livro de corridas utilizando a Azure Data Factory, crie primeiro um **Webhook** para o livro de execução. O **Webhook** fornecerá um URL que pode ser chamado através de uma atividade web da Azure Data Factory.

> [!IMPORTANT]
> Para criar um **Webhook,** o estado do Livro de Execução deve ser **publicado**.

1. No seu Manual de Execução de Automação, clique em **Webhooks**, e, em seguida, clique em **Adicionar Webhook**.

   ![Adicionar Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Dê ao Webhook um nome e uma caducidade.  O nome apenas identifica o Webhook dentro do Manual de Automação, não faz parte do URL.

   >[!CAUTION]
   >Certifique-se de copiar o URL antes de fechar o assistente, uma vez que não pode recuperá-lo uma vez fechado.
    
   ![Configure Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Os parâmetros para o gancho de teia podem permanecer em branco.  Ao configurar a atividade web da Azure Data Factory, os parâmetros podem ser passados para o corpo da chamada web.

3. Na Fábrica de Dados, configure uma **atividade web**

### <a name="example"></a>Exemplo

   ![Exemplo atividade web](./media/analysis-services-refresh-azure-automation/19.png)

O **URL** é o URL criado a partir do Webhook.

O **corpo** é um documento JSON que deve conter as seguintes propriedades:


|Propriedade  |Valor  |
|---------|---------|
|**AnáliseServiçosBase de Dados**     |O nome da base de dados dos Serviços de Análise Azure <br/> Exemplo: AdventureWorksDB         |
|**Serviços de AnáliseServidor**     |O nome do servidor dos Serviços de Análise Azure. <br/> Exemplo: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**Tipo de atualização de base de dados**     |O tipo de refrescamento para executar. <br/> Exemplo: Cheio         |

Exemplo corpo JSON:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Estes parâmetros são definidos no script PowerShell do livro de execução.  Quando a atividade web é executada, a carga útil JSON passada é WEBHOOKDATA.

Isto é desserializado e armazenado como parâmetros PowerShell, que são então utilizados pelo comando Invoke-ProcesASDatabase PowerShell.

![Webhook desserializado](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Utilize um trabalhador híbrido com serviços de análise azure

Uma Máquina Virtual Azure com um endereço IP público estático pode ser usada como um Trabalhador Híbrido de Automação Azure.  Este endereço IP público pode então ser adicionado à firewall dos Serviços de Análise do Azure.

> [!IMPORTANT]
> Certifique-se de que o endereço IP público da Máquina Virtual está configurado como estático.
>
>Para saber mais sobre a configuração dos Trabalhadores Híbridos da Automação Azure, consulte [os recursos automate no seu datacenter ou nuvem utilizando o Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

Uma vez configurado um Trabalhador Híbrido, crie um Webhook conforme descrito na secção [Consumir com Fábrica](#consume-with-data-factory)de Dados .  A única diferença aqui é selecionar a opção **Executar no** > **Trabalhador Híbrido** ao configurar o Webhook.

Exemplo webhook usando Trabalhador Híbrido:

![Exemplo, Webhook trabalhador híbrido](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Livro de execução da PowerShell da amostra

O seguinte código é um exemplo de como executar o modelo Deserviços de Análise Azure utilizando um PowerShell Runbook.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Passos seguintes

[Amostras](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
