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
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: f66395b1e0f45f1e80cd0ac93bf8c9ae8674a0f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732967"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Importar uma Aplicação de Funções do Azure como uma API na Gestão de API do Azure

A Gestão de API do Azure suporta a importação de Aplicações de Funções do Azure como novas APIs ou anexa-as às APIs existentes. O processo gera automaticamente uma chave de anfitrião na Aplicação de Funções do Azure que, em seguida, é atribuída a um valor nomeado na Gestão de API do Azure.

Este artigo explica como importar uma Function App do Azure como uma API na Gestão de API do Azure. Também descreve o processo de teste.

Vai aprender a:

> [!div class="checklist"]
> * Importar uma Function App do Azure como uma API
> * Anexar uma Function App do Azure como uma API
> * Ver a nova chave de anfitrião da Function App do Azure e o valor nomeado da Gestão de API do Azure
> * Testar a API no portal do Azure
> * Testar a API no portal do programador

## <a name="prerequisites"></a>Pré-requisitos

* Concluir o início rápido [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
* Certifique-se de que tem uma aplicação de Funções do Azure na sua subscrição. Para obter mais informações, veja [Criar uma Function App do Azure](../azure-functions/functions-get-started.md). Ela deve conter Funções com o acionador HTTP e a definição de nível de autorização definida como *Anónimo* ou *Função*.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> Importar uma Function App do Azure como uma nova API

Siga os passos abaixo para criar uma nova API a partir de uma Function App do Azure.

1. Navegue para o seu serviço de Gestão API no portal Azure e selecione **APIs** no menu.

2. Na lista **Adicionar uma nova API**, selecione **Aplicação de Funções**.

    ![Screenshot que mostra o azulejo da App de Função.](./media/import-function-app-as-api/add-01.png)

3. Clique em **Procurar** para selecionar as Funções a importar.

    ![Screenshot que realça o botão Navegar.](./media/import-function-app-as-api/add-02.png)

4. Clique na secção **Function App** à sua escolha na lista de Function Apps disponíveis.

    ![Screenshot que realça a secção 'App' de funções.](./media/import-function-app-as-api/add-03.png)

5. Encontre a Function App da qual pretende importar Funções, clique na mesma e prima **Selecionar**.

    ![Screenshot que realça a App de Função que pretende importar Funções e o botão Select.](./media/import-function-app-as-api/add-04.png)

6. Selecione as Funções que pretende importar e clique em **Selecionar**.

    ![Screenshot que realça as Funções a importar e o botão Select.](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > Pode importar apenas as Funções que são baseadas no acionador HTTP e ter a definição do nível de autorização definido como *Anónimo* ou *Função*.

7. Mude para a vista **Completa** e atribua o **Produto** à sua nova API. Se necessário, especifique outros campos durante a criação ou configufique-os mais tarde, indo ao **separador Definições.** As definições são explicadas no Import e publicam o seu primeiro tutorial [da API.](import-and-publish.md#import-and-publish-a-backend-api)
8. Clique em **Criar**.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> Anexar uma Function App do Azure como uma API existente

Siga os passos abaixo para anexar a Function App do Azure a uma API existente.

1. Na sua instância de serviço de **Gestão de API do Azure**, selecione **APIs** no menu à esquerda.

2. Escolha a API para a qual pretende importar uma Function App do Azure. Clique em **...** e selecione **Importar** no menu de contexto.

    ![Screenshot que destaca a opção menu Import.](./media/import-function-app-as-api/append-01.png)

3. Clique no mosaico **Function App**.

    ![Screenshot que realça o azulejo da App de Função.](./media/import-function-app-as-api/append-02.png)

4. Na janela de pop-up, clique em **Procurar**.

    ![Screenshot que mostra o botão Browse.](./media/import-function-app-as-api/append-03.png)

5. Clique na secção **Function App** à sua escolha na lista de Function Apps disponíveis.

    ![Screenshot que destaca a lista de Apps de Função.](./media/import-function-app-as-api/add-03.png)

6. Encontre a Function App da qual pretende importar Funções, clique na mesma e prima **Selecionar**.

    ![Screenshot que realça a App de Função a partir daí que pretende importar funções.](./media/import-function-app-as-api/add-04.png)

7. Selecione as Funções que pretende importar e clique em **Selecionar**.

    ![Screenshot que destaca os functnios que você gostaria de importar.](./media/import-function-app-as-api/add-05.png)

8. Clique **em Importar.**

    ![Anexar a partir da Function App](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a><a name="authorization"></a> Autorização

A importação de uma Function App do Azure gera automaticamente:

* Chave de anfitrião dentro da App de funções com o nome apim-{*o nome da instância de serviço API Management Azure },*
* Valor nomeado dentro da instância de Gestão API Azure com o nome {*o nome da aplicação de função Azure*}-chave, que contém a chave de anfitrião criada.

Para as APIs criadas após o dia 4 de abril de 2019, a chave de anfitrião é passada em pedidos HTTP da API Management para a App de Função num cabeçalho. As APIs mais antigas passam a chave hospedeira como [parâmetro de consulta.](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization) Este comportamento pode ser alterado através da `PATCH Backend` [chamada REST API](/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract) na entidade *Backend* associada à App de Função.

> [!WARNING]
> Remover ou alterar o valor da chave de anfitrião da Aplicação de Funções do Azure ou o valor nomeado da Gestão de API do Azure irá interromper a comunicação entre os serviços. Os valores não são sincronizados automaticamente.
>
> Se precisar de rodar a chave de anfitrião, certifique-se de que o valor nomeado na Gestão de API do Azure também foi modificado.

### <a name="access-azure-function-app-host-key"></a>Chave de anfitrião da Aplicação de Funções do Azure

1. Navegue até à sua instância da Aplicação de Funções do Azure.

2. Selecione **definições da Aplicação de Funções** na descrição geral.

    ![Screenshot que realça a opção de definições de Aplicações de Função.](./media/import-function-app-as-api/keys-02-a.png)

3. A chave encontra-se na secção **Chaves de Anfitrião**.

    ![Screenshot que realça a secção 'Chaves do Anfitrião'.](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>Aceda ao valor nomeado na Gestão de API do Azure

Navegue até à sua instância de Gestão de API do Azure e selecione **Valores nomeados** no menu à esquerda. A chave da Aplicação de Funções do Azure encontra-se lá armazenada.

![Adicionar a partir da Function App](./media/import-function-app-as-api/keys-01.png)

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Teste a nova API no portal Azure

Pode chamar operações diretamente a partir do portal do Azure. Utilizar o portal do Azure é um meio cómodo de ver e testar as operações de uma API.  

1. Selecione a API que criou na secção anterior.

2. Selecione o separador **Teste**.

3. Selecione uma operação.

    A página apresenta campos para os parâmetros de consulta e campos para os cabeçalhos. Um dos cabeçalhos é **a Chave de Subscrição Ocp-Apim,** para a chave de subscrição do produto que está associada a esta API. Se tiver criado a instância de Gestão de API, já é um administrador, pelo que a chave é preenchida automaticamente. 

4. Selecione **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Transformar e proteger a sua API](transform-api.md)
