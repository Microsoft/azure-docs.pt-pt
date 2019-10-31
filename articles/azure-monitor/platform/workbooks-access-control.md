---
title: Criar relatórios interativos com Azure Monitor pastas de trabalho com o controle de acesso baseado em função | Microsoft docs
description: Simplifique relatórios complexos com pastas de trabalho parametrizadas predefinidas e personalizadas com controle de acesso baseado em função
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: e2f1388d9823744d2382f1818ecb8dcb613895bc
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166190"
---
# <a name="access-control"></a>Controlo de acesso

O controle de acesso em pastas de trabalho refere-se a duas coisas:

* Acesso necessário para ler dados em uma pasta de trabalho. Esse acesso é controlado pelas [funções padrão do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) nos recursos usados na pasta de trabalho. As pastas de trabalho não especificam ou configuram o acesso a esses recursos. Normalmente, os usuários terão esse acesso a esses recursos usando a função [leitor de monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) nesses recursos.

* Acesso necessário para salvar pastas de trabalho

    - Salvar pastas de trabalho `("My")` particulares não requer privilégios adicionais. Todos os usuários podem salvar pastas de trabalho particulares e apenas podem ver essas pastas de trabalho.
    - Salvar pastas de trabalho compartilhadas requer privilégios de gravação em um grupo de recursos para salvar a pasta de trabalho. Esses privilégios geralmente são especificados pela função de [colaborador de monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) , mas também podem ser definidos por meio da função colaborador de pastas de *trabalho* .
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Funções padrão com privilégios relacionados à pasta de trabalho

O [leitor de monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) inclui privilégios de/Read padrão que seriam usados por ferramentas de monitoramento (incluindo pastas de trabalho) para ler dados de recursos.

O [colaborador de monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) inclui privilégios gerais de `/write` usados por várias ferramentas de monitoramento para salvar itens (incluindo o privilégio de `workbooks/write` para salvar pastas de trabalho compartilhadas).
"Colaborador de pastas de trabalho" adiciona privilégios de "pastas de trabalho/gravação" a um objeto para salvar pastas de trabalho compartilhadas.
Nenhum privilégio especial é necessário para que os usuários salvem pastas de trabalho particulares que só possam ver.

Para controle de acesso baseado em função personalizado:

Adicione `microsoft.insights/workbooks/write` para salvar pastas de trabalho compartilhadas. Para obter mais detalhes, consulte a função [colaborador da pasta de trabalho](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) .

## <a name="next-steps"></a>Passos seguintes

* [Comece a aprender mais](workbooks-visualizations.md) sobre pastas de trabalho muitas opções de visualizações ricas.
