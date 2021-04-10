---
title: Data Lake Analytics alterações recentes
description: Este artigo fornece uma lista contínua de alterações recentes que são feitas ao Data Lake Analytics.
author: xujiang1
ms.service: data-lake-analytics
ms.topic: overview
ms.author: xujiang1
ms.date: 07/31/2020
ms.openlocfilehash: cc3f9623f3b09c14e0e478252768890e4de3acad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025463"
---
# <a name="whats-new-in-data-lake-analytics"></a>Quais são as novidades no Data Lake Analytics?

O Azure Data Lake Analytics é atualizado numa base aperídica para determinados componentes. Para se manter atualizado com a mais recente atualização, este artigo fornece-lhe informações sobre:

- A notificação da pré-visualização beta do componente chave
- A importante informação da versão componente, por exemplo: a lista das versões disponíveis do componente, a versão padrão atual e assim por diante.


## <a name="notification-of-key-component-beta-preview"></a>Notificação da pré-visualização beta do componente chave

Não há versão beta de componente chave disponível para pré-visualização. 

## <a name="u-sql-runtime"></a>Tempo de execução U-SQL

O tempo de execução U-SQL do Azure Data Lake, incluindo o compilador, otimizador e gestor de emprego, é o que processa o seu código U-SQL.

Quando submeter o trabalho de análise do Azure Data Lake a partir de quaisquer ferramentas, o seu trabalho utilizará o tempo de funcionamento padrão atualmente disponível no ambiente de produção. 

A versão de tempo de execução será atualizada de forma aperiodicamente. E o tempo de execução anterior será mantido disponível por algum tempo. Quando uma nova versão Beta estiver pronta para pré-visualização, também estará disponível lá.

> [!CAUTION]
> - Escolher um tempo de execução diferente do padrão tem o potencial de quebrar os seus empregos U-SQL. É altamente recomendável não utilizar estas versões não padrão para produção, mas apenas para testes.
> - A versão de tempo de execução não padrão tem um ciclo de vida fixo. Será expirado automaticamente.

A versão a seguir é a versão atual padrão de tempo de execução.

- **release_20200707_scope_2b8d563_usql**

Para compreender como resolver falhas de tempo de execução U-SQL, consulte [falhas de tempo de execução U-SQL de resolução de problemas](runtime-troubleshoot.md).

## <a name="net-framework"></a>.NET Framework

O Azure Data Lake Analytics está agora a utilizar o **.NET Framework v4.7.2**. 

Se o seu código de script U-SQL Azure Data Lake Analytics utilizar conjuntos personalizados e os conjuntos personalizados utilizarem bibliotecas .NET, valide o seu código para verificar se existe alguma avaria.

Para compreender como resolver problemas uma atualização .NET utilizando [a atualização Troubleshoot a .NET](runtime-troubleshoot.md).

## <a name="release-note"></a>Nota de lançamento

Para obter detalhes recentes da atualização, consulte a nota de lançamento do [Azure Data Lake Analytics](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes).


## <a name="next-steps"></a>Passos seguintes

* Começar com data lake analytics usando [o portal](data-lake-analytics-get-started-portal.md)  |  [Azure PowerShell](data-lake-analytics-get-started-powershell.md)  |  [CLI](data-lake-analytics-get-started-cli.md)

