---
title: Gerir modelos personalizados
titleSuffix: Azure Digital Twins
description: Veja como criar, editar e eliminar um modelo dentro da Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 00e6802f37ec60467edbea98038dd23fcba1239a
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185500"
---
# <a name="manage-azure-digital-twins-models"></a>Gerir os modelos Azure Digital Twins

Pode gerir os [modelos](concepts-models.md) que a sua instância Azure Digital Twins conhece sobre a utilização das [**APIs digitalTwinModels,**](/rest/api/digital-twins/dataplane/models)da [.NET (C#) SDK,](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)ou do [Azure Digital Twins CLI](how-to-use-cli.md). 

As operações de gestão incluem upload, validação, recuperação e eliminação de modelos. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-models"></a>Formas de gerir modelos

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-models"></a>Criar modelos

Os modelos para Azure Digital Twins são escritos em DTDL e guardados como ficheiros *.json.* Existe também uma [extensão DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) disponível para [o Código do Estúdio Visual,](https://code.visualstudio.com/)que fornece validação de sintaxe e outras funcionalidades para facilitar a escrita de documentos DTDL.

Considere um exemplo em que um hospital quer representar digitalmente os seus quartos. Cada quarto contém um dispensador de sabão inteligente para monitorizar a lavagem manual e sensores para monitorizar o tráfego através da sala.

O primeiro passo para a solução é criar modelos que representem aspetos do hospital. Um quarto de paciente neste cenário pode ser descrito assim:

:::code language="json" source="~/digital-twins-docs-samples/models/PatientRoom.json":::

> [!NOTE]
> Este é um corpo de amostra para um ficheiro .json no qual um modelo é definido e guardado, para ser carregado como parte de um projeto de cliente. A chamada REST API, por outro lado, leva uma série de definições de modelo como a acima (que é mapeada para um `IEnumerable<string>` no .NET SDK). Assim, para utilizar este modelo na API REST diretamente, rodei-lo com suportes.

Este modelo define um nome e um ID único para a sala do paciente, e propriedades para representar a contagem de visitantes e o estado de lavagem à mão (estes contadores serão atualizados a partir de sensores de movimento e distribuidores de sabão inteligentes, e serão usados em conjunto para calcular uma *percentagem de lavagem à mão).* O modelo também define uma relação *com Devices*, que será usada para ligar quaisquer [gémeos digitais](concepts-twins-graph.md) com base neste modelo *da Sala* aos dispositivos reais.

Seguindo este método, pode continuar a definir modelos para as enfermarias, zonas ou o próprio hospital.

### <a name="validate-syntax"></a>Validar sintaxe

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="upload-models"></a>Modelos de upload

Uma vez criados os modelos, pode enviá-los para a instância Azure Digital Twins.

Quando estiver pronto para carregar um modelo, pode utilizar o seguinte corte de código:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Observe que o `CreateModels` método aceita vários ficheiros numa única transação. Aqui está uma amostra para ilustrar:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModels_multi":::

Os ficheiros de modelo podem conter mais do que um único modelo. Neste caso, os modelos devem ser colocados numa matriz JSON. Por exemplo:

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Moon.json":::

No upload, os ficheiros de modelos são validados pelo serviço.

## <a name="retrieve-models"></a>Recuperar modelos

Pode listar e recuperar modelos armazenados na sua instância Azure Digital Twins. 

Aqui estão as suas opções para isto:
* Recuperar um único modelo
* Recuperar todos os modelos
* Recuperar metadados e dependências para modelos

Aqui estão algumas chamadas de exemplo:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

A API chama para recuperar os modelos todos os objetos de `DigitalTwinsModelData` retorno. `DigitalTwinsModelData` contém metadados sobre o modelo armazenado no exemplo Azure Digital Twins, como nome, DTMI, e data de criação do modelo. O `DigitalTwinsModelData` objeto também inclui opcionalmente o próprio modelo. Dependendo dos parâmetros, pode assim utilizar as chamadas de recuperação para recuperar apenas metadados (o que é útil em cenários em que pretende apresentar uma lista de UI de ferramentas disponíveis, por exemplo), ou todo o modelo.

A `RetrieveModelWithDependencies` chamada devolve não só o modelo solicitado, mas também todos os modelos de que o modelo solicitado depende.

Os modelos não são necessariamente devolvidos no formulário de documento em que foram carregados. A Azure Digital Twins apenas garante que o formulário de devolução será semântica equivalente. 

## <a name="update-models"></a>Atualizar modelos

Uma vez que um modelo é carregado para a sua instância Azure Digital Twins, toda a interface do modelo é imutável. Isto significa que não existe uma "edição" tradicional dos modelos. A Azure Digital Twins também não permite o re upload do mesmo modelo.

Em vez disso, se pretender fazer alterações a um modelo , como atualizar `displayName` ou — a forma de o fazer é carregar uma versão mais `description` **recente** do modelo. 

### <a name="model-versioning"></a>Controlo de versões de modelos

Para criar uma nova versão de um modelo existente, comece pelo DTDL do modelo original. Atualizar, adicionar ou remover os campos que gostaria de alterar.

Em seguida, marque isto como uma versão mais recente do modelo atualizando o `id` campo do modelo. A última secção do ID do modelo, depois `;` do, representa o número do modelo. Para indicar que esta é agora uma versão mais atualizada deste modelo, incremente o número no final do `id` valor para qualquer número maior do que o número de versão atual.

Por exemplo, se o seu ID do seu modelo anterior fosse assim:

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

versão 2 deste modelo pode ser assim:

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

Em seguida, faça o upload da nova versão do modelo para o seu exemplo. 

Esta versão do modelo estará então disponível no seu caso para usar para gémeos digitais. **Não substitui** versões anteriores do modelo, pelo que várias versões do modelo coexistirão no seu caso até as [remover.](#remove-models)

### <a name="impact-on-twins"></a>Impacto nos gémeos

Quando se cria um novo twin, uma vez que a versão do novo modelo e a versão do modelo antigo coexistem, o novo twin pode utilizar quer a nova versão do modelo, quer a versão mais antiga.

Isto também significa que o upload de uma nova versão de um modelo não afeta automaticamente os gémeos existentes. Os gémeos existentes continuarão a ser exemplos da versão modelo antiga.

Pode atualizar estes gémeos existentes para a versão do novo modelo, remendando-os, conforme descrito na [*Atualização de um modelo de modelo de*](how-to-manage-twin.md#update-a-digital-twins-model) *"Como-a-: Gerir gémeos digitais".* Dentro do mesmo patch, tem de atualizar tanto o **ID** do modelo (para a nova versão) **como quaisquer campos que devam ser alterados no gémeo para o tornar em conformidade com o novo modelo.**

## <a name="remove-models"></a>Remover modelos

Os modelos também podem ser removidos do serviço, de uma de duas formas:
* **Desmantelamento** : Uma vez que um modelo é desativado, já não pode usá-lo para criar novos gémeos digitais. Os gémeos digitais existentes que já utilizam este modelo não são afetados, pelo que ainda pode atualizá-los com mudanças de propriedade e adicionar ou eliminar relacionamentos.
* **Eliminação** : Isto removerá completamente o modelo da solução. Os gémeos que estavam a usar este modelo já não estão associados a nenhum modelo válido, por isso são tratados como se não tivessem um modelo. Ainda pode ler estes gémeos, mas não poderá fazer nenhuma atualização sobre eles até que sejam transferidos para um modelo diferente.

Estas são características separadas e não se impactam entre si, embora possam ser usadas em conjunto para remover um modelo gradualmente. 

### <a name="decommissioning"></a>Desmantelamento

Aqui está o código para desativar um modelo:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DecommissionModel":::

O estado de desmantelamento de um modelo está incluído nos `ModelData` registos devolvidos pelas APIs de recuperação do modelo.

### <a name="deletion"></a>Eliminação

Pode eliminar todos os modelos no seu caso de uma vez, ou pode fazê-lo individualmente.

Para um exemplo de como eliminar todos os modelos, descarregue a aplicação de amostra utilizada no [*Tutorial: Explore o básico com uma aplicação de cliente de amostra.*](tutorial-command-line-app.md) O *ficheiro CommandLoop.cs* faz isto numa `CommandDeleteAllModels` função.

O resto desta secção decompõe a eliminação do modelo em detalhes mais próximos, e mostra como fazê-lo para um modelo individual.

#### <a name="before-deletion-deletion-requirements"></a>Antes da eliminação: Requisitos de eliminação

Geralmente, os modelos podem ser apagados a qualquer momento.

A exceção são os modelos de que outros modelos dependem, seja com uma `extends` relação ou como componente. Por exemplo, se um modelo *ConferenceRoom* alargar um modelo *de Sala* e tiver um modelo *ACUnit* como componente, não é possível apagar *a Sala* ou *a ACUnit* até que o *ConferenceRoom* remova as respetivas referências. 

Pode fazê-lo atualizando o modelo dependente para remover as dependências, ou eliminando completamente o modelo dependente.

#### <a name="during-deletion-deletion-process"></a>Durante a eliminação: Processo de eliminação

Mesmo que um modelo cumpra os requisitos para o eliminar imediatamente, é possível que queira passar por alguns passos primeiro para evitar consequências não intencionais para os gémeos deixados para trás. Aqui estão alguns passos que podem ajudá-lo a gerir o processo:
1. Primeiro, desativar o modelo
2. Aguarde alguns minutos, para ter certeza de que o serviço processou quaisquer pedidos de criação de gémeos de última hora enviados antes do desmantelamento
3. Consulta gémeas por modelo para ver todos os gémeos que estão a usar o modelo agora desativado
4. Elimine os gémeos se já não precisar deles, ou remenda-os para um novo modelo, se necessário. Também pode optar por deixá-los em paz, caso em que se tornarão gémeos sem modelos uma vez que o modelo é apagado. Veja a próxima secção para as implicações deste estado.
5. Aguarde mais alguns minutos para se certificar de que as mudanças percolaram através
6. Eliminar o modelo 

Para eliminar um modelo, utilize esta chamada:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DeleteModel":::

#### <a name="after-deletion-twins-without-models"></a>Após a eliminação: Gémeos sem modelos

Uma vez que um modelo é eliminado, quaisquer gémeos digitais que estavam a usar o modelo são agora considerados sem um modelo. Note que não há nenhuma consulta que possa dar-lhe uma lista de todos os gémeos neste estado - embora ainda *possa* consultar os gémeos pelo modelo apagado para saber quais os gémeos afetados.

Aqui está uma visão geral do que pode ou não fazer com gémeos que não têm um modelo.

Coisas que **pode** fazer:
* Consulta o gémeo
* Ler propriedades
* Ler relacionamentos de saída
* Adicione e elimine as relações recebidas (como em, outros gémeos ainda podem formar relações *com* este gémeo)
  - A `target` definição de relacionamento ainda pode refletir o DTMI do modelo eliminado. Uma relação sem alvo definido também pode funcionar aqui.
* Eliminar relações
* Apague o gémeo

Coisas que **não se pode** fazer.
* Editar relações de saída (como em, *relacionamentos* deste gémeo para outros gémeos)
* Editar propriedades

#### <a name="after-deletion-re-uploading-a-model"></a>Após a eliminação: Recarregamento de um modelo

Depois de um modelo ter sido eliminado, poderá decidir mais tarde carregar um novo modelo com o mesmo ID que o que apagou. Eis o que acontece nesse caso.
* Do ponto de vista da loja de soluções, isto é o mesmo que carregar um modelo completamente novo. O serviço não se lembra que o antigo já foi carregado.   
* Se houver gémeos restantes no gráfico que referem o modelo eliminado, deixaram de estar órfãos; este ID do modelo é válido novamente com a nova definição. No entanto, se a nova definição para o modelo for diferente da definição do modelo que foi eliminada, estes gémeos podem ter propriedades e relações que correspondam à definição eliminada e não são válidas com a nova.

A Azure Digital Twins não impede este estado, por isso tenha cuidado para remendar os gémeos adequadamente, de modo a garantir que se mantêm válidos através do interruptor de definição de modelo.

## <a name="next-steps"></a>Passos seguintes

Veja como criar e gerir gémeos digitais com base nos seus modelos:
* [*Como fazer: Gerir gémeos digitais*](how-to-manage-twin.md)