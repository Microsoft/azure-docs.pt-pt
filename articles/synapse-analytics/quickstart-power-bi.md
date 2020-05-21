---
title: Quickstart - Ligar um espaço de trabalho power BI a um espaço de trabalho sinapse
description: Ligue um espaço de trabalho power BI a um espaço de trabalho Azure Synapse Analytics seguindo os passos deste guia.
services: synapse-analytics
author: jocaplan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/29/2020
ms.author: jocaplan
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: c5b86319752fc70e754e34b787bbdc12f18636f0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655975"
---
# <a name="quickstart-linking-a-power-bi-workspace-to-a-synapse-analytics-workspace-preview"></a>Quickstart: Ligar um espaço de trabalho power BI a um espaço de trabalho Synapse Analytics (pré-visualização)

Neste arranque rápido, aprenderá a ligar um espaço de trabalho power BI a um espaço de trabalho Synapse Analytics para criar novos relatórios e conjuntos de dados power BI do Estúdio Synapse (pré-visualização).

Se não tiver uma subscrição Azure, [crie uma conta gratuita antes](https://azure.microsoft.com/free/)de começar .

## <a name="prerequisites"></a>Pré-requisitos

- [Criar um espaço de trabalho Azure Synapse e uma conta de armazenamento associada](quickstart-create-workspace.md)
- [Um espaço de trabalho power BI Profissional ou Premium](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces)

## <a name="link-power-bi-workspace-to-your-synapse-workspace"></a>Link Power BI espaço de trabalho para o seu espaço de trabalho Synapse

1. A partir do Estúdio Synapse, clique em **Gerir**.

    ![Synapse Studio clique em gerir.](media/quickstart-link-powerbi/synapse-studio-click-manage.png)

2. Em **Conexões Externas,** clique em **serviços Linked**.

    ![Serviços ligados em destaque.](media/quickstart-link-powerbi/manage-click-linked-services.png)

3. Clique em **+ Novo**.

    ![+ Novos serviços ligados estão em destaque.](media/quickstart-link-powerbi/new-highlighted.png)

4. Clique em **Power BI** e clique **em Continuar**.

    ![Exibindo o serviço ligado ao Power BI.](media/quickstart-link-powerbi/powerbi-linked-service.png)

5. Introduza um nome para o serviço ligado e selecione um espaço de trabalho da lista de dropdown.

    ![Exibindo a configuração do serviço ligado ao Power BI.](media/quickstart-link-powerbi/workspace-link-dialog.png)

6. Clique em **Criar**.

## <a name="view-power-bi-workspace-in-synapse-studio"></a>Ver espaço de trabalho power BI no Estúdio Synapse

Uma vez que os seus espaços de trabalho estejam ligados, pode navegar nos seus conjuntos de dados Power BI, editar/criar novos Relatórios power BI do Estúdio Synapse.

1. Clique em **Desenvolver**.

    ![Synapse Studio clique em desenvolver.](media/quickstart-link-powerbi/synapse-studio-click-develop.png)

2. Expanda o Power BI e o espaço de trabalho que deseja utilizar.

    ![Expanda o Power BI e o espaço de trabalho.](media/quickstart-link-powerbi/develop-expand-powerbi.png)

Novos relatórios podem ser criados clicando **+** na parte superior do separador **Desenvolver.** Os relatórios existentes podem ser editados clicando no nome do relatório. Quaisquer alterações guardadas serão redigidas para o espaço de trabalho power BI.

![Ver e editar relatório Power BI.](media/quickstart-link-powerbi/powerbi-report.png)


## <a name="next-steps"></a>Próximos passos

Saiba mais sobre a criação de [relatório power BI sobre ficheiros armazenados no Armazenamento Azure](sql/tutorial-connect-power-bi-desktop.md).
