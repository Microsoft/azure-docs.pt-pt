---
title: Recomendações de desempenho na base de dados do Azure para PostgreSQL
description: Este artigo descreve a funcionalidade de recomendação de desempenho na base de dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: d8edbc2847c06e95e658a1324f2e85f1758e60be
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487945"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Recomendações de desempenho na base de dados do Azure para PostgreSQL

**Aplica-se a:** Base de dados do Azure para PostgreSQL 9.6 e 10

A funcionalidade de recomendações de desempenho identifica os índices principais que podem ser criados na base de dados do Azure para o servidor PostgreSQL para melhorar o desempenho. Para produzir recomendações de índice, a funcionalidade leva em consideração várias características de base de dados, incluindo o respetivo esquema e a carga de trabalho, conforme comunicado pelo Store de consulta. Depois de implementar quaisquer recomendações de desempenho, os clientes devem testar o desempenho para avaliar o impacto dessas alterações. 

## <a name="permissions"></a>Permissões
As permissões **Proprietário** ou **Contribuidor** necessárias para executar a análise que utiliza a funcionalidade Recomendações de Desempenho.

## <a name="performance-recommendations"></a>Recomendações de desempenho
A funcionalidade [Recomendações de Desempenho](concepts-performance-recommendations.md) analisa as cargas de trabalho no servidor para identificar índices com o potencial de melhorar o desempenho.

Abra as **Recomendações de Desempenho** a partir da secção **Suporte + resolução de problemas** da barra de menus da página do portal do Azure do servidor PostgreSQL.

![Página de destino das Recomendações de Desempenho](./media/concepts-performance-recommendations/performance-recommendations-landing-page.png)

Selecione **Analisar** e escolha uma base de dados. Esta ação permitirá iniciar a análise. Dependendo da sua carga de trabalho, isto pode demorar vários minutos a concluir. Quando a análise estiver concluída, será apresentada uma notificação no portal.

A janela **Recomendações de Desempenho** mostrará uma lista de recomendações se for detetada alguma. As recomendações mostrarão informações sobre a **Base de Dados**, a **Tabela**, a **Coluna** e o **Tamanho do Índice** relevantes.

![Nova página recomendações de desempenho](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Para implementar uma recomendação, copie o texto da consulta e execute-o no cliente escolhido.

## <a name="next-steps"></a>Passos Seguintes
- Saber mais sobre [monitorização e otimização](concepts-monitoring.md) na Base de Dados do Azure para PostgreSQL.

