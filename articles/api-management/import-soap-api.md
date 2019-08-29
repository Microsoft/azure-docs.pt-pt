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
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: 22b7b79426829b57f15263f7792dca6596c6641f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073250"
---
# <a name="import-soap-api"></a>Importar uma API SOAP

Este artigo mostra como importar uma representação XML padrão de uma API SOAP. O artigo também mostra como testar a API APIM.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Importar uma API SOAP
> * Testar a API no portal do Azure
> * Testar a API no portal do Programador

## <a name="prerequisites"></a>Pré-requisitos

Conclua o início rápido a seguir: [crie uma instância da Gestão de API do Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importar e publicar uma API de back-end

1. Selecione **APIs** em **GESTÃO DE API**.
2. Selecione **WSDL** na lista **Adicionar uma nova API**.

    ![Soap api](./media/import-soap-api/wsdl-api.png)
3. Na **Especificação de WSDL**, introduza o URL do local onde reside a API SOAP.
4. O botão de opção **Pass-through SOAP** está selecionado por predefinição. Com esta seleção, a API vai ser exposta como SOAP. O consumidor tem de utilizar as regras de SOAP. Se pretender converter a API em REST, siga os passos em [Importar uma API SOAP e convertê-la em REST](restify-soap-api.md).

    ![Pass-through](./media/import-soap-api/pass-through.png)
5. Prima o separador.

    Os campos a seguir são preenchidos com as informações da API SOAP: Nome de exibição, nome, descrição.
6. Adicione um sufixo de URL de API. O sufixo é um nome que identifica esta API específica nesta instância de APIM. Tem de ser exclusivo nesta instância de APIM.
9. Associe a API a um produto e publique-a. Neste caso, é utilizado o produto "*Unlimited*".  Se pretender que a API seja publicada e esteja disponível para programadores, adicione-a a um produto. Pode fazê-lo durante a criação da API ou defini-lo mais tarde.

    Os produtos são associações de uma ou mais APIs. Pode incluir um número de APIs e disponibilizá-las para os programadores através do portal do programador. Os programadores têm de subscrever primeiro um produto para obter acesso à API. Quando subscrevem, recebem uma chave de subscrição que é válida para qualquer API nesse produto. Se tiver criado a instância APIM, já é um administrador, pelo que tem todos os produtos subscritos por predefinição.

    Por predefinição, cada instância daAPI Management é fornecida com dois produtos de exemplo:

    * **Inicial**
    * **Ilimitado**   
10. Selecione **Criar**.

### <a name="test-the-new-apim-api-in-the-administrative-portal"></a>Testar a nova API de APIM no portal administrativo

As operações podem ser chamadas diretamente a partir do portal administrativo, que oferece uma forma conveniente para ver e testar as operações de uma API.  

1. Selecione a API que criou no passo anterior.
2. Prima o separador **Teste**.
3. Selecione uma operação.

    A página apresenta campos para os parâmetros de consulta e campos para os cabeçalhos. Um dos cabeçalhos é “Ocp-Apim-Subscription-Key”, para a chave de subscrição do produto que está associado a esta API. Se tiver criado a instância de APIM, já é um administrador, pelo que a chave é preenchida automaticamente. 
1. Prima **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

### <a name="call-operation"> </a>Chamar uma operação a partir do portal do programador

Também é possível chamar operações a partir do **portal do Programador** para testar as APIs. 

1. Selecione a API que criou no passo “Importar e publicar uma API de back-end”.
2. Prima **Portal do Programador**.

    O site "portal do Programador" abre-se.
3. Selecione a **API** que criou.
4. Clique na operação que pretende testar.
5. Prima **Experimente**.
6. Prima **Enviar**.
    
    Depois de uma operação ser invocada, o portal do programador apresenta o **Estado da resposta**, os **Cabeçalhos da resposta** e qualquer **Conteúdo da resposta**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Transformar e proteger a sua API](transform-api.md)