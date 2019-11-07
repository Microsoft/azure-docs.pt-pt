---
title: Análise de Desempenho de Consultas para o banco de dados SQL do Azure
description: O monitoramento de desempenho de consulta identifica a maioria das consultas de consumo de CPU para um banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 01/03/2019
ms.openlocfilehash: eb6827669829dc7aeeb6758a07218a29955b3682
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687589"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Análise de Desempenho de Consultas para o banco de dados SQL do Azure

O gerenciamento e o ajuste do desempenho de bancos de dados relacionais leva conhecimentos e tempo. Análise de Desempenho de Consultas faz parte da linha de produtos de desempenho inteligente do banco de dados SQL do Azure. Ele ajuda você a gastar menos tempo Solucionando problemas de desempenho do banco de dados fornecendo:

* Informações mais aprofundadas sobre o consumo de seu recurso de bancos de dados (DTU).
* Detalhes sobre as principais consultas de banco de dados por CPU, duração e contagem de execução (possíveis candidatos de ajuste para melhorias de desempenho).
* A capacidade de fazer uma busca detalhada em detalhes de uma consulta, para exibir o texto da consulta e o histórico da utilização de recursos.
* Anotações que mostram recomendações de desempenho de [Assistente do banco de dados SQL](sql-database-advisor.md).

![Query Performance Insight](./media/sql-database-query-performance/opening-title.png)

> [!TIP]
> Para o monitoramento de desempenho básico com o banco de dados SQL do Azure, recomendamos Análise de Desempenho de Consultas. Observe as limitações de produto publicadas neste artigo. Para o monitoramento avançado do desempenho do banco de dados em escala, recomendamos [análise de SQL do Azure](../azure-monitor/insights/azure-sql.md). Ele tem inteligência interna para solução de problemas de desempenho automatizado. Para ajustar automaticamente alguns dos problemas de desempenho de banco de dados mais comuns, recomendamos o [ajuste automático](sql-database-automatic-tuning.md).

## <a name="prerequisites"></a>Pré-requisitos

Análise de Desempenho de Consultas requer que [repositório de consultas](https://msdn.microsoft.com/library/dn817826.aspx) esteja ativa no seu banco de dados. Ele é habilitado automaticamente para todos os bancos de dados SQL do Azure por padrão. Se Repositório de Consultas não estiver em execução, o portal do Azure solicitará que você o habilite.

> [!NOTE]
> Se a mensagem "Repositório de Consultas não estiver configurada corretamente neste banco de dados" for exibida no portal, consulte [otimizando a configuração de repositório de consultas](#optimize-the-query-store-configuration-for-query-performance-insight).
>

## <a name="permissions"></a>Permissões

Você precisa das seguintes permissões de [controle de acesso baseado em função](../role-based-access-control/overview.md) para usar análise de desempenho de consultas:

* As permissões **leitor**, **proprietário**, **colaborador**, **colaborador de BD SQL**ou **SQL Server colaborador** são necessárias para exibir as principais consultas e os gráficos de consumo de recursos.
* As permissões de colaborador **proprietário**, **colaborador**, colaborador do **banco de BD SQL**ou **SQL Server** são necessárias para exibir o texto da consulta.

## <a name="use-query-performance-insight"></a>Utilizar o Query Performance Insight

Análise de Desempenho de Consultas é fácil de usar:

1. Abra o [portal do Azure](https://portal.azure.com/) e localize um banco de dados que você deseja examinar.
2. No menu do lado esquerdo, abra o > de **desempenho inteligente** **análise de desempenho de consultas**.
  
   ![Análise de Desempenho de Consultas no menu](./media/sql-database-query-performance/tile.png)

3. Na primeira guia, examine a lista de principais consultas que consomem recursos.
4. Selecione uma consulta individual para exibir seus detalhes.
5. Abra o **desempenho inteligente** > **recomendações de desempenho** e verifique se há recomendações de desempenho disponíveis. Para obter mais informações sobre recomendações de desempenho internas, consulte [Assistente do banco de dados SQL](sql-database-advisor.md).
6. Use controles deslizantes ou ícones de zoom para alterar o intervalo observado.

   ![Painel de desempenho](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Para que o banco de dados SQL processe as informações em Análise de Desempenho de Consultas, Repositório de Consultas precisa capturar algumas horas. Se o banco de dados não tiver nenhuma atividade ou se Repositório de Consultas não estava ativo durante um determinado período, os gráficos estarão vazios quando Análise de Desempenho de Consultas exibir esse intervalo de tempo. Você pode habilitar Repositório de Consultas a qualquer momento se ela não estiver em execução. Para obter mais informações, consulte [práticas recomendadas com repositório de consultas](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store).

## <a name="review-top-cpu-consuming-queries"></a>Examinar as principais consultas que consomem a CPU

Por padrão, Análise de Desempenho de Consultas mostra as cinco principais consultas que consomem a CPU quando você a abre pela primeira vez.

1. Marque ou desmarque as consultas individuais para incluí-las ou excluí-las do gráfico usando as caixas de seleção.

    A linha superior mostra a porcentagem de DTU geral para o banco de dados. As barras mostram a porcentagem de CPU que as consultas selecionadas consumiram durante o intervalo selecionado. Por exemplo, se a **semana passada** for selecionada, cada barra representará um único dia.

    ![Principais consultas](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > A linha de DTU mostrada é agregada a um valor de consumo máximo em períodos de uma hora. Ele é destinado a uma comparação de alto nível somente com estatísticas de execução de consulta. Em alguns casos, a utilização de DTU pode parecer muito alta em comparação com as consultas executadas, mas esse pode não ser o caso.
   >
   > Por exemplo, se uma consulta esgotou DTU para 100% por apenas alguns minutos, a linha de DTU em Análise de Desempenho de Consultas mostrará a hora de consumo inteira como 100% (a consequência do valor máximo agregado).
   >
   > Para uma comparação mais refinada (até um minuto), considere criar um gráfico de utilização de DTU personalizado:
   >
   > 1. No portal do Azure, selecione **banco de dados SQL do Azure** > **monitoramento**.
   > 2. Selecione **Métricas**.
   > 3. Selecione **+ Adicionar gráfico**.
   > 4. Selecione a porcentagem de DTU no gráfico.
   > 5. Além disso, selecione **últimas 24 horas** no menu superior esquerdo e altere-o para um minuto.
   >
   > Use o gráfico de DTU personalizado com um nível mais preciso de detalhes para comparar com o gráfico de execução de consulta.

   A grade inferior mostra informações agregadas para as consultas visíveis:

   * ID da consulta, que é um identificador exclusivo para a consulta no banco de dados.
   * CPU por consulta durante um intervalo observável, que depende da função de agregação.
   * Duração por consulta, que também depende da função de agregação.
   * Número total de execuções para uma consulta específica.

2. Se os dados ficarem obsoletos, selecione o botão **Atualizar** .

3. Use os controles deslizantes e os botões de zoom para alterar o intervalo de observação e investigar os picos de consumo:

   ![Controles deslizantes e botões de zoom para alterar o intervalo](./media/sql-database-query-performance/zoom.png)

4. Opcionalmente, você pode selecionar a guia **personalizada** para personalizar a exibição para:

   * Métrica (CPU, duração, contagem de execução).
   * Intervalo de tempo (últimas 24 horas, semana passada ou mês passado).
   * Número de consultas.
   * Função de agregação.
  
   ![Guia personalizada](./media/sql-database-query-performance/custom-tab.png)
  
5. Selecione o botão **ir >** para ver a exibição personalizada.

   > [!IMPORTANT]
   > Análise de Desempenho de Consultas é limitado a exibir as principais consultas de consumo de 5-20, dependendo da sua seleção. Seu banco de dados pode executar muitas outras consultas além das principais mostradas, e essas consultas não serão incluídas no gráfico.
   >
   > Pode existir um tipo de carga de trabalho de banco de dados no qual muitas consultas menores, além das principais mostradas, são executadas com frequência e usam a maioria de DTU. Essas consultas não aparecem no gráfico de desempenho.
   >
   > Por exemplo, uma consulta pode ter consumido uma quantidade substancial de DTU por um tempo, embora seu consumo total no período observado seja menor do que as outras consultas de alto consumo. Nesse caso, a utilização de recursos dessa consulta não apareceria no gráfico.
   >
   > Se você precisar entender as principais execuções de consulta além das limitações de Análise de Desempenho de Consultas, considere o uso de [análise de SQL do Azure](../azure-monitor/insights/azure-sql.md) para monitoramento e solução de problemas avançados de desempenho de banco de dados.
   >

## <a name="view-individual-query-details"></a>Exibir detalhes da consulta individual

Para exibir detalhes da consulta:

1. Selecione qualquer consulta na lista de principais consultas.

    ![Lista de principais consultas](./media/sql-database-query-performance/details.png)

   Uma exibição detalhada é aberta. Ele mostra o consumo de CPU, a duração e a contagem de execução ao longo do tempo.

2. Selecione os recursos do gráfico para obter detalhes.

   * O gráfico superior mostra uma linha com a porcentagem de DTU geral do banco de dados. As barras são a porcentagem de CPU consumida pela consulta selecionada.
   * O segundo gráfico mostra a duração total da consulta selecionada.
   * O gráfico inferior mostra o número total de execuções pela consulta selecionada.

   ![Detalhes da consulta](./media/sql-database-query-performance/query-details.png)

3. Opcionalmente, use controles deslizantes, use botões de zoom ou selecione **configurações** para personalizar como os dados de consulta são exibidos ou para escolher um intervalo de tempo diferente.

   > [!IMPORTANT]
   > Análise de Desempenho de Consultas não captura nenhuma consulta DDL. Em alguns casos, ele pode não capturar todas as consultas ad hoc.
   >

## <a name="review-top-queries-per-duration"></a>Examinar as principais consultas por duração

Duas métricas no Análise de Desempenho de Consultas podem ajudá-lo a encontrar gargalos potenciais: duração e contagem de execução.

Consultas de execução longa têm o maior potencial para bloquear recursos por mais tempo, bloqueando outros usuários e limitando a escalabilidade. Eles também são os melhores candidatos para otimização.

Para identificar consultas de execução longa:

1. Abra a guia **personalizado** em análise de desempenho de consultas para o banco de dados selecionado.
2. Altere as métricas para **duração**.
3. Selecione o número de consultas e o intervalo de observação.
4. Selecione a função de agregação:

   * **Sum** adiciona todo o tempo de execução de consulta para todo o intervalo de observação.
   * **Max** localiza consultas nas quais o tempo de execução era máximo para todo o intervalo de observação.
   * **AVG** localiza o tempo médio de execução de todas as execuções de consulta e mostra as principais para essas médias.

   ![Duração da consulta](./media/sql-database-query-performance/top-duration.png)

5. Selecione o botão **ir >** para ver a exibição personalizada.

   > [!IMPORTANT]
   > O ajuste da exibição de consulta não atualiza a linha de DTU. A linha DTU sempre mostra o valor de consumo máximo para o intervalo.
   >
   > Para entender o consumo de DTU do banco de dados com mais detalhes (até um minuto), considere criar um gráfico personalizado na portal do Azure:
   >
   > 1. Selecione o **monitoramento**de > **do banco de dados SQL do Azure** .
   > 2. Selecione **Métricas**.
   > 3. Selecione **+ Adicionar gráfico**.
   > 4. Selecione a porcentagem de DTU no gráfico.
   > 5. Além disso, selecione **últimas 24 horas** no menu superior esquerdo e altere-o para um minuto.
   >
   > Recomendamos que você use o gráfico de DTU personalizado para comparar com o gráfico de desempenho de consulta.
   >

## <a name="review-top-queries-per-execution-count"></a>Examinar as principais consultas por contagem de execução

Um aplicativo de usuário que usa o banco de dados pode ficar lento, mesmo que um grande número de execuções possa não estar afetando o próprio banco de dados e o uso de recursos seja baixo.

Em alguns casos, uma contagem de execução alta pode levar a mais viagens de ida e volta da rede. Viagens de ida e volta afetam o desempenho. Eles estão sujeitos à latência de rede e à latência de servidor downstream.

Por exemplo, muitos sites controlados por dados acessam muito o banco de dado para cada solicitação de usuário. Embora o pool de conexões ajude, o aumento do tráfego de rede e da carga de processamento no servidor de banco de dados pode reduzir o desempenho. Em geral, mantenha as viagens de ida e volta no mínimo.

Para identificar consultas executadas com frequência ("informativas"):

1. Abra a guia **personalizado** em análise de desempenho de consultas para o banco de dados selecionado.
2. Altere as métricas para **contagem de execução**.
3. Selecione o número de consultas e o intervalo de observação.
4. Selecione o botão **ir >** para ver a exibição personalizada.

   ![Contagem de execução de consulta](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Entender as anotações de ajuste de desempenho

Ao explorar sua carga de trabalho em Análise de Desempenho de Consultas, você pode observar ícones com uma linha vertical sobre o gráfico.

Esses ícones são anotações. Eles mostram recomendações de desempenho de [Assistente do banco de dados SQL](sql-database-advisor.md). Ao passar o mouse sobre uma anotação, você pode obter informações resumidas sobre recomendações de desempenho.

   ![Anotação de consulta](./media/sql-database-query-performance/annotation.png)

Se você quiser entender mais ou aplicar a recomendação do Advisor, selecione o ícone para abrir detalhes da ação recomendada. Se essa for uma recomendação ativa, você poderá aplicá-la imediatamente no Portal.

   ![Detalhes da anotação da consulta](./media/sql-database-query-performance/annotation-details.png)

Em alguns casos, devido ao nível de zoom, é possível que as anotações próximas umas às outras sejam recolhidas em uma única anotação. Análise de Desempenho de Consultas representa isso como um ícone de anotação de grupo. A seleção do ícone de anotação de grupo abre uma nova folha que lista as anotações.

Correlacionar consultas e ações de ajuste de desempenho pode ajudá-lo a entender melhor sua carga de trabalho.

## <a name="optimize-the-query-store-configuration-for-query-performance-insight"></a>Otimizar a configuração de Repositório de Consultas para Análise de Desempenho de Consultas

Ao usar Análise de Desempenho de Consultas, você poderá ver as seguintes Repositório de Consultas mensagens de erro:

* "Repositório de Consultas não está configurado corretamente neste banco de dados. Clique aqui para saber mais. "
* "Repositório de Consultas não está configurado corretamente neste banco de dados. Clique aqui para alterar as configurações. "

Essas mensagens geralmente aparecem quando Repositório de Consultas não pode coletar novos dados.

O primeiro caso ocorre quando Repositório de Consultas está no estado somente leitura e os parâmetros são definidos de forma ideal. Você pode corrigir isso aumentando o tamanho do armazenamento de dados ou desmarcando Repositório de Consultas. (Se você limpar Repositório de Consultas, todas as telemetrias coletadas anteriormente serão perdidas.)

   ![Detalhes do Repositório de Consultas](./media/sql-database-query-performance/qds-off.png)

O segundo caso ocorre quando Repositório de Consultas não está habilitado ou os parâmetros não são definidos de forma ideal. Você pode alterar a política de retenção e captura e também habilitar Repositório de Consultas, executando os comandos a seguir fornecidos no [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou no portal do Azure.

### <a name="recommended-retention-and-capture-policy"></a>Política de retenção e captura recomendada

Há dois tipos de políticas de retenção:

* **Baseado em tamanho**: se essa política for definida como **automática**, ela limpará os dados automaticamente quando o tamanho máximo for atingido.
* **Baseado em tempo**: por padrão, essa política é definida como 30 dias. Se Repositório de Consultas ficar sem espaço, ele excluirá informações de consulta com mais de 30 dias.

Você pode definir a política de captura para:

* **Todos**: repositório de consultas captura todas as consultas.
* **Automático**: repositório de consultas ignora consultas e consultas incomuns com duração de compilação e execução insignificante. Os limites para contagem de execução, duração da compilação e duração do tempo de execução são determinados internamente. Essa é a opção padrão.
* **Nenhum**: repositório de consultas interrompe a captura de novas consultas, mas as estatísticas de tempo de execução para consultas já capturadas ainda são coletadas.

É recomendável definir todas as políticas para **automático** e a política de limpeza para 30 dias executando os comandos a seguir do [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou do portal do Azure. (Substitua `YourDB` pelo nome do banco de dados.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Aumente o tamanho de Repositório de Consultas conectando-se a um banco de dados por meio do [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou do portal do Azure e executando a consulta a seguir. (Substitua `YourDB` pelo nome do banco de dados.)

```T-SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

A aplicação dessas configurações eventualmente fará Repositório de Consultas coletar telemetria para novas consultas. Se precisar de Repositório de Consultas estar funcionando imediatamente, você pode optar por limpar Repositório de Consultas executando a consulta a seguir por meio do SSMS ou do portal do Azure. (Substitua `YourDB` pelo nome do banco de dados.)

> [!NOTE]
> A execução da consulta a seguir excluirá toda a telemetria monitorada anteriormente coletada em Repositório de Consultas.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="summary"></a>Resumo

Análise de Desempenho de Consultas ajuda a entender o impacto da carga de trabalho de consulta e como ela se relaciona com o consumo de recursos do banco de dados. Com esse recurso, você aprenderá sobre as consultas que mais consomem em seu banco de dados e encontrará consultas a serem otimizadas antes de se tornarem um problema.

## <a name="next-steps"></a>Passos seguintes

* Para obter recomendações de desempenho de banco de dados, selecione [recomendações](sql-database-advisor.md) na folha de navegação análise de desempenho de consultas.

    ![A guia recomendações](./media/sql-database-query-performance/ia.png)

* Considere habilitar o [ajuste automático](sql-database-automatic-tuning.md) para problemas comuns de desempenho do banco de dados.
* Saiba como [Intelligent insights](sql-database-intelligent-insights.md) pode ajudar a solucionar problemas de desempenho de banco de dados automaticamente.
* Considere o uso de [análise de SQL do Azure]( ../azure-monitor/insights/azure-sql.md) para o monitoramento avançado de desempenho de uma grande frota de bancos de dados SQL, pools elásticos e instâncias gerenciadas com inteligência interna.
