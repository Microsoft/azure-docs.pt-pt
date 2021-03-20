---
title: Terminologia - Azure Synapse Analytics
description: Guia de referência que percorre o utilizador através do Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 828f37030ae567cacbaad25849b7ba24c561c20c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98132771"
---
# <a name="azure-synapse-analytics-terminology"></a>Terminologia Azure Synapse Analytics

Este documento guia-o através dos conceitos básicos da Azure Synapse Analytics.

## <a name="basics"></a>Noções básicas

Um espaço de **trabalho da Sinapse** é um limite de colaboração garantido para fazer análises empresariais baseadas na nuvem em Azure. Um espaço de trabalho é implantado numa região específica e tem um sistema de conta e ficheiro ADLS Gen2 associado (para armazenar dados temporários). Um espaço de trabalho está sob um grupo de recursos.

Um espaço de trabalho permite-lhe realizar análises com faísca SQL e Apache. Os recursos disponíveis para análises SQL e Spark estão organizados em **piscinas** SQL e Spark. 

## <a name="linked-services"></a>Serviços ligados

Um espaço de trabalho pode conter qualquer número de **serviço Linked,** essencialmente cordas de conexão que definem as informações de conexão necessárias para que o espaço de trabalho se conecte a recursos externos.

## <a name="synapse-sql"></a>SQL do Synapse

**O SQL de Sinapse** é a capacidade de fazer análises baseadas em T-SQL no espaço de trabalho da Synapse. O Synapse SQL tem dois modelos de consumo: dedicados e sem servidor.  Para o modelo dedicado, utilize **piscinas SQL dedicadas.** Um espaço de trabalho pode ter qualquer número destas piscinas. Para utilizar o modelo sem servidor, utilize as **piscinas SQL sem servidor**. Cada espaço de trabalho tem uma destas piscinas.

Dentro do Synapse Studio, você pode trabalhar com piscinas SQL criando e executando **scripts SQL** .

## <a name="apache-spark-for-synapse"></a>Faísca apache para sinapse

Para utilizar a análise spark, crie e use **piscinas Apache Spark sem servidor** no seu espaço de trabalho Synapse. Quando você começa a usar uma piscina Spark, os espaços de trabalho criam uma **sessão de faísca** para lidar com os recursos associados a essa sessão. 

Há duas maneiras dentro da Sinapse de usar faísca:
* **Os Cadernos Spark** para fazer dados Ciência e Engenharia usam Scala, PySpark, C#e SparkSQL
* **Spark definições de trabalho** para executar trabalhos de spark de lote usando ficheiros de frascos.

## <a name="pipelines"></a>Pipelines

Os oleodutos são como o Azure Synapse fornece integração de dados - permitindo-lhe mover dados entre serviços e atividades orquestradas.

* **Pipeline** são agrupamentos lógicos de atividades que realizam uma tarefa em conjunto.
* **As atividades** definem ações dentro de um Pipeline para executar dados como copiar dados, executar um Caderno ou um script SQL.
* **Data Flows** é um tipo específico de atividade que fornece uma experiência sem código para fazer a transformação de dados que usa a Synapse Spark sub-the-covers.
* **Trigger** - Executa um oleoduto. Pode ser executado manualmente ou automaticamente (agendar, tropeçar na janela ou no evento)
* **Conjunto de dados** de integração - Visão nomeada de dados que simplesmente apontam ou referenciam os dados a serem utilizados numa atividade como entrada e saída. Pertence a um Serviço Linked.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Synapse Analytics](get-started.md)
* [Criar uma área de trabalho](quickstart-create-workspace.md)
* [Utilizar conjunto de SQL sem servidor](quickstart-sql-on-demand.md)

