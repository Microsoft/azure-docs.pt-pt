---
title: Importar e publicar a sua primeira API na Azure API Management
description: Aprenda a importar uma API de especificação OpenAPI para a Gestão da API Azure e teste a sua API no portal Azure.
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
ms.openlocfilehash: 0b5fbb49e2f60f101f16988538af86c2caf550eb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82202860"
---
# <a name="import-and-publish-your-first-api"></a>Importar e publicar a sua primeira API

Este tutorial mostra como importar uma API de especificação OpenAPI em formato JSON para a Azure API Management. A Microsoft fornece a API de backend [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json)e acolhe-a no Azure em .

Uma vez que importa a API de backend para a API Management, a sua API Management API torna-se uma fachada para a API backend. Pode personalizar a fachada às suas necessidades na API Management sem tocar na API de backend. Para obter mais informações, veja [Transformar e proteger a sua API](transform-api.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Importar uma API para a Gestão da API
> * Testar a API no portal do Azure

![Nova API](./media/api-management-import-and-publish/created-api.png)

## <a name="prerequisites"></a>Pré-requisitos

- Compreender a [terminologia de Gestão da API Azure.](api-management-terminology.md)
- Criar uma instância de [Gestão API Azure.](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a><a name="create-api"> </a>Importar e publicar uma API de backend

Esta secção mostra como importar e publicar uma API de especificação OpenAPI.

1. Na navegação à esquerda da sua instância de Gestão API, selecione **APIs** da secção **de Gestão aPI.**
1. Selecione o azulejo **OpenAPI** e, em seguida, selecione **Full** no ecrã pop-up.
1. No ecrã **de especificações Create from OpenAPI,** utilize os valores da tabela seguinte para criar a sua API.

   Uma estrela vermelha ao lado de um campo na forma indica que o campo é necessário. Pode definir valores de API durante a criação ou posteriormente indo ao separador **Definições.**

   ![Criar uma API](./media/api-management-import-and-publish/create-api.png)

   |Definição|Valor|Descrição|
   |-------|-----|-----------|
   |**Especificação OpenAPI**|*https:\//conferenceapi.azurewebsites.net?format=json*|O serviço de implementação da API. A API de Gestão reencaminha os pedidos para este endereço.|
   |**Nome a apresentar**|Depois de introduzir o URL de serviço anterior, a API Management preenche este campo com base no JSON.|O nome apresentado no portal do desenvolvedor.|
   |**Nome**|Depois de introduzir o URL de serviço anterior, a API Management preenche este campo com base no JSON.|Um nome único para a API.|
   |**Descrição**|Depois de introduzir o URL de serviço anterior, a API Management preenche este campo com base no JSON.|Uma descrição opcional da API.|
   |**Esquema do URL**|**HTTPS**|Que protocolos podem ser usados para aceder à API.|
   |**Sufixo do URL da API**|*conferência*|O sufixo anexado ao URL base para o serviço de Gestão API. A API Management distingue as APIs pelo seu sufixo, pelo que o sufixo deve ser único para cada API para um determinado editor.|
   |**Etiquetas**| |Etiquetas para organização de APIs para pesquisa, agrupamento ou filtragem.|
   |**Produtos**|**Ilimitado**|Associação de uma ou mais APIs. Cada instância de Gestão API vem com dois produtos de amostra: **Starter** e **Unlimited**. Publica uma API associando a API a um produto, **Ilimitado** neste exemplo.<br/>Pode incluir várias APIs num produto e oferecê-las aos desenvolvedores através do portal de desenvolvedores. Para adicionar este API a outro produto, digite ou selecione o nome do produto. Repita este passo para adicionar a API a vários produtos. Também pode adicionar APIs aos produtos posteriormente a partir da página **Definições.**<br/>Para obter acesso à API, os programadores têm de subscrever primeiro um produto. Quando subscrevem, recebem uma chave de subscrição que é boa para qualquer API nesse produto. <br/>Se criou a instância de Gestão API, já é administrador, por isso está subscrito a todos os produtos da região.|
   |**Gateways**|**Gerido**|Gateway(s) da API que expõe a API. Este campo está disponível apenas em serviços **de desenvolvimento** e de nível **Premium.**<br/>**Gateway gerido** indica o gateway incorporado no serviço de Gestão API e hospedado pela Microsoft em Azure. Outros gateways são [gateways auto-hospedados](self-hosted-gateway-overview.md) e estão disponíveis apenas nos níveis de serviço Premium e Developer. Pode implantá-los no local ou em outras nuvens.<br/>Se não forem selecionados gateways, a API não estará disponível e os seus pedidos de API não terão sucesso.|
   |**Controlar a versão desta API?**|Selecione ou desmarque|Para obter mais informações sobre versão, consulte [Publicar várias versões da sua API](api-management-get-started-publish-versions.md).|

   > [!NOTE]
   > Para publicar a API aos consumidores de API, deve associá-la a um produto.

2. Selecione **Criar**.

Se tiver problemas em importar uma definição de API, consulte a [lista de questões e restrições conhecidas](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Teste a nova API no portal Azure

Pode ligar para operações da API diretamente do portal Azure, que fornece uma forma conveniente de visualizar e testar as operações.

1. Na navegação à esquerda da sua instância de Gestão API, selecione **APIs** da secção de Gestão da **API** e, em seguida, selecione **API Conferência de Demonstração**.
1. Selecione o separador **Teste** e, em seguida, selecione **GetSpeakers**. A página mostra **parâmetros de consulta** e **cabeçalhos,** se houver. A Chave de **Assinatura Ocp-Apim** é preenchida automaticamente para a chave de subscrição associada a esta API.
1. Selecione **Enviar**.

   ![Testar o mapa da API](./media/api-management-import-and-publish/01-import-first-api-01.png)

   O back-end responde com **200 OK** e alguns dados.

## <a name="next-steps"></a><a name="next-steps"> </a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Importar a sua primeira API
> * Testar a API no portal do Azure

Avançar para o próximo tutorial para aprender a criar e publicar um produto:

> [!div class="nextstepaction"]
> [Criar e publicar um produto](api-management-howto-add-products.md)
