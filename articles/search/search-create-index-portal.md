---
title: Criar um índice de pesquisa no portal Azure
titleSuffix: Azure Cognitive Search
description: Aprenda a criar um índice para a Pesquisa Cognitiva Azure usando um designer de índices de portal incorporado.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f2e875c625431867e6e83cfd1e0b2c6d7a2781f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74112840"
---
# <a name="create-an-azure-cognitive-search-index-in-the-portal"></a>Crie um índice de pesquisa cognitiva azure no portal

A Azure Cognitive Search inclui um designer de índices incorporado no portal útil para protótipos ou criação de um índice de [pesquisa](search-what-is-an-index.md) hospedado no seu serviço de Pesquisa Cognitiva Azure. A ferramenta é utilizada para a construção de esquemas. Quando se salva a definição, um índice vazio torna-se totalmente expresso na Pesquisa Cognitiva Azure. A forma como o carrega com conteúdo pesquisável depende de si.

O designer de índices é apenas uma abordagem para criar um índice. Em alternativa, pode criar e carregar um índice utilizando o assistente de [dados da Importação](search-get-started-portal.md). O feiticeiro só trabalha com índices que se cria. Programáticamente, pode criar um índice utilizando as APIs [.NET](search-create-index-dotnet.md) ou [REST.](search-create-index-rest-api.md)

## <a name="start-index-designer"></a>Iniciar designer de índices

1. Inicie sessão no [portal do Azure](https://portal.azure.com) e abra o dashboard de serviço. Pode clicar em **Todos os serviços** na barra de atalhos para procurar os “serviços de pesquisa” existentes na subscrição atual. 

2. Clique no link **de índice Adicionar** na barra de comando na parte superior da página.

   ![Adicionar ligação de índice na barra de comando](media/search-create-index-portal/add-index.png "Adicionar ligação de índice na barra de comando")

3. Nomeie o seu índice de pesquisa cognitiva Azure. Os nomes indexados são referenciados em operações de indexação e consulta. O nome do índice torna-se parte do URL de ponto final utilizado nas ligações ao índice e para o envio de pedidos HTTP na API de PESQUISA Cognitiva Azure.

   * Comece com uma letra.
   * Utilize apenas letras minúsculas, números ou hífenes ("-").
   * Limite o nome a 60 carateres.

## <a name="add-fields"></a>Adicionar campos

A composição do índice inclui uma *coleção de Campos* que define os dados pesquisáveis no índice. No total, a coleção de campos especifica a estrutura dos documentos que envia separadamente. Uma coleção Fields inclui campos necessários e opcionais, nomeados e digitados, com atributos de índice que determinam como o campo pode ser usado.

1. Adicione campos para especificar totalmente os documentos que irá enviar, definindo um tipo de [dados](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para cada um. Por exemplo, se os documentos consistem num *hotel-id,* *nome do hotel,* *endereço,* *cidade,* cidade e *região,* criar um campo correspondente para cada um no índice. Reveja a [orientação](#design) de design na secção abaixo para obter ajuda com adefinição de atributos.

1. Se os dados de entrada forem de natureza hierárquica, o seu esquema deve incluir [tipos complexos](search-howto-complex-data-types.md) para representar as estruturas aninhadas. O conjunto de dados de amostras incorporados, Hotéis, ilustra tipos complexos usando um Endereço (contém vários sub-campos) que tem uma relação um-a-um com cada hotel, e uma coleção complexa de quartos, onde vários quartos estão associados a cada hotel. 

1. Especifique um campo *chave* do tipo Edm.String. Um campo-chave é obrigatório para cada índice de Pesquisa Cognitiva Azure e deve ser uma corda. Os valores para este campo devem identificar de forma única cada documento. Por predefinição, o campo tem o nome *id*, mas pode alterá-lo, desde que a cadeia satisfaça as [regras de nomenclatura](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Por exemplo, se a sua coleção de campos inclui *hotel-id,* você escolheria isso para a sua chave. 

1. Definir atributos em cada campo. O designer de índices exclui quaisquer atributos que sejam inválidos para o tipo de dados, mas não sugere o que incluir. Reveja a orientação na secção seguinte para entender para que são os atributos.

    A documentação da API de Pesquisa Cognitiva Azure inclui exemplos de código com um simples índice de *hotéis.* Na imagem abaixo, pode ver a definição de índice, incluindo o analisador de língua francesa especificado durante a definição de índice, que pode recriar como um exercício de prática no portal.

    ![Índice de demonstração de hotéis](media/search-create-index-portal/field-definitions.png "Índice de demonstração de hotéis")

1. Quando terminar, clique **em Criar** para salvar e criar o índice.

<a name="design"></a>

## <a name="set-attributes"></a>Definir atributos

Embora possa adicionar novos campos em qualquer altura, as definições de campos existentes estão bloqueadas durante o ciclo de vida do índice. Por este motivo, os programadores utilizam normalmente o portal para criar índices simples, testar ideias ou utilizar as páginas do portal para procurar uma definição. A iteração frequente através de uma estrutura de índice é mais eficiente se seguir uma abordagem baseada em código de modo a poder reconstruir o índice facilmente.

O analisadores e os sugestores estão associados a campos antes de o índice ser guardado. Certifique-se de adicionar analisadores de linguagem ou sugestionantes à sua definição de índice enquanto está a criá-lo.

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

Depois de criar um índice de Pesquisa Cognitiva Azure, pode passar para o próximo passo: [carregar dados pesquisáveis para o índice](search-what-is-data-import.md).

Em alternativa, também pode [olhar mais profundamente para os índices.](search-what-is-an-index.md) Além da coleção de Campos, um índice também especifica analisadores, sugestores, perfis de classificação e definições CORS. O portal fornece páginas com separadores para definir os elementos mais comuns: campos, analisadores e sugestores. Para criar ou modificar outros elementos, pode utilizar a API REST ou o SDK .NET.

## <a name="see-also"></a>Consulte também

 [Como funciona a pesquisa em texto completo](search-lucene-query-architecture.md)  
 [API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/) [SDK .NET](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

