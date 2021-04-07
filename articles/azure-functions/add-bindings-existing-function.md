---
title: Ligar funções a outros serviços Azure
description: Saiba como adicionar encadernações que se ligam a outros serviços Azure a uma função existente no seu projeto Azure Functions.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: d1c6f5bb8ca5fcf995b8a8d326abbec96f1d2e35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258206"
---
# <a name="connect-functions-to-azure-services-using-bindings"></a>Conecte funções aos serviços Azure utilizando encadernações

Quando cria uma função, o código de disparo específico da linguagem é adicionado no seu projeto a partir de um conjunto de modelos de gatilho. Se pretender ligar a sua função a outros serviços utilizando encadernações de entrada ou saída, tem de adicionar definições de ligação específicas na sua função. Para saber mais sobre encadernações, consulte [a Azure Functions triggers e encaderna os conceitos.](functions-triggers-bindings.md)

## <a name="local-development"></a>Desenvolvimento local       

Quando desenvolve funções localmente, tem de atualizar o código de função para adicionar encadernações. A utilização do Código do Estúdio Visual pode facilitar a adição de encadernações a uma função.  

### <a name="visual-studio-code"></a>Visual Studio Code

Quando utiliza o Código do Estúdio Visual para desenvolver a sua função e a sua função utiliza um function.jsno ficheiro, a extensão Azure Functions pode adicionar automaticamente uma ligação a um function.jsexistente no ficheiro. Para saber mais, consulte [adicionar entradas e encadernações de saída](functions-develop-vs-code.md#add-input-and-output-bindings).   

### <a name="manually-add-bindings-based-on-examples"></a>Adicionar manualmente encadernações com base em exemplos

Ao adicionar uma ligação a uma função existente, necessitará de atualizar tanto o código de função como o function.jsno ficheiro de configuração, se utilizado pelo seu idioma. Tanto as funções de classe .NET como as funções java usam atributos em vez de function.jsligados, pelo que terá de atualizar isso.

Utilize a tabela seguinte para encontrar exemplos de tipos de encadernação específicos que pode utilizar para o orientar na atualização de uma função existente. Primeiro, escolha o separador de idiomas que corresponde ao seu projeto. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="azure-portal"></a>Portal do Azure

Quando desenvolver as suas funções no [portal Azure,](https://portal.azure.com)adicione entradas e encadernações de saída no **separador Integração** para uma determinada função. As novas ligações são adicionadas ao function.jsno ficheiro ou aos atributos do método, dependendo do seu idioma. Os seguintes artigos mostram exemplos de como adicionar ligações a uma função existente no portal:

+ [Encadernação de saída de armazenamento de fila](functions-integrate-storage-queue-output-binding.md)
+ [Vinculação de saída DB Azure Cosmos](functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="next-steps"></a>Passos seguintes

+ [Conceitos dos acionadores e dos enlaces das Funções do Azure](functions-triggers-bindings.md)
