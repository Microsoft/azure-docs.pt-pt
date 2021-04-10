---
title: Criar SDKs de linguagem personalizada com AutoRest
titleSuffix: Azure Digital Twins
description: Aprenda a usar o AutoRest para gerar SDKs de linguagem personalizada, para escrever código Azure Digital Twins em outras línguas que não tenham publicado SDKs.
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 35cf54199f8f2c187ad397c21fb941111f07c4a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561845"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>Criar SDKs de linguagem personalizada para gémeos digitais Azure usando o AutoRest

Se precisar de trabalhar com a Azure Digital Twins utilizando um idioma que não tenha um [SDK Azure Digital Twins publicado,](how-to-use-apis-sdks.md)este artigo irá mostrar-lhe como usar o AutoRest para gerar o seu próprio SDK no idioma à sua escolha. 

Os exemplos deste artigo mostram a criação de um plano de [dados SDK,](how-to-use-apis-sdks.md#overview-data-plane-apis)mas este processo funcionará também para gerar um plano de [controlo SDK.](how-to-use-apis-sdks.md#overview-control-plane-apis)

## <a name="prerequisites"></a>Pré-requisitos

Para gerar um SDK, primeiro terá de completar a seguinte configuração na sua máquina local:
* Instalar [**AutoRest**](https://github.com/Azure/autorest), versão 2.0.4413 (a versão 3 não está suportada atualmente)
* Instale [**Node.js,**](https://nodejs.org)que é um pré-requisito para a utilização do AutoRest
* Instalar [ **Estúdio Visual**](https://visualstudio.microsoft.com/downloads/)
* Descarregue o mais recente **ficheiro do plano de dados** Azure Digital Twins Swagger (OpenAPI) a partir da pasta Do plano de [dados Swagger,](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)juntamente com a sua pasta de exemplos que acompanha. O ficheiro Swagger é o chamado *digitaltwins.js.*

>[!TIP]
> Para criar um **plano de controlo SDK,** em vez disso, complete os passos deste artigo utilizando o mais recente ficheiro do plano de **controlo Swagger** (OpenAPI) da pasta do plano de [controlo Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) em vez do plano de dados um.

Uma vez que a sua máquina esteja equipada com tudo da lista acima, está pronto a usar o AutoRest para criar um SDK.

## <a name="create-the-sdk-using-autorest"></a>Criar o SDK utilizando o AutoRest 

Uma vez instalado Node.js, pode executar este comando para se certificar de que tem a versão necessária do AutoRes instalado:
```cmd/sh
npm install -g autorest@2.0.4413
```

Para executar o AutoRest contra o ficheiro Azure Digital Twins Swagger, siga estes passos:
1. Copie o ficheiro Azure Digital Twins Swagger e a sua pasta de exemplos que o acompanha num diretório de trabalho.
2. Use uma janela de ordem de comando para mudar para o diretório de trabalho.
3. Corra autorest com o seguinte comando. Substitua o `<language>` espaço reservado pelo seu idioma de eleição: , , e assim por `python` `java` `go` diante. (Pode encontrar a lista completa de opções no [AutoRest README](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

Como resultado, verá uma nova pasta chamada *DigitalTwinsApi* no seu diretório de trabalho. Os ficheiros SDK gerados terão o espaço de nome *DigitalTwinsApi*. Você continuará a usar esse espaço de nome através do resto dos exemplos de uso neste artigo.

O AutoRest suporta uma vasta gama de geradores de códigos linguísticos.

## <a name="make-the-sdk-into-a-class-library"></a>Transforme o SDK numa biblioteca de classes

Pode incluir os ficheiros gerados pelo AutoRest diretamente numa solução .NET. No entanto, é provável que queira incluir o Azure Digital Twins SDK em vários projetos separados (aplicações para clientes, apps Azure Functions, entre outras). Por esta razão, pode ser útil construir um projeto separado (uma biblioteca de classe .NET) a partir dos ficheiros gerados. Em seguida, você pode incluir este projeto de biblioteca de classes em várias soluções como referência de projeto.

Esta secção dá instruções sobre como construir o SDK como uma biblioteca de classes, que é o seu próprio projeto e pode ser incluído em outros projetos. Estes passos dependem do **Visual Studio.**

Eis os passos:

1. Crie uma nova solução visual Studio para uma biblioteca de classes
2. Use *o DigitalTwinsApi* como nome do projeto
3. No Solutions Explorer, selecione à direita o projeto *DigitalTwinsApi* da solução gerada e escolha *Adicionar > Item Existente...*
4. Encontre a pasta onde gerou o SDK e selecione os ficheiros ao nível da raiz
5. Prima "Ok"
6. Adicione uma pasta ao projeto (selecione à direita o projeto no Solution Explorer e escolha *Adicionar > Nova Pasta)*
7. Nomeie os *modelos* de pasta
8. Selecione à direita a pasta *Modelos* no Explorador de Soluções e *selecione Adicionar > Item existente...*
9. Selecione os ficheiros na pasta *Modelos* do SDK gerado e prima "Ok"

Para construir o SDK com sucesso, o seu projeto necessitará destas referências:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Para adicionar estes, abra *ferramentas > Gestor de Pacotes NuGet > gerir pacotes NuGet para solução...*.

1. No painel, certifique-se de que o *separador Browse* está selecionado
2. Procurar *microsoft.rest*
3. Selecione os `ClientRuntime` `ClientRuntime.Azure` pacotes e adicione-os à sua solução

Agora pode construir o projeto e incluí-lo como referência de projeto em qualquer aplicação Azure Digital Twins que escreva.

## <a name="tips-for-using-the-sdk"></a>Dicas para a utilização do SDK

Esta secção contém informações gerais e diretrizes para a utilização do SDK gerado.

### <a name="synchronous-and-asynchronous-calls"></a>Chamadas sincronizadas e assíncronos

Todas as funções SDK vêm em versões sincronizadas e assíncronos.

### <a name="typed-and-untyped-data"></a>Dados dactilografados e não tipigidos

REST As chamadas API geralmente devolvem objetos fortemente dactilografados. No entanto, como a Azure Digital Twins permite que os utilizadores definam os seus próprios tipos personalizados para gémeos, não há forma de pré-definir dados estáticos de retorno para muitas chamadas Azure Digital Twins. Em vez disso, as APIs devolvem tipos de invólucros fortemente digitado quando aplicável, e os dados gémeos de tipo personalizado estão em objetos Json.NET (utilizados onde quer que o tipo de dados "objeto" apareça nas assinaturas API). Pode lançar estes objetos adequadamente no seu código.

### <a name="error-handling"></a>Processamento de erros

Sempre que ocorrer um erro no SDK (incluindo erros HTTP como o 404), o SDK lançará uma exceção. Por esta razão, é importante encapsular todas as chamadas da API dentro de blocos de tentativa/captura.

Aqui está um código que tenta adicionar um gémeo e apanha quaisquer erros neste processo:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>Paginação

O AutoResco gera dois tipos de padrões de paging para o SDK:
* Um para todos os APIs, exceto a API de Consulta
* Um para a Consulta API

No padrão de paging não-consulta, aqui está um método de amostra que mostra como recuperar uma lista de relações de saída de Azure Digital Twins:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

O segundo padrão é gerado apenas para a API de consulta. Usa `continuationToken` explicitamente.

>[!TIP]
> A principal razão para obter páginas é calcular os [custos](concepts-query-units.md) da Unidade de Consulta para uma chamada de API de consulta.

Aqui está um exemplo com este padrão:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>Passos seguintes

Caminhe pelos degraus para criar uma aplicação para clientes onde pode utilizar o seu SDK:
* [*Tutorial: Código de uma aplicação de cliente*](tutorial-code.md)
