---
title: Tutorial - Importe e publique a sua primeira API na Azure API Management
description: Neste tutorial, importa uma API de Especificação OpenAPI para a Azure API Management e, em seguida, teste a sua API no portal Azure.
author: mikebudzynski
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 9ff64f57e61002101b4e2c560bdcd91863cc461e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91626979"
---
# <a name="tutorial-import-and-publish-your-first-api"></a>Tutorial: Importe e publique a sua primeira API

Este tutorial mostra como importar uma Especificação OpenAPI API em formato JSON para a Azure API Management. A Microsoft fornece o backend API usado neste exemplo, e acolhe-o no Azure em [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json) .

Uma vez importa a API backend para a API Management, a sua API Management API Management torna-se uma fachada para a API backend. Pode personalizar a fachada às suas necessidades na Gestão da API sem tocar na API de backend. Para obter mais informações, veja [Transformar e proteger a sua API](transform-api.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Importar uma API para a API Management
> * Testar a API no portal do Azure

Após a importação, pode gerir a API no portal Azure.

:::image type="content" source="media/import-and-publish/created-api.png" alt-text="Nova API em Gestão de API":::

## <a name="prerequisites"></a>Pré-requisitos

- Compreender [a terminologia da Gestão API da Azure.](api-management-terminology.md)
- [Criar um exemplo de Gestão API Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a>Importar e publicar uma API de backend

Esta secção mostra como importar e publicar uma API de backend de especificações OpenAPI.

1. Na navegação à esquerda da sua instância de Gestão de API, selecione **APIs**.
1. Selecione o azulejo **OpenAPI.**
1. Na janela **de especificações Create a partir de OpenAPI,** selecione **Full**.
1. Introduza os valores a partir da tabela seguinte. Em seguida, **selecione Criar** para criar a sua API.

   Pode definir os valores de API durante a criação ou mais tarde indo para o **separador Definições.**

   :::image type="content" source="media/import-and-publish/create-api.png" alt-text="Criar uma API":::


   |Definição|Valor|Descrição|
   |-------|-----|-----------|
   |**Especificação OpenAPI**|*https: \/ /conferenceapi.azurewebsites.net?format=json*|O serviço de implementação da API. A API Management remete pedidos para este endereço.|
   |**Nome a apresentar**|Depois de introduzir o URL de serviço anterior, a API Management preenche este campo com base no JSON.|O nome apresentado no portal do [desenvolvedor.](api-management-howto-developer-portal.md)|
   |**Nome**|Depois de introduzir o URL de serviço anterior, a API Management preenche este campo com base no JSON.|Um nome único para a API.|
   |**Descrição**|Depois de introduzir o URL de serviço anterior, a API Management preenche este campo com base no JSON.|Uma descrição opcional da API.|
   |**Esquema do URL**|**HTTPS**|Quais os protocolos que podem aceder à API.|
   |**Sufixo do URL da API**|*conferência*|O sufixo anexado ao URL base para o serviço de Gestão da API. A API Management distingue as APIs pelo seu sufixo, pelo que o sufixo deve ser único para cada API para um determinado editor.|
   |**Etiquetas**| |Etiquetas para organizar APIs para pesquisa, agrupamento ou filtragem.|
   |**Produtos**|**Ilimitado**|Associação de uma ou mais APIs. Cada instância de Gestão da API vem com dois produtos de amostra: **Starter** e **Unlimited**. Publica uma API associando a API a um produto, **Ilimitado** neste exemplo.<br/><br/> Pode incluir várias APIs num produto e oferecê-las aos desenvolvedores através do portal de desenvolvedores. Para adicionar esta API a outro produto, escreva ou selecione o nome do produto. Repita este passo para adicionar a API a vários produtos. Também pode adicionar APIs aos produtos mais tarde a partir da página **Definições.**<br/><br/>  Para obter mais informações sobre produtos, consulte [Criar e publicar um produto.](api-management-howto-add-products.md)|
   |**Gateways**|**Gerido**|Gateway(s) API que expõe a API. Este campo está disponível apenas nos serviços **de Nível Developer** e **Premium.**<br/><br/>**Gerido** indica o gateway incorporado no serviço de Gestão API e hospedado pela Microsoft em Azure. [Gateways auto-hospedados](self-hosted-gateway-overview.md) estão disponíveis apenas nos níveis de serviço Premium e Developer. Pode implantá-los no local ou em outras nuvens.<br/><br/> Se não forem selecionados gateways, a API não estará disponível e os seus pedidos de API não serão bem sucedidos.|
   |**Controlar a versão desta API?**|Selecione ou desmarca|Para mais informações, consulte [publicar várias versões da sua API.](api-management-get-started-publish-versions.md)|

   > [!NOTE]
   > Para publicar a API aos consumidores da API, deve associá-la a um produto.

2. Selecione **Criar**.

Se tiver problemas em importar uma definição de API, consulte a [lista de questões e restrições conhecidas.](api-management-api-import-restrictions.md)

## <a name="test-the-new-api-in-the-azure-portal"></a>Teste a nova API no portal Azure

Você pode ligar para as operações da API diretamente a partir do portal Azure, que fornece uma maneira conveniente de ver e testar as operações.

1. Na navegação à esquerda da sua instância de Gestão de API, selecione **APIs**  >  **Demo Conference API**.
1. Selecione o separador **Teste** e, em seguida, selecione **GetSpeakers**. A página mostra **parâmetros de consulta** e **cabeçalhos,** se houver. A **Chave de Subscrição Ocp-Apim** é preenchida automaticamente para a chave de subscrição associada a esta API.
1. Selecione **Enviar**.

   :::image type="content" source="media/import-and-publish/01-import-first-api-01.png" alt-text="Teste API no portal Azure":::

   O backend responde com **200 OK** e alguns dados.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Importar a sua primeira API
> * Testar a API no portal do Azure

Avance para o próximo tutorial para aprender a criar e publicar um produto:

> [!div class="nextstepaction"]
> [Criar e publicar um produto](api-management-howto-add-products.md)
