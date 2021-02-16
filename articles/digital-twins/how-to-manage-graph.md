---
title: Gerir o grafo duplo com relações
titleSuffix: Azure Digital Twins
description: Veja como gerir um gráfico de gémeos digitais ligando-os a relacionamentos.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b713a19cbe572998bb6e5050ab2d7721a844f07a
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530455"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Gerir um gráfico de gémeos digitais usando relacionamentos

O coração de Azure Digital Twins é o [gráfico gémeo](concepts-twins-graph.md) que representa todo o seu ambiente. O gráfico gémeo é feito de gémeos digitais individuais ligados através de **relacionamentos.** 

Uma vez que tenha uma [instância Azure Digital Twins](how-to-set-up-instance-portal.md) em funcionamento e tenha configurado código de [autenticação](how-to-authenticate-client.md) na sua aplicação de cliente, pode utilizar as [**APIs digitalTwins**](/rest/api/digital-twins/dataplane/twins) para criar, modificar e eliminar gémeos digitais e as suas relações num caso Azure Digital Twins. Também pode utilizar o [.NET (C#) SDK,](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)ou o [CLI das Gémeas Digitais Azure](how-to-use-cli.md).

Este artigo centra-se na gestão das relações e do gráfico como um todo; para trabalhar com gémeos digitais individuais, ver [*Como-a- Gerir gémeos digitais.*](how-to-manage-twin.md)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>Formas de gerir o gráfico

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

Também pode fazer alterações no seu gráfico utilizando a amostra do Explorador Azure Digital Twins (ADT), que lhe permite visualizar os seus gémeos e gráficos, e faz uso do SDK nos bastidores. A secção seguinte descreve esta amostra em detalhe.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Criar relações

As relações descrevem como diferentes gémeos digitais estão ligados entre si, o que constitui a base do gráfico gémeo.

As relações são criadas usando a `CreateOrReplaceRelationshipAsync()` chamada. 

Para criar uma relação, precisa de especificar:
* O ID gémeo de origem `srcId` (na amostra de código abaixo): A identificação do gémeo de onde a relação se origina.
* O ID gémeo alvo `targetId` (na amostra de código abaixo): A identificação do gémeo onde a relação chega.
* Um nome de relacionamento `relName` (na amostra de código abaixo): O tipo genérico de relacionamento, algo como _._
* Um ID de relacionamento `relId` (na amostra de código abaixo): O nome específico para esta relação, algo como _Relacionamento1_.

A identificação da relação deve ser única dentro do gémeo de origem dada. Não precisa de ser globalmente único.
Por exemplo, para o *twin foo,* cada identificação de relacionamento específico deve ser único. No entanto, outro *bar* gémeo pode ter uma relação de saída que corresponde à mesma identificação de uma relação *foo.*

A seguinte amostra de código ilustra como criar uma relação no seu exemplo de Azure Digital Twins. Utiliza a chamada SDK (realçada) dentro de um método personalizado que pode aparecer no contexto de um programa maior.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod" highlight="13":::

Esta função personalizada pode agora ser chamada para criar uma relação _de contenção_ como esta: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

Se desejar criar múltiplas relações, pode repetir chamadas para o mesmo método, passando diferentes tipos de relacionamento para o argumento. 

Para obter mais informações sobre a classe de `BasicRelationship` ajudante, consulte [*Como-a-fazer: Use as APIs e SDKs de gémeos digitais Azure*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Criar relações múltiplas entre gémeos

As relações podem ser classificadas como: 

* Relações de saída: Relacionamentos pertencentes a este gémeo que apontam para fora para ligá-lo a outros gémeos. O `GetRelationshipsAsync()` método é usado para obter relações de saída de um gémeo.
* Relações de entrada: Relações pertencentes a outras gémeas que apontam para este gémeo para criar uma ligação "incoming". O `GetIncomingRelationshipsAsync()` método é usado para obter relações de um gémeo.

Não há restrição no número de relacionamentos que pode ter entre dois gémeos — pode ter tantas relações entre gémeos quanto quiser. 

Isto significa que pode expressar vários tipos diferentes de relacionamentos entre dois gémeos ao mesmo tempo. Por exemplo, *a Twin A* pode ter uma relação *armazenada* e uma relação *manufaturada* com *a Twin B*.

Pode até criar múltiplos casos do mesmo tipo de relação entre os mesmos dois gémeos, se desejar. Neste exemplo, *Twin A* poderia ter duas relações diferentes *armazenadas* com *Twin B*, desde que as relações tenham iDs de relacionamento diferentes.

## <a name="list-relationships"></a>Relações de lista

Para aceder à lista de relações **de saída** para um dado gémeo no gráfico, pode utilizar o método `GetRelationships()` como este:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

Isto devolve `Azure.Pageable<T>` uma `Azure.AsyncPageable<T>` ou, dependendo se utiliza a versão sincronizada ou assíncrona da chamada.

Aqui está um exemplo que recupera uma lista de relacionamentos. Utiliza a chamada SDK (realçada) dentro de um método personalizado que pode aparecer no contexto de um programa maior.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod" highlight="8":::

Agora pode chamar este método personalizado para ver as relações de saída dos gémeos assim:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

Pode usar as relações recuperadas para navegar para outros gémeos no seu gráfico. Para isso, leia o `target` campo a partir da relação que é devolvida, e use-o como ID para a sua próxima chamada para `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Encontre relacionamentos de entrada para um gémeo digital

A Azure Digital Twins também tem uma API para encontrar todas as relações **recebidas** com um dado gémeo. Isto é frequentemente útil para a navegação inversa, ou quando elimina um gémeo.

>[!NOTE]
> `IncomingRelationship` as chamadas não devolvem todo o corpo da relação. Para obter mais informações sobre a `IncomingRelationship` classe, consulte a sua [documentação de referência.](/dotnet/api/azure.digitaltwins.core.incomingrelationship?view=azure-dotnet&preserve-view=true)

A amostra de código na secção anterior focou-se em encontrar relações de saída de um gémeo. O exemplo a seguir é estruturado da mesma forma, mas encontra relações *de entrada* com o gémeo. Este exemplo também usa a chamada SDK (realçada) dentro de um método personalizado que pode aparecer no contexto de um programa maior.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod" highlight="8":::

Agora pode chamar este método personalizado para ver as relações dos gémeos como este:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>Listar todas as propriedades e relacionamentos gémeos

Utilizando os métodos acima referidos para listar relações de saída e entrada a um gémeo, pode criar um método que imprime informações gémeas completas, incluindo as propriedades do gémeo e ambos os tipos de relacionamentos. Aqui está um método personalizado de exemplo que mostra como combinar os métodos personalizados acima para este fim.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

Pode agora chamar esta função personalizada como esta: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="update-relationships"></a>Atualizar relações

As relações são atualizadas usando o `UpdateRelationship` método. 

>[!NOTE]
>Este método é para atualizar as **propriedades** de uma relação. Se precisar de alterar a origem gémea ou a gémea-alvo da relação, terá de [apagar a relação](#delete-relationships) e recriar [uma](#create-relationships) usando os novos gémeos.

Os parâmetros necessários para a chamada do cliente são o ID do gémeo de origem (o gémeo de onde a relação é originária), o ID da relação a atualizar, e um documento [JSON Patch](http://jsonpatch.com/) contendo as propriedades e novos valores que gostaria de atualizar.

Aqui está o código de amostra que mostra como usar este método. Este exemplo utiliza a chamada SDK (realçada) dentro de um método personalizado que pode aparecer no contexto de um programa maior.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UpdateRelationshipMethod" highlight="6":::

Aqui está um exemplo de uma chamada para este método personalizado, passando em um documento JSON Patch com a informação para atualizar um imóvel.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseUpdateRelationship":::

## <a name="delete-relationships"></a>Eliminar relações

O primeiro parâmetro especifica o gémeo de origem (o gémeo de onde a relação se origina). O outro parâmetro é a identificação da relação. Precisas tanto da identificação gémea como da identificação da relação, porque as identificações de relacionamento são únicas no âmbito de um gémeo.

Aqui está o código de amostra que mostra como usar este método. Este exemplo utiliza a chamada SDK (realçada) dentro de um método personalizado que pode aparecer no contexto de um programa maior.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod" highlight="5":::

Pode agora chamar a este método personalizado para eliminar uma relação como esta:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="runnable-twin-graph-sample"></a>Amostra de gráfico duplo runnable

O seguinte código runnable snippet usa as operações de relacionamento deste artigo para criar um gráfico gémeo a partir de gémeos digitais e relacionamentos.

### <a name="set-up-the-runnable-sample"></a>Configurar a amostra runnable

O snippet usa o [*Room.jse*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) [*Floor.jsem*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) definições de modelo a partir de [*Tutorial: Explore Azure Digital Twins com uma aplicação de cliente de amostra.*](tutorial-command-line-app.md) Pode utilizar estes links para ir diretamente aos ficheiros ou descarregá-los como parte do projeto de amostra completa de ponta a [ponta aqui](/samples/azure-samples/digital-twins-samples/digital-twins-samples/). 

Antes de executar a amostra, faça o seguinte:
1. Descarregue os ficheiros de modelos, coloque-os no seu projeto e substitua os `<path-to>` espaços reservados no código abaixo para dizer ao seu programa onde os encontrar.
2. Substitua o espaço reservado `<your-instance-hostname>` pelo nome de anfitrião da sua instância Azure Digital Twins.
3. Adicione duas dependências ao seu projeto que serão necessárias para trabalhar com a Azure Digital Twins. O primeiro é o pacote para o [Azure Digital Twins SDK para .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true), o segundo fornece ferramentas para ajudar na autenticação contra o Azure.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

Também terá de configurar credenciais locais se quiser executar a amostra diretamente. A próxima secção passa por isto.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Executar o exemplo

Depois de completar os passos acima, pode executar diretamente o seguinte código de amostra.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

Aqui está a saída da consola do programa acima: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Saída da consola mostrando os detalhes gémeos, as relações de entrada e saída dos gémeos." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> O gráfico gémeo é um conceito de criação de relações entre gémeos. Se quiser ver a representação visual do gráfico gémeo, consulte a secção [*visualização*](how-to-manage-graph.md#visualization) deste artigo. 

## <a name="create-graph-from-a-csv-file"></a>Criar gráfico a partir de um ficheiro CSV

Em casos práticos de utilização, as hierarquias gémeas serão muitas vezes criadas a partir de dados armazenados numa base de dados diferente, ou talvez numa folha de cálculo ou num ficheiro CSV. Esta secção ilustra como ler dados a partir de um ficheiro CSV e criar um gráfico duplo a partir dele.

Considere a seguinte tabela de dados, descrevendo um conjunto de gémeos digitais e relacionamentos.

|  ID do Modelo    | Twin ID (deve ser único) | Nome da relação  | ID gémeo alvo  | Dados de informação dupla |
| --- | --- | --- | --- | --- |
| dtmi:exemplo:Piso;1    | Piso1 | contains | Sala1 | |
| dtmi:exemplo:Piso;1    | Piso0 | contains | Sala0 | |
| dtmi:exemplo:Quarto;1    | Sala1 | | | {"Temperatura": 80} |
| dtmi:exemplo:Quarto;1    | Sala0 | | | {"Temperatura": 70} |

Uma forma de obter estes dados em Azure Digital Twins é converter a tabela num ficheiro CSV e escrever código para interpretar o ficheiro em comandos para criar gémeos e relacionamentos. A amostra de código que se segue ilustra a leitura dos dados a partir do ficheiro CSV e a criação de um gráfico duplo em Azure Digital Twins.

No código abaixo, o ficheiro CSV é chamado *data.csv*, e existe um espaço reservado que representa o **nome de anfitrião** da sua instância Azure Digital Twins. A amostra também faz uso de vários pacotes que você pode adicionar ao seu projeto para ajudar neste processo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a consulta de um gráfico gémeo Azure Digital Twins:
* [*Conceitos: Linguagem de consulta*](concepts-query-language.md)
* [*Como fazer: Consulta do gráfico gémeo*](how-to-query-graph.md)