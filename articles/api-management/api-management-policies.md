---
title: Políticas de gestão da API Azure | Microsoft Docs
description: Saiba mais sobre as políticas disponíveis para utilização na Gestão de API do Azure. As políticas permitem ao editor alterar o comportamento da API através da configuração.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/17/2021
ms.author: apimpm
ms.openlocfilehash: e809efa9da32da5fe9ca296608c602e770f78265
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103562353"
---
# <a name="api-management-policies"></a>Políticas de API Management
Esta secção fornece uma referência para as seguintes políticas de Gestão da API. Para obter informações sobre políticas de adição e configuração, consulte [Políticas em Gestão de API.](api-management-howto-policies.md)

 As políticas são uma poderosa capacidade do sistema que permite ao editor alterar o comportamento da API através da configuração. As políticas são uma coleção de Declarações que são executadas sequencialmente a pedido ou resposta de uma API. As Declarações Populares incluem a conversão de formato de XML para JSON e a taxa de chamada limitando a quantidade de chamadas recebidas de um desenvolvedor. Muitas mais políticas estão disponíveis fora da caixa.

 As expressões de política podem ser utilizadas como valores de atributo ou valores de texto em qualquer uma das políticas de API Management, a menos que a política especifique o contrário. Algumas políticas, como [Fluxo de controlo](api-management-advanced-policies.md#choose) e [Definir variável](api-management-advanced-policies.md#set-variable), baseiam-se em expressões de política. Para obter mais informações, veja [Políticas avançadas](api-management-advanced-policies.md#AdvancedPolicies) e [Expressões de política](api-management-policy-expressions.md).

##  <a name="policies"></a><a name="ProxyPolicies"></a> Políticas

-   [Políticas de restrição de acesso](api-management-access-restriction-policies.md#AccessRestrictionPolicies)
    -   [Ver cabeçalho HTTP](api-management-access-restriction-policies.md#CheckHTTPHeader) - Executa a existência e/ou o valor de um cabeçalho HTTP.
    -   [Taxa de chamada limite por subscrição](api-management-access-restriction-policies.md#LimitCallRate) - Previne picos de utilização da API limitando a taxa de chamada, por subscrição.
    -   [Limitar a taxa de chamada por chave](api-management-access-restriction-policies.md#LimitCallRateByKey) - Previne picos de utilização da API limitando a taxa de chamada, por cada chave.
    -   [Restringir os IPs do chamador](api-management-access-restriction-policies.md#RestrictCallerIPs) - Filtros (permite/nega) chamadas de endereços IP específicos e/ou intervalos de endereços.
    -   [Definir quota de utilização por subscrição](api-management-access-restriction-policies.md#SetUsageQuota) - Permite-lhe impor um volume de chamada renovável ou vitalício e/ou quota de largura de banda, por subscrição.
    -   [Definir quota de utilização por chave](api-management-access-restriction-policies.md#SetUsageQuotaByKey) - Permite-lhe impor um volume de chamada renovável ou vitalício e/ou quota de largura de banda, por chave.
    -   [Validar JWT](api-management-access-restriction-policies.md#ValidateJWT) - Impõe a existência e validade de um JWT extraído de um cabeçalho HTTP especificado ou de um parâmetro de consulta especificado.
-   [Políticas avançadas](api-management-advanced-policies.md#AdvancedPolicies)
    -   [Fluxo de](api-management-advanced-policies.md#choose) controlo - Aplica, condicionalmente, declarações políticas baseadas na avaliação das expressões booleanas.
    -   [Pedido de encaminhar](api-management-advanced-policies.md#ForwardRequest) - Remete o pedido para o serviço de backend.
    -   [Limita a concordância](api-management-advanced-policies.md#LimitConcurrency) - Impede que as políticas em anexo executem mais do que o número especificado de pedidos de cada vez.
    -   [Iniciar sessão no Event Hub](api-management-advanced-policies.md#log-to-eventhub) - Envia mensagens no formato especificado para um alvo de mensagem definido por uma entidade Logger.
    -   [Resposta falsa](api-management-advanced-policies.md#mock-response) - Aborta a execução do gasoduto e devolve uma resposta ridícula diretamente ao autor da chamada.
    -   [Retry](api-management-advanced-policies.md#Retry) - Recauchutagem execução das declarações políticas em anexo, se e até que a condição seja satisfeita. A execução repetir-se-á nos intervalos de tempo especificados e até à contagem de repetição especificada.
    -   [Resposta de retorno](api-management-advanced-policies.md#ReturnResponse) - Aborta a execução do gasoduto e devolve a resposta especificada diretamente ao autor da chamada.
    -   [Enviar um pedido de ida](api-management-advanced-policies.md#SendOneWayRequest) - Envia um pedido para o URL especificado sem esperar por uma resposta.
    -   [Envio de pedido](api-management-advanced-policies.md#SendRequest) - Envia um pedido para a URL especificada.
    -   [Definir http proxy](api-management-advanced-policies.md#SetHttpProxy) - Permite-lhe encaminhar os pedidos reencaminhados através de um representante HTTP.
    -   [Variação definida](api-management-advanced-policies.md#set-variable) - Persa um valor numa variável de contexto nomeado para acesso posterior.
    -   [Definir método de pedido](api-management-advanced-policies.md#SetRequestMethod) - Permite-lhe alterar o método HTTP para um pedido.
    -   [Definir código de estado](api-management-advanced-policies.md#SetStatus) - Altera o código de estado HTTP para o valor especificado.
    -   [Trace](api-management-advanced-policies.md#Trace) - Adiciona vestígios personalizados na saída do [Inspetor API,](./api-management-howto-api-inspector.md) telemetria de Insights de Aplicação e Registos de Recursos.
    -   [Aguarde](api-management-advanced-policies.md#Wait) - Espera por pedido de [envio](api-management-advanced-policies.md#SendRequest)anexo, [Obtenha valor a partir de cache,](api-management-caching-policies.md#GetFromCacheByKey)ou políticas de [fluxo de controlo](api-management-advanced-policies.md#choose) para concluir antes de prosseguir.
-   [Políticas de autenticação](api-management-authentication-policies.md#AuthenticationPolicies)
    -   [Autenticar com Básico](api-management-authentication-policies.md#Basic) - Autenticar com um serviço de backend utilizando a autenticação Básica.
    -   [Autenticar com certificado de cliente](api-management-authentication-policies.md#ClientCertificate) - Autenticar com um serviço de backend utilizando certificados de cliente.
    -   [Autenticar com identidade gerida](api-management-authentication-policies.md#ManagedIdentity) - Autenticar com um serviço de backend utilizando uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md).
-   [Políticas de colocação em cache](api-management-caching-policies.md#CachingPolicies)
    -   [Obter de cache](api-management-caching-policies.md#GetFromCache) - Execute cache olhar para cima e devolva uma resposta em cache válida quando disponível.
    -   [Armazenar em cache](api-management-caching-policies.md#StoreToCache) - Resposta caches de acordo com a configuração de controlo de cache especificada.
    -   [Obtenha valor a partir de cache](api-management-caching-policies.md#GetFromCacheByKey) - Recupere um item em cache por chave.
    -   [Valor da loja em cache](api-management-caching-policies.md#StoreToCacheByKey) - Guarde um item na cache por chave.
    -   [Remover o valor da cache](api-management-caching-policies.md#RemoveCacheByKey) - Remova um item na cache por chave.
-   [Políticas entre domínios](api-management-cross-domain-policies.md#CrossDomainPolicies)
    -   [Permitir chamadas de domínio transversal](api-management-cross-domain-policies.md#AllowCrossDomainCalls) - Torna a API acessível a partir de clientes baseados no navegador Adobe Flash e Microsoft Silverlight.
    -   [CORS](api-management-cross-domain-policies.md#CORS) - Adiciona suporte de partilha de recursos de origem cruzada (CORS) a uma operação ou a uma API para permitir chamadas de domínio transversal de clientes baseados no navegador.
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) - Adiciona JSON com suporte de enchimento (JSONP) a uma operação ou a uma API para permitir chamadas de domínio transversal de clientes baseados no navegador JavaScript.
-   [Políticas de transformação](api-management-transformation-policies.md#TransformationPolicies)
    -   [Converter JSON em XML](api-management-transformation-policies.md#ConvertJSONtoXML) - Converte o pedido ou o corpo de resposta de JSON para XML.
    -   [Converter XML em JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - Converte o pedido ou o corpo de resposta de XML para JSON.
    -   [Encontrar e substituir a corda no corpo](api-management-transformation-policies.md#Findandreplacestringinbody) - Encontra um pedido ou sublagem de resposta e substitui-o por um sublamamento diferente.
    -   [Mascarar URLs em conteúdo](api-management-transformation-policies.md#MaskURLSContent) - Re-escreve links (máscaras) no corpo de resposta de modo a que eles apaguem para o link equivalente através do gateway.
    -   [Serviço de backend definido](api-management-transformation-policies.md#SetBackendService) - Altera o serviço de backend para um pedido de entrada.
    -   [Conjunto do corpo](api-management-transformation-policies.md#SetBody) - Define o corpo da mensagem para pedidos de entrada e saída.
    -   [Definir cabeçalho HTTP](api-management-transformation-policies.md#SetHTTPheader) - Atribui um valor a uma resposta existente e/ou solicitar cabeçalho ou adiciona uma nova resposta e/ou cabeçalho de pedido.
    -   [Definir parâmetro de cadeia de consulta](api-management-transformation-policies.md#SetQueryStringParameter) - Adiciona, substitui valor ou elimina parâmetro de cadeia de pedido.
    -   [Reescrever URL](api-management-transformation-policies.md#RewriteURL) - Converte um URL de pedido do seu formulário público para o formulário esperado pelo serviço web.
    -   [Transforme o XML utilizando um XSLT](api-management-transformation-policies.md#XSLTransform) - Aplica uma transformação XSL a XML no corpo de pedido ou resposta.
- [Políticas de integração da Dapr](api-management-dapr-policies.md)
    - [Enviar pedido para um serviço](api-management-dapr-policies.md#invoke) - usa o tempo de execução da Dapr para localizar e comunicar de forma fiável com um microserviço Dapr.
    -  [Enviar mensagem para pub/sub tópico](api-management-dapr-policies.md#pubsub) - usa o tempo de execução da Dapr para publicar uma mensagem para um tópico de Publicação/Subscreva.
    -  [Ligação de saída do gatilho](api-management-dapr-policies.md#bind) - usa o tempo de execução da Dapr para invocar um sistema externo através da ligação de saída.
- [Políticas de validação](validation-policies.md)
    - [Validar o conteúdo](validation-policies.md#validate-content) - Valida o tamanho ou esquema JSON de um órgão de pedido ou resposta contra o esquema da API.
. 
    - [Validar parâmetros](validation-policies.md#validate-parameters) - Valida o cabeçalho, consulta ou parâmetros de percurso do pedido contra o esquema da API.
    - [Validar cabeçalhos](validation-policies.md#validate-headers) - Valida os cabeçalhos de resposta contra o esquema da API.
    - [Validar código de estado](validation-policies.md#validate-status-code) - Valida os códigos de estado HTTP em respostas ao esquema API.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre as políticas, consulte:

+ [Políticas em Gestão de API](api-management-howto-policies.md)
+ [Transformar APIs](transform-api.md)
+ [Exemplos de Políticas](./policy-reference.md)
