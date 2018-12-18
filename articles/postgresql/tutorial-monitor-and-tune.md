---
title: Tutorial sobre Monitorização e Otimização na Base de Dados do Azure para PostgreSQL
description: Este tutorial explica a monitorização e a otimização na Base de Dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: f05e0eef7680b08ce116cc0243d944f6a1db597c
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536446"
---
# <a name="tutorial-monitor-and-tune-azure-database-for-postgresql"></a>Tutorial: Monitorizar e otimizar a base de dados do Azure para PostgreSQL

A Base de Dados do Azure para PostgreSQL possui recursos que o ajudam a compreender e a melhorar o desempenho do servidor. Neste tutorial, vai aprender a:
> [!div class="checklist"]
> * Ativar a recolha de consultas e de estatísticas de espera
> * Aceder e utilizar os dados recolhidos
> * Ver o desempenho das consultas e as estatísticas de espera ao longo do tempo
> * Analisar uma base de dados para obter recomendações de desempenho
> * Aplicar recomendações de desempenho

## <a name="before-you-begin"></a>Antes de começar
Precisa de um servidor da Base de Dados do Azure para PostgreSQL com a versão 9.6 ou 10 do PostgreSQL. Pode seguir os passos no [Tutorial de criação](tutorial-design-database-using-azure-portal.md) para criar um servidor.

> [!IMPORTANT]
> O **Arquivo de Consultas**, o **Query Performance Insight** e as **Recomendações de Desempenho** estão em Pré-visualização Pública.

## <a name="enabling-data-collection"></a>Ativar a recolha de dados
O [Arquivo de Consultas](concepts-query-store.md) captura um histórico das consultas e das estatísticas de espera no seu servidor e armazena-o na base de dados **azure_sys** do servidor. É uma funcionalidade opcional. Para a ativar:

1. Abra o portal do Azure.

2. Selecione o servidor da Base de Dados do Azure para PostgreSQL.

3. Selecione **Parâmetros do servidor**, na secção **Definições** do menu esquerdo.

4. Defina **pg_qs.query_capture_mode** como **PRINCIPAIS** para começar a recolher dados de desempenho da consulta. Defina **pgms_wait_sampling.query_capture_mode** como **TUDO** para começar a recolher de estatísticas de espera. Guarde.
   
   ![Parâmetros do servidor do Arquivo de Consultas](./media/tutorial-performance-intelligence/query-store-parameters.png)

5. Aguarde 20 minutos para o primeiro lote de dados se manter na base de dados **azure_sys**.


## <a name="performance-insights"></a>Informações de desempenho
A vista [Query Performance Insight](concepts-query-performance-insight.md) no portal do Azure vai apresentar visualizações sobre informações importantes do Arquivo de Consultas. 

1. Na página do portal da Base de Dados do Azure para PostgreSQL, selecione **Informações de desempenho de consultas** na secção **Suporte + resolução de problemas** do menu esquerdo.

2. O separador **Consultas de execução longa** mostra as cinco principais consultas por duração média para cada execução, agregadas em intervalos de 15 minutos. 
   
   ![Página de destino do Query Performance Insight](./media/tutorial-performance-intelligence/query-performance-insight-landing-page.png)

   Pode ver mais consultas ao selecionar no menu pendente **Número de Consultas**. Ao fazer isso, as cores do gráfico podem ser alteradas para um ID de Consulta específico.

3. Pode clicar e arrastar no gráfico para restringir a uma janela de tempo específica.

4. Utilize os ícones de ampliar e reduzir para ver um período de tempo menor ou maior, respetivamente.

5. Veja a tabela abaixo do gráfico para obter mais detalhes sobre as consultas de longa execução nessa janela de tempo.

6. Selecione o separador **Estatísticas de Espera** para ver as visualizações correspondentes sobre esperas no servidor.
   
   ![Estatísticas de espera do Query Performance Insight](./media/tutorial-performance-intelligence/query-performance-insight-wait-statistics.png)

### <a name="permissions"></a>Permissões
As permissões **Proprietário**ou **Contribuidor** necessárias para ver o texto das consultas no Query Performance Insight. O **Leitor** pode ver gráficos e tabelas, mas não o texto da consulta.


## <a name="performance-recommendations"></a>Recomendações de desempenho
A funcionalidade [Recomendações de Desempenho](concepts-performance-recommendations.md) analisa as cargas de trabalho no servidor para identificar índices com o potencial de melhorar o desempenho.

1. Abra as **Recomendações de Desempenho** a partir da secção **Suporte + resolução de problemas** da barra de menus da página do portal do Azure do servidor PostgreSQL.
   
   ![Página de destino das Recomendações de Desempenho](./media/tutorial-performance-intelligence/performance-recommendations-landing-page.png)

2. Selecione **Analisar** e escolha uma base de dados. Esta ação permitirá iniciar a análise.

3. Consoante a carga de trabalho, poderá demorar vários minutos a ser concluída. Quando a análise estiver concluída, será apresentada uma notificação no portal.

4. A janela **Recomendações de Desempenho** mostrará uma lista de recomendações se for detetada alguma. 

5. As recomendações mostrarão informações sobre a **Base de Dados**, a **Tabela**, a **Coluna** e o **Tamanho do Índice** relevantes.

   ![Resultado das Recomendações de Desempenho](./media/tutorial-performance-intelligence/performance-recommendations-result.png)

6. Para implementar uma recomendação, copie o texto da consulta e execute-o no cliente escolhido.

### <a name="permissions"></a>Permissões
As permissões **Proprietário** ou **Contribuidor** necessárias para executar a análise que utiliza a funcionalidade Recomendações de Desempenho.

## <a name="next-steps"></a>Passos Seguintes
- Saber mais sobre [monitorização e otimização](concepts-monitoring.md) na Base de Dados do Azure para PostgreSQL.