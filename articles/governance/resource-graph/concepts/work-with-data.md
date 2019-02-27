---
title: Trabalhar com grandes conjuntos de dados
description: Compreenda como obter e controlar grandes conjuntos de dados durante o trabalho com o gráfico de recursos do Azure.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/26/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: ef61314ae124668fc8970e6d68a0f927bdf771bc
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889040"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Trabalhar com conjuntos de dados de grandes recursos do Azure

Gráfico de recursos do Azure foi concebido para trabalhar com e obter informações sobre os recursos no ambiente do Azure. Gráfico de recursos faz obter estes dados rápidas e até mesmo ao consultar os milhares de registos. Gráfico de recursos tem várias opções para trabalhar com estes conjuntos de dados grandes.

## <a name="data-set-result-size"></a>Tamanho do conjunto de dados de resultado

Por predefinição, o gráfico de recursos limita qualquer consulta para devolver apenas **100** registos. Esse controle protege o utilizador e o serviço de consultas não intencionais, isso resultariam em grandes conjuntos de dados. Este evento ocorre, frequentemente, como um cliente está a experimentar consultas de localizar e filtrar recursos da forma que se adapta às suas necessidades específicas. Esse controle é diferente de utilizar o [top](/azure/kusto/query/topoperator) ou [limite](/azure/kusto/query/limitoperator) operadores de idioma do Explorador de dados do Azure para limitar os resultados.

> [!NOTE]
> Ao usar **primeira**, é recomendado para ordenar os resultados pelo menos uma coluna com `asc` ou `desc`. Sem classificação, os resultados retornados são aleatório e não passível de repetição.

O limite predefinido pode ser substituído por meio de todos os métodos de interação com o gráfico de recursos. Os exemplos seguintes mostram como alterar o limite de tamanho de conjunto de dados para _200_:

```azurecli-interactive
az graph query -q "project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -First 200
```

Na [REST API](/rest/api/azureresourcegraph/resources/resources), o controle é **$top** e faz parte da **QueryRequestOptions**.

O controle _mais restrito_ ganhará. Por exemplo, se a consulta utiliza a **superior** ou **limite** operadores e iria resultar em mais registos que **primeiro**, o máximo de registos devolvidos seria igual ao **Primeiro**. Da mesma forma, se **superior** ou **limite** é menor do que **primeiro**, o conjunto retornado de registos seria o menor valor configurado por **superior** ou **limite**.

**Primeira** tem um valor máximo permitido de atualmente _5000_.

## <a name="skipping-records"></a>A ignorar registos

A próxima opção para trabalhar com grandes conjuntos de dados é o **ignorar** controle. Esse controle permite que a sua consulta para passar ou ignore o número definido de registos antes de retornar os resultados. **Ignorar** é útil para consultas que ordenar os resultados de uma maneira significativa em que a intenção é chegar aos registos em algum lugar no meio do conjunto de resultados. Se os resultados necessários estão no final do conjunto de dados retornado, é mais eficiente para utilizar uma configuração de classificação diferentes e recuperar os resultados da parte superior do conjunto de dados em vez disso.

> [!NOTE]
> Ao usar **Skip**, é recomendado para ordenar os resultados pelo menos uma coluna com `asc` ou `desc`. Sem classificação, os resultados retornados são aleatório e não passível de repetição.

Os exemplos seguintes mostram como ignorar os primeiros _10_ registos iria resultar numa consulta, em vez disso, a partir de resultados devolvidos definido com o registo de 11:

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

Na [REST API](/rest/api/azureresourcegraph/resources/resources), o controle é **$skip** e faz parte da **QueryRequestOptions**.

## <a name="paging-results"></a>Resultados de paginação

Quando é necessário interromper um conjunto em conjuntos menores de registos para o processamento de resultados ou porque o conjunto de resultados iria exceder o valor máximo permitido de _5000_ devolveu registos, utilize a paginação. O [REST API](/rest/api/azureresourcegraph/resources/resources) **QueryResponse** fornece valores para indicar de um conjunto foi quebrado de resultados: **resultTruncated** e **$skipToken** .
**resultTruncated** é um valor booleano que informa o consumidor se existem registos adicionais não devolvido na resposta. Esta condição também pode ser identificado quando os **contagem** propriedade é menos do que o **totalRecords** propriedade. **totalRecords** define a quantidade de registos que correspondem à consulta.

Quando **resultTruncated** é **true**, o **$skipToken** propriedade está definida na resposta. Este valor é utilizado com os mesmos valores de consulta e uma subscrição para obter o próximo conjunto de registos que correspondem a consulta.

> [!IMPORTANT]
> A consulta tem **project** a **id** campo por ordem para a paginação trabalhar. Se estiver em falta da consulta, a resposta de REST API não incluir a **$skipToken**.

Por exemplo, veja [próxima consulta de página](/rest/api/azureresourcegraph/resources/resources#next_page_query) nos documentos de REST API.

## <a name="next-steps"></a>Passos Seguintes

- Consulte o idioma utilizado na [consultas Starter](../samples/starter.md)
- Consulte avançada usa no [avançadas consultas](../samples/advanced.md)
- Aprender a [explorar recursos](explore-resources.md)