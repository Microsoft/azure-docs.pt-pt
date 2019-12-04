---
title: Recomendações de desempenho-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve o recurso de recomendação de desempenho no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768474"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Recomendações de desempenho no banco de dados do Azure para PostgreSQL-servidor único

**Aplica-se a:** Banco de dados do Azure para PostgreSQL-versões de servidor único 9,6, 10, 11

O recurso de recomendações de desempenho analisa seus bancos de dados para criar sugestões personalizadas para melhorar o desempenho. Para produzir as recomendações, a análise examina várias características do banco de dados, incluindo o esquema. Habilite [repositório de consultas](concepts-query-store.md) no seu servidor para utilizar totalmente o recurso de recomendações de desempenho. Depois de implementar qualquer recomendação de desempenho, você deve testar o desempenho para avaliar o impacto dessas alterações. 

## <a name="permissions"></a>Permissões
As permissões **Proprietário** ou **Contribuidor** necessárias para executar a análise que utiliza a funcionalidade Recomendações de Desempenho.

## <a name="performance-recommendations"></a>Recomendações de desempenho
A funcionalidade [Recomendações de Desempenho](concepts-performance-recommendations.md) analisa as cargas de trabalho no servidor para identificar índices com o potencial de melhorar o desempenho.

Abra **recomendações de desempenho** na seção de **desempenho inteligente** da barra de menus na página portal do Azure para o servidor PostgreSQL.

![Página de destino das Recomendações de Desempenho](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selecione **analisar** e escolha um banco de dados, que iniciará a análise. Dependendo de sua carga de trabalho, a análise de th pode levar vários minutos para ser concluída. Quando a análise estiver concluída, será apresentada uma notificação no portal. A análise executa um exame profundo do seu banco de dados. Recomendamos que você execute a análise fora dos períodos de pico. 

A janela **recomendações** mostrará uma lista de recomendações, se alguma for encontrada.

![Página de recomendações de desempenho nova](./media/concepts-performance-recommendations/performance-recommendations-result.png)

As recomendações não são aplicadas automaticamente. Para aplicar a recomendação, copie o texto da consulta e execute-o do seu cliente de sua escolha. Lembre-se de testar e monitorar para avaliar a recomendação. 

## <a name="recommendation-types"></a>Tipos de recomendação

Atualmente, há suporte para dois tipos de recomendações: *CREATE INDEX* e *drop index*.

### <a name="create-index-recommendations"></a>Criar recomendações de índice
*Criar* recomendações de índice sugerem novos índices para acelerar a execução com mais frequência ou as consultas demoradas na carga de trabalho. Este tipo de recomendação requer que [repositório de consultas](concepts-query-store.md) seja habilitado. Repositório de Consultas coleta informações de consulta e fornece as estatísticas detalhadas de tempo de execução de consulta e frequência que a análise usa para fazer a recomendação.

### <a name="drop-index-recommendations"></a>Recomendações de drop index
Além de detectar índices ausentes, o banco de dados do Azure para PostgreSQL analisa o desempenho dos índices existentes. Se um índice for raramente usado ou redundante, o analisador recomendará soltá-lo.

## <a name="considerations"></a>Considerações
* As recomendações de desempenho não estão disponíveis para [réplicas de leitura](concepts-read-replicas.md).
## <a name="next-steps"></a>Passos seguintes
- Saber mais sobre [monitorização e otimização](concepts-monitoring.md) na Base de Dados do Azure para PostgreSQL.

