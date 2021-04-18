---
title: Importar uma App de Função Azure como API em Gestão de API
titleSuffix: Azure API Management
description: Este artigo mostra-lhe como importar uma App de Função Azure para a Azure API Management como API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/16/2021
ms.author: apimpm
ms.openlocfilehash: 8e8047fc6865bab8f4aac2315b269bf7526b1e42
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599383"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Importar uma Aplicação de Funções do Azure como uma API na Gestão de API do Azure

A Gestão de API do Azure suporta a importação de Aplicações de Funções do Azure como novas APIs ou anexa-as às APIs existentes. O processo gera automaticamente uma chave de anfitrião na Aplicação de Funções do Azure que, em seguida, é atribuída a um valor nomeado na Gestão de API do Azure.

Este artigo percorre a importação e teste de uma App de Função Azure como API na Azure API Management. 

Vai aprender a:

> [!div class="checklist"]
> * Importar uma Function App do Azure como uma API
> * Anexar uma Function App do Azure como uma API
> * Ver a nova chave de anfitrião da Function App do Azure e o valor nomeado da Gestão de API do Azure
> * Testar a API no portal do Azure

## <a name="prerequisites"></a>Pré-requisitos

* Complete o início rápido da [gestão da API Create azure.](get-started-create-service-instance.md)
* Certifique-se de que tem uma aplicação de Funções do Azure na sua subscrição. Para obter mais informações, veja [Criar uma Function App do Azure](../azure-functions/functions-get-started.md). As funções devem ter o gatilho HTTP e o nível de autorização definidos para *Anónimo* ou *Função.*

> [!NOTE]
> Pode utilizar a Extensão de Gestão da API para código de estúdio visual para importar e gerir as suas APIs. Siga o tutorial de [Extensão de Gestão da API](visual-studio-code-tutorial.md) para instalar e começar.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> Importar uma Function App do Azure como uma nova API

Siga os passos abaixo para criar uma nova API a partir de uma Function App do Azure.

1. Navegue para o seu serviço de Gestão API no portal Azure e selecione **APIs** no menu.

2. Na lista **Adicionar uma nova API**, selecione **Aplicação de Funções**.

    :::image type="content" source="./media/import-function-app-as-api/add-01.png" alt-text="Screenshot que mostra o azulejo da App de Função.":::

3. Clique em **Procurar** para selecionar as Funções a importar.

    :::image type="content" source="./media/import-function-app-as-api/add-02.png" alt-text="Screenshot que realça o botão Navegar.":::

4. Clique na secção **Function App** à sua escolha na lista de Function Apps disponíveis.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Screenshot que realça a secção 'App' de funções.":::

5. Encontre a Function App da qual pretende importar Funções, clique na mesma e prima **Selecionar**.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Screenshot que realça a App de Função que pretende importar Funções e o botão Select.":::

6. Selecione as Funções que pretende importar e clique em **Selecionar**.
    * Só é possível importar funções com base no gatilho HTTP com níveis de autorização *anónimos* ou *de função.*

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Screenshot que realça as Funções a importar e o botão Select.":::

7. Mude para a vista **Completa** e atribua o **Produto** à sua nova API. 
8. Se necessário, especifique outros campos durante a criação ou configufique-os mais tarde através do **separador Definições.** 
    * As definições são explicadas no Import e publicam o seu primeiro tutorial [da API.](import-and-publish.md#import-and-publish-a-backend-api)

    >[!NOTE]
    > Os produtos são associações de uma ou mais APIs oferecidas aos desenvolvedores através do portal de desenvolvedores. Em primeiro lugar, os desenvolvedores devem subscrever um produto para ter acesso à API. Uma vez subscritos, obtêm uma chave de subscrição para qualquer API nesse produto. Como criador da instância de Gestão da API, é administrador e é subscrito por padrão em todos os produtos.
    >
    > Cada instância de Gestão da API vem com dois produtos de amostra padrão:
    > - **Inicial**
    > - **Ilimitado**

9. Clique em **Criar**.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> Anexar uma Function App do Azure como uma API existente

Siga os passos abaixo para anexar a Function App do Azure a uma API existente.

1. Na sua instância de serviço de **Gestão de API do Azure**, selecione **APIs** no menu à esquerda.

2. Escolha a API para a qual pretende importar uma Function App do Azure. Clique em **...** e selecione **Importar** no menu de contexto.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-1.png" alt-text="Screenshot que destaca a opção menu Import.":::

3. Clique no mosaico **Function App**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-2.png" alt-text="Screenshot que realça o azulejo da App de Função.":::

4. Na janela de pop-up, clique em **Procurar**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-3.png" alt-text="Screenshot que mostra o botão Browse.":::

5. Clique na secção **Function App** à sua escolha na lista de Function Apps disponíveis.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Screenshot que destaca a lista de Apps de Função.":::

6. Encontre a Function App da qual pretende importar Funções, clique na mesma e prima **Selecionar**.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Screenshot que realça a App de Função a partir daí que pretende importar funções.":::

7. Selecione as Funções que pretende importar e clique em **Selecionar**.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Screenshot que realça as funções que gostaria de importar.":::

8. Clique **em Importar.**

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-4.png" alt-text="Anexar a partir da Function App":::

## <a name="authorization"></a><a name="authorization"></a> Autorização

A importação de uma Function App do Azure gera automaticamente:

* Chave de anfitrião dentro da App de funções com o nome apim-{*o nome da instância de serviço API Management Azure },*
* Valor nomeado dentro da instância de Gestão API Azure com o nome {*o nome da aplicação de função Azure*}-chave, que contém a chave de anfitrião criada.

Para as APIs criadas após o dia 4 de abril de 2019, a chave de anfitrião é passada em pedidos HTTP da API Management para a App de Função num cabeçalho. As APIs mais antigas passam a chave hospedeira como [parâmetro de consulta.](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization) Pode alterar este comportamento através da `PATCH Backend` [chamada REST API](/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract) na entidade *Backend* associada à App de Função.

> [!WARNING]
> Remover ou alterar o valor-chave do anfitrião da App Azure Function app ou o valor nomeado pela Azure API Management quebrará a comunicação entre os serviços. Os valores não são sincronizados automaticamente.
>
> Se precisar de rodar a chave de anfitrião, certifique-se de que o valor nomeado na Gestão de API do Azure também foi modificado.

### <a name="access-azure-function-app-host-key"></a>Chave de anfitrião da Aplicação de Funções do Azure

1. Navegue até à sua instância da Aplicação de Funções do Azure.

    :::image type="content" source="./media/import-function-app-as-api/keys-01.png" alt-text="Screenshot que realça a seleção da sua instância de aplicação Function.":::

2. Na secção **Funções** do menu de navegação lateral, selecione **as teclas de aplicação**.

    :::image type="content" source="./media/import-function-app-as-api/keys-02b.png" alt-text="Screenshot que realça a opção de definições de Aplicações de Função.":::

3. Encontre as chaves na secção **das chaves do anfitrião.**

    :::image type="content" source="./media/import-function-app-as-api/keys-03.png" alt-text="Screenshot que realça a secção 'Chaves do Anfitrião'.":::

### <a name="access-the-named-value-in-azure-api-management"></a>Aceda ao valor nomeado na Gestão de API do Azure

Navegue até à sua instância de Gestão de API do Azure e selecione **Valores nomeados** no menu à esquerda. A chave da Aplicação de Funções do Azure encontra-se lá armazenada.

:::image type="content" source="./media/import-function-app-as-api/api-named-value.png" alt-text="Adicionar a partir da Function App":::

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Teste a nova API no portal Azure

Pode chamar operações diretamente a partir do portal do Azure. Utilizar o portal do Azure é um meio cómodo de ver e testar as operações de uma API.  

:::image type="content" source="./media/import-function-app-as-api/test-api.png" alt-text="Screenshot que realça o procedimento de teste.":::

1. Selecione a API que criou na secção anterior.

2. Selecione o separador **Teste**.

3. Selecione a operação que pretende testar.

    * A página apresenta campos para parâmetros de consulta e cabeçalhos. 
    * Um dos cabeçalhos é "Ocp-Apim-Subscription-Key", para a chave de subscrição do produto associada a esta API. 
    * Como criador da instância de Gestão da API, já é administrador, pelo que a chave é preenchida automaticamente. 

4. Selecione **Enviar**.

    * Quando o teste tiver sucesso, a parte traseira responde com **200 OK** e alguns dados.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Transformar e proteger a sua API](transform-api.md)
