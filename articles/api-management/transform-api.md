---
title: Tutorial - Transforme e proteja a sua API na Azure API Management / Microsoft Docs
description: Neste tutorial, aprende-se a proteger a sua API em Gestão de API com políticas de transformação e estrangulamento (limitação de taxas).
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/28/2020
ms.author: apimpm
ms.openlocfilehash: 979bdaa1e0dac4f45a321abda2a208f46983f9cd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010236"
---
# <a name="tutorial-transform-and-protect-your-api"></a>Tutorial: Transforme e proteja a sua API

O tutorial mostra como transformar a sua API para que não revele informações sobre o backend privado. Por exemplo, é melhor esconder a informação sobre a pilha de tecnologia que está a funcionar no backend. Também pode querer esconder URLs originais que aparecem no corpo da resposta HTTP da API e, em vez disso, redirecioná-los para o gateway APIM.

Este tutorial também mostra como é fácil adicionar proteção para a sua API backend, configurando um limite de taxa com a Azure API Management. Por exemplo, pode querer limitar a taxa de chamadas API para que a API não seja sobreutilizada pelos desenvolvedores. Para mais informações, consulte [as políticas de Gestão da API.](api-management-policies.md)

Neste tutorial, vai aprender a:

> [!div class="checklist"]
>
> -   Transformar uma API para remover os cabeçalhos de resposta
> -   Substituir os URLs originais no corpo de resposta da API pelos URLs de gateway de APIM
> -   Proteger uma API adicionando uma política de limite de taxa (estrangulamento)
> -   Testar as transformações

:::image type="content" source="media/transform-api/api-management-management-console.png" alt-text="Políticas no portal":::

## <a name="prerequisites"></a>Pré-requisitos

-   Conhecer a [terminologia da Gestão de API do Azure](api-management-terminology.md).
-   Compreender o [conceito das políticas da Gestão de API do Azure](api-management-howto-policies.md).
-   Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
-   Conclua também o tutorial seguinte: [Importar e publicar a sua primeira API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Transformar uma API para remover os cabeçalhos de resposta

Esta secção mostra como esconder os cabeçalhos HTTP que não pretende mostrar aos seus utilizadores. Este exemplo mostra como eliminar os seguintes cabeçalhos na resposta HTTP:

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>Testar a resposta original

Para ver a resposta original:

1. Na sua instância de serviço de Gestão API, selecione **APIs**.
1. Selecione API de **Conferência de Demonstração** da sua lista de API.
1. Selecione o separador **Teste,** na parte superior do ecrã.
1. Selecione a operação **GetSpeakers** e selecione **Enviar**.

A resposta original deve ser semelhante à seguinte:

:::image type="content" source="media/transform-api/original-response.png" alt-text="Resposta original da API":::

Como pode ver, a resposta inclui os cabeçalhos **X-AspNet-Version** e **X-Powered-By.**

### <a name="set-the-transformation-policy"></a>Definir a política de transformação

1. Selecione **Demo Conference API**  >  **Design**  >  **Todas as operações**.
4. Na secção **de processamento outbound,** selecione o ícone do editor de código **</>** ()

   :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png" alt-text="Navegar para a política de saída" border="false":::

1. Posicione o cursor dentro do elemento **&lt; de saída &gt;** e selecione Mostrar os **cortes** no canto superior direito.
1. Na janela direita, sob **as políticas de transformação,** selecione **set HTTP duas** vezes (para inserir dois cortes de política).

   :::image type="content" source="media/transform-api/transform-api.png" alt-text="Definir política de cabeçalho HTTP":::

1. Modifique o seu **\<outbound>** código para ficar assim:

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   :::image type="content" source="media/transform-api/set-policy.png" alt-text="Definir cabeçalho HTTP":::

1. Selecione **Guardar**.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Substituir os URLs originais no corpo de resposta da API pelos URLs de gateway de APIM

Esta secção mostra como esconder URLs originais que aparecem no corpo da resposta HTTP da API e, em vez disso, redirecionam-nos para o gateway da APIM.

### <a name="test-the-original-response"></a>Testar a resposta original

Para ver a resposta original:

1. Selecione **Demo Conference API**  >  **Test**.
1. Selecione a operação **GetSpeakers** e selecione **Enviar**.

    Como pode ver, a resposta inclui os URLs de backend originais:

    :::image type="content" source="media/transform-api/original-response2.png" alt-text="URLs originais em resposta":::


### <a name="set-the-transformation-policy"></a>Definir a política de transformação

1.  Selecione **Demo Conference API**  >  **Todos os projetos de operações.**  >  **Design**
1.  Na secção **de processamento outbound,** selecione o ícone do editor de código **</>** ()
1.  Posicione o cursor dentro do elemento **&lt; de saída &gt;** e selecione Mostrar os **cortes** no canto superior direito.
1.  Na janela direita, sob as **políticas de transformação,** selecione **URLs de máscara em conteúdo**. 
1.  Selecione **Guardar**.

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Proteger uma API ao adicionar a política de limite de taxas (limitação)

Esta secção mostra como adicionar proteção à API de back-end através da configuração de limites de taxas. Por exemplo, pode querer limitar a taxa de chamadas API para que a API não seja sobreutilizada pelos desenvolvedores. Neste exemplo, o limite é definido para 3 chamadas por 15 segundos para cada ID de subscrição. Após os 15 segundos, um programador pode tentar chamar a API novamente.

1.  Selecione **Demo Conference API**  >  **Todos os projetos de operações.**  >  **Design**
1.  Na secção **de processamento de entrada,** selecione o ícone do editor de código **</>** ()
1.  Posicione o cursor dentro do elemento **&lt; de entrada &gt;** e selecione Mostrar os **cortes** no canto superior direito.

    :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png" alt-text="Definir a política de entrada" border="false":::

1.  Na janela direita, ao abrigo das **políticas de restrição de acesso,** selecione **+ Limite a taxa de chamada por tecla**.
1.  Modifique o seu código **limite de taxa por chave** (no **\<inbound\>** elemento) para o seguinte código:

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>Testar as transformações

Neste momento, se olharmos para o código no editor de código, as tuas políticas são assim:

   ```
   <policies>
      <inbound>
        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
        <base />
      </inbound>
      <backend>
        <base />
      </backend>
      <outbound>
        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
        <redirect-content-urls />
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

O resto desta secção testa as transformações de política que definiu neste artigo.

### <a name="test-the-stripped-response-headers"></a>Testar os cabeçalhos de resposta removidos

1. Selecione **Demo Conference API**  >  **Test**.
1. Selecione a operação **GetSpeakers** e selecione **Enviar**.

    Como pode ver, os cabeçalhos foram despojados:

    :::image type="content" source="media/transform-api/final-response1.png" alt-text="Cabeçalhos de resposta despidos":::

### <a name="test-the-replaced-url"></a>Testar o URL substituído

1. Selecione **Demo Conference API**  >  **Test**.
1. Selecione a operação **GetSpeakers** e selecione **Enviar**.

    Como pode ver, a URL foi substituída.

    :::image type="content" source="media/transform-api/final-response2.png" alt-text="URL substituído":::

### <a name="test-the-rate-limit-throttling"></a>Testar o limite de taxa (limitação)

1. Selecione **Demo Conference API**  >  **Test**.
1. Selecione a operação **GetSpeakers**. Selecione **Enviar** três vezes seguidas.

    Depois de enviar o pedido 3 vezes, obtém a **resposta de muitos pedidos 429.**

    :::image type="content" source="media/transform-api/test-throttling.png" alt-text="Muitos pedidos":::

1. Aguarde mais ou menos 15 segundos e selecione **Enviar** novamente. Neste momento, deve obter uma resposta **200 OK**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> -   Transformar uma API para remover os cabeçalhos de resposta
> -   Substituir os URLs originais no corpo de resposta da API pelos URLs de gateway de APIM
> -   Proteger uma API ao adicionar a política de limite de taxas (limitação)
> -   Testar as transformações

Avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Monitorizar a sua API](api-management-howto-use-azure-monitor.md)
