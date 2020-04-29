---
title: Políticas de Gestão da API Azure Microsoft Docs
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
ms.openlocfilehash: f4a29c7cb7e35e69eb9410dd7ddc9f07757e3565
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82128723"
---
# <a name="api-management-policies"></a>Políticas de API Management
Esta secção fornece uma referência para as seguintes políticas de Gestão da API. Para obter informações sobre a adição e configuração de políticas, consulte [Políticas na Gestão da API](api-management-howto-policies.md).  
  
 As políticas são uma capacidade poderosa do sistema que permite ao editor alterar o comportamento da API através da configuração. As políticas são uma coleção de Declarações que são executadas sequencialmente a pedido ou resposta de uma API. As Declarações Populares incluem a conversão do formato de XML para JSON e a taxa de chamada limitada para restringir a quantidade de chamadas recebidas de um desenvolvedor. Muitas mais políticas estão disponíveis fora da caixa.  
  
 As expressões de política podem ser utilizadas como valores de atributo ou valores de texto em qualquer uma das políticas de API Management, a menos que a política especifique o contrário. Algumas políticas, como [Fluxo de controlo](api-management-advanced-policies.md#choose) e [Definir variável](api-management-advanced-policies.md#set-variable), baseiam-se em expressões de política. Para obter mais informações, veja [Políticas avançadas](api-management-advanced-policies.md#AdvancedPolicies) e [Expressões de política](api-management-policy-expressions.md).  
  
##  <a name="policies"></a><a name="ProxyPolicies"></a>Políticas  
  
-   [Políticas de restrição de acesso](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   Consulte o [cabeçalho HTTP](api-management-access-restriction-policies.md#CheckHTTPHeader) - Aplica a existência e/ou o valor de um cabeçalho HTTP.  
    -   Limitar a taxa de [chamada por subscrição](api-management-access-restriction-policies.md#LimitCallRate) - Previne picos de utilização da API limitando a taxa de chamada, por subscrição.  
    -   [Limitar a taxa de chamada por chave](api-management-access-restriction-policies.md#LimitCallRateByKey) - Previne picos de utilização de API limitando a taxa de chamada, numa base chave.  
    -   [Restringir os IPs de chamada](api-management-access-restriction-policies.md#RestrictCallerIPs) - Filtrar (permite/nega) chamadas de endereços IP específicos e/ou intervalos de endereços.  
    -   Definir quota de [utilização por subscrição](api-management-access-restriction-policies.md#SetUsageQuota) - Permite-lhe impor uma quota de chamada renovável ou vitalícia e/ou largura de banda, por subscrição.  
    -   Definir a quota de [utilização por chave](api-management-access-restriction-policies.md#SetUsageQuotaByKey) - Permite-lhe impor uma quota de chamada renovável ou vitalícia e/ou largura de banda, por base chave.  
    -   [Validar jWT](api-management-access-restriction-policies.md#ValidateJWT) - Aplica a existência e validade de um JWT extraído de um cabeçalho HTTP especificado ou de um parâmetro de consulta especificado.  
-   [Políticas avançadas](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Fluxo de controlo](api-management-advanced-policies.md#choose) - Aplica condicionalmente declarações políticas baseadas na avaliação das expressões booleanas.  
    -   [Pedido para a frente](api-management-advanced-policies.md#ForwardRequest) - Reencaminha o pedido para o serviço backend.
    -   [Limite a conmoeda](api-management-advanced-policies.md#LimitConcurrency) - Impede que as políticas fechadas executem por mais do que o número especificado de pedidos de cada vez.
    -   [Iniciar sessão no Event Hub](api-management-advanced-policies.md#log-to-eventhub) - Envia mensagens no formato especificado para um alvo de mensagem definido por uma entidade Logger.
    -   [Resposta falsa](api-management-advanced-policies.md#mock-response) - Aborta a execução do gasoduto e devolve uma resposta ridicularizada diretamente ao chamador.
    -   [Retry](api-management-advanced-policies.md#Retry) - Retry execução das declarações políticas fechadas, se e até que a condição seja cumprida. A execução repetir-se-á nos intervalos de tempo especificados e até à contagem de repetição especificada.  
    -   [Resposta de devolução](api-management-advanced-policies.md#ReturnResponse) - Aborta a execução do gasoduto e devolve a resposta especificada diretamente ao chamador.  
    -   [Enviar um pedido](api-management-advanced-policies.md#SendOneWayRequest) de ida - Envia um pedido para o URL especificado sem esperar por uma resposta.  
    -   [Enviar pedido](api-management-advanced-policies.md#SendRequest) - Envia um pedido para o URL especificado.
    -   [Definir proxy HTTP](api-management-advanced-policies.md#SetHttpProxy) - Permite-lhe encaminhar pedidos encaminhadas através de um proxy HTTP.
    -   [Variável definida](api-management-advanced-policies.md#set-variable) - Persistir um valor numa variável de contexto nomeado para acesso posterior.  
    -   [Definir método](api-management-advanced-policies.md#SetRequestMethod) de pedido - Permite-lhe alterar o método HTTP para um pedido.  
    -   [Definir código](api-management-advanced-policies.md#SetStatus) de estado - Altera o código de estado HTTP para o valor especificado.  
    -   [Trace](api-management-advanced-policies.md#Trace) - Adiciona vestígios personalizados na saída do [Inspetor API,](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) telemetria de Insights de Aplicação e Registos de Recursos.  
    -   [Aguardar](api-management-advanced-policies.md#Wait) - Aguarda por pedido de [envio](api-management-advanced-policies.md#SendRequest)em anexo , [Obter valor da cache](api-management-caching-policies.md#GetFromCacheByKey), ou controlar as políticas de [fluxo](api-management-advanced-policies.md#choose) para completar antes de prosseguir.  
-   [Políticas de autenticação](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Autenticar com Básico](api-management-authentication-policies.md#Basic) - Autenticar com um serviço backend utilizando a autenticação Básica.  
    -   [Autenticar com certificado de cliente](api-management-authentication-policies.md#ClientCertificate) - Autenticar com um serviço backend utilizando certificados de cliente.  
    -   [Autenticar com identidade gerida](api-management-authentication-policies.md#ManagedIdentity) - Autenticar com um serviço backend utilizando uma [identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
-   [Políticas de colocação em cache](api-management-caching-policies.md#CachingPolicies)  
    -   [Obtenha a partir da cache](api-management-caching-policies.md#GetFromCache) - Execute a cache procure e devolva uma resposta em cache válida quando disponível.  
    -   [Armazenar em cache](api-management-caching-policies.md#StoreToCache) - Resposta caches de acordo com a configuração especificada de controlo de cache.  
    -   [Obtenha valor a partir de cache](api-management-caching-policies.md#GetFromCacheByKey) - Recupere um item em cache por chave.  
    -   [Valor de loja em cache](api-management-caching-policies.md#StoreToCacheByKey) - Guarde um item na cache por chave.  
    -   [Retire o valor da cache](api-management-caching-policies.md#RemoveCacheByKey) - Retire um item na cache por chave.  
-   [Políticas entre domínios](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Permitir chamadas cruzadas](api-management-cross-domain-policies.md#AllowCrossDomainCalls) - Torna a API acessível a partir de clientes baseados no navegador Adobe Flash e Microsoft Silverlight.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) - Adiciona suporte de partilha de recursos de origem cruzada (CORS) a uma operação ou a uma API para permitir chamadas de domínio cruzado de clientes baseados no navegador.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) - Adiciona jSON com suporte de estofamento (JSONP) a uma operação ou a uma API para permitir chamadas cruzadas de clientes baseados no navegador JavaScript.  
-   [Políticas de transformação](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Converter JSON para XML](api-management-transformation-policies.md#ConvertJSONtoXML) - Converte o corpo de pedido ou resposta da JSON para xML.  
    -   [Converter XML para JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - Converte o corpo de pedido ou resposta de XML para JSON.  
    -   [Encontre e substitua](api-management-transformation-policies.md#Findandreplacestringinbody) a cadeia no corpo - Encontra um substring de pedido ou resposta e substitui-a por uma subcadeia diferente.  
    -   [Mask URLs em conteúdo](api-management-transformation-policies.md#MaskURLSContent) - Re-escreve (máscaras) ligações no corpo de resposta de modo a que apontem para o link equivalente através do gateway.  
    -   [Despfique](api-management-transformation-policies.md#SetBackendService) o serviço de backend - Altera o serviço de backend para um pedido de entrada.  
    -   [Definir o corpo](api-management-transformation-policies.md#SetBody) - Define o corpo da mensagem para pedidos de entrada e saída.  
    -   [set HTTP header](api-management-transformation-policies.md#SetHTTPheader) - Atribui um valor a uma resposta existente e/ou cabeçalho de pedido ou adiciona uma nova resposta e/ou cabeçalho de pedido.  
    -   [Definir parâmetro](api-management-transformation-policies.md#SetQueryStringParameter) de corda de consulta - Adiciona, substitui o valor de ou elimina o parâmetro de corda de pedido de consulta.  
    -   [Reescrever URL](api-management-transformation-policies.md#RewriteURL) - Converte um URL de pedido do seu formulário público para o formulário esperado pelo serviço web.  
    -   [Transforme o XML utilizando um XSLT](api-management-transformation-policies.md#XSLTransform) - Aplica uma transformação XSL ao XML no corpo de pedido ou resposta.  



## <a name="next-steps"></a>Passos seguintes
Para mais informações que trabalhem com políticas, consulte:

+ [Políticas em Gestão aPi](api-management-howto-policies.md)
+ [Transforme APIs](transform-api.md)
+ [Amostras políticas](policy-samples.md)   
