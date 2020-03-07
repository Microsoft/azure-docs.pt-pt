---
title: Gatilhos e encadernações em Funções Azure
description: Aprenda a usar gatilhos e encadernações para ligar a sua Função Azure a eventos online e serviços baseados na nuvem.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356877"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Funções Azure desencadeia e encaderna conceitos

Neste artigo aprende-se os conceitos de alto nível que envolvem funções degatilhos e encadernações.

Os gatilhos são o que faz com que uma função funcione. Um gatilho define como uma função é invocada e uma função deve ter exatamente um gatilho. Os gatilhos têm dados associados, que são muitas vezes fornecidos como a carga útil da função. 

Vincular a uma função é uma forma de ligar declarativamente outro recurso à função; as encadernações podem ser ligadas como *encadernações*de entrada, *encadernações*de saída, ou ambas. Os dados das encadernações são fornecidos à função como parâmetros.

Pode misturar e combinar diferentes encadernações de acordo com as suas necessidades. As encadernações são opcionais e uma função pode ter uma ou múltipla supressão de entradas e/ou de saída.

Os gatilhos e encadernações permitem evitar o acesso a outros serviços. A sua função recebe dados (por exemplo, o conteúdo de uma mensagem de fila) nos parâmetros de função. Envia dados (por exemplo, para criar uma mensagem de fila) utilizando o valor de retorno da função. 

Considere os seguintes exemplos de como poderia implementar diferentes funções.

| Cenário de exemplo | Acionador | Encadernação de entrada | Encadernação de saída |
|-------------|---------|---------------|----------------|
| Chega uma nova mensagem de fila que funciona com uma função de escrever para outra fila. | <sup>Fila*</sup> | *Nenhuma.* | <sup>Fila*</sup> |
|Um trabalho programado lê conteúdos de Blob Storage e cria um novo documento Cosmos DB. | Temporizador | Armazenamento de Blobs | BD do Cosmos |
|A Grelha de Eventos é usada para ler uma imagem do Blob Storage e um documento da Cosmos DB para enviar um e-mail. | Event Grid | Blob Storage e Cosmos DB | SendGrid |
| Um webhook que usa o Microsoft Graph para atualizar uma folha excel. | HTTP | *Nenhuma.* | Microsoft Graph |

<sup>\*</sup> Representa filas diferentes

Estes exemplos não são para ser exaustivos, mas são fornecidos para ilustrar como pode utilizar gatilhos e encadernações em conjunto.

###  <a name="trigger-and-binding-definitions"></a>Definições de gatilho e de ligação

Os gatilhos e encadernações são definidos de forma diferente dependendo da abordagem de desenvolvimento.

| Plataforma | Os gatilhos e encadernações são configurados por... |
|-------------|--------------------------------------------|
| C#biblioteca de classes | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;métodos e parâmetros de decoração com C# atributos |
| Todos os outros (incluindo o portal Azure) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[ atualização função.json](./functions-reference.md) [(esquema)](http://json.schemastore.org/function) |

O portal fornece um UI para esta configuração, mas pode editar o ficheiro diretamente abrindo o **editor Avançado** disponível através do separador **Integrado** da sua função.

Em .NET, o tipo de parâmetro define o tipo de dados para os dados de entrada. Por exemplo, utilize `string` para se ligar ao texto de um gatilho de fila, um matriz byte para ler como binário e um tipo personalizado para desserializar a um objeto.

Para idiomas que são dinamicamente digitados como JavaScript, utilize a propriedade `dataType` no ficheiro *fun.json.* Por exemplo, ler o conteúdo de um pedido HTTP em formato binário, definir `dataType` para `binary`:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Outras opções para `dataType` são `stream` e `string`.

## <a name="binding-direction"></a>Direção vinculativa

Todos os gatilhos e encadernações têm uma propriedade `direction` no ficheiro [função.json:](./functions-reference.md)

- Para os gatilhos, a direção é sempre `in`
- As encadernações de entrada e saída utilizam `in` e `out`
- Algumas encadernações apoiam uma direção especial `inout`. Se utilizar `inout`, apenas o **editor Avançado** está disponível através do separador **Integrano** no portal.

Quando utiliza [atributos numa biblioteca](functions-dotnet-class-library.md) de classes para configurar gatilhos e encadernações, a direção é fornecida num construtor de atributos ou inferida do tipo de parâmetro.

## <a name="supported-bindings"></a>Encadernações apoiadas

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Para obter informações sobre quais as encadernações em pré-visualização ou aprovadas para utilização da produção, consulte [as línguas apoiadas.](supported-languages.md)

## <a name="resources"></a>Recursos
- [Expressões e padrões de ligação](./functions-bindings-expressions-patterns.md)
- [Utilização do valor de retorno da Função Azure](./functions-bindings-return-value.md)
- [Como registar uma expressão vinculativa](./functions-bindings-register.md)
- Ensaio:
  - [Estratégias para testar o seu código em Funções Azure](functions-test-a-function.md)
  - [Executar manualmente uma função não ativada por HTTP](functions-manually-run-non-http.md)
- [Manuseamento de erros de encadernação](./functions-bindings-errors.md)

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Registar extensões de ligação de funções Azure](./functions-bindings-register.md)
