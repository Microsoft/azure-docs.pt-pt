---
title: Solução de gestão do Office 365 em Azure Microsoft Docs
description: Este artigo fornece detalhes sobre a configuração e utilização da solução Office 365 em Azure.  Inclui descrição detalhada dos registos do Office 365 criados no Monitor Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: 5aa025fb366634e796abfb2eb9c0035d9b87dc3c
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437051"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Solução de gestão do Office 365 em Azure (Pré-visualização)

![Logotipo do escritório 365](media/solution-office-365/icon.png)


> [!IMPORTANT]
> ## <a name="solution-update"></a>Atualização da solução
> Esta solução foi substituída pela solução de disponibilidade geral [do Office 365](../../sentinel/connect-office-365.md) no [Azure Sentinel](../../sentinel/overview.md) e pela solução de [reporte e monitorização da AD Azure.](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) Juntos, fornecem uma versão atualizada da solução anterior do Azure Monitor Office 365 com uma experiência de configuração melhorada. Pode continuar a utilizar a solução existente até 30 de julho de 2020.
> 
> O Azure Sentinel é uma solução de Informação de Segurança e Gestão de Eventos nativa da nuvem que ingere logs e fornece funcionalidades adicionais do SIEM, incluindo deteções, investigações, insights orientados para a caça e aprendizagem automática. A utilização do Azure Sentinel irá agora fornecer-lhe a ingestão de registos de atividade supérrea do Office 365 SharePoint e de gestão de trocas.
> 
> O relatório Azure AD fornece uma visão mais abrangente dos registos da atividade da AD Azure no seu ambiente, incluindo assinar em eventos, eventos de auditoria e alterações no seu diretório. Para ligar os registos da AD Azure, pode utilizar o [conector Azure Sentinel Azure AD](../../sentinel/connect-azure-active-directory.md) ou configurar a integração de [registos da AD Azure com](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)o Monitor Azure . 
>
> A recolha de registo sacana azure está sujeita aos preços do Monitor Azure.  Consulte [os preços do Monitor Azure](https://azure.microsoft.com/pricing/details/monitor/) para obter mais informações.
>
> Para utilizar a solução Azure Sentinel Office 365:
> 1. A utilização do conector Office 365 em Azure Sentinel afeta os preços do seu espaço de trabalho. Para mais informações, consulte [os preços do Azure Sentinel.](https://azure.microsoft.com/pricing/details/azure-sentinel/)
> 2. Se já estiver a utilizar a solução Azure Monitor Office 365, tem primeiro de desinstalá-la utilizando a script na [secção Desinstalação abaixo](#uninstall).
> 3. [Ative a solução Azure Sentinel](../../sentinel/quickstart-onboard.md) no seu espaço de trabalho.
> 4. Vá à página dos **conectores de Dados** no Azure Sentinel e ative o conector **Office 365.**
>
> ## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-april-30th"></a>P: É possível embarcar na solução do Monitor Azure do Office 365 entre hoje e 30 de abril?
> Não, a solução Azure Monitor Office 365 sobre scripts de embarque já não está disponível. A solução será removida no dia 30 de abril.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>P: As mesas e os eschemas serão trocados?
> O nome da tabela **OfficeActivity** e o esquema permanecerão os mesmos que na solução atual. Pode continuar a utilizar as mesmas consultas na nova solução, excluindo consultas que referenciam dados da AD Azure.
> 
> Os novos registos de soluções de [reporte e monitorização da AD Azure](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) serão ingeridos nas [tabelas SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) e [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) em vez de **OfficeActivity**. Para mais informações, consulte [como analisar os registos da AD Azure,](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md)o que também é relevante para os utilizadores do Azure Sentinel e do Azure Monitor.
> 
> Seguem-se amostras para a conversão de consultas do **OfficeActivity** para **SigninLogs:**
> 
> **Consulta falha em inscrições, por utilizador:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId    
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Ver operações da AD Azure:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>P: Como posso embarcar no Azure Sentinel?
> O Azure Sentinel é uma solução que pode permitir no novo ou existente espaço de trabalho do Log Analytics. Para saber mais, consulte a documentação de [embarque do Azure Sentinel.](../../sentinel/quickstart-onboard.md)
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>P: Preciso do Azure Sentinel para ligar os registos da AD Azure?
> Pode configurar a integração de [registos da Azure AD com o Azure Monitor,](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)que não está relacionado com a solução Azure Sentinel. O Azure Sentinel fornece um conector nativo e conteúdo fora da caixa para registos de Anúncios Azure. Para obter mais informações, consulte a questão abaixo sobre o conteúdo orientado para a segurança fora da caixa.
>
> ###    <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>P: Quais são as diferenças ao ligar os registos da Azure AD do Azure Sentinel e do Monitor Azure?
> Azure Sentinel e Azure Monitor ligam-se a registos adatos Azure com base na mesma solução de [reporte e monitorização da AD Azure](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). O Azure Sentinel fornece um conector nativo de um clique que conecta os mesmos dados e fornece informações de monitorização.
>
> ###    <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>P: O que preciso de mudar quando me mudar para as novas tabelas de relatórios e monitorização da AD Azure?
> Todas as consultas que utilizam dados da AD Azure, incluindo consultas em alertas, dashboards e qualquer conteúdo que tenha criado utilizando dados do Office 365 Azure AD, devem ser recriadas utilizando as novas tabelas.
>
> A Azure Sentinel e a Azure AD fornecem conteúdo incorporado que pode utilizar ao deslocar-se para a solução de reporte e monitorização da AD Azure. Para obter mais informações, consulte a próxima questão sobre conteúdos orientados para a segurança fora da caixa e como utilizar os livros de [trabalho do Azure Monitor para relatórios de Diretório Ativo Azure](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md). 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>P: Como posso usar o conteúdo orientado para a segurança do Azure Sentinel fora da caixa?
> O Azure Sentinel fornece dashboards orientados para a segurança fora da caixa, consultas de alerta personalizados, consultas de caça, investigação e capacidades de resposta automatizadas com base nos registos do Office 365 e Azure AD. Explore o Azure Sentinel GitHub e tutoriais para saber mais:
>
> - [Detetar ameaças fora da caixa](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Criar regras analíticas personalizadas para detetar ameaças suspeitas](../../sentinel/tutorial-detect-threats-custom.md)
> - [Monitorizar os seus dados](../../sentinel/tutorial-monitor-your-data.md)
> - [Investigar incidentes com O Sentinela](../../sentinel/tutorial-investigate-cases.md)
> - [Criar respostas de ameaça automatizadas no Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Comunidade Azure Sentinel GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>P: O Azure Sentinel fornece conectores adicionais como parte da solução?
> Sim, veja [o Azure Sentinel ligar fontes](../../sentinel/connect-data-sources.md)de dados.
> 
> ###    <a name="q-what-will-happen-on-april-30-do-i-need-to-offboard-beforehand"></a>P: O que vai acontecer no dia 30 de abril? Preciso de estar fora de bordo antes?
> 
> - Não poderá receber dados da solução **Office365.** A solução deixará de estar disponível no Mercado
> - Para os clientes Azure Sentinel, a solução de espaço de trabalho Log Analytics **Office365** será incluída na solução Azure Sentinel **SecurityInsights.**
> - Se não desativar manualmente a sua solução, os seus dados serão desligados automaticamente no dia 30 de abril.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>P: Os meus dados serão transferidos para a nova solução?
> Sim. Quando retirar a solução **do Office 365** do seu espaço de trabalho, os seus dados ficarão temporariamente indisponíveis porque o esquema é removido. Quando ativar o novo conector **Office 365** no Sentinel, o esquema é restaurado no espaço de trabalho e todos os dados já recolhidos estarão disponíveis. 
 

A solução de gestão do Office 365 permite-lhe monitorizar o seu ambiente office 365 no Monitor Azure.

- Monitorize as atividades dos utilizadores nas suas contas do Office 365 para analisar padrões de utilização, bem como identificar tendências comportamentais. Por exemplo, pode extrair cenários de utilização específicos, tais como ficheiros que são partilhados fora da sua organização ou os sites sharePoint mais populares.
- Monitorize as atividades do administrador para acompanhar alterações de configuração ou operações de privilégio elevado.
- Detete e investigue o comportamento indesejado do utilizador, que pode ser personalizado para as suas necessidades organizacionais.
- Demonstrar auditoria e conformidade. Por exemplo, pode monitorizar as operações de acesso a ficheiros em ficheiros confidenciais, o que pode ajudá-lo no processo de auditoria e conformidade.
- Realizar resolução de problemas operacionais utilizando consultas de [registo](../log-query/log-query-overview.md) em cima dos dados de atividade do Office 365 da sua organização.


## <a name="uninstall"></a>Desinstalar

Pode remover a solução de gestão do Office 365 utilizando o processo em [Remover uma solução](solutions.md#remove-a-monitoring-solution)de gestão . No entanto, isto não impedirá que os dados sejam recolhidos do Office 365 para o Monitor Azure. Siga o procedimento abaixo para cancelar a subscrição do Office 365 e deixe de recolher dados.

1. Guarde o seguinte guião como *office365_unsubscribe.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Executar o script com o seguinte comando:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Exemplo:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

Serão solicitados para credenciais. Forneça as credenciais para o seu espaço de trabalho Log Analytics.

## <a name="data-collection"></a>Recolha de dados

Pode levar algumas horas para que os dados sejam inicialmente recolhidos. Assim que começar a recolher, o Office 365 envia uma [notificação webhook](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) com dados detalhados para o Monitor Azure sempre que um registo é criado. Este registo está disponível no Monitor Azure poucos minutos depois de ter sido recebido.

## <a name="using-the-solution"></a>Utilizar a solução

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Quando adicionar a solução Office 365 ao seu espaço de trabalho Log Analytics, o azulejo **do Office 365** será adicionado ao seu painel de instrumentos. Este mosaico apresenta uma contagem e uma representação gráfica do número de computadores no seu ambiente e a respetiva conformidade de atualização.<br><br>
![Escritório 365 Azulejo sumário](media/solution-office-365/tile.png)  

Clique no **azulejo do Office 365** para abrir o painel de instrumentos do Office **365.**

![Office 365 Dashboard](media/solution-office-365/dashboard.png)  

O dashboard inclui as colunas da tabela seguinte. Cada coluna enumera os dez principais alertas por contagem correspondente aos critérios dessa coluna para o intervalo de tempo e alcance especificado. Pode executar uma pesquisa de registo que fornece toda a lista clicando Para ver tudo na parte inferior da coluna ou clicando no cabeçalho da coluna.

| Coluna | Descrição |
|:--|:--|
| Operações | Fornece informações sobre os utilizadores ativos a partir de todas as subscrições monitorizadas do Office 365. Poderá também ver o número de atividades que acontecem ao longo do tempo.
| Troca | Mostra a desagregação das atividades do Exchange Server, tais como Permissão de Caixa de Correio ou Set-Mailbox. |
| SharePoint | Mostra as principais atividades que os utilizadores realizam em documentos do SharePoint. Quando se afunda a partir deste azulejo, a página de pesquisa mostra os detalhes destas atividades, como o documento-alvo e a localização desta atividade. Por exemplo, para um evento de acesso a ficheiros, poderá ver o documento que está a ser acedido, o seu nome de conta associado e endereço IP. |
| Azure Active Directory | Inclui as principais atividades do utilizador, tais como redefinir a palavra-passe do utilizador e as tentativas de login. Quando você perfurar, você poderá ver os detalhes destas atividades como o Estado do Resultado. Isto é mais útil se quiser monitorizar atividades suspeitas no seu Diretório Ativo Azure. |




## <a name="azure-monitor-log-records"></a>Registos de registos do Monitor Azure

Todos os registos criados no espaço de trabalho Log Analytics no Monitor Azure pela solução Office 365 têm um **Tipo** de Atividade de **Escritório**.  A propriedade **OfficeWorkload** determina a que serviço office 365 o registo se refere - Exchange, AzureActiveDirectory, SharePoint ou OneDrive.  A propriedade **RecordType** especifica o tipo de funcionamento.  As propriedades variam para cada tipo de operação e são mostradas nas tabelas abaixo.

### <a name="common-properties"></a>Common properties (Propriedades comuns)

As seguintes propriedades são comuns a todos os registos do Office 365.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo | *Atividade do Escritório* |
| ClientIP | O endereço IP do dispositivo que foi utilizado quando a atividade foi registada. O endereço IP é apresentado no formato de endereço IPv4 ou IPv6. |
| OfficeWorkload | Serviço de escritório 365 a que o registo se refere.<br><br>AzureActiveDirectory<br>Troca<br>SharePoint|
| Operação | O nome da atividade do utilizador ou administrador.  |
| OrganizationId | O GUID para o escritório 365 inquilino da sua organização. Este valor será sempre o mesmo para a sua organização, independentemente do serviço do Office 365 em que ocorra. |
| RecordType | Tipo de operação realizada. |
| ResultStatus | Indica se a ação (especificada na propriedade Operação) foi concluída com êxito ou não. Os valores possíveis são bem sucedidos, parcialmente bem sucedidos ou falhados. Para a atividade de administrador a Exchange, o valor é verdadeiro ou falso. |
| IDUtilizador | A UPN (Nome Principal do Utilizador) do utilizador que realizou a ação que resultou no registo foi registada; por exemplo, my_name@my_domain_name. Note que também estão incluídos registos de atividade realizadas por contas do sistema (como o sistema SHAREPOINT\ou NTAUTHORITY\SYSTEM). | 
| UserKey | Um ID alternativo para o utilizador identificado na propriedade UserId.  Por exemplo, esta propriedade é povoada com o id exclusivo do passaporte (PUID) para eventos realizados pelos utilizadores em SharePoint, OneDrive for Business e Exchange. Esta propriedade também pode especificar o mesmo valor que a propriedade UserID para eventos ocorridos em outros serviços e eventos realizados por contas do sistema|
| UserType | O tipo de utilizador que executou a operação.<br><br>Administrador<br>Aplicação<br>DcAdmin<br>Normal<br>Reservado<br>ServicePrincipal<br>Sistema |


### <a name="azure-active-directory-base"></a>Base de Diretório Ativo Azure

As seguintes propriedades são comuns a todos os registos do Diretório Ativo Azure.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | O tipo de evento Azure AD. |
| Propriedades Estendidas | As propriedades estendidas do evento Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Logon da Conta de Diretório Ativo Azure

Estes registos são criados quando um utilizador do Diretório Ativo tenta iniciar sessão.

| Propriedade | Descrição |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | A aplicação que desencadeia o evento de login da conta, como o Office 15. |
| `Client` | Detalhes sobre o dispositivo cliente, dispositivo OS e navegador de dispositivo que foi usado para o evento de login da conta. |
| `LoginStatus` | Esta propriedade é de OrgIdLogon.LoginStatus diretamente. O mapeamento de várias falhas interessantes de logon poderia ser feito alertando algoritmos. |
| `UserDomain` | A Informação de Identidade do Inquilino (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Estes registos são criados quando são feitas alterações ou adições em objetos de Diretório Ativo Azure.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| Alvo AAD | O utilizador em que a ação (identificada pela operação imóvel) foi realizada. |
| Ator | O utilizador ou diretor de serviço que realizou a ação. |
| ActorContextid | O GUID da organização a que o ator pertence. |
| ActorIpAddress | O endereço IP do ator no formato de endereço IPV4 ou IPV6. |
| InterSystemsId | O GUID que acompanha as ações através de componentes dentro do serviço Office 365. |
| Intrasistemaid |     O GUID que é gerado pelo Azure Ative Directory para acompanhar a ação. |
| SupportTicketid | O bilhete de apoio ao cliente ID para a ação em situações de "act-on-be-of". |
| TargetContextid | O GUID da organização a que pertence o utilizador visado. |


### <a name="data-center-security"></a>Segurança do Data Center

Estes registos são criados a partir de dados de auditoria de segurança do Data Center.  

| Propriedade | Descrição |
|:--- |:--- |
| Organização Eficaz | O nome do inquilino a que a elevação/cmdlet foi alvo. |
| ElevaçãoTempo Aprovado | A marca de tempo para quando a elevação foi aprovada. |
| Aprovador de Elevação | O nome de um gerente da Microsoft. |
| Duração de elevação | A duração pela qual a elevação estava ativa. |
| Elevação Requestdóide |     Um identificador único para o pedido de elevação. |
| Papel de Elevação | O papel que a elevação foi solicitada. |
| Tempo de Elevação | O início da elevação. |
| Start_Time | A hora de início da execução do cmdlet. |


### <a name="exchange-admin"></a>Administrador de intercâmbio

Estes registos são criados quando são feitas alterações à configuração de Troca.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Troca |
| RecordType     | ExchangeAdmin |
| ExternalAccess |     Especifica se o cmdlet foi gerido por um utilizador na sua organização, por pessoal do Centro de Dados da Microsoft ou por uma conta de serviço de datacenter, ou por um administrador delegado. O valor Falso indica que o cmdlet foi dirigido por alguém da sua organização. O valor True indica que o cmdlet foi gerido por pessoal do datacenter, uma conta de serviço de datacenter ou um administrador delegado. |
| Nome modificado DoObjeto Resolvido |     Este é o nome amigável do utilizador do objeto que foi modificado pelo cmdlet. Isto só é registado se o cmdlet modificar o objeto. |
| OrganizationName | O nome do inquilino. |
| OriginandoServer | O nome do servidor a partir do qual o cmdlet foi executado. |
| Parâmetros | O nome e valor para todos os parâmetros utilizados com o cmdlet identificado na propriedade Operações. |


### <a name="exchange-mailbox"></a>Caixa de correio de troca

Estes registos são criados quando são feitas alterações ou adições para trocar caixas de correio.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Troca |
| RecordType     | ExchangeItem |
| ClientInfoString | Informações sobre o cliente de e-mail que foi usado para realizar a operação, como uma versão de navegador, versão Outlook e informações de dispositivos móveis. |
| Client_IPAddress | O endereço IP do dispositivo que foi utilizado quando a operação foi registada. O endereço IP é apresentado no formato de endereço IPv4 ou IPv6. |
| Nome da Máquina cliente | O nome da máquina que acolhe o cliente do Outlook. |
| Nome do Processo do Cliente | O cliente de e-mail que foi usado para aceder à caixa de correio. |
| Versão do Cliente | A versão do cliente de e-mail. |
| InternalLogonType | Reservado para uso interno. |
| Logon_Type | Indica o tipo de utilizador que acedeu à caixa de correio e realizou a operação que foi registada. |
| LogonUserDisplayName |     O nome fácil de utilizar do utilizador que realizou a operação. |
| LogonUserSid | O SID do utilizador que realizou a operação. |
| Caixa de correioGuid | O Guia de Intercâmbio da caixa de correio a que foi acedido. |
| Caixa de correioOwnerMasterAccountSid | Conta principal da conta do dono da caixa de correio SID. |
| Caixa de correioOwnerSid | O SID do dono da caixa de correio. |
| Caixa de correioOwnerUPN | O endereço de e-mail da pessoa proprietária da caixa de correio a que foi acedida. |


### <a name="exchange-mailbox-audit"></a>Auditoria de Caixa de Correios de Troca

Estes registos são criados quando é criada uma entrada de auditoria de uma caixa de correio.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Troca |
| RecordType     | ExchangeItem |
| Item | Representa o item sobre o qual a operação foi realizada | 
| SendAsUserMailboxGuid | O Guia de Intercâmbio da caixa de correio que foi acedida para enviar e-mail como. |
| SendAsUserSmtp | Endereço SMTP do utilizador que está a ser personificado. |
| SendonBehalfOfUserBoxGuid | O Guia de Intercâmbio da caixa de correio que foi acedida para enviar correio em nome de. |
| SendOnBehalfOfUserSmtp | Endereço SMTP do utilizador em nome de quem o e-mail é enviado. |


### <a name="exchange-mailbox-audit-group"></a>Grupo de Auditoria de Caixa de Correios

Estes registos são criados quando são feitas alterações ou adições aos grupos de intercâmbio.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Troca |
| OfficeWorkload | Grupo ExchangeItem |
| Itens afetados | Informação sobre cada item do grupo. |
| Operações CrossMailbox | Indica se a operação envolveu mais de uma caixa de correio. |
| DestMailboxId | Definir apenas se o parâmetro CrossMailboxOperations for True. Especifica a caixa de correio-alvo GUID. |
| DestMailboxOwnerMasterAccountSid | Definir apenas se o parâmetro CrossMailboxOperations for True. Especifica o SID para a conta principal SID do proprietário da caixa de correio alvo. |
| DestMailboxOwnerSid | Definir apenas se o parâmetro CrossMailboxOperations for True. Especifica o SID da caixa de correio alvo. |
| DestMailboxOwnerUPN | Definir apenas se o parâmetro CrossMailboxOperations for True. Especifica a UPN do proprietário da caixa de correio alvo. |
| DestFolder | A pasta de destino, para operações como Move. |
| Pasta | A pasta onde está localizado um grupo de itens. |
| Pastas |     Informações sobre as pastas de origem envolvidas numa operação; por exemplo, se as pastas forem selecionadas e depois eliminadas. |


### <a name="sharepoint-base"></a>SharePoint Base

Estas propriedades são comuns a todos os registos do SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Identifica que um evento ocorreu no SharePoint. Os valores possíveis são SharePoint ou ObjectModel. |
| TipoItem | O tipo de objeto que foi acedido ou modificado. Consulte a tabela ItemType para obter detalhes sobre os tipos de objetos. |
| MachineDomainInfo | Informações sobre operações de sincronização do dispositivo. Esta informação só é reportada se estiver presente no pedido. |
| Máquina de lavar |     Informações sobre operações de sincronização do dispositivo. Esta informação só é reportada se estiver presente no pedido. |
| Site_ | O GUID do site onde está localizado o ficheiro ou pasta acedido pelo utilizador. |
| Source_Name | A entidade que desencadeou a operação auditada. Os valores possíveis são SharePoint ou ObjectModel. |
| UserAgent | Informações sobre o cliente ou navegador do utilizador. Esta informação é fornecida pelo cliente ou navegador. |


### <a name="sharepoint-schema"></a>SharePoint Schema

Estes registos são criados quando as alterações de configuração são feitas para o SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| Evento Personalizado | Corda opcional para eventos personalizados. |
| Event_Data |     Carga opcional para eventos personalizados. |
| Propriedades Modificadas | A propriedade está incluída para eventos administrativos, tais como adicionar um utilizador como membro de um site ou um grupo de administração de recolha de site. A propriedade inclui o nome da propriedade que foi modificada (por exemplo, o grupo Site Admin), o novo valor da propriedade modificada (tal é o utilizador que foi adicionado como administrador do site), e o valor anterior do objeto modificado. |


### <a name="sharepoint-file-operations"></a>Operações de ficheiros SharePoint

Estes registos são criados em resposta a operações de ficheiros no SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | Operação SharePointFile |
| Extensão do Ficheiro de Destino | A extensão do ficheiro de um ficheiro que é copiado ou movido. Esta propriedade é apresentada apenas para eventos FileCopied e FileMoved. |
| Nome de DestinationFile | O nome do ficheiro que é copiado ou movido. Esta propriedade é apresentada apenas para eventos FileCopied e FileMoved. |
| DestinationRelativeUrl | O URL da pasta de destino onde um ficheiro é copiado ou movido. A combinação dos valores para os parâmetros SiteURL, DestinationRelativeURL e DestinationFileName é o mesmo que o valor para a propriedade ObjectID, que é o nome completo do caminho para o ficheiro que foi copiado. Esta propriedade é apresentada apenas para eventos FileCopied e FileMoved. |
| PartilharType | O tipo de permissões de partilha que foram atribuídas ao utilizador com que o recurso foi partilhado. Este utilizador é identificado pelo parâmetro UserSharedWith. |
| Site_Url | O URL do site onde o ficheiro ou pasta acedido pelo utilizador está localizado. |
| Extensão do Ficheiro fonte | A extensão do ficheiro que foi acedida pelo utilizador. Esta propriedade está em branco se o objeto que foi acedido é uma pasta. |
| Nome sourcefile |     O nome do ficheiro ou pasta acedido pelo utilizador. |
| SourceRelativeUrl | O URL da pasta que contém o ficheiro acedido pelo utilizador. A combinação dos valores para os parâmetros SiteURL, SourceRelativeURL e SourceFileName é o mesmo que o valor para a propriedade ObjectID, que é o nome de caminho completo para o ficheiro acedido pelo utilizador. |
| UserSharedWith |     O utilizador com o que um recurso foi partilhado. |




## <a name="sample-log-queries"></a>Consultas de registo de amostras

A tabela seguinte fornece consultas de registo de amostras para registos de atualização recolhidos por esta solução.

| Consulta | Descrição |
| --- | --- |
|Contagem de todas as operações na subscrição do seu Office 365 |OfficeActivity &#124; resumir contagem() por Operação |
|Utilização de sites sharePoint|OfficeActivity &#124; onde officeWorkload =~ "sharepoint" &#124; resumir \| a contagem() por siteUrl tipo por Count asc|
|Operações de acesso a ficheiros por tipo de utilizador | OfficeActivity &#124; resumir contagem() por UserType |
|Monitorizar as ações externas no Exchange|OfficeActivity &#124; onde officeWorkload =~ "exchange" e ExternalAccess == verdadeiro|



## <a name="next-steps"></a>Passos seguintes

* Utilize consultas de [registo no Monitor Azure](../log-query/log-query-overview.md) para visualizar dados de atualização detalhados.
* [Crie os seus próprios dashboards](../learn/tutorial-logs-dashboards.md) para exibir as suas consultas de pesquisa favoritas do Office 365.
* [Criar alertas](../platform/alerts-overview.md) para serem ativamente notificados das importantes atividades do Office 365.  
