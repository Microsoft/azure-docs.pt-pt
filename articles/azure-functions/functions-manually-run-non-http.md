---
title: Executar manualmente uma função Azure não ativada por HTTP
description: Utilize um pedido http para executar uma função azure não-HTTP desencadeada
author: craigshoemaker
ms.topic: tutorial
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 4ce7b8590e4718585fe841921466e049dc204928
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75769137"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Executar manualmente uma função não acionada por HTTP

Este artigo demonstra como executar manualmente uma função não ativada por HTTP através de um pedido http especialmente formatado.

Em alguns contextos, poderá ser necessário executar uma Função Azure "on-demand" que é indiretamente desencadeada.  Exemplos de gatilhos indiretos incluem [funções num horário](./functions-create-scheduled-function.md) ou funções que funcionam como resultado da ação de [outro recurso](./functions-create-storage-blob-triggered-function.md). 

[O carteiro](https://www.getpostman.com/) é utilizado no seguinte exemplo, mas pode utilizar [cURL,](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler) ou qualquer outra ferramenta similar para enviar pedidos HTTP.

## <a name="define-the-request-location"></a>Definir a localização do pedido

Para executar uma função não ativada pelo HTTP, precisa de uma forma de enviar um pedido ao Azure para executar a função. O URL usado para fazer este pedido assume um formulário específico.

![Defina o local do pedido: nome do anfitrião + caminho da pasta + nome de função](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Nome do anfitrião:** A localização pública da aplicação de funções que é composta pelo nome da aplicação de funções mais *azurewebsites.net* ou o seu domínio personalizado.
- **Caminho da pasta:** Para aceder a funções não ativadas pelo HTTP através de um pedido HTTP, tem de enviar o pedido através das pastas *de administração/funções*.
- **Nome da função:** O nome da função que quer executar.

Você usa este local de pedido no Carteiro juntamente com a chave principal da função no pedido ao Azure para executar a função.

> [!NOTE]
> Quando se corre localmente, a chave principal da função não é necessária. Pode ligar diretamente para a `x-functions-key` [função](#call-the-function) omitindo o cabeçalho.

## <a name="get-the-functions-master-key"></a>Obtenha a chave principal da função

Navegue para a sua função no portal Azure e clique em **Gerir** e encontrar a secção Chaves do **Anfitrião.** Clique no botão **Copiar** na linha *_master* para copiar a chave principal para a sua área de redação.

![Chave principal de cópia do ecrã de Gestão de Funções](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

Depois de copiar a chave principal, clique no nome da função para voltar à janela de ficheiros de código. Em seguida, clique no separador **Logs.** Verá mensagens da função registada aqui quando executar manualmente a função do Carteiro.

> [!CAUTION]  
> Devido às permissões elevadas na sua aplicação de funções concedidas pela chave principal, não deve partilhar esta chave com terceiros ou distribuí-la numa aplicação.

## <a name="call-the-function"></a>Ligue para a função

Abra o Carteiro e siga estes passos:

1. Insira o local de **pedido na caixa de texto URL**.
2. Certifique-se de que o método HTTP está definido para **POST**.
3. **Clique** no separador **Cabeçalhos.**
4. Introduza a **chave x-funções** como a primeira **chave** e colhe a chave principal (a partir da área de reparação) na caixa de **valor.**
5. Introduza o **Tipo de Conteúdo** como segunda **chave** e introduza **a** **aplicação/json** como valor .

    ![Definições de cabeçalhos do carteiro](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

6. **Clique** no separador **Body.**
7. Introduza **{"entrada": "teste" }** como o corpo para o pedido.

    ![Definições do corpo do carteiro](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

8. Clique em **Enviar**.

    ![Envio de um pedido com carteiro](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

O carteiro reporta então um estatuto de **202 Aceite.**

Em seguida, volte à sua função no portal Azure. Localize a janela *Registos* e verá mensagens provenientes da chamada manual para a função.

![Resultados do registo de funções da chamada manual](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>Passos seguintes

- [Estratégias para testar o seu código nas Funções do Azure](./functions-test-a-function.md)
- [Rede de eventos de função azure gatilho depuração local](./functions-debug-event-grid-trigger-local.md)
