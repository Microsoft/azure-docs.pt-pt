---
title: Controlo de acesso a livros de trabalho do Azure Monitor
description: Simplificar relatórios complexos com livros de trabalho parametrizados pré-construídos e personalizados com controlo de acesso baseado em função
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: dd01b7e57441d4e5d763a07647ee27f1eef53ea9
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613801"
---
# <a name="access-control"></a>Controlo de acesso

O controlo de acesso nos livros refere-se a duas coisas:

* Acesso necessário para ler dados num livro. Este acesso é controlado por [funções padrão Azure](../../role-based-access-control/overview.md) nos recursos utilizados no livro. Os livros de trabalho não especificam nem configuram o acesso a esses recursos. Normalmente, os utilizadores teriam este acesso a esses recursos utilizando o papel do [Monitoring Reader](../../role-based-access-control/built-in-roles.md#monitoring-reader) nesses recursos.

* Acesso necessário para guardar livros

    - Salvar `("My")` livros privados não requer privilégios adicionais. Todos os utilizadores podem guardar livros privados, e só eles podem ver esses livros.
    - A poupança de livros partilhados requer privilégios de escrita num grupo de recursos para salvar o livro. Estes privilégios são geralmente especificados pela função [de Contribuinte de Monitorização,](../../role-based-access-control/built-in-roles.md#monitoring-contributor) mas também podem ser *definidos* através da função de Contribuinte de Livros de Trabalho.
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Funções padrão com privilégios relacionados com livros

[O Leitor de Monitorização](../../role-based-access-control/built-in-roles.md#monitoring-reader) inclui privilégios padrão/leitura que seriam usados por ferramentas de monitorização (incluindo livros) para ler dados a partir de recursos.

[O Colaborador de Monitorização](../../role-based-access-control/built-in-roles.md#monitoring-contributor) inclui `/write` privilégios gerais utilizados por várias ferramentas de monitorização para guardar itens (incluindo `workbooks/write` privilégio para guardar livros partilhados).
"Workbooks Contributor" adiciona privilégios de "livros/escrita" a um objeto para salvar livros partilhados.
Não são necessários privilégios especiais para que os utilizadores guardem livros privados que só eles possam ver.

Para funções personalizadas:

Adicione `microsoft.insights/workbooks/write` para guardar livros partilhados. Para mais detalhes, consulte a função [de Contribuinte de Livro.](../../role-based-access-control/built-in-roles.md#monitoring-contributor)

## <a name="next-steps"></a>Passos seguintes

* [Começar a](../platform/workbooks-overview.md#visualizations) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.