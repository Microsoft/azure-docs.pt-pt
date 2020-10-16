---
title: 'Tutorial: Começa com o Azure Synapse Analytics - monitorize o teu espaço de trabalho da Sinapse'
description: Neste tutorial, você aprenderá a monitorizar atividades no seu espaço de trabalho Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: 752021e3838809cace8b5d1c71c2516645031f9d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089779"
---
# <a name="monitor-your-synapse-workspace"></a>Monitorize o seu espaço de trabalho de sinapse

Neste tutorial, você aprenderá a monitorizar atividades no seu espaço de trabalho Synapse. Você pode monitorizar atividades atuais e históricas para SQL, Apache Spark. e Pipelines. 

## <a name="introduction-to-the-monitor-hub"></a>Introdução ao Monitor Hub

Abra o Estúdio Synapse e navegue até ao centro **monitor.** Aqui, você pode ver uma história de todas as atividades que estão acontecendo no espaço de trabalho e quais estão ativas agora. 

* Sob **Orquestração,** pode monitorizar os oleodutos, os gatilhos e os tempos de execução da integração
* No âmbito **de Atividades,** pode monitorizar as atividades spark e SQL. 

## <a name="orchestration"></a>Orquestração

1. Navegue para **o Pipeline > Orquestração.** Nesta vista, pode ver sempre que um oleoduto corre no seu espaço de trabalho. 
1. Encontre o oleoduto que correu no passo anterior e clique no **seu nome Pipeline**.
1. Agora você pode ver como as atividades individuais dentro do oleoduto funcionam.
1. Clique **na barra Breadcrumb** perto do topo do Synapse Studio, clique em **Todas as corridas de pipeline** para voltar à vista anterior.

## <a name="apache-spark-activities"></a>Atividades de Faísca Apache

1. Navegue para **a Orquestração > Atividades > aplicações Apache Spark**. Agora pode ver todas as aplicações Spark que estão em execução ou que correram no seu espaço de trabalho.
1. Encontre uma aplicação que já não esteja em execução e clique no **nome da sua Aplicação.** Agora pode ver os detalhes da aplicação de faíscas.
1. Se estiver familiarizado com o Apache Spark, pode encontrar o servidor histórico padrão apache Spark UI clicando no **servidor histórico spark**.

## <a name="sql-activities"></a>Atividades SQL

1. Navegue para **a Orquestração > Atividades > pedidos SQL.**
1. Nesta vista pode ver os pedidos do SQL.
1. Selecione uma **Piscina** para monitorizar. Agora você pode ver todos os pedidos SQL que estão executando ou executado no seu espaço de trabalho nessa piscina.
1. Encontre um pedido SQL específico e sobressaa o rato sobre esse item. Assim que pairar, verá um ícone de script SQL aparecer.
1. Clique no ícone de script SQL para ver o texto completo do pedido SQL.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore o Centro de Conhecimento](get-started-knowledge-center.md)
