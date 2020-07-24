---
title: 'Tutorial: Começa com o Azure Synapse Analytics'
description: Neste tutorial, você aprenderá os passos básicos para configurar e usar Azure Synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 7affc5aad89fd79e6ba6480f7bf10d37f90dc5e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075872"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Começar com Azure Synapse Analytics

Este tutorial é um guia passo a passo através das principais áreas de recurso da Azure Synapse Analytics. O tutorial é o ponto de partida ideal para alguém que quer uma visita guiada através dos cenários-chave do Azure Synapse Analytics. Depois de seguir os passos no tutorial, você terá um espaço de trabalho synapse totalmente funcional no qual você pode começar a analisar dados usando SQL, SQL on-demand, e Apache Spark.

Vai aprender a:
* Provisionar um espaço de trabalho synapse numa subscrição do Azure
* Configure o controlo de acesso numa conta ADLSGEN2 para que funcione perfeitamente com o espaço de trabalho synapse
* Carregue os dados da amostra NYCTaxi no espaço de trabalho da Sinaapse para que possa ser usado por SQL, SQL on-demand e Spark
* Editar e executar scripts SQL e Cadernos Synapse usando o Synapse Studio
* Mesas SQL de consulta e mesas de faísca
* Carregar dados das tabelas SQL em dataframes spark
* Carregue os dados nas tabelas SQL a partir de dataframes spark
* Explore o conteúdo de uma conta ADLSGEN2
* Analise os ficheiros de parquet nas contas ADLSGEN2 utilizando a Spark e a SQL on demand 
* Construa um pipeline de dados que executa automaticamente um caderno Synapse a cada hora

Siga os passos *para que os* dois, como mostrado abaixo, faça um tour por muitas das capacidades e aprenda a exercitar as suas principais características.

* [PASSO 1 - Criar e configurar um espaço de trabalho sinapse](get-started-create-workspace.md)
* [PASSO 2 - Analisar usando uma piscina SQL](get-started-analyze-sql-pool.md)
* [PASSO 3 - Analisar usando faísca](get-started-analyze-spark.md)
* [PASSO 4 - Analisar usando SQL on demand](get-started-analyze-sql-on-demand.md)
* [PASSO 5 - Analisar dados numa conta de armazenamento](get-started-analyze-storage.md)
* [PASSO 6 - Orquestrar com oleodutos](get-started-pipelines.md)
* [PASSO 7 - Visualizar dados com Power BI](get-started-visualize-power-bi.md)
