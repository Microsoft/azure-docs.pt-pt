---
title: Executar manualmente das funções não acionada por HTTP do Azure
description: Utilize uma solicitação HTTP para executar um não-HTTP acionada as funções do Azure
services: functions
keywords: ''
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: tutorial
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: cfebe5c783018cfab51f384cce578e43383c3905
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479829"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Executar manualmente uma função não acionada por HTTP

Este artigo demonstra como executar manualmente uma função não acionada por HTTP através de pedido HTTP formatado de modo especial.

Em alguns contextos, poderá ter de executar "on-demand" uma função do Azure que é acionada indiretamente.  Exemplos de acionadores indiretos [funções com base numa agenda](./functions-create-scheduled-function.md) ou as funções que são executados como o resultado da [ação de outro recurso](./functions-create-storage-blob-triggered-function.md). 

[Postman](https://www.getpostman.com/) é utilizado no exemplo a seguir, mas pode usar [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) ou qualquer outro como ferramenta para enviar pedidos HTTP.

## <a name="define-the-request-location"></a>Defina a localização do pedido

Para executar uma função não acionada por HTTP, terá de uma forma de enviar um pedido para o Azure para executar a função. O URL utilizado para fazer este pedido leva um formulário específico.

![Defina a localização do pedido: nome + a caminho da pasta + a função de anfitrião nome](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Nome do anfitrião:** Localização pública da aplicação de função que é composta por do nome da aplicação de função adição *azurewebsites.net* ou o seu domínio personalizado.
- **Caminho da pasta:** Para aceder às funções não acionada por HTTP através de um pedido HTTP, terá de enviar o pedido através das pastas *administrador/funções*.
- **Nome da função:** O nome da função que pretende executar.

Utilize esta localização de pedido no Postman, juntamente com a chave mestra da função no pedido para o Azure para executar a função.

> [!NOTE]
> Ao executar localmente, não é necessária a chave de principal da função. Pode diretamente [chamar a função](#call-the-function) omitindo o `x-functions-key` cabeçalho.

## <a name="get-the-functions-master-key"></a>Obter a chave mestra da função

Navegue para a sua função no portal do Azure e clique em **Manage** e localize a **chaves de anfitrião** secção. Clique nas **cópia** botão no *_master* linha para copiar a chave mestra para a área de transferência.

![Copiar a chave mestra do ecrã de gestão de função](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

Depois de copiar a chave mestra, clique no nome da função para regressar à janela de arquivo de código. Em seguida, clique nas **registos** separador. Verá mensagens a partir da função registado, aqui, quando executa manualmente a função do Postman.

> [!CAUTION]  
> Devido a permissões elevadas na sua aplicação de função concedidas pela chave mestra, não deve partilhar esta chave com terceiros ou distribuí-la num aplicativo.

## <a name="call-the-function"></a>Chamar a função

Abra o Postman e siga estes passos:

1. Introduza o **solicitam a localização na caixa de texto URL**.
2. Certifique-se de que o método HTTP está definido como **POST**.
3. **Clique em** sobre o **cabeçalhos** separador.
4. Introduza **x-funções-key** como o primeiro **chave** e cole a chave mestra (da área de transferência) para o **valor** caixa.
5. Introduza **Content-Type** como o segundo **chave** e introduza **application/json** como o **valor**.

    ![Definições de cabeçalhos do postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

6. **Clique em** sobre o **corpo** separador.
7. Introduza **{"de entrada": "teste"}** como o corpo da solicitação.

    ![Definições de corpo do postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

8. Clique em **enviar**.

    ![Enviar um pedido com o Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

Postman, em seguida, relatórios de estado **aceite 202**.

Em seguida, regresse à função no portal do Azure. Localize a *registos* janela e verá mensagens recebidas da chamada manual para a função.

![Resultados do registo de função de chamada manual](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>Passos Seguintes

- [Estratégias para testar seu código nas funções do Azure](./functions-test-a-function.md)
- [Função do Azure Event Grid acionador Local depuração](./functions-debug-event-grid-trigger-local.md)
