---
title: Importe e publique a sua primeira API na Azure API Management
description: Saiba como importar uma API de Especificação OpenAPI para a Azure API Management e teste a sua API no portal Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 38b723cb823547d4737a68576575c2b629f5eb67
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206282"
---
# <a name="import-and-publish-your-first-api"></a>Importar e publicar a sua primeira API

Este tutorial mostra como importar uma Especificação OpenAPI API em formato JSON para a Azure API Management. A Microsoft fornece a API de backend e acolhe-a no Azure em [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json) .

Uma vez importa a API backend para a API Management, a sua API Management API Management torna-se uma fachada para a API backend. Pode personalizar a fachada às suas necessidades na Gestão da API sem tocar na API de backend. Para obter mais informações, veja [Transformar e proteger a sua API](transform-api.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Importar uma API para a API Management
> * Testar a API no portal do Azure

![Nova API](./media/api-management-import-and-publish/created-api.png)

## <a name="prerequisites"></a>Pré-requisitos

- Compreender [a terminologia da Gestão API da Azure.](api-management-terminology.md)
- [Criar um exemplo de Gestão API Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a><a name="create-api"> </a>Importar e publicar uma API de backend

Esta secção mostra como importar e publicar uma API de backend de especificações OpenAPI.

1. Na navegação à esquerda da sua instância de Gestão de API, selecione **APIs** na secção de Gestão da **API.**
1. Selecione o azulejo **OpenAPI** e, em seguida, selecione **Full** no ecrã pop-up.
1. No ecrã **de especificações OpenAPI,** utilize os valores da tabela seguinte para criar a sua API.

   Uma estrela vermelha ao lado de um campo no formulário indica que o campo é necessário. Pode definir os valores de API durante a criação ou mais tarde indo para o **separador Definições.**

   ![Criar uma API](./media/api-management-import-and-publish/create-api.png)

   |Definição|Valor|Descrição|
   |-------|-----|-----------|
   |**Especificação OpenAPI**|*https: \/ /conferenceapi.azurewebsites.net?format=json*|O serviço de implementação da API. A API de Gestão reencaminha os pedidos para este endereço.|
   |**Nome a apresentar**|Depois de introduzir o URL de serviço anterior, a API Management preenche este campo com base no JSON.|O nome apresentado no portal do desenvolvedor.|
   |**Name**|Depois de introduzir o URL de serviço anterior, a API Management preenche este campo com base no JSON.|Um nome único para a API.|
   |**Descrição**|Depois de introduzir o URL de serviço anterior, a API Management preenche este campo com base no JSON.|Uma descrição opcional da API.|
   |**Esquema do URL**|**HTTPS**|Quais os protocolos que podem ser usados para aceder à API.|
   |**Sufixo do URL da API**|*conferência*|O sufixo anexado ao URL base para o serviço de Gestão da API. A API Management distingue as APIs pelo seu sufixo, pelo que o sufixo deve ser único para cada API para um determinado editor.|
   |**Etiquetas**| |Etiquetas para organizar APIs para pesquisa, agrupamento ou filtragem.|
   |**Produtos**|**Ilimitado**|Associação de uma ou mais APIs. Cada instância de Gestão da API vem com dois produtos de amostra: **Starter** e **Unlimited**. Publica uma API associando a API a um produto, **Ilimitado** neste exemplo.<br/>Pode incluir várias APIs num produto e oferecê-las aos desenvolvedores através do portal de desenvolvedores. Para adicionar esta API a outro produto, escreva ou selecione o nome do produto. Repita este passo para adicionar a API a vários produtos. Também pode adicionar APIs aos produtos mais tarde a partir da página **Definições.**<br/>Para obter acesso à API, os programadores têm de subscrever primeiro um produto. Quando subscrevem, recebem uma chave de subscrição que é boa para qualquer API nesse produto. <br/>Se criou a instância de Gestão da API, já é administrador, por isso está a subscrever todos os produtos do caso.|
   |**Gateways**|**Gerido**|Gateway(s) API que expõe a API. Este campo está disponível apenas nos serviços **de Nível Developer** e **Premium.**<br/>**Gateway gerido** indica o gateway incorporado no serviço de Gestão API e hospedado pela Microsoft em Azure. Outros gateways são [portais auto-hospedados](self-hosted-gateway-overview.md) e estão disponíveis apenas nos níveis de serviço Premium e Developer. Pode implantá-los nas instalações ou noutras nuvens.<br/>Se não forem selecionados gateways, a API não estará disponível e os seus pedidos de API não serão bem sucedidos.|
   |**Controlar a versão desta API?**|Selecione ou desmarca|Para obter mais informações sobre a versão, consulte [publicar várias versões da sua API.](api-management-get-started-publish-versions.md)|

   > [!NOTE]
   > Para publicar a API aos consumidores da API, deve associá-la a um produto.

2. Selecione **Criar**.

Se tiver problemas em importar uma definição de API, consulte a [lista de questões e restrições conhecidas.](api-management-api-import-restrictions.md)

## <a name="test-the-new-api-in-the-azure-portal"></a>Teste a nova API no portal Azure

Você pode ligar para as operações da API diretamente a partir do portal Azure, que fornece uma maneira conveniente de ver e testar as operações.

1. Na navegação à esquerda da sua instância de Gestão de API, selecione **APIs** na secção **de Gestão** da API e, em seguida, selecione **API da Conferência de Demonstração**.
1. Selecione o separador **Teste** e, em seguida, selecione **GetSpeakers**. A página mostra **parâmetros de consulta** e **cabeçalhos,** se houver. A **Chave de Subscrição Ocp-Apim** é preenchida automaticamente para a chave de subscrição associada a esta API.
1. Selecione **Send** (Enviar).

   ![Testar o mapa da API](./media/api-management-import-and-publish/01-import-first-api-01.png)

   O back-end responde com **200 OK** e alguns dados.

## <a name="next-steps"></a><a name="next-steps"> </a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Importar a sua primeira API
> * Testar a API no portal do Azure

Avance para o próximo tutorial para aprender a criar e publicar um produto:

> [!div class="nextstepaction"]
> [Criar e publicar um produto](api-management-howto-add-products.md)