---
title: 'Quickstart: Ligando um espaço de trabalho power BI a um espaço de trabalho synapse'
description: Ligue um espaço de trabalho Power BI a um espaço de trabalho Azure Synapse Analytics seguindo os passos deste guia.
services: synapse-analytics
author: jocaplan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: business-intelligence
ms.date: 10/27/2020
ms.author: jocaplan
ms.reviewer: jrasnick
ms.openlocfilehash: 9c63e5e24495f373f288d2789780a6c671a7cc24
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218404"
---
# <a name="quickstart-linking-a-power-bi-workspace-to-a-synapse-workspace"></a>Quickstart: Ligando um espaço de trabalho power BI a um espaço de trabalho synapse

Neste quickstart, você aprenderá a ligar um espaço de trabalho Power BI a um espaço de trabalho Azure Synapse Analytics para criar novos relatórios e conjuntos de dados power BI a partir do Synapse Studio.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Criar um espaço de trabalho Azure Synapse e conta de armazenamento associada](quickstart-create-workspace.md)
- [Um espaço de trabalho Power BI Professional ou Premium](/power-bi/service-create-the-new-workspaces)

## <a name="link-power-bi-workspace-to-your-synapse-workspace"></a>Ligue o espaço de trabalho power BI ao seu espaço de trabalho synapse

1. A partir do Synapse Studio, clique em **Manage**.

    ![Synapse Studio clique para gerir.](media/quickstart-link-powerbi/synapse-studio-click-manage.png)

2. Em **Ligações Externas,** clique **nos serviços Linked**.

    ![Serviços ligados em destaque.](media/quickstart-link-powerbi/manage-click-linked-services.png)

3. Clique em **+ Novo**.

    ![+ Destaque-se nos novos serviços ligados.](media/quickstart-link-powerbi/new-highlighted.png)

4. Clique **em Power BI** e clique em **Continuar.**

    ![Serviço ligado à Power BI.](media/quickstart-link-powerbi/powerbi-linked-service.png)

5. Introduza um nome para o serviço ligado e selecione um espaço de trabalho da lista de dropdown.

    ![Apresentando a configuração do serviço ligado ao Power BI.](media/quickstart-link-powerbi/workspace-link-dialog.png)

6. Clique em **Criar**.

## <a name="view-power-bi-workspace-in-synapse-studio"></a>Ver espaço de trabalho power bi no Estúdio Synapse

Uma vez ligados os seus espaços de trabalho, pode navegar nos conjuntos de dados power BI, editar/criar novos Relatórios Power BI do Synapse Studio.

1. Clique **em Desenvolver.**

    ![Sinaapse Studio clique desenvolver.](media/quickstart-link-powerbi/synapse-studio-click-develop.png)

2. Expanda o Power BI e o espaço de trabalho que pretende utilizar.

    ![Expandir o Power BI e o espaço de trabalho.](media/quickstart-link-powerbi/develop-expand-powerbi.png)

Podem ser criados novos relatórios clicando **+** no topo do separador **Desenvolvimento.** Os relatórios existentes podem ser editados clicando no nome do relatório. Quaisquer alterações guardadas serão escritas de volta para o espaço de trabalho Power BI.

![Ver e editar relatório Power BI.](media/quickstart-link-powerbi/powerbi-report.png)


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a criação do relatório Power BI sobre ficheiros armazenados no Azure Storage](sql/tutorial-connect-power-bi-desktop.md).