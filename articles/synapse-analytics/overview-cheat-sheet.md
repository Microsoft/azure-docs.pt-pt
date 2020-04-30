---
title: Folha de batota - Azure Synapse Analytics
description: Guia de referência que anda pelo IA Anapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: aa93a816fc11158d928978bdec2dbf42119fa149
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424658"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Folha de batota azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

A folha de batota Azure Synapse Analytics irá guiá-lo através dos conceitos básicos do serviço e comandos importantes. Este artigo é útil tanto para os novos alunos como para aqueles que querem destaques dos tópicos essenciais do Azure Synapse.

## <a name="architecture"></a>Arquitetura

> [!div class="mx-imgBorder"]
>![Arquitetura Sinapse](media/overview-cheat-sheet/azure-synapse-architecture-cheat-sheet.png)

## <a name="concepts"></a>Conceitos
| Substantivos e verbos                         | O que faz       |
|:---                                 |:---                 |
| **Espaço de trabalho sinapse (pré-visualização)** | Um limite de colaboração assegurável para fazer análises empresariais baseadas em nuvem em Azure. Um espaço de trabalho é implantado numa região específica e tem um sistema de conta e ficheiros ADLSg2 associado (para armazenar dados temporários). Um espaço de trabalho está sob um grupo de recursos. |
| **Análise de SQL**   | Executar análises com piscinas ou com capacidades a pedido.  |
| **Conjunto de SQL**   | Os recursos aprovisionados 0 a N SQL com as respetivas bases de dados podem ser implantados num espaço de trabalho. Cada piscina SQL tem uma base de dados associada. Uma piscina SQL pode ser dimensionada, pausada e retomada manualmente ou automaticamente. Uma piscina SQL pode escalar de 100 DWU até 30.000 DWU.       |
| **SQL on-demand (pré-visualização)**   | Sistema de processamento de dados distribuído construído para dados em larga escala que permite executar consultas T-SQL sobre dados em data lake. É inútil para que não precise de gerir a infraestrutura.       |
|**Apache Spark** | Tempo de faísca usado numa piscina de faíscas. A versão atual suportada é Spark 2.4 com Python 3.6.1, Scala 2.11.12, .NET suporte para Apache Spark 0.5 e Delta Lake 0.3.  | 
| **Piscina Apache Spark (pré-visualização)**  | Os recursos aprovisionados 0-a-N Spark com as respetivas bases de dados podem ser implantados num espaço de trabalho. Uma piscina spark pode ser automaticamente pausada, retomada e escalada.  |
| **Aplicação de faíscas**  |   Consiste num processo de condutor e num conjunto de processos de executor. Uma aplicação Spark corre numa piscina de Faíscas.            |
| **Sessão de faíscas**  |   Ponto de entrada unificado de uma aplicação de faísca. Fornece uma forma de interagir com as várias funcionalidades da Spark e com um menor número de construções. Para executar um caderno, é preciso criar uma sessão. Uma sessão pode ser configurada para funcionar num número específico de executores de tamanho específico. A configuração predefinida para uma sessão de caderno é executar em 2 executores de tamanho médio. |
| **Pedido sql**  |   Funcionamento como uma consulta que passa por piscina SQL ou SQL a pedido. |
|**Integração de Dados**| Dá a capacidade de ingerir dados entre várias fontes e orquestrar atividades que funcionam dentro de um espaço de trabalho ou fora de um espaço de trabalho.| 
|**Artefactos**| Conceito que encapsula todos os objetos necessários para que um utilizador gere fontes de dados, desenvolva, orquequequee e visualize.|
|**Bloco de Notas**| Interface interativa e reativa de Data Science and Engineering suportando Scala, PySpark, C#e SparkSQL. |
|**Definição de trabalho de faísca**|Interface para submeter um trabalho spark com frasco de montagem contendo o código e suas dependências.|
|**Fluxo de Dados**|  Proporciona uma experiência totalmente visual sem codificação necessária para fazer a transformação de grandes dados. Toda a otimização e execução são tratadas de forma sem servidor. |
|**Script de SQL**| Conjunto de comandos SQL guardados num ficheiro. Um script SQL pode conter uma ou mais declarações SQL. Pode ser usado para executar pedidos SQL através de piscina SQL ou SQL on-demand.|
|**Pipeline**| Agrupamento lógico de atividades que realizam uma tarefa em conjunto.|
|**Atividade**| Define ações para executar em dados como copiar dados, executar um Notebook ou um script SQL.|
|**Acionador**| Executa um oleoduto. Pode ser executado manualmente ou automaticamente (horário, janela caindo ou baseado em eventos).|
|**Serviço ligado**| Cordas de ligação que definem as informações de ligação necessárias para que o espaço de trabalho se conectem a recursos externos.|
|**Conjunto de dados**|  Vista nomeada de dados que simplesmente apontam ou referem os dados a serem utilizados numa atividade como entrada e saída. Pertence a um Serviço Ligado.|

## <a name="next-steps"></a>Passos seguintes

- [Criar uma área de trabalho](quickstart-create-workspace.md)
- [Use o Estúdio Synapse](quickstart-synapse-studio.md)
- [Criar uma piscina SQL](quickstart-create-sql-pool.md)
- [Utilizar o SQL a pedido](quickstart-sql-on-demand.md)
- [Crie uma piscina apache spark](quickstart-create-apache-spark-pool.md)

