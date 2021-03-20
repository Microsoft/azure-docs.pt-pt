---
title: Executar manualmente uma função Azure não acionada por HTTP
description: Utilize um pedido HTTP para executar uma função Azure não-HTTP desencadeada
author: craigshoemaker
ms.topic: article
ms.date: 04/23/2020
ms.author: cshoe
ms.openlocfilehash: 79aebf7ed80fea370ff7a5d5cc40911da4144414
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537706"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Executar manualmente uma função não acionada por HTTP

Este artigo demonstra como executar manualmente uma função não acionada por HTTP através de um pedido HTTP especialmente formatado.

Em alguns contextos, poderá ser necessário executar uma Função Azure "a pedido" que seja ativada indiretamente.  Exemplos de gatilhos indiretos incluem [funções num horário](./functions-create-scheduled-function.md) ou funções que funcionam como resultado da ação de [outro recurso.](./functions-create-storage-blob-triggered-function.md) 

[O carteiro](https://www.getpostman.com/) é utilizado no seguinte exemplo, mas pode utilizar [cURL,](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler) ou qualquer outra ferramenta similar para enviar pedidos HTTP.

## <a name="define-the-request-location"></a>Definir o local do pedido

Para executar uma função não acionada por HTTP, precisa de uma forma de enviar um pedido ao Azure para executar a função. O URL usado para fazer este pedido requer um formulário específico.

![Defina o local do pedido: nome de anfitrião + caminho de pasta + nome da função](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Nome do anfitrião:** A localização pública da aplicação de função que é composta pelo nome da aplicação de função mais *azurewebsites.net* ou o seu domínio personalizado.
- **Caminho da pasta:** Para aceder a funções não desencadeadas por HTTP através de um pedido HTTP, tem de enviar o pedido através das *pastas administradas/funções*.
- **Nome da função:** O nome da função que quer executar.

Utilize esta localização de pedido no Carteiro juntamente com a chave principal da função no pedido ao Azure para executar a função.

> [!NOTE]
> Ao funcionar localmente, a chave principal da função não é necessária. Pode ligar diretamente [para a função](#call-the-function) omitindo o `x-functions-key` cabeçalho.

## <a name="get-the-functions-master-key"></a>Obtenha a chave principal da função

1. Navegue para a sua aplicação de função no [portal Azure,](https://portal.azure.com)selecione **'App Keys'** e, em seguida, a `_master` chave. 

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key.png" alt-text="Localize a chave principal para copiar." border="true":::

1. Na secção **chave Editar,** copie o valor da chave para a sua área de transferência e, em seguida, selecione **OK**.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-copy.png" alt-text="Copie a chave principal da pasta." border="true":::

1. Depois de copiar a tecla *_master,* selecione **Código + Teste** e, em seguida, selecione **Registos**. Verá mensagens da função registada aqui quando executar manualmente a função do Carteiro.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-function-log.png" alt-text="Screenshot que mostra a página 'Código + Teste' com uma mensagem dos registos apresentados." border="true":::

> [!CAUTION]  
> Devido às permissões elevadas na sua aplicação de função concedida pela chave principal, não deve partilhar esta chave com terceiros ou distribuí-la numa aplicação. A chave só deve ser enviada para um ponto final HTTPS.

## <a name="call-the-function"></a>Chamar a função

Abra o Carteiro e siga estes passos:

1. Introduza o local do **pedido na caixa de texto URL**.
1. Certifique-se de que o método HTTP está definido para **POST**.
1. Selecione o **separador Cabeçalhos.**
1. Tipo **x-funções-chave** como a primeira chave e cole a tecla principal (da área de transferência) como o valor.
1. Digite **o Tipo de Conteúdo** como a segunda chave e tipo de **aplicação/json** como o valor.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png" alt-text="Configurações de cabeçalhos do carteiro." border="true":::

1. Selecione o **separador Corpo.**
1. Tipo **{ "entrada": "teste" }** como o corpo para o pedido.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png" alt-text="Configurações do corpo do carteiro." border="true":::

1. Selecione **Enviar**.
        
    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png" alt-text="Envie um pedido com o Carteiro." border="true":::

    O carteiro relata então um estatuto de **202 Aceitos.**

1. Em seguida, volte à sua função no portal Azure. Reveja os registos e verá mensagens provenientes da chamada manual para a função.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-logs.png" alt-text="Veja os registos para ver os resultados dos testes da chave principal." border="true":::

## <a name="next-steps"></a>Passos seguintes

- [Estratégias para testar o seu código nas Funções do Azure](./functions-test-a-function.md)
- [Grelha de evento de função Azure desencadeia depuração local](./functions-debug-event-grid-trigger-local.md)
