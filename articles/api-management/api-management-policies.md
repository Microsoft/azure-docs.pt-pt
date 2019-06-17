---
title: Políticas de gestão de API do Azure | Documentos da Microsoft
description: Saiba mais sobre as políticas disponíveis para utilização na Gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: e27829fe5ebf57552ef4e97a2bfc7b6aefd81dc8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66254404"
---
# <a name="api-management-policies"></a>Políticas de API Management
Esta secção fornece uma referência para as seguintes políticas de gestão de API. Para informações sobre como adicionar e configurar as políticas, consulte [políticas de gestão de API](api-management-howto-policies.md).  
  
 As políticas são uma funcionalidade poderosa do sistema que permite ao publicador alterar o comportamento da API através da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou a resposta de uma API. As instruções populares incluem a conversão de formato de XML para JSON e limitação para restringir a quantidade de chamadas recebidas de um desenvolvedor de taxa de chamadas. Muitos mais políticas estão disponíveis de imediato.  
  
 As expressões de política podem ser utilizadas como valores de atributo ou valores de texto em qualquer uma das políticas de API Management, a menos que a política especifique o contrário. Algumas políticas, como [Fluxo de controlo](api-management-advanced-policies.md#choose) e [Definir variável](api-management-advanced-policies.md#set-variable), baseiam-se em expressões de política. Para obter mais informações, veja [Políticas avançadas](api-management-advanced-policies.md#AdvancedPolicies) e [Expressões de política](api-management-policy-expressions.md).  
  
##  <a name="ProxyPolicies"></a> Políticas  
  
-   [Políticas de restrição de acesso](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Cabeçalho HTTP de verificação](api-management-access-restriction-policies.md#CheckHTTPHeader) -impõe a existência e/ou valor de um cabeçalho de HTTP.  
    -   [Limitar taxa de chamadas por subscrição](api-management-access-restriction-policies.md#LimitCallRate) -picos de utilização da API impede ao limitar taxa de chamadas, numa base por subscrição.  
    -   [Limitar taxa de chamadas por chave](api-management-access-restriction-policies.md#LimitCallRateByKey) -picos de utilização da API impede ao limitar taxa de chamadas, numa base por chave.  
    -   [Restringir o chamador IPs](api-management-access-restriction-policies.md#RestrictCallerIPs) -chamadas de filtros (permite/nega) provenientes de endereços IP específicos e/ou intervalos de endereços.  
    -   [Definir quota de utilização por subscrição](api-management-access-restriction-policies.md#SetUsageQuota) -permite-lhe impor um renovável ou tempo de vida volume e/ou de largura de banda de quota de chamadas, numa base por subscrição.  
    -   [Definir quota de utilização por chave](api-management-access-restriction-policies.md#SetUsageQuotaByKey) -permite-lhe impor um renovável ou tempo de vida volume e/ou de largura de banda de quota de chamadas, numa base por chave.  
    -   [Validar JWT](api-management-access-restriction-policies.md#ValidateJWT) -impõe a existência e a validade de um JWT extraído de um cabeçalho de HTTP especificado ou um parâmetro de consulta especificada.  
-   [Políticas avançadas](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Controlar o fluxo](api-management-advanced-policies.md#choose) - condicionalmente aplica-se as declarações de política com base na avaliação de expressões booleanas.  
    -   [Reencaminhar pedido](api-management-advanced-policies.md#ForwardRequest) -reencaminha o pedido para o serviço de back-end.
    -   [Limitar a simultaneidade](api-management-advanced-policies.md#LimitConcurrency) -impede entre as políticas de execução em mais do que o número especificado de pedidos de cada vez.
    -   [Registo para o Hub de eventos](api-management-advanced-policies.md#log-to-eventhub) -envia mensagens no formato especificado para um destino de mensagem definido por uma entidade de registo.
    -   [Simular a resposta](api-management-advanced-policies.md#mock-response) -anulações de execução do pipeline e devolver uma resposta simulada diretamente para o chamador.
    -   [Repita](api-management-advanced-policies.md#Retry) -repete a execução das declarações a política circunscritas, se e até que a condição é cumprida. A execução será repetida em intervalos de tempo especificados e até especificado o número de tentativas.  
    -   [Devolver a resposta](api-management-advanced-policies.md#ReturnResponse) -anulações de execução do pipeline e devolve a resposta especificada diretamente para o chamador.  
    -   [Enviar pedido unidirecional](api-management-advanced-policies.md#SendOneWayRequest) -envia um pedido para o URL especificado sem aguardar uma resposta.  
    -   [Enviar pedido](api-management-advanced-policies.md#SendRequest) -envia um pedido para o URL especificado.
    -   [Definir o HTTP proxy](api-management-advanced-policies.md#SetHttpProxy) -permite-lhe a pedidos de rota reencaminhado através de um proxy HTTP.
    -   [Definir variável](api-management-advanced-policies.md#set-variable) -manter um valor numa variável de contexto nomeado para acesso posterior.  
    -   [Definir o método de pedido](api-management-advanced-policies.md#SetRequestMethod) -permite-lhe alterar o método HTTP para um pedido.  
    -   [Definir o código de estado](api-management-advanced-policies.md#SetStatus) -altera o código de estado HTTP para o valor especificado.  
    -   [Rastreio](api-management-advanced-policies.md#Trace) -adiciona uma cadeia de caracteres para o [Inspetor de API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) saída.  
    -   [Aguarde](api-management-advanced-policies.md#Wait) -tem de aguardar para incluído [pedido de envio](api-management-advanced-policies.md#SendRequest), [obter o valor da cache](api-management-caching-policies.md#GetFromCacheByKey), ou [controlar o fluxo de](api-management-advanced-policies.md#choose) políticas para concluir antes de continuar.  
-   [Políticas de autenticação](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Autenticar com o Basic](api-management-authentication-policies.md#Basic) -autenticar com um serviço de back-end usando a autenticação básica.  
    -   [Autenticar com o certificado de cliente](api-management-authentication-policies.md#ClientCertificate) -autenticar com um serviço de back-end com certificados de cliente.  
    -   [Autenticar com a identidade gerida](api-management-authentication-policies.md#ManagedIdentity) -autenticar com um serviço de back-end com um [identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
-   [Caching policies (Políticas de colocação em cache)](api-management-caching-policies.md#CachingPolicies)  
    -   [Obter a partir do cache](api-management-caching-policies.md#GetFromCache) -realizar o cache de procurar e devolver uma resposta em cache válida quando disponível.  
    -   [Store à cache](api-management-caching-policies.md#StoreToCache) -armazena em cache a resposta, de acordo com a configuração de controlo de cache especificado.  
    -   [Obter o valor da cache](api-management-caching-policies.md#GetFromCacheByKey) -obter um item em cache por chave.  
    -   [Store valor em cache](api-management-caching-policies.md#StoreToCacheByKey) -Store um item na cache por chave.  
    -   [Remover o valor da cache](api-management-caching-policies.md#RemoveCacheByKey) -remover um item na cache por chave.  
-   [Políticas entre domínios](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Permitir chamadas entre domínios](api-management-cross-domain-policies.md#AllowCrossDomainCalls) -torna a API acessível a partir de clientes baseada no browser e Adobe Flash e o Microsoft Silverlight.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) -adiciona recursos de várias origens (CORS) suporte a uma operação ou uma API para permitir chamadas entre domínios de clientes baseados em navegador de partilha.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) -adiciona JSON com o suporte de preenchimento (JSONP) para uma operação ou uma API para permitir chamadas entre domínios de clientes baseada no browser de JavaScript.  
-   [Políticas de transformação](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Converter o JSON em XML](api-management-transformation-policies.md#ConvertJSONtoXML) - converte pedido ou corpo de resposta de JSON em XML.  
    -   [Converter o XML em JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - converte pedido ou corpo de resposta do XML para JSON.  
    -   [Localizar e substituir a cadeia de caracteres no corpo](api-management-transformation-policies.md#Findandreplacestringinbody) – localiza uma subcadeia de solicitação ou resposta e o substitui por uma subcadeia diferente.  
    -   [Mascarar URLs no conteúdo](api-management-transformation-policies.md#MaskURLSContent) -ligações reescreverá (máscaras) na resposta body, de modo a que apontam para a ligação equivalente através do gateway.  
    -   [Defina o serviço de back-end](api-management-transformation-policies.md#SetBackendService) -altera o serviço de back-end para uma solicitação de entrada.  
    -   [Definir corpo](api-management-transformation-policies.md#SetBody) -define o corpo da mensagem para pedidos de entrada e saídos.  
    -   [Definir o cabeçalho HTTP](api-management-transformation-policies.md#SetHTTPheader) - atribui um valor a uma resposta existente e/ou o cabeçalho do pedido ou adiciona um novo cabeçalho de resposta e/ou a pedido.  
    -   [Defina o parâmetro de cadeia de caracteres de consulta](api-management-transformation-policies.md#SetQueryStringParameter) - adiciona, substitui o valor de ou elimina o parâmetro de cadeia de caracteres de consulta do pedido.  
    -   [Reescrever URL](api-management-transformation-policies.md#RewriteURL) -converte um URL do pedido de sua forma pública para o formato esperado pelo serviço web.  
    -   [Transformar XML usando um XSLT](api-management-transformation-policies.md#XSLTransform) -aplica-se uma transformação XSL para XML no corpo da solicitação ou resposta.  



## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações para trabalhar com políticas, consulte:

+ [Políticas de gestão de API](api-management-howto-policies.md)
+ [Transforme as APIs](transform-api.md)
+ [Exemplos de política](policy-samples.md)   
