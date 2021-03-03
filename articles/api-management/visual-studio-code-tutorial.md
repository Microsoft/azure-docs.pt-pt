---
title: Tutorial - Importar e gerir APIs - Azure API Management e Visual Studio Code | Microsoft Docs
description: Neste tutorial, aprenda a usar a Extensão de Gestão da API Azure para o Código do Estúdio Visual para importar, testar e gerir APIs.
ms.service: api-management
author: dlepow
ms.author: apimpm
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: 0dea8e43d5f09b84c5795bc257cf3331ad919fcb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649551"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>Tutorial: Utilize a Extensão de Gestão da API para código de estúdio visual para importar e gerir APIs

Neste tutorial, aprende-se a utilizar a Extensão de Gestão da API para Código de Estúdio Visual para operações comuns na Gestão da API. Utilize o ambiente familiar do Código do Estúdio Visual para importar, atualizar, testar e gerir APIs.

Saiba como:

> [!div class="checklist"]
> * Importar uma API para a API Management
> * Editar a API
> * Aplicar políticas de gestão de API
> * Testar a API


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="API na Extensão de Gestão da API":::

Para uma introdução a funcionalidades adicionais de Gestão da API, consulte os tutoriais de Gestão da API utilizando o [portal Azure.](import-and-publish.md)

## <a name="prerequisites"></a>Pré-requisitos
- Compreender [a terminologia da Gestão da API Azure](api-management-terminology.md)
- Certifique-se de que instalou o [Código do Estúdio Visual](https://code.visualstudio.com/) e a mais recente [extensão de gestão da API para código de estúdio visual](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)
- [Criar um caso de Gestão de API](vscode-create-service-instance.md)

## <a name="import-an-api"></a>Importar uma API

O exemplo a seguir importa uma Especificação OpenAPI em formato JSON para a Gestão da API. A Microsoft fornece o backend API usado neste exemplo, e acolhe-o no Azure em `https://conferenceapi.azurewebsites.net?format=json` .

1. No Código do Estúdio Visual, selecione o ícone Azure a partir da Barra de Atividades.
1. No painel Explorer, expanda o caso de Gestão API que criou.
1. **APIs** de clique à direita e selecione **Import from OpenAPI Link**. 
1. Quando solicitado, insira os seguintes valores:
    1. Uma **ligação OpenAPI** para conteúdos no formato JSON. Para este exemplo: *https://conferenceapi.azurewebsites.net?format=json* .
    Este URL é o serviço que implementa o exemplo API. A API Management remete pedidos para este endereço.
    1. Um **nome API**, como *demo-conference-api,* que é único no caso de Gestão da API. Este nome só pode conter letras, número e hífens. Os primeiros e últimos caracteres devem ser alfanuméricos. Este nome é usado no caminho para chamar a API.

Após a API ser importada com sucesso, aparece no painel explorer, e as operações disponíveis da API aparecem no nó **operações.**

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="API importada no painel explorer":::

## <a name="edit-the-api"></a>Editar a API

Pode editar a API no Código do Estúdio Visual. Por exemplo, edite a descrição do Gestor de Recursos JSON da API na janela do editor para remover o protocolo **http** utilizado para aceder à API. Em seguida, selecione  >  **'Guardar ficheiros'.**

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="Editar descrição do JSON":::

Para editar o formato OpenAPI, clique com o botão direito no painel Dese do Explorer e **selecione Editar OpenAPI**. Faça as alterações e, em seguida, selecione  >  **'Guardar ficheiros'.**

## <a name="apply-policies-to-the-api"></a>Aplicar políticas à API 

A API Management fornece [políticas](api-management-policies.md) que pode configurar para as suas APIs. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou na resposta de uma API. As políticas podem ser globais, que se aplicam a todas as APIs no seu caso de Gestão de API, ou podem ser telescópios para uma operação específica de API ou API.

Esta secção mostra como aplicar algumas políticas comuns de saída à sua API que transformam a resposta da API. As políticas neste exemplo mudam os cabeçalhos de resposta e escondem URLs originais de backend que aparecem no corpo de resposta.

1. No painel Explorer, selecione **Política** sob a *demo-conference-api* que importou. O ficheiro de política abre na janela do editor. Este ficheiro configura políticas para todas as operações na API. 

1. Atualizar o ficheiro com o seguinte conteúdo no `<outbound>` elemento:
    ```html
    [...]
    <outbound>
        <set-header name="Custom" exists-action="override">
            <value>"My custom value"</value>
        </set-header>
        <set-header name="X-Powered-By" exists-action="delete" />
        <redirect-content-urls />
        <base />
    </outbound>
    [...]
    ```

    * A primeira `set-header` política adiciona um cabeçalho de resposta personalizado para fins de demonstração.
    * A segunda `set-header` política elimina o **cabeçalho X-Powered-By,** se existir. Este cabeçalho pode revelar a estrutura de aplicação utilizada no backend da API, e os editores muitas vezes removem-no.
    * A `redirect-content-urls` política reescreve (máscaras) ligações no organismo de resposta para que apitam para os links equivalentes através do gateway de Gestão da API.
    
1. Guarde o ficheiro. Se for solicitado, **selecione Upload** para carregar o ficheiro para a nuvem.

## <a name="test-the-api"></a>Testar a API

### <a name="get-the-subscription-key"></a>Obtenha a chave de subscrição

Para testar a API importada que importou e as políticas que são aplicadas, precisa de uma chave de subscrição para o seu exemplo de Gestão de API.

1. No painel Explorer, clique com o botão direito no nome da sua instância de Gestão API.
1. Selecione **a chave de subscrição de cópias**.

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="Chave de subscrição de cópia":::

### <a name="test-an-api-operation"></a>Testar uma operação API

1. No painel Explorer, expanda o nó **operações** sob a *demo-conference-api* que importou.
1. Selecione uma operação como *GetSpeakers*.
1. Na janela do editor, junto à **chave de subscrição Ocp-Apim, substitua-a** `{{SubscriptionKey}}` pela chave de subscrição que copiou.
1. Selecione **Enviar pedido**. 

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="Enviar pedido de API a partir do Código do Estúdio Visual":::

Quando o pedido é bem sucedido, o backend responde com **200 OK** e alguns dados.

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="Operação de teste da API":::

Note os seguintes detalhes na resposta:
* O cabeçalho **personalizado** é adicionado à resposta.
* O **cabeçalho X-Powered-By** não aparece na resposta.
* Neste caso, os URLs para o backend da API são redirecionados para o portal de gestão da `https://apim-hello-world.azure-api.net/demo-conference-api` API.

### <a name="trace-the-api-operation"></a>Rastrear a operação da API

Para obter informações detalhadas sobre o rastreio para ajudá-lo a depurar a operação API, selecione o link que aparece ao lado **do Ocp-APIM-Trace-Location**. 

O ficheiro JSON naquele local contém informações de inbound, Backend e Outbound para que possa determinar onde ocorrer qualquer problema após o pedido ser feito.

> [!TIP]
> Ao testar as operações da API, a Extensão de Gestão da API permite [a depuragem](api-management-debug-policies.md) de políticas opcionais (disponível no nível de serviço do Desenvolvedor).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, remova a instância de Gestão da API clicando à direita e selecionando **Open in Portal** para eliminar o serviço de [Gestão API](get-started-create-service-instance.md#clean-up-resources) e o seu grupo de recursos.

Em alternativa, pode selecionar **eliminar a Gestão da API** apenas para eliminar a instância de Gestão da API (esta operação não elimina o seu grupo de recursos).

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="Eliminar instância de Gestão de API do Código VS":::

## <a name="next-steps"></a>Passos seguintes

Este tutorial introduziu várias funcionalidades da Extensão de Gestão da API para o Código do Estúdio Visual que pode utilizar para importar e gerir APIs. Aprendeu a:

> [!div class="checklist"]
> * Importar uma API para a API Management
> * Editar a API
> * Aplicar políticas de gestão de API
> * Testar a API

A Extensão de Gestão da API fornece funcionalidades adicionais para trabalhar com as suas APIs. Por exemplo, [depurar polícias](api-management-debug-policies.md) (disponíveis no nível de serviço developer), ou criar e gerir [valores nomeados](api-management-howto-properties.md).