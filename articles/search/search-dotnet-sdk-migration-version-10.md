---
title: Upgrade para Azure Cognitive Search .NET SDK versão 10
titleSuffix: Azure Cognitive Search
description: Migrar código para a versão 10 da Azure Cognitive Search .NET SDK a partir de versões mais antigas. Saiba o que é novo e quais as alterações de código que são necessárias.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ad912eb0b26354d40a654a1c8782dfcb960235e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73847511"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Upgrade para Azure Cognitive Search .NET SDK versão 10

Se estiver a utilizar a versão 9.0 ou mais antiga do [Azure Search .NET SDK,](https://aka.ms/search-sdk)este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 10.

O Azure Search é renomeado para Azure Cognitive Search na versão 10, mas os espaços de nome e os nomes dos pacotes não mudam. As versões anteriores do SDK (9.0 e anterior) continuam a usar o nome anterior. Para obter mais informações sobre a utilização do SDK, incluindo exemplos, consulte [Como utilizar a Pesquisa Cognitiva Azure a partir de uma aplicação .NET](search-howto-dotnet-sdk.md).

A versão 10 adiciona várias funcionalidades e correções de bugs, elevando-a `2019-05-06`ao mesmo nível funcional que o lançamento mais recente da versão REST API . Nos casos em que uma mudança infringe o código existente, vamos acompanhá-lo através dos [passos necessários para resolver o problema.](#UpgradeSteps)

> [!NOTE]
> Se estiver a utilizar a versão 8.0-preview ou mais antiga, deve fazer o upgrade para a versão 9 primeiro e, em seguida, atualizar para a versão 10. Consulte o Upgrade para a versão 9 do [Azure Search .NET SDK](search-dotnet-sdk-migration-version-9.md) para obter instruções.
>
> A sua instância de serviço de pesquisa suporta várias versões REST API, incluindo a mais recente. Pode continuar a utilizar uma versão quando já não é a mais recente, mas recomendamos que emigra o seu código para utilizar a versão mais recente. Ao utilizar a API REST, deve especificar a versão API em todos os pedidos através do parâmetro da versão api. Ao utilizar o .NET SDK, a versão do SDK que está a utilizar determina a versão correspondente da API REST. Se estiver a utilizar um SDK mais antigo, pode continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para suportar uma versão API mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>O que há de novo na versão 10
A versão 10 do Azure Cognitive Search .NET SDK tem como alvo`2019-05-06`a mais recente versão geralmente disponível da Rest API ( ) com estas atualizações:

* Introdução de duas novas competências - [competência condicional](cognitive-search-skill-conditional.md) e competência de [tradução de texto.](cognitive-search-skill-text-translation.md)
* [As](cognitive-search-skill-shaper.md) inputs de habilidade sinuosa foram reestruturadas para acomodar a consolidação a partir de contextos aninhados. Para mais informações, consulte este exemplo a [definição JSON](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts).
* Adição de duas novas funções de mapeamento de [campo:](search-indexer-field-mappings.md)
    - [urlEncode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* Em certas ocasiões, erros e avisos que aparecem no estado de [execução do indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) podem ter detalhes adicionais que ajudam na depuração. `IndexerExecutionResult`foi atualizado para refletir este comportamento.
* As competências individuais definidas dentro de um `name` [skillset](cognitive-search-defining-skillset.md) podem ser identificadas opcionalmente especificando uma propriedade.
* `ServiceLimits`mostra limites para `IndexerExecutionInfo` [tipos complexos](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) e mostra limites/quotas pertinentes do indexante.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar

1. Atualize a sua `Microsoft.Azure.Search` referência NuGet para utilizar a Consola nuGet Package Manager ou clicando corretamente nas referências do seu projeto e selecionando "Gerir pacotes NuGet..." no Estúdio Visual.

2. Assim que o NuGet tiver descarregado os novos pacotes e as suas dependências, reconstrua o seu projeto. 

3. Se a sua construção falhar, terá de corrigir cada erro de construção. Consulte [as alterações de Rutura na versão 10](#ListOfChanges) para obter detalhes sobre como resolver cada erro de construção potencial.

4. Depois de corrigir quaisquer erros de construção ou avisos, pode fazer alterações na sua aplicação para tirar partido de novas funcionalidades, se desejar. As novas funcionalidades no SDK são detalhadas no [What's new na versão 10](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>Quebrar alterações na versão 10

Existem várias alterações de quebra na versão 10 que podem exigir alterações de código para além de reconstruir a sua aplicação.

> [!NOTE]
> A lista de alterações abaixo não é exaustiva. Algumas mudanças provavelmente não resultarão em erros de construção, mas estão tecnicamente a quebrar uma vez que quebram a compatibilidade binária com conjuntos que dependem de versões anteriores dos conjuntos Azure Cognitive Search .NET SDK. Alterações significativas que se enquadram nesta categoria também são enumeradas, juntamente com recomendações. Por favor, reconstrua a sua aplicação ao atualizar para a versão 10 para evitar quaisquer problemas de compatibilidade binários.

### <a name="custom-web-api-skill-definition"></a>Definição de habilidade api da Web personalizada

A definição da habilidade Custom [Web API](cognitive-search-custom-skill-web-api.md) foi especificada incorretamente na versão 9 ou mais antiga. 

O modelo `WebApiSkill` especificado `HttpHeaders` como uma propriedade de objeto que _contém_ um dicionário. A criação de `WebApiSkill` uma competência com uma construção desta forma resultaria numa exceção, porque a API do REST consideraria o pedido mal formado. Esta questão foi corrigida, `HttpHeaders` fazendo **uma propriedade de dicionário de alto nível** sobre o `WebApiSkill` próprio modelo - que é considerado um pedido válido da API REST.

Por exemplo, se tentou instantaneamente um `WebApiSkill` seguinte:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

altere-o para o seguinte, para evitar o erro de validação da API REST:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>A habilidade do shaper permite a consolidação do contexto aninhada

A habilidade do shaper pode agora permitir a consolidação da entrada a partir de contextos aninhados. Para permitir esta alteração, `InputFieldMappingEntry` modificámos para que possa ser instantaneamente `Source` instantaneamente especificando apenas uma propriedade, ou tanto a `SourceContext` e `Inputs` as propriedades.

É provável que não precise de fazer alterações de código; no entanto, note que apenas uma destas duas combinações é permitida. Isto significa:

- A `InputFieldMappingEntry` criação `Source` de um local onde só é inicializada é válida.
- Criar `InputFieldMappingEntry` um `SourceContext` local `Inputs` apenas e ser inicializado é válido.
- Todas as outras combinações envolvendo estas três propriedades são inválidas.

Se decidir começar a utilizar esta nova capacidade, certifique-se de que todos os seus clientes são atualizados para utilizar a versão 10 primeiro, antes de lançar essa alteração. Caso contrário, existe a possibilidade de que uma atualização de um cliente (utilizando uma versão mais antiga do SDK) para a habilidade Shaper possa resultar em erros de validação.

> [!NOTE]
> Embora o `InputFieldMappingEntry` modelo subjacente tenha sido modificado para permitir a consolidação a partir de contextos aninhados, o seu uso só é válido dentro da definição de uma habilidade Shaper. A utilização desta capacidade noutras competências, embora válida no momento da compilação, resultará num erro de validação no prazo de execução.

## <a name="skills-can-be-identified-by-a-name"></a>As competências podem ser identificadas por um nome

Cada habilidade dentro de um skillset agora tem uma nova propriedade, `Name`que pode ser inicializada no seu código para ajudar a identificar a habilidade. Isto é opcional - quando não especificado (que é o padrão, se não foi feita nenhuma alteração explícita de código), é-lhe atribuído um nome predefinido utilizando o índice 1 baseado na habilidade no skillset, pré-fixado com o caracteres '#'. Por exemplo, na seguinte definição de skillset (a maioria das inicializações ignoradas para a brevidade):

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

`SentimentSkill`é atribuído um `#1` `WebApiSkill` nome , `#2` `ShaperSkill` é atribuído `#3` , é atribuído e assim por diante.

Se optar por identificar as competências por um nome personalizado, certifique-se de atualizar todos os casos dos seus clientes para a versão 10 do SDK primeiro. Caso contrário, existe a possibilidade de um cliente que usa `null` uma `Name` versão mais antiga do SDK poder sair da propriedade de uma habilidade, fazendo com que o cliente recue no esquema de nomeação padrão.

## <a name="details-about-errors-and-warnings"></a>Detalhes sobre erros e avisos

`ItemError`e `ItemWarning` os modelos que encapsulam detalhes de erros e advertências (respectivamente) que ocorram durante uma execução indexante foram modificados para incluir três novas propriedades com o objetivo de ajudar a depurar o indexador. Estas propriedades são:

- `Name`: O nome da fonte em que o erro teve origem. Por exemplo, poderia referir-se a uma habilidade particular no conjunto de habilidades anexa.
- `Details`: Detalhes verbosos adicionais sobre o erro ou aviso.
- `DocumentationLink`: Um link para um guia de resolução de problemas para o erro ou aviso específico.

> [!NOTE]
> Começamos a estruturar os nossos erros e avisos para incluir estes detalhes úteis sempre que possível. Estamos a trabalhar para garantir que, para todos os erros e advertências, estes detalhes estejam presentes, mas é um trabalho em curso e estes detalhes adicionais nem sempre podem ser preenchidos.

## <a name="next-steps"></a>Passos seguintes

- As alterações à habilidade Shaper têm o impacto mais potencial no código novo ou existente. Como próximo passo, não se esqueça de revisitar este exemplo ilustrando a estrutura de entrada: [Shaper skill JSON exemplo](cognitive-search-skill-shaper.md) de definição
- Veja a visão geral do enriquecimento da [IA.](cognitive-search-concept-intro.md)
- Congratulamo-nos com o seu feedback sobre o SDK. Se encontrar problemas, sinta-se à vontade para nos pedir ajuda no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se encontrar um bug, pode apresentar um problema no [repositório Azure.NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de pré-fixar o título de emissão com "[Pesquisa Cognitiva Azure]".

