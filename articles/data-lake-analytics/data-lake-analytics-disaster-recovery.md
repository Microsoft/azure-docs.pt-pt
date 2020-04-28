---
title: Orientação de recuperação de desastres para Azure Data Lake Analytics
description: Saiba como planear a recuperação de desastres para as suas contas Azure Data Lake Analytics.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f9b22e6b806f76189134ec63c83d48f48bf95587
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73889771"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Orientação de recuperação de desastres para Azure Data Lake Analytics

O Azure Data Lake Analytics é um serviço de tarefa de análise no local que simplifica os macrodados. Em vez de implementar, configurar e otimizar hardware, escreve consultas para transformar os dados e extrair informações valiosas. O serviço de análise pode processar tarefas de qualquer dimensionamento instantaneamente definindo a quantidade de potência necessária. Apenas paga a tarefa quando estiver a ser executada, tornando-a económica. Este artigo fornece orientações sobre como proteger os seus empregos de falhas raras em toda a região ou supressões acidentais.

## <a name="disaster-recovery-guidance"></a>Documentação de orientação da recuperação após desastre

Ao utilizar o Azure Data Lake Analytics, é fundamental para si preparar o seu próprio plano de recuperação de desastres. Este artigo ajuda a guiá-lo para construir um plano de recuperação de desastres. Existem recursos adicionais que podem ajudá-lo a criar o seu próprio plano:
- [Falha e recuperação após desastre para aplicações do Azure](/azure/architecture/reliability/disaster-recovery)
- [Orientações técnicas sobre resiliência do Azure](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Boas práticas e orientação do cenário

Você pode executar um trabalho u-SQL recorrente em uma conta ADLA em uma região que lê e escreve tabelas U-SQL, bem como dados não estruturados.  Prepare-se para um desastre tomando estes passos:

1. Crie contas ADLA e ADLS na região secundária que serão usadas durante uma paragem.

   > [!NOTE]
   > Uma vez que os nomes de contas são globalmente únicos, use um sistema de nomeação consistente que indique qual a conta secundária.

2. Para dados não estruturados, orientação de recuperação de desastres de referência [para dados em Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Para dados estruturados armazenados em tabelas e bases de dados da ADLA, crie cópias dos artefactos metadados, tais como bases de dados, tabelas, funções de valor de tabela e conjuntos. É necessário resincronizar periodicamente estes artefactos quando as mudanças acontecem na produção. Por exemplo, os dados recém-inseridos têm de ser replicados para a região secundária copiando os dados e inserindo na tabela secundária.

   > [!NOTE]
   > Estes nomes de objetos são remetidos para a conta secundária e não são globalmente únicos, pelo que podem ter os mesmos nomes que na conta de produção primária.

Durante uma paragem, precisa de atualizar os seus scripts para que os caminhos de entrada apontem para o ponto final secundário. Em seguida, os utilizadores submetem os seus postos de trabalho à conta ADLA na região secundária. A saída do trabalho será então escrita na conta ADLA e ADLS na região secundária.

## <a name="next-steps"></a>Passos seguintes

[Orientação de recuperação de desastres para dados em Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
