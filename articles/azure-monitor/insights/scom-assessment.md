---
title: Avaliar o Gestor de Operações do Centro de Sistemas com o Monitor Azure
description: Pode utilizar a solução health check do Gestor de Operações do System Center para avaliar o risco e a saúde dos seus ambientes num intervalo regular.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2018
ms.openlocfilehash: 94251dfa2d9fa732912ed20d825e64f542d79188
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055417"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optimize your environment with the System Center Operations Manager Health Check (Preview) solution (Otimizar o ambiente com a solução Verificação de Estado de Funcionamento do System Center Operations Manager [Pré-visualização])

![System Center Operations Manager Health Check símbolo](./media/scom-assessment/scom-assessment-symbol.png)

Pode utilizar a solução de Verificação de Saúde do Gestor de Operações do System Center para avaliar o risco e a saúde do seu grupo de gestão de gestão de operações do Centro de Sistemas num intervalo regular. Este artigo ajuda-o a instalar, configurar e utilizar a solução para que possa tomar medidas corretivas para potenciais problemas.

Esta solução fornece uma lista prioritária de recomendações específicas para a sua infraestrutura de servidores implementadas. As recomendações são categorizadas em quatro áreas de foco, que o ajudam a entender rapidamente o risco e a tomar medidas corretivas.

As recomendações feitas baseiam-se no conhecimento e experiência adquiridos pelos engenheiros da Microsoft a partir de milhares de visitas de clientes. Cada recomendação fornece orientações sobre o porquê de um problema poder ser importante para si e como implementar as alterações sugeridas.

Você pode escolher áreas de foco que são mais importantes para a sua organização e acompanhar o seu progresso para executar um ambiente livre de riscos e saudável.

Depois de ter adicionado a solução e de ser feita uma avaliação, são mostradas informações sumárias para áreas de foco no painel de verificação de saúde do Gestor de **Operações** do System Center para a sua infraestrutura. As seguintes secções descrevem como usar a informação no painel de verificação de **saúde do Gestor** de Operações do System Center, onde pode ver e, em seguida, tomar medidas recomendadas para o ambiente do seu Gestor de Operações.

![Azulejo de solução de gestor de operações do Centro de Sistema](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Painel de verificação de saúde do gestor de operações do centro de sistema](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução

A solução funciona com o Microsoft System Center 2012 Operations Manager Service Pack 1, Microsoft System Center 2012 R2 Operations Manager, Microsoft System Center 2016 Operations Manager, Microsoft System Center 2016 Operations Manager 2016 e Microsoft System Center Operations Manager 1807. Uma versão suportada do .NET Framework 4.6.2 deve ser instalada em cada servidor de gestão.

Utilize as seguintes informações para instalar e configurar a solução.

- Antes de poder utilizar a solução Health Check no Log Analytics, tem de ter a solução instalada. Instale a solução a partir do [mercado Azure.](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview)

- Depois de adicionar a solução ao espaço de trabalho, o **sistema center Operações Manager Health Check** azulejos no painel de instrumentos apresenta uma mensagem adicional necessária. Clique no azulejo e siga os passos de configuração mencionados na página

  ![Azulejo do gestor de operações do Centro de Sistema](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> A configuração do System Center Operations Manager pode ser feita utilizando um script seguindo os passos mencionados na página de configuração da solução em Log Analytics.

 Para configurar a avaliação através da consola Operamanager Operations, execute os passos abaixo na seguinte ordem:
1. [Definir a corrida Como conta para o Sistema Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
2. Configure a regra de verificação de saúde do gestor de operações do Centro de Sistemas

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>Detalhes da recolha de dados da Verificação de Estado de Funcionamento do System Center Operations Manager

A solução de Verificação de Saúde do Gestor de Operações do Centro de Sistema recolhe dados das seguintes fontes:

* Registo
* Windows Management Instrumentation (WMI)
* Registo de eventos
* Dados de ficheiros
* Diretamente do Gestor de Operações utilizando consultas PowerShell e SQL, a partir de um servidor de gestão que especificou.  

Os dados são recolhidos no servidor de gestão e encaminhados para o Log Analytics de sete em sete dias.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Contas Run As do Operations Manager para o Log Analytics

O Log Analytics baseia-se em pacotes de gestão para cargas de trabalho para fornecer serviços de valor acrescentado. Cada carga de trabalho requer privilégios específicos de carga de trabalho para executar pacotes de gestão num contexto de segurança diferente, como uma conta de utilizador de domínio. Configure um Gestor de Operações Executar Como conta com credenciais privilegiadas. Para obter informações adicionais, consulte [Como criar uma conta Run As](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) na documentação do Gestor de Operações.

Utilize as seguintes informações para definir o Funcionador de Operações Como conta para o Sistema De Gestão de Operações Controlo de Saúde.

### <a name="set-the-run-as-account"></a>Definir a conta Run As

A conta Run As deve satisfazer os seguintes requisitos antes de prosseguir:

* Uma conta de utilizador de domínio que é membro do grupo de Administradores locais em todos os servidores que suportam qualquer função de Gestor de Operações - Servidor de Gestão, Servidor SQL hospedando a base de dados operacional, armazém de dados e ACS, Relatório, consola web e servidor Gateway.
* Função de Administrador de Funcionamento para o grupo de gestão que está a ser avaliada
* Se a conta não tiver direitos de sysadmina SQL, execute o [script](#sql-script-to-grant-granular-permissions-to-the-run-as-account) para conceder permissões granulares à conta em cada instância do SQL Server que acolhe uma ou todas as bases de dados do Gestor de Operações.

1. Na Consola do Gestor de Operações, selecione o botão de navegação **da Administração.**
2. Sob **execução Como Configuração,** clique em **Contas**.
3. Na **"Create Run As Account** Wizard", na página **introdução** clique em **Seguinte**.
4. Na página **Propriedades Gerais,** selecione **Windows** no **tipo 'Executar Como Conta:** lista.'
5. Digite um nome de exibição na caixa de texto **'Nome do ecrã'** e digite opcionalmente uma descrição na caixa **descrição** e, em seguida, clique em **Seguinte**.
6. Na página de Segurança de **Distribuição,** selecione **Mais seguro**.
7. Clique em **Criar**.  

Agora que a conta Run As é criada, precisa de direcionar servidores de gestão no grupo de gestão e associado a um perfil de Run As pré-definido para que os fluxos de trabalho funcionem utilizando as credenciais.  

1. Em **Executar Como Configuração,** Contas, no painel de **resultados,** clique duas vezes na conta que criou anteriormente.
2. No separador **Distribuição,** clique em **Adicionar** para a caixa **de computadores Selecionados** e adicione o servidor de gestão para distribuir a conta.  Clique **em OK** duas vezes para guardar as suas alterações.
3. Sob **execução Como Configuração,** clique em **Perfis**.
4. Pesquisar o perfil de avaliação do *SCOM*.
5. O nome do perfil deve ser: *Microsoft System Center Operations Manager Health Check Run As Profile*.
6. Clique à direita e atualize as suas propriedades e adicione o recém-criado Run As Account que criou anteriormente.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Script de SQL para conceder permissões granulares à conta Run As

Execute o seguinte script SQL para conceder permissões necessárias para a conta Run As na instância do Servidor SQL utilizada pelo Gestor de Operações que acolhe a base de dados operacional, de dados e ACS.

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

O pacote de gestão da solução de verificação de saúde do Gestor de Operações do System Center inclui uma regra chamada *Microsoft System Center Operations Manager Run Health Check Rule*. Esta regra é responsável pela verificação da saúde. Para ativar a regra e configurar a frequência, utilize os procedimentos abaixo.

Por predefinição, a regra de verificação de saúde do Gestor de Operações do Microsoft System Center corre desativada. Para fazer a verificação de saúde, deve ativar a regra num servidor de gestão. Utilize os seguintes passos.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Ativar a regra para um servidor de gestão específico

1. No espaço de trabalho da **autoria** da consola de operações, procure a regra microsoft System Center Operations Manager Executar regra de *verificação* de saúde no painel **de regras.**
2. Nos resultados da pesquisa, selecione o que inclui o Tipo de *texto: Servidor de Gestão*.
3. Clique na regra e clique em **Sobreposições** > **para um objeto específico de classe: Management Server**.
4.  Na lista de servidores de gestão disponível, selecione o servidor de gestão onde a regra deve ser executada.  Este deve ser o mesmo servidor de gestão que configuraste anteriormente para associar a conta Run As.
5.  Certifique-se de que altera o valor de substituição para **True** para o valor do parâmetro **habilitado.**<br><br> ![parâmetro de sobreposição](./media/scom-assessment/rule.png)

    Enquanto estiver ainda nesta janela, configure a frequência de execução utilizando o procedimento seguinte.

#### <a name="configure-the-run-frequency"></a>Configurar a frequência de execução

A avaliação está configurada para ser executada a cada 10.080 minutos (ou sete dias) por padrão. Pode anular o valor para um valor mínimo de 1440 minutos (ou um dia). O valor representa o intervalo de tempo mínimo exigido entre as sucessivas avaliações. Para anular o intervalo, utilize os passos abaixo.

1. No espaço de trabalho da **Autoria** da consola do Gestor de Operações, procure a regra do Gestor de *Operações do Microsoft System Center Executar regra* de verificação de saúde na secção **Regras.**
2. Nos resultados da pesquisa, selecione o que inclui o Tipo de *texto: Servidor de Gestão*.
3. Clique na regra e clique em **anular a regra** > **Para todos os objetos de classe: Servidor de Gestão**.
4. Altere o valor do parâmetro **intervalo** para o valor de intervalo desejado. No exemplo abaixo, o valor é definido para 1440 minutos (um dia).<br><br> ![parâmetro de intervalo](./media/scom-assessment/interval.png)<br>  

    Se o valor for definido para menos de 1440 minutos, então a regra passa num intervalo de um dia. Neste exemplo, a regra ignora o valor do intervalo e corre a uma frequência de um dia.


## <a name="understanding-how-recommendations-are-prioritized"></a>Compreender como é definida a prioridade das recomendações

Todas as recomendações feitas recebem um valor de ponderação que identifica a importância relativa da recomendação. Apenas são apresentadas as 10 recomendações mais importantes.

### <a name="how-weights-are-calculated"></a>Método de cálculo das ponderações

As ponderações são valores agregados com base em três factores-chave:

- A *probabilidade de* um problema identificado causar problemas. Uma maior probabilidade equivale a uma pontuação global maior para a recomendação.
- O *impacto* do problema na sua organização se causar um problema. Um impacto maior equivale a uma pontuação global maior para a recomendação.
- O *esforço* necessário para implementar a recomendação. Um esforço mais elevado equivale a uma pontuação global menor para a recomendação.

A ponderação para cada recomendação é expressa em percentagem da pontuação total disponível para cada área de foco. Por exemplo, se uma recomendação na área de foco de Disponibilidade e Continuidade de Negócios tiver uma pontuação de 5%, implementando essa recomendação aumenta a sua pontuação global de Disponibilidade e Continuidade de Negócios em 5%.

### <a name="focus-areas"></a>Áreas em foco

**Disponibilidade e Continuidade de Negócios** - Esta área de foco mostra recomendações para disponibilidade de serviço, resiliência da sua infraestrutura e proteção empresarial.

**Desempenho e Escalabilidade** - Esta área de foco mostra recomendações para ajudar a infraestrutura de TI da sua organização a crescer, garantir que o seu ambiente de TI satisfaz os requisitos de desempenho atuais, e é capaz de responder às necessidades de infraestrutura em mudança.

**Upgrade, Migração e Implantação** - Esta área de foco mostra recomendações para ajudá-lo a atualizar, migrar e implementar o SQL Server para a sua infraestrutura existente.

**Operações e Monitorização** - Esta área de foco apresenta recomendações para ajudar a agilizar as suas operações de TI, implementar a manutenção preventiva e maximizar o desempenho.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Deve visar uma pontuação de 100% em cada uma das áreas em foco?

Não necessariamente. As recomendações baseiam-se nos conhecimentos e experiências adquiridas pelos engenheiros da Microsoft em milhares de visitas de clientes. No entanto, nenhuma infraestrutura de servidores é a mesma, e recomendações específicas podem ser mais ou menos relevantes para si. Por exemplo, algumas recomendações de segurança podem ser menos relevantes se as suas máquinas virtuais não estiverem expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para serviços que fornecem recolha e reporte de dados ad hoc de baixa prioridade. As questões que são importantes para um negócio maduro podem ser menos importantes para uma start-up. Você pode querer identificar quais as áreas de foco são as suas prioridades e, em seguida, olhar para como as suas pontuações mudam ao longo do tempo.

Todas as recomendações incluem orientações sobre o porquê de ser importante. Use esta orientação para avaliar se a implementação da recomendação é apropriada para si, dada a natureza dos seus serviços de TI e as necessidades empresariais da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Use recomendações de área de foco de verificação de saúde

Antes de poder utilizar uma solução de verificação de saúde no Log Analytics, tem de ter a solução instalada. Para ler mais sobre a instalação de soluções, consulte Instalar uma solução de [gestão.](../../azure-monitor/insights/solutions.md) Depois de instalado, pode ver o resumo das recomendações utilizando o suporte de verificação de saúde do Gestor de Operações do System Center na página **'Overview'** para o seu espaço de trabalho no portal Azure.

Consulte as avaliações de conformidade resumidas para a sua infraestrutura e, em seguida, faça recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Ver recomendações para uma área de foco e tomar medidas corretivas
1. Faça login no portal [https://portal.azure.com](https://portal.azure.com)Azure em .
2. No portal do Azure, clique em **Mais serviços**, que se encontra no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics**.
3. No painel de subscrições do Log Analytics, selecione um espaço de trabalho e clique no item do menu resumo do **Workspace.**  
4. Na página **'Overview',** clique no azulejo de verificação de saúde do **Gestor de Operações** do System Center.
5. Na página de Verificação de **Saúde do Gestor** de Operações do System Center, reveja a informação sumária numa das lâminas da área de foco e clique numa para ver recomendações para essa área de foco.
6. Em qualquer uma das páginas da área de foco, você pode ver as recomendações prioritárias feitas para o seu ambiente. Clique numa recomendação em **Objetos Afetados** para ver detalhes sobre o porquê da recomendação ser feita.<br><br> ![área de foco](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Pode tomar medidas corretivas sugeridas em **Ações Sugeridas**. Quando o item tiver sido abordado, avaliações posteriores registarão que foram tomadas as ações recomendadas e a sua pontuação de conformidade aumentará. Itens corrigidos aparecem como **Objetos Passados**.

## <a name="ignore-recommendations"></a>Ignorar recomendações

Se tiver recomendações que deseja ignorar, pode criar um ficheiro de texto que o Log Analytics utiliza para evitar que as recomendações apareçam nos resultados da sua avaliação.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Para identificar recomendações que pretende ignorar
1. No portal Azure na página do espaço de trabalho Log Analytics para o seu espaço de trabalho selecionado, clique no item do menu **'Pesquisa** de Registos'.
2. Utilize a seguinte consulta para listar recomendações que falharam para computadores no seu ambiente.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Se o seu espaço de trabalho tiver sido atualizado para a nova linguagem de [consulta log analytics,](../../azure-monitor/log-query/log-query-overview.md)então a consulta acima mudaria para a seguinte.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Aqui está uma imagem mostrando a consulta de Pesquisa de Registo:<br><br> ![pesquisa de registos](./media/scom-assessment/scom-log-search.png)<br>

3. Escolha recomendações que queira ignorar. Utilizará os valores para Recomendação id no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e utilizar um ficheiro de texto IgnoreRecommendations.txt

1. Crie um ficheiro chamado Ignorrecommendations.txt.
2. Colar ou digitar cada Recomendação Id para cada recomendação que pretende que o Log Analytics ignore numa linha separada e, em seguida, guarde e feche o ficheiro.
3. Coloque o ficheiro na seguinte pasta em cada computador onde pretende que o Log Analytics ignore as recomendações.
4. No servidor de gestão do Gestor de Operações - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas

1. Após a próxima avaliação programada, por defeito de sete em sete dias, as recomendações especificadas são marcadas ignoradas e não aparecerão no painel de verificação de saúde.
2. Pode utilizar as seguintes consultas de Pesquisa de Registo para listar todas as recomendações ignoradas.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Se o seu espaço de trabalho tiver sido atualizado para a nova linguagem de [consulta log analytics,](../../azure-monitor/log-query/log-query-overview.md)então a consulta acima mudaria para a seguinte.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Se decidir mais tarde que pretende ver recomendações ignoradas, remova quaisquer ficheiros IgnoreRecommendations.txt ou pode remover recomendações das mesmas.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>FAQ sobre a solução Verificação de Estado de Funcionamento do System Center Operations Manager

*Adicionei a solução health check ao meu espaço de trabalho log Analytics. Mas não vejo as recomendações. Porque não?* Depois de adicionar a solução, utilize os seguintes passos, ver as recomendações no painel de instrumentos Log Analytics.  

- [Definir a corrida Como conta para o Sistema Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
- [Configure a regra de verificação de saúde do gestor de operações do Centro de Sistemas](#configure-the-health-check-rule)


*Existe uma maneira de configurar a frequência com que o cheque corre?* Sim. Consulte [a frequência de execução .](#configure-the-run-frequency)

*Se outro servidor for descoberto depois de eu adicionar a solução de Verificação de Saúde do Gestor de Operações do System Center, será verificado?* Sim, depois de descoberta é verificado a partir daí, por defeito a cada sete dias.

*Qual é o nome do processo que faz a recolha de dados?* AdvisorAssessment.exe

*Para onde funciona o processo AdvisorAssessment.exe?* AdvisorAssessment.exe corre no âmbito do processo HealthService do servidor de gestão onde a regra de verificação de saúde está ativada. Utilizando esse processo, a descoberta de todo o seu ambiente é conseguida através da recolha remota de dados.

*Quanto tempo demora para a recolha de dados?* A recolha de dados no servidor demora cerca de uma hora. Pode demorar mais tempo em ambientes que têm muitas instâncias ou bases de dados do Gestor de Operações.

*E se eu fixar o intervalo da avaliação para menos de 1440 minutos?* A avaliação é pré-configurada para ser executada a um máximo de uma vez por dia. Se ultrapassar o valor do intervalo para um valor inferior a 1440 minutos, então a avaliação utiliza 1440 minutos como valor de intervalo.

*Como saber se há falhas pré-requisitos?* Se o exame de saúde decorreu e não se vê resultados, então é provável que alguns dos pré-requisitos para o cheque falharam. Pode executar consultas: `Operation Solution=SCOMAssessment` `SCOMAssessmentRecommendation FocusArea=Prerequisites` e em 'Pesquisa de Registo' para ver os pré-requisitos falhados.

*Há uma `Failed to connect to the SQL Instance (….).` mensagem em falhas pré-requisitos. Qual é o problema?* AdvisorAssessment.exe, o processo que recolhe dados, decorre no âmbito do processo HealthService no servidor de gestão. Como parte da verificação de saúde, o processo tenta ligar-se ao Servidor SQL onde está presente a base de dados do Gestor de Operações. Este erro pode ocorrer quando as regras de firewall bloqueiam a ligação à instância do Servidor SQL.

*Que tipo de dados são recolhidos?* Os seguintes tipos de dados são recolhidos: - Dados do WMI - Dados do Registo - Dados do EventLog - Dados do Gestor de Operações através do Windows PowerShell, SQL Queries e File Information Collector.

*Por que tenho que configurar uma conta de execução?* Com o Gestor de Operações, são executadas várias consultas SQL. Para que possam ser executados, deve utilizar uma Conta Run As com as permissões necessárias. Além disso, as credenciais de administrador local são necessárias para consultar o WMI.

*Por que exibir apenas as 10 melhores recomendações?* Em vez de lhe dar uma lista exaustiva e esmagadora de tarefas, recomendamos que se concentre em abordar as recomendações prioritárias primeiro. Depois de os abordar, serão disponibilizadas recomendações adicionais. Se preferir ver a lista detalhada, pode ver todas as recomendações usando a Pesquisa de Registo.

*Há alguma maneira de ignorar uma recomendação?* Sim, veja as [recomendações de Ignor.](#ignore-recommendations)


## <a name="next-steps"></a>Passos seguintes

- [Pesquisar registos](../../azure-monitor/log-query/log-query-overview.md) para saber como analisar dados e recomendações detalhadas do Gestor de Operações do System Center.
