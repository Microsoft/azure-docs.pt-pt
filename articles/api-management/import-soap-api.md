---
title: Importar a API SOAP com o portal do Azure portal | Microsoft Docs
description: Saiba como importar a API SOAP com a Gestão de API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 7b1fd34824bec9641edc94ce278fa21a1b57b2c0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82202745"
---
# <a name="import-soap-api"></a>Importar uma API SOAP

Este artigo mostra como importar uma representação XML padrão de uma API SOAP. O artigo mostra ainda como testar a API Management API.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Importar uma API SOAP
> * Testar a API no portal do Azure
> * Testar a API no portal do Programador

## <a name="prerequisites"></a>Pré-requisitos

Complete o seguinte quickstart: Criar uma instância de [Gestão API Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importar e publicar uma API de back-end

1. Navegue para o seu serviço de Gestão API no portal Azure e selecione **APIs** do menu.
2. Selecione **WSDL** na lista **Adicionar uma nova API**.

    ![Soap api](./media/import-soap-api/wsdl-api.png)
3. Na **Especificação de WSDL**, introduza o URL do local onde reside a API SOAP.
4. O botão de opção **Pass-through SOAP** está selecionado por predefinição. Com esta seleção, a API vai ser exposta como SOAP. O consumidor tem de utilizar as regras de SOAP. Se pretender converter a API em REST, siga os passos em [Importar uma API SOAP e convertê-la em REST](restify-soap-api.md).

    ![Pass-through](./media/import-soap-api/pass-through.png)
5. Prima o separador.

    Os campos seguintes são preenchidos com as informações da API SOAP: Nome a apresentar, Nome, Descrição.
6. Adicione um sufixo de URL de API. O sufixo é um nome que identifica esta API específica nesta instância de Gestão de API. Tem de ser único neste caso de Gestão API.
7. Publique a API ao associá-la a um produto. Neste caso, é utilizado o produto "*Unlimited*".  Se pretender que a API seja publicada e esteja disponível para programadores, adicione-a a um produto. Pode fazê-lo durante a criação da API ou defini-lo mais tarde.

    Os produtos são associações de uma ou mais APIs. Pode incluir um número de APIs e disponibilizá-las para os programadores através do portal do programador. Os programadores têm de subscrever primeiro um produto para obter acesso à API. Quando subscrevem, recebem uma chave de subscrição que é válida para qualquer API nesse produto. Se criou a instância de Gestão API, já é administrador, pelo que é subscrito a cada produto por defeito.

    Por predefinição, cada instância daAPI Management é fornecida com dois produtos de exemplo:

    * **Inicial**
    * **Ilimitado**   
8. Introduza outras definições de API. Pode definir os valores durante a criação ou configurá-los mais tarde, indo ao separador **Definições.** As definições são explicadas no Importar e publicar o seu primeiro tutorial [API.](import-and-publish.md#-import-and-publish-a-backend-api)
9. Selecione **Criar**.

### <a name="test-the-new-api-in-the-administrative-portal"></a>Testar a nova API no portal administrativo

As operações podem ser chamadas diretamente a partir do portal administrativo, que oferece uma forma conveniente para ver e testar as operações de uma API.  

1. Selecione a API que criou no passo anterior.
2. Prima o separador **Teste**.
3. Selecione uma operação.

    A página apresenta campos para os parâmetros de consulta e campos para os cabeçalhos. Um dos cabeçalhos é “Ocp-Apim-Subscription-Key”, para a chave de subscrição do produto que está associado a esta API. Se tiver criado a instância de Gestão de API, já é um administrador, pelo que a chave é preenchida automaticamente. 
1. Prima **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Transformar e proteger a sua API](transform-api.md)