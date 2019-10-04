---
title: Criar um índice de Azure Search no portal do Azure-Azure Search
description: Saiba como criar um índice para Azure Search usando um designer de índice do portal interno.
manager: nitinme
author: heidisteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: heidist
ms.openlocfilehash: 4abef5a3030643d4c7b91d2911f350190972f1eb
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937270"
---
# <a name="create-an-azure-search-index-in-the-portal"></a>Criar um índice de Azure Search no portal

O Azure Search inclui um designer de índice interno no portal útil para protótipos ou a criação de um [índice de pesquisa](search-what-is-an-index.md) hospedado em seu serviço de Azure Search. A ferramenta é usada para construção de esquema. Quando você salva a definição, um índice vazio se torna totalmente expresso em Azure Search. A forma como você o carrega com conteúdo pesquisável cabe a você.

O designer de índice é apenas uma abordagem para a criação de um índice. Como alternativa, você pode criar e carregar um índice usando o [Assistente de importação de dados](search-get-started-portal.md). O assistente só funciona com índices que ele cria. Programaticamente, você pode criar um índice usando as APIs do [.net](search-create-index-dotnet.md) ou [REST](search-create-index-rest-api.md) .

## <a name="start-index-designer"></a>Iniciar o designer de índice

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e abra o dashboard de serviço. Pode clicar em **Todos os serviços** na barra de atalhos para procurar os “serviços de pesquisa” existentes na subscrição atual. 

2. Clique no link **Adicionar índice** na barra de comandos na parte superior da página.

   ![Link Adicionar índice na barra de comandos](media/search-create-index-portal/add-index.png "Adicionar link de índice na barra de comandos")

3. Atribua um nome ao índice do Azure Search. Os nomes de índice são referenciados em operações de indexação e consulta. O nome do índice passa a fazer parte do URL do ponto final utilizado nas ligações ao índice e para enviar pedidos HTTP na API REST do Azure Search.

   * Comece com uma letra.
   * Utilize apenas letras minúsculas, números ou hífenes ("-").
   * Limite o nome a 60 carateres.

## <a name="add-fields"></a>Adicionar campos

A composição do índice inclui uma *coleção de Campos* que define os dados pesquisáveis no índice. Totalmente, a coleção Fields especifica a estrutura de documentos que você carrega separadamente. Uma coleção Fields inclui campos obrigatórios e opcionais, nomeados e tipados, com atributos de índice que determinam como o campo pode ser usado.

1. Adicione campos para especificar completamente os documentos que serão carregados, definindo um [tipo de dados](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para cada um. Por exemplo, se os documentos consistem em um *Hotel-ID*, *Hotel-Name*, *Address*, *City*e *Region*, crie um campo correspondente para cada um no índice. Examine as [diretrizes de design na seção abaixo](#design) para obter ajuda com a configuração de atributos.

1. Se os dados de entrada forem hierárquicos por natureza, o esquema deverá incluir [tipos complexos](search-howto-complex-data-types.md) para representar as estruturas aninhadas. O conjunto de dados de exemplo interno, hotéis, ilustra os tipos complexos usando um endereço (contém vários subcampos) que têm uma relação um-para-um com cada hotel e uma coleção complexa de salas, em que várias salas são associadas a cada hotel. 

1. Especifique um campo de *chave* do tipo EDM. String. Um campo de chave é obrigatório para todos os índices do Azure Search e tem de ser uma cadeia. Os valores para esse campo devem identificar exclusivamente cada documento. Por predefinição, o campo tem o nome *id*, mas pode alterá-lo, desde que a cadeia satisfaça as [regras de nomenclatura](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Por exemplo, se a coleção Fields incluir o *Hotel-ID*, você escolheria isso para sua chave. 

1. Defina atributos em cada campo. O designer de índice exclui todos os atributos que são inválidos para o tipo de dados, mas não sugere o que incluir. Revise as diretrizes na próxima seção para entender a finalidade dos atributos.

    A documentação da API do Azure Search inclui exemplos de código com um índice de *hotéis* simples. Na captura de tela abaixo, você pode ver a definição do índice, incluindo o analisador de idioma francês especificado durante a definição do índice, que você pode recriar como um exercício de prática no Portal.

    ![](media/search-create-index-portal/field-definitions.png "Índice") de demonstração de hotéis do índice de demonstração de hotéis

1. Quando terminar, clique em **criar** para salvar e criar o índice.

<a name="design"></a>

## <a name="set-attributes"></a>Definir atributos

Embora possa adicionar novos campos em qualquer altura, as definições de campos existentes estão bloqueadas durante o ciclo de vida do índice. Por este motivo, os programadores utilizam normalmente o portal para criar índices simples, testar ideias ou utilizar as páginas do portal para procurar uma definição. A iteração frequente através de uma estrutura de índice é mais eficiente se seguir uma abordagem baseada em código de modo a poder reconstruir o índice facilmente.

O analisadores e os sugestores estão associados a campos antes de o índice ser guardado. Certifique-se de adicionar analisadores de idioma ou sugestores à definição de índice enquanto você o estiver criando.

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

## <a name="next-steps"></a>Passos seguintes

Depois de criar um índice do Azure Search, pode avançar para o próximo passo: [carregar dados pesquisáveis para o índice](search-what-is-data-import.md).

Como alternativa, você também pode obter uma [análise mais profunda dos índices](search-what-is-an-index.md). Além da coleção de Campos, um índice também especifica analisadores, sugestores, perfis de classificação e definições CORS. O portal fornece páginas com guias para definir os elementos mais comuns: Campos, analisadores e sugestores. Para criar ou modificar outros elementos, pode utilizar a API REST ou o SDK .NET.

## <a name="see-also"></a>Consulte também

 [Como funciona a pesquisa em texto completo](search-lucene-query-architecture.md)  
 [API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/) [SDK .NET](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

