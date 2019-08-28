---
title: Gatilhos e associações no Azure Functions
description: Aprenda a usar gatilhos e associações para conectar sua função do Azure a eventos online e serviços baseados em nuvem.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 914158ba7cfcc7530120d427c62e69036b3bb156
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085078"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Conceitos de Azure Functions de gatilhos e associações

Neste artigo, você aprende os conceitos de alto nível em torno de gatilhos e associações de funções.

Os gatilhos são o que causa a execução de uma função. Um gatilho define como uma função é invocada e uma função deve ter exatamente um gatilho. Os gatilhos têm dados associados, que geralmente são fornecidos como a carga da função. 

A associação a uma função é uma maneira de conectar declarativamente outro recurso à função; as associações podem ser conectadas como *associações de entrada*, associações de *saída*ou ambas. Os dados das associações são fornecidos para a função como parâmetros.

Você pode misturar e combinar associações diferentes para atender às suas necessidades. Associações são opcionais e uma função pode ter uma ou várias associações de entrada e/ou saída.

Gatilhos e associações permitem que você evite codificar o acesso a outros serviços. Sua função recebe dados (por exemplo, o conteúdo de uma mensagem da fila) em parâmetros de função. Você envia dados (por exemplo, para criar uma mensagem de fila) usando o valor de retorno da função. 

Considere os exemplos a seguir de como você pode implementar funções diferentes.

| Cenário de exemplo | Acionador | Associação de entrada | Associação de saída |
|-------------|---------|---------------|----------------|
| Uma nova mensagem de fila chega, que executa uma função para gravar em outra fila. | Espera<sup>*</sup> | *Nenhum* | Espera<sup>*</sup> |
|Um trabalho agendado lê o conteúdo do armazenamento de BLOBs e cria um novo documento Cosmos DB. | Temporizador | Armazenamento de Blobs | BD do Cosmos |
|A grade de eventos é usada para ler uma imagem do armazenamento de BLOBs e um documento do Cosmos DB para enviar um email. | Event Grid | Armazenamento de BLOBs e Cosmos DB | SendGrid |
| Um webhook que usa Microsoft Graph para atualizar uma planilha do Excel. | HTTP | *Nenhum* | Microsoft Graph |

<sup>\*</sup>Representa filas diferentes

Esses exemplos não devem ser completos, mas são fornecidos para ilustrar como você pode usar gatilhos e associações juntos.

###  <a name="trigger-and-binding-definitions"></a>Definições de associação e gatilho

Os gatilhos e as associações são definidos de forma diferente, dependendo da abordagem de desenvolvimento.

| Plataforma | Gatilhos e associações são configurados por... |
|-------------|--------------------------------------------|
| C#biblioteca de classes | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;decoração de métodos e parâmetros C# com atributos |
| Todos os outros (incluindo portal do Azure) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Atualizando [Function. JSON](./functions-reference.md) ([esquema](http://json.schemastore.org/function)) |

O portal fornece uma interface do usuário para essa configuração, mas você pode editar o arquivo diretamente abrindo o **Editor avançado** disponível por meio da guia **integrar** da sua função.

No .NET, o tipo de parâmetro define o tipo de dados para dados de entrada. Por exemplo, use `string` para associar ao texto de um gatilho de fila, uma matriz de bytes para ler como binário e um tipo personalizado para desserializar para um objeto.

Para idiomas que são digitados dinamicamente, como JavaScript, use `dataType` a propriedade no arquivo *Function. JSON* . Por exemplo, para ler o conteúdo de uma solicitação HTTP no formato binário, defina `dataType` como `binary`:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Outras opções para `dataType` são `stream` e `string`.

## <a name="binding-direction"></a>Direção da Associação

Todos os gatilhos e associações têm `direction` uma propriedade no arquivo [Function. JSON](./functions-reference.md) :

- Para gatilhos, a direção é sempre`in`
- As associações de entrada e saída `in` usam e`out`
- Algumas associações dão suporte a uma direção `inout`especial. Se você usar `inout`, somente o **Editor avançado** estará disponível por meio da guia **integrar** no Portal.

Quando você usa [atributos em uma biblioteca de classes](functions-dotnet-class-library.md) para configurar gatilhos e associações, a direção é fornecida em um construtor de atributo ou inferida a partir do tipo de parâmetro.

## <a name="supported-bindings"></a>Associações com suporte

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Para obter informações sobre quais associações estão na visualização ou são aprovadas para uso em produção, consulte [idiomas com suporte](supported-languages.md).

## <a name="resources"></a>Recursos
- [Expressões e padrões de associação](./functions-bindings-expressions-patterns.md)
- [Usando o valor de retorno da função do Azure](./functions-bindings-return-value.md)
- [Como registrar uma expressão de associação](./functions-bindings-register.md)
- Testado
  - [Estratégias para testar seu código no Azure Functions](functions-test-a-function.md)
  - [Executar manualmente uma função não disparada por HTTP](functions-manually-run-non-http.md)
- [Tratamento de erros de associação](./functions-bindings-errors.md)

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Registrar Azure Functions extensões de associação](./functions-bindings-register.md)
