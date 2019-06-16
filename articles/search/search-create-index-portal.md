---
title: Criar um índice da Azure Search no portal do Azure - Azure Search
description: Saiba como criar um índice para o Azure Search com designers um índice de portal internos.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6a2bac71c37cc750eb24e3492ecdcdf0b2333cce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60817300"
---
# <a name="create-an-azure-search-index-in-the-portal"></a>Criar um índice da Azure Search no portal

O Azure Search inclui um índice internos designer no portal do útil para protótipos ou criar um [índice de pesquisa](search-what-is-an-index.md) alojados no seu serviço Azure Search. A ferramenta é utilizada para a construção de esquema. Ao guardar a definição, um índice vazio torna-se totalmente expresso no Azure Search. Como carregá-lo com dados pesquisáveis cabe a.

O estruturador de índice é apenas uma abordagem para criar um índice. Por meio de programação, pode criar um índice com o [.NET](search-create-index-dotnet.md) ou [REST](search-create-index-rest-api.md) APIs.

## <a name="start-index-designer"></a>Estruturador de índice de início

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e abra o dashboard de serviço. Pode clicar em **Todos os serviços** na barra de atalhos para procurar os “serviços de pesquisa” existentes na subscrição atual. 

2. Clique nas **Adicionar índice** ligação na barra de comandos na parte superior da página.

   ![Adicionar ligação de índice na barra de comandos](media/search-create-index-portal/add-index.png "adicionar ligação de índice na barra de comandos")

3. Atribua um nome ao índice do Azure Search. Nomes de índice são referenciados nas operações de indexação e consulta. O nome do índice passa a fazer parte do URL do ponto final utilizado nas ligações ao índice e para enviar pedidos HTTP na API REST do Azure Search.

   * Comece com uma letra.
   * Utilize apenas letras minúsculas, números ou hífenes ("-").
   * Limite o nome a 60 carateres.

## <a name="add-fields"></a>Adicione campos

A composição do índice inclui uma *coleção de Campos* que define os dados pesquisáveis no índice. Em conjunto, a coleção de campos Especifica a estrutura de documentos que carrega separadamente. Uma coleção de campos inclui campos obrigatórios e opcionais, com o nome e escritos, com atributos de índice que determinam como o campo pode ser utilizado.

1. Adicione campos para especificar completamente os documentos que irá carregar, definindo um [tipo de dados](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para cada um deles. Por exemplo, se os documentos são compostas por um *id de hotel*, *nome de hotel*, *endereço*, *Cidade*, e *região*, crie um campo correspondente para cada um no índice. Reveja os [documentação de orientação na secção abaixo](#design) para obter ajuda com a definição de atributos.

2. Especifique um *chave* campo do tipo EDM. Valores para este campo tem de identificar exclusivamente cada documento. Por predefinição, o campo tem o nome *id*, mas pode alterá-lo, desde que a cadeia satisfaça as [regras de nomenclatura](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Por exemplo, se a sua coleção de campos inclui *id de hotel*, deve escolher que para a sua chave. Um campo de chave é obrigatório para todos os índices do Azure Search e tem de ser uma cadeia.

3. Definir atributos em cada campo. O estruturador de índice exclui todos os atributos que são inválidos para o tipo de dados, mas não sugere o que pretende incluir. Reveja a secção seguinte para compreender quais são os atributos para a documentação de orientação.

    A documentação da API do Azure Search inclui exemplos de código com um índice de *hotéis* simples. A captura de ecrã abaixo, pode ver a definição de índice, incluindo o analisador de idioma francês especificado durante a definição do índice, que pode recriar como um exercício prático no portal.

    ![Índice de demonstração de hotéis](media/search-create-index-portal/field-definitions.png "índice de demonstração de hotéis")

4. Quando terminar, clique em **criar** para guardar e criar o índice.

<a name="design"></a>

## <a name="set-attributes"></a>Conjunto de atributos

Embora possa adicionar novos campos em qualquer altura, as definições de campos existentes estão bloqueadas durante o ciclo de vida do índice. Por este motivo, os programadores utilizam normalmente o portal para criar índices simples, testar ideias ou utilizar as páginas do portal para procurar uma definição. A iteração frequente através de uma estrutura de índice é mais eficiente se seguir uma abordagem baseada em código de modo a poder reconstruir o índice facilmente.

O analisadores e os sugestores estão associados a campos antes de o índice ser guardado. Certifique-se de que adicionar sugestores ou analisadores de idioma à definição do índice enquanto estiver a criar.

Os campos de cadeia são frequentemente marcados como **Pesquisáveis** e **Recuperáveis**. Os campos utilizados para restringir os resultados da pesquisa incluem as definições **Ordenável**, **Filtrável** e **Facetável**.

Os atributos de campo determinam como um campo é utilizado, por exemplo, se é utilizado na pesquisa em texto completo, navegação por facetas, operações de ordenação e assim sucessivamente. A tabela seguinte descreve cada atributo.

|Atributo|Descrição|  
|---------------|-----------------|  
|**pesquisável**|Texto completo pesquisável, sujeito a análise lexical, como separação de palavras durante a indexação. Se definir um campo pesquisável para um valor como "sunny day", será dividido internamente nos tokens individuais "sunny" e "day". Para obter detalhes, veja [Como funciona a pesquisa em texto completo](search-lucene-query-architecture.md).|  
|**filtrável**|Referenciado na consulta **$filter**. Os campos filtráveis do tipo `Edm.String` ou `Collection(Edm.String)` não são submetidos a separação de palavras, pelo que as comparações destinam-se apenas a correspondências exatas. Por exemplo, se definir um campo f para "sunny day", `$filter=f eq 'sunny'` não encontrará correspondências, mas `$filter=f eq 'sunny day'` sim. |  
|**ordenável**|Por predefinição, o sistema ordena os resultados por classificação, mas pode configurar a ordenação com base nos campos nos documentos. Os campos do tipo `Collection(Edm.String)` não podem ser **ordenáveis**. |  
|**facetável**|Normalmente, é utilizado numa apresentação de resultados de pesquisa que inclui uma contagem de resultados por categoria (por exemplo, hotéis numa cidade específica). Esta opção não pode ser utilizada com campos do tipo `Edm.GeographyPoint`. Os campos do tipo `Edm.String` que sejam **filtráveis**, **ordenáveis** ou **facetáveis** podem ter um máximo de 32 kilobytes. Para mais detalhes, veja [Criar um Índice (API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**chave**|Identificador exclusivo de documentos no índice. Deve ser selecionado exatamente um campo como o campo de chave e tem de ser do tipo `Edm.String`.|  
|**recuperável**|Determina se o campo pode ser devolvido num resultado da pesquisa. Isto é útil quando quiser utilizar um campo (como *margem de lucro*) como mecanismo de filtro, ordenação ou classificação, mas não quer que o campo esteja visível para o utilizador final. Este atributo tem de ser `true` para campos `key`.|  

## <a name="next-steps"></a>Passos Seguintes

Depois de criar um índice do Azure Search, pode avançar para o próximo passo: [carregar dados pesquisáveis para o índice](search-what-is-data-import.md).

Em alternativa, também pode demorar um [mais detalhadamente o índices](search-what-is-an-index.md). Além da coleção de Campos, um índice também especifica analisadores, sugestores, perfis de classificação e definições CORS. O portal fornece páginas com separadores para definir os elementos mais comuns: Campos, analisadores e sugestores. Para criar ou modificar outros elementos, pode utilizar a API REST ou o SDK .NET.

## <a name="see-also"></a>Consulte também

 [Como funciona a pesquisa em texto completo](search-lucene-query-architecture.md)  
 [API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/) [SDK .NET](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

