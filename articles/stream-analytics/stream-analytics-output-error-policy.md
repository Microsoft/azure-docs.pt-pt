---
title: Políticas de erro de saída no Azure Stream Analytics
description: Saiba mais sobre as políticas de manipulação de erros de saída disponíveis no Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 19d762a55127af34e84185b11518aa6584acb5bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012415"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Política de erro de saída do Azure Stream Analytics
Este artigo descreve as políticas de tratamento de erros de erro de saída que podem ser configuradas no Azure Stream Analytics.

As políticas de tratamento de erros de saída aplicam-se apenas a erros de conversão de dados que ocorrem quando o evento de saída produzido por um trabalho stream Analytics não está em conformidade com o esquema do pia-alvo. Pode configurar esta política escolhendo **a Retry** ou **a Drop**. No portal Azure, enquanto num trabalho de Stream Analytics, em **Configure,** selecione **Error Policy** para fazer a sua seleção.

![Localização da política de erro de saída do Azure Stream Analytics](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Tentar novamente
Quando ocorre um erro, a Azure Stream Analytics retrindo a escrita do evento indefinidamente até que a escrita tenha sucesso. Não há tempo para as recaídas. Eventualmente, todos os eventos subsequentes são bloqueados do processamento pelo evento que está a voltar a tentar. Esta opção é a política de tratamento de erros de saída por defeito.

## <a name="drop"></a>Gota
O Azure Stream Analytics ignorará todos os eventos de saída que resultem em erro de conversão de dados. Os eventos ignorados não podem ser recuperados para reprocessamento mais tarde.


Todos os erros transitórios (por exemplo, erros de rede) são novamente julgados independentemente da configuração da política de manipulação de erros de saída.


## <a name="next-steps"></a>Passos seguintes
[Guia de resolução de problemas para Azure Stream Analytics](./stream-analytics-troubleshoot-query.md)