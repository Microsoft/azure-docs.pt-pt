---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/21/2020
ms.author: glenga
ms.openlocfilehash: 380878fedaa2f7ea6160c3c4801c8dece6e1a9ff
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648947"
---
#### <a name="enable-app-service-authenticationauthorization"></a>Ativar a autenticação/autorização do serviço de aplicações

A plataforma App Service permite-lhe utilizar o Azure Ative Directory (AAD) e vários fornecedores de identidade de terceiros para autenticar clientes. Pode utilizar esta estratégia para implementar regras de autorização personalizadas para as suas funções, podendo trabalhar com informações do utilizador a partir do seu código de função. Para saber mais, consulte autenticação e autorização no Serviço de [Aplicações Azure](../articles/app-service/overview-authentication-authorization.md) e [trabalhando com identidades de clientes.](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities)

#### <a name="use-azure-api-management-apim-to-authenticate-requests"></a>Utilizar a Azure API Management (APIM) para autenticar pedidos

A APIM fornece uma variedade de opções de segurança da API para pedidos de entrada. Para saber mais, consulte as políticas de [autenticação da API Management.](../articles/api-management/api-management-authentication-policies.md) Com a APIM no lugar, pode configurar a sua aplicação de funções para aceitar pedidos apenas a partir do endereço IP da sua instância APIM. Para saber mais, consulte [as restrições de endereço IP](../articles/azure-functions/ip-addresses.md#ip-address-restrictions).
