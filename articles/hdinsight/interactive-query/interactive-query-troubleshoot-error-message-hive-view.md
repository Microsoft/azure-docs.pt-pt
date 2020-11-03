---
title: Mensagem de erro não mostrada na Visão de Colmeia Apache - Azure HDInsight
description: A consulta falha no Apache Hive View sem quaisquer detalhes sobre o cluster Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 7aadef0d4c70f748b4f7a7c7f9fc16c38216fa7a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288954"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Cenário: Mensagem de erro de consulta não exibida na Visão de Colmeia Apache em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao utilizar componentes de consultainterna interactive em clusters Azure HDInsight.

## <a name="issue"></a>Problema

A mensagem de erro de consulta Apache Hive View será algo assim, sem mais informações:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Causa

Por vezes, a mensagem de erro de uma falha de consulta pode ser demasiado grande para ser exibida na página principal do Hive View.

## <a name="resolution"></a>Resolução

Consulte o separador Notificações no canto superior direito do Hive_view para ver a mensagem completa de Stacktrace e Error.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]