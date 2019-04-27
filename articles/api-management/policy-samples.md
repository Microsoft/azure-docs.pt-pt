---
title: Exemplos de políticas de Gestão de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas disponíveis para utilização na Gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 550161ce39aa944d0e01bb349ba48acbf719a860
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859380"
---
# <a name="api-management-policy-samples"></a>Exemplos de políticas de Gestão de API

As [políticas](api-management-howto-policies.md) são uma funcionalidade poderosa do sistema que permite ao publicador alterar o comportamento da API através da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou na resposta de uma API. A tabela seguinte inclui ligações para exemplos e fornece uma breve descrição de cada exemplo.

|                                                                                                                                                                      |                                                                                                                                                                                                                             |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Políticas de entrada**                                                                                                                                                 |                                                                                                                                                                                                                             |
| [Adicionar um cabeçalho Reencaminhado para permitir à API de back-end construir URLs corretos](./policies/set-header-to-enable-backend-to-construct-urls.md?toc=api-management/toc.json) | Demonstra como adicionar um cabeçalho Reencaminhado no pedido de entrada para permitir à API de back-end construir URLs corretos.                                                                                                        |
| [Adicionar um cabeçalho com um ID de correlação](./policies/add-correlation-id.md?toc=api-management/toc.json)                                                             | Demonstra como adicionar um cabeçalho com um ID de correlação ao pedido de entrada.                                                                                                                                        |
| [Adicionar capacidades para um serviço de back-end e colocar a resposta em cache](./policies/cache-response.md?toc=api-management/toc.json)                                             | Mostra como adicionar capacidades a um serviço de back-end. Por exemplo, aceitar o nome de um local em vez de latitude e longitude numa API de previsão meteorológica.                                                                    |
| [Autorizar o acesso com base em afirmações JWT](./policies/authorize-request-based-on-jwt-claims.md?toc=api-management/toc.json)                                              | Mostra como autorizar o acesso a métodos HTTP específicos numa API baseada em afirmações JWT.                                                                                                                                       |
| [Autorizar pedidos com um autorizador externo](./policies/authorize-request-using-external-authorizer.md)                                                   | Mostra como utilizar um autorizador externo para proteger o acesso à API.                                                                                                                                                               |
| [Autorizar o acesso com o token de OAuth do Google](./policies/use-google-as-oauth-token-provider.md?toc=api-management/toc.json)                                            | Mostra como autorizar o acesso aos pontos finais ao utilizar o Google como um fornecedor de tokens de OAuth.                                                                                                                                    |
| [Gerar a Assinatura de Acesso Partilhado e reencaminhar o pedido para o armazenamento do Azure](./policies/generate-shared-access-signature.md?toc=api-management/toc.json)                  | Mostra como gerar a [Assinatura de Acesso Partilhado](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) através de expressões e reencaminhar o pedido para o armazenamento do Azure com a política rewrite-uri. |
| [Obter o token de acesso de OAuth2 do AAD e reencaminhá-lo para o back-end](./policies/use-oauth2-for-authorization.md?toc=api-management/toc.json)                             | Fornece um exemplo de como utilizar o OAuth2 para autorização entre o gateway e um back-end. Mostra como obter um token de acesso do AAD e reencaminhá-lo para o back-end.                                                    |
| [Obter o token X-CSRF do gateway SAP através da política de envio de pedidos](./policies/get-x-csrf-token-from-sap-gateway.md?toc=api-management/toc.json)                           | Mostra como implementar o padrão X-CSRF utilizado por muitas APIs. Este exemplo é específico do Gateway SAP.                                                                                                                           |
| [Encaminhar o pedido com base no tamanho do respetivo corpo](./policies/route-requests-based-on-size.md?toc=api-management/toc.json)                                            | Demonstra como encaminhar pedidos com base no tamanho dos respetivos corpos.                                                                                                                                                       |
| [Enviar informações de contexto do pedido para o serviço de back-end](./policies/send-request-context-info-to-backend-service.md?toc=api-management/toc.json)                    | Mostra como enviar algumas informações de contexto para o serviço de back-end para registo ou processamento.                                                                                                                                |
| [Definir a duração da cache de resposta](./policies/set-cache-duration.md?toc=api-management/toc.json)                                                                          | Demonstra como definir a duração da cache de resposta através do valor maxAge no cabeçalho Cache-Control enviado pelo back-end.                                                                                                             |
| **Políticas de saída**                                                                                                                                                |                                                                                                                                                                                                                             |
| [Filtrar conteúdo de resposta](./policies/filter-response-content.md?toc=api-management/toc.json)                                                                         | Demonstra como filtrar elementos de dados do payload de resposta com base no produto associado ao pedido.                                                                                                        |
| **Políticas de erro**                                                                                                                                                |                                                                                                                                                                                                                             |
| [Registar erros no Stackify](./policies/log-errors-to-stackify.md?toc=api-management/toc.json)                                                                           | Mostra como adicionar uma política de registo de erros para enviar erros para o Stackify para registo.                                                                                                                                            |
