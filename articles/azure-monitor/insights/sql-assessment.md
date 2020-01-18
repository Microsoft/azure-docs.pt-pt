---
title: Otimize seu ambiente de SQL Server com Azure Monitor | Microsoft Docs
description: Com o Azure Monitor, você pode usar a solução de verificação de integridade do SQL para avaliar o risco e a integridade de seus ambientes em intervalos regulares.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/28/2019
ms.openlocfilehash: 23b1391033713fc8eeccf2d0872c49a4291b8292
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76168898"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Otimize seu ambiente SQL com a solução de verificação de integridade SQL Server no Azure Monitor

![Símbolo de verificação de integridade do SQL](./media/sql-assessment/sql-assessment-symbol.png)

Você pode usar a solução verificação de integridade do SQL para avaliar o risco e a integridade de seus ambientes de servidor em intervalos regulares. Este artigo o ajudará a instalar a solução para que você possa tomar ações corretivas para possíveis problemas.

Essa solução fornece uma lista priorizada de recomendações específicas para sua infraestrutura de servidor implantado. As recomendações são categorizadas em seis áreas de foco que ajudam você a entender rapidamente o risco e a tomar medidas corretivas.

As recomendações feitas são baseadas no conhecimento e na experiência obtidas pelos engenheiros da Microsoft de milhares de visitas a clientes. Cada recomendação fornece orientação sobre por que um problema pode ser importante para você e como implementar as alterações sugeridas.

Você pode escolher as áreas de foco que são mais importantes para sua organização e acompanhar seu progresso em relação à execução de um ambiente íntegro e sem riscos.

Depois de adicionar a solução e uma avaliação ser concluída, as informações resumidas para áreas de foco são mostradas no painel de **verificação de integridade do SQL** para a infraestrutura em seu ambiente. As seções a seguir descrevem como usar as informações no painel de **verificação de integridade do SQL** , onde você pode exibir e, em seguida, executar as ações recomendadas para sua infraestrutura de SQL Server.

![imagem do bloco verificação de integridade do SQL](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![imagem do painel de verificação da integridade do SQL](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Pré-requisitos

* A solução de verificação de integridade do SQL requer uma versão com suporte do .NET Framework 4.6.2 instalada em cada computador que tenha o Microsoft Monitoring Agent (MMA) instalado.  O agente MMA é usado pelo System Center 2016-Operations Manager e Operations Manager 2012 R2 e Azure Monitor.  
* A solução dá suporte à SQL Server versão 2012, 2014 e 2016.
* Um espaço de trabalho Log Analytics para adicionar a solução de verificação de integridade do SQL do Azure Marketplace no portal do Azure.  Para instalar a solução, você deve ser um administrador ou colaborador na assinatura do Azure.

  > [!NOTE]
  > Depois de adicionar a solução, o arquivo AdvisorAssessment. exe é adicionado aos servidores com agentes. Os dados de configuração são lidos e enviados para Azure Monitor na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço cloud regista os dados.
  >
  >

Para executar a verificação de integridade em seus servidores de SQL Server, eles exigem um agente e conectividade para Azure Monitor usando um dos seguintes métodos com suporte:

1. Instale o [Microsoft Monitoring Agent (MMA)](../../azure-monitor/platform/agent-windows.md) se o servidor ainda não estiver monitorado pelo System Center 2016-Operations Manager ou Operations Manager 2012 R2.
2. Se ele for monitorado com o System Center 2016-Operations Manager ou Operations Manager 2012 R2 e o grupo de gerenciamento não estiver integrado com o Azure Monitor, o servidor poderá ser multihomed com Log Analytics para coletar dados e encaminhar para o serviço e ainda ser monitorado por Operations Manager.  
3. Caso contrário, se o grupo de gerenciamento de Operations Manager estiver integrado ao serviço, você precisará adicionar os controladores de domínio para coleta de dados pelo serviço seguindo as etapas em [Adicionar computadores gerenciados por agente](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) depois de habilitar a solução em seu espaço de trabalho.  

O agente no SQL Server que relata para um grupo de gerenciamento de Operations Manager, coleta dados, encaminha para seu servidor de gerenciamento atribuído e é enviado diretamente de um servidor de gerenciamento para Azure Monitor.  Os dados não são gravados nos bancos de dado do Operations Manager.  

Se o SQL Server for monitorado pelo Operations Manager, você precisará configurar uma conta Executar como Operations Manager. Consulte [Operations Manager contas Executar como para Azure monitor](#operations-manager-run-as-accounts-for-log-analytics) abaixo para obter mais informações.

## <a name="sql-health-check-data-collection-details"></a>Detalhes da coleta de dados da verificação de integridade do SQL
A verificação de integridade do SQL coleta dados das seguintes fontes usando o agente que você habilitou:

* Windows Management Instrumentation (WMI)
* Registo
* Contadores de desempenho
* SQL Server resultados da exibição de gerenciamento dinâmico

Os dados são coletados na SQL Server e encaminhados para Log Analytics a cada sete dias.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Operations Manager contas Executar como para Log Analytics
Log Analytics usa o agente de Operations Manager e o grupo de gerenciamento para coletar e enviar dados para o serviço de Log Analytics. Log Analytics baseia-se nos pacotes de gerenciamento para cargas de trabalho para fornecer serviços de valor agregado. Cada carga de trabalho requer privilégios específicos de carga de trabalho para executar pacotes de gerenciamento em um contexto de segurança diferente, como uma conta de usuário de domínio. Você precisa fornecer informações de credenciais Configurando uma conta Executar como Operations Manager.

Use as informações a seguir para definir a conta Executar como Operations Manager para verificação de integridade do SQL.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Definir a conta Executar como para verificação de integridade do SQL
 Se você já estiver usando o pacote de gerenciamento SQL Server, deverá usar essa configuração executar como.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Para configurar a conta Executar como do SQL no console de operações
> [!NOTE]
> Por padrão, os fluxos de trabalho no pacote de gerenciamento são executados no contexto de segurança da conta do sistema local. Se você estiver usando o Microsoft Monitoring Agent conectado diretamente ao serviço, em vez de reportar diretamente a um grupo de gerenciamento de Operations Manager, pule as etapas 1-5 abaixo e execute o exemplo de T-SQL ou PowerShell, especificando NT AUTHORITY\SYSTEM como o nome de usuário.
>
>

1. No Operations Manager, abra o console de operações e clique em **Administração**.
2. Em **configuração de executar como**, clique em **perfis**e abra **avaliação do SQL perfil executar como**.
3. Na página **Contas Run As**, clique em **Adicionar**.
4. Selecione uma conta Executar como do Windows que contenha as credenciais necessárias para SQL Server ou clique em **novo** para criar uma.

   > [!NOTE]
   > O tipo de conta Executar como deve ser Windows. A conta Executar como também deve fazer parte do grupo de administradores locais em todos os servidores Windows que hospedam SQL Server instâncias.
   >
   >
5. Clique em **Guardar**.
6. Modifique e execute o seguinte exemplo de T-SQL em cada instância de SQL Server para conceder permissões mínimas necessárias para a conta Executar como executar a verificação de integridade. No entanto, você não precisa fazer isso se uma conta Executar como já faz parte da função de servidor sysadmin em instâncias de SQL Server.

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

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Para configurar a conta Executar como do SQL usando o Windows PowerShell
Abra uma janela do PowerShell e execute o script a seguir depois de atualizá-lo com suas informações:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Entendendo como as recomendações são priorizadas
Cada recomendação feita recebe um valor de peso que identifica a importância relativa da recomendação. Somente as dez recomendações mais importantes são mostradas.

### <a name="how-weights-are-calculated"></a>Como os pesos são calculados
Os pesos são valores agregados com base em três fatores-chave:

* A *probabilidade* de que um problema identificado causará problemas. Uma probabilidade mais alta é igual a uma pontuação geral maior para a recomendação.
* O *impacto* do problema na sua organização se isso causar um problema. Um impacto maior é igual a uma pontuação geral maior para a recomendação.
* O *esforço* necessário para implementar a recomendação. Um esforço mais alto é igual a uma pontuação geral menor para a recomendação.

O peso de cada recomendação é expresso como um percentual da pontuação total disponível para cada área de foco. Por exemplo, se uma recomendação na área de foco de segurança e conformidade tiver uma pontuação de 5%, implementar essa recomendação aumentará sua pontuação geral de segurança e conformidade em 5%.

### <a name="focus-areas"></a>Áreas de foco
**Segurança e conformidade** – essa área de foco mostra recomendações para possíveis ameaças à segurança e violações, políticas corporativas e requisitos de conformidade técnica, legal e regulatório.

**Disponibilidade e continuidade dos negócios** – essa área de foco mostra recomendações para disponibilidade de serviço, resiliência de sua infraestrutura e proteção de negócios.

**Desempenho e escalabilidade** -essa área de foco mostra recomendações para ajudar a expansão da infraestrutura de ti de sua organização, garantir que seu ambiente de ti atenda aos requisitos de desempenho atuais e que seja capaz de responder às necessidades de infraestrutura em constante mudança.

**Atualização, migração e implantação** – essa área de foco mostra recomendações para ajudá-lo a atualizar, migrar e implantar SQL Server em sua infraestrutura existente.

**Operações e monitoramento** – essa área de foco mostra recomendações para ajudar a simplificar suas operações de ti, implementar a manutenção preventiva e maximizar o desempenho.

**Gerenciamento de alterações e configurações** – essa área de foco mostra recomendações para ajudar a proteger as operações diárias, garantir que as alterações não afetem negativamente sua infraestrutura, estabelecer procedimentos de controle de alterações e rastrear e auditar as configurações do sistema.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Você deve visar a pontuação de 100% em cada área de foco?
Não necessariamente. As recomendações são baseadas no conhecimento e nas experiências obtidas pelos engenheiros da Microsoft em milhares de visitas a clientes. No entanto, não há duas infraestruturas de servidor iguais, e recomendações específicas podem ser mais ou menos relevantes para você. Por exemplo, algumas recomendações de segurança podem ser menos relevantes se suas máquinas virtuais não estiverem expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para serviços que fornecem relatórios e coleta de dados ad hoc de baixa prioridade. Os problemas que são importantes para um negócio maduro podem ser menos importantes para uma inicialização. Talvez você queira identificar quais áreas de foco são suas prioridades e, em seguida, examinar como suas pontuações mudam ao longo do tempo.

Cada recomendação inclui diretrizes sobre por que é importante. Você deve usar essas diretrizes para avaliar se a implementação da recomendação é apropriada para você, considerando a natureza dos seus serviços de ti e as necessidades comerciais da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Usar recomendações da área de foco de verificação de integridade
Para poder usar uma solução de avaliação no Azure Monitor, você deve ter a solução instalada.  Após a instalação, você pode exibir o resumo das recomendações usando o bloco verificação de integridade do SQL na página **visão geral** para Azure Monitor no portal do Azure.

Exiba as avaliações de conformidade resumidas para sua infraestrutura e, em seguida, faça drill-in nas recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para exibir as recomendações para uma área de foco e tomar uma ação corretiva
1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. No portal do Azure, clique em **Mais serviços**, que se encontra no canto inferior esquerdo. Na lista de recursos, escreva **Monitorizar**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Monitorizar**.
3. Na seção **insights** do menu, selecione **mais**.  
4. Na página **visão geral** , clique no bloco **verificação de integridade do SQL** .
5. Na página **verificação de integridade** , examine as informações de resumo em uma das folhas da área de foco e clique em uma para exibir as recomendações para essa área de foco.
6. Em qualquer uma das páginas da área de foco, você pode exibir as recomendações priorizadas feitas para seu ambiente. Clique em uma recomendação em **objetos afetados** para exibir detalhes sobre por que a recomendação é feita.<br><br> ![imagem das recomendações de verificação de integridade do SQL](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Você pode tomar ações corretivas sugeridas em **ações sugeridas**. Quando o item for resolvido, as avaliações posteriores registrarão que as ações recomendadas foram executadas e a pontuação de conformidade aumentará. Os itens corrigidos aparecem como **objetos passados**.

## <a name="ignore-recommendations"></a>Ignorar recomendações
Se você tiver recomendações que deseja ignorar, poderá criar um arquivo de texto que Azure Monitor será usado para impedir que as recomendações apareçam nos resultados da avaliação.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar as recomendações que serão ignoradas
1. No menu Azure Monitor, clique em **logs**.
2. Use a consulta a seguir para listar as recomendações que falharam para computadores em seu ambiente.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Aqui está uma captura de tela mostrando a consulta de log:<br><br> ![recomendações com falha](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Escolha as recomendações que você deseja ignorar. Você usará os valores para a Recomendaçãoid no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e usar um arquivo de texto IgnoreRecommendations. txt
1. Crie um arquivo chamado IgnoreRecommendations. txt.
2. Cole ou digite cada Recomendaçãoid para cada recomendação que você deseja que Azure Monitor ignore em uma linha separada e, em seguida, salve e feche o arquivo.
3. Coloque o arquivo na pasta a seguir em cada computador em que você deseja que Azure Monitor ignore as recomendações.
   * Em computadores com o Microsoft Monitoring Agent (conectado diretamente ou por meio de Operations Manager)- *systemdrive*: \Program Files\Microsoft Monitoring Agent\Agent
   * Na Operations Manager servidor de gerenciamento- *systemdrive*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Na Operations Manager servidor de gerenciamento 2016- *systemdrive*: \Program Files\Microsoft System Center 2016 \ Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas
1. Após a execução da próxima avaliação agendada, por padrão a cada 7 dias, as recomendações especificadas são marcadas como ignoradas e não serão exibidas no painel de avaliação.
2. Você pode usar as consultas de pesquisa de log a seguir para listar todas as recomendações ignoradas.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Se você decidir posteriormente que deseja ver as recomendações ignoradas, remova todos os arquivos IgnoreRecommendations. txt ou remova o as recommendationids deles.

## <a name="sql-health-check-solution-faq"></a>Perguntas frequentes da solução de verificação da integridade do SQL

*Quais verificações são executadas pela solução de Avaliação do SQL?*

* A consulta a seguir mostra uma descrição de todas as verificações realizadas no momento:

```Kusto
SQLAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Os resultados podem ser exportados para o Excel para examinar detalhadamente.


*Com que frequência uma verificação de integridade é executada?*

* A verificação é executada a cada sete dias.

*Há uma maneira de configurar a frequência com que a verificação é executada?*

* Neste momento, não.

*Se outro servidor for descoberto após ter adicionado a solução de verificação de integridade do SQL, ele será verificado?*

* Sim, depois de ser descoberto, ele é verificado a partir de então, a cada sete dias.

*Se um servidor for encerrado, quando ele será removido da verificação de integridade?*

* Se um servidor não enviar dados por 3 semanas, ele será removido.

*Qual é o nome do processo que faz a coleta de dados?*

* AdvisorAssessment.exe

*Quanto tempo leva para os dados serem coletados?*

* A coleta de dados real no servidor leva cerca de 1 hora. Pode levar mais tempo em servidores que têm um grande número de instâncias ou bancos de dados SQL.

*Que tipo de dados é coletado?*

* Os seguintes tipos de dados são coletados:
  * WMI
  * Registo
  * Contadores de desempenho
  * Exibições de gerenciamento dinâmico do SQL (DMV).

*Há uma maneira de configurar quando os dados são coletados?*

* Neste momento, não.

*Por que é necessário configurar uma conta Executar como?*

* Por SQL Server, um pequeno número de consultas SQL é executado. Para que eles sejam executados, uma conta Executar como com permissões Exibir estado do servidor para SQL deve ser usada.  Além disso, para consultar o WMI, são necessárias credenciais de administrador local.

*Por que exibir apenas as 10 principais recomendações?*

* Em vez de fornecer uma lista exaustiva de tarefas, recomendamos que você se concentre primeiro em abordar as recomendações priorizadas. Depois de solucioná-los, recomendações adicionais ficarão disponíveis. Se você preferir ver a lista detalhada, poderá exibir todas as recomendações usando a pesquisa de log Log Analytics.

*Há uma maneira de ignorar uma recomendação?*

* Sim, consulte a seção [ignorar recomendações](#ignore-recommendations) acima.

## <a name="next-steps"></a>Passos seguintes
* [Registre consultas](../log-query/log-query-overview.md) para saber como analisar dados e recomendações de verificação de integridade do SQL detalhados.
