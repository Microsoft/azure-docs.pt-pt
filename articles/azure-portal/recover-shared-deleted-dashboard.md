---
title: Recuperar um dashboard apagado no portal Azure Microsoft Docs
description: Se eliminar um dashboard publicado no portal Azure, poderá recuperar o painel de instrumentos.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7b3cc088a87731d2a118a4fe5183831e4d1bd6cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763980"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Recuperar um dashboard eliminado no portal do Azure

Se estiver na nuvem Azure pública e apagar um dashboard _publicado_ no portal Azure, pode recuperar o painel de instrumentos no prazo de 14 dias após a eliminação. Se estiver numa nuvem do governo Azure ou o painel não for publicado, não poderá recuperá-lo, e deve reconstruí-lo. Para obter mais informações sobre a publicação de um dashboard, consulte [o painel de instrumentos publicar](azure-portal-dashboard-share-access.md#publish-dashboard). Siga estes passos para recuperar um dashboard publicado:

1. A partir do menu do portal Azure, selecione **grupos de Recursos,** em seguida, selecione o grupo de recursos onde publicou o dashboard (por padrão, os **seus dashboards nomeados).**

1. No **registo de atividade,** expanda a operação **Delete Dashboard.** Selecione o separador Alterar o **histórico** e, em seguida, selecione **\<deleted resource\>** .

    ![Screenshot do separador de histórico de mudança](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Selecione e copie o conteúdo do painel esquerdo e, em seguida, guarde para um ficheiro de texto com uma extensão de ficheiro _.json._ O portal utiliza o ficheiro JSON para recriar o painel de instrumentos.

    ![Screenshot da mudança história diff](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. A partir do menu do portal Azure, selecione **Dashboards**e, em seguida, selecione **Upload**.

    ![Screenshot do upload do painel de instrumentos](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Selecione o ficheiro JSON que guardou. O portal recria o painel de instrumentos com o mesmo nome e elementos que o painel de instrumentos apagado.

1. Selecione **Partilhar** para publicar o painel de instrumentos e restabelecer o controlo de acesso apropriado.

    ![Screenshot da partilha do painel de instrumentos](media/recover-shared-deleted-dashboard/dashboard-share.png)
