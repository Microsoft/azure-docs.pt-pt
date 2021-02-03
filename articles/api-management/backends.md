---
title: Azure API Management recua | Microsoft Docs
description: Conheça os backends personalizados na API Management
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: 54a46e999391507f5ec7d927f62b88fcd2169b75
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500620"
---
# <a name="backends-in-api-management"></a>Backends na Gestão da API

Um *backend* (ou *backend da API)* na API Management é um serviço HTTP que implementa a sua API frontal e as suas operações.

Ao importar determinadas APIs, a API Management configura automaticamente o backend da API. Por exemplo, a API Management configura o backend ao importar uma [especificação OpenAPI](import-api-from-oas.md), [SOAP API,](import-soap-api.md)ou recursos Azure, como uma App de [Função Azure](import-function-app-as-api.md) ativada por HTTP ou [App Lógica](import-logic-app-as-api.md).

A API Management também suporta a utilização de outros recursos Azure, como um [cluster de Tecido de Serviço](how-to-configure-service-fabric-backend.md) ou um serviço personalizado como backend da API. A utilização destes backends personalizados requer uma configuração extra, por exemplo, para autorizar credenciais de pedidos ao serviço de backend e definir operações API. Configura e gere estes backends no portal Azure ou utilizando APIs ou ferramentas Azure.

Depois de criar um backend, pode fazer referência ao URL de backend nas suas APIs. Utilize a [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) política para redirecionar um pedido de API para o backend personalizado em vez do backend padrão para essa API.

## <a name="benefits-of-backends"></a>Benefícios dos backends

Um backend personalizado tem vários benefícios, incluindo:

* Resumos informações sobre o serviço backend, promovendo a reutilização em todas as APIs e melhor governação  
* Facilmente utilizado configurando uma política de transformação em uma API existente
* Aproveita a funcionalidade de Gestão API para manter segredos no Cofre da Chave Azure se [os valores nomeados](api-management-howto-properties.md) forem configurados para autenticação de parâmetros de cabeçalho ou consulta

## <a name="next-steps"></a>Passos seguintes

* Configurar um [backend de Tecido de Serviço](how-to-configure-service-fabric-backend.md) utilizando o portal Azure.
* Os backends também podem ser configurados utilizando os modelos API Management [REST API](/rest/api/apimanagement), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)ou [Azure Resource Manager](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

