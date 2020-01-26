---
title: Recuperar um painel apagado no portal Azure Microsoft Docs
description: Se eliminar um dashboard publicado no portal Azure, pode recuperar o painel de instrumentos.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: ead9dab61e0cb60505aed9db43c61eb73a0e22a9
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760203"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Recuperar um dashboard apagado no portal Azure

Se eliminar um dashboard _publicado_ no portal Azure, pode recuperar esse painel no prazo de 14 dias após a eliminação. Se o painel não for publicado, não pode recuperá-lo, e tem de o reconstruir. Para obter mais informações sobre a publicação de um dashboard, consulte [Publicar dashboard](azure-portal-dashboard-share-access.md#publish-dashboard). Siga estes passos para recuperar um dashboard publicado:

1. A partir do menu do portal Azure, selecione **grupos de recursos,** em seguida, selecione o grupo de recursos onde publicou o dashboard (por padrão, chama-se **dashboards).**

1. Sob **o registo de atividade,** expanda a operação Eliminar o **Dashboard.** Selecione o separador **'Alterar'** e, em seguida, selecione\<\>de **recursos eliminados** .

    ![Screenshot do separador de histórico de mudança](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Selecione e copie o conteúdo do painel esquerdo e, em seguida, guarde para um ficheiro de texto com uma extensão de ficheiro _.json._ O portal utiliza o ficheiro JSON para recriar o painel de instrumentos.

    ![Screenshot da mudança de história diff](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. A partir do menu do portal Azure, selecione **Dashboards**e, em seguida, **selecione Upload**.

    ![Screenshot do upload do painel de instrumentos](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Selecione o ficheiro JSON que guardou. O portal recria o painel com o mesmo nome e elementos que o painel de instrumentos apagado.

1. Selecione **Partilhar** para publicar o painel de instrumentos e restabelecer o controlo de acesso apropriado.

    ![Screenshot da partilha do painel de instrumentos](media/recover-shared-deleted-dashboard/dashboard-share.png)
