---
title: Falha ao ligar a um cluster no Explorador de dados do Azure
description: Este artigo descreve os passos de resolução de problemas para ligar a um cluster no Explorador de Azure.Data.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 66d77a11f0f68a2dc48802ed21acca8420012207
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758551"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Resolução de problemas: Falha ao ligar a um cluster no Explorador de dados do Azure

Se não conseguir ligar a um cluster no Explorador de dados do Azure, siga estes passos.

1. Certifique-se de que a cadeia de ligação está correta. Ele deve ter o formato: `https://<ClusterName>.<Region>.kusto.windows.net`, como o exemplo seguinte: `https://docscluster.westus.kusto.windows.net`.

1. Certifique-se de que tem as permissões adequadas. Caso contrário, obterá uma resposta de *não autorizado*.

    Para obter mais informações sobre as permissões, consulte [gerir permissões de base de dados](manage-database-permissions.md). Se necessário, trabalhe com o seu administrador de cluster para que eles podem adicioná-lo à função adequada.

1. Certifique-se de que o cluster ainda não foram eliminado: rever o registo de atividades na sua subscrição.

1. Verifique os [dashboard de estado de funcionamento do serviço do Azure](https://azure.microsoft.com/status/). Procure o estado do Explorador de dados do Azure na região onde está a tentar ligar a um cluster.

    Se o estado não estiver **boa** (marca de verificação verde), tente ligar ao cluster depois do Estado melhora.

1. Se ainda precisar de assistência para resolver seu problema, abra um pedido de suporte no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).