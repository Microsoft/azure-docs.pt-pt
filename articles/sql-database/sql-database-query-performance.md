---
title: Query Performance Insight
description: A monitorização do desempenho da consulta identifica as consultas mais consumistas e de longa duração do CPU para bases de dados individuais e agrofadas na base de dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: f5998fde6659715de4fcb533cb0f41a8939b1c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214048"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Consulta Performance Insight para Base de Dados SQL Azure

Consulta Performance Insight fornece análise seleção inteligente de consultas para bases de dados individuais e reunidas. Ajuda a identificar as principais consultas de consumo de recursos e de longa duração na sua carga de trabalho. Isto ajuda-o a encontrar as consultas para otimizar para melhorar o desempenho global da carga de trabalho e utilizar eficientemente o recurso que está a pagar. Consulta Performance Insight ajuda-o a gastar menos tempo a resolver o desempenho da base de dados, fornecendo:

* Informação mais profunda sobre o consumo de recursos (DTU) das suas bases de dados
* Detalhes sobre as consultas de base de dados de topo por CPU, duração e contagem de execução (potenciais candidatos a afinação para melhorias de desempenho)
* A capacidade de aprofundar detalhes de uma consulta, para ver o texto de consulta e a história da utilização de recursos
* Anotações que mostram recomendações de desempenho de [consultores](sql-database-advisor.md) de base de dados

![Query Performance Insight](./media/sql-database-query-performance/opening-title.png)

## <a name="prerequisites"></a>Pré-requisitos

Consulta Performance Insight requer que a [Consulta Store](https://msdn.microsoft.com/library/dn817826.aspx) esteja ativa na sua base de dados. É ativado automaticamente para todas as bases de dados Azure SQL por padrão. Se a Loja de Consultas não estiver a funcionar, o portal Azure irá pedir-lhe para o ativar.

> [!NOTE]
> Se a mensagem "Consulta Store não estiver corretamente configurada nesta base de dados" aparece no portal, consulte [otimizar a configuração da Loja de Consultas](#optimize-the-query-store-configuration).

## <a name="permissions"></a>Permissões

Precisa das seguintes permissões [de controlo de acesso baseadas em funções](../role-based-access-control/overview.md) para utilizar a Query Performance Insight:

* **O leitor**, **proprietário,** **colaborador,** **Contribuinte SQL DB,** ou permissões **do Contribuinte do Servidor SQL** são necessárias para visualizar as principais consultas e gráficos que consomem recursos.
* **Proprietário**, **Contribuinte,** **Contribuinte Db SQL**ou Permissões Do Colaborador do **Servidor SQL** são necessárias para ver texto de consulta.

## <a name="use-query-performance-insight"></a>Utilizar o Query Performance Insight

Consulta Performance Insight é fácil de usar:

1. Abra o [portal Azure](https://portal.azure.com/) e encontre uma base de dados que pretende examinar.
2. A partir do menu do lado esquerdo, abra a**Visão**de **Desempenho Inteligente** > de Performance de Desempenho .
  
   ![Consulta Performance Insight no menu](./media/sql-database-query-performance/tile.png)

3. No primeiro separador, reveja a lista das principais consultas que consomem recursos.
4. Selecione uma consulta individual para ver os seus detalhes.
5. Abra**recomendações** **de desempenho** > inteligente e verifique se existem recomendações de desempenho disponíveis. Para obter mais informações sobre recomendações de desempenho incorporadas, consulte O Consultor de Base de [Dados SQL](sql-database-advisor.md).
6. Utilize sliders ou ícones de zoom para alterar o intervalo observado.

   ![Painel de instrumentos de desempenho](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Para que a Base de Dados SQL forneça a informação no Query Performance Insight, a Consulta Store precisa de capturar algumas horas de dados. Se a base de dados não tiver atividade ou se a Consulta Store não estiver ativa durante um determinado período, os gráficos ficarão vazios quando a Consulta Performance Insight apresentar essa gama de tempo. Pode ativar a Consulta Store a qualquer momento se não estiver a funcionar. Para mais informações, consulte [as melhores práticas com a Consulta Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store).
>

Para recomendações de desempenho na base de dados, selecione [Recomendações](sql-database-advisor.md) na lâmina de navegação De query Performance Insight.

![O separador recomendações](./media/sql-database-query-performance/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>Rever as principais consultas que consomem CPU

Por padrão, a Query Performance Insight mostra as cinco principais consultas que consomem CPU quando a abrepela primeira vez.

1. Selecione ou limpe consultas individuais para incluí-las ou excluí-las do gráfico utilizando caixas de verificação.

   A linha de cima mostra a percentagem global de DTU para a base de dados. As barras mostram a percentagem de CPU que as consultas selecionadas consumidas durante o intervalo selecionado. Por exemplo, se **a semana passada** for selecionada, cada bar representa um único dia.

   ![Principais consultas](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > A linha DTU mostrada é agregada a um valor máximo de consumo em períodos de uma hora. Destina-se a uma comparação de alto nível apenas com estatísticas de execução de consultas. Em alguns casos, a utilização do DTU pode parecer demasiado elevada em comparação com consultas executadas, mas este pode não ser o caso.
   >
   > Por exemplo, se uma consulta fornou o DTU a 100% apenas por alguns minutos, a linha DTU em Consulta Performance Insight mostrará toda a hora de consumo como 100% (a consequência do valor agregado máximo).
   >
   > Para uma comparação mais fina (até um minuto), considere criar um gráfico de utilização dTU personalizado:
   >
   > 1. No portal Azure, selecione > **Monitorização**de Base de **Dados Azure SQL**.
   > 2. Selecione **Métricas**.
   > 3. Selecione **+Adicionar gráfico**.
   > 4. Selecione a percentagem de DTU na tabela.
   > 5. Além disso, selecione **Last 24 horas** no menu superior esquerdo e mude-o para um minuto.
   >
   > Utilize o gráfico dTU personalizado com um nível de detalhes mais fino para comparar com o gráfico de execução de consulta.

   A grelha inferior mostra informações agregadas para as consultas visíveis:

   * Consulta ID, que é um identificador único para a consulta na base de dados.
   * CPU por consulta durante um intervalo observável, que depende da função de agregação.
   * Duração por consulta, que também depende da função de agregação.
   * Número total de execuções para uma consulta específica.

2. Se os seus dados ficarem velhos, selecione o botão **Refresh.**

3. Utilize sliders e botões de zoom para alterar o intervalo de observação e investigar picos de consumo:

   ![Deslizadores e botões de zoom para alterar o intervalo](./media/sql-database-query-performance/zoom.png)

4. Opcionalmente, pode selecionar o separador **Custom** para personalizar a vista para:

   * Métrica (CPU, duração, contagem de execução).
   * Intervalo de tempo (últimas 24 horas, semana passada, ou mês passado).
   * Número de consultas.
   * Função de agregação.
  
   ![Separador personalizado](./media/sql-database-query-performance/custom-tab.png)
  
5. Selecione o botão **Go >** para ver a vista personalizada.

   > [!IMPORTANT]
   > A Consulta Performance Insight limita-se a apresentar as consultas de 5 a 20 melhores, dependendo da sua seleção. A sua base de dados pode executar muitas mais consultas para além das principais mostradas, e estas consultas não serão incluídas na tabela.
   >
   > Pode existir um tipo de carga de trabalho de base de dados em que muitas consultas menores, para além das mais notadas, funcionam frequentemente e utilizam a maioria do DTU. Estas consultas não aparecem na tabela de desempenho.
   >
   > Por exemplo, uma consulta poderia ter consumido uma quantidade substancial de DTU durante algum tempo, embora o seu consumo total no período observado seja inferior às outras consultas de maior consumo. Neste caso, a utilização de recursos desta consulta não constaria da tabela.
   >
   > Se precisar de compreender as execuções de topo para além das limitações da Query Performance Insight, considere utilizar o [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) para monitorização avançada de desempenho na base de dados e resolução de problemas.
   >

## <a name="view-individual-query-details"></a>Ver detalhes de consulta individual

Para ver detalhes da consulta:

1. Selecione qualquer consulta na lista de consultas de topo.

    ![Lista de consultas de topo](./media/sql-database-query-performance/details.png)

   Abre-se uma vista detalhada. Mostra o consumo, duração e contagem de execução da CPU ao longo do tempo.

2. Selecione as funcionalidades do gráfico para mais detalhes.

   * O gráfico superior mostra uma linha com a percentagem de DTU da base de dados global. As barras são a percentagem de CPU que a consulta selecionada consumiu.
   * O segundo gráfico mostra a duração total da consulta selecionada.
   * O gráfico inferior mostra o número total de execuções pela consulta selecionada.

   ![Detalhes de consulta](./media/sql-database-query-performance/query-details.png)

3. Opcionalmente, use sliders, use botões de zoom ou selecione **Definições** para personalizar a forma como os dados de consulta são apresentados, ou para escolher um intervalo de tempo diferente.

   > [!IMPORTANT]
   > Consulta Performance Insight não captura quaisquer consultas DDL. Em alguns casos, pode não capturar todas as consultas ad hoc.
   >

## <a name="review-top-queries-per-duration"></a>Rever as principais consultas por duração

Duas métricas em Query Performance Insight podem ajudá-lo a encontrar potenciais estrangulamentos: duração e contagem de execução.

As consultas de longa duração têm o maior potencial para bloquear recursos por mais tempo, bloqueando outros utilizadores e limitando a escalabilidade. São também os melhores candidatos à otimização.

Para identificar consultas de longa duração:

1. Abra o separador **Custom** no Query Performance Insight para a base de dados selecionada.
2. Altere as métricas para a **duração**.
3. Selecione o número de consultas e o intervalo de observação.
4. Selecione a função de agregação:

   * **Soma** adiciona todo o tempo de execução de consulta para todo o intervalo de observação.
   * **Max** encontra consultas em que o tempo de execução foi máximo para todo o intervalo de observação.
   * **Avg** encontra o tempo médio de execução de todas as execuções de consulta e mostra-lhe os melhores para estas médias.

   ![Duração da consulta](./media/sql-database-query-performance/top-duration.png)

5. Selecione o botão **Go >** para ver a vista personalizada.

   > [!IMPORTANT]
   > Ajustar a vista de consulta não atualiza a linha DTU. A linha DTU mostra sempre o valor máximo de consumo para o intervalo.
   >
   > Para compreender o consumo de DTU da base de dados com mais detalhes (até um minuto), considere criar um gráfico personalizado no portal Azure:
   >
   > 1. Selecione > **Monitorização**da Base de **Dados Azure SQL**.
   > 2. Selecione **Métricas**.
   > 3. Selecione **+Adicionar gráfico**.
   > 4. Selecione a percentagem de DTU na tabela.
   > 5. Além disso, selecione **Last 24 horas** no menu superior esquerdo e mude-o para um minuto.
   >
   > Recomendamos que utilize o gráfico dTU personalizado para comparar com o gráfico de desempenho da consulta.
   >

## <a name="review-top-queries-per-execution-count"></a>Rever as principais consultas por contagem de execução

Uma aplicação do utilizador que utiliza a base de dados pode ficar lenta, mesmo que um elevado número de execuções possa não estar a afetar a própria base de dados e o uso de recursos seja baixo.

Em alguns casos, uma alta contagem de execuções pode levar a mais viagens de ida e volta em rede. As viagens de ida e volta afetam o desempenho. Estão sujeitos a latência de rede e à latência do servidor a jusante.

Por exemplo, muitos sites baseados em dados acedem fortemente à base de dados para cada pedido do utilizador. Embora o agrupamento de ligações ajude, o aumento do tráfego de rede e a carga de processamento no servidor de base de dados podem abrandar o desempenho. Em geral, mantenha as viagens de ida e volta ao mínimo.

Identificar consultas frequentemente executadas ("chatty") ):

1. Abra o separador **Custom** no Query Performance Insight para a base de dados selecionada.
2. Mude as métricas para a **contagem de execução.**
3. Selecione o número de consultas e o intervalo de observação.
4. Selecione o botão **Go >** para ver a vista personalizada.

   ![Contagem de execução de consulta](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Compreender anotações de anotação de afinação de desempenho

Ao explorar a sua carga de trabalho no Query Performance Insight, poderá notar ícones com uma linha vertical em cima da tabela.

Estes ícones são anotações. Mostram recomendações de desempenho do [SQL Database Advisor](sql-database-advisor.md). Ao pairar sobre uma anotação, você pode obter informações resumidas sobre recomendações de desempenho.

   ![Anotação de consulta](./media/sql-database-query-performance/annotation.png)

Se quiser compreender mais ou aplicar a recomendação do consultor, selecione o ícone para abrir detalhes da ação recomendada. Se esta for uma recomendação ativa, pode aplicá-la imediatamente ao portal.

   ![Detalhes de anotação de consulta](./media/sql-database-query-performance/annotation-details.png)

Em alguns casos, devido ao nível de zoom, é possível que anotações próximas umas das outras sejam colapsadas numa única anotação. Consulta Performance Insight representa isto como um ícone de anotação em grupo. A seleção do ícone de anotação em grupo abre uma nova lâmina que lista as anotações.

Relacionar consultas e ações de afinação de desempenho pode ajudá-lo a entender melhor a sua carga de trabalho.

## <a name="optimize-the-query-store-configuration"></a>Otimizar a configuração da Loja de Consultas

Ao utilizar o Query Performance Insight, poderá ver as seguintes mensagens de erro da Consulta Store:

* "A Consulta Store não está devidamente configurada nesta base de dados. Clique aqui para saber mais."
* "A Consulta Store não está devidamente configurada nesta base de dados. Clique aqui para alterar definições."

Estas mensagens geralmente aparecem quando a Consulta Store não consegue recolher novos dados.

O primeiro caso acontece quando a Consulta Store está no estado de leitura e os parâmetros são definidos da melhor forma. Pode corrigi-lo aumentando o tamanho da loja de dados, ou limpando a Query Store. (Se limpar a Consulta Store, perder-se-á toda a telemetria previamente recolhida.)

   ![Detalhes da Loja de Consultas](./media/sql-database-query-performance/qds-off.png)

O segundo caso acontece quando a Consulta Store não está ativada, ou os parâmetros não estão definidos da melhor forma. Pode alterar a política de retenção e captura, e também ativar a Query Store, executando os seguintes comandos fornecidos pelo [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou pelo portal Azure.

### <a name="recommended-retention-and-capture-policy"></a>Política de retenção e captura recomendada

Existem dois tipos de políticas de retenção:

* **Baseado no tamanho**: Se esta política for definida para **AUTO,** limpará automaticamente os dados quando for atingido o tamanho máximo.
* **Baseado**no tempo : Por defeito, esta política é definida para 30 dias. Se a Loja de Consultas ficar sem espaço, eliminará informações de consulta com mais de 30 dias.

Pode definir a política de captura para:

* **Tudo:** A Consulta Store capta todas as consultas.
* **Auto**: A Consulta Store ignora consultas e consultas pouco frequentes com duração de compilação e execução insignificantes. Os limiares para a contagem de execução, a duração da compilação e a duração do tempo de funcionação são determinados internamente. Esta é a opção padrão.
* **Nenhuma**: A Consulta Store deixa de capturar novas consultas, mas as estatísticas de tempo de execução para consultas já capturadas ainda são recolhidas.

Recomendamos definir todas as políticas para **auto** e a política de limpeza para 30 dias executando os seguintes comandos da [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou do portal Azure. (Substitua `YourDB` pelo nome da base de dados.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Aumente o tamanho da Consulta Store ligando-se a uma base de dados através de [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou do portal Azure e executando a seguinte consulta. (Substitua `YourDB` pelo nome da base de dados.)

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Aplicar estas definições irá eventualmente fazer com que a Query Store recolha telemetria para novas consultas. Se necessitar de Query Store para estar operacional imediatamente, pode optar opcionalmente por limpar a Consulta Store executando a seguinte consulta através do SSMS ou do portal Azure. (Substitua `YourDB` pelo nome da base de dados.)

> [!NOTE]
> A execução da seguinte consulta eliminará todas as telemetrias monitorizadas previamente recolhidas na Query Store.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>Passos seguintes

Considere utilizar o [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) para monitorização avançada do desempenho de uma grande frota de bases de dados individuais e agrofadas, piscinas elásticas, instâncias geridas e bases de dados de instâncias.
