---
title: Atualizar modelos de Azure Analysis Services com a automação do Azure | Microsoft Docs
description: Este artigo descreve como codificar atualizações de modelo para Azure Analysis Services usando a automação do Azure.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a79123d57f80474e1871ef68f9a92ea9417089ac
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572359"
---
# <a name="refresh-with-azure-automation"></a>Atualizar com a Automatização do Azure

Usando a automação do Azure e os Runbooks do PowerShell, você pode executar operações automatizadas de atualização de dados em seus modelos de tabela de análise do Azure.  

O exemplo neste artigo usa os [módulos do SqlServer do PowerShell](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Um runbook do PowerShell de exemplo, que demonstra a atualização de um modelo é fornecido posteriormente neste artigo.  

## <a name="authentication"></a>Autenticação

Todas as chamadas devem ser autenticadas com um token de Azure Active Directory (OAuth 2) válido.  O exemplo neste artigo usará uma entidade de serviço (SPN) para autenticar para Azure Analysis Services.

Para saber mais sobre como criar uma entidade de serviço, consulte [criar uma entidade de serviço usando portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> O exemplo a seguir pressupõe que o firewall de Azure Analysis Services está desabilitado. Se o firewall estiver habilitado, o endereço IP público do iniciador de solicitação precisará estar na lista de permissões no firewall.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Instale os módulos do SqlServer da galeria do PowerShell.

1. Na sua conta de automação do Azure, clique em **módulos**e **procure Galeria**.

2. Na barra de pesquisa, pesquise **SqlServer**.

    ![Pesquisar módulos](./media/analysis-services-refresh-azure-automation/1.png)

3. Selecione SqlServer e clique em **importar**.
 
    ![Importar módulo](./media/analysis-services-refresh-azure-automation/2.png)

4. Clique em **OK**.
 
### <a name="create-a-service-principal-spn"></a>Criar uma entidade de serviço (SPN)

Para saber mais sobre como criar uma entidade de serviço, consulte [criar uma entidade de serviço usando portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Configurar permissões no Azure Analysis Services
 
A entidade de serviço que você criar deve ter permissões de administrador do servidor no servidor. Para saber mais, consulte [Adicionar uma entidade de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Projetar o runbook de automação do Azure

1. Na conta de automação, crie um recurso de **credenciais** que será usado para armazenar com segurança a entidade de serviço.

    ![Criar credencial](./media/analysis-services-refresh-azure-automation/6.png)

2. Insira os detalhes da credencial.  Para o **nome de usuário**, insira **o SPN ClientID**, para a **senha**, insira o **segredo SPN**.

    ![Criar credencial](./media/analysis-services-refresh-azure-automation/7.png)

3. Importar o runbook de automação

    ![Importar runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Procure o arquivo **Refresh-Model. ps1** , forneça um **nome** e uma **Descrição**e, em seguida, clique em **criar**.

    ![Importar runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. Quando o runbook tiver sido criado, ele entrará automaticamente no modo de edição.  Selecione **Publicar**.

    ![Publicar runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > O recurso de credencial que foi criado anteriormente é recuperado pelo runbook usando o comando **Get-AutomationPSCredential** .  Esse comando é passado para o comando **Invoke-ProcessASADatabase** do PowerShell para executar a autenticação para Azure Analysis Services.

6. Teste o runbook clicando em **Iniciar**.

    ![Iniciar o runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. Preencha os parâmetros **DatabaseName**, **ANALYSISSERVER**e **RefreshType** e, em seguida, clique em **OK**. O parâmetro **WEBHOOKDATA** não é necessário quando o runbook é executado manualmente.

    ![Iniciar o runbook](./media/analysis-services-refresh-azure-automation/12.png)

Se o runbook for executado com êxito, você receberá uma saída semelhante à seguinte:

![Execução bem-sucedida](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Usar um runbook autocontido da automação do Azure

O runbook pode ser configurado para disparar a atualização do modelo de Azure Analysis Services em uma base agendada.

Isso pode ser configurado da seguinte maneira:

1. No runbook de automação, clique em **agendas**e **adicione uma agenda**.
 
    ![Criar agenda](./media/analysis-services-refresh-azure-automation/14.png)

2. Clique em **agendar** > **criar uma nova agenda**e preencha os detalhes.

    ![Configurar agendamento](./media/analysis-services-refresh-azure-automation/15.png)

3. Clique em **Criar**.

4. Preencha os parâmetros para a agenda. Eles serão usados cada vez que o runbook for disparado. O parâmetro **WEBHOOKDATA** deve ser deixado em branco ao ser executado por meio de um agendamento.

    ![Configurar parâmetros](./media/analysis-services-refresh-azure-automation/16.png)

5. Clique em **OK**.

## <a name="consume-with-data-factory"></a>Consumir com Data Factory

Para consumir o runbook usando Azure Data Factory, primeiro crie um **webhook** para o runbook. O **webhook** fornecerá uma URL que pode ser chamada por meio de uma atividade da Web Azure data Factory.

> [!IMPORTANT]
> Para criar um **webhook**, o status do runbook deve ser **publicado**.

1. No runbook de automação, clique em **WebHooks**e, em seguida, clique em **Adicionar webhook**.

   ![Adicionar webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Dê um nome e uma expiração ao webhook.  O nome identifica apenas o webhook dentro do runbook de automação; ele não faz parte da URL.

   >[!CAUTION]
   >Certifique-se de copiar a URL antes de fechar o assistente, pois você não pode obtê-lo novamente depois de fechado.
    
   ![Configurar webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Os parâmetros para o webhook podem permanecer em branco.  Ao configurar a atividade da Web do Azure Data Factory, os parâmetros podem ser passados para o corpo da chamada da Web.

3. Em Data Factory, configure uma **atividade da Web**

### <a name="example"></a>Exemplo

   ![Exemplo de atividade da Web](./media/analysis-services-refresh-azure-automation/19.png)

A **URL** é a URL criada no webhook.

O **corpo** é um documento JSON que deve conter as seguintes propriedades:


|Propriedade  |Valor  |
|---------|---------|
|**AnalysisServicesDatabase**     |O nome do banco de dados Azure Analysis Services <br/> Exemplo: AdventureWorksDB         |
|**AnalysisServicesServer**     |O nome do servidor de Azure Analysis Services. <br/> Exemplo: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |O tipo de atualização a ser executada. <br/> Exemplo: completo         |

Exemplo de corpo JSON:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Esses parâmetros são definidos no script do PowerShell do runbook.  Quando a atividade da Web é executada, a carga JSON passada é WEBHOOKDATA.

Isso é desserializado e armazenado como parâmetros do PowerShell, que são usados pelo comando Invoke-ProcesASDatabase do PowerShell.

![Webhook desserializado](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Usar um Hybrid Worker com Azure Analysis Services

Uma máquina virtual do Azure com um endereço IP público estático pode ser usada como um Hybrid Worker de Automação do Azure.  Esse endereço IP público pode ser adicionado ao firewall do Azure Analysis Services.

> [!IMPORTANT]
> Verifique se o endereço IP público da máquina virtual está configurado como estático.
>
>Para saber mais sobre como configurar os trabalhos híbridos de automação do Azure, confira [automatizar recursos em seu datacenter ou nuvem usando Hybrid runbook Worker](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

Quando uma Hybrid Worker estiver configurada, crie um webhook conforme descrito na seção [consumir com data Factory](#consume-with-data-factory).  A única diferença aqui é selecionar a opção **executar no** > **Hybrid Worker** ao configurar o webhook.

Webhook de exemplo usando Hybrid Worker:

![Exemplo Hybrid Worker webhook](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Exemplo de runbook do PowerShell

O trecho de código a seguir é um exemplo de como executar a atualização do modelo de Azure Analysis Services usando um runbook do PowerShell.

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
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)
