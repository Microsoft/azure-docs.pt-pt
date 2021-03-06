---
title: Como corrigir manualmente problemas de sincronização do ServiceNow
description: Reinicie a ligação ao ServiceNow para que os alertas no Microsoft Azure possam voltar a ligar para o ServiceNow
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/17/2021
ms.openlocfilehash: 664f1522775d96b813b7cd99bdffdb26630497f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037443"
---
# <a name="how-to-manually-fix-sync-problems"></a>Como corrigir manualmente problemas de sincronização

O Azure Monitor pode ligar-se a fornecedores de gestão de serviços de TI de terceiros (ITSM). O ServiceNow é um desses fornecedores.

Por razões de segurança, poderá necessitar de atualizar o token de autenticação utilizado para a sua ligação com o ServiceNow.
Utilize o seguinte processo de sincronização para reativar a ligação e refrescar o token:

1. Procure a solução no banner de pesquisa superior e, em seguida, selecione as soluções relevantes

    ![Screenshot que mostra o banner de pesquisa superior e onde selecionar as soluções relevantes.](media/itsmc-resync-servicenow/solution-search-8-bit.png)

1. No ecrã da solução, escolha "Select All" no filtro de subscrição e, em seguida, filtre por "ServiceDesk"

    ![Screenshot que mostra onde escolher Select All e onde filtrar por ServiceDesk.](media/itsmc-resync-servicenow/solutions-list-8-bit.png)

1. Selecione a solução da sua ligação ITSM.
1. Selecione a ligação ITSM no banner esquerdo.

    ![Screenshot que mostra onde selecionar ligações ITSM.](media/itsmc-resync-servicenow/itsm-connector-8-bit.png)

1. Selecione cada conector da lista. 
    1. Clique no nome do Conector para o configurar
    1. Eliminar quaisquer conectores que já não sejam utilizados

    1. Atualizar os campos de acordo com [estas definições](./itsmc-connections.md) com base no tipo de parceiro

    1. Clique em sincronização

       ![Screenshot que realça o botão Sync.](media/itsmc-resync-servicenow/resync-8-bit-2.png)

    1. Clique em guardar

        ![Nova ligação](media/itsmc-resync-servicenow/save-8-bit.png)

f.    Reveja as notificações para ver se o processo começou.
