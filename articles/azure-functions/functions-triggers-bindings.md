---
title: Acionadores e enlaces nas funções do Azure
description: Saiba como utilizar acionadores e enlaces para ligar a sua função do Azure para eventos online e serviços baseados na nuvem.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 3865f748a9ca2fe09660d6454542d64f73a8e3c1
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736962"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Acionadores de funções do Azure e conceitos de enlaces

Neste artigo, irá aprender os conceitos de alto nível em torno das funções acionadores e enlaces.

Os acionadores são o que fazer com que uma função ser executado. Um acionador define como uma função é invocada e uma função tem de ter exatamente um acionador. Acionadores tem associado a dados, que, muitas vezes, são fornecidos como o payload da função. 

A ligação para uma função é uma forma de forma declarativa ligar a outro recurso para a função; enlaces podem estar conectados como *enlaces de entrada*, *enlaces de saída*, ou ambos. Dados a partir de ligações são fornecidos para a função como parâmetros.

Pode misturar e combinar diferentes vinculações para satisfazer as suas necessidades. Enlaces são opcionais e uma função pode ter um ou vários entrada e/ou ligações de saída.

Acionadores e enlaces permitem-lhe evitar o acesso de codificar a outros serviços. A função recebe dados (por exemplo, o conteúdo de uma mensagem de fila) nos parâmetros de função. Enviar dados (por exemplo, para criar uma mensagem de fila), utilizando o valor de retorno da função. 

Considere os seguintes exemplos de como pode implementar funções diferentes.

| Cenário de exemplo | Acionador | Enlace de entrada | Enlace de saída |
|-------------|---------|---------------|----------------|
| Uma nova mensagem de fila chegar que executa uma função para gravar em outra fila. | fila<sup>*</sup> | *Nenhum* | fila<sup>*</sup> |
|Uma tarefa agendada lê o conteúdo do armazenamento de BLOBs e cria um novo documento do Cosmos DB. | Temporizador | Blob Storage | BD do Cosmos |
|O Event Grid é usado para ler uma imagem a partir do armazenamento de BLOBs e um documento do Cosmos DB para enviar um e-mail. | Event Grid | Armazenamento de BLOBs e o Cosmos DB | SendGrid |
| Um webhook que utiliza o Microsoft Graph para atualizar uma folha do Excel. | HTTP | *Nenhum* | Microsoft Graph |

<sup>\*</sup> Representa a filas diferentes

Estes exemplos não devem ser exaustivos, mas são fornecidos para ilustrar como pode usar acionadores e enlaces em conjunto.

###  <a name="trigger-and-binding-definitions"></a>Definições de Acionador e ligação

Acionadores e enlaces são definidos-se de forma diferente consoante a abordagem de desenvolvimento.

| Plataforma | Acionadores e enlaces são configurados por... |
|-------------|--------------------------------------------|
| C#biblioteca de classes | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;decorando os métodos e parâmetros com C# atributos |
| Todos os outros utilizadores (incluindo o portal do Azure) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;updating [function.json](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

O portal fornece uma interface do Usuário para esta configuração, mas pode editar o ficheiro diretamente ao abrir o **editor avançado** disponível por meio do **integrar** separador da sua função.

No .NET, o tipo de parâmetro define o tipo de dados para dados de entrada. Por exemplo, usar `string` para vincular ao texto de um acionador de fila, uma matriz de bytes para leitura como binário e um tipo personalizado para anular a serialização para um objeto.

Para idiomas que são digitados dinamicamente, como JavaScript, utilize o `dataType` propriedade no *Function* ficheiro. Por exemplo, para ler o conteúdo de uma solicitação HTTP no formato binário, defina `dataType` para `binary`:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Outras opções para `dataType` estão `stream` e `string`.

## <a name="binding-direction"></a>Direção de ligação

Todos os acionadores e enlaces de tem uma `direction` propriedade no [Function](./functions-reference.md) ficheiro:

- Para acionadores, a direção é sempre `in`
- Ligações de entrada e saídas usam `in` e `out`
- Algumas ligações oferecem suporte a uma direção especial `inout`. Se utilizar `inout`, apenas o **editor avançado** está disponível através da **integrar** separador no portal do.

Quando utiliza [atributos numa biblioteca de classe](functions-dotnet-class-library.md) para configurar acionadores e enlaces, a direção é fornecida no construtor de atributos ou inferida do tipo de parâmetro.

## <a name="supported-bindings"></a>Enlaces suportados

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Para obter informações sobre as ligações estão em pré-visualização ou são aprovadas para utilização em produção, consulte [idiomas suportados](supported-languages.md).

## <a name="resources"></a>Recursos
- [Expressões de associação e padrões](./functions-bindings-expressions-patterns.md)
- [Utilizar o valor de retorno da função do Azure](./functions-bindings-return-value.md)
- [Como registar uma expressão de vinculação](./functions-bindings-register.md)
- Teste:
  - [Estratégias para testar seu código nas funções do Azure](functions-test-a-function.md)
  - [Executar manualmente uma função não acionada por HTTP](functions-manually-run-non-http.md)
- [Tratamento de erros de ligação](./functions-bindings-errors.md)

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Registar as extensões de vinculação de funções do Azure](./functions-bindings-register.md)
