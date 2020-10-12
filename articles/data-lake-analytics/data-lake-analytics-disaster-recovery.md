---
title: Orientação de recuperação de desastres para Azure Data Lake Analytics
description: Saiba como planear a recuperação de desastres para as suas contas Azure Data Lake Analytics.
services: data-lake-analytics
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: ab39ca8e71376fed681c049d338096ff992fed99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132573"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Orientação de recuperação de desastres para Azure Data Lake Analytics

O Azure Data Lake Analytics é um serviço de tarefa de análise no local que simplifica os macrodados. Em vez de implementar, configurar e otimizar hardware, escreve consultas para transformar os dados e extrair informações valiosas. O serviço de análise pode processar tarefas de qualquer dimensionamento instantaneamente definindo a quantidade de potência necessária. Apenas paga a tarefa quando estiver a ser executada, tornando-a económica. Este artigo fornece orientações sobre como proteger os seus empregos de interrupções raras em toda a região ou supressões acidentais.

## <a name="disaster-recovery-guidance"></a>Documentação de orientação da recuperação após desastre

Ao utilizar o Azure Data Lake Analytics, é fundamental para si preparar o seu próprio plano de recuperação de desastres. Este artigo ajuda a guiá-lo para construir um plano de recuperação de desastres. Existem recursos adicionais que podem ajudá-lo a criar o seu próprio plano:
- [Falha e recuperação após desastre para aplicações do Azure](/azure/architecture/reliability/disaster-recovery)
- [Orientações técnicas sobre resiliência do Azure](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Boas práticas e orientação de cenários

Você pode executar um trabalho U-SQL recorrente em uma conta ADLA em uma região que lê e escreve tabelas U-SQL, bem como dados não estruturados.  Prepare-se para um desastre tomando estes passos:

1. Criar contas ADLA e ADLS na região secundária que serão utilizadas durante uma paragem.

   > [!NOTE]
   > Uma vez que os nomes das contas são globalmente únicos, use um esquema de nomeação consistente que indique qual a conta secundária.

2. Para dados não estruturados, referência [Orientação de recuperação de desastres para dados em Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Para dados estruturados armazenados em tabelas e bases de dados ADLA, crie cópias dos artefactos dos metadados, tais como bases de dados, tabelas, funções de valor de tabela e conjuntos. É preciso ressíndir periodicamente estes artefactos quando as mudanças acontecem na produção. Por exemplo, os dados recém-inseridos devem ser replicados na região secundária copiando os dados e inserindo-os na tabela secundária.

   > [!NOTE]
   > Estes nomes de objetos são traçados para a conta secundária e não são globalmente únicos, para que possam ter os mesmos nomes que na conta de produção primária.

Durante uma paragem, precisa de atualizar os scripts para que os caminhos de entrada aplindam para o ponto final secundário. Em seguida, os utilizadores submetem os seus postos de trabalho à conta ADLA na região secundária. A produção do trabalho será então escrita para a conta ADLA e ADLS na região secundária.

## <a name="next-steps"></a>Passos seguintes

[Orientação de recuperação de desastres para dados em Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
