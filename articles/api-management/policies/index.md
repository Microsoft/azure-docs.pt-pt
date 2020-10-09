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
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: b5b8c82aa420b62e3b6e68ee53352eb9f77988f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86506694"
---
# <a name="api-management-policy-samples"></a>Exemplos de políticas de Gestão de API

As [políticas](../api-management-howto-policies.md) são uma funcionalidade poderosa do sistema que permite ao publicador alterar o comportamento da API através da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou na resposta de uma API. A tabela seguinte inclui ligações para exemplos e fornece uma breve descrição de cada exemplo.

| Políticas de entrada | Descrição |
| ---------------- | ----------- |
| [Adicionar um cabeçalho Reencaminhado para permitir à API de back-end construir URLs corretos](./set-header-to-enable-backend-to-construct-urls.md) | Demonstra como adicionar um cabeçalho Reencaminhado no pedido de entrada para permitir à API de back-end construir URLs corretos.                                                                                                        |
| [Adicionar um cabeçalho com um ID de correlação](./add-correlation-id.md)                                                             | Demonstra como adicionar um cabeçalho com um ID de correlação ao pedido de entrada.                                                                                                                                        |
| [Adicionar capacidades para um serviço de back-end e colocar a resposta em cache](./cache-response.md)                                             | Mostra como adicionar capacidades a um serviço de back-end. Por exemplo, aceitar o nome de um local em vez de latitude e longitude numa API de previsão meteorológica.                                                                    |
| [Autorizar o acesso com base em afirmações JWT](./authorize-request-based-on-jwt-claims.md)                                              | Mostra como autorizar o acesso a métodos HTTP específicos numa API baseada em afirmações JWT.                                                                                                                                       |
| [Autorizar pedidos com um autorizador externo](./authorize-request-using-external-authorizer.md)                                                   | Mostra como utilizar um autorizador externo para proteger o acesso à API.                                                                                                                                                               |
| [Autorizar o acesso com o token de OAuth do Google](./use-google-as-oauth-token-provider.md)                                            | Mostra como autorizar o acesso aos pontos finais ao utilizar o Google como um fornecedor de tokens de OAuth.                                                                                                                                    |
| [Filtrar Endereços IP ao utilizar um Gateway de Aplicação](./filter-ip-addresses-when-using-appgw.md) | Mostra como filtrar IPs em políticas quando a instância da Gestão de API é acedida através de um Gateway de Aplicação
| [Gerar a Assinatura de Acesso Partilhado e reencaminhar o pedido para o armazenamento do Azure](./generate-shared-access-signature.md)                  | Mostra como gerar a [Assinatura de Acesso Partilhado](../../storage/common/storage-sas-overview.md) através de expressões e reencaminhar o pedido para o armazenamento do Azure com a política rewrite-uri. |
| [Obter o token de acesso de OAuth2 do AAD e reencaminhá-lo para o back-end](./use-oauth2-for-authorization.md)                             | Fornece um exemplo de como utilizar o OAuth2 para autorização entre o gateway e um back-end. Mostra como obter um token de acesso do AAD e reencaminhá-lo para o back-end.                                                    |
| [Obter o token X-CSRF do gateway SAP através da política de envio de pedidos](./get-x-csrf-token-from-sap-gateway.md)                           | Mostra como implementar o padrão X-CSRF utilizado por muitas APIs. Este exemplo é específico do Gateway SAP.                                                                                                                           |
| [Encaminhar o pedido com base no tamanho do respetivo corpo](./route-requests-based-on-size.md)                                            | Demonstra como encaminhar pedidos com base no tamanho dos respetivos corpos.                                                                                                                                                       |
| [Enviar informações de contexto do pedido para o serviço de back-end](./send-request-context-info-to-backend-service.md)                    | Mostra como enviar algumas informações de contexto para o serviço de back-end para registo ou processamento.                                                                                                                                |
| [Definir a duração da cache de resposta](./set-cache-duration.md)                                                                          | Demonstra como definir a duração da cache de resposta através do valor maxAge no cabeçalho Cache-Control enviado pelo back-end.                                                                                                             |
| **Políticas de saída** | **Descrição** |
| [Filtrar conteúdo de resposta](./filter-response-content.md)                                                                         | Demonstra como filtrar elementos de dados do payload de resposta com base no produto associado ao pedido.                                                                                                        |
| **Políticas de erro** | **Descrição** |
| [Registar erros no Stackify](./log-errors-to-stackify.md)                                                                           | Mostra como adicionar uma política de registo de erros para enviar erros para o Stackify para registo.                                                                                                                                            |
