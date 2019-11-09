---
title: Diretrizes de recuperação de desastre para Azure Data Lake Analytics
description: Saiba como planejar a recuperação de desastres para suas contas de Azure Data Lake Analytics.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f9b22e6b806f76189134ec63c83d48f48bf95587
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889771"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Diretrizes de recuperação de desastre para Azure Data Lake Analytics

O Azure Data Lake Analytics é um serviço de tarefa de análise no local que simplifica os macrodados. Em vez de implementar, configurar e otimizar hardware, escreve consultas para transformar os dados e extrair informações valiosas. O serviço de análise pode processar tarefas de qualquer dimensionamento instantaneamente definindo a quantidade de potência necessária. Apenas paga a tarefa quando estiver a ser executada, tornando-a económica. Este artigo fornece orientação sobre como proteger seus trabalhos contra interrupções raras em toda a região ou exclusões acidentais.

## <a name="disaster-recovery-guidance"></a>Documentação de orientação da recuperação após desastre

Ao usar Azure Data Lake Analytics, é essencial preparar seu próprio plano de recuperação de desastres. Este artigo ajuda você a criar um plano de recuperação de desastres. Há recursos adicionais que podem ajudá-lo a criar seu próprio plano:
- [Falha e recuperação após desastre para aplicações do Azure](/azure/architecture/reliability/disaster-recovery)
- [Orientações técnicas sobre resiliência do Azure](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Práticas recomendadas e diretrizes de cenário

Você pode executar um trabalho do U-SQL recorrente em uma conta do ADLA em uma região que lê e grava tabelas U-SQL, bem como dados não estruturados.  Prepare-se para um desastre executando estas etapas:

1. Crie contas ADLA e ADLS na região secundária que será usada durante uma interrupção.

   > [!NOTE]
   > Como os nomes de conta são globalmente exclusivos, use um esquema de nomenclatura consistente que indica qual conta é secundária.

2. Para dados não estruturados, faça referência [à orientação de recuperação de desastre para dados no Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Para os dados estruturados armazenados em tabelas e bancos de dado do ADLA, crie cópias dos artefatos de metadados, como bancos de dados, tabelas, funções com valor de tabela e assemblies. Você precisa ressincronizar periodicamente esses artefatos quando as alterações acontecem na produção. Por exemplo, os dados inseridos recentemente têm que ser replicados para a região secundária copiando os dados e inserindo na tabela secundária.

   > [!NOTE]
   > Esses nomes de objeto têm o escopo definido para a conta secundária e não são globalmente exclusivos, portanto, eles podem ter os mesmos nomes da conta de produção primária.

Durante uma interrupção, você precisa atualizar seus scripts para que os caminhos de entrada apontem para o ponto de extremidade secundário. Em seguida, os usuários enviam seus trabalhos para a conta ADLA na região secundária. A saída do trabalho será então gravada na conta ADLA e ADLS na região secundária.

## <a name="next-steps"></a>Passos seguintes

[Diretrizes de recuperação de desastre para dados em Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
