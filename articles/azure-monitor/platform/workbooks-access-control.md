---
title: Controlo de acesso dos livros de trabalho azure Monitor
description: Simplificar relatórios complexos com livros pré-construídos e parametrizados personalizados com controlo de acesso baseado em funções
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658426"
---
# <a name="access-control"></a>Controlo de acesso

O controlo de acesso nos livros refere-se a duas coisas:

* Acesso necessário para ler dados num livro. Este acesso é controlado por funções padrão [do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) sobre os recursos utilizados no livro. Os livros de trabalho não especificam ou configuram o acesso a esses recursos. Normalmente, os utilizadores teriam este acesso a esses recursos utilizando o papel do [Monitor ingestão](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) de Leitores nesses recursos.

* Acesso necessário para salvar livros

    - Salvar `("My")` livros privados não requer privilégios adicionais. Todos os utilizadores podem guardar livros privados, e só eles podem ver esses livros.
    - Salvar livros partilhados requer privilégios de escrita num grupo de recursos para salvar o livro. Estes privilégios são geralmente especificados pela função [de Colaborador Monitorador,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) mas também podem ser definidos através da função de Colaborador de Livros de *Trabalho.*
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Funções padrão com privilégios relacionados com livro

[O Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) inclui privilégios padrão/leitura que seriam utilizados através da monitorização de ferramentas (incluindo livros de trabalho) para ler dados a partir de recursos.

[O Colaborador de Monitorização](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) inclui privilégios gerais `/write` utilizados `workbooks/write` por várias ferramentas de monitorização para guardar itens (incluindo privilégio sinuoso para salvar livros partilhados).
"Workbooks Contributor" adiciona privilégios de "livros/escrita" a um objeto para salvar livros partilhados.
Não são necessários privilégios especiais para que os utilizadores guardem livros privados que só eles possam ver.

Para um controlo de acesso baseado em funções personalizadas:

Adicione `microsoft.insights/workbooks/write` para guardar livros partilhados. Para mais detalhes, consulte o papel de Colaborador do [Livro.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)

## <a name="next-steps"></a>Passos seguintes

* [Começar a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
