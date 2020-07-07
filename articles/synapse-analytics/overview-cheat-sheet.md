---
title: Folha de batota - Azure Synapse Analytics (pré-visualização de espaços de trabalho)
description: Guia de referência que percorre o utilizador através do Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 3141f8044a4a257de8022ff789b12d5d3e6e7a90
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85807031"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Folha de batota Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

A folha de batota Azure Synapse Analytics irá guiá-lo através dos conceitos básicos do serviço e comandos importantes. Este artigo é útil tanto para os novos alunos como para aqueles que querem destaques dos tópicos essenciais do Azure Synapse.

## <a name="architecture"></a>Arquitetura

> [!div class="mx-imgBorder"]
>![Arquitetura Sinapse](media/overview-cheat-sheet/azure-synapse-architecture-cheat-sheet.png)

## <a name="terminology"></a>Terminologia
| Termo                         | Definição      |
|:---                                 |:---                 |
| **Espaço de trabalho sinapse** | Um limite de colaboração securável para fazer análises empresariais baseadas em nuvem em Azure. Um espaço de trabalho é implantado numa região específica e tem um sistema de conta e ficheiro ADLS Gen2 associado (para armazenar dados temporários). Um espaço de trabalho está sob um grupo de recursos. |
| **SQL do Synapse**   | Executar análises com piscinas ou com capacidades a pedido.  |
| **Conjunto de SQL**   | Os recursos a provisionados de 0 a N SQL com as respetivas bases de dados podem ser implantados num espaço de trabalho. Cada piscina SQL tem uma base de dados associada. Uma piscina SQL pode ser dimensionada, pausada e retomada manualmente ou automaticamente. Uma piscina SQL pode escalar de 100 DWU até 30.000 DWU.       |
| **SQL a pedido**   | Sistema de processamento de dados distribuído construído para dados em larga escala que permite executar consultas T-SQL sobre dados em data lake. É sem servidor, por isso não precisa de gerir a infraestrutura.       |
|**Faísca apache para sinapse** | Tempo de corrida de faísca usado numa piscina de faíscas. A versão atual suportada é Spark 2.4 com Python 3.6.1, Scala 2.11.12, suporte .NET para Apache Spark 0.5 e Delta Lake 0.3.  | 
| **Piscina de faíscas Apache**  | Os recursos ateados 0-a-N Spark com as respetivas bases de dados podem ser implantados num espaço de trabalho. Uma piscina spark pode ser auto-pausada, retomada e escalada.  |
| **Aplicação de faísca**  |   Consiste num processo de condutor e num conjunto de processos de executor. Uma aplicação Spark funciona numa piscina de faíscas.            |
| **Sessão de faísca**  |   Ponto de entrada unificado de uma aplicação de faísca. Fornece uma forma de interagir com as várias funcionalidades de Spark e com um menor número de construções. Para executar um caderno, é preciso criar uma sessão. Uma sessão pode ser configurada para executar um número específico de executores de tamanho específico. A configuração predefinida para uma sessão de portátil é para ser executada em 2 executores de tamanho médio. |
| **Pedido SQL**  |   Funcionamento como uma consulta que passa pela piscina SQL ou SQL a pedido. |
|**Integração de Dados**| Dá a capacidade de ingerir dados entre várias fontes e orquestrar atividades que funcionam dentro de um espaço de trabalho ou fora de um espaço de trabalho.| 
|**Artefactos**| Conceito que encapsula todos os objetos necessários para que um utilizador gere fontes de dados, desenvolva, orquestite e visualize.|
|**Bloco de Notas**| Interface interativa e reativa de Data Science and Engineering suportando Scala, PySpark, C#e SparkSQL. |
|**Definição de trabalho de faísca**|Interface para submeter um trabalho Spark com frasco de montagem contendo o código e suas dependências.|
|**Fluxo de Dados**|  Proporciona uma experiência totalmente visual sem codificação necessária para fazer a transformação de big data. Toda a otimização e execução são tratadas de forma sem servidor. |
|**Script de SQL**| Conjunto de comandos SQL guardados num ficheiro. Um script SQL pode conter uma ou mais declarações SQL. Pode ser usado para executar pedidos SQL através da piscina SQL ou SQL on demand.|
|**Pipeline**| Agrupamento lógico de atividades que realizam uma tarefa em conjunto.|
|**Atividade**| Define ações para executar em dados como copiar dados, executar um Caderno ou um script SQL.|
|**Acionador**| Executa um oleoduto. Pode ser executado manualmente ou automaticamente (agendar, caindo na janela ou no evento).|
|**Serviço ligado**| Cadeias de ligação que definem as informações de ligação necessárias para que o espaço de trabalho se conecte a recursos externos.|
|**Conjunto de dados**|  Vista nomeada de dados que simplesmente apontam ou referenciam os dados a serem utilizados numa atividade como entrada e saída. Pertence a um Serviço Linked.|

## <a name="next-steps"></a>Próximos passos

- [Criar uma área de trabalho](quickstart-create-workspace.md)
- [Use o Estúdio Synapse](quickstart-synapse-studio.md)
- [Criar uma piscina SQL](quickstart-create-sql-pool-portal.md)
- [Criar uma piscina Apache Spark](quickstart-create-apache-spark-pool-portal.md)
- [Utilizar o SQL a pedido](quickstart-sql-on-demand.md)

