---
title: Consultar informações de desempenho da base de dados SQL do Azure | Documentos da Microsoft
description: Monitorização do desempenho de consulta identifica a maioria das consultas de consumo de CPU para uma base de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 5d892005881436dec89c0d0d010f7f02e7bdebf9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60585377"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>O Query Performance Insight para a base de dados SQL do Azure

Gerenciamento e ajuste do desempenho de bases de dados relacionais demora conhecimentos e a hora. O Query Performance Insight é uma parte da linha de produtos de desempenho inteligentes de base de dados do Azure SQL. Ajuda-o a despender menos tempo de resolução de problemas de desempenho de base de dados ao fornecer:

* Informações mais aprofundadas sobre o consumo de recursos (DTUS) de bases de dados.
* Detalhes sobre as consultas de base de dados superior por contagem de CPU, a duração e a execução (potencial ajuste candidatos para melhorar o desempenho).
* A capacidade de fazer busca detalhada nos detalhes de uma consulta, para ver o texto da consulta e o histórico de utilização de recursos.
* Anotações mostram recomendações de desempenho da [Assistente de base de dados SQL](sql-database-advisor.md).

![Query Performance Insight](./media/sql-database-query-performance/opening-title.png)

> [!TIP]
> Para monitorizar o desempenho básico com a base de dados do Azure SQL, recomendamos o Query Performance Insight. Tenha em atenção as limitações de produto publicadas neste artigo. Para avançadas de monitorização de desempenho de base de dados em escala, recomendamos [a análise de SQL do Azure](../azure-monitor/insights/azure-sql.md). Ele tem inteligência incorporada para resolução de problemas de desempenho automatizado. Ajustar automaticamente alguns dos problemas de desempenho de base de dados mais comuns, recomendamos [otimização automática](sql-database-automatic-tuning.md).

## <a name="prerequisites"></a>Pré-requisitos

Query Performance Insight requer que [Query Store](https://msdn.microsoft.com/library/dn817826.aspx) está ativo na base de dados. Este é ativado automaticamente para todas as bases de dados SQL do Azure por predefinição. Se não estiver a executar a consulta Store, o portal do Azure irá solicitar-lhe para ativá-la.

> [!NOTE]
> Se aparecer a mensagem "Store de consulta não está corretamente configurado nesta base de dados" no portal, veja [Otimização da configuração de consulta Store](#optimize-the-query-store-configuration-for-query-performance-insight).
>

## <a name="permissions"></a>Permissões

Precisa dos seguintes elementos [controlo de acesso baseado em funções](../role-based-access-control/overview.md) permissões para utilizar o Query Performance Insight:

* **Leitor**, **proprietário**, **contribuinte**, **contribuinte da BD SQL**, ou **contribuinte do SQL Server** são necessárias permissões Para ver as principais consultas de consumo de recursos e gráficos.
* **Proprietário**, **contribuinte**, **contribuinte da BD SQL**, ou **contribuinte do SQL Server** são necessárias permissões para ver o texto de consulta.

## <a name="use-query-performance-insight"></a>Utilizar o Query Performance Insight

O Query Performance Insight é fácil de usar:

1. Abra o [portal do Azure](https://portal.azure.com/) e encontrar uma base de dados que pretende examinar.
2. No menu do lado esquerdo, abra **desempenho inteligente** > **Query Performance Insight**.
  
   ![O Query Performance Insight no menu](./media/sql-database-query-performance/tile.png)

3. No primeiro separador, reveja a lista de consultas de consumo de recursos principais.
4. Selecione uma consulta individual para ver os detalhes.
5. Open **desempenho inteligente** > **recomendações de desempenho** e verifique se existem quaisquer recomendações de desempenho. Para obter mais informações sobre as recomendações de desempenho incorporada, consulte [Assistente de base de dados SQL](sql-database-advisor.md).
6. Utilizar controlos de deslize ou ícones para alterar o intervalo observado de zoom.

   ![dashboard de desempenho](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Para a base de dados SQL processar as informações no Query Performance Insight Store de consulta tem de capturar a duas horas de dados. Se a base de dados não tem nenhuma atividade ou se a consulta Store não esteve ativo durante um determinado período, os gráficos estará vazios quando o Query Performance Insight Exibe esse intervalo de tempo. Pode ativar Store de consulta em qualquer altura, se não está em execução. Para obter mais informações, consulte [melhores práticas com Query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store).

## <a name="review-top-cpu-consuming-queries"></a>Rever as consultas de consumo de CPU principais

Por predefinição, o Query Performance Insight mostra as principais consultas de consumo de CPU cinco quando abre pela primeira vez.

1. Marque ou desmarque as consultas individuais a incluir ou exclui-las no gráfico, utilizando as caixas de verificação.

    A linha superior mostra a percentagem de DTU geral para a base de dados. As barras mostram a percentagem de CPU que as consultas selecionadas consumidas durante o intervalo selecionado. Por exemplo, se **semana passada** é selecionado, cada barra representa um único dia.

    ![consultas principais](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > A linha DTU mostrada é agregada a um valor máximo de consumo em períodos de uma hora. Destina-se para obter uma comparação de alto nível apenas com as estatísticas de execução de consulta. Em alguns casos, a utilização de DTU pode parecer muito alta em comparação com a execução de consultas, mas este pode não ser o caso.
   >
   > Por exemplo, se uma consulta maximizado DTU para 100% para apenas alguns minutos, a linha DTU no Query Performance Insight irá mostrar toda a hora de consumo como 100% (a conseqüência do máximo valor agregado).
   >
   > Para obter uma comparação mais (até um minuto), considere a criação de um gráfico de utilização de DTU personalizado:
   >
   > 1. No portal do Azure, selecione **Azure SQL Database** > **monitorização**.
   > 2. Selecione **Métricas**.
   > 3. Selecione **+ adicionar gráfico**.
   > 4. Selecione a percentagem DTU no gráfico.
   > 5. Além disso, selecione **últimas 24 horas** no menu do canto superior esquerdo e alterá-lo para um minuto.
   >
   > Utilize o gráfico DTU personalizado com um nível mais de detalhes para comparar com o gráfico de execução da consulta.

   A grade inferior mostra informações agregadas para as consultas visível:

   * ID de consulta, que é um identificador exclusivo para a consulta na base de dados.
   * CPU por consulta durante um intervalo de observable, que depende da função de agregação.
   * Duração por consulta, que também depende da função de agregação.
   * Número total de execuções para uma consulta específica.

2. Se seus dados se torna obsoletos, selecione o **atualizar** botão.

3. Utilizar controlos de deslize e botões para alterar o intervalo de observação e investigar a picos de consumo de zoom:

   ![Controlos de deslize e botões de zoom para alterar o intervalo](./media/sql-database-query-performance/zoom.png)

4. Opcionalmente, pode selecionar o **personalizado** separador para personalizar a vista para:

   * Métrica (CPU, duração, a contagem de execução).
   * Intervalo de tempo (últimas 24 horas, semana passada, ou último mês).
   * Número de consultas.
   * Função de agregação.
  
   ![Separador personalizado](./media/sql-database-query-performance/custom-tab.png)
  
5. Selecione o **ir >** botão para ver a vista personalizada.

   > [!IMPORTANT]
   > O Query Performance Insight está limitado a apresentar as principais consultas de consumo de 5 a 20, dependendo da seleção. A base de dados pode executar várias consultas mais além dos mostrado principais e estas consultas não serão incluídas no gráfico.
   >
   > Pode existir-se um tipo de carga de trabalho de base de dados em que muitas consultas mais pequenas, além dos mostrado, principais são executados com frequência e utilizam a maioria de DTU. Estas consultas não são apresentados no gráfico de desempenho.
   >
   > Por exemplo, uma consulta pode tiver consumido uma quantidade substancial de DTU por algum tempo, embora seu consumo total no período de observado é menor do que as consultas estão consumindo a parte superior. Nesse caso, a utilização de recursos desta consulta não iria aparecer no gráfico.
   >
   > Se precisa entender as execuções de consulta superior além as limitações do Query Performance Insight, considere utilizar [a análise de SQL do Azure](../azure-monitor/insights/azure-sql.md) para desempenho da base de dados avançadas de monitorização e resolução de problemas.
   >

## <a name="view-individual-query-details"></a>Ver detalhes de consulta individual

Para ver os detalhes de consulta:

1. Selecione qualquer consulta na lista de consultas principais.

    ![Lista de consultas principais](./media/sql-database-query-performance/details.png)

   É aberta uma vista detalhada. Mostra o consumo da CPU, duração e contagem de execução ao longo do tempo.

2. Selecione as funcionalidades de gráfico para obter detalhes.

   * O gráfico superior mostra uma linha com a base de dados geral percentagem de DTU. As barras são a percentagem de CPU que a consulta selecionada consumida.
   * O segundo gráfico mostra a duração total da consulta selecionada.
   * O gráfico na parte inferior mostra o número total de execuções pela consulta selecionada.

   ![Detalhes da consulta](./media/sql-database-query-performance/query-details.png)

3. Opcionalmente, utilizar controlos de deslize, utilize os botões de zoom ou selecione **definições** para personalizar a forma como são apresentados os dados de consulta ou escolher um intervalo de tempo diferentes.

   > [!IMPORTANT]
   > O Query Performance Insight não captura quaisquer consultas DDL. Em alguns casos, este pode não recolher todas as consultas ad hoc.
   >

## <a name="review-top-queries-per-duration"></a>Rever as consultas principais por duração

Duas métricas no Query Performance Insight podem ajudar a encontrar possíveis afunilamentos: contagem de duração e a execução.

Consultas de execução longa tem o potencial de maior para bloquear os recursos mais tempo, bloqueando outros utilizadores além de limitar a escalabilidade. Elas também são os melhores candidatos para a otimização.

Para identificar as consultas de execução longa:

1. Abra o **personalizado** separador no Query Performance Insight para a base de dados selecionada.
2. Alterar as métricas para **duração**.
3. Selecione o número de consultas e o intervalo de observação.
4. Selecione a função de agregação:

   * **Soma** soma todos os tempos de execução da consulta durante o intervalo de observação inteira.
   * **Máx.** encontra consulta no qual o tempo de execução foi máximo durante o intervalo de observação inteira.
   * **Média** localiza o tempo médio de execução de todas as execuções de consulta e mostra-lhe dos principais para estes médias.

   ![Duração da consulta](./media/sql-database-query-performance/top-duration.png)

5. Selecione o **ir >** botão para ver a vista personalizada.

   > [!IMPORTANT]
   > Ajustar a vista de consulta não atualiza a linha DTU. A linha DTU mostra sempre o valor de consumo máximo durante o intervalo.
   >
   > Para compreender o consumo de DTU da base de dados com mais detalhes (até um minuto), considere criar um gráfico personalizado no portal do Azure:
   >
   > 1. Selecione **base de dados SQL do Azure** > **monitorização**.
   > 2. Selecione **Métricas**.
   > 3. Selecione **+ adicionar gráfico**.
   > 4. Selecione a percentagem DTU no gráfico.
   > 5. Além disso, selecione **últimas 24 horas** no menu do canto superior esquerdo e alterá-lo para um minuto.
   >
   > Recomendamos que utilize o gráfico personalizado de DTU para comparar com o gráfico de desempenho de consulta.
   >

## <a name="review-top-queries-per-execution-count"></a>Rever as consultas principais por contagem de execução

Um aplicativo de usuário que utiliza a base de dados poderá get lento, embora um elevado número de execuções não estar afetando o próprio banco de dados e a utilização de recursos é baixa.

Em alguns casos, uma contagem de execução elevada pode causar mais viagens de ida e volta de rede. Ida e volta afetar o desempenho. Eles estão sujeitos a latência de rede e a latência de servidor a jusante.

Por exemplo, muitos sites controlados por dados intensamente acessar o banco de dados para cada solicitação de usuário. Embora o agrupamento de ligações de ajuda, o tráfego de rede maior e a carga de processamento no servidor de base de dados podem abrandar o desempenho. Em geral, mantenha ida e volta ao mínimo possível.

Para identificar frequentemente executadas consultas ("conversadoras"):

1. Abra o **personalizado** separador no Query Performance Insight para a base de dados selecionada.
2. Alterar as métricas para **contagem de execução**.
3. Selecione o número de consultas e o intervalo de observação.
4. Selecione o **ir >** botão para ver a vista personalizada.

   ![Contagem de execução de consulta](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Compreender as anotações de ajuste de desempenho

Ao explorar a carga de trabalho no Query Performance Insight, pode observar ícones com uma linha vertical na parte superior do gráfico.

Estes ícones são anotações. Eles mostram recomendações de desempenho a partir [Assistente de base de dados SQL](sql-database-advisor.md). Ao pairar o rato sobre uma anotação, pode obter as informações resumidas em recomendações de desempenho.

   ![anotação de consulta](./media/sql-database-query-performance/annotation.png)

Se quiser saber mais ou aplicar a recomendação do assistente, selecione o ícone para abrir os detalhes da ação recomendada. Se se tratar de uma recomendação de Active Directory, pode aplicá-la imediatamente no portal.

   ![detalhes de anotação de consulta](./media/sql-database-query-performance/annotation-details.png)

Em alguns casos, devido ao nível de zoom, é possível que anotações perto uns dos outros são recolhidas numa única anotação. O Query Performance Insight representa isso como um ícone de anotação de grupo. Selecionar o ícone de anotação de grupo é aberto um novo painel que apresenta uma lista de anotações.

Consultas correlating e ações de otimização de desempenho podem ajudá-lo a compreender melhor a sua carga de trabalho.

## <a name="optimize-the-query-store-configuration-for-query-performance-insight"></a>Otimizar a configuração de consulta Store para o Query Performance Insight

Ao utilizar o Query Performance Insight, poderá ver as seguintes mensagens de erro de consulta Store:

* "Store de consulta não está corretamente configurado nesta base de dados. Clique aqui para saber mais."
* "Store de consulta não está corretamente configurado nesta base de dados. Clique aqui para alterar as definições."

Estas mensagens são apresentados normalmente quando a consulta Store não é possível recolher novos dados.

O primeiro caso acontece quando consulta Store está no estado só de leitura e os parâmetros estão definidos de forma otimizada. Pode corrigir este problema ao aumentar o tamanho do arquivo de dados ou desmarcando Store de consulta. (Se desmarcar Store de consulta, toda a telemetria recolhida anteriormente serão perdida.)

   ![Detalhes de consulta Store](./media/sql-database-query-performance/qds-off.png)

O segundo caso acontece quando Store de consulta não está ativado ou parâmetros não estão definidos de forma otimizada. Pode alterar a política de retenção e a captura e também permitir Store de consulta, ao executar os comandos seguintes fornecidos pela [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou o portal do Azure.

### <a name="recommended-retention-and-capture-policy"></a>Política de retenção e captura recomendada

Existem dois tipos de políticas de retenção:

* **Tamanho com base**: Se esta política estiver definida como **automática**, ele irá limpar dados automaticamente quando o próximo tamanho máximo é atingido.
* **Tempo com base**: Por predefinição, esta política estiver definida para 30 dias. Se a consulta Store ficar sem espaço, vai eliminar informações de consulta com mais de 30 dias.

Pode definir a política de captura:

* **Todos os**: Consulta Store captura todas as consultas.
* **Automática**: Consulta Store ignora consultas pouco frequentes e consultas com a duração insignificante de compilação e execução. Os limites de contagem de execução, compilar duração e a duração de tempo de execução são determinados internamente. Esta é a opção predefinida.
* **Nenhum**: Consulta Store deixa de novas consultas de capturar, mas as estatísticas de tempo de execução de consultas já capturadas são ainda recolhidas.

Recomendamos a definição de todas as políticas **automática** e a política de limpeza para 30 dias, executando os seguintes comandos a partir [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou o portal do Azure. (Substitua `YourDB` com o nome de base de dados.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Aumentar o tamanho de consulta Store ao ligar a uma base de dados por meio [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou o portal do Azure e executar a seguinte consulta. (Substitua `YourDB` com o nome de base de dados.)

```T-SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Aplicar estas definições, eventualmente, fará com que recolher telemetria para novas consultas de Store de consulta. Se precisar de consulta Store fique operacional imediatamente, opcionalmente, pode optar por limpar Query Store executando a seguinte consulta através do SSMS ou do portal do Azure. (Substitua `YourDB` com o nome de base de dados.)

> [!NOTE]
> Executar a seguinte consulta eliminará toda a telemetria monitorizada recolhida anteriormente no Query Store.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="summary"></a>Resumo

O Query Performance Insight ajuda-o a compreender o impacto da carga de trabalho de consulta e como se relaciona com o consumo de recursos de base de dados. Com esta funcionalidade, aprenderá sobre as consultas de consumo por parte superior na sua base de dados e encontrará consultas para otimizar antes que se tornem um problema.

## <a name="next-steps"></a>Passos Seguintes

* Para obter recomendações de desempenho da base de dados, selecione [recomendações](sql-database-advisor.md) no painel de navegação do Query Performance Insight.

    ![Separador recomendações](./media/sql-database-query-performance/ia.png)

* Considere ativar [otimização automática](sql-database-automatic-tuning.md) para problemas de desempenho de base de dados comuns.
* Saiba como [informações inteligentes](sql-database-intelligent-insights.md) pode ajudar a resolver automaticamente problemas de desempenho de base de dados.
* Considere a utilização [a análise de SQL do Azure]( ../azure-monitor/insights/azure-sql.md) para monitorização de desempenho avançado de uma frota de grandes dimensões de bases de dados SQL, os conjuntos elásticos e instâncias geridas com a inteligência incorporada.
