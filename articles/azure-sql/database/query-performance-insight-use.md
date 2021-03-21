---
title: Query Performance Insight
description: A monitorização do desempenho de consulta identifica as consultas mais consumidas por CPU e de longa duração para bases de dados individuais e agrárias na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: db24f280f66e567572821297cfc9bb9b1e19743b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222348"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Insight de desempenho de consulta para base de dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

O Query Performance Insight disponibiliza análise de consultas inteligente para bases de dados individuais e de conjunto. Ajuda a identificar as principais consultas que consomem recursos e de execução prolongada na sua carga de trabalho. Deste modo, ajuda a localizar as consultas que devem ser otimizadas para melhorar o desempenho geral da carga de trabalho e utilizar eficientemente o recurso que está a pagar. O Insight de Desempenho de Consulta ajuda-o a gastar menos tempo a resolver o desempenho da base de dados, fornecendo:

* Informação mais profunda sobre o consumo de recursos de bases de dados (DTU)
* Detalhes sobre as principais consultas de base de dados por CPU, contagem de duração e execução (potenciais candidatos à afinação para melhorias de desempenho)
* A capacidade de aprofundar detalhes de uma consulta, de ver o texto de consulta e a história da utilização de recursos
* Anotações que mostram recomendações de desempenho de conselheiros de [base de dados](database-advisor-implement-performance-recommendations.md)

![Query Performance Insight](./media/query-performance-insight-use/opening-title.png)

## <a name="prerequisites"></a>Pré-requisitos

O Insight de Desempenho da Consulta requer que [a Loja de Consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) esteja ativa na sua base de dados. É automaticamente ativado para todas as bases de dados na Base de Dados Azure SQL por padrão. Se a Loja de Consultas não estiver a funcionar, o portal Azure irá solicitar-lhe que o ative.

> [!NOTE]
> Se a mensagem "Query Store não estiver devidamente configurada nesta base de dados" aparecer no portal, consulte [a otimização da configuração da Loja de Consultas](#optimize-the-query-store-configuration).

## <a name="permissions"></a>Permissões

Você precisa das seguintes permissões [de controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md) para usar o Insight de Desempenho de Consulta:

* **As** permissões de leitor , **Proprietário**, **Contribuinte**, **DB DB** ou **SQL Server Contributor** são necessárias para visualizar as principais consultas e gráficos que consomem recursos.
* **O Proprietário**, **Contribuinte**, **SqL DB Contributor,** ou **permissões de contribuinte do sql server** são necessárias para visualizar texto de consulta.

## <a name="use-query-performance-insight"></a>Utilizar o Query Performance Insight

Consulta Performance Insight é fácil de usar:

1. Abra o [portal Azure](https://portal.azure.com/) e encontre uma base de dados que pretende examinar.
2. A partir do menu do lado esquerdo, abra **o Smart Performance**  >  **Query Performance Insight**.
  
   ![Consulta Performance Insight no menu](./media/query-performance-insight-use/tile.png)

3. No primeiro separador, reveja a lista das principais consultas que consomem recursos.
4. Selecione uma consulta individual para ver os seus detalhes.
5. Abra as  >  **recomendações de Desempenho** Inteligente e verifique se existem recomendações de desempenho disponíveis. Para obter mais informações sobre recomendações de desempenho incorporadas, consulte [o Azure SQL Database Advisor](database-advisor-implement-performance-recommendations.md).
6. Utilize sliders ou ícones de zoom para alterar o intervalo observado.

   ![Painel de desempenho](./media/query-performance-insight-use/performance.png)

> [!NOTE]
> Para que a Base de Dados Azure SQL forneça a informação no Insight de Desempenho de Consulta, a Loja de Consultas precisa de capturar algumas horas de dados. Se a base de dados não tiver atividade ou se a Loja de Consulta não estiver ativa durante um determinado período, os gráficos ficarão vazios quando a Consulta Performance Insight mostrar esse intervalo de tempo. Pode ativar a Loja de Consultas a qualquer momento se não estiver a funcionar. Para mais informações, consulte [as melhores práticas com a Loja de Consultas.](/sql/relational-databases/performance/best-practice-with-the-query-store)
>

Para recomendações de desempenho da base de dados, selecione [Recomendações](database-advisor-implement-performance-recommendations.md) sobre a lâmina de navegação 'Insight de Desempenho de Consulta'.

![O separador recomendações](./media/query-performance-insight-use/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>Rever consultas de consumo de CPU de topo

Por predefinição, o Query Performance Insight mostra as cinco principais consultas que consomem CPU quando a abre pela primeira vez.

1. Selecione ou limpe as consultas individuais para incluí-las ou excluí-las da tabela utilizando caixas de verificação.

   A linha de cima mostra uma percentagem global de DTU para a base de dados. As barras mostram a percentagem de CPU que as consultas selecionadas consumiram durante o intervalo selecionado. Por exemplo, se **a semana passada** for selecionada, cada barra representa um único dia.

   ![Consultas de topo](./media/query-performance-insight-use/top-queries.png)

   > [!IMPORTANT]
   > A linha DTU mostrada é agregada a um valor máximo de consumo em períodos de uma hora. Destina-se a uma comparação de alto nível apenas com estatísticas de execução de consultas. Em alguns casos, a utilização do DTU pode parecer demasiado elevada em comparação com consultas executadas, mas este pode não ser o caso.
   >
   > Por exemplo, se uma consulta atingir o limite máximo de DTU para 100% apenas por alguns minutos, a linha DTU em Query Performance Insight mostrará toda a hora de consumo como 100% (consequência do valor máximo agregado).
   >
   > Para uma comparação mais fina (até um minuto), considere criar um gráfico de utilização DTU personalizado:
   >
   > 1. No portal Azure, selecione **Azure SQL Database**  >  **Monitoring**.
   > 2. Selecione **Métricas**.
   > 3. Selecione **+Adicionar gráfico**.
   > 4. Selecione a percentagem de DTU na tabela.
   > 5. Além disso, selecione **Last 24 horas** no menu superior esquerdo e altere-o para um minuto.
   >
   > Utilize o gráfico DTU personalizado com um nível de detalhes mais fino para comparar com o gráfico de execução de consultas.

   A grelha inferior mostra informações agregadas para as consultas visíveis:

   * Identificação de consulta, que é um identificador único para a consulta na base de dados.
   * CPU por consulta durante um intervalo observável, que depende da função de agregação.
   * Duração por consulta, que também depende da função de agregação.
   * Número total de execuções para uma consulta específica.

2. Se os seus dados se tornarem obsoletos, selecione o botão **Refresh.**

3. Utilize sliders e botões de zoom para alterar o intervalo de observação e investigar picos de consumo:

   ![Sliders e botões de zoom para alterar o intervalo](./media/query-performance-insight-use/zoom.png)

4. Opcionalmente, pode selecionar o separador **Personalizado** para personalizar a vista para:

   * Métrica (CPU, duração, contagem de execução).
   * Intervalo de tempo (dura 24 horas, semana passada ou mês passado).
   * Número de consultas.
   * Função de agregação.
  
   ![Separador personalizado](./media/query-performance-insight-use/custom-tab.png)
  
5. Selecione o botão **Go >** para ver a vista personalizada.

   > [!IMPORTANT]
   > O Insight de Desempenho de Consulta limita-se a exibir as consultas de consumo top 5-20, dependendo da sua seleção. A sua base de dados pode executar muitas mais consultas para além das principais apresentadas, e estas consultas não serão incluídas na tabela.
   >
   > Pode existir um tipo de carga de trabalho de base de dados em que muitas consultas menores, para além das mais antigas mostradas, funcionam frequentemente e usam a maioria do DTU. Estas consultas não aparecem na tabela de desempenho.
   >
   > Por exemplo, uma consulta pode ter consumido uma quantidade substancial de DTU durante algum tempo, embora o seu consumo total no período observado seja inferior às outras consultas mais consumidas. Neste caso, a utilização de recursos desta consulta não apareceria na tabela.
   >
   > Se precisar de compreender execuções de consultas superiores para além das limitações do Query Performance Insight, considere utilizar [o Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) para monitorização avançada do desempenho da base de dados e resolução de problemas.
   >

## <a name="view-individual-query-details"></a>Ver detalhes de consulta individual

Para ver detalhes de consulta:

1. Selecione qualquer consulta na lista de consultas de topo.

    ![Lista de consultas de topo](./media/query-performance-insight-use/details.png)

   Uma visão detalhada abre- se. Mostra o consumo, duração e execução da CPU ao longo do tempo.

2. Selecione as funcionalidades do gráfico para mais detalhes.

   * O gráfico superior mostra uma linha com a percentagem de DTU de base de dados global. As barras são a percentagem de CPU que a consulta selecionada consumiu.
   * O segundo gráfico mostra a duração total da consulta selecionada.
   * O gráfico inferior mostra o número total de execuções pela consulta selecionada.

   ![Detalhes de consulta](./media/query-performance-insight-use/query-details.png)

3. Opcionalmente, utilize sliders, use botões de zoom ou selecione Definições para personalizar a forma como os **dados** de consulta são apresentados, ou para escolher um intervalo de tempo diferente.

   > [!IMPORTANT]
   > Consulta Performance Insight não captura quaisquer consultas DDL. Em alguns casos, pode não capturar todas as consultas ad hoc.
   >

## <a name="review-top-queries-per-duration"></a>Rever consultas de topo por duração

Duas métricas no Insight de Desempenho de Consulta podem ajudá-lo a encontrar potenciais estrangulamentos: duração e contagem de execução.

As consultas de longa duração têm o maior potencial para bloquear recursos por mais tempo, bloquear outros utilizadores e limitar a escalabilidade. São também os melhores candidatos à otimização. Para mais informações, consulte [Compreender e resolver problemas de bloqueio do Azure SQL](understand-resolve-blocking.md).

Para identificar consultas de longa duração:

1. Abra o **separador Personalizado** em Consulta Performance Insight para a base de dados selecionada.
2. Altere as métricas para **a duração**.
3. Selecione o número de consultas e o intervalo de observação.
4. Selecione a função de agregação:

   * **A soma** soma todo o tempo de execução de consultas para todo o intervalo de observação.
   * **Max** encontra consultas em que o tempo de execução foi máximo para todo o intervalo de observação.
   * **A Avg** encontra o tempo médio de execução de todas as execuções de consultas e mostra-lhe as melhores para estas médias.

   ![Duração da consulta](./media/query-performance-insight-use/top-duration.png)

5. Selecione o botão **Go >** para ver a vista personalizada.

   > [!IMPORTANT]
   > A regulação da vista de consulta não atualiza a linha DTU. A linha DTU mostra sempre o valor máximo de consumo para o intervalo.
   >
   > Para compreender o consumo de DTU de base de dados com mais detalhes (até um minuto), considere criar um gráfico personalizado no portal Azure:
   >
   > 1. Selecione **Azure SQL Database**  >  **Monitoring**.
   > 2. Selecione **Métricas**.
   > 3. Selecione **+Adicionar gráfico**.
   > 4. Selecione a percentagem de DTU na tabela.
   > 5. Além disso, selecione **Last 24 horas** no menu superior esquerdo e altere-o para um minuto.
   >
   > Recomendamos que utilize o gráfico DTU personalizado para comparar com o gráfico de desempenho da consulta.
   >

## <a name="review-top-queries-per-execution-count"></a>Rever consultas de topo por contagem de execução

Uma aplicação de utilizador que utilize a base de dados pode ficar lenta, mesmo que um elevado número de execuções possa não estar a afetar a própria base de dados e o uso dos recursos seja reduzido.

Em alguns casos, uma alta contagem de execuções pode levar a mais viagens de ida e volta em rede. As viagens de ida e volta afetam o desempenho. Estão sujeitos à latência da rede e à latência do servidor a jusante.

Por exemplo, muitos sites orientados por dados acedem fortemente à base de dados para cada pedido do utilizador. Embora a ligação de agrupamentos ajude, o aumento do tráfego de rede e a carga de processamento no servidor podem atrasar o desempenho. Em geral, mantenha as viagens de ida e volta ao mínimo.

Para identificar consultas frequentemente executadas ("chatty") :

1. Abra o **separador Personalizado** em Consulta Performance Insight para a base de dados selecionada.
2. Altere as métricas para a **contagem de execução.**
3. Selecione o número de consultas e o intervalo de observação.
4. Selecione o botão **Go >** para ver a vista personalizada.

   ![Contagem de execução de consultas](./media/query-performance-insight-use/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Compreender anotações de afinação de desempenho

Ao explorar a sua carga de trabalho no Query Performance Insight, poderá notar ícones com uma linha vertical no topo da tabela.

Estes ícones são anotações. Eles mostram recomendações de desempenho do [Azure SQL Database Advisor](database-advisor-implement-performance-recommendations.md). Ao pairar sobre uma anotação, você pode obter informações resumidas sobre recomendações de desempenho.

   ![Anotação de consulta](./media/query-performance-insight-use/annotation.png)

Se quiser compreender mais ou aplicar a recomendação do consultor, selecione o ícone para abrir detalhes da ação recomendada. Se esta é uma recomendação ativa, pode aplicá-la imediatamente a partir do portal.

   ![Detalhes da anotação de consulta](./media/query-performance-insight-use/annotation-details.png)

Em alguns casos, devido ao nível de zoom, é possível que anotações próximas umas das outras sejam colapsadas numa única anotação. Consulta Performance Insight representa isto como um ícone de anotação de grupo. Selecionar o ícone de anotação de grupo abre uma nova lâmina que lista as anotações.

Correlacionar consultas e ações de afinação de desempenho pode ajudá-lo a entender melhor a sua carga de trabalho.

## <a name="optimize-the-query-store-configuration"></a>Otimizar a configuração da Loja de Consultas

Ao utilizar o Insight de Desempenho de Consulta, poderá ver as seguintes mensagens de erro da Loja de Consultas:

* "A Loja de Consultas não está devidamente configurada nesta base de dados. Clique aqui para saber mais."
* "A Loja de Consultas não está devidamente configurada nesta base de dados. Clique aqui para alterar as definições."

Estas mensagens geralmente aparecem quando a Loja de Consulta não pode recolher novos dados.

O primeiro caso acontece quando a Loja de Consulta está no estado de leitura e os parâmetros são definidos da melhor forma. Pode corrigi-lo aumentando o tamanho da loja de dados ou limpando a Loja de Consultas. (Se limpar a Loja de Consultas, perder-se-á toda a telemetria previamente recolhida.)

   ![Detalhes da Loja de Consultas](./media/query-performance-insight-use/qds-off.png)

O segundo caso acontece quando a Loja de Consultas não está ativada, ou os parâmetros não são definidos da melhor forma. Pode alterar a política de retenção e captura, e também ativar a Query Store, executando os seguintes comandos fornecidos pelo [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) ou pelo portal Azure.

### <a name="recommended-retention-and-capture-policy"></a>Política recomendada de retenção e captura

Existem dois tipos de políticas de retenção:

* **Com base em tamanho**: Se esta política for definida como **AUTO,** limpará os dados automaticamente quando se atingir o tamanho máximo próximo.
* **Com base no tempo**: Por defeito, esta política está definida para 30 dias. Se a Loja de Consulta ficar sem espaço, eliminará informações de consulta com mais de 30 dias.

Pode definir a política de captura para:

* **Todas**: A Loja de Consultas captura todas as consultas.
* **Auto**: A Loja de Consultas ignora consultas e consultas pouco frequentes com a duração insignificante da compilação e execução. Os limiares para a contagem de execuções, a duração da compilação e a duração do tempo de execução são determinados internamente. Esta é a opção por defeito.
* **Nenhuma**: A Loja de Consultas deixa de capturar novas consultas, mas as estatísticas de tempo de execução para consultas já capturadas ainda são recolhidas.

Recomendamos que se ajustem todas as políticas para **AUTO** e a política de limpeza para 30 dias executando os seguintes comandos da [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) ou do portal Azure. (Substitua `YourDB` pelo nome da base de dados.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Aumente o tamanho da Loja de Consultas ligando-se a uma base de dados através [do SSMS](/sql/ssms/download-sql-server-management-studio-ssms) ou do portal Azure e executando a seguinte consulta. (Substitua `YourDB` pelo nome da base de dados.)

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

A aplicação destas definições irá eventualmente fazer com que a Loja de Consultas recolha telemetria para novas consultas. Se precisar da Loja de Consultas para estar operacional imediatamente, pode optar opcionalmente por limpar a Loja de Consultas executando a seguinte consulta através do SSMS ou do portal Azure. (Substitua `YourDB` pelo nome da base de dados.)

> [!NOTE]
> A execução da seguinte consulta eliminará toda a telemetria monitorizada previamente recolhida na Loja de Consultas.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>Passos seguintes

Considere usar [o Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) para monitorização avançada do desempenho de uma grande frota de bases de dados individuais e agalhadas, piscinas elásticas, instâncias geridas e bases de dados de casos.