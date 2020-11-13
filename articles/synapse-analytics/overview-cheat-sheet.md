---
title: Folha de batota - Azure Synapse Analytics (pré-visualização de espaços de trabalho)
description: Guia de referência que percorre o utilizador através do Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: fbd113194aa904096425ee09b741cf693f89bba2
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591925"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Folha de batota Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

A folha de batota Azure Synapse Analytics irá guiá-lo através dos conceitos básicos do serviço e comandos importantes. Este artigo é útil tanto para os novos alunos como para aqueles que querem destaques dos tópicos essenciais do Azure Synapse.

## <a name="basics"></a>Informações básicas

Um espaço de **trabalho da Sinapse** é um limite de colaboração garantido para fazer análises empresariais baseadas na nuvem em Azure. Um espaço de trabalho é implantado numa região específica e tem um sistema de conta e ficheiro ADLS Gen2 associado (para armazenar dados temporários). Um espaço de trabalho está sob um grupo de recursos.

Um espaço de trabalho permite-lhe realizar análises com faísca SQL e Apache. Os recursos disponíveis para análises SQL e Spark estão organizados em **piscinas** SQL e Spark. 

## <a name="synapse-sql"></a>SQL do Synapse

**O SQL de Sinapse** é a capacidade de fazer análises baseadas em T-SQL no espaço de trabalho da Synapse. O Synapse SQL tem dois modelos de consumo: dedicados e sem servidor.  Para o modelo dedicado, utilize **piscinas SQL dedicadas.** Um espaço de trabalho pode ter qualquer número destas piscinas. Para utilizar o modelo sem servidor, utilize as **piscinas SQL sem servidor**. Cada espaço de trabalho tem uma destas piscinas.

## <a name="apache-spark-for-synapse"></a>Faísca apache para sinapse

Para utilizar a análise spark, crie e use **piscinas Apache Spark sem servidor** no seu espaço de trabalho Synapse.

* **PEDIDO SQL** - Funcionamento como uma consulta executada através de piscina SQL dedicada ou piscina SQL sem servidor.
* **Script SQL** - Conjunto de comandos SQL guardados num ficheiro. Um script SQL pode conter uma ou mais declarações SQL. Pode ser usado para executar pedidos SQL através de piscina SQL dedicada ou piscina SQL sem servidor.


## <a name="spark-terminology"></a>Terminologia de faíscas

* **Apache Spark for Synapse** - Spark run-time usado em uma piscina de faísca sem servidor. A versão atual suportada é Spark 2.4 com Python 3.6.1, Scala 2.11.12, suporte .NET para Apache Spark 0.5 e Delta Lake 0.3.  
* **O conjunto Apache Spark** - 0-to-N Spark recursos ateados com as respetivas bases de dados podem ser implantados num espaço de trabalho. Uma piscina spark pode ser auto-pausada, retomada e escalada.  
* **Pedido de faísca** - Consiste num processo de condutor e num conjunto de processos de executor. Uma aplicação Spark funciona numa piscina de faíscas sem servidor.            
* **Sessão de faíscas** - Ponto de entrada unificado de uma aplicação de faísca. Fornece uma forma de interagir com as várias funcionalidades de Spark e com um menor número de construções. Para executar um caderno, é preciso criar uma sessão. Uma sessão pode ser configurada para executar um número específico de executores de tamanho específico. A configuração predefinida para uma sessão de portátil é executada em 2 executores de tamanho médio.
* **Notebook** - Interface interativa e reativa de Data Science and Engineering de apoio a Scala, PySpark, C#e SparkSQL.
* **Definição de trabalho de faísca** - Interface para submeter um trabalho de Faísca com frasco de montagem contendo o código e suas dependências.

## <a name="pipelines-terminology"></a>Terminologia dos Oleodutos
* **Integração de Dados** - Dá a capacidade de ingerir dados entre várias fontes e orquestrar atividades que funcionam dentro de um espaço de trabalho ou fora de um espaço de trabalho.
* **Data Flow** - Proporciona uma experiência totalmente visual sem codificação necessária para fazer a transformação de big data. Toda a otimização e execução são tratadas de forma sem servidor.
* **Pipeline** - Agrupamento lógico de atividades que realizam uma tarefa em conjunto.
* **Atividade** - Define ações para executar em dados como copiar dados, executar um Caderno ou um script SQL.
* **Trigger** - Executa um oleoduto. Pode ser executado manualmente ou automaticamente (agendar, tropeçar na janela ou no evento)
* **Conjunto de dados** de integração - Visão nomeada de dados que simplesmente apontam ou referenciam os dados a serem utilizados numa atividade como entrada e saída. Pertence a um Serviço Linked.

## <a name="general-terminology"></a>Terminologia Geral

* **Artefactos** - Conceito que encapsula todos os objetos necessários para que um utilizador gere fontes de dados, desenvolva, orquestite e visualize.
* **Serviço ligado** - Cadeias de ligação que definem as informações de ligação necessárias para que o espaço de trabalho se conecte a recursos externos.

## <a name="next-steps"></a>Próximos passos

- [Criar uma área de trabalho](quickstart-create-workspace.md)
- [Utilizar o Synapse Studio](quickstart-synapse-studio.md)
- [Crie uma piscina SQL dedicada](quickstart-create-sql-pool-portal.md)
- [Crie uma piscina Apache Spark sem servidor](quickstart-create-apache-spark-pool-portal.md)
- [Utilizar conjunto de SQL sem servidor](quickstart-sql-on-demand.md)

