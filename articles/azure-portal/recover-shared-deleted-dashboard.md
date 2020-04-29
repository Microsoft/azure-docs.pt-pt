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
ms.openlocfilehash: af0c72f0bc5dd8f3a3cbae7b82b1ac56447cbb08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77133282"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Recuperar um dashboard apagado no portal Azure

Se estiver na nuvem pública do Azure e apagar um painel _publicado_ no portal Azure, poderá recuperar esse painel no prazo de 14 dias após a eliminação. Se estiver numa nuvem governamental azure ou o tablier não for publicado, não pode recuperá-lo, e deve reconstruí-lo. Para obter mais informações sobre a publicação de um dashboard, consulte [Publicar dashboard](azure-portal-dashboard-share-access.md#publish-dashboard). Siga estes passos para recuperar um dashboard publicado:

1. A partir do menu do portal Azure, selecione **grupos de recursos,** em seguida, selecione o grupo de recursos onde publicou o dashboard (por padrão, chama-se **dashboards).**

1. Sob **o registo de atividade,** expanda a operação Eliminar o **Dashboard.** Selecione o separador **'Alterar'** e, em seguida, ** \<selecione o\>recurso eliminado**.

    ![Screenshot do separador de histórico de mudança](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Selecione e copie o conteúdo do painel esquerdo e, em seguida, guarde para um ficheiro de texto com uma extensão de ficheiro _.json._ O portal utiliza o ficheiro JSON para recriar o painel de instrumentos.

    ![Screenshot da mudança de história diff](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. A partir do menu do portal Azure, selecione **Dashboards**e, em seguida, **selecione Upload**.

    ![Screenshot do upload do painel de instrumentos](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Selecione o ficheiro JSON que guardou. O portal recria o painel com o mesmo nome e elementos que o painel de instrumentos apagado.

1. Selecione **Partilhar** para publicar o painel de instrumentos e restabelecer o controlo de acesso apropriado.

    ![Screenshot da partilha do painel de instrumentos](media/recover-shared-deleted-dashboard/dashboard-share.png)
