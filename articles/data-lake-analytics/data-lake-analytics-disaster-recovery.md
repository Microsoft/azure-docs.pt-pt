---
title: Orientações sobre a recuperação após desastre para o Azure Data Lake Analytics
description: Saiba como planear a recuperação após desastre para as suas contas do Azure Data Lake Analytics.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ea1d4020aa9be23b4839690ae0b386d35bce8a23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498893"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Orientações sobre a recuperação após desastre para o Azure Data Lake Analytics

O Azure Data Lake Analytics é um serviço de tarefa de análise no local que simplifica os macrodados. Em vez de implementar, configurar e otimizar hardware, escreve consultas para transformar os dados e extrair informações valiosas. O serviço de análise pode processar tarefas de qualquer dimensionamento instantaneamente definindo a quantidade de potência necessária. Apenas paga a tarefa quando estiver a ser executada, tornando-a económica. Este artigo fornece orientações sobre como proteger suas tarefas de falhas raras de toda a região ou eliminações acidentais.

## <a name="disaster-recovery-guidance"></a>Documentação de orientação da recuperação após desastre

Ao utilizar o Azure Data Lake Analytics, é fundamental para se preparar o seu plano de recuperação após desastre. Este artigo ajuda a guiá-lo para criar um plano de recuperação após desastre. Existem recursos adicionais que podem ajudar a criar o seu plano:
- [Falha e recuperação após desastre para aplicações do Azure](/azure/architecture/reliability/disaster-recovery)
- [Orientações técnicas sobre resiliência do Azure](/azure/architecture/reliability)

## <a name="best-practices-and-scenario-guidance"></a>Melhores práticas e diretrizes de cenário

Pode executar uma tarefa recorrente do U-SQL numa conta ADLA numa região que lê e escreve as tabelas de U-SQL, bem como os dados não estruturados.  Prepare-se de um desastre através dos passos seguintes:

1. Crie contas ADLA e ADLS na região secundária que será utilizado durante um período de indisponibilidade.

   > [!NOTE]
   > Uma vez que os nomes das contas são globalmente exclusivos, use um esquema de nomenclatura consistente que indica que conta é secundária.

2. Para dados não estruturados, referenciar [orientações sobre a recuperação após desastre para dados na geração 1 de armazenamento do Azure Data Lake](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Para dados estruturados armazenados em tabelas ADLA e bases de dados, crie cópias dos artefactos de metadados, tais como bases de dados, tabelas, as funções com valor de tabela e assemblies. É necessário ressincronizar periodicamente esses artefatos quando as alterações ocorrem na produção. Por exemplo, dados recém-inserida tem de ser replicadas para a região secundária ao copiar os dados e a inserir na tabela secundária.

   > [!NOTE]
   > Estes nomes de objeto passam para a conta secundária e não são globalmente exclusivos, pelo que podem ter os mesmos nomes, tal como a conta de produção principal.

Durante uma falha, terá de atualizar seus scripts, para que os caminhos de entrada apontam para o ponto final secundário. Em seguida, os utilizadores enviar seus trabalhos para a conta ADLA na região secundária. O resultado da tarefa, em seguida, será escrito para a conta ADLA e ADLS na região secundária.

## <a name="next-steps"></a>Passos Seguintes

[Orientações sobre a recuperação após desastre para dados na geração 1 de armazenamento do Azure Data Lake](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
