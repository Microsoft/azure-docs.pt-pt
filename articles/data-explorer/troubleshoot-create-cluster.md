---
title: Falha ao criar um cluster no Explorador de dados do Azure
description: Este artigo descreve os passos de resolução de problemas para criar um cluster no Explorador de dados do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b95dabbdecd98902da3bf8217a14f41019c31e82
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757683"
---
# <a name="troubleshoot-failure-to-create-a-cluster-in-azure-data-explorer"></a>Resolução de problemas: Falha ao criar um cluster no Explorador de dados do Azure

Na improvável eventualidade que falha ao criar o cluster no Explorador de dados do Azure, siga estes passos.

1. Certifique-se de que tem as permissões adequadas. Para criar um cluster, tem de ser um membro do *contribuinte* ou *proprietário* função para a subscrição do Azure. Se necessário, trabalhe com o seu administrador de subscrição para que eles podem adicioná-lo à função adequada.

1. Certifique-se de que existem não existem erros de validação relacionados com o nome do cluster que introduziu sob **criar um cluster** no portal do Azure.

1. Verifique os [dashboard de estado de funcionamento do serviço do Azure](https://azure.microsoft.com/status/). Procure o estado do Explorador de dados do Azure na região onde está a tentar criar o cluster.

    Se o estado não estiver **boa** (marca de verificação verde), tente criar o cluster depois do Estado melhora.

1. Se ainda precisar de assistência para resolver seu problema, abra um pedido de suporte no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).