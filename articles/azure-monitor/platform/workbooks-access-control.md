---
title: Controlo de acesso a livros de trabalho do Azure Monitor
description: Simplificar relatórios complexos com livros de trabalho parametrizados pré-construídos e personalizados com controlo de acesso baseado em função
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658426"
---
# <a name="access-control"></a>Controlo de acesso

O controlo de acesso nos livros refere-se a duas coisas:

* Acesso necessário para ler dados num livro. Este acesso é controlado por [funções padrão Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) nos recursos utilizados no livro. Os livros de trabalho não especificam nem configuram o acesso a esses recursos. Normalmente, os utilizadores teriam este acesso a esses recursos utilizando o papel do [Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) nesses recursos.

* Acesso necessário para guardar livros

    - Salvar `("My")` livros privados não requer privilégios adicionais. Todos os utilizadores podem guardar livros privados, e só eles podem ver esses livros.
    - A poupança de livros partilhados requer privilégios de escrita num grupo de recursos para salvar o livro. Estes privilégios são geralmente especificados pela função [de Contribuinte de Monitorização,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) mas também podem ser *definidos* através da função de Contribuinte de Livros de Trabalho.
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Funções padrão com privilégios relacionados com livros

[O Leitor de Monitorização](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) inclui privilégios padrão/leitura que seriam usados por ferramentas de monitorização (incluindo livros) para ler dados a partir de recursos.

[O Colaborador de Monitorização](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) inclui `/write` privilégios gerais utilizados por várias ferramentas de monitorização para guardar itens (incluindo `workbooks/write` privilégio para guardar livros partilhados).
"Workbooks Contributor" adiciona privilégios de "livros/escrita" a um objeto para salvar livros partilhados.
Não são necessários privilégios especiais para que os utilizadores guardem livros privados que só eles possam ver.

Para o controlo de acesso personalizado baseado em funções:

Adicione `microsoft.insights/workbooks/write` para guardar livros partilhados. Para mais detalhes, consulte a função [de Contribuinte de Livro.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)

## <a name="next-steps"></a>Próximos passos

* [Começar a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções de visualizações ricas.
