---
title: Políticas de gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas disponíveis para utilização na Gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 2b6e056fbfb134f0b1218b4281b9f971a0e24202
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219465"
---
# <a name="api-management-policies"></a>Políticas de API Management
Esta seção fornece uma referência para as seguintes políticas de gerenciamento de API. Para obter informações sobre como adicionar e configurar políticas, consulte [políticas no gerenciamento de API](api-management-howto-policies.md).  
  
 As políticas são um recurso poderoso do sistema que permite que o editor altere o comportamento da API por meio da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente na solicitação ou resposta de uma API. As instruções populares incluem conversão de formato de XML para JSON e limitação de taxa de chamada para restringir a quantidade de chamadas de entrada de um desenvolvedor. Muitas outras políticas estão disponíveis prontamente.  
  
 As expressões de política podem ser utilizadas como valores de atributo ou valores de texto em qualquer uma das políticas de API Management, a menos que a política especifique o contrário. Algumas políticas, como [Fluxo de controlo](api-management-advanced-policies.md#choose) e [Definir variável](api-management-advanced-policies.md#set-variable), baseiam-se em expressões de política. Para obter mais informações, veja [Políticas avançadas](api-management-advanced-policies.md#AdvancedPolicies) e [Expressões de política](api-management-policy-expressions.md).  
  
##  <a name="ProxyPolicies"></a>Policie  
  
-   [Políticas de restrição de acesso](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Verificar cabeçalho http](api-management-access-restriction-policies.md#CheckHTTPHeader) – impõe a existência e/ou valor de um cabeçalho http.  
    -   [Limitar a taxa de chamada por assinatura](api-management-access-restriction-policies.md#LimitCallRate) -impede picos de uso da API limitando a taxa de chamada, por assinatura.  
    -   [Limitar a taxa de chamada por chave](api-management-access-restriction-policies.md#LimitCallRateByKey) -impede picos de uso da API limitando a taxa de chamada, de acordo com a chave.  
    -   [Restringir IPS de chamador](api-management-access-restriction-policies.md#RestrictCallerIPs) – filtra (permite/nega) chamadas de endereços IP específicos e/ou intervalos de endereços.  
    -   [Definir cota de uso por assinatura](api-management-access-restriction-policies.md#SetUsageQuota) – permite impor uma cota renovável ou de tempo de vida de volume de chamada e/ou largura de banda, por assinatura.  
    -   [Definir a cota de uso por chave](api-management-access-restriction-policies.md#SetUsageQuotaByKey) – permite que você aplique uma cota renovável ou de tempo de vida de volume de chamada e/ou largura de banda, de acordo com a chave.  
    -   [Validar JWT](api-management-access-restriction-policies.md#ValidateJWT) – impõe a existência e a validade de um JWT extraído de um cabeçalho HTTP especificado ou um parâmetro de consulta especificado.  
-   [Políticas avançadas](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Fluxo de controle](api-management-advanced-policies.md#choose) – aplica-se condicionalmente a instruções de política com base na avaliação de expressões booleanas.  
    -   [Encaminhar solicitação](api-management-advanced-policies.md#ForwardRequest) – encaminha a solicitação para o serviço de back-end.
    -   [Simultaneidade de limite](api-management-advanced-policies.md#LimitConcurrency) – impede que políticas incluídas sejam executadas por mais do que o número especificado de solicitações por vez.
    -   [Registrar no Hub de eventos](api-management-advanced-policies.md#log-to-eventhub) – envia mensagens no formato especificado para um destino de mensagem definido por uma entidade de agente.
    -   [Resposta de simulação](api-management-advanced-policies.md#mock-response) – anula a execução do pipeline e retorna uma resposta fictícia diretamente para o chamador.
    -   [Retry](api-management-advanced-policies.md#Retry) -repete a execução das instruções de política incluídas, se e até que a condição seja atendida. A execução será repetida nos intervalos de tempo especificados e até a contagem de repetições especificada.  
    -   [Retornar resposta](api-management-advanced-policies.md#ReturnResponse) – anula a execução do pipeline e retorna a resposta especificada diretamente para o chamador.  
    -   [Enviar solicitação unidirecional](api-management-advanced-policies.md#SendOneWayRequest) – envia uma solicitação para a URL especificada sem aguardar uma resposta.  
    -   [Enviar solicitação](api-management-advanced-policies.md#SendRequest) – envia uma solicitação para a URL especificada.
    -   [Definir proxy http](api-management-advanced-policies.md#SetHttpProxy) – permite rotear solicitações encaminhadas por meio de um proxy http.
    -   [Definir variável](api-management-advanced-policies.md#set-variable) – manter um valor em uma variável de contexto nomeada para acesso posterior.  
    -   [Definir método de solicitação](api-management-advanced-policies.md#SetRequestMethod) – permite que você altere o método http para uma solicitação.  
    -   [Definir código de status](api-management-advanced-policies.md#SetStatus) -altera o código de status HTTP para o valor especificado.  
    -   [Trace](api-management-advanced-policies.md#Trace) – adiciona rastreamentos personalizados na saída do [Inspetor de API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) , Application insights telemetrias e logs de diagnóstico.  
    -   [Wait](api-management-advanced-policies.md#Wait) -aguarda a conclusão das políticas de [envio por solicitação](api-management-advanced-policies.md#SendRequest), [obtenção de valor do cache](api-management-caching-policies.md#GetFromCacheByKey)ou [fluxo de controle](api-management-advanced-policies.md#choose) antes de continuar.  
-   [Políticas de autenticação](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Autenticar com Basic](api-management-authentication-policies.md#Basic) -autenticar com um serviço de back-end usando a autenticação básica.  
    -   [Autenticar com certificado de cliente](api-management-authentication-policies.md#ClientCertificate) -autenticar com um serviço de back-end usando certificados de cliente.  
    -   [Autenticar com a identidade gerenciada](api-management-authentication-policies.md#ManagedIdentity) – autentique com um serviço de back-end usando uma [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
-   [Caching policies (Políticas de colocação em cache)](api-management-caching-policies.md#CachingPolicies)  
    -   [Obter do cache](api-management-caching-policies.md#GetFromCache) – executar pesquisa de cache e retornar uma resposta válida em cache quando disponível.  
    -   [Armazenar](api-management-caching-policies.md#StoreToCache) em cache-armazena em cache a resposta de acordo com a configuração de controle de cache especificada.  
    -   [Obter valor do cache](api-management-caching-policies.md#GetFromCacheByKey) – recuperar um item armazenado em cache por chave.  
    -   [Armazenar valor no cache](api-management-caching-policies.md#StoreToCacheByKey) – armazene um item no cache por chave.  
    -   [Remover valor do cache](api-management-caching-policies.md#RemoveCacheByKey) -remove um item do cache por chave.  
-   [Políticas entre domínios](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Permitir chamadas entre domínios](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – torna a API acessível a partir de clientes do Adobe Flash e do Microsoft Silverlight baseados em navegador.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) -adiciona suporte a CORS (compartilhamento de recursos entre origens) a uma operação ou a uma API para permitir chamadas entre domínios de clientes baseados em navegador.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) -adiciona suporte a JSON com preenchimento (JSONP) a uma operação ou a uma API para permitir chamadas entre domínios de clientes baseados em navegador JavaScript.  
-   [Políticas de transformação](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Converter JSON em XML](api-management-transformation-policies.md#ConvertJSONtoXML) – converte o corpo da solicitação ou da resposta de JSON em XML.  
    -   [Converter XML em JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) – converte o corpo da solicitação ou da resposta de XML para JSON.  
    -   [Localizar e substituir cadeia de caracteres no corpo](api-management-transformation-policies.md#Findandreplacestringinbody) – localiza uma subcadeia de caracteres de solicitação ou resposta e a substitui por uma subcadeia de caracteres diferente.  
    -   [Mascarar URLs no conteúdo](api-management-transformation-policies.md#MaskURLSContent) – reescreve (mascara) links no corpo da resposta para que eles apontem para o link equivalente por meio do gateway.  
    -   [Definir serviço de back-end](api-management-transformation-policies.md#SetBackendService) -altera o serviço de back-end para uma solicitação de entrada.  
    -   [Definir corpo](api-management-transformation-policies.md#SetBody) – define o corpo da mensagem para solicitações de entrada e saída.  
    -   [Definir cabeçalho http](api-management-transformation-policies.md#SetHTTPheader) – atribui um valor a um cabeçalho de resposta e/ou solicitação existente ou adiciona um novo cabeçalho de resposta e/ou solicitação.  
    -   [Definir parâmetro de cadeia de caracteres de consulta](api-management-transformation-policies.md#SetQueryStringParameter) -adiciona, substitui o valor ou exclui o parâmetro de cadeia de caracteres de consulta de solicitação.  
    -   [Reescrever URL](api-management-transformation-policies.md#RewriteURL) – converte uma URL de solicitação de seu formato público para a forma esperada pelo serviço Web.  
    -   [Transformar XML usando um XSLT](api-management-transformation-policies.md#XSLTransform) – aplica uma transformação XSL ao XML no corpo da solicitação ou da resposta.  



## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Políticas no gerenciamento de API](api-management-howto-policies.md)
+ [APIs de transformação](transform-api.md)
+ [Exemplos de política](policy-samples.md)   
