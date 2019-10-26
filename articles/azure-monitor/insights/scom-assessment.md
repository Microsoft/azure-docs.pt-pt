---
title: Otimize seu ambiente de System Center Operations Manager com o Log Analytics do Azure | Microsoft Docs
description: Você pode usar a solução Verificação de Integridade do System Center Operations Manager para avaliar o risco e a integridade de seus ambientes em intervalos regulares.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/25/2018
ms.openlocfilehash: 33aa246e21b54aebaa902304ff92d4b74bfaac4b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898773"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Otimize seu ambiente com a solução de Verificação de Integridade do System Center Operations Manager (versão prévia)

![Símbolo de Verificação de Integridade do System Center Operations Manager](./media/scom-assessment/scom-assessment-symbol.png)

Você pode usar a solução Verificação de Integridade do System Center Operations Manager para avaliar o risco e a integridade de seu grupo de gerenciamento de System Center Operations Manager em um intervalo regular. Este artigo ajuda você a instalar, configurar e usar a solução para que você possa tomar ações corretivas para possíveis problemas.

Essa solução fornece uma lista priorizada de recomendações específicas para sua infraestrutura de servidor implantado. As recomendações são categorizadas em quatro áreas de foco, que ajudam você a entender rapidamente o risco e a tomar medidas corretivas.

As recomendações feitas são baseadas no conhecimento e na experiência obtidas pelos engenheiros da Microsoft de milhares de visitas a clientes. Cada recomendação fornece orientação sobre por que um problema pode ser importante para você e como implementar as alterações sugeridas.

Você pode escolher as áreas de foco que são mais importantes para sua organização e acompanhar seu progresso em relação à execução de um ambiente íntegro e sem riscos.

Depois de ter adicionado a solução e uma avaliação é realizada, as informações resumidas para áreas de foco são mostradas no painel de **verificação de integridade do System Center Operations Manager** para sua infraestrutura. As seções a seguir descrevem como usar as informações no painel **verificação de integridade do System Center Operations Manager** , onde você pode exibir e, em seguida, executar as ações recomendadas para seu ambiente de Operations Manager.

![Bloco de solução System Center Operations Manager](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Painel do Verificação de Integridade do System Center Operations Manager](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Instalando e configurando a solução

A solução funciona com o Microsoft System Center 2012 Operations Manager Service Pack 1, o Microsoft System Center 2012 R2 Operations Manager, o Microsoft System Center 2016 Operations Manager, o Microsoft System Center 2016 Operations Manager e o Microsoft System Centro Operations Manager 1807

Utilize as seguintes informações para instalar e configurar a solução.

- Para poder usar a solução de verificação de integridade no Log Analytics, você deve ter a solução instalada. Instale a solução do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

- Depois de adicionar a solução ao espaço de trabalho, o bloco **verificação de integridade do System Center Operations Manager** no painel exibe uma mensagem de configuração adicional necessária. Clique no bloco e siga as etapas de configuração mencionadas na página

  ![Bloco do painel do System Center Operations Manager](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> A configuração de System Center Operations Manager pode ser feita usando um script seguindo as etapas mencionadas na página de configuração da solução em Log Analytics.

 Para configurar a avaliação por meio do console de operações Operations Manager, execute as etapas abaixo na seguinte ordem:
1. [Definir a conta Executar como para Verificação de Integridade do System Center Operations Manager](#operations-manager-run-as-accounts-for-log-analytics)  
2. Configurar a regra de Verificação de Integridade do System Center Operations Manager

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>Detalhes da coleta de dados de Verificação de Integridade do System Center Operations Manager

A solução Verificação de Integridade do System Center Operations Manager coleta dados das seguintes fontes:

* Registo
* Instrumentação de Gerenciamento do Windows (WMI)
* Log de eventos
* Dados de arquivo
* Diretamente de Operations Manager usando consultas do PowerShell e do SQL, de um servidor de gerenciamento que você especificou.  

Os dados são coletados no servidor de gerenciamento e encaminhados para Log Analytics a cada sete dias.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Operations Manager contas Executar como para Log Analytics

Log Analytics baseia-se em pacotes de gerenciamento para cargas de trabalho para fornecer serviços de valor agregado. Cada carga de trabalho requer privilégios específicos de carga de trabalho para executar pacotes de gerenciamento em um contexto de segurança diferente, como uma conta de usuário de domínio. Configure uma conta Executar como Operations Manager com credenciais privilegiadas. Para obter informações adicionais, consulte [como criar uma conta Executar como](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) na documentação do Operations Manager.

Use as informações a seguir para definir a Operations Manager conta Executar como para Verificação de Integridade do System Center Operations Manager.

### <a name="set-the-run-as-account"></a>Definir a conta Executar como

A conta Executar como deve atender aos seguintes requisitos antes de continuar:

* Uma conta de usuário de domínio que seja membro do grupo local de administradores em todos os servidores que dão suporte a qualquer Operations Manager servidor de gerenciamento de funções, SQL Server hospedando o banco de dados operacional, data warehouse e ACS, relatórios, console Web e servidor de gateway.
* Função de administrador do Operations Manager para o grupo de gerenciamento que está sendo avaliado
* Se a conta não tiver direitos de sysadmin do SQL, execute o [script](#sql-script-to-grant-granular-permissions-to-the-run-as-account) para conceder permissões granulares à conta em cada instância de SQL Server que hospeda um ou todos os bancos de dados de Operations Manager.

1. No console do Operations Manager, selecione o botão de navegação **Administração** .
2. Em **configuração de executar como**, clique em **contas**.
3. No Assistente para **criação de conta Executar como** , na página **introdução** , clique em **Avançar**.
4. Na página **Propriedades gerais** , selecione **Windows** na lista **tipo de conta Executar como:** .
5. Digite um nome de exibição na caixa de texto **nome de exibição** e, opcionalmente, digite uma descrição na caixa **Descrição** e clique em **Avançar**.
6. Na página **segurança de distribuição** , selecione **mais seguro**.
7. Clique em **Criar**.  

Agora que a conta Executar como é criada, ela precisa ter como destino servidores de gerenciamento no grupo de gerenciamento e associada a um perfil executar como predefinido para que os fluxos de trabalho sejam executados usando as credenciais.  

1. Em **configuração de executar como**, **contas**, no painel de resultados, clique duas vezes na conta que você criou anteriormente.
2. Na guia **distribuição** , clique em **Adicionar** para a caixa **computadores selecionados** e adicione o servidor de gerenciamento para o qual distribuir a conta.  Clique em **OK** duas vezes para salvar suas alterações.
3. Em **configuração de executar como**, clique em **perfis**.
4. Procure o *perfil de avaliação do SCOM*.
5. O nome do perfil deve ser: *Microsoft verificação de integridade do System Center Operations Manager perfil executar como*.
6. Clique com o botão direito do mouse e atualize suas propriedades e adicione a conta Executar como criada recentemente que você criou anteriormente.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Script SQL para conceder permissões granulares para a conta Executar como

Execute o script SQL a seguir para conceder as permissões necessárias para a conta Executar como na instância de SQL Server usada pelo Operations Manager hospedando o banco de dados operacional, data warehouse e ACS.

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

### <a name="configure-the-health-check-rule"></a>Configurar a regra de verificação de integridade

O pacote de gerenciamento da solução Verificação de Integridade do System Center Operations Manager inclui uma regra chamada *Microsoft System Center Operations Manager executar regra de verificação de integridade*. Essa regra é responsável por executar a verificação de integridade. Para habilitar a regra e configurar a frequência, use os procedimentos a seguir.

Por padrão, a regra de verificação de integridade de execução do Microsoft System Center Operations Manager está desabilitada. Para executar a verificação de integridade, você deve habilitar a regra em um servidor de gerenciamento. Use as etapas a seguir.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Habilitar a regra para um servidor de gerenciamento específico

1. No espaço de trabalho **criação** do console de operações Operations Manager, procure a regra *Microsoft System Center Operations Manager executar regra de verificação de integridade* no painel **regras** .
2. Nos resultados da pesquisa, selecione aquele que inclui o tipo de texto *: servidor de gerenciamento*.
3. Clique com o botão direito do mouse na regra e clique em **substituições** > **para um objeto específico da classe: servidor de gerenciamento**.
4.  Na lista servidores de gerenciamento disponíveis, selecione o servidor de gerenciamento onde a regra deve ser executada.  Esse deve ser o mesmo servidor de gerenciamento configurado anteriormente para associar a conta Executar como a.
5.  Certifique-se de alterar o valor de substituição para **verdadeiro** para o valor do parâmetro **habilitado** .<br><br> ![](./media/scom-assessment/rule.png) de parâmetro de substituição

    Ainda nesta janela, configure a frequência de execução usando o procedimento a seguir.

#### <a name="configure-the-run-frequency"></a>Configurar a frequência de execução

A avaliação é configurada para ser executada a cada 10.080 minutos (ou sete dias) por padrão. Você pode substituir o valor por um valor mínimo de 1440 minutos (ou um dia). O valor representa o intervalo de tempo mínimo necessário entre as execuções de avaliação sucessivas. Para substituir o intervalo, use as etapas abaixo.

1. No espaço de trabalho **criação** do console do Operations Manager, procure a regra *Microsoft System Center Operations Manager executar a regra de verificação de integridade* na seção **regras** .
2. Nos resultados da pesquisa, selecione aquele que inclui o tipo de texto *: servidor de gerenciamento*.
3. Clique com o botão direito do mouse na regra e clique em **substituir a regra** > **para todos os objetos da classe: servidor de gerenciamento**.
4. Altere o valor do parâmetro **intervalo** para o valor do intervalo desejado. No exemplo a seguir, o valor é definido como 1440 minutos (um dia).<br><br> parâmetro de intervalo de ![](./media/scom-assessment/interval.png)<br>  

    Se o valor for definido para menos de 1440 minutos, a regra será executada em um intervalo de um dia. Neste exemplo, a regra ignora o valor de intervalo e é executada em uma frequência de um dia.


## <a name="understanding-how-recommendations-are-prioritized"></a>Entendendo como as recomendações são priorizadas

Cada recomendação feita recebe um valor de peso que identifica a importância relativa da recomendação. Somente as 10 recomendações mais importantes são mostradas.

### <a name="how-weights-are-calculated"></a>Como os pesos são calculados

Os pesos são valores agregados com base em três fatores-chave:

- A *probabilidade* de que um problema identificado causará problemas. Uma probabilidade mais alta é igual a uma pontuação geral maior para a recomendação.
- O *impacto* do problema na sua organização se isso causar um problema. Um impacto maior é igual a uma pontuação geral maior para a recomendação.
- O *esforço* necessário para implementar a recomendação. Um esforço mais alto é igual a uma pontuação geral menor para a recomendação.

O peso de cada recomendação é expresso como um percentual da pontuação total disponível para cada área de foco. Por exemplo, se uma recomendação na área de foco disponibilidade e continuidade de negócios tiver uma pontuação de 5%, implementar essa recomendação aumentará a pontuação geral de disponibilidade e continuidade dos negócios em 5%.

### <a name="focus-areas"></a>Áreas de foco

**Disponibilidade e continuidade dos negócios** – essa área de foco mostra recomendações para disponibilidade de serviço, resiliência de sua infraestrutura e proteção de negócios.

**Desempenho e escalabilidade** -essa área de foco mostra recomendações para ajudar a expansão da infraestrutura de ti de sua organização, garantir que seu ambiente de ti atenda aos requisitos de desempenho atuais e que seja capaz de responder às necessidades de infraestrutura em constante mudança.

**Atualização, migração e implantação** – essa área de foco mostra recomendações para ajudá-lo a atualizar, migrar e implantar SQL Server em sua infraestrutura existente.

**Operações e monitoramento** – essa área de foco mostra recomendações para ajudar a simplificar suas operações de ti, implementar a manutenção preventiva e maximizar o desempenho.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Você deve visar a pontuação de 100% em cada área de foco?

Não necessariamente. As recomendações são baseadas no conhecimento e nas experiências obtidas pelos engenheiros da Microsoft em milhares de visitas a clientes. No entanto, não há duas infraestruturas de servidor iguais, e recomendações específicas podem ser mais ou menos relevantes para você. Por exemplo, algumas recomendações de segurança podem ser menos relevantes se suas máquinas virtuais não estiverem expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para serviços que fornecem relatórios e coleta de dados ad hoc de baixa prioridade. Os problemas que são importantes para um negócio maduro podem ser menos importantes para uma inicialização. Talvez você queira identificar quais áreas de foco são suas prioridades e, em seguida, examinar como suas pontuações mudam ao longo do tempo.

Cada recomendação inclui diretrizes sobre por que é importante. Use estas diretrizes para avaliar se a implementação da recomendação é apropriada para você, considerando a natureza dos seus serviços de ti e as necessidades comerciais da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Usar recomendações da área de foco de verificação de integridade

Para poder usar uma solução de verificação de integridade no Log Analytics, você deve ter a solução instalada. Para ler mais sobre a instalação de soluções, consulte [instalar uma solução de gerenciamento](../../azure-monitor/insights/solutions.md). Após a instalação, você pode exibir o resumo das recomendações usando o bloco Verificação de Integridade do System Center Operations Manager na página **visão geral** do seu espaço de trabalho na portal do Azure.

Exiba as avaliações de conformidade resumidas para sua infraestrutura e, em seguida, faça drill-in nas recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para exibir as recomendações para uma área de foco e tomar uma ação corretiva
1. Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. No portal do Azure, clique em **Mais serviços**, que se encontra no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics**.
3. No painel de assinaturas do Log Analytics, selecione um espaço de trabalho e clique no item de menu **Resumo do espaço de trabalho** .  
4. Na página **visão geral** , clique no bloco **verificação de integridade do System Center Operations Manager** .
5. Na página **verificação de integridade do System Center Operations Manager** , examine as informações resumidas em uma das folhas da área de foco e clique em uma para exibir as recomendações para essa área de foco.
6. Em qualquer uma das páginas da área de foco, você pode exibir as recomendações priorizadas feitas para seu ambiente. Clique em uma recomendação em **objetos afetados** para exibir detalhes sobre por que a recomendação é feita.<br><br> área de foco ![](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Você pode tomar ações corretivas sugeridas em **ações sugeridas**. Quando o item for resolvido, as avaliações posteriores registrarão que as ações recomendadas foram executadas e a pontuação de conformidade aumentará. Os itens corrigidos aparecem como **objetos passados**.

## <a name="ignore-recommendations"></a>Ignorar recomendações

Se você tiver recomendações que deseja ignorar, poderá criar um arquivo de texto que Log Analytics usa para impedir que as recomendações apareçam nos resultados da avaliação.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Para identificar as recomendações que você deseja ignorar
1. Na portal do Azure na página do espaço de trabalho Log Analytics para seu espaço de trabalho selecionado, clique no item de menu **pesquisa de logs** .
2. Use a consulta a seguir para listar as recomendações que falharam para computadores em seu ambiente.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Se o espaço de trabalho tiver sido atualizado para o [novo log Analytics linguagem de consulta](../../azure-monitor/log-query/log-query-overview.md), a consulta acima mudaria para o seguinte.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Aqui está uma captura de tela mostrando a consulta de pesquisa de log:<br><br> ![pesquisa de registos](./media/scom-assessment/scom-log-search.png)<br>

3. Escolha as recomendações que você deseja ignorar. Você usará os valores para a Recomendaçãoid no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e usar um arquivo de texto IgnoreRecommendations. txt

1. Crie um arquivo chamado IgnoreRecommendations. txt.
2. Cole ou digite cada Recomendaçãoid para cada recomendação que você deseja que Log Analytics ignore em uma linha separada e, em seguida, salve e feche o arquivo.
3. Coloque o arquivo na pasta a seguir em cada computador em que você deseja que Log Analytics ignore as recomendações.
4. Na Operations Manager servidor de gerenciamento- *systemdrive*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas

1. Após a execução da próxima avaliação agendada, por padrão a cada sete dias, as recomendações especificadas são marcadas como ignoradas e não aparecerão no painel de verificação de integridade.
2. Você pode usar as consultas de pesquisa de log a seguir para listar todas as recomendações ignoradas.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Se o espaço de trabalho tiver sido atualizado para o [novo log Analytics linguagem de consulta](../../azure-monitor/log-query/log-query-overview.md), a consulta acima mudaria para o seguinte.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Se você decidir posteriormente que deseja ver as recomendações ignoradas, remova todos os arquivos IgnoreRecommendations. txt ou remova o as recommendationids deles.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>Perguntas frequentes sobre a solução Verificação de Integridade do System Center Operations Manager

*Adicionei a solução de verificação de integridade ao meu espaço de trabalho do Log Analytics. Mas não vejo as recomendações. Por que não?* Depois de adicionar a solução, use as seguintes etapas para exibir as recomendações no painel Log Analytics.  

- [Definir a conta Executar como para Verificação de Integridade do System Center Operations Manager](#operations-manager-run-as-accounts-for-log-analytics)  
- [Configurar a regra de Verificação de Integridade do System Center Operations Manager](#configure-the-health-check-rule)


*Há uma maneira de configurar a frequência com que a verificação é executada?* Sim. Consulte [Configurar a frequência de execução](#configure-the-run-frequency).

*Se outro servidor for descoberto após ter adicionado a solução de Verificação de Integridade do System Center Operations Manager, ele será verificado?* Sim, após a descoberta, ela é verificada, por padrão, a cada sete dias.

*Qual é o nome do processo que faz a coleta de dados?* AdvisorAssessment. exe

*Onde o processo AdvisorAssessment. exe é executado?* O AdvisorAssessment. exe é executado sob o processo HealthService do servidor de gerenciamento em que a regra de verificação de integridade está habilitada. Usando esse processo, a descoberta de todo o ambiente é obtida por meio da coleta de dados remota.

*Quanto tempo leva para a coleta de dados?* A coleta de dados no servidor leva cerca de uma hora. Pode levar mais tempo em ambientes que têm muitas Operations Manager instâncias ou bancos de dados.

*E se eu definir o intervalo da avaliação para menos de 1440 minutos?* A avaliação é pré-configurada para ser executada no máximo uma vez por dia. Se você substituir o valor do intervalo por um valor menor que 1440 minutos, a avaliação usará 1440 minutos como o valor do intervalo.

*Como saber se há falhas de pré-requisito?* Se a verificação de integridade foi executada e você não vê os resultados, é provável que alguns dos pré-requisitos para a verificação falharam. Você pode executar consultas: `Operation Solution=SCOMAssessment` e `SCOMAssessmentRecommendation FocusArea=Prerequisites` na pesquisa de logs para ver os pré-requisitos com falha.

*Há uma mensagem de `Failed to connect to the SQL Instance (….).` em falhas de pré-requisito. Qual é o problema?* AdvisorAssessment. exe, o processo que coleta dados, é executado sob o processo HealthService no servidor de gerenciamento. Como parte da verificação de integridade, o processo tenta se conectar ao SQL Server onde o banco de dados Operations Manager está presente. Esse erro pode ocorrer quando as regras de firewall bloqueiam a conexão com a instância de SQL Server.

*Que tipo de dados é coletado?* Os seguintes tipos de dados são coletados:-WMI data-Registry data-EventLog data-Operations Manager dados por meio do Windows PowerShell, consultas SQL e coletor de informações de arquivo.

*Por que é necessário configurar uma conta Executar como?* Com Operations Manager, várias consultas SQL são executadas. Para que eles sejam executados, você deve usar uma conta Executar como com as permissões necessárias. Além disso, as credenciais de administrador local são necessárias para consultar o WMI.

*Por que exibir apenas as 10 principais recomendações?* Em vez de fornecer uma lista completa e árdua de tarefas, recomendamos que você se concentre primeiro em abordar as recomendações priorizadas. Depois de solucioná-los, recomendações adicionais ficarão disponíveis. Se você preferir ver a lista detalhada, poderá exibir todas as recomendações usando a pesquisa de logs.

*Há uma maneira de ignorar uma recomendação?* Sim, consulte [ignorar recomendações](#ignore-recommendations).


## <a name="next-steps"></a>Passos seguintes

- [Pesquise logs](../../azure-monitor/log-query/log-query-overview.md) para saber como analisar dados de verificação de integridade do System Center Operations Manager detalhados e recomendações.
