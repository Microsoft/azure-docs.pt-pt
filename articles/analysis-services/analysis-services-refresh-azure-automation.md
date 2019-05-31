---
title: Atualizar Modelos do Azure Analysis Services com a automatização do Azure | Documentos da Microsoft
description: Saiba como codificar as atualizações de modelo ao utilizar a automatização do Azure.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 4cae93cff594ad561973f8029ea7335dc4c60263
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357007"
---
# <a name="refresh-with-azure-automation"></a>Atualizar com a Automatização do Azure

Ao utilizar a automatização do Azure e os Runbooks do PowerShell, pode efetuar operações de atualização automática de dados em seus modelos em tabela de análise do Azure.  

O exemplo neste artigo utiliza a [módulos do PowerShell SqlServer](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Um Runbook do PowerShell, que demonstra a atualizar um modelo de exemplo é fornecido neste artigo.  

## <a name="authentication"></a>Autenticação

Todas as chamadas têm de ser autenticadas com um token válido do Azure Active Directory (OAuth 2).  O exemplo deste artigo irá utilizar um Principal do serviço (SPN) para autenticar para o Azure Analysis Services.

Para saber mais sobre como criar um Principal de serviço, veja [criar um principal de serviço com o portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> O exemplo a seguir supõe que a firewall do Azure Analysis Services está desativada. Se o firewall estiver ativado, em seguida, o endereço IP público do iniciador de pedido terão de ser na lista de permissões na firewall.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Instale SqlServer módulos da galeria do PowerShell.

1. Na sua conta de automatização do Azure, clique em **módulos**, em seguida, **Galeria de procura**.

2. Na barra de pesquisa, procure **SqlServer**.

    ![Procurar módulos](./media/analysis-services-refresh-azure-automation/1.png)

3. Selecione SqlServer, em seguida, clique em **importação**.
 
    ![Módulo de importação](./media/analysis-services-refresh-azure-automation/2.png)

4. Clique em **OK**.
 
### <a name="create-a-service-principal-spn"></a>Criar um Principal de serviço (SPN)

Para saber mais sobre como criar um Principal de serviço, veja [criar um principal de serviço com o portal do Azure](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Configurar as permissões no Azure Analysis Services
 
O Principal de serviço que cria tem de ter permissões de administrador do servidor no servidor. Para obter mais informações, consulte [adicionar um principal de serviço para a função de administrador de servidor](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Design do Runbook de automatização do Azure

1. A conta de automatização, criar um **credenciais** recurso que será utilizado para armazenar com segurança o Principal de serviço.

    ![Criar credencial](./media/analysis-services-refresh-azure-automation/6.png)

2. Introduza os detalhes para a credencial.  Para o **nome de utilizador**, introduza o **ClientId do SPN**, para o **palavra-passe**, introduza o **SPN segredo**.

    ![Criar credencial](./media/analysis-services-refresh-azure-automation/7.png)

3. Importe o Runbook de automatização

    ![Importar Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Navegue para o **atualização Model.ps1** de ficheiros, forneça um **nome** e **Descrição**e, em seguida, clique em **criar**.

    ![Importar Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. Quando o Runbook tiver sido criado, ele automaticamente irá entrar em modo de edição.  Selecione **Publicar**.

    ![Publicar o Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > O recurso de credencial que foi criado anteriormente é recuperado pelo runbook utilizando o **Get-AutomationPSCredential** comando.  Este comando, em seguida, é passado para o **Invoke-ProcessASADatabase** comando do PowerShell para efetuar a autenticação para o Azure Analysis Services.

6. Testar o runbook ao clicar em **iniciar**.

    ![Iniciar o Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. Preencha os **DATABASENAME**, **ANALYSISSERVER**, e **REFRESHTYPE** parâmetros e clique em **OK**. O **WEBHOOKDATA** parâmetro não é necessário quando o Runbook é executado manualmente.

    ![Iniciar o Runbook](./media/analysis-services-refresh-azure-automation/12.png)

Se o Runbook foi executada com êxito, receberá um resultado semelhante ao seguinte:

![Execução concluída com êxito](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Utilizar um Runbook da automatização do Azure autónoma e contém

O Runbook pode ser configurado para o acionador de modelo do Azure Analysis Services atualizar de forma agendada.

Isso pode ser configurado da seguinte forma:

1. O runbook de automatização, clique em **agendas**, em seguida, **adicionar uma agenda**.
 
    ![Criar agenda](./media/analysis-services-refresh-azure-automation/14.png)

2. Clique em **agenda** > **criar uma nova agenda**e, em seguida, preencha os detalhes.

    ![Configurar agenda](./media/analysis-services-refresh-azure-automation/15.png)

3. Clique em **Criar**.

4. Preencha os parâmetros para a agenda. Serão utilizados sempre que aciona o Runbook. O **WEBHOOKDATA** parâmetro deve ser deixado em branco quando em execução por meio de uma agenda.

    ![Configurar parâmetros](./media/analysis-services-refresh-azure-automation/16.png)

5. Clique em **OK**.

## <a name="consume-with-data-factory"></a>Consumir com o Data Factory

Para consumir o runbook com o Azure Data Factory, primeiro crie uma **Webhook** para o runbook. O **Webhook** irá fornecer um URL que pode ser chamado por meio de uma atividade de web do Azure Data Factory.

> [!IMPORTANT]
> Para criar uma **Webhook**, o estado do Runbook tem de ser **publicada**.

1. No seu Runbook de automatização, clique em **Webhooks**e, em seguida, clique em **adicionar Webhook**.

   ![Add Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Dê o Webhook um nome e uma expiração.  O nome identifica apenas o Webhook no interior do Runbook de automatização, ele não fazem parte do URL.

   >[!CAUTION]
   >Certifique-se de que copie o URL antes de fechar o assistente como não pode recuperá-lo novamente uma vez fechado.
    
   ![Configure Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Os parâmetros do webhook podem permanecer em branco.  Ao configurar a atividade de web do Azure Data Factory, os parâmetros podem ser transmitidos no corpo da chamada da web.

3. No Data Factory, configurar um **atividade da web**

### <a name="example"></a>Exemplo

   ![Atividade de Web de exemplo](./media/analysis-services-refresh-azure-automation/19.png)

O **URL** é o URL criado a partir do Webhook.

O **corpo** é um documento JSON que deve conter as seguintes propriedades:


|Propriedade  |Value  |
|---------|---------|
|**AnalysisServicesDatabase**     |O nome da base de dados do Azure Analysis Services <br/> Exemplo: AdventureWorksDB         |
|**AnalysisServicesServer**     |O nome do servidor Azure Analysis Services. <br/> Exemplo: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |O tipo de atualização para executar. <br/> Exemplo: Completo         |

Corpo JSON de exemplo:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Esses parâmetros são definidos num runbook do script do PowerShell.  Quando a atividade web é executada, o payload JSON passado é o WEBHOOKDATA.

Esta é anular a serialização e armazenada como parâmetros do PowerShell, que depois são usados pelo comando Invoke ProcesASDatabase PowerShell.

![Webhook de serialização anulada](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Utilize a função de trabalho híbrida com o Azure Analysis Services

Uma Máquina Virtual com um endereço IP público estático pode ser usado como uma função de trabalho do híbrida de automatização do Azure.  Este endereço IP público, em seguida, pode ser adicionado à firewall do Azure Analysis Services.

> [!IMPORTANT]
> Certifique-se de que o endereço IP público de Máquina Virtual está configurado como estático.
>
>Para saber mais sobre como configurar a funções de trabalho híbridas de automatização do Azure, veja [automatizar recursos no seu datacenter ou na cloud utilizando o Runbook Worker híbrido](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

Quando uma função de trabalho híbrida estiver configurada, criar um Webhook, conforme descrito na secção [Consume com o Data Factory](#consume-with-data-factory).  A única diferença aqui é selecionar o **executar no** > **função de trabalho híbrida** opção quando configurar o Webhook.

Webhook de exemplo usando a função de trabalho híbrida:

![Webhook de Worker híbrido de exemplo](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Runbook do PowerShell de exemplo

O fragmento de código seguinte é um exemplo de como realizar a atualização de modelo do Azure Analysis Services com um Runbook do PowerShell.

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


## <a name="next-steps"></a>Passos Seguintes

[Amostras](analysis-services-samples.md)  
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)
