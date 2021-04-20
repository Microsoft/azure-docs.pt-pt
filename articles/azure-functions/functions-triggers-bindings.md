---
title: Gatilhos e encadernações em Funções Azure
description: Aprenda a usar gatilhos e encadernações para ligar a sua Função Azure a eventos online e serviços baseados na nuvem.
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8648a52c58929983070b9a89c8fe946b89d37385
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739409"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Conceitos dos acionadores e dos enlaces das Funções do Azure

Neste artigo aprende-se os conceitos de alto nível em torno das funções que desencadeiam e encadernações.

Os gatilhos são o que faz uma função funcionar. Um gatilho define como uma função é invocada e uma função deve ter exatamente um gatilho. Os acionadores têm dados associados, que são geralmente fornecidos como o payload da função. 

A ligação a uma função é uma forma de ligar declarativamente outro recurso à função; as ligações podem ser ligadas como *encadernações de entrada,* *encadernações de saída,* ou ambas. Os dados de enlaces são fornecidos à função como parâmetros.

Pode combinar e corresponder enlaces diferentes consoante as suas necessidades. Os enlaces são opcionais e uma função pode ter um ou mais enlaces de entrada e/ou de saída.

Os gatilhos e encadernações permitem evitar o acesso a outros serviços. A sua função recebe dados (por exemplo, os conteúdos de uma mensagem de fila) em parâmetros de função. Pode enviar dados (por exemplo, para criar uma mensagem de fila) através do valor de retorno da função. 

Considere os seguintes exemplos de como poderia implementar diferentes funções.

| Cenário de exemplo | Acionador | Encadernação de entrada | Ligação de saída |
|-------------|---------|---------------|----------------|
| Chega uma nova mensagem de fila que executa uma função para escrever para outra fila. | Fila<sup>*</sup> | *Nenhuma* | Fila<sup>*</sup> |
|Um trabalho programado lê o conteúdo do Blob Storage e cria um novo documento de Cosmos DB. | Temporizador | Armazenamento de Blobs | Cosmos DB |
|A Grade de Eventos é usada para ler uma imagem do Blob Storage e um documento da Cosmos DB para enviar um e-mail. | Event Grid | Blob Storage e Cosmos DB | SendGrid |
| Um webhook que usa o Microsoft Graph para atualizar uma folha de Excel. | HTTP | *Nenhuma* | Microsoft Graph |

<sup>\*</sup> Representa diferentes filas

Estes exemplos não se destinam a ser exaustivos, mas são fornecidos para ilustrar como se pode usar gatilhos e encadernações em conjunto.

###  <a name="trigger-and-binding-definitions"></a>Definições de gatilho e de ligação

Os gatilhos e encadernações são definidos de forma diferente dependendo da linguagem de desenvolvimento.

| Linguagem | Os gatilhos e as encadernações são configurados por... |
|-------------|--------------------------------------------|
| Biblioteca de classes C# | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;métodos e parâmetros de decoração com atributos C# |
| Java | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;métodos de decoração e parâmetros com anotações java  | 
| JavaScript/PowerShell/Python/TypeScript | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;atualização [function.js](./functions-reference.md) [(esquema)](http://json.schemastore.org/function) |

Para as línguas que dependem de function.js, o portal fornece uma UI para adicionar encadernações no **separador Integração.** Também pode editar o ficheiro diretamente no portal no separador **Código + teste** da sua função. O Visual Studio Code permite-lhe adicionar facilmente [uma ligação a um function.jsno ficheiro,](functions-develop-vs-code.md?tabs=nodejs#add-a-function-to-your-project) seguindo um conveniente conjunto de pedidos. 

Em .NET e Java, o tipo de parâmetro define o tipo de dados para os dados de entrada. Por exemplo, use `string` para ligar ao texto de um gatilho de fila, uma matriz byte para ler como binário, e um tipo personalizado para des-serializar a um objeto. Uma vez que as funções da biblioteca de classe .NET e as funções de Java não dependem *defunction.js* para definições vinculativas, não podem ser criadas e editadas no portal. A edição do portal C# baseia-se no script C#, que utiliza *function.jsem* vez de atributos.

Para saber mais sobre como adicionar encadernações às funções existentes, consulte [as funções De Ligar aos serviços Azure utilizando encadernações](add-bindings-existing-function.md).

Para idiomas que são digitado dinamicamente como JavaScript, use a `dataType` propriedade nofunction.js *no* ficheiro. Por exemplo, para ler o conteúdo de um pedido HTTP em formato binário, definido `dataType` `binary` para:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Outras opções para `dataType` são `stream` e `string` .

## <a name="binding-direction"></a>Direção de encadernação

Todos os gatilhos e encadernações têm uma `direction` propriedade no [function.jsarquivada:](./functions-reference.md)

- Para os gatilhos, a direção é sempre `in`
- Utilização de encadernações de entrada e saída `in` e `out`
- Algumas encadernações suportam uma direção `inout` especial. Se `inout` utilizar, apenas o **editor Advanced** está disponível através do **separador Integração** no portal.

Quando utiliza [atributos numa biblioteca de classes](functions-dotnet-class-library.md) para configurar gatilhos e encadernações, a direção é fornecida num construtor de atributos ou inferida do tipo de parâmetro.

## <a name="add-bindings-to-a-function"></a>Adicione encadernações a uma função

Pode ligar a sua função a outros serviços utilizando encadernações de entrada ou saída. Adicione uma ligação adicionando as suas definições específicas à sua função. Para saber como, consulte [Adicionar encadernações a uma função existente em Funções Azure](add-bindings-existing-function.md).  

## <a name="supported-bindings"></a>Encadernações apoiadas

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Para obter informações sobre quais as ligações que estão em pré-visualização ou aprovadas para utilização na produção, consulte [as línguas suportadas](supported-languages.md).

## <a name="bindings-code-examples"></a>Vincula exemplos de código

Utilize a tabela seguinte para encontrar exemplos de tipos de encadernação específicos que lhe mostrem como trabalhar com encadernações nas suas funções. Primeiro, escolha o separador de idiomas que corresponde ao seu projeto. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="custom-bindings"></a>Encadernações personalizadas

Pode criar entradas personalizadas e encadernações de saída. As encadernações devem ser da autoria em .NET, mas podem ser consumidas a partir de qualquer língua suportada. Para obter mais informações sobre a criação de encadernações personalizadas, consulte [criar entradas personalizadas e encadernações de saída.](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)

## <a name="resources"></a>Recursos
- [Expressões e padrões de ligação](./functions-bindings-expressions-patterns.md)
- [Utilizando o valor de retorno da função Azure](./functions-bindings-return-value.md)
- [Como registar uma expressão vinculativa](./functions-bindings-register.md)
- Testes:
  - [Estratégias para testar o seu código nas Funções do Azure](functions-test-a-function.md)
  - [Executar manualmente uma função não acionada por HTTP](functions-manually-run-non-http.md)
- [Manipulação de erros de ligação](./functions-bindings-errors.md)

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Registar extensões de ligação funções Azure Funções](./functions-bindings-register.md)
