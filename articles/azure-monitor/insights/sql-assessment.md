---
title: Otimize o seu ambiente SQL Server com o Monitor Azure [ Microsoft Docs
description: Com o Monitor Azure, pode utilizar a solução SQL Health Check para avaliar o risco e a saúde dos seus ambientes num intervalo regular.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/28/2019
ms.openlocfilehash: ceaed0800df01bf2c44fee13d98b01b6e726200d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77662489"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Otimize o seu ambiente SQL com a solução SQL Server Health Check no Monitor Azure

![Símbolo de verificação de saúde SQL](./media/sql-assessment/sql-assessment-symbol.png)

Pode utilizar a solução SQL Health Check para avaliar o risco e a saúde dos ambientes do seu servidor num intervalo regular. Este artigo irá ajudá-lo a instalar a solução para que possa tomar medidas corretivas para potenciais problemas.

Esta solução fornece uma lista prioritária de recomendações específicas para a sua infraestrutura de servidores implementadas. As recomendações são categorizadas em seis áreas de foco que o ajudam a entender rapidamente o risco e a tomar medidas corretivas.

As recomendações feitas baseiam-se no conhecimento e experiência adquiridos pelos engenheiros da Microsoft a partir de milhares de visitas de clientes. Cada recomendação fornece orientações sobre o porquê de um problema poder ser importante para si e como implementar as alterações sugeridas.

Você pode escolher áreas de foco que são mais importantes para a sua organização e acompanhar o seu progresso para executar um ambiente livre de riscos e saudável.

Depois de ter adicionado a solução e de ser concluída uma avaliação, são mostradas informações sumárias para áreas de foco no painel de verificação de **saúde SQL** para a infraestrutura no seu ambiente. As seguintes secções descrevem como usar as informações no painel de verificação de **saúde SQL,** onde pode ver e, em seguida, tomar medidas recomendadas para a sua infraestrutura SQL Server.

![imagem do azulejo SQL Health Check](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![imagem do painel de verificação de saúde SQL](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Pré-requisitos

* A solução SQL Health Check requer uma versão suportada do .NET Framework 4.6.2 instalado em cada computador que tenha o Microsoft Monitoring Agent (MMA) instalado.  O agente MMA é utilizado pelo System Center 2016 - Diretor de Operações e Gestor de Operações 2012 R2 e Azure Monitor.  
* A solução suporta a versão SQL Server 2012, 2014 e 2016.
* Um espaço de trabalho de Log Analytics para adicionar a solução SQL Health Check do mercado Azure no portal Azure.  Para instalar a solução, deve ser administrador ou colaborador na subscrição do Azure.

  > [!NOTE]
  > Depois de ter adicionado a solução, o ficheiro AdvisorAssessment.exe é adicionado aos servidores com agentes. Os dados de configuração são lidos e depois enviados para o Monitor Azure na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço cloud regista os dados.
  >
  >

Para efetuar a verificação de saúde contra os seus servidores SQL Server, eles requerem um agente e conectividade para o Monitor Azure usando um dos seguintes métodos suportados:

1. Instale o [Microsoft Monitoring Agent (MMA)](../../azure-monitor/platform/agent-windows.md) se o servidor ainda não estiver monitorizado pelo System Center 2016 - Diretor de Operações ou Gestor de Operações 2012 R2.
2. Se for monitorizado com o System Center 2016 - Diretor de Operações ou Gestor de Operações 2012 R2 e o grupo de gestão não estiver integrado no Monitor Azure, o servidor pode ser multi-alojado com o Log Analytics para recolher dados e encaminhar-se para o serviço e ainda ser monitorizado pelo Gestor de Operações.  
3. Caso contrário, se o seu grupo de gestão do Gestor de Operações estiver integrado ao serviço, terá de adicionar os controladores de domínio para recolha de dados pelo serviço, seguindo os passos em [computadores geridos por agentes adicionais](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) depois de ativar a solução no seu espaço de trabalho.  

O agente no seu Servidor SQL que reporta a um grupo de gestão do Gestor de Operações, recolhe dados, reencaminha para o seu servidor de gestão atribuído, e depois é enviado diretamente de um servidor de gestão para o Monitor Azure.  Os dados não estão escritos nas bases de dados do Gestor de Operações.  

Se o Servidor SQL for monitorizado pelo Gestor de Operações, é necessário configurar um Gestor de Operações Executar Como conta. Consulte as contas de [execução do Gestor de Operações para](#operations-manager-run-as-accounts-for-log-analytics) o Monitor Azure abaixo para obter mais informações.

## <a name="sql-health-check-data-collection-details"></a>Detalhes da recolha de dados da Verificação de Estado de Funcionamento do SQL
O SQL Health Check recolhe dados das seguintes fontes utilizando o agente que ativou:

* Windows Management Instrumentation (WMI)
* Registo
* Contadores de desempenho
* Resultados da visão dinâmica de gestão dinâmica do SQL Server

Os dados são recolhidos no Servidor SQL e encaminhados para log Analytics de sete em sete dias.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Contas Run As do Operations Manager para o Log Analytics
O Log Analytics utiliza o agente e grupo de gestão do Gestor de Operações para recolher e enviar dados para o serviço Log Analytics. O Log Analytics baseia-se em pacotes de gestão para cargas de trabalho para fornecer serviços de valor acrescentado. Cada carga de trabalho requer privilégios específicos de carga de trabalho para executar pacotes de gestão num contexto de segurança diferente, como uma conta de utilizador de domínio. Você precisa fornecer informações credenciais configurando um Gestor de Operações Executar Como conta.

Utilize as seguintes informações para definir o Executar do Gestor de Operações Como conta para o SQL Health Check.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Definir a conta Run As para a Verificação de Estado de Funcionamento do SQL
 Se já estiver a utilizar o pacote de gestão do SQL Server, deve utilizar esse Executar Como configuração.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Para configurar a execução SQL Como conta na consola Operações
> [!NOTE]
> Por incumprimento, os fluxos de trabalho no pacote de gestão funcionam no contexto de segurança da conta do Sistema Local. Se estiver a utilizar o Agente de Monitorização da Microsoft ligado diretamente ao serviço em vez de reportar diretamente a um grupo de gestão do Gestor de Operações, ignore os passos 1-5 abaixo e execute a amostra T-SQL ou PowerShell, especificando nt AUTHORITY\SYSTEM como o nome do utilizador.
>
>

1. No Gestor de Operações, abra a consola de Operações e, em seguida, clique em **Administração**.
2. Sob **execução Como configuração,** clique em **Perfis**e abra **a avaliação sql como perfil**.
3. Na página **Contas Run As**, clique em **Adicionar**.
4. Selecione um Windows Run Como conta que contenha as credenciais necessárias para o Servidor SQL, ou clique em **New** para criar um.

   > [!NOTE]
   > O tipo de conta Executar As deve ser windows. A conta Run As também deve fazer parte do grupo de Administradores Locais em todos os Servidores Do Windows que hospedam instâncias de servidor SQL.
   >
   >
5. Clique em **Guardar**.
6. Modificar e, em seguida, executar a seguinte amostra T-SQL em cada instância do Servidor SQL para conceder permissões mínimas necessárias para a Conta Run As para realizar a verificação de saúde. No entanto, não precisa de o fazer se uma Conta Run As já fizer parte do papel do servidor de sysadmin em instâncias do Servidor SQL.

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

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Para configurar o SQL Run As conta utilizando o Windows PowerShell
Abra uma janela PowerShell e execute o seguinte script depois de atualizá-lo com as suas informações:

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
As ponderações são valores agregados com base em três factores-chave:

* A *probabilidade de* um problema identificado causar problemas. Uma maior probabilidade equivale a uma pontuação global maior para a recomendação.
* O *impacto* do problema na sua organização se causar um problema. Um impacto maior equivale a uma pontuação global maior para a recomendação.
* O *esforço* necessário para implementar a recomendação. Um esforço mais elevado equivale a uma pontuação global menor para a recomendação.

A ponderação para cada recomendação é expressa em percentagem da pontuação total disponível para cada área de foco. Por exemplo, se uma recomendação na área de foco de Segurança e Conformidade tiver uma pontuação de 5%, implementando essa recomendação aumentará a sua pontuação global de Segurança e Conformidade em 5%.

### <a name="focus-areas"></a>Áreas em foco
**Segurança e Conformidade** - Esta área de foco apresenta recomendações para potenciais ameaças e violações de segurança, políticas corporativas e requisitos técnicos, legais e regulamentares de conformidade.

**Disponibilidade e Continuidade de Negócios** - Esta área de foco mostra recomendações para disponibilidade de serviço, resiliência da sua infraestrutura e proteção empresarial.

**Desempenho e Escalabilidade** - Esta área de foco mostra recomendações para ajudar a infraestrutura de TI da sua organização a crescer, garantir que o seu ambiente de TI satisfaz os requisitos de desempenho atuais, e é capaz de responder às necessidades de infraestrutura em mudança.

**Upgrade, Migração e Implantação** - Esta área de foco mostra recomendações para ajudá-lo a atualizar, migrar e implementar o SQL Server para a sua infraestrutura existente.

**Operações e Monitorização** - Esta área de foco apresenta recomendações para ajudar a agilizar as suas operações de TI, implementar a manutenção preventiva e maximizar o desempenho.

**Gestão de Alterações e Configurações** - Esta área de foco apresenta recomendações para ajudar a proteger as operações do dia-a-dia, garantir que as alterações não afetam negativamente a sua infraestrutura, estabelecer procedimentos de controlo de alterações e rastrear e auditar configurações do sistema.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Deve visar uma pontuação de 100% em cada uma das áreas em foco?
Não necessariamente. As recomendações baseiam-se nos conhecimentos e experiências adquiridas pelos engenheiros da Microsoft em milhares de visitas de clientes. No entanto, nenhuma infraestrutura de servidores é a mesma, e recomendações específicas podem ser mais ou menos relevantes para si. Por exemplo, algumas recomendações de segurança podem ser menos relevantes se as suas máquinas virtuais não estiverem expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para serviços que fornecem recolha e reporte de dados ad hoc de baixa prioridade. As questões que são importantes para um negócio maduro podem ser menos importantes para uma start-up. Você pode querer identificar quais as áreas de foco são as suas prioridades e, em seguida, olhar para como as suas pontuações mudam ao longo do tempo.

Todas as recomendações incluem orientações sobre o porquê de ser importante. Deve utilizar esta orientação para avaliar se a implementação da recomendação é adequada para si, dada a natureza dos seus serviços de TI e as necessidades empresariais da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Utilizar as recomendações das áreas em foco da verificação de estado de funcionamento
Antes de poder utilizar uma solução de avaliação no Monitor Azure, tem de ter a solução instalada.  Depois de instalado, pode ver o resumo das recomendações utilizando o azulejo SQL Health Check na página **de visão geral** do Monitor Azure no portal Azure.

Consulte as avaliações de conformidade resumidas para a sua infraestrutura e, em seguida, faça recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Ver recomendações para uma área de foco e tomar medidas corretivas
1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. No portal do Azure, clique em **Mais serviços**, que se encontra no canto inferior esquerdo. Na lista de recursos, escreva **Monitorizar**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Monitorizar**.
3. Na secção **Insights** do menu, selecione **Mais**.  
4. Na página **'Overview',** clique no azulejo **SQL Health Check.**
5. Na página **Health Check,** reveja as informações sumárias numa das lâminas da área de foco e clique numa para ver recomendações para essa área de foco.
6. Em qualquer uma das páginas da área de foco, você pode ver as recomendações prioritárias feitas para o seu ambiente. Clique numa recomendação em **Objetos Afetados** para ver detalhes sobre o porquê da recomendação ser feita.<br><br> ![imagem das recomendações do SQL Health Check](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Pode tomar medidas corretivas sugeridas em **Ações Sugeridas**. Quando o item tiver sido abordado, avaliações posteriores registarão que foram tomadas as ações recomendadas e a sua pontuação de conformidade aumentará. Itens corrigidos aparecem como **Objetos Passados**.

## <a name="ignore-recommendations"></a>Ignorar recomendações
Se tiver recomendações que deseja ignorar, pode criar um ficheiro de texto que o Azure Monitor utilizará para evitar que as recomendações apareçam nos resultados da sua avaliação.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar recomendações que irá ignorar
1. No menu Do Monitor Azure, clique em **Registos**.
2. Utilize a seguinte consulta para listar recomendações que falharam para computadores no seu ambiente.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Aqui está uma imagem mostrando a consulta de registo:<br><br> ![recomendações falhadas](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Escolha recomendações que queira ignorar. Utilizará os valores para Recomendação id no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e utilizar um ficheiro de texto IgnoreRecommendations.txt
1. Crie um ficheiro chamado Ignorrecommendations.txt.
2. Colar ou digitar cada Recomendação Id para cada recomendação que pretende que o Monitor Azure ignore numa linha separada e, em seguida, guarde e feche o ficheiro.
3. Coloque o ficheiro na seguinte pasta em cada computador onde pretende que o Monitor Azure ignore as recomendações.
   * Em computadores com o Microsoft Monitoring Agent (ligado diretamente ou através do Gestor de Operações) - *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * No servidor de gestão do Gestor de Operações - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * No servidor de gestão do Gestor de Operações 2016 - *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas
1. Após a próxima avaliação programada, por defeito a cada 7 dias, as recomendações especificadas são marcadas ignoradas e não aparecerão no painel de avaliação.
2. Pode utilizar as seguintes consultas de Pesquisa de Registo para listar todas as recomendações ignoradas.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Se decidir mais tarde que pretende ver recomendações ignoradas, remova quaisquer ficheiros IgnoreRecommendations.txt ou pode remover recomendações das mesmas.

## <a name="sql-health-check-solution-faq"></a>FAQ sobre a solução Verificação de Estado de Funcionamento do SQL

*Que controlos são realizados pela solução de Avaliação SQL?*

* A seguinte consulta mostra uma descrição de todos os controlos atualmente realizados:

```Kusto
SQLAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Os resultados podem então ser exportados para o Excel para posterior revisão.


*Com que frequência funciona um exame de saúde?*

* O cheque é de sete em sete dias.

*Existe uma maneira de configurar a frequência com que o cheque corre?*

* Neste momento, não.

*Se outro servidor for descoberto depois de eu adicionar a solução SQL Health Check, será verificado?*

* Sim, uma vez descoberto que é verificado a partir daí, a cada sete dias.

*Se um servidor for desativado, quando será removido do exame de saúde?*

* Se um servidor não submeter dados durante 3 semanas, é removido.

*Qual é o nome do processo que faz a recolha de dados?*

* AdvisorAssessment.exe

*Quanto tempo demora a recolher dados?*

* A recolha real de dados no servidor demora cerca de 1 hora. Pode demorar mais tempo em servidores que tenham um grande número de instâncias ou bases de dados SQL.

*Que tipo de dados são recolhidos?*

* São recolhidos os seguintes tipos de dados:
  * WMI
  * Registo
  * Contadores de desempenho
  * Pontos de vista de gestão dinâmica SQL (DMV).

*Existe uma forma de configurar quando os dados são recolhidos?*

* Neste momento, não.

*Por que tenho que configurar uma conta de execução?*

* Para o SQL Server, um pequeno número de consultas SQL são executados. Para que possam ser executados, deve ser utilizada uma conta 'Executar As Conta Com VIEW SERVER STATE' para sQL.  Além disso, para consultar o WMI, são necessárias credenciais de administrador local.

*Por que exibir apenas as 10 melhores recomendações?*

* Em vez de lhe dar uma lista exaustiva e esmagadora de tarefas, recomendamos que se concentre em abordar as recomendações prioritárias primeiro. Depois de os abordar, serão disponibilizadas recomendações adicionais. Se preferir ver a lista detalhada, pode ver todas as recomendações utilizando a pesquisa de log Analytics.

*Há alguma maneira de ignorar uma recomendação?*

* Sim, consulte a secção de [recomendações de ignorar](#ignore-recommendations) acima.

## <a name="next-steps"></a>Passos seguintes
* [Faça consultas](../log-query/log-query-overview.md) para saber como analisar dados e recomendações detalhadas do SQL Health Check.
