---
title: Gatilhos e encadernações em Funções Azure
description: Aprenda a usar gatilhos e encadernações para ligar a sua Função Azure a eventos online e serviços baseados na nuvem.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276507"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Funções Azure desencadeia e encaderna conceitos

Neste artigo aprende-se os conceitos de alto nível que envolvem funções degatilhos e encadernações.

Os gatilhos são o que faz com que uma função funcione. Um gatilho define como uma função é invocada e uma função deve ter exatamente um gatilho. Os acionadores têm dados associados, que são geralmente fornecidos como o payload da função. 

Vincular a uma função é uma forma de ligar declarativamente outro recurso à função; as encadernações podem ser ligadas como *encadernações*de entrada, *encadernações*de saída, ou ambas. Os dados de enlaces são fornecidos à função como parâmetros.

Pode combinar e corresponder enlaces diferentes consoante as suas necessidades. Os enlaces são opcionais e uma função pode ter um ou mais enlaces de entrada e/ou de saída.

Os gatilhos e encadernações permitem evitar o acesso a outros serviços. A sua função recebe dados (por exemplo, os conteúdos de uma mensagem de fila) em parâmetros de função. Pode enviar dados (por exemplo, para criar uma mensagem de fila) através do valor de retorno da função. 

Considere os seguintes exemplos de como poderia implementar diferentes funções.

| Cenário de exemplo | Acionador | Encadernação de entrada | Encadernação de saída |
|-------------|---------|---------------|----------------|
| Chega uma nova mensagem de fila que funciona com uma função de escrever para outra fila. | Fila<sup>*</sup> | *Nenhum* | Fila<sup>*</sup> |
|Um trabalho programado lê conteúdos de Blob Storage e cria um novo documento Cosmos DB. | Temporizador | Blob Storage | Cosmos DB |
|A Grelha de Eventos é usada para ler uma imagem do Blob Storage e um documento da Cosmos DB para enviar um e-mail. | Event Grid | Blob Storage e Cosmos DB | SendGrid |
| Um webhook que usa o Microsoft Graph para atualizar uma folha excel. | HTTP | *Nenhum* | Microsoft Graph |

<sup>\*</sup>Representa filas diferentes

Estes exemplos não são para ser exaustivos, mas são fornecidos para ilustrar como pode utilizar gatilhos e encadernações em conjunto.

###  <a name="trigger-and-binding-definitions"></a>Definições de gatilho e de ligação

Os gatilhos e encadernações são definidos de forma diferente dependendo da abordagem de desenvolvimento.

| Plataforma | Os gatilhos e encadernações são configurados por... |
|-------------|--------------------------------------------|
| Biblioteca de classe C# | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;métodos de decoração e parâmetros com atributos C# |
| Todos os outros (incluindo o portal Azure) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;atualização [função.json](./functions-reference.md) [(esquema)](http://json.schemastore.org/function) |

O portal fornece um UI para esta configuração, mas pode editar o ficheiro diretamente abrindo o **editor Avançado** disponível através do separador **Integrado** da sua função.

Em .NET, o tipo de parâmetro define o tipo de dados para os dados de entrada. Por exemplo, `string` utilize para se ligar ao texto de um gatilho de fila, um matriz byte para ler como binário e um tipo personalizado para desserializar a um objeto.

Para idiomas que são dinamicamente digitados como JavaScript, utilize a `dataType` propriedade no ficheiro *fun.json.* Por exemplo, ler o conteúdo de um pedido `dataType` HTTP `binary`em formato binário, definido para:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Outras `dataType` opções para são `stream` e `string`.

## <a name="binding-direction"></a>Direção vinculativa

Todos os gatilhos e `direction` encadernações têm uma propriedade no ficheiro [função.json:](./functions-reference.md)

- Para os gatilhos, a direção é sempre`in`
- Utilização `in` de encadernações de entrada e saída e`out`
- Algumas encadernações apoiam uma direção `inout`especial. Se utilizar `inout`, apenas o **editor Avançado** está disponível através do separador **Integrano** no portal.

Quando utiliza [atributos numa biblioteca](functions-dotnet-class-library.md) de classes para configurar gatilhos e encadernações, a direção é fornecida num construtor de atributos ou inferida do tipo de parâmetro.

## <a name="supported-bindings"></a>Encadernações apoiadas

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Para obter informações sobre quais as encadernações em pré-visualização ou aprovadas para utilização da produção, consulte [as línguas apoiadas.](supported-languages.md)

## <a name="resources"></a>Recursos
- [Expressões e padrões de ligação](./functions-bindings-expressions-patterns.md)
- [Utilização do valor de retorno da Função Azure](./functions-bindings-return-value.md)
- [Como registar uma expressão vinculativa](./functions-bindings-register.md)
- Ensaio:
  - [Estratégias para testar o seu código nas Funções do Azure](functions-test-a-function.md)
  - [Executar manualmente uma função não acionada por HTTP](functions-manually-run-non-http.md)
- [Manuseamento de erros de encadernação](./functions-bindings-errors.md)

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Registar extensões de ligação de funções Azure](./functions-bindings-register.md)
