---
title: Otimize o seu ambiente de servidor SQL com | do Azure Monitor Microsoft Docs
description: Com o Azure Monitor, pode utilizar a solução SQL Health Check para avaliar o risco e a saúde dos seus ambientes num intervalo regular.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: 0f2319ea6ba314c08a67651667837f05df5765a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101723236"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Otimize o seu ambiente SQL com a solução SQL Server Health Check no Azure Monitor

![Símbolo de Verificação de Saúde SQL](./media/sql-assessment/sql-assessment-symbol.png)

Pode utilizar a solução SQL Health Check para avaliar o risco e a saúde dos ambientes do seu servidor num intervalo regular. Este artigo irá ajudá-lo a instalar a solução para que possa tomar ações corretivas para potenciais problemas.

Esta solução fornece uma lista prioritária de recomendações específicas à infraestrutura de servidores implementadas. As recomendações são categorizadas em seis áreas de foco que o ajudam a entender rapidamente o risco e a tomar medidas corretivas.

As recomendações feitas baseiam-se no conhecimento e experiência adquiridos pelos engenheiros da Microsoft a partir de milhares de visitas ao cliente. Cada recomendação fornece orientações sobre o porquê de um problema poder ser importante para si e como implementar as alterações sugeridas.

Você pode escolher áreas de foco que são mais importantes para a sua organização e acompanhar o seu progresso para executar um ambiente livre de riscos e saudável.

Depois de ter adicionado a solução e de ter concluído uma avaliação, são mostradas informações sumárias para áreas de foco no painel **de controlo de saúde SQL** para a infraestrutura no seu ambiente. As secções seguintes descrevem como utilizar as informações no painel de controlo **de saúde SQL,** onde pode ver e, em seguida, tomar as ações recomendadas para a sua infraestrutura do SQL Server.

![imagem do azulejo sql Health Check](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![imagem do painel de verificação de saúde SQL](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Pré-requisitos

* A solução SQL Health Check requer uma versão suportada do Quadro .NET 4.6.2 instalado em cada computador que tenha o Microsoft Monitoring Agent (MMA) instalado.  O agente MMA é utilizado pelo System Center 2016 - Gestor de Operações e Gestor de Operações 2012 R2 e Azure Monitor.  
* A solução suporta a versão SQL Server 2012, 2014, 2016, 2017 e 2019.
* Um espaço de trabalho Log Analytics para adicionar a solução SQL Health Check do mercado Azure no portal Azure. Para instalar a solução, tem de ser administrador ou colaborador na subscrição do Azure.

  > [!NOTE]
  > Depois de ter adicionado a solução, o ficheiro AdvisorAssessment.exe é adicionado aos servidores com agentes. Os dados de configuração são lidos e depois enviados para o Azure Monitor na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço cloud regista os dados.
  >
  >

Para efetuar a verificação de saúde contra os seus servidores SQL Server, eles requerem um agente e conectividade ao Azure Monitor usando um dos seguintes métodos suportados:

1. Instale o [Microsoft Monitoring Agent (MMA)](../agents/agent-windows.md) se o servidor ainda não estiver monitorizado pelo System Center 2016 - Gestor de Operações ou Gestor de Operações 2012 R2.
2. Se for monitorizado com o System Center 2016 - Gestor de Operações ou Gestor de Operações 2012 R2 e o grupo de gestão não estiver integrado com o Azure Monitor, o servidor pode ser multi-acotado com o Log Analytics para recolher dados e encaminhar para o serviço e ainda ser monitorizado pelo Gestor de Operações.  
3. Caso contrário, se o seu grupo de gestão de Gestor de Operações estiver integrado no serviço, tem de adicionar os controladores de domínio para recolha de dados pelo serviço seguindo os passos em [adicionar computadores geridos por agentes](../agents/om-agents.md#connecting-operations-manager-to-azure-monitor) depois de ativar a solução no seu espaço de trabalho.  

O agente no seu SQL Server que reporta a um grupo de gestão de Gestores de Operações, recolhe dados, encaminha para o seu servidor de gestão atribuído e, em seguida, é enviado diretamente de um servidor de gestão para o Azure Monitor.  Os dados não são escritos nas bases de dados do Gestor de Operações.  

Se o SQL Server for monitorizado pelo Gestor de Operações, tem de configurar uma conta do Gestor de Operações Como conta. Consulte as [contas do Azure Monitor abaixo para](#operations-manager-run-as-accounts-for-log-analytics) obter mais informações.

## <a name="sql-health-check-data-collection-details"></a>Detalhes da recolha de dados da Verificação de Estado de Funcionamento do SQL
O SQL Health Check recolhe dados das seguintes fontes utilizando o agente que ativou:

* Windows Management Instrumentation (WMI)
* Registo
* Contadores de desempenho
* Resultados da visão dinâmica da gestão do SQL Server

Os dados são recolhidos no SQL Server e reencaminhados para Registar Analytics a cada sete dias.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Contas Run As do Operations Manager para o Log Analytics
O Log Analytics utiliza o agente e o grupo de gestão do Gestor de Operações para recolher e enviar dados para o serviço Log Analytics. O Log Analytics baseia-se em pacotes de gestão para cargas de trabalho para fornecer serviços de valor acrescentado. Cada carga de trabalho requer privilégios específicos da carga de trabalho para executar pacotes de gestão num contexto de segurança diferente, como uma conta de utilizador de domínio. Tem de fornecer informações credenciais configurando uma conta de Gestor de Operações Como.

Utilize as seguintes informações para definir o Gestor de Operações executado como conta para o SQL Health Check.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Definir a conta Run As para a Verificação de Estado de Funcionamento do SQL
 Se já estiver a utilizar o pacote de gestão do SQL Server, deve utilizar a configuração Run As.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Para configurar a sql run como conta na consola Operações
> [!NOTE]
> Por defeito, os fluxos de trabalho no pacote de gestão funcionam no contexto de segurança da conta do Sistema Local. Se estiver a utilizar o Agente de Monitorização da Microsoft ligado diretamente ao serviço em vez de reportar diretamente a um grupo de gestão de Gestores de Operações, salte os passos 1-5 abaixo e execute a amostra T-SQL ou PowerShell, especificando NT AUTHORITY\SYSTEM como o nome de utilizador.
>
>

1. Em Gestor de Operações, abra a consola operações e, em seguida, clique em **Administração**.
2. Em **Execução como configuração**, clique em **Perfis** e abra **a avaliação do SQL executada como perfil**.
3. Na página **Contas Run As**, clique em **Adicionar**.
4. Selecione uma conta Windows Run Como que contenha as credenciais necessárias para o SQL Server, ou clique em **Novo** para criar uma.

   > [!NOTE]
   > O tipo de conta Run As deve ser Windows. A conta Run As também deve fazer parte do grupo de Administradores Locais em todos os Servidores do Windows que hospedam as instâncias do servidor SQL.
   >
   >
5. Clique em **Guardar**.
6. Modifique e execute a seguinte amostra T-SQL em cada instância do SQL Server para conceder as permissões mínimas necessárias para a Conta Run As para realizar o exame de saúde. No entanto, não precisa de o fazer se uma Conta Run As já fizer parte da função do servidor Sysadmin em instâncias do SQL Server.

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Para configurar a sql run Como conta usando o Windows PowerShell
Abra uma janela PowerShell e execute o seguinte script depois de o atualizar com as suas informações:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Compreender como é definida a prioridade das recomendações
Todas as recomendações feitas recebem um valor de ponderação que identifica a importância relativa da recomendação. Apenas são apresentadas as dez recomendações mais importantes.

### <a name="how-weights-are-calculated"></a>Método de cálculo das ponderações
As ponderações são valores agregados baseados em três factores-chave:

* A *probabilidade de* um problema identificado causar problemas. Uma maior probabilidade equivale a uma pontuação global maior para a recomendação.
* O *impacto* do problema na sua organização se isso causar um problema. Um impacto mais elevado equivale a uma pontuação global maior para a recomendação.
* O *esforço* necessário para implementar a recomendação. Um esforço mais elevado equivale a uma pontuação global menor para a recomendação.

A ponderação de cada recomendação é expressa em percentagem da pontuação total disponível para cada área de foco. Por exemplo, se uma recomendação na área de foco de Segurança e Conformidade tiver uma pontuação de 5%, implementar essa recomendação aumentará a sua pontuação global de Segurança e Conformidade em 5%.

### <a name="focus-areas"></a>Áreas em foco
**Segurança e Conformidade** - Esta área de foco apresenta recomendações para potenciais ameaças à segurança e violações, políticas corporativas e requisitos técnicos, legais e regulamentares de conformidade.

**Disponibilidade e Continuidade empresarial** - Esta área de foco mostra recomendações para disponibilidade de serviços, resiliência da sua infraestrutura e proteção empresarial.

**Performance e Escalaability** - Esta área de foco mostra recomendações para ajudar a infraestrutura de TI da sua organização a crescer, garantir que o seu ambiente de TI cumpre os requisitos de desempenho atuais, e é capaz de responder às necessidades de infraestrutura em mudança.

**Upgrade, Migração e Implementação** - Esta área de foco mostra recomendações para ajudá-lo a atualizar, migrar e implementar o SQL Server para a sua infraestrutura existente.

**Operações e Monitorização** - Esta área de foco mostra recomendações para ajudar a agilizar as suas operações de TI, implementar a manutenção preventiva e maximizar o desempenho.

**Gestão de Alterações e Configurações** - Esta área de foco apresenta recomendações para ajudar a proteger as operações do dia-a-dia, garantir que as alterações não afetam negativamente a sua infraestrutura, estabelecer procedimentos de controlo de alterações e rastrear e auditar configurações do sistema.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Deve visar uma pontuação de 100% em cada uma das áreas em foco?
Não necessariamente. As recomendações baseiam-se nos conhecimentos e experiências adquiridas pelos engenheiros da Microsoft em milhares de visitas ao cliente. No entanto, não existem duas infraestruturas de servidores iguais, e recomendações específicas podem ser mais ou menos relevantes para si. Por exemplo, algumas recomendações de segurança podem ser menos relevantes se as suas máquinas virtuais não estiverem expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para serviços que fornecem baixa prioridade a recolha e reporte de dados ad hoc. As questões que são importantes para um negócio maduro podem ser menos importantes para uma start-up. Pode querer identificar quais as áreas de foco que são as suas prioridades e, em seguida, ver como as suas pontuações mudam ao longo do tempo.

Todas as recomendações incluem orientações sobre o porquê de ser importante. Deve utilizar esta orientação para avaliar se a implementação da recomendação é adequada para si, dada a natureza dos seus serviços de TI e as necessidades empresariais da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Utilizar as recomendações das áreas em foco da verificação de estado de funcionamento
Antes de utilizar uma solução de avaliação no Azure Monitor, tem de ter a solução instalada.  Depois de instalado, pode ver o resumo das recomendações utilizando o azulejo SQL Health Check na página **'Visão Geral'** do Monitor Azure no portal Azure.

Veja as avaliações de conformidade resumidas para a sua infraestrutura e, em seguida, faça recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para ver recomendações para uma área de foco e tomar medidas corretivas
1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. No portal do Azure, clique em **Mais serviços**, que se encontra no canto inferior esquerdo. Na lista de recursos, escreva **Monitorizar**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Monitorizar**.
3. Na secção **Insights** do menu, selecione **Mais**.  
4. Na página **'Visão Geral',** clique no azulejo **SQL Health Check.**
5. Na página **Health Check,** reveja a informação do resumo numa das lâminas da área de foco e, em seguida, clique numa para ver recomendações para essa área de foco.
6. Em qualquer uma das páginas da área de foco, você pode ver as recomendações prioritárias feitas para o seu ambiente. Clique numa recomendação em **Objetos Afetados** para ver detalhes sobre o porquê da recomendação ser feita.<br><br> ![imagem das recomendações do SQL Health Check](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Pode tomar medidas corretivas sugeridas em **Ações Sugeridas**. Quando o artigo tiver sido abordado, as avaliações posteriores registarão que foram tomadas as ações recomendadas e a sua pontuação de conformidade aumentará. Os itens corrigidos aparecem como **Objetos Passados.**

## <a name="ignore-recommendations"></a>Ignorar recomendações
Se tiver recomendações que pretende ignorar, pode criar um ficheiro de texto que o Azure Monitor utilizará para impedir que as recomendações apareçam nos resultados da sua avaliação.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar recomendações que irá ignorar
1. No menu Azure Monitor, clique em **Registars**.
2. Utilize a seguinte consulta para listar recomendações que falharam para computadores no seu ambiente.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Aqui está uma imagem mostrando a consulta de registo:<br><br> ![recomendações falhadas](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Escolha recomendações que queira ignorar. Usará os valores para RecomendaçãoId no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e utilizar um ficheiro de texto IgnoreRecommendations.txt
1. Crie um ficheiro chamado IgnoreRecommendations.txt.
2. Cole ou digite cada RecomendaçãoId para cada recomendação que pretende que o Azure Monitor ignore numa linha separada e, em seguida, guarde e feche o ficheiro.
3. Coloque o ficheiro na seguinte pasta em cada computador onde pretende que o Azure Monitor ignore as recomendações.
   * Nos computadores com o Agente de Monitorização da Microsoft (ligado diretamente ou através do Gestor de Operações) - *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * No servidor de gestão do Gestor de Operações - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * No servidor de gestão do Gestor de Operações 2016 - *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas
1. Após a próxima avaliação programada ser executado, por padrão a cada 7 dias, as recomendações especificadas são marcadas ignoradas e não aparecerão no painel de avaliação.
2. Pode utilizar as seguintes consultas de Pesquisa de Registo para listar todas as recomendações ignoradas.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Se decidir mais tarde que pretende ver recomendações ignoradas, remova quaisquer IgnoreRecommendations.txt ficheiros ou poderá remover os Recomendações dos mesmos.

## <a name="sql-health-check-solution-faq"></a>FAQ sobre a solução Verificação de Estado de Funcionamento do SQL

*Que controlos são efetuados pela solução sql Assessment?*

* A seguinte consulta mostra uma descrição de todas as verificações atualmente efetuadas:

```Kusto
SQLAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Os resultados podem então ser exportados para o Excel para posterior revisão.


*Quantas vezes é que um exame de saúde é executado?*

* O cheque é de sete em sete dias.

*Há uma maneira de configurar com que frequência o cheque é executado?*

* Neste momento, não.

*Se outro servidor for descoberto depois de eu ter adicionado a solução SQL Health Check, será verificado?*

* Sim, uma vez descoberto, é verificado a partir daí, a cada sete dias.

*Se um servidor for desativado, quando será removido do exame de saúde?*

* Se um servidor não submeter dados durante 3 semanas, é removido.

*Qual é o nome do processo que faz a recolha de dados?*

* AdvisorAssessment.exe

*Quanto tempo demora a recolher os dados?*

* A recolha de dados real no servidor demora cerca de 1 hora. Pode demorar mais tempo em servidores que têm um grande número de casos SQL ou bases de dados.

*Que tipo de dados são recolhidos?*

* São recolhidos os seguintes tipos de dados:
  * WMI
  * Registo
  * Contadores de desempenho
  * Vistas dinâmicas de gestão SQL (DMV).

*Existe uma forma de configurar quando os dados são recolhidos?*

* Neste momento, não.

*Por que tenho que configurar uma conta run as?*

* Para o SQL Server, um pequeno número de consultas SQL são executadas. Para que possam funcionar, deve ser utilizada uma conta como conta com VISTA SERVER STATE para SQL.  Além disso, para consultar o WMI, são necessárias credenciais de administrador local.

*Porquê exibir apenas as 10 melhores recomendações?*

* Em vez de lhe dar uma lista exaustiva de tarefas, recomendamos que se concentre em abordar as recomendações prioritárias primeiro. Depois de os abordar, serão disponibilizadas recomendações adicionais. Se preferir ver a lista detalhada, pode ver todas as recomendações utilizando a pesquisa de registo do Log Analytics.

*Há alguma maneira de ignorar uma recomendação?*

* Sim, consulte a secção [de recomendações ignore](#ignore-recommendations) acima.

## <a name="next-steps"></a>Passos seguintes
* [Faça consultas](../logs/log-query-overview.md) para aprender a analisar dados e recomendações detalhadas do SQL Health Check.

