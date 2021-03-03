---
title: Avaliar Gestor de Operações do Centro de Sistema com Monitor Azure
description: Pode utilizar a solução health Check do Gestor de Operações do System Center para avaliar o risco e a saúde dos seus ambientes num intervalo regular.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2018
ms.openlocfilehash: 35ae1e09fd0a06014a747cef99631a7bfe2dee1c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731413"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optimize your environment with the System Center Operations Manager Health Check (Preview) solution (Otimizar o ambiente com a solução Verificação de Estado de Funcionamento do System Center Operations Manager [Pré-visualização])

![Símbolo de verificação de saúde do gestor de operações do centro do sistema](./media/scom-assessment/scom-assessment-symbol.png)

Pode utilizar a solução de Verificação de Saúde do Gestor de Operações do System Center para avaliar o risco e a saúde do seu grupo de gestão de operações do System Center Operations Manager num intervalo regular. Este artigo ajuda-o a instalar, configurar e utilizar a solução para que possa tomar ações corretivas para potenciais problemas.

Esta solução fornece uma lista prioritária de recomendações específicas à infraestrutura de servidores implementadas. As recomendações são categorizadas em quatro áreas de foco, que o ajudam a entender rapidamente o risco e a tomar medidas corretivas.

As recomendações feitas baseiam-se no conhecimento e experiência adquiridos pelos engenheiros da Microsoft a partir de milhares de visitas ao cliente. Cada recomendação fornece orientações sobre o porquê de um problema poder ser importante para si e como implementar as alterações sugeridas.

Você pode escolher áreas de foco que são mais importantes para a sua organização e acompanhar o seu progresso para executar um ambiente livre de riscos e saudável.

Depois de ter adicionado a solução e de ser realizada uma avaliação, são apresentadas informações sumárias para áreas de foco no painel **de controlo de saúde do System Center Operations Manager** para a sua infraestrutura. As secções seguintes descrevem como utilizar a informação no painel de controlo **de saúde do System Center Operations Manager,** onde pode ver e, em seguida, tomar as ações recomendadas para o ambiente do seu Gestor de Operações.

![Azulejo de solução do Gestor de Operações do Centro de Sistema](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Painel de verificação de verificação de saúde do gestor de operações do centro do sistema](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução

A solução funciona com o Microsoft System Center 2012 Operations Manager Service Pack 1, Microsoft System Center 2012 R2 Gestor de Operações, Microsoft System Center 2016 Operations Manager, Microsoft System Center 2016 Operations Manager e Microsoft System Center Operations Manager 1807. Uma versão suportada do Quadro .NET 4.6.2 deve ser instalada em cada servidor de gestão.

Utilize as seguintes informações para instalar e configurar a solução.

- Antes de utilizar a solução Health Check no Log Analytics, tem de ter a solução instalada. Instale a solução a partir do [mercado Azure.](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview)

- Depois de adicionar a solução ao espaço de trabalho, o azulejo **health check do System Center Operations Manager** no painel de instrumentos apresenta uma configuração adicional necessária. Clique no azulejo e siga os passos de configuração mencionados na página

  ![Azulejo do painel do Gestor de Operações do Centro do Sistema](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> A configuração do Gestor de Operações do Centro de Sistema pode ser feita utilizando um script seguindo os passos mencionados na página de configuração da solução em Log Analytics.

 Para configurar a avaliação através da consola Operations Manager Operations, execute os passos abaixo na seguinte ordem:
1. [Definir a conta run como conta para System Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
2. Configure a regra do Gestor de Verificação de Saúde do Gestor de Operações do System Center

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>Detalhes da recolha de dados da Verificação de Estado de Funcionamento do System Center Operations Manager

A solução Health Check do Gestor de Operações do Sistema do System Center recolhe dados das seguintes fontes:

* Registo
* Windows Management Instrumentation (WMI)
* Registo de eventos
* Dados de ficheiros
* Diretamente do Gestor de Operações utilizando consultas PowerShell e SQL, a partir de um servidor de gestão que especificou.  

Os dados são recolhidos no servidor de gestão e reencaminhados para Log Analytics a cada sete dias.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Contas Run As do Operations Manager para o Log Analytics

O Log Analytics baseia-se em pacotes de gestão para cargas de trabalho para fornecer serviços de valor acrescentado. Cada carga de trabalho requer privilégios específicos da carga de trabalho para executar pacotes de gestão num contexto de segurança diferente, como uma conta de utilizador de domínio. Configure um Gestor de Operações Executado como conta com credenciais privilegiadas. Para obter informações adicionais, consulte [Como criar uma conta Run As](/previous-versions/system-center/system-center-2012-R2/hh321655(v=sc.12)) na documentação do Gestor de Operações.

Utilize as seguintes informações para definir o Gestor de Operações executado como conta para o System Center Operations Manager Health Check.

### <a name="set-the-run-as-account"></a>Definir a conta Run As

A conta Run As deve satisfazer os seguintes requisitos antes de prosseguir:

* Uma conta de utilizador de domínio que é membro do grupo de Administradores locais em todos os servidores que suportam qualquer função de Gestor de Operações - Servidor de Gestão, SQL Server que hospeda a base de dados operacional, de armazém de dados e ACS, Reporting, Consola Web e Gateway.
* Papel de Administrador de Operação para o grupo de gestão que está a ser avaliado
* Se a conta não tiver direitos de sysadmin SQL, então execute o [script](#sql-script-to-grant-granular-permissions-to-the-run-as-account) para conceder permissões granulares à conta em cada instância do SQL Server que hospeda uma ou todas as bases de dados do Gestor de Operações.

1. Na Consola Gestor de Operações, selecione o botão de navegação **Administração.**
2. Em **'Executar como configuração'** clique em **Contas**.
3. No **'Criar Como Assistente de Conta',** na página **Introdução** clique em **Seguinte**.
4. Na página **Propriedades Gerais,** selecione **Windows** in the **Run As Account type:** list.
5. Digite um nome de exibição na caixa de texto **do Nome do Visor** e digite opcionalmente uma descrição na caixa **Descrição** e, em seguida, clique em **Seguinte**.
6. Na página **de Segurança de Distribuição,** selecione **Mais seguro**.
7. Clique em **Criar**.  

Agora que a conta Run As foi criada, precisa de direcionar os servidores de gestão no grupo de gestão e associado a uma execução pré-definida Como perfil para que os fluxos de trabalho sejam executados usando as credenciais.  

1. Em **'Executar Como Configuração**' ' Contas' , no painel de **resultados,** clique duas vezes na conta que criou anteriormente.
2. No separador **Distribuição,** clique em **Adicionar** para a caixa de **computadores Selecionados** e adicione o servidor de gestão para distribuir a conta.  Clique **em OK** duas vezes para guardar as suas alterações.
3. Em **Execução como configuração**, clique em **Perfis**.
4. Procurar o *Perfil de Avaliação SCOM.*
5. O nome do perfil deve ser: *Microsoft System Center Operations Manager Health Check Run As Profile*.
6. Clique com o botão direito e atualize as suas propriedades e adicione a conta Run As recentemente criada anteriormente.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Script de SQL para conceder permissões granulares à conta Run As

Execute o seguinte script SQL para conceder as permissões necessárias à conta Run As na instância sql Server usada pelo Gestor de Operações que hospeda a base de dados operacional, de dados e acs.

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

O pacote de gestão do Gestor de Operações do Sistema Health Check inclui uma regra chamada *Microsoft System Center Operations Manager Run Health Check Rule*. Esta regra é responsável pela execução do exame de saúde. Para ativar a regra e configurar a frequência, utilize os procedimentos abaixo.

Por predefinição, o Gestor de Operações do Microsoft System Center Executar a Regra de Verificação de Saúde está desativado. Para executar a verificação de saúde, deve ativar a regra num servidor de gestão. Utilize os passos seguintes.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Ativar a regra para um servidor de gestão específico

1. No espaço de trabalho de **autoria** da consola Operations Manager Operations, procure a regra microsoft System Center Operations Manager executar regra de *verificação de saúde* no painel **de regras.**
2. Nos resultados da pesquisa, selecione o que inclui o *tipo de texto: Servidor de gestão*.
3. Clique com o direito na regra e, em seguida, clique em **Overrides**  >  **Para um objeto específico de classe: Management Server**.
4.  Na lista de servidores de gestão disponíveis, selecione o servidor de gestão onde a regra deve ser executada.  Este deve ser o mesmo servidor de gestão que configuraste anteriormente para associar a conta Run As.
5.  Certifique-se de que altera o valor de substituição para **Verdadeiro** para o valor do parâmetro **Ativado.**<br><br> ![sobreposição parâmetro](./media/scom-assessment/rule.png)

    Enquanto ainda estiver nesta janela, configuure a frequência de funcionamento utilizando o procedimento seguinte.

#### <a name="configure-the-run-frequency"></a>Configurar a frequência de execução

A avaliação é configurada para ser executada a cada 10.080 minutos (ou sete dias) por defeito. Pode sobrepor o valor a um valor mínimo de 1440 minutos (ou um dia). O valor representa o intervalo de tempo mínimo exigido entre as sucessivas execuções de avaliação. Para anular o intervalo, utilize os passos abaixo.

1. No espaço de trabalho de **autoria** da consola Gestor de Operações, procure a regra *do Gestor de Operações do Microsoft System Center executar a regra de verificação de saúde* na secção **Regras.**
2. Nos resultados da pesquisa, selecione o que inclui o *tipo de texto: Servidor de gestão*.
3. Clique com o direito na regra e, em seguida, clique em **Override the Rule**  >  **Para todos os objetos de classe: Servidor de gestão**.
4. Altere o valor do parâmetro **intervalo** para o valor de intervalo pretendido. No exemplo abaixo, o valor é definido para 1440 minutos (um dia).<br><br> ![parâmetro de intervalo](./media/scom-assessment/interval.png)<br>  

    Se o valor for definido para menos de 1440 minutos, então a regra é executado num intervalo de um dia. Neste exemplo, a regra ignora o valor do intervalo e corre numa frequência de um dia.


## <a name="understanding-how-recommendations-are-prioritized"></a>Compreender como é definida a prioridade das recomendações

Todas as recomendações feitas recebem um valor de ponderação que identifica a importância relativa da recomendação. Apenas são apresentadas as 10 recomendações mais importantes.

### <a name="how-weights-are-calculated"></a>Método de cálculo das ponderações

As ponderações são valores agregados baseados em três factores-chave:

- A *probabilidade de* um problema identificado causar problemas. Uma maior probabilidade equivale a uma pontuação global maior para a recomendação.
- O *impacto* do problema na sua organização se isso causar um problema. Um impacto mais elevado equivale a uma pontuação global maior para a recomendação.
- O *esforço* necessário para implementar a recomendação. Um esforço mais elevado equivale a uma pontuação global menor para a recomendação.

A ponderação de cada recomendação é expressa em percentagem da pontuação total disponível para cada área de foco. Por exemplo, se uma recomendação na área de foco de Disponibilidade e Continuidade empresarial tiver uma pontuação de 5%, implementar essa recomendação aumenta a sua pontuação global de Disponibilidade e Continuidade de Negócios em 5%.

### <a name="focus-areas"></a>Áreas em foco

**Disponibilidade e Continuidade empresarial** - Esta área de foco mostra recomendações para disponibilidade de serviços, resiliência da sua infraestrutura e proteção empresarial.

**Performance e Escalaability** - Esta área de foco mostra recomendações para ajudar a infraestrutura de TI da sua organização a crescer, garantir que o seu ambiente de TI cumpre os requisitos de desempenho atuais, e é capaz de responder às necessidades de infraestrutura em mudança.

**Upgrade, Migração e Implementação** - Esta área de foco mostra recomendações para ajudá-lo a atualizar, migrar e implementar o SQL Server para a sua infraestrutura existente.

**Operações e Monitorização** - Esta área de foco mostra recomendações para ajudar a agilizar as suas operações de TI, implementar a manutenção preventiva e maximizar o desempenho.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Deve visar uma pontuação de 100% em cada uma das áreas em foco?

Não necessariamente. As recomendações baseiam-se nos conhecimentos e experiências adquiridas pelos engenheiros da Microsoft em milhares de visitas ao cliente. No entanto, não existem duas infraestruturas de servidores iguais, e recomendações específicas podem ser mais ou menos relevantes para si. Por exemplo, algumas recomendações de segurança podem ser menos relevantes se as suas máquinas virtuais não estiverem expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para serviços que fornecem baixa prioridade a recolha e reporte de dados ad hoc. As questões que são importantes para um negócio maduro podem ser menos importantes para uma start-up. Pode querer identificar quais as áreas de foco que são as suas prioridades e, em seguida, ver como as suas pontuações mudam ao longo do tempo.

Todas as recomendações incluem orientações sobre o porquê de ser importante. Utilize esta orientação para avaliar se a implementação da recomendação é adequada para si, dada a natureza dos seus serviços de TI e as necessidades empresariais da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Use recomendações de área de foco de verificação de saúde

Antes de utilizar uma solução de verificação de saúde no Log Analytics, tem de ter a solução instalada. Para ler mais sobre a instalação de soluções, consulte [instalar uma solução de gestão.](./solutions.md) Depois de instalado, pode ver o resumo das recomendações utilizando o azulejo do System Center Operations Check na página **'Visão Geral'** para o seu espaço de trabalho no portal Azure.

Veja as avaliações de conformidade resumidas para a sua infraestrutura e, em seguida, faça recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para ver recomendações para uma área de foco e tomar medidas corretivas
1. Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com) .
2. No portal do Azure, clique em **Mais serviços**, que se encontra no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione o **Log Analytics**.
3. No painel de subscrições do Log Analytics, selecione um espaço de trabalho e, em seguida, clique no item do menu **de resumo do Espaço de Trabalho.**  
4. Na página **'Visão Geral',** clique no azulejo **do Gestor de Operações do System Center.**
5. Na página **'Health Check' do System Center Operations Manager,** reveja a informação sumária numa das lâminas da área de foco e, em seguida, clique numa para visualizar recomendações para essa área de foco.
6. Em qualquer uma das páginas da área de foco, você pode ver as recomendações prioritárias feitas para o seu ambiente. Clique numa recomendação em **Objetos Afetados** para ver detalhes sobre o porquê da recomendação ser feita.<br><br> ![área de foco](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Pode tomar medidas corretivas sugeridas em **Ações Sugeridas**. Quando o artigo tiver sido abordado, as avaliações posteriores registarão que foram tomadas as ações recomendadas e a sua pontuação de conformidade aumentará. Os itens corrigidos aparecem como **Objetos Passados.**

## <a name="ignore-recommendations"></a>Ignorar recomendações

Se tiver recomendações que pretende ignorar, pode criar um ficheiro de texto que o Log Analytics utiliza para impedir que as recomendações apareçam nos resultados da sua avaliação.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Para identificar recomendações que quer ignorar
1. No portal Azure na página do espaço de trabalho Log Analytics para o seu espaço de trabalho selecionado, clique no item do menu **'Registar'.**
2. Utilize a seguinte consulta para listar recomendações que falharam para computadores no seu ambiente.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Se o seu espaço de trabalho foi atualizado para o [novo idioma de consulta log analytics,](../logs/log-query-overview.md)então a consulta acima mudaria para o seguinte.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Aqui está uma imagem mostrando a consulta de Pesquisa de Registo:<br><br> ![pesquisa de registos](./media/scom-assessment/scom-log-search.png)<br>

3. Escolha recomendações que queira ignorar. Usará os valores para RecomendaçãoId no próximo procedimento.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e utilizar um ficheiro de texto IgnoreRecommendations.txt

1. Crie um ficheiro chamado IgnoreRecommendations.txt.
2. Cole ou digite cada RecomendaçãoId para cada recomendação que pretende que o Log Analytics ignore numa linha separada e, em seguida, guarde e feche o ficheiro.
3. Coloque o ficheiro na seguinte pasta em cada computador onde pretende que o Log Analytics ignore as recomendações.
4. No servidor de gestão do Gestor de Operações - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas

1. Após a próxima avaliação programada ser executado, por padrão a cada sete dias, as recomendações especificadas são marcadas ignoradas e não aparecerão no painel de verificação de saúde.
2. Pode utilizar as seguintes consultas de Pesquisa de Registo para listar todas as recomendações ignoradas.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Se o seu espaço de trabalho foi atualizado para o [novo idioma de consulta log analytics,](../logs/log-query-overview.md)então a consulta acima mudaria para o seguinte.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Se decidir mais tarde que pretende ver recomendações ignoradas, remova quaisquer IgnoreRecommendations.txt ficheiros ou poderá remover os Recomendações dos mesmos.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>FAQ sobre a solução Verificação de Estado de Funcionamento do System Center Operations Manager

*Adicionei a solução health Check ao meu espaço de trabalho Log Analytics. Mas não vejo as recomendações. Porque não?* Depois de adicionar a solução, utilize os seguintes passos para ver as recomendações no painel Log Analytics.  

- [Definir a conta run como conta para System Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
- [Configure a regra do Gestor de Verificação de Saúde do Gestor de Operações do System Center](#configure-the-health-check-rule)


*Há uma maneira de configurar com que frequência o cheque é executado?* Sim. Consulte [a configuração da frequência de funcionamento](#configure-the-run-frequency).

*Se outro servidor for descoberto depois de eu ter adicionado a solução de Verificação de Saúde do Gestor de Operações do System Center, será verificado?* Sim, após a descoberta é verificado a partir daí, por defeito a cada sete dias.

*Qual é o nome do processo que faz a recolha de dados?* AdvisorAssessment.exe

*Onde corre o processo AdvisorAssessment.exe?* AdvisorAssessment.exe funciona no âmbito do processo HealthService do servidor de gestão onde a regra de verificação de saúde está ativada. Utilizando esse processo, a descoberta de todo o seu ambiente é conseguida através da recolha remota de dados.

*Quanto tempo demora a recolha de dados?* A recolha de dados no servidor demora cerca de uma hora. Pode demorar mais tempo em ambientes que tenham muitas instâncias ou bases de dados do Gestor de Operações.

*E se eu definir o intervalo da avaliação para menos de 1440 minutos?* A avaliação está pré-configurada para funcionar no máximo uma vez por dia. Se se sobrepor o valor do intervalo a um valor inferior a 1440 minutos, a avaliação utiliza 1440 minutos como valor de intervalo.

*Como saber se há falhas pré-requisitos?* Se o exame de saúde correu e não vê resultados, então é provável que alguns dos pré-requisitos para o cheque falhou. Pode executar consultas: `Operation Solution=SCOMAssessment` e em Log Search para ver os `SCOMAssessmentRecommendation FocusArea=Prerequisites` pré-requisitos falhados.

*Há uma `Failed to connect to the SQL Instance (….).` mensagem em falhas pré-requisitos. Qual é o problema?* AdvisorAssessment.exe, o processo que recolhe dados, funciona no âmbito do processo HealthService no servidor de gestão. Como parte da verificação de saúde, o processo tenta ligar-se ao SQL Server onde está presente a base de dados do Gestor de Operações. Este erro pode ocorrer quando as regras de firewall bloqueiam a ligação à instância sql Server.

*Que tipo de dados são recolhidos?* São recolhidos os seguintes tipos de dados: - Dados do WMI - Dados do Registo - Dados do EventLog - Dados do Gestor de Operações através do Windows PowerShell, das consultas SQL e do colecionador de informações de ficheiros.

*Por que tenho que configurar uma conta run as?* Com o Gestor de Operações, são executadas várias consultas SQL. Para que sejam executados, deve utilizar uma Conta Como Correr com as permissões necessárias. Além disso, as credenciais de administrador local são necessárias para consultar o WMI.

*Porquê exibir apenas as 10 melhores recomendações?* Em vez de lhe dar uma lista exaustiva e esmagadora de tarefas, recomendamos que se concentre em abordar as recomendações prioritárias primeiro. Depois de os abordar, serão disponibilizadas recomendações adicionais. Se preferir ver a lista detalhada, pode ver todas as recomendações usando a Registar.

*Há alguma maneira de ignorar uma recomendação?* Sim, consulte as [recomendações do Ignore.](#ignore-recommendations)


## <a name="next-steps"></a>Passos seguintes

- [Pesquisa de registos](../logs/log-query-overview.md) para aprender a analisar dados e recomendações detalhadas do System Center Operations Manager Health Check.

