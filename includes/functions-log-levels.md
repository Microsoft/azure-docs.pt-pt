---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 72588ccfd9ad00305cbdeaf8132999731d86b9b1
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164927"
---
Um *nível de registo* é atribuído a cada registo. O valor é um número inteiro que indica importância relativa:

|Nível de Registo    |Código| Descrição |
|------------|---|--------------|
|Rastreio       | 0 |Registos que contêm as mensagens mais detalhadas. Estas mensagens podem conter dados de aplicação sensíveis. Estas mensagens são desativadas por padrão e nunca devem ser ativadas num ambiente de produção.|
|Depurar       | 1 | Troncos que são usados para investigação interativa durante o desenvolvimento. Estes registos devem conter principalmente informações úteis para depuração e não têm valor a longo prazo. |
|Informações | 2 | Troncos que acompanham o fluxo geral da aplicação. Estes registos devem ter valor a longo prazo. |
|Aviso     | 3 | Registos que realçam um evento anormal ou inesperado no fluxo da aplicação, mas não fazem com que a execução da aplicação pare. |
|Erro       | 4 | Troncos que realçam quando o fluxo atual de execução é interrompido por causa de uma falha. Estes erros devem indicar uma falha na atividade atual, e não uma falha a nível da aplicação. |
|Crítico    | 5 | Registos que descrevem uma aplicação ou falha do sistema não recuperáveis, ou uma falha catastrófica que requer atenção imediata. |
|Nenhum        | 6 | Desativa o registo para a categoria especificada. |

A [ *host.jsna* ](../articles/azure-functions/functions-host-json.md) configuração do ficheiro determina a quantidade de registo de uma aplicação de funções que envia para o Application Insights.  
