---
title: Falha na resolução de problemas da criação de cluster do Azure Data Explorer
description: Este artigo descreve passos de resolução de problemas para a criação de um cluster no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562416"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Troubleshoot: Falha na criação de cluster do Azure Data Explorer

No caso improvável de a criação de cluster falhar no Azure Data Explorer, siga estes passos.

1. Verifique se tem as permissões adequadas. Para criar um cluster, deve ser membro da função *De Colaborador* ou *Proprietário* para a subscrição do Azure. Se necessário, trabalhe com o seu administrador de subscrição para que possam adicioná-lo ao papel adequado.

1. Certifique-se de que não existem erros de validação relacionados com o nome de cluster introduzido no **cluster Create** no portal Azure.

1. Consulte o [dashboard do estado de funcionamento do serviço Azure](https://azure.microsoft.com/status/) Procure o estado do Azure Data Explorer na região onde está a tentar criar o cluster.

    Se o estado não for **bom** (marca de verificação verde), tente criar o cluster depois de o estado melhorar.

1. Se ainda precisar de assistência para resolver o seu problema, por favor abra um pedido de apoio no [portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
