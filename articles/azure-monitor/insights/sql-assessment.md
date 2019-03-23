---
title: Otimizar seu ambiente do SQL Server com o Azure Log Analytics | Documentos da Microsoft
description: Com o Azure Log Analytics, pode utilizar a solução de verificação de estado de funcionamento do SQL para avaliar o risco e estado de funcionamento dos seus ambientes num intervalo regular.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: magoedte
ms.openlocfilehash: e8c06f0a3a33133c7b1595db52204d15b03d6aab
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372476"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-log-analytics"></a>Otimizar seu ambiente de SQL com a solução de verificação de estado de funcionamento do SQL Server no Log Analytics

![Símbolo de verificação de estado de funcionamento do SQL](./media/sql-assessment/sql-assessment-symbol.png)

Pode utilizar a solução de verificação de estado de funcionamento do SQL para avaliar o risco e estado de funcionamento dos seus ambientes de servidor num intervalo regular. Este artigo irá ajudá-lo a instalar a solução para que pode tomar medidas corretivas para potenciais problemas.

Esta solução fornece uma lista prioritária de recomendações específicas para sua infra-estrutura de servidor implementado. As recomendações são categorizadas em seis áreas de foco que ajudá-lo rapidamente entender o risco e tomar medidas corretivas.

As recomendações feitas baseiam-se o conhecimento e a experiência obtida pelos engenheiros da Microsoft de milhares de visitas a clientes. Cada recomendação fornece orientações sobre por que um problema poderá são importantes para si e como implementar as alterações sugeridas.

Pode escolher áreas de foco que são mais importantes para a sua organização e acompanhar seu progresso em relação a execução de um ambiente gratuito e em bom estado de risco.

Depois de adicionar a solução e uma avaliação é concluído, resumo informações para áreas de foco são mostradas no **verificação de estado de funcionamento do SQL** dashboard para a infraestrutura no seu ambiente. As secções seguintes descrevem como utilizar as informações sobre o **verificação de estado de funcionamento do SQL** dashboard, onde pode ver e, em seguida, efetuar as ações recomendadas para a sua infraestrutura do SQL Server.

![imagem do mosaico de verificação de estado de funcionamento do SQL](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![imagem do dashboard de verificação de estado de funcionamento do SQL](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Pré-requisitos

* A solução de verificação de estado de funcionamento do SQL requer uma versão suportada do .NET Framework 4 instalado em cada computador que tenha o Microsoft Monitoring Agent (MMA) instalado.  O agente MMA é utilizado pelo System Center 2016 - Operations Manager e Operations Manager 2012 R2 e o serviço Log Analytics.  
* A solução suporta a versão 2012, 2014 e 2016 do SQL Server.
* Uma área de trabalho do Log Analytics para adicionar a solução de verificação de estado de funcionamento do SQL no Azure marketplace no portal do Azure.  Para instalar a solução, tem de ser um administrador ou contribuinte na subscrição do Azure.

  > [!NOTE]
  > Depois de adicionar a solução, o ficheiro de AdvisorAssessment.exe é adicionado aos servidores com os agentes. Leia e, em seguida, enviados para o serviço de Log Analytics na cloud para processamento de dados de configuração. Lógica é aplicada para os dados recebidos e o serviço em nuvem regista os dados.
  >
  >

Para efetuar a verificação de estado de funcionamento em relação a seus servidores do SQL Server, necessitam de um agente e a conectividade para o Log Analytics através de um dos seguintes métodos suportados:

1. Instalar o [Microsoft Monitoring Agent (MMA)](../../azure-monitor/platform/agent-windows.md) se o servidor já não é monitorizado pelo System Center 2016 - Operations Manager ou do Operations Manager 2012 R2.
2. Se está a ser monitorizado com o System Center 2016 - Operations Manager ou do Operations Manager 2012 R2 e o grupo de gestão não estiver integrado com o serviço Log Analytics, o servidor pode ser multihomed com o Log Analytics para recolher dados e reencaminhar para o serviço e ainda monitorizados pelo Operations Manager.  
3. Caso contrário, se o grupo de gestão do Operations Manager está integrado com o serviço, terá de adicionar os controladores de domínio para a recolha de dados pelo serviço de seguir os passos em [adicionar computadores geridos por agente](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) depois de ativar a solução na sua área de trabalho.  

O agente no seu servidor SQL, os relatórios a um grupo de gestão do Operations Manager, recolhe os dados, por sua vez encaminha para o respetivo servidor de gestão atribuído e, em seguida, é enviado diretamente a partir de um servidor de gestão para o serviço Log Analytics.  Os dados não são gravados para as bases de dados do Operations Manager.  

Se o SQL Server é monitorizado pelo Operations Manager, terá de configurar uma Operations Manager conta Run As. Ver [contas do Operations Manager Run para o Log Analytics](#operations-manager-run-as-accounts-for-log-analytics) abaixo para obter mais informações.

## <a name="sql-health-check-data-collection-details"></a>Detalhes de recolha de dados SQL de verificação de estado de funcionamento
Verificação de estado de funcionamento do SQL recolhe dados das seguintes origens a utilizar o agente que tiver ativado:

* Windows Management Instrumentation (WMI)
* Registo
* Contadores de desempenho
* Ver resultados de gestão dinâmica do SQL Server

Dados são recolhidos no SQL Server e reencaminhados para o Log Analytics a cada sete dias.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Executar como contas do Operations Manager para o Log Analytics
O log Analytics utiliza o grupo de gestão e agente do Operations Manager para recolher e enviar dados para o serviço Log Analytics. É criado de análise do registo sobre pacotes de gestão para cargas de trabalho fornecer valor agregado dos serviços. Cada carga de trabalho requer privilégios de carga de trabalho específica para executar os pacotes de gestão num contexto de segurança diferentes, como uma conta de utilizador de domínio. Tem de fornecer informações de credenciais ao configurar uma conta do Operations Manager Run As.

Utilize as seguintes informações para configurar a Operations Manager conta Run As para verificação de estado de funcionamento do SQL.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Definir a conta Run As para verificação de estado de funcionamento do SQL
 Se já estiver a utilizar o pacote de gestão do SQL Server, deve usar essa configuração de Run As.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Para configurar a conta Run As do SQL na consola de operações
> [!NOTE]
> Por predefinição, o pacote de gestão de fluxos de trabalho é executado no contexto de segurança da conta do sistema Local. Se estiver a utilizar o Microsoft Monitoring Agent ligado diretamente ao serviço em vez relatórios diretamente a um grupo de gestão do Operations Manager, ignorar os passos 1 a 5 abaixo e execute o T-SQL ou o exemplo de PowerShell, especificando NT AUTHORITY\SYSTEM como o nome de utilizador.
>
>

1. No Operations Manager, abra a consola de operações e, em seguida, clique em **administração**.
2. Sob **configuração de Run As**, clique em **perfis**e abra **SQL avaliação perfil Run As**.
3. Sobre o **contas Run as** página, clique em **Add**.
4. Selecione uma conta Run As do Windows que contém as credenciais necessárias para o SQL Server, ou clique em **New** para criar um.

   > [!NOTE]
   > O tipo de conta Run As tem de ser Windows. A conta Run As tem de ser também parte do grupo de administradores Local em todos os servidores do Windows que aloja a instâncias do SQL Server.
   >
   >
5. Clique em **Guardar**.
6. Modificar e, em seguida, execute o seguinte exemplo de T-SQL em cada instância do SQL Server para conceder as permissões mínimas necessárias para a conta Run As efetuar a verificação de estado de funcionamento. No entanto, não precisa de fazer isso, se uma conta Run As já faz parte da função de servidor sysadmin em instâncias do SQL Server.

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

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Para configurar a conta Run As do SQL com o Windows PowerShell
Abra uma janela do PowerShell e execute o seguinte script depois atualizou-lo com as suas informações:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Compreender a forma como as recomendações são priorizadas
Cada recomendação feita é fornecida um valor de peso que identifica a importância relativa da recomendação. Apenas as dez mais importantes recomendações são apresentadas.

### <a name="how-weights-are-calculated"></a>Como os pesos são calculados
Pesos são valores agregados com base em três fatores principais:

* O *probabilidade* que um problema identificado causará problemas. Uma probabilidade maior equivale a uma maior pontuação geral para a recomendação.
* O *impacto* do problema na sua organização se ele faz com que um problema. Um impacto maior equivale a uma maior pontuação geral para a recomendação.
* O *esforço* necessário para implementar a recomendação. Um esforço maior equivale a uma menor pontuação geral para a recomendação.

O peso de cada recomendação é expressa como uma percentagem da pontuação total disponível para todas as áreas de foco. Por exemplo, se uma recomendação na área de foco de segurança e conformidade tem uma pontuação igual a 5%, implementar essa recomendação irá aumentar seu % de pontuação por 5 geral da segurança e conformidade.

### <a name="focus-areas"></a>Áreas de foco
**Segurança e conformidade** -esta área de foco mostra recomendações para potenciais ameaças de segurança e falhas, as políticas empresariais e os requisitos de conformidade técnicos, jurídicos e regulamentares.

**Disponibilidade e continuidade do negócio** -esta área de foco mostra recomendações para a disponibilidade do serviço, a resiliência da infraestrutura e proteção de negócios.

**Desempenho e escalabilidade** -esta área de foco mostra recomendações para ajudar a sua organização infraestrutura de TI aumentam, certifique-se de que o seu ambiente de TI cumpre os requisitos de desempenho atuais e é capaz de responder para alterar a infraestrutura necessidades.

**Atualizar, migração e a implantação** -esta área de foco mostra recomendações para o ajudar a atualizar, migrar e implemente o SQL Server para a sua infraestrutura existente.

**Operações e monitorização** -esta área de foco mostra recomendações para ajudar a simplificar as operações de TI, implementar a manutenção preventiva e maximizar o desempenho.

**Gestão de configuração e alteração** -esta área de foco mostra recomendações para ajudar a proteger as operações diárias, certifique-se de que as alterações não negativamente afetam a sua infraestrutura, estabelecerem procedimentos de controlo de alteração e para controlar e auditar configurações do sistema.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Deve visar à pontuação 100% em todas as áreas de foco?
Não necessariamente. As recomendações baseiam-se o conhecimento e experiências obtidas pelos engenheiros da Microsoft em milhares de visitas a clientes. No entanto, não infra-estruturas de dois servidor são os mesmos e recomendações específicas podem ser mais ou menos relevantes para si. Por exemplo, algumas recomendações de segurança poderão ser menos relevantes, se as máquinas virtuais não são expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para os serviços que fornecem o relatório e a coleta de dados ad hoc de baixa prioridade. Problemas que são importantes para uma empresa madura e podem ser menos importantes para uma inicialização. Pode querer identificar quais áreas de foco são suas prioridades e, em seguida, examinar como as suas pontuações alteram ao longo do tempo.

Cada recomendação inclui documentação de orientação sobre por que é importante. Deve usar essa orientação para avaliar se implementar a recomendação é adequada para si, tendo em conta a natureza de seus serviços de TI e as necessidades de negócio da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Utilize recomendações de área de foco verificar o estado de funcionamento
Antes de poder utilizar uma solução de avaliação do Log Analytics, tem de ter a solução instalada.  Depois de ser instalado, pode ver o resumo de recomendações utilizando o mosaico de verificação de estado de funcionamento do SQL na página da solução no portal do Azure.

Ver as avaliações de conformidade resumidos para sua infraestrutura e, em seguida, no teste de recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para ver as recomendações para uma área de foco e tomar medidas corretivas
1. Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. No portal do Azure, clique em **Mais serviços**, que se encontra no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics**.
3. No painel de subscrições do Log Analytics, selecione uma área de trabalho e, em seguida, clique nas **descrição geral** mosaico.  
4. Sobre o **descrição geral** página, clique no **verificação de estado de funcionamento do SQL** mosaico.
5. Sobre o **verificar o estado de funcionamento** página, reveja as informações de resumo em um dos painéis de área de foco e, em seguida, clique num para ver as recomendações para essa área de foco.
6. Em qualquer uma das páginas de área de foco, pode ver as fez para o seu ambiente de recomendações ordenadas por prioridade. Clique numa recomendação em **Objetos afetados** para ver detalhes sobre por que motivo a recomendação é feita.<br><br> ![imagem de recomendações de verificação de estado de funcionamento do SQL](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Pode efetuar ações corretivas sugeridas na **ações sugeridas**. Quando o item tiver sido resolvido, avaliações posteriores gravará que recomenda ações foram executadas e irão aumentar a sua pontuação de conformidade. Itens corrigidos aparecem como **objetos transmitidos**.

## <a name="ignore-recommendations"></a>Ignorar recomendações
Se tiver recomendações que deseja ignorar, pode criar um arquivo de texto que o Log Analytics irá utilizar para impedir que as recomendações de aparecer no seu os resultados da avaliação.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar as recomendações que irá ignorar
1. No portal do Azure na página de área de trabalho do Log Analytics para a sua área de trabalho selecionada, clique nas **pesquisa de registos** mosaico.
2. Utilize a seguinte consulta para recomendações de lista que tenham falhado para computadores no seu ambiente.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Se a sua área de trabalho tiver sido atualizada para o [linguagem de consulta do Log Analytics de novas](../../azure-monitor/log-query/log-query-overview.md), em seguida, a consulta acima serão alteradas para o seguinte.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Eis uma captura de ecrã que mostra a consulta de pesquisa de registos:<br><br> ![recomendações com falhas](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Escolha as recomendações que pretende ignorar. Vai utilizar os valores para RecommendationId no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e utilizar um ficheiro de texto IgnoreRecommendations.txt
1. Crie um ficheiro denominado IgnoreRecommendations.txt.
2. Cole ou introduza cada RecommendationId para cada recomendação que pretende que o Log Analytics para ignorar numa linha separada e, em seguida, guarde e feche o ficheiro.
3. Coloca o ficheiro na pasta em cada computador onde pretende que o Log Analytics para ignorar recomendações.
   * Em computadores com o Microsoft Monitoring Agent (conectado diretamente ou através do Operations Manager) - *SystemDrive*: \Programas\Microsoft Agent\Agent de monitorização
   * No servidor de gestão do Operations Manager - *SystemDrive*: \Programas\Microsoft System Center 2012 R2\Operations Manager\Server
   * No servidor de gestão do Operations Manager 2016 - *SystemDrive*: \Programas\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar que as recomendações são ignoradas
1. Após a próxima agendada execuções de avaliação, por predefinição, 7 dias, as recomendações especificadas são marcadas ignorado e não serão apresentados no dashboard de avaliação.
2. Pode utilizar as seguintes consultas de pesquisa de registos para listar todas as recomendações ignoradas.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Se a sua área de trabalho tiver sido atualizada para o [linguagem de consulta do Log Analytics de novas](../../azure-monitor/log-query/log-query-overview.md), em seguida, a consulta acima serão alteradas para o seguinte.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Se decidir posteriormente que pretende ver recomendações ignoradas, remova todos os ficheiros IgnoreRecommendations.txt ou pode remover RecommendationIDs dos mesmos.

## <a name="sql-health-check-solution-faq"></a>Solução de verificação de estado de funcionamento do SQL FAQ
*A frequência com que é executada uma verificação de estado de funcionamento?*

* A verificação é executada a cada sete dias.

*Existe uma forma de configurar a frequência com que a verificação é executada?*

* Neste momento, não.

*Se for detetado o outro servidor depois adicionei a solução de verificação de estado de funcionamento do SQL, irá ser deu entrada?*

* Sim, assim que o descobrir que seu check-in de, em seguida, no, a cada sete dias.

*Se um servidor está desativado, quando irá este ser removido da verificação de estado de funcionamento?*

* Se um servidor não enviar dados para três semanas, este é removido.

*O que é o nome do processo que faz a recolha de dados?*

* AdvisorAssessment.exe

*Quanto tempo demora para recolher dados?*

* A recolha de dados real no servidor demora cerca de 1 hora. Pode demorar mais tempo nos servidores que têm um grande número de instâncias do SQL ou bases de dados.

*Que tipo de dados é recolhido?*

* Os seguintes tipos de dados são recolhidos:
  * WMI
  * Registo
  * Contadores de desempenho
  * Vistas de gestão dinâmica do SQL (DMV).

*Existe uma forma de configurar quando os dados são recolhidos?*

* Neste momento, não.

*Por que motivo é necessário configurar uma conta Run As?*

* Para o SQL Server, um pequeno número de consultas SQL é executado. Na ordem que sejam executadas, deve ser utilizada uma conta Run As com permissões de VIEW SERVER STATE para SQL.  Além disso, para consultar o WMI, as credenciais de administrador local são necessárias.

*Por que motivo exibir apenas as recomendações de 10 principais?*

* Em vez de apresentar uma lista exaustiva esmagadora de tarefas, recomendamos que se concentre em endereçamento de recomendações ordenadas por prioridade em primeiro lugar. Depois de corrigi-los, recomendações adicionais estarão disponíveis. Se preferir ver a lista detalhada, pode ver todas as recomendações com a pesquisa de registos do Log Analytics.

*Existe uma forma de ignorar uma recomendação?*

* Sim, consulte [ignorar recomendações](#ignore-recommendations) secção acima.

## <a name="next-steps"></a>Passos Seguintes
* [Pesquisar registos](../../azure-monitor/log-query/log-query-overview.md) para saber como analisar dados de verificação de estado de funcionamento do SQL detalhados e recomendações.
