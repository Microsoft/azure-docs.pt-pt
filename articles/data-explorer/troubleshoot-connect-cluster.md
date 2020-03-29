---
title: Falhas de ligação ao cluster do Explorador de Dados Azure
description: Este artigo descreve passos de resolução de problemas para a ligação a um cluster no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c71af799f614e9cd28221d79634666cbc3b2c987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60827041"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Resolução de problemas: Falha na ligação a um cluster no Azure Data Explorer

Se não conseguir ligar a um cluster no Azure Data Explorer, siga estes passos.

1. Verifique se a cadeia de ligação está correta. Deve estar na forma: `https://<ClusterName>.<Region>.kusto.windows.net`, tal como `https://docscluster.westus.kusto.windows.net`o seguinte exemplo: .

1. Verifique se tem as permissões adequadas. Se não, receberá uma resposta de *não autorizado*.

    Para mais informações sobre permissões, consulte [Gerir permissões de bases de dados](manage-database-permissions.md). Se necessário, trabalhe com o administrador do cluster para que este o possa adicionar à função correspondente.

1. Verifique se o cluster não foi eliminado: reveja o registo de atividades na sua subscrição.

1. Consulte o [dashboard do estado de funcionamento do serviço Azure](https://azure.microsoft.com/status/) Procure o estado do Azure Data Explorer na região na qual está a tentar ligar a um cluster.

    Se o estado não for **bom** (marca de verificação verde), tente ligar-se ao cluster depois de o estado melhorar.

1. Se ainda precisar de assistência para resolver o seu problema, por favor abra um pedido de apoio no [portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).