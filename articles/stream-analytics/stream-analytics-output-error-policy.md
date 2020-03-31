---
title: Políticas de erro de saída no Azure Stream Analytics
description: Conheça as políticas de manipulação de erros de saída disponíveis no Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 22112272bb302769e5969cf6995d486438deb41f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431617"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Política de erro de saída do Azure Stream Analytics
Este artigo descreve as políticas de manipulação de erros de dados de saída que podem ser configuradas no Azure Stream Analytics.

As políticas de manipulação de erros de dados de saída aplicam-se apenas a erros de conversão de dados que ocorrem quando o evento de saída produzido por um trabalho de Stream Analytics não está em conformidade com o esquema do sumidouro-alvo. Pode configurar esta política escolhendo **retry** ou **Drop**. No portal Azure, durante um trabalho de Stream Analytics, no âmbito da **Configure, selecione** **Error Policy** para fazer a sua seleção.

![Localização da política de erro de saída do Azure Stream Analytics](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Repetir
Quando ocorre um erro, o Azure Stream Analytics volta a escrever o evento indefinidamente até que a escrita tenha sucesso. Não há tempo para repetições. Eventualmente, todos os eventos subsequentes são bloqueados do processamento pelo evento que está a ser retentado. Esta opção é a política de manipulação de erros de erro de saída predefinida.

## <a name="drop"></a>Gota
O Azure Stream Analytics ignorará todos os eventos de saída que resultem em erro de conversão de dados. Os eventos ignorados não podem ser recuperados para reprocessamento mais tarde.


Todos os erros transitórios (por exemplo, erros de rede) são novamente experimentados independentemente da configuração da política de manipulação de erros de saída.


## <a name="next-steps"></a>Passos seguintes
[Guia de resolução de problemas para Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
