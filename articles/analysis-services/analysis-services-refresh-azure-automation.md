---
title: Atualizar modelos de Serviços de Análise Azure com Azure Automation Microsoft Docs
description: Este artigo descreve como codificar atualizações de modelos para serviços de análise Azure utilizando a Azure Automation.
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: chlound
ms.openlocfilehash: 7c801511b6f24cf5ef04d55bb195e3a4c62d7b6d
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491252"
---
# <a name="refresh-with-azure-automation"></a>Atualizar com a Automatização do Azure

Utilizando a Azure Automation e PowerShell Runbooks, pode realizar operações automatizadas de atualização de dados nos seus modelos tabulares Azure Analysis.  

O exemplo neste artigo utiliza o [módulo SqlServer PowerShell](/powershell/module/sqlserver/?view=sqlserver-ps&preserve-view=true). Uma amostra powerShell Runbook, que demonstra que refrescar um modelo é fornecido mais tarde neste artigo.  

## <a name="authentication"></a>Autenticação

Todas as chamadas devem ser autenticadas com um token Azure Ative (OAuth 2) válido.  O exemplo neste artigo utiliza um Principal de Serviço (SPN) para autenticar os Serviços de Análise Azure. Para saber mais, consulte [Criar um principal de serviço utilizando o portal Azure.](../active-directory/develop/howto-create-service-principal-portal.md)

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> O exemplo a seguir pressupõe que a firewall dos Serviços de Análise Azure está desativada. Se uma firewall estiver ativada, o endereço IP público do iniciador de pedidos deve ser incluído numa regra de firewall.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Instale os módulos SqlServer na galeria PowerShell.

1. Na sua Conta de Automação Azure, clique em **Módulos,** em **seguida, navegue na galeria**.

2. Na barra de pesquisa, procure **sqlServer.**

    ![Módulos de Pesquisa](./media/analysis-services-refresh-azure-automation/1.png)

3. Selecione SqlServer e, em seguida, clique em **Importar**.
 
    ![Módulo de Importação](./media/analysis-services-refresh-azure-automation/2.png)

4. Clique em **OK**.
 
### <a name="create-a-service-principal-spn"></a>Criar um diretor de serviço (SPN)

Para saber mais sobre a criação de um Diretor de Serviços, consulte [Criar um principal de serviço utilizando o portal Azure.](../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="configure-permissions-in-azure-analysis-services"></a>Configure permissões em Serviços de Análise Azure
 
O Diretor de Serviço que cria deve ter permissões de administrador de servidores no servidor. Para saber mais, consulte [Adicionar um principal de serviço à função de administrador do servidor](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Desenhe o Runbook Azure Automation

1. Na Conta Dem automação, crie um recurso **de Credenciais** que será utilizado para armazenar de forma segura o Diretor de Serviço.

    ![Screenshot que mostra a página "Credenciais" com a ação "Adicionar uma credencial" selecionada.](./media/analysis-services-refresh-azure-automation/6.png)

2. Insira os detalhes para a credencial. Em **Nome do Utilizador,** insira o ID principal do serviço (appid) e, em seguida, em **Password,** insira o serviço principal Secret.

    ![Criar credencial](./media/analysis-services-refresh-azure-automation/7.png)

3. Importe o Livro de Execução da Automação.

    ![Screenshot que mostra a página "Runbooks" com a ação "Importer um runbook" selecionada.](./media/analysis-services-refresh-azure-automation/8.png)

4. Navegue pelo ficheiro [Refresh-Model.ps1,](#sample-powershell-runbook) forneça um **Nome** e **Descrição** e, em seguida, clique em **Criar**.

    > [!NOTE]
    > Utilize o script da secção [Sample Powershell Runbook](#sample-powershell-runbook) na parte inferior deste documento para criar um ficheiro chamado Refresh-Model.ps1 e guardar para a máquina local para importar em Runbook.

    ![Livro de importação](./media/analysis-services-refresh-azure-automation/9.png)

5. Quando o Runbook tiver sido criado, entrará automaticamente no modo de edição.  Selecione **Publicar**.

    ![Publicar Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > O recurso credencial que foi criado anteriormente é recuperado pelo runbook utilizando o comando **Get-AutomationPSCredential.**  Este comando é então transmitido ao comando **Invoke-ProcessASADatabase** PowerShell para executar a autenticação para os Serviços de Análise Azure.

6. Teste o livro de execução clicando **em Iniciar**.

    ![Screenshot que mostra a página "Visão Geral" com a ação "Iniciar" selecionada.](./media/analysis-services-refresh-azure-automation/11.png)

7. Preencha os parâmetros **DATABASENAME,** **ANALYSISSERVER** e **REFRESHTYPE** e, em seguida, clique **em OK**. O parâmetro **WEBHOOKDATA** não é necessário quando o Runbook é executado manualmente.

    ![Inicie o Runbook](./media/analysis-services-refresh-azure-automation/12.png)

Se o Runbook for executado com sucesso, receberá uma saída como a seguinte:

![Corrida bem sucedida](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Use um runbook autossuficiente da Azure Automation

O Runbook pode ser configurado para ativar a atualização do modelo Azure Analysis Services numa base programada.

Isto pode ser configurado da seguinte forma:

1. No Livro de Execuções de Automação, clique em **Horários** e, em seguida, **Adicione uma Programação**.
 
    ![Criar horários](./media/analysis-services-refresh-azure-automation/14.png)

2. **Click Schedule** Criar um  >  **novo horário** e, em seguida, preencher os detalhes.

    ![Calendário de configuração](./media/analysis-services-refresh-azure-automation/15.png)

3. Clique em **Criar**.

4. Preencha os parâmetros para o horário. Estes serão utilizados sempre que o Runbook acionar. O parâmetro **WEBHOOKDATA** deve ficar em branco quando funciona através de um horário.

    ![Configurar os parâmetros](./media/analysis-services-refresh-azure-automation/16.png)

5. Clique em **OK**.

## <a name="consume-with-data-factory"></a>Consumir com Fábrica de Dados

Para consumir o runbook utilizando a Azure Data Factory, crie primeiro um **Webhook** para o runbook. O **Webhook** fornecerá um URL que pode ser chamado através de uma atividade web Azure Data Factory.

> [!IMPORTANT]
> Para criar um **Webhook,** o estado do Runbook tem de ser **publicado**.

1. No seu Livro de Execução de Automação, clique em **Webhooks** e, em seguida, clique em **Adicionar Webhook**.

   ![Adicionar Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Dê ao Webhook um nome e uma expiração.  O nome apenas identifica o Webhook dentro do Livro de Execução de Automação, não faz parte do URL.

   >[!CAUTION]
   >Certifique-se de que copia o URL antes de fechar o assistente, uma vez que não o consegue voltar a ter fechado.
    
   ![Configurar Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Os parâmetros para o webhook podem permanecer em branco.  Ao configurar a atividade web da Azure Data Factory, os parâmetros podem ser transmitidos para o corpo da chamada web.

3. Na Data Factory, configurar uma **atividade web**

### <a name="example"></a>Exemplo

   ![Exemplo Atividade Web](./media/analysis-services-refresh-azure-automation/19.png)

O **URL** é o URL criado a partir do Webhook.

O **corpo** é um documento JSON que deve conter as seguintes propriedades:


|Propriedade  |Valor  |
|---------|---------|
|**Análise ServiçosDatabase**     |O nome da base de dados dos Serviços de Análise Azure <br/> Exemplo: AdventureWorksDB         |
|**Serviços de AnáliseServer**     |O nome do servidor Azure Analysis Services. <br/> Exemplo: https: \/ /westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**Base de DadosRefreshType**     |O tipo de refrescamento para realizar. <br/> Exemplo: Completo         |

Exemplo JSON corpo:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Estes parâmetros são definidos no script powerShell do runbook.  Quando a atividade web é executada, a carga útil JSON passada é WEBHOOKDATA.

Isto é deseralizado e armazenado como parâmetros PowerShell, que são então usados pelo comando powerShell Invoke-ProcesASDatabase.

![Webhook deserializado](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Utilize um trabalhador híbrido com serviços de análise Azure

Uma Máquina Virtual Azure com um endereço IP público estático pode ser usada como um Trabalhador Híbrido Azure Automation.  Este endereço IP público pode então ser adicionado à firewall dos Serviços de Análise Azure.

> [!IMPORTANT]
> Certifique-se de que o endereço IP público da Máquina Virtual está configurado como estático.
>
>Para saber mais sobre a configuração dos Trabalhadores Híbridos da Automação Azure, consulte [a instalação hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md#hybrid-runbook-worker-installation).

Uma vez configurado um Trabalhador Híbrido, crie um Webhook como descrito na secção [Consumir com data factory](#consume-with-data-factory).  A única diferença aqui é selecionar a opção **Run on**  >  **Hybrid Worker** ao configurar o Webhook.

Exemplo webhook usando Trabalhador Híbrido:

![Exemplo Híbrido Trabalhador Webhook](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Livro de corridas powershell da amostra

O seguinte corte de código é um exemplo de como executar o modelo Azure Analysis Services refresh usando um PowerShell Runbook.

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
[API REST](/rest/api/analysisservices/servers)