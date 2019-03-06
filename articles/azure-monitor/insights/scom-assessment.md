---
title: Otimizar seu ambiente do System Center Operations Manager com o Azure Log Analytics | Documentos da Microsoft
description: Pode utilizar a solução de verificação do System Center Operations Manager estado de funcionamento para avaliar o risco e estado de funcionamento dos seus ambientes num intervalo regular.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: magoedte
ms.openlocfilehash: 596bc86033fdeaf872f1facfebbf47780893b7d2
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455946"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Otimizar seu ambiente com a solução do System Center Operations Manager verificação de integridade (pré-visualização)

![System Center Operations Manager Health Check symbol](./media/scom-assessment/scom-assessment-symbol.png)

Pode utilizar a solução de verificação do System Center Operations Manager estado de funcionamento para avaliar o risco e estado de funcionamento do seu grupo de gestão do System Center Operations Manager num intervalo regular. Este artigo ajuda-o a instalar, configurar e utilizar a solução para que pode tomar medidas corretivas para potenciais problemas.

Esta solução fornece uma lista prioritária de recomendações específicas para sua infra-estrutura de servidor implementado. As recomendações são categorizadas em quatro áreas de foco, ajudá-lo a rapidamente entender o risco e tomar medidas corretivas.

As recomendações feitas baseiam-se o conhecimento e a experiência obtida pelos engenheiros da Microsoft de milhares de visitas a clientes. Cada recomendação fornece orientações sobre por que um problema poderá são importantes para si e como implementar as alterações sugeridas.

Pode escolher áreas de foco que são mais importantes para a sua organização e acompanhar seu progresso em relação a execução de um ambiente gratuito e em bom estado de risco.

Depois de adicionar a solução e uma avaliação é executada, resumo informações para áreas de foco são mostradas no **Center Operations Manager estado de funcionamento da verificação do sistema** dashboard para a sua infraestrutura. As secções seguintes descrevem como utilizar as informações sobre o **Center Operations Manager estado de funcionamento da verificação do sistema** dashboard, onde pode ver e, em seguida, efetuar as ações recomendadas para o seu ambiente do Operations Manager.

![Mosaico de solução do System Center Operations Manager](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Dashboard de verificação de estado de funcionamento do Center Operations Manager do sistema](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução

A solução é compatível com o Microsoft System Center 2012 Operations Manager Service Pack 1, o Microsoft System Center 2012 R2 Operations Manager, o Microsoft System Center 2016 Operations Manager, o Microsoft System Center 2016 Operations Manager e o Microsoft System 1807 do Center Operations Manager

Utilize as seguintes informações para instalar e configurar a solução.

 - Antes de poder utilizar a solução de verificação de estado de funcionamento no Log Analytics, tem de ter a solução instalada. Instalar a solução a partir [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

 - Depois de adicionar a solução para a área de trabalho, o **Center Operations Manager estado de funcionamento da verificação do sistema** mosaico no dashboard mostra uma mensagem necessária configuração adicional. Clique no mosaico e siga os passos de configuração mencionados na página

 ![Mosaico de dashboard do System Center Operations Manager](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Configuração do System Center Operations Manager pode ser feita usando um script ao seguir os passos mencionados na página de configuração da solução no Log Analytics.

 Para configurar a avaliação através da consola de operações do Operations Manager, execute os passos abaixo na seguinte ordem:
1. [Definir a conta Run As para o Centro de operações de Gestor de estado de funcionamento da verificação do sistema](#operations-manager-run-as-accounts-for-log-analytics)  
2. Configurar a regra de verificação do System Center Operations Manager estado de funcionamento

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>Detalhes de recolha de dados verificação de estado de funcionamento do Center Operations Manager do sistema

A solução de verificação de estado de funcionamento Manager do System Center Operations recolhe dados das seguintes origens:

* Registo
* Windows Management Instrumentation (WMI)
* Registo de eventos
* Dados de ficheiros
* Diretamente a partir do Operations Manager através de consultas SQL e o PowerShell, de um servidor de gestão que especificou.  

Dados são recolhidos no servidor de gestão e reencaminhados para o Log Analytics a cada sete dias.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Executar como contas do Operations Manager para o Log Analytics

Compilações de análise de registo de pacotes de gestão para cargas de trabalho fornecer valor agregado dos serviços. Cada carga de trabalho requer privilégios de carga de trabalho específica para executar os pacotes de gestão num contexto de segurança diferentes, como uma conta de utilizador de domínio. Configure uma Operations Manager conta Run As com credenciais com privilégios. Para obter mais informações, consulte [como criar uma conta Run As](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) na documentação do Operations Manager.

Utilize as seguintes informações para definir a Operations Manager conta Run As para o Centro de operações de Gestor de estado de funcionamento da verificação do sistema.

### <a name="set-the-run-as-account"></a>Definir a conta Run As

A conta Run As deve cumprir seguintes requisitos antes de continuar:

* Uma conta de utilizador de domínio que seja membro do grupo Administradores local em todos os servidores que suporta qualquer função do Operations Manager - Management server, SQL Server que aloja o operacional, armazém de dados e base de dados ACS, relatórios, Web console e o servidor de Gateway.
* Função de administrador do Gestor de operação para o grupo de gestão que está a ser avaliado
* Se a conta não tem direitos de administrador do sistema do SQL, em seguida, executar o [script](#sql-script-to-grant-granular-permissions-to-the-run-as-account) para conceder permissões granulares para a conta em cada instância de SQL Server que aloja uma ou todas as bases de dados do Operations Manager.

1. Na consola do Operations Manager, selecione o **administração** botão de navegação.
2. Sob **configuração de Run As**, clique em **contas**.
3. Na **criar conta Run As** assistente, na **introdução** página clique **seguinte**.
4. Sobre o **propriedades gerais** página, selecione **Windows** no **tipo de conta Run As:** lista.
5. Escreva um nome a apresentar na **nome a apresentar** texto caixa e, opcionalmente, escreva uma descrição na **Descrição** caixa e, em seguida, clique em **seguinte**.
6. Sobre o **segurança de distribuição** página, selecione **mais seguro**.
7. Clique em **Criar**.  

Agora que foi criada a conta Run As, tem de servidores de gestão de destino no grupo de gestão e associados a um perfil Run As predefinidas para que os fluxos de trabalho serão executado com as credenciais.  

1. Sob **configuração de Run As**, **contas**, no painel de resultados, faça duplo clique a conta que criou anteriormente.
2. Na **distribuição** separador, clique em **Add** para o **selecionado computadores** caixa e adicione o servidor de gestão para distribuir a conta a.  Clique em **OK** duas vezes para guardar as alterações.
3. Sob **configuração de Run As**, clique em **perfis**.
4. Procure o *perfil de avaliação do SCOM*.
5. O nome de perfil deve ser: *Verificação de estado de funcionamento do Microsoft System Center Operations Manager perfil Run As*.
6. Com o botão direito e atualizar as respetivas propriedades e adicionar recentemente criado a conta Run as que criou anteriormente.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Script SQL para conceder permissões mais detalhadas para a conta Run As

Execute o seguinte script SQL para conceder as permissões necessárias para a conta Run As na instância do SQL Server utilizada pelo Operations Manager que aloja o operacional, armazém de dados e base de dados ACS.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>Configurar a regra de verificação de estado de funcionamento

Pacote de gestão da solução de verificação de estado de funcionamento Manager do System Center Operations inclui uma regra com o nome *Microsoft Center Operations Manager executar o estado de funcionamento verificar regra do sistema*. Esta regra é responsável por executar a verificação de estado de funcionamento. Para ativar a regra e configurar a frequência, utilize os procedimentos abaixo.

Por predefinição, o Microsoft System Center Operations Manager executar estado de funcionamento verificar regra está desativada. Para executar a verificação de estado de funcionamento, tem de ativar a regra num servidor de gestão. Utilize os seguintes passos.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Ativar a regra para um servidor de gestão específico

1. Na **Authoring** área de trabalho da consola de operações do Operations Manager, procure a regra *Microsoft Center Operations Manager executar o estado de funcionamento verificar regra do sistema* no **regras** painel.
2. Nos resultados da pesquisa, selecione aquele que inclui o texto *tipo: Servidor de gestão*.
3. A regra com o botão direito e, em seguida, clique em **substitui** > **para um objeto específico da classe: Servidor de gestão**.
4.  Na lista de servidores de gestão disponíveis, selecione o servidor de gestão em que a regra deve ser executado.  Deve ser o mesmo servidor de gestão que configurou anteriormente para associar a conta Run As com.
5.  Certifique-se de que altere o valor de substituição como **True** para o **ativado** valor do parâmetro.<br><br> ![parâmetro de substituição](./media/scom-assessment/rule.png)

    Ainda nessa janela, configure a frequência de execução usando o procedimento seguinte.

#### <a name="configure-the-run-frequency"></a>Configurar a frequência de execução

Por predefinição, a avaliação é configurada para executar cada 10.080 minutos (ou sete dias). É possível substituir o valor para um valor mínimo de 1440 minutos (ou um dia). O valor representa a lacuna de tempo mínimo necessária entre as execuções de avaliação sucessivas. Para o intervalo de substituição, utilize os passos abaixo.

1. Na **Authoring** área de trabalho da consola do Operations Manager, procure a regra *Microsoft Center Operations Manager executar o estado de funcionamento verificar regra do sistema* no **regras** secção.
2. Nos resultados da pesquisa, selecione aquele que inclui o texto *tipo: Servidor de gestão*.
3. A regra com o botão direito e, em seguida, clique em **substituir a regra** > **para todos os objetos da classe: Servidor de gestão**.
4. Alteração da **intervalo** valor do parâmetro para o valor de intervalo desejado. No exemplo abaixo, o valor é definido como 1.440 minutos (um dia).<br><br> ![parâmetro de intervalo](./media/scom-assessment/interval.png)<br>  

    Se o valor é definido como inferior a 1440 minutos, em seguida, a regra é executada num intervalo de um dia. Neste exemplo, a regra ignora o valor do intervalo e é executado com uma frequência de um dia.


## <a name="understanding-how-recommendations-are-prioritized"></a>Compreender a forma como as recomendações são priorizadas

Cada recomendação feita é fornecida um valor de peso que identifica a importância relativa da recomendação. As 10 recomendações mais importantes são apresentadas.

### <a name="how-weights-are-calculated"></a>Como os pesos são calculados

Pesos são valores agregados com base em três fatores principais:

- O *probabilidade* que um problema identificado causará problemas. Uma probabilidade maior equivale a uma maior pontuação geral para a recomendação.
- O *impacto* do problema na sua organização se ele faz com que um problema. Um impacto maior equivale a uma maior pontuação geral para a recomendação.
- O *esforço* necessário para implementar a recomendação. Um esforço maior equivale a uma menor pontuação geral para a recomendação.

O peso de cada recomendação é expressa como uma percentagem da pontuação total disponível para todas as áreas de foco. Por exemplo, se uma recomendação na área de foco de disponibilidade e continuidade do negócio tem uma pontuação igual a 5%, implementar essa recomendação aumenta sua % de pontuação por 5 geral da disponibilidade e continuidade do negócio.

### <a name="focus-areas"></a>Áreas de foco

**Disponibilidade e continuidade do negócio** -esta área de foco mostra recomendações para a disponibilidade do serviço, a resiliência da infraestrutura e proteção de negócios.

**Desempenho e escalabilidade** -esta área de foco mostra recomendações para ajudar a sua organização infraestrutura de TI aumentam, certifique-se de que o seu ambiente de TI cumpre os requisitos de desempenho atuais e é capaz de responder para alterar a infraestrutura necessidades.

**Atualização, migração e implantação** -esta área de foco mostra recomendações para o ajudar a atualizar, migrar e implemente o SQL Server para a sua infraestrutura existente.

**Operações e monitorização** -esta área de foco mostra recomendações para ajudar a simplificar as operações de TI, implementar a manutenção preventiva e maximizar o desempenho.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Deve visar à pontuação 100% em todas as áreas de foco?

Não necessariamente. As recomendações baseiam-se o conhecimento e experiências obtidas pelos engenheiros da Microsoft em milhares de visitas a clientes. No entanto, não infra-estruturas de dois servidor são os mesmos e recomendações específicas podem ser mais ou menos relevantes para si. Por exemplo, algumas recomendações de segurança poderão ser menos relevantes, se as máquinas virtuais não são expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para os serviços que fornecem o relatório e a coleta de dados ad hoc de baixa prioridade. Problemas que são importantes para uma empresa madura e podem ser menos importantes para uma inicialização. Pode querer identificar quais áreas de foco são suas prioridades e, em seguida, examinar como as suas pontuações alteram ao longo do tempo.

Cada recomendação inclui documentação de orientação sobre por que é importante. Use essa orientação para avaliar se implementar a recomendação é adequada para si, tendo em conta a natureza de seus serviços de TI e as necessidades de negócio da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Recomendações de área de foco de verificação de integridade de utilização

Antes de poder utilizar uma solução de verificação de estado de funcionamento no Log Analytics, tem de ter a solução instalada. Para ler mais sobre a instalação de soluções, veja [instalar uma solução de gestão](../../azure-monitor/insights/solutions.md). Depois de ser instalado, pode ver o resumo de recomendações por meio do mosaico do Center Operations Manager estado de funcionamento da verificação do sistema no **descrição geral** page de sua área de trabalho no portal do Azure.

Ver as avaliações de conformidade resumidos para sua infraestrutura e, em seguida, no teste de recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para ver as recomendações para uma área de foco e tomar medidas corretivas
1. Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. No portal do Azure, clique em **Mais serviços**, que se encontra no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics**.
3. No painel de subscrições do Log Analytics, selecione uma área de trabalho e, em seguida, clique nas **resumo de área de trabalho** item de menu.  
4. Sobre o **descrição geral** página, clique no **Center Operations Manager estado de funcionamento da verificação do sistema** mosaico.
5. Sobre o **Center Operations Manager estado de funcionamento da verificação do sistema** página, reveja as informações de resumo em um dos painéis de área de foco e, em seguida, clique num para ver as recomendações para essa área de foco.
6. Em qualquer uma das páginas de área de foco, pode ver as fez para o seu ambiente de recomendações ordenadas por prioridade. Clique numa recomendação em **Objetos afetados** para ver detalhes sobre por que motivo a recomendação é feita.<br><br> ![área de foco](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Pode efetuar ações corretivas sugeridas na **ações sugeridas**. Quando o item tiver sido resolvido, avaliações posteriores gravará que recomenda ações foram executadas e irão aumentar a sua pontuação de conformidade. Itens corrigidos aparecem como **objetos transmitidos**.

## <a name="ignore-recommendations"></a>Ignorar recomendações

Se tiver recomendações que deseja ignorar, pode criar um arquivo de texto que utiliza o Log Analytics para impedir recomendações apareçam nos resultados de sua avaliação.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Para identificar as recomendações que pretende ignorar
1. No portal do Azure na página de área de trabalho do Log Analytics para a sua área de trabalho selecionada, clique nas **pesquisa de registos** item de menu.
2. Utilize a seguinte consulta para recomendações de lista que tenham falhado para computadores no seu ambiente.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Se a sua área de trabalho tiver sido atualizada para o [linguagem de consulta do Log Analytics de novas](../../azure-monitor/log-query/log-query-overview.md), em seguida, a consulta acima serão alteradas para o seguinte.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Eis uma captura de ecrã que mostra a consulta de pesquisa de registos:<br><br> ![pesquisa de registos](./media/scom-assessment/scom-log-search.png)<br>

3. Escolha as recomendações que pretende ignorar. Vai utilizar os valores para RecommendationId no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e utilizar um ficheiro de texto IgnoreRecommendations.txt

1. Crie um ficheiro denominado IgnoreRecommendations.txt.
2. Cole ou introduza cada RecommendationId para cada recomendação que pretende que o Log Analytics para ignorar numa linha separada e, em seguida, guarde e feche o ficheiro.
3. Coloca o ficheiro na pasta em cada computador onde pretende que o Log Analytics para ignorar recomendações.
4. No servidor de gestão do Operations Manager - *SystemDrive*: \Programas\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar que as recomendações são ignoradas

1. Após a próxima agendada execuções de avaliação, por predefinição a cada sete dias, as recomendações especificadas são marcadas ignorado e não serão apresentados no dashboard de verificação do Estado de funcionamento.
2. Pode utilizar as seguintes consultas de pesquisa de registos para listar todas as recomendações ignoradas.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Se a sua área de trabalho tiver sido atualizada para o [linguagem de consulta do Log Analytics de novas](../../azure-monitor/log-query/log-query-overview.md), em seguida, a consulta acima serão alteradas para o seguinte.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Se decidir posteriormente que pretende ver recomendações ignoradas, remova todos os ficheiros IgnoreRecommendations.txt ou pode remover RecommendationIDs dos mesmos.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>FAQ de solução de verificação de estado de funcionamento do Center Operations Manager de sistema

*A solução de verificação de integridade que adicionei à minha área de trabalho do Log Analytics. Mas eu não vejo as recomendações. Por que não?* Depois de adicionar a solução, utilize a vista de passos seguinte as recomendações no dashboard do Log Analytics.  

- [Definir a conta Run As para o Centro de operações de Gestor de estado de funcionamento da verificação do sistema](#operations-manager-run-as-accounts-for-log-analytics)  
- [Configurar a regra de verificação do System Center Operations Manager estado de funcionamento](#configure-the-health-check-rule)


*Existe uma forma de configurar a frequência com que a verificação é executada?* Sim. Ver [configurar a frequência de execução](#configure-the-run-frequency).

*Se for detetado o outro servidor depois adicionei a solução de verificação do System Center Operations Manager estado de funcionamento, será ele verificado?* Sim, após a deteção que seu check-in de ora em diante, por predefinição a cada sete dias.

*O que é o nome do processo que faz a recolha de dados?* AdvisorAssessment.exe

*Onde é executado o processo de AdvisorAssessment.exe?* AdvisorAssessment.exe é executado sob o processo de HealthService do servidor de gestão em que a regra de verificação de estado de funcionamento está ativada. Usando esse processo, a deteção de todo o seu ambiente é assegurada através da recolha de dados remota.

*Quanto tempo demora para recolha de dados?* Recolha de dados no servidor demora cerca de uma hora. Pode demorar mais em ambientes que tenham muitas bases de dados ou instâncias do Operations Manager.

*E se definir o intervalo da avaliação como menos de 1.440 minutos?* A avaliação está pré-configurada para executar no máximo uma vez por dia. Se substituir o valor do intervalo para um valor inferior a 1440 minutos, em seguida, a avaliação utiliza 1440 minutos como o valor do intervalo.

*Como saber se existem falhas de pré-requisito?* Se a verificação de estado de funcionamento foi executado e não vir os resultados, em seguida, é provável que alguns dos pré-requisitos para a verificação falhou. Pode executar consultas: `Operation Solution=SCOMAssessment` e `SCOMAssessmentRecommendation FocusArea=Prerequisites` na pesquisa de registos para ver os pré-requisitos com falha.

*Existe um `Failed to connect to the SQL Instance (….).` mensagem em falhas de pré-requisitos. O que é o problema?* AdvisorAssessment.exe, o processo que recolhe dados, é executado sob o processo de HealthService do servidor de gestão. Como parte da verificação de estado de funcionamento, o processo tenta se conectar ao SQL Server onde a base de dados do Operations Manager está presente. Este erro pode ocorrer quando as regras de firewall bloquear a ligação à instância do SQL Server.

*Que tipo de dados é recolhido?* Os seguintes tipos de dados são recolhidos: dados do Operations Manager de dados - dados de registo de eventos - de registo do - dados WMI - por meio do recoletor de informações do Windows PowerShell, consultas SQL e arquivo.

*Por que motivo é necessário configurar uma conta Run As?* Com o Operations Manager, são executadas várias consultas SQL. Na ordem que sejam executadas, tem de utilizar uma conta Run As com as permissões necessárias. Além disso, as credenciais de administrador local são necessárias à consulta de WMI.

*Por que motivo exibir apenas as recomendações de 10 principais?* Em vez de apresentar uma lista exaustiva, esmagadora de tarefas, recomendamos que se concentre em endereçamento de recomendações ordenadas por prioridade em primeiro lugar. Depois de corrigi-los, recomendações adicionais estarão disponíveis. Se preferir ver a lista detalhada, pode ver todas as recomendações com pesquisa de registos.

*Existe uma forma de ignorar uma recomendação?* Sim, consulte a [ignorar recomendações](#Ignore-recommendations).


## <a name="next-steps"></a>Passos Seguintes

- [Pesquisar registos](../../azure-monitor/log-query/log-query-overview.md) para saber como analisar dados detalhados de verificação do System Center Operations Manager estado de funcionamento e recomendações.
