---
title: Políticas de erro de saída no Azure Stream Analytics
description: Saiba mais sobre os políticas disponíveis no Azure Stream Analytics de tratamento de erros de saída.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 22112272bb302769e5969cf6995d486438deb41f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431617"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Política de erros de saída do Azure Stream Analytics
Este artigo descreve o erro de dados de saída lidar com as políticas que podem ser configuradas no Azure Stream Analytics.

Tratamento de erros de dados de saída políticas se aplicam apenas a erros de conversão de dados que ocorrem quando o evento de saída produzidos por uma tarefa do Stream Analytics não obedece ao esquema do coletor de destino. Pode configurar esta política ao selecionar qualquer um **repita** ou **Drop**. No portal do Azure, enquanto estiver numa tarefa do Stream Analytics, sob **configurar**, selecione **política de erro** para fazer a sua seleção.

![Localização de política de erro de saída do Azure Stream Analytics](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Repetir
Quando ocorre um erro, as repetições de Azure Stream Analytics escrever o evento indefinidamente até que a gravação for concluída com êxito. Não há nenhum tempo de limite de tentativas. Eventualmente, todos os eventos subsequentes são impedidos de processamento pelo evento que está a tentar novamente. Esta opção é o erro de saída predefinido, diretiva de tratamento.

## <a name="drop"></a>Remover
O Azure Stream Analytics ignorará todos os eventos de saída que resultem em erro de conversão de dados. Os eventos ignorados não podem ser recuperados para reprocessamento mais tarde.


Todos os erros transitórios (por exemplo, erros de rede) sejam repetidos, independentemente do configuração de diretiva de tratamento de erros de saída.


## <a name="next-steps"></a>Passos seguintes
[Guia de resolução de problemas para o Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
