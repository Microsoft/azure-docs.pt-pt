---
title: Supressão do conector ITSM e da ação que lhe está associada
description: Este artigo fornece uma explicação de como eliminar o conector ITSM e os grupos de ação que lhe estão associados.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 1fbaa48d104642984e604bc66a3aa914fbfed44c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530973"
---
# <a name="deletion-of-unused-itsm-connectors"></a>Supressão de conectores ITSM não reutilizados

O processo de supressão do conector não reutilizado contém 2 fases:

1. Supressão das ações associadas: todas as ações associadas ao conector ITSM devem ser eliminadas. Isto deve ser feito para não ter ações sem conector que possam causar erros na sua subscrição.

2. Supressão do conector ITSM não reutilizado.

## <a name="deletion-of-the-associated-actions"></a>Supressão das ações associadas

1. Para encontrar o grupo de ação, deve entrar no "Monitor"  ![ Screenshot da seleção do monitor.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Selecione "Alerts"  ![ Screenshot da seleção de alertas.](media/itsmc-connector-deletion/itsmc-alert-selection.png)
3. Selecione "Gerir ações"  ![ Screenshot da seleção de ações de gestão.](media/itsmc-connector-deletion/itsmc-actions-selection.png)
4. Selecione todos os conectores ITSM que estão ligados ao Cherwell  ![ Screenshot dos conectores ITSM que estão ligados a Cherwell.](media/itsmc-connector-deletion/itsmc-actions-screen.png)
5. Eliminar o grupo de ação  ![ Screenshot da eliminação do grupo de ação.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="deletion-of-the-unused-itsm-connector"></a>Supressão do conector ITSM não reutilizado

1. Deve pesquisar e selecionar "ServiceDesk" LA na barra de pesquisa de topo  ![ Screenshot de pesquisa e selecione "ServiceDesk" LA.](media/itsmc-connector-deletion/itsmc-connector-selection.png)
2. Selecione as "Ligações ITSM" e selecione o conector Cherwell  ![ Screenshot dos conectores Cherwell ITSM.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)
3. Selecione "Delete"  ![ Screenshot da eliminação do conector ITSM.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas do Conector do ITSM](./itsmc-resync-servicenow.md)
