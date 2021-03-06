---
title: Recuperar um dashboard eliminado no portal do Azure
description: Se eliminar um dashboard publicado no portal Azure, poderá recuperar o painel de instrumentos.
ms.date: 03/25/2021
ms.topic: troubleshooting
ms.openlocfilehash: 96d330872327f8719e7cc4287415d86fd0ae5fd4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559723"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Recuperar um dashboard eliminado no portal do Azure

Se estiver na nuvem Azure global e eliminar um dashboard _publicado_ no portal Azure, pode recuperar o painel de instrumentos no prazo de 14 dias após a eliminação. Se estiver numa nuvem do Governo Azure ou o painel de instrumentos não for publicado, não poderá recuperá-lo e deve reconstruí-lo. Para obter mais informações sobre a publicação de um dashboard, consulte [o painel de instrumentos publicar](azure-portal-dashboard-share-access.md#publish-a-dashboard). Siga estes passos para recuperar um dashboard publicado:

1. A partir do menu do portal Azure, selecione **grupos de Recursos,** em seguida, selecione o grupo de recursos onde publicou o dashboard (por padrão, os **seus dashboards nomeados).**

1. No **registo de atividade,** expanda a operação **Delete Dashboard.** Selecione o separador Alterar o **histórico** e, em seguida, selecione **\<deleted resource\>** .

    ![Screenshot do separador de histórico de mudança](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Selecione e copie o conteúdo do painel esquerdo e, em seguida, guarde para um ficheiro de texto com uma extensão de ficheiro _.json._ O portal utiliza o ficheiro JSON para recriar o painel de instrumentos.

    ![Screenshot da mudança história diff](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. A partir do menu do portal Azure, selecione **Dashboards** e, em seguida, selecione **Upload**.

    ![Screenshot do upload do painel de instrumentos](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Selecione o ficheiro JSON que guardou. O portal recria o painel de instrumentos com o mesmo nome e elementos que o painel de instrumentos apagado.

1. Selecione **Partilhar** para publicar o painel de instrumentos e restabelecer o controlo de acesso apropriado.

    ![Screenshot da partilha do painel de instrumentos](media/recover-shared-deleted-dashboard/dashboard-share.png)
