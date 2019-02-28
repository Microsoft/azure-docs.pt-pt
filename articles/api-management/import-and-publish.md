---
title: Importar e publicar a sua primeira API na Gestão de API do Azure | Microsoft Docs
description: Saiba como importar e publicar a sua primeira API com a Gestão de API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: apimpm
ms.openlocfilehash: 0e104f4cbdef7edc5c109781509e3924400e5ba4
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959736"
---
# <a name="import-and-publish-your-first-api"></a>Importar e publicar a sua primeira API 

Este tutorial mostra como importar uma API de back-end de “Especificação de OpenAPI” que reside no https://conferenceapi.azurewebsites.net?format=json. Esta API de back-end é fornecida pela Microsoft e alojada no Azure. 

Assim que a API de back-end é importada para a Gestão de API (APIM), a API de APIM torna-se uma fachada para a API de back-end. No momento em que importa a API de back-end, a API de origem e a API de APIM são idênticas. A APIM permite-lhe personalizar a fachada consoante as suas necessidades, sem afetar a API de back-end. Para obter mais informações, veja [Transformar e proteger a sua API](transform-api.md). 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Importar a sua primeira API
> * Testar a API no Portal do Azure
> * Testar a API no portal do Programador

![Nova API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>Pré-requisitos

+ Conhecer a [terminologia da Gestão de API do Azure](api-management-terminology.md).
+ Conclua o início rápido seguinte: [Criar uma instância de gestão de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importar e publicar uma API de back-end

Esta secção mostra como importar e publicar uma API de back-end de especificação de OpenAPI.
 
1. Selecione **APIs** em **GESTÃO DE API**.
2. Selecione **especificação de OpenAPI** na lista e clique em **completo** no pop-up.

    ![Criar uma API](./media/api-management-get-started/create-api.png)

    Pode definir os valores da API durante a criação ou mais tarde, acedendo ao separador **Definições**. O asterisco vermelho junto a um campo indica que o campo é obrigatório.

    Utilize os valores da tabela abaixo para criar a sua primeira API.

    | Definição                   | Valor                                              | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    |---------------------------|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Especificação de OpenAPI** | https://conferenceapi.azurewebsites.net?format=json | Referencia o serviço que implementa a API. A API de Gestão reencaminha os pedidos para este endereço.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
    | **Nome a apresentar**          | *API da Conferência de Demonstração*                              | Se premir o separador depois de introduzir o URL do serviço, a APIM irá preencher este campo com base no que está no json. <br/>Este nome é apresentado no portal do Programador.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **Nome**                  | *demo-conference-api*                              | Fornece um nome exclusivo para a API. <br/>Se premir o separador depois de introduzir o URL do serviço, a APIM irá preencher este campo com base no que está no json.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Descrição**           | Forneça uma descrição opcional da API.        | Se premir o separador depois de introduzir o URL do serviço, a APIM irá preencher este campo com base no que está no json.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
    | **Esquema do URL**            | *HTTPS*                                            | Determina que protocolos podem ser utilizados para aceder à API.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | **Sufixo do URL da API**        | *conferência*                                       | O sufixo é anexado ao URL base do serviço Gestão de API. A Gestão de API distingue as APIs pelo respetivo sufixo, pelo que cada API tem de ter o seu sufixo exclusivo para um determinado editor.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Produtos**              | *Ilimitado*                                        | Os produtos são associações de uma ou mais APIs. Pode incluir várias APIs num Produto e disponibilizá-las para os programadores através do portal do programador. <br/>Publique a API, ao associar a API a um produto (neste exemplo, *Ilimitado*). Para adicionar esta nova API a um produto, escreva o nome do produto (também pode fazê-lo mais tarde a partir da página **Definições**). Este passo pode ser repetido múltiplas vezes para adicionar a API a vários produtos.<br/>Para obter acesso à API, os programadores têm de subscrever primeiro um produto. Quando subscrevem, recebem uma chave de subscrição que é válida para qualquer API nesse produto. <br/> Se tiver criado a instância APIM, já é um administrador, pelo que tem todos os produtos subscritos.<br/> Por predefinição, cada instância daAPI Management é fornecida com dois produtos de exemplo: **Starter** e **ilimitado**. |
    | **Etiquetas**                  |                                                    | Etiquetas para organizar a APIs. As etiquetas podem ser utilizadas para pesquisa, agrupamento ou filtragem.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **Versão desta API?**     |                                                    | Para obter mais informações sobre o controlo de versões, veja [Publicar várias versões da sua API](api-management-get-started-publish-versions.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

    >[!NOTE]
    > Para publicar a API, precisa de associá-la a um produto. Pode fazê-lo a partir da **Página de definições**.

3. Selecione **Criar**.

> [!TIP]
> Se estiver a ter problemas na importação da sua própria definição de API, [veja a lista de problemas conhecidos e restrições](api-management-api-import-restrictions.md).

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testar a nova API de APIM no Portal do Azure

![Testar o mapa da API](./media/api-management-get-started/01-import-first-api-01.png)

As operações podem ser chamadas diretamente a partir do Portal do Azure, que fornece um meio cómodo para ver e testar as operações de uma API.

1. Selecione a API que criou no passo anterior (do separador **APIs**).
2. Prima o separador **Teste**.
3. Clique em **GetSpeakers**. A página apresenta os campos para os parâmetros de consulta; contudo, neste caso, não temos nenhum, e cabeçalhos. Um dos cabeçalhos é “Ocp-Apim-Subscription-Key”, para a chave de subscrição do produto que está associado a esta API. A chave é preenchida automaticamente.
4. Prima **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

## <a name="call-operation"> </a>Chamar uma operação a partir do portal do programador

Também é possível chamar operações do **portal do Programador** para testar as APIs.

1. Navegue para o **portal do Programador**.

    ![Portal do programador](./media/api-management-get-started/developer-portal.png)

2. Selecione **APIS**, clique em **API da Conferência de Demonstração** e, em seguida **GetSpeakers**.

    A página apresenta os campos para os parâmetros de consulta; contudo, neste caso, não temos nenhum, e cabeçalhos. Um dos cabeçalhos é “Ocp-Apim-Subscription-Key”, para a chave de subscrição do produto que está associado a esta API. Se tiver criado a instância de APIM, já é um administrador, pelo que a chave é preenchida automaticamente.

3. Prima **Experimente**.
4. Prima **Enviar**.

    Após uma operação ser invocada, o portal do programador mostra as respostas.  

## <a name="next-steps"> </a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Importar a sua primeira API
> * Testar a API no Portal do Azure
> * Testar a API no portal do Programador

Avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Criar e publicar um produto](api-management-howto-add-products.md)
