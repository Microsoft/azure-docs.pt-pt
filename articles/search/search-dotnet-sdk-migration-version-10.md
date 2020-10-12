---
title: Upgrade para a versão 10 da NET SDK
titleSuffix: Azure Cognitive Search
description: Migrar código para a versão 10 da Azure Cognitive Search .NET SDK a partir de versões mais antigas. Saiba o que é novo e quais as alterações de código necessárias.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: bfe24ff38446fa0d0ccea96799e6f42b561713bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89002815"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Upgrade para Azure Cognitive Search .NET SDK versão 10

Se estiver a utilizar a versão 9.0 ou mais antiga do [.NET SDK,](/dotnet/api/overview/azure/search)este artigo irá ajudá-lo a atualizar a sua aplicação para utilizar a versão 10.

A Azure Search é renomeado para Azure Cognitive Search na versão 10, mas os espaços de nome e os nomes dos pacotes são inalterados. As versões anteriores do SDK (9.0 e anteriores) continuam a utilizar o nome anterior. Para obter mais informações sobre a utilização do SDK, incluindo exemplos, consulte [Como utilizar a Azure Cognitive Search a partir de uma aplicação .NET](search-howto-dotnet-sdk.md).

A versão 10 adiciona várias funcionalidades e correções de bugs, elevando-a ao mesmo nível funcional que a versão REST API `2019-05-06` . Nos casos em que uma mudança quebra o código existente, vamos acompanhá-lo através dos [passos necessários para resolver o problema.](#UpgradeSteps)

> [!NOTE]
> Se estiver a utilizar a versão 8.0 pré-visualização ou mais antiga, deve atualizar para a versão 9 primeiro e, em seguida, atualizar para a versão 10. Consulte [a atualização para a versão 9 da Azure Search .NET SDK](search-dotnet-sdk-migration-version-9.md) para obter instruções.
>
> A sua instância de serviço de pesquisa suporta várias versões REST API, incluindo a mais recente. Pode continuar a utilizar uma versão quando já não é a mais recente, mas recomendamos que emigre o seu código para utilizar a versão mais recente. Ao utilizar a API REST, deve especificar a versão API em cada pedido através do parâmetro da versão API. Ao utilizar o .NET SDK, a versão do SDK que está a utilizar determina a versão correspondente da API REST. Se estiver a utilizar um SDK mais antigo, pode continuar a executar esse código sem alterações, mesmo que o serviço seja atualizado para suportar uma versão API mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>Novidades na versão 10
Versão 10 do Azure Cognitive Search .NET SDK alvos REST API `2019-05-06` com estas atualizações:

* Introdução de duas novas competências - [competência condicional](cognitive-search-skill-conditional.md) e [tradução de texto.](cognitive-search-skill-text-translation.md)
* [As entradas de habilidades](cognitive-search-skill-shaper.md) do shaper foram reestruturadas para acomodar a consolidação a partir de contextos aninhados. Para mais informações, consulte este [exemplo da definição JSON.](./cognitive-search-skill-shaper.md#scenario-3-input-consolidation-from-nested-contexts)
* Adição de duas novas funções de [mapeamento de campo:](search-indexer-field-mappings.md)
    - [urlEncode](./search-indexer-field-mappings.md#urlencode-function)
    - [urlDecode](./search-indexer-field-mappings.md#urldecode-function)
* Em certas ocasiões, erros e avisos que aparecem no [estado de execução do indexante](/rest/api/searchservice/get-indexer-status) podem ter detalhes adicionais que ajudam a depurar. `IndexerExecutionResult` foi atualizado para refletir este comportamento.
* As competências individuais definidas dentro de um [skillset](cognitive-search-defining-skillset.md) podem opcionalmente ser identificadas especificando um `name` imóvel.
* `ServiceLimits` mostra limites para [tipos complexos](./search-howto-complex-data-types.md) e `IndexerExecutionInfo` mostra limites/quotas de indexante pertinentes.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar

1. Atualize a sua referência NuGet para `Microsoft.Azure.Search` utilizar a consola Do Gestor de Pacotes NuGet ou clicando à direita nas referências do seu projeto e selecionando "Gerir pacotes NuGet..." em Estúdio Visual.

2. Assim que o NuGet tiver descarregado os novos pacotes e as suas dependências, reconstrua o seu projeto. 

3. Se a sua construção falhar, terá de corrigir cada erro de construção. Consulte [alterações de Breaking na versão 10](#ListOfChanges) para obter detalhes sobre como resolver cada potencial erro de construção.

4. Uma vez corrigido quaisquer erros ou avisos de construção, pode escoar alterações na sua aplicação para tirar partido de novas funcionalidades, se desejar. As novas funcionalidades no SDK são detalhadas no [que há de novo na versão 10](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>Quebrando mudanças na versão 10

Existem várias alterações na versão 10 que podem exigir alterações de código para além de reconstruir a sua aplicação.

> [!NOTE]
> A lista de alterações abaixo não é exaustiva. Algumas alterações provavelmente não resultarão em erros de construção, mas estão tecnicamente a quebrar uma vez que quebram a compatibilidade binária com conjuntos que dependem de versões anteriores dos conjuntos Azure Cognitive Search .NET SDK. Estão também enumeradas alterações significativas que se enquadram nesta categoria, juntamente com recomendações. Por favor, reconstrua a sua aplicação ao atualizar para a versão 10 para evitar quaisquer problemas de compatibilidade binária.

### <a name="custom-web-api-skill-definition"></a>Definição de habilidade de API web personalizada

A definição da [habilidade de API web personalizada](cognitive-search-custom-skill-web-api.md) foi incorretamente especificada na versão 9 ou mais antiga. 

O modelo `WebApiSkill` para especificado como uma propriedade de objeto que `HttpHeaders` _contém_ um dicionário. A criação de um skillset com uma `WebApiSkill` construção desta forma resultaria numa exceção, uma vez que a API do REST consideraria o pedido mal formado. Esta questão foi corrigida, fazendo `HttpHeaders` **uma propriedade de dicionário de alto nível** sobre o `WebApiSkill` próprio modelo - que é considerado um pedido válido da API REST.

Por exemplo, se já tentou instantaneamente o `WebApiSkill` seguinte:

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

alterá-lo para o seguinte, para evitar o erro de validação da API REST:

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

## <a name="shaper-skill-allows-nested-context-consolidation"></a>Habilidade de shaper permite a consolidação de contexto aninhado

A habilidade do shaper pode agora permitir a consolidação de entradas a partir de contextos aninhados. Para ativar esta alteração, modificámos `InputFieldMappingEntry` para que possa ser instantânea especificando apenas uma `Source` propriedade, ou tanto as `SourceContext` `Inputs` propriedades.

Provavelmente não necessitará de fazer alterações de código; no entanto, note que apenas uma destas duas combinações é permitida. Isto significa:

- Criar um `InputFieldMappingEntry` lugar onde só é `Source` inicializado é válido.
- Criar um `InputFieldMappingEntry` "onde" `SourceContext` apenas e `Inputs` são inicializados é válido.
- Todas as outras combinações que envolvem essas três propriedades são inválidas.

Se decidir começar a utilizar esta nova capacidade, certifique-se de que todos os seus clientes estão atualizados para utilizar a versão 10 primeiro, antes de lançar essa alteração. Caso contrário, existe a possibilidade de que uma atualização por um cliente (utilizando uma versão mais antiga do SDK) para a habilidade Shaper possa resultar em erros de validação.

> [!NOTE]
> Embora o modelo subjacente `InputFieldMappingEntry` tenha sido modificado para permitir a consolidação a partir de contextos aninhados, o seu uso é válido apenas dentro da definição de uma habilidade Shaper. A utilização desta capacidade noutras competências, embora válidas no tempo de compilação, resultará num erro de validação no tempo de execução.

## <a name="skills-can-be-identified-by-a-name"></a>As competências podem ser identificadas por um nome

Cada habilidade dentro de um skillset agora tem uma nova propriedade `Name` , que pode ser inicializada no seu código para ajudar a identificar a habilidade. Isto é opcional - quando não especificado (que é o padrão, se não foi feita nenhuma alteração explícita de código), é atribuído um nome predefinido usando o índice 1 base da habilidade no skillset, prefixado com o caráter '#'. Por exemplo, na seguinte definição de skillset (a maioria das inicializações ignoradas para a brevidade):

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

`SentimentSkill` é atribuído um `#1` nome, `WebApiSkill` é `#2` atribuído, é atribuído e assim por `ShaperSkill` `#3` diante.

Se optar por identificar habilidades por um nome personalizado, certifique-se de atualizar todas as instâncias dos seus clientes para a versão 10 do SDK primeiro. Caso contrário, existe a possibilidade de um cliente que usa uma versão mais antiga do SDK poder `null` eliminar a propriedade de uma habilidade, fazendo com que o cliente recue no esquema de `Name` nomeação padrão.

## <a name="details-about-errors-and-warnings"></a>Detalhes sobre erros e avisos

`ItemError` e `ItemWarning` os modelos que englobam detalhes de erros e advertências (respectivamente) que ocorrem durante uma execução indexante foram modificados para incluir três novas propriedades com o objetivo de ajudar na depuração do indexante. Estas propriedades são:

- `Name`: O nome da fonte à qual o erro originou. Por exemplo, poderia referir-se a uma habilidade específica no skillset anexo.
- `Details`: Detalhes verbosos adicionais sobre o erro ou aviso.
- `DocumentationLink`: Uma ligação a um guia de resolução de problemas para o erro ou aviso específico.

> [!NOTE]
> Começamos a estruturar os nossos erros e avisos para incluir estes detalhes úteis sempre que possível. Estamos a trabalhar para garantir que, para todos os erros e avisos, estes detalhes estão presentes, mas é um trabalho em curso e estes detalhes adicionais podem nem sempre ser povoados.

## <a name="next-steps"></a>Passos seguintes

- As alterações na habilidade do Shaper têm o impacto mais potencial no código novo ou existente. Como próximo passo, certifique-se de revisitar este exemplo ilustrando a estrutura de entrada: [Exemplo de definição JSON de habilidade de modelador](cognitive-search-skill-shaper.md)
- Ver a visão geral do [enriquecimento da IA.](cognitive-search-concept-intro.md)
- Congratulamo-nos com o seu feedback sobre o SDK. Se encontrar problemas, sinta-se à vontade para nos pedir ajuda no [Stack Overflow.](https://stackoverflow.com/questions/tagged/azure-search) Se encontrar um bug, pode arquivar um problema no [repositório Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de pré-afixar o seu título de emissão com "[Azure Cognitive Search]".