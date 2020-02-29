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
ms.date: 02/27/2020
ms.author: apimpm
ms.openlocfilehash: 886063dcf886d79ac960814f20b3789e8e3b6839
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163512"
---
# <a name="import-and-publish-your-first-api"></a>Importar e publicar a sua primeira API 

Este tutorial mostra como importar uma API de especificação OpenAPI em formato JSON para a Azure API Management. A Microsoft fornece a API de backend e acolhe-a no Azure em [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json).

Uma vez que importa a API de backend para a API Management, a sua API Management API torna-se uma fachada para a API backend. Pode personalizar a fachada às suas necessidades na API Management sem tocar na API de backend. Para obter mais informações, veja [Transformar e proteger a sua API](transform-api.md). 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Importar uma API para a Gestão da API
> * Testar a API no Portal do Azure

![Nova API](./media/api-management-import-and-publish/created-api.png)

## <a name="prerequisites"></a>Pré-requisitos

- Compreender a [terminologia de Gestão da API Azure.](api-management-terminology.md)
- Criar uma instância de [Gestão API Azure.](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importar e publicar uma API de backend

Esta secção mostra como importar e publicar uma API de especificação OpenAPI.
 
1. Na navegação à esquerda da sua instância de Gestão API, selecione **APIs** da secção **de Gestão aPI.**
1. Selecione o azulejo **OpenAPI** e, em seguida, selecione **Full** no ecrã pop-up.
1. No ecrã **de especificações Create from OpenAPI,** utilize os valores da tabela seguinte para criar a sua API.
   
   Uma estrela vermelha ao lado de um campo na forma indica que o campo é necessário. Pode definir valores de API durante a criação ou posteriormente indo ao separador **Definições.** 
   
   ![Criar uma API](./media/api-management-import-and-publish/create-api.png)
   
   |Definição|Valor|Descrição|
   |-------|-----|-----------|
   |**Especificação de OpenAPI**|*https:\//conferenceapi.azurewebsites.net?format=json*|O serviço de implementação da API. A API de Gestão reencaminha os pedidos para este endereço.|
   |**Nome a apresentar**|Depois de introduzir o URL de serviço anterior, a API Management preenche este campo com base no JSON.|O nome apresentado no portal do desenvolvedor.|
   |**Nome**|Depois de introduzir o URL de serviço anterior, a API Management preenche este campo com base no JSON.|Um nome único para a API.|
   |**Descrição**|Depois de introduzir o URL de serviço anterior, a API Management preenche este campo com base no JSON.|Uma descrição opcional da API.|
   |**Esquema do URL**|**HTTPS**|Que protocolos podem ser usados para aceder à API.|
   |**Sufixo do URL da API**|*conferência*|O sufixo anexado ao URL base para o serviço de Gestão API. A API Management distingue as APIs pelo seu sufixo, pelo que o sufixo deve ser único para cada API para um determinado editor.|
   |**Produtos**|**Ilimitado**|Associação de uma ou mais APIs. Cada instância de Gestão API vem com dois produtos de amostra: **Starter** e **Unlimited**. Publica uma API associando a API a um produto, **Ilimitado** neste exemplo.<br/>Pode incluir várias APIs num produto e oferecê-las aos desenvolvedores através do portal de desenvolvedores. Para adicionar este API a outro produto, digite ou selecione o nome do produto. Repita este passo para adicionar a API a vários produtos. Também pode adicionar APIs aos produtos posteriormente a partir da página **Definições.**<br/>Para obter acesso à API, os programadores têm de subscrever primeiro um produto. Quando subscrevem, recebem uma chave de subscrição que é boa para qualquer API nesse produto. <br/>Se criou a instância de Gestão API, já é administrador, por isso está subscrito a todos os produtos da região.|
   |**Etiquetas**| |Etiquetas para organização de APIs para pesquisa, agrupamento ou filtragem.|
   |**Versão deste API?**|Selecione ou desmarque|Para obter mais informações sobre versão, consulte [Publicar várias versões da sua API](api-management-get-started-publish-versions.md).|
   
   > [!NOTE]
   > Para publicar a API, precisa de associá-la a um produto. Pode fazê-lo a partir da página **Definições.**
   
1. Selecione **Criar**.

Se tiver problemas em importar uma definição de API, consulte a [lista de questões e restrições conhecidas](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Teste a nova API no portal Azure

Pode ligar para operações da API diretamente do portal Azure, que fornece uma forma conveniente de visualizar e testar as operações.

1. Na navegação à esquerda da sua instância de Gestão API, selecione **APIs** da secção de Gestão da **API** e, em seguida, selecione **API Conferência de Demonstração**.
1. Selecione o separador **Teste** e, em seguida, selecione **GetSpeakers**. A página mostra **parâmetros de consulta** e **cabeçalhos,** se houver. A Chave de **Assinatura Ocp-Apim** é preenchida automaticamente para a chave de subscrição associada a esta API.
1. Selecione **Enviar**.
   
   ![Testar o mapa da API](./media/api-management-import-and-publish/01-import-first-api-01.png)
   
   O back-end responde com **200 OK** e alguns dados.

## <a name="next-steps"> </a>Próximos passos

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Importar a sua primeira API
> * Testar a API no Portal do Azure

Avançar para o próximo tutorial para aprender a criar e publicar um produto:

> [!div class="nextstepaction"]
> [Criar e publicar um produto](api-management-howto-add-products.md)
