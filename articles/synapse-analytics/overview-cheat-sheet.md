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
ms.openlocfilehash: 774e503bec3f1f8c4cc5b85bb599230a3397f811
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858443"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Folha de batota Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

A folha de batota Azure Synapse Analytics irá guiá-lo através dos conceitos básicos do serviço e comandos importantes. Este artigo é útil tanto para os novos alunos como para aqueles que querem destaques dos tópicos essenciais do Azure Synapse.

## <a name="basics"></a>Noções básicas

Um espaço de **trabalho da Sinapse** é um limite de colaboração garantido para fazer análises empresariais baseadas na nuvem em Azure. Um espaço de trabalho é implantado numa região específica e tem um sistema de conta e ficheiro ADLS Gen2 associado (para armazenar dados temporários). Um espaço de trabalho está sob um grupo de recursos.

Um espaço de trabalho permite-lhe realizar análises com faísca SQL e Apache. Os recursos disponíveis para análises SQL e Spark estão organizados em **piscinas**SQL e Spark. 

## <a name="synapse-sql"></a>SQL do Synapse
**O SQL de Sinapse** é a capacidade de fazer análises baseadas em T-SQL no espaço de trabalho da Synapse. O Synapse SQL tem dois modelos de consumo: dedicados e sem servidor.  Para o modelo dedicado, utilize **piscinas SQL dedicadas.** Um espaço de trabalho pode ter qualquer número destas piscinas. Para utilizar o modelo sem servidor, utilize a piscina SQL sem servidor chamada "SQL on demand". Cada espaço de trabalho tem uma destas piscinas.

## <a name="apache-spark-for-synapse"></a>Faísca apache para sinapse
Para utilizar a análise spark, crie e use **piscinas Spark** no seu espaço de trabalho Synapse.

## <a name="sql-terminology"></a>Terminologia SQL
| Termo                         | Definição      |
|:---                                 |:---                 |
| **Pedido SQL**  |   Funcionamento como uma consulta que passa pela piscina SQL ou SQL a pedido. |

## <a name="spark-terminology"></a>Terminologia de faíscas
| Termo                         | Definição      |
|:---                                 |:---                 |
|**Faísca apache para sinapse** | Tempo de corrida de faísca usado numa piscina de faíscas. A versão atual suportada é Spark 2.4 com Python 3.6.1, Scala 2.11.12, suporte .NET para Apache Spark 0.5 e Delta Lake 0.3.  | 
| **Piscina de faíscas Apache**  | Os recursos ateados 0-a-N Spark com as respetivas bases de dados podem ser implantados num espaço de trabalho. Uma piscina spark pode ser auto-pausada, retomada e escalada.  |
| **Aplicação de faísca**  |   Consiste num processo de condutor e num conjunto de processos de executor. Uma aplicação Spark funciona numa piscina de faíscas.            |
| **Sessão de faísca**  |   Ponto de entrada unificado de uma aplicação de faísca. Fornece uma forma de interagir com as várias funcionalidades de Spark e com um menor número de construções. Para executar um caderno, é preciso criar uma sessão. Uma sessão pode ser configurada para executar um número específico de executores de tamanho específico. A configuração predefinida para uma sessão de portátil é para ser executada em 2 executores de tamanho médio. |
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
- [Utilizar o Synapse Studio](quickstart-synapse-studio.md)
- [Criar uma piscina SQL](quickstart-create-sql-pool-portal.md)
- [Criar uma piscina Apache Spark](quickstart-create-apache-spark-pool-portal.md)
- [Utilizar o SQL a pedido](quickstart-sql-on-demand.md)

