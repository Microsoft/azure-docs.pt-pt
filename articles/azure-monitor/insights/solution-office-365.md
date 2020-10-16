---
title: Solução de gestão do Office 365 em Azure
description: Este artigo fornece detalhes sobre a configuração e utilização da solução Office 365 em Azure.  Inclui uma descrição detalhada dos registos do Office 365 criados no Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: eb20bf4164cb2153f6786dbec04f79453554fa25
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999745"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Solução de gestão do Office 365 em Azure (Pré-visualização)

![Logotipo do Escritório 365](media/solution-office-365/icon.png)

> [!IMPORTANT]
> ## <a name="solution-update"></a>Atualização de soluções
> Esta solução foi substituída pela solução de disponibilidade geral [do Office 365](../../sentinel/connect-office-365.md) em [Azure Sentinel](../../sentinel/overview.md) e pela solução de relatório e monitorização da [Azure AD.](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) Em conjunto, fornecem uma versão atualizada da solução anterior do Azure Monitor Office 365 com uma experiência de configuração melhorada. Pode continuar a utilizar a solução existente até 31 de outubro de 2020.
> 
> Azure Sentinel é uma solução de Informação de Segurança nativa da nuvem e gestão de eventos que ingere registos e fornece funcionalidades adicionais de SIEM, incluindo deteções, investigações, pesquisas e aprendizagem automática. A utilização do Azure Sentinel irá agora fornecer-lhe a ingestão de registos de atividade do Office 365 SharePoint e de gestão de câmbio.
> 
> O relatório AD AD da Azure proporciona uma visão mais abrangente dos registos da atividade da AZure AD no seu ambiente, incluindo sinal em eventos, eventos de auditoria e alterações no seu diretório. Para ligar os registos AZure AD, pode utilizar o [conector Azure AD do Azure Sentinel](../../sentinel/connect-azure-active-directory.md) ou configurar [a integração de registos Azure AD com o Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 
>
> A recolha do registo AD Azure está sujeita aos preços do Azure Monitor.  Consulte [os preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) para obter mais informações.
>
> Para utilizar a solução Azure Sentinel Office 365:
> 1. A utilização do conector Do Office 365 em Azure Sentinel afeta os preços do seu espaço de trabalho. Para mais informações, consulte [os preços do Azure Sentinel.](https://azure.microsoft.com/pricing/details/azure-sentinel/)
> 2. Se já estiver a utilizar a solução Azure Monitor Office 365, deve primeiro desinstalá-la utilizando o script na [secção Desinstalar abaixo](#uninstall).
> 3. [Ative a solução Azure Sentinel](../../sentinel/quickstart-onboard.md) no seu espaço de trabalho.
> 4. Aceda à página **de conectores de dados** em Azure Sentinel e ative o conector **Office 365.**
>
> ## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-october-31"></a>P: É possível embarcar na solução do Office 365 Azure Monitor entre hoje e 31 de outubro?
> Não, a solução Azure Monitor Office 365 scripts de bordo já não estão disponíveis. A solução será removida no dia 31 de outubro.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>P: As mesas e esquemas serão alterados?
> O nome e o esquema da mesa **OfficeActivity** permanecerão iguais à solução atual. Pode continuar a utilizar as mesmas consultas na nova solução, excluindo consultas que refiram dados AZure AD.
> 
> Os novos registos [de solução de relatório e monitorização da AZure AD](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) serão ingeridos nas [tabelas SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) e [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) em vez do **OfficeActivity**. Para mais informações, consulte [como analisar os registos Azure AD,](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md)o que também é relevante para os utilizadores do Azure Sentinel e do Azure Monitor.
> 
> Seguem-se amostras para converter consultas do **OfficeActivity** para **SigninLogs:**
> 
> **Consulta falhou nos ins-ins, pelo utilizador:**
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
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>P: Como posso embarcar Azure Sentinel?
> O Azure Sentinel é uma solução que pode permitir no novo ou existente espaço de trabalho log analytics. Para saber mais, consulte [a documentação de embarque do Azure Sentinel.](../../sentinel/quickstart-onboard.md)
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>P: Preciso que o Azure Sentinel ligue os registos AZure AD?
> Pode configurar [a integração de registos Azure AD com o Azure Monitor,](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)que não está relacionado com a solução Azure Sentinel. O Azure Sentinel fornece um conector nativo e conteúdo fora da caixa para registos Azure AD. Para obter mais informações, consulte a pergunta abaixo sobre o conteúdo fora da caixa orientado para a segurança.
>
> ###    <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>P: Quais são as diferenças ao ligar os registos Azure AD de Azure Sentinel e Azure Monitor?
> O Azure Sentinel e o Azure Monitor ligam-se aos registos AD do Azure com base na mesma [solução de relatório e monitorização da AZure AD](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). O Azure Sentinel fornece um conector nativo de um clique que liga os mesmos dados e fornece informações de monitorização.
>
> ###    <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>P: O que preciso de mudar quando me mudar para as novas tabelas de relatórios e monitorização da AZure AD?
> Todas as consultas utilizando dados AD do Azure, incluindo consultas em alertas, dashboards e qualquer conteúdo que tenha criado utilizando dados AD do Office 365 Azure, devem ser recriadas utilizando as novas tabelas.
>
> A azure Sentinel e Azure AD fornecem conteúdo incorporado que pode utilizar ao mudar-se para a solução de relatório e monitorização da Azure AD. Para obter mais informações, consulte a próxima pergunta sobre conteúdo sonoro orientado para a segurança fora da caixa e [como utilizar os livros de trabalho do Azure Monitor para relatórios do Azure Ative Directory](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md). 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>P: Como posso usar o conteúdo orientado para a segurança do Azure Sentinel?
> O Azure Sentinel fornece dashboards orientados para a segurança fora da caixa, consultas de alerta personalizadas, consultas de caça, investigação e capacidades de resposta automatizada com base nos registos do Office 365 e Azure AD. Explore o Azure Sentinel GitHub e tutoriais para saber mais:
>
> - [Detetar ameaças fora da caixa](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Criar regras analíticas personalizadas para detetar ameaças suspeitas](../../sentinel/tutorial-detect-threats-custom.md)
> - [Monitorizar os seus dados](../../sentinel/tutorial-monitor-your-data.md)
> - [Investigue incidentes com Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)
> - [Configurar respostas automáticas de ameaças em Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Comunidade Azure Sentinel GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>P: O Azure Sentinel fornece conectores adicionais como parte da solução?
> Sim, ver [Azure Sentinel ligar fontes de dados.](../../sentinel/connect-data-sources.md)
> 
> ###    <a name="q-what-will-happen-on-october-31-do-i-need-to-offboard-beforehand"></a>P: O que vai acontecer no dia 31 de outubro? Preciso de sair de lá antes?
> 
> - Não poderá receber dados da solução **Office365.** A solução será removida do seu espaço de trabalho e deixará de estar disponível no Mercado.
> - Para os clientes do Azure Sentinel, a solução de espaço de trabalho Log Analytics **Office365** será incluída na solução Azure Sentinel **SecurityInsights.**
> - Se não desacolexar a sua solução manualmente até 31 de outubro, os seus dados serão desligados automaticamente e a tabela **OfficeActivity** será removida. Ainda assim, poderá restaurar a tabela quando ativar o conector Do Office 365 em Azure Sentinel, como explicado abaixo.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>P: Os meus dados serão transferidos para a nova solução?
> Sim. Quando remover a solução **Office 365** do seu espaço de trabalho, os seus dados ficarão temporariamente indisponíveis porque o esquema é removido. Quando ativar o novo conector **do Office 365** no Sentinel, o esquema é restaurado no espaço de trabalho e quaisquer dados já recolhidos ficarão disponíveis. 
 

A solução de gestão do Office 365 permite-lhe monitorizar o ambiente do Seu Office 365 no Azure Monitor.

- Monitorize as atividades dos utilizadores nas suas contas do Office 365 para analisar padrões de utilização, bem como identificar tendências comportamentais. Por exemplo, pode extrair cenários de utilização específicos, tais como ficheiros partilhados fora da sua organização ou os sites sharePoint mais populares.
- Monitorize as atividades do administrador para acompanhar as alterações de configuração ou operações de alto privilégio.
- Detete e investigue comportamentos indesejados do utilizador, que podem ser personalizados para as suas necessidades organizacionais.
- Demonstrar auditoria e conformidade. Por exemplo, pode monitorizar as operações de acesso a ficheiros em ficheiros confidenciais, o que pode ajudá-lo com o processo de auditoria e conformidade.
- Realize a resolução de problemas operacionais utilizando consultas de [registo](../log-query/log-query-overview.md) em cima dos dados de atividade do Office 365 da sua organização.


## <a name="uninstall"></a>Desinstalar

Pode remover a solução de gestão do Office 365 utilizando o processo em [Remover uma solução de gestão.](solutions.md#remove-a-monitoring-solution) No entanto, isto não impedirá que os dados sejam recolhidos do Office 365 para o Azure Monitor. Siga o procedimento abaixo para cancelar a subscrição do Office 365 e pare de recolher dados.

1. Guarde o seguinte script como *office365_unsubscribe.ps1*.

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

2. Execute o script com o seguinte comando:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Exemplo:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

Serão solicitados para obter credenciais. Forneça as credenciais para o seu espaço de trabalho Log Analytics.

## <a name="data-collection"></a>Recolha de dados

Pode levar algumas horas para que os dados sejam inicialmente recolhidos. Uma vez que começa a ser recolhido, o Office 365 envia uma [notificação webhook](/office/office-365-management-api/office-365-management-activity-api-reference#receiving-notifications) com dados detalhados para o Azure Monitor cada vez que um registo é criado. Este registo está disponível no Azure Monitor poucos minutos depois de ter sido recebido.

## <a name="using-the-solution"></a>Utilizar a solução

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Quando adicionar a solução Office 365 ao seu espaço de trabalho Log Analytics, o azulejo **do Office 365** será adicionado ao seu painel de instrumentos. Este mosaico apresenta uma contagem e uma representação gráfica do número de computadores no seu ambiente e a respetiva conformidade de atualização.<br><br>
![Azulejo sumário do Escritório 365](media/solution-office-365/tile.png)  

Clique no **azulejo do Office 365** para abrir o painel do Office **365.**

![Painel de instrumentos do Escritório 365](media/solution-office-365/dashboard.png)  

O dashboard inclui as colunas da tabela seguinte. Cada coluna lista os dez primeiros alertas por contagem que corresponde aos critérios dessa coluna para o âmbito e intervalo de tempo especificados. Pode executar uma pesquisa de registo que forneça toda a lista clicando em Ver tudo na parte inferior da coluna ou clicando no cabeçalho da coluna.

| Coluna | Descrição |
|:--|:--|
| Operações | Fornece informações sobre os utilizadores ativos a partir de todas as subscrições monitorizadas do Office 365. Você também poderá ver o número de atividades que acontecem ao longo do tempo.
| Troca | Mostra a desagregação das atividades do Exchange Server, tais como Add-Mailbox Permission ou Set-Mailbox. |
| SharePoint | Mostra as principais atividades que os utilizadores realizam nos documentos do SharePoint. Ao perfurar a partir deste azulejo, a página de pesquisa mostra os detalhes destas atividades, como o documento-alvo e a localização desta atividade. Por exemplo, para um evento Com Acesso a Ficheiros, poderá ver o documento que está a ser acedido, o nome da conta associada e o endereço IP. |
| Azure Active Directory | Inclui as principais atividades do utilizador, tais como redefinir a palavra-passe do utilizador e as tentativas de login. Ao aprofundar, poderá ver os detalhes destas atividades como o Estado do Resultado. Isto é mais útil se quiser monitorizar atividades suspeitas no seu Diretório Ativo Azure. |




## <a name="azure-monitor-log-records"></a>Registos de registos do Azure Monitor

Todos os registos criados no espaço de trabalho Log Analytics em Azure Monitor pela solução Office 365 têm um **tipo** de **OfficeActivity**.  A propriedade **OfficeWorkload** determina qual o serviço office 365 a que o registo se refere - Exchange, AzureActiveDirectory, SharePoint ou OneDrive.  A propriedade **RecordType** especifica o tipo de operação.  As propriedades variam para cada tipo de operação e são mostradas nas tabelas abaixo.

### <a name="common-properties"></a>Common properties (Propriedades comuns)

As seguintes propriedades são comuns a todos os registos do Office 365.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo | *OfficeActivity* |
| ClientIP | O endereço IP do dispositivo que foi utilizado quando a atividade foi registada. O endereço IP é apresentado no formato de endereço IPv4 ou IPv6. |
| OfficeWorkload | Serviço do Escritório 365 a que o registo se refere.<br><br>AzureActiveDirectory<br>Troca<br>SharePoint|
| Operação | O nome da atividade do utilizador ou administrador.  |
| OrganizationId | O GUID para o escritório 365 inquilino da sua organização. Este valor será sempre o mesmo para a sua organização, independentemente do serviço Do Office 365 em que ocorra. |
| RecordType | Tipo de operação realizada. |
| ResultStatus | Indica se a ação (especificada na propriedade Operação) foi concluída com êxito ou não. Os valores possíveis são bem sucedidos, parcialmenteduzidos ou falhados. Para a atividade de administração exchange, o valor é verdadeiro ou falso. |
| IDUtilizador | A UPN (Nome Principal do Utilizador) do utilizador que realizou a ação que resultou na registo; por exemplo, my_name@my_domain_name . . Note que os registos de atividades realizadas por contas do sistema (tais como SHAREPOINT\system ou NTAUTHORITY\SYSTEM) também estão incluídos. | 
| UserKey | Uma identificação alternativa para o utilizador identificado na propriedade UserId.  Por exemplo, esta propriedade é povoada com o passaporte único ID (PUID) para eventos realizados por utilizadores em SharePoint, OneDrive for Business e Exchange. Esta propriedade também pode especificar o mesmo valor que a propriedade UserID para eventos que ocorrem em outros serviços e eventos realizados por contas do sistema|
| UserType | O tipo de utilizador que executou a operação.<br><br>Administrador<br>Aplicação<br>DcAdmin<br>Normal<br>Reservado<br>ServicePrincipal<br>Sistema |


### <a name="azure-active-directory-base"></a>Base de Diretório Ativo Azure

As seguintes propriedades são comuns a todos os registos do Azure Ative Directory.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | O tipo de evento AD AZure. |
| Extensões | As propriedades estendidas do evento AD Azure. |


### <a name="azure-active-directory-account-logon"></a>Início da conta do Diretório Ativo Azure

Estes registos são criados quando um utilizador do Ative Directory tenta iniciar sessão.

| Propriedade | Descrição |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | A aplicação que desencadeia o evento de login da conta, como o Office 15. |
| `Client` | Detalhes sobre o dispositivo cliente, o sistema operativo e o navegador de dispositivos que foi utilizado para o evento de login da conta. |
| `LoginStatus` | Esta propriedade é de OrgIdLogon.LoginStatus diretamente. O mapeamento de várias falhas interessantes de início de são poderia ser feito alertando algoritmos. |
| `UserDomain` | A Informação de Identidade do Inquilino (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Estes registos são criados quando alterações ou adições são feitas a objetos do Azure Ative Directory.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | O utilizador em que a ação (identificada pelo imóvel da Operação) foi realizada. |
| Ator | O utilizador ou diretor de serviço que realizou a ação. |
| ActorContextId | O GUID da organização a que pertence o ator. |
| ActorIpAddress | O endereço IP do ator no formato ipv4 ou IPV6. |
| InterSystemsId | O GUID que acompanha as ações através de componentes dentro do serviço Office 365. |
| IntraSystemId |     O GUID que é gerado pelo Azure Ative Directory para acompanhar a ação. |
| SupportTicketId | O ID do bilhete de apoio ao cliente para a ação em situações de "agir em nome". |
| TargetContextId | O GUID da organização a que pertence o utilizador-alvo pertence. |


### <a name="data-center-security"></a>Segurança do Data Center

Estes registos são criados a partir de dados de auditoria do Data Center Security.  

| Propriedade | Descrição |
|:--- |:--- |
| Organização Eficaz | O nome do inquilino a que a elevação/cmdlet foi alvo. |
| Hora de Aprovação de Elevação | A hora da elevação foi aprovada. |
| ElevaçãoApprover | O nome de um gerente da Microsoft. |
| ElevaçãoDuração | A duração para a qual a elevação estava ativa. |
| ElevaçãoRequestId |     Um identificador único para o pedido de elevação. |
| Elevação | O papel que a elevação foi solicitado. |
| Hora da Elevação | A hora de início da elevação. |
| Start_Time | A hora de início da execução do cmdlet. |


### <a name="exchange-admin"></a>Administração de Intercâmbio

Estes registos são criados quando são feitas alterações à configuração de Troca.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Troca |
| RecordType     | ExchangeAdmin |
| ExternalAccess |     Especifica se o cmdlet foi gerido por um utilizador na sua organização, pelo pessoal do Datacenter da Microsoft ou por uma conta de serviço datacenter, ou por um administrador delegado. O valor Falso indica que o cmdlet era gerido por alguém da sua organização. O valor True indica que o cmdlet era gerido por pessoal do datacenter, uma conta de serviço do datacenter ou um administrador delegado. |
| ModificadoObjectResolvedName |     Este é o nome amigável do utilizador do objeto que foi modificado pelo cmdlet. Isto só é registado se o cmdlet modificar o objeto. |
| OrganizationName | O nome do inquilino. |
| Vime | O nome do servidor a partir do qual o cmdlet foi executado. |
| Parâmetros | O nome e valor de todos os parâmetros que foram utilizados com o cmdlet que é identificado na propriedade Operações. |


### <a name="exchange-mailbox"></a>Caixa de Correio de Troca

Estes registos são criados quando são feitas alterações ou adições nas caixas de correio de Exchange.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Troca |
| RecordType     | ExchangeItem |
| ClientInfoString | Informações sobre o cliente de e-mail que foi usado para realizar a operação, como uma versão de navegador, versão Outlook e informações de dispositivos móveis. |
| Client_IPAddress | O endereço IP do dispositivo que foi utilizado quando a operação foi registada. O endereço IP é apresentado no formato de endereço IPv4 ou IPv6. |
| Nome cliente | O nome da máquina que acolhe o cliente Outlook. |
| Nome de Processamento de Clientes | O cliente de e-mail que foi usado para aceder à caixa de correio. |
| Versão do Cliente | A versão do cliente de e-mail. |
| Sistema InternoType | Reservado para uso interno. |
| Logon_Type | Indica o tipo de utilizador que acedeu à caixa de correio e realizou a operação que foi registada. |
| Nome logonUserDisplay |     O nome fácil de utilizar do utilizador que realizou a operação. |
| LogonUserSid | O SID do utilizador que realizou a operação. |
| MailboxGuid | A Troca GUID da caixa de correio a que foi acedida. |
| MailboxOwnerMasterAccountsid | Conta principal do proprietário da caixa de correio SID. |
| MailboxOwnerSid | O SID do dono da caixa de correio. |
| MailboxOwnerUPN | O endereço de e-mail da pessoa proprietária da caixa de correio que foi acedida. |


### <a name="exchange-mailbox-audit"></a>Auditoria de Caixa de Correio de Câmbio

Estes registos são criados quando uma entrada de auditoria da caixa de correio é criada.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Troca |
| RecordType     | ExchangeItem |
| Item | Representa o item sobre o qual a operação foi realizada | 
| SendAsUserMailboxGuid | A Troca GUID da caixa de correio que foi acedida para enviar e-mail como. |
| SendAsUserSmtp | Endereço SMTP do utilizador que está a ser personificado. |
| SernBehalfOfUserMailboxGuid | A Troca GUID da caixa de correio a que foi acedida para enviar correio em nome de. |
| SendOnBehalfOfUserSmtp | Endereço SMTP do utilizador em nome de quem o e-mail é enviado. |


### <a name="exchange-mailbox-audit-group"></a>Grupo de Auditoria de Caixa de Correio de Intercâmbio

Estes registos são criados quando alterações ou adições são feitas aos grupos Exchange.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Troca |
| OfficeWorkload | ExchangeItemGroup |
| AfectadosItems | Informação sobre cada item do grupo. |
| Operações CrossMailbox | Indica se a operação envolveu mais de uma caixa de correio. |
| DestMailboxId | Definido apenas se o parâmetro CrossMailboxOperations for Verdadeiro. Especifica a caixa de correio alvo GUID. |
| DestMailboxOwnerMasterAccountsid | Definido apenas se o parâmetro CrossMailboxOperations for Verdadeiro. Especifica o SID para a conta principal SID do proprietário da caixa de correio alvo. |
| DestMailboxOwnerSid | Definido apenas se o parâmetro CrossMailboxOperations for Verdadeiro. Especifica o SID da caixa de correio alvo. |
| DestMailboxOwnerUPN | Definido apenas se o parâmetro CrossMailboxOperations for Verdadeiro. Especifica a UPN do proprietário da caixa de correio alvo. |
| DestFolder | A pasta de destino, para operações como Move. |
| Pasta | A pasta onde se encontra um grupo de itens. |
| Pastas |     Informações sobre as pastas de origem envolvidas numa operação; por exemplo, se as pastas forem selecionadas e depois eliminadas. |


### <a name="sharepoint-base"></a>SharePoint Base

Estas propriedades são comuns a todos os registos do SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| Fonte de Eventos | Identifica que ocorreu um evento no SharePoint. Os valores possíveis são SharePoint ou ObjectModel. |
| TipoItem | O tipo de objeto que foi acedido ou modificado. Consulte a tabela ItemType para obter mais detalhes sobre os tipos de objetos. |
| MachineDomainInfo | Informação sobre operações de sincronização de dispositivos. Esta informação só é reportada se estiver presente no pedido. |
| MachineId |     Informação sobre operações de sincronização de dispositivos. Esta informação só é reportada se estiver presente no pedido. |
| Site_ | O GUID do site onde se encontra o ficheiro ou pasta acedido pelo utilizador. |
| Source_Name | A entidade que desencadeou a operação auditada. Os valores possíveis são SharePoint ou ObjectModel. |
| UserAgent | Informação sobre o cliente ou navegador do utilizador. Esta informação é fornecida pelo cliente ou navegador. |


### <a name="sharepoint-schema"></a>SharePoint Schema

Estes registos são criados quando as alterações de configuração são feitas para SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| Evento Personalizado | Cadeia opcional para eventos personalizados. |
| Event_Data |     Carga útil opcional para eventos personalizados. |
| Propriedades Modificadas | A propriedade está incluída para eventos administrativos, tais como adicionar um utilizador como membro de um site ou um grupo de administração de coleção de site. O imóvel inclui o nome do imóvel que foi modificado (por exemplo, o grupo De administrador do site), o novo valor da propriedade modificada (tal utilizador que foi adicionado como administrador do site), e o valor anterior do objeto modificado. |


### <a name="sharepoint-file-operations"></a>Operações de ficheiros SharePoint

Estes registos são criados em resposta às operações de ficheiros no SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperação |
| DestinationFileExtension | A extensão do ficheiro de um ficheiro que é copiado ou movido. Esta propriedade é exibida apenas para eventos FileCopied e FileMoved. |
| Nome do DestinationFile | O nome do ficheiro que é copiado ou movido. Esta propriedade é exibida apenas para eventos FileCopied e FileMoved. |
| DestinationRelativeUrl | O URL da pasta de destino onde um ficheiro é copiado ou movido. A combinação dos valores para os parâmetros SiteURL, DestinationRelativeURL e DestinationFileName é o mesmo que o valor para a propriedade ObjectID, que é o nome de caminho completo para o ficheiro que foi copiado. Esta propriedade é exibida apenas para eventos FileCopied e FileMoved. |
| Tipo de Partilha | O tipo de permissões de partilha que foram atribuídas ao utilizador com a quais o recurso foi partilhado. Este utilizador é identificado pelo parâmetro UserSharedWith. |
| Site_Url | O URL do site onde está localizado o ficheiro ou pasta acedido pelo utilizador. |
| FonteFileExtension | A extensão do ficheiro que foi acedido pelo utilizador. Esta propriedade está em branco se o objeto que foi acedido for uma pasta. |
| Nome SourceFile |     O nome do ficheiro ou pasta acedido pelo utilizador. |
| FonteRelativeUrl | O URL da pasta que contém o ficheiro acedido pelo utilizador. A combinação dos valores para os parâmetros SiteURL, SourceRelativeURL e SourceFileName é o mesmo que o valor para a propriedade ObjectID, que é o nome de caminho completo para o ficheiro acedido pelo utilizador. |
| Partilha de UtilizadorCom |     O utilizador com o que um recurso foi partilhado. |




## <a name="sample-log-queries"></a>Consultas de registo de amostras

A tabela seguinte fornece consultas de registo de amostras para registos de atualização recolhidos por esta solução.

| Consulta | Description |
| --- | --- |
|Conte de todas as operações na subscrição do seu Office 365 |OfficeActivity &#124; resumir a contagem() por Operação |
|Utilização de sites SharePoint|OfficeActivity &#124; onde OfficeWorkload =~ "sharepoint" &#124; resumir a contagem() por SiteUrl \| sortido por Count asc|
|Operações de acesso a ficheiros por tipo de utilizador | OfficeActivity &#124; resumir a contagem() por UserType |
|Monitorizar as ações externas em troca|OfficeActivity &#124; onde OfficeWorkload =~ "exchange" e ExternalAccess == verdadeiro|



## <a name="next-steps"></a>Passos seguintes

* Utilize [consultas de registo no Azure Monitor](../log-query/log-query-overview.md) para ver dados de atualização detalhados.
* [Crie os seus próprios dashboards](../learn/tutorial-logs-dashboards.md) para exibir as suas consultas de pesquisa favoritas do Office 365.
* [Criar alertas](../platform/alerts-overview.md) para ser notificado proativamente de importantes atividades do Office 365.  
