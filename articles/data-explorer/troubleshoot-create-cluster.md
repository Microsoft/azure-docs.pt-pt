---
title: Falha ao criar um cluster no Explorador de dados do Azure
description: Este artigo descreve os passos de resolução de problemas para criar um cluster no Explorador de dados do Azure.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9b8bfe2a4b9b7a8432f14fb53b3e7a4cae49a3b4
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189976"
---
# <a name="troubleshoot-failure-to-create-a-cluster-in-azure-data-explorer"></a>Resolução de problemas: Falha ao criar um cluster no Explorador de dados do Azure

Na improvável eventualidade que falha ao criar o cluster no Explorador de dados do Azure, siga estes passos.

1. Certifique-se de que tem as permissões adequadas. Para criar um cluster, tem de ser um membro do *contribuinte* ou *proprietário* função para a subscrição do Azure. Se necessário, trabalhe com o seu administrador de subscrição para que eles podem adicioná-lo à função adequada.

1. Certifique-se de que existem não existem erros de validação relacionados com o nome do cluster que introduziu sob **criar um cluster** no portal do Azure.

1. Verifique os [dashboard de estado de funcionamento do serviço do Azure](https://azure.microsoft.com/status/). Procure o estado do Explorador de dados do Azure na região onde está a tentar criar o cluster.

    Se o estado não estiver **boa** (marca de verificação verde), tente criar o cluster depois do Estado melhora.

1. Se ainda precisar de assistência para resolver seu problema, abra um pedido de suporte no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).