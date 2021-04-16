---
title: Eliminar conectores ITSM não reutilizados
description: Este artigo fornece uma explicação de como eliminar os conectores ITSM e os grupos de ação que lhe estão associados.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 3dc84ea6def9b762527226dbeb3e2eaab78ec200
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387943"
---
# <a name="delete-unused-itsm-connectors"></a>Eliminar conectores ITSM não reutilizados

O processo de eliminação dos conectores de serviço de TI não usused (ITSM) tem duas fases. Elimina todas as ações associadas a um conector ITSM e, em seguida, apaga o próprio conector. Primeiro elimina as ações porque as ações sem um conector podem causar erros na sua subscrição.

## <a name="delete-associated-actions"></a>Eliminar ações associadas

1. No portal Azure, selecione **Monitor**.
  
    ![Screenshot da seleção monitor.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Selecione **Alertas**.
   
    ![Screenshot da seleção alertas.](media/itsmc-connector-deletion/itsmc-alert-selection.png)

3. Selecione **Gestão de Ações**.
   
    ![Screenshot da seleção de Manage Actions.](media/itsmc-connector-deletion/itsmc-actions-selection.png)

4. Selecione um grupo de ação associado ao conector ITSM que pretende eliminar. Este artigo usa o exemplo de um conector Cherwell.
   
    ![Screenshot de ações que estão associadas ao conector Cherwell.](media/itsmc-connector-deletion/itsmc-actions-screen.png)

5. Reveja as informações e, em seguida, **selecione Delete action group**.

    ![Screenshot da informação do grupo de ação e o botão para apagar o grupo.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="delete-the-connector"></a>Eliminar o conector

1. Na barra de pesquisa, procure **por um serviço de assistência.** Em seguida, selecione **ServiceDesk** na lista de recursos.

    ![Screenshot de pesquisa e seleção de ServiceDesk.](media/itsmc-connector-deletion/itsmc-connector-selection.png)

2. Selecione **as ligações ITSM** e, em seguida, selecione o conector Cherwell.

    ![Screenshot do conector Cherwell I T S M.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)

3. Selecione **Eliminar**.

    ![Screenshot do botão de exclusão para o conector I T S M.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Passos seguintes

* [Problemas de resolução de problemas num conector ITSM](./itsmc-resync-servicenow.md)
