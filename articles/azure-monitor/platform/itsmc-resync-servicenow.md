---
title: Como corrigir manualmente problemas de sincronização do ServiceNow
description: Reinicie a ligação ao ServiceNow para que os alertas no Microsoft Azure possam voltar a ligar para o ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: a3382f93990612b0ab34eb0848cbf3d6577c44ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87087939"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>Como corrigir manualmente problemas de sincronização do ServiceNow

O Azure Monitor pode ligar-se a fornecedores de gestão de serviços de TI de terceiros (ITSM). O ServiceNow é um desses fornecedores.

Por razões de segurança, poderá necessitar de atualizar o token de autenticação utilizado para a sua ligação com o ServiceNow.
Utilize o seguinte processo de sincronização para reativar a ligação e refrescar o token:


1. Procure a solução no banner de pesquisa superior e, em seguida, selecione as soluções relevantes

    ![Nova ligação](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. No ecrã da solução, escolha "Select All" no filtro de subscrição e, em seguida, filtre por "ServiceDesk"

    ![Nova ligação](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Selecione a solução da sua ligação ITSM.
1. Selecione a ligação ITSM no banner esquerdo.

    ![Nova ligação](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Selecione cada conector da lista. 
    1. Clique no nome do Conector para o configurar
    1. Eliminar quaisquer conectores que já não sejam utilizados

    1. Atualizar os campos de acordo com [estas definições](./itsmc-connections.md) com base no tipo de parceiro

    1. Clique em sincronização

       ![Nova ligação](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Clique em guardar

        ![Nova ligação](media/itsmc-resync-servicenow/save-8bit.png)

f.    Reveja as notificações para ver se o processo terminou com sucesso 

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [conexões de gestão de serviços de TI](itsmc-connections.md)
