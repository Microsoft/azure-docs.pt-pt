---
title: Configurar ações de regra padrão/premium da porta frontal configure
description: Este artigo fornece uma lista das várias ações que pode fazer com o conjunto de regras da Porta frontal Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c9995df0f292c5e528156a3280df5484db017fca
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100045"
---
# <a name="azure-front-door-standardpremium-rule-set-actions"></a>Azure Porta frontal Standard/Premium Regra Definir Ações

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

Um conjunto de [regras da porta](concept-rule-set.md) frontal Azure consiste em regras com uma combinação de condições e ações de jogo. Este artigo fornece uma descrição detalhada das ações que pode usar num Conjunto de Regras. A ação define o comportamento que é aplicado a um tipo de pedido que uma(s) condição de correspondência(s) identifica. Num Conjunto de Regras da Porta Frontal Azure, uma regra pode conter até cinco ações. A variável do servidor é suportada em todas as ações.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

As seguintes ações estão disponíveis para utilização no conjunto de regras da Porta Frontal Azure.  

## <a name="cache-expiration"></a>Expiração da cache

Utilize esta ação para substituir o valor do tempo de vida (TTL) do ponto final para pedidos que as regras correspondem às condições especificam.

### <a name="required-fields"></a>Campos necessários

A seguinte descrição aplica-se ao selecionar estes comportamentos de cache e a regra corresponde:

Comportamento da cache |  Description              
---------------|----------------
Cache de bypass | O conteúdo não está em cache.
Substituição | O valor TTL devolvido da sua origem é substituído com o valor especificado na ação. Este comportamento só será aplicado se a resposta for cacheable. Para o cabeçalho de resposta de controlo de cache com valores "sem cache", "privado", "não armazenar", a ação não será aplicável.
Conjunto se faltar | Se nenhum valor TTL for devolvido da sua origem, a regra define o TTL ao valor especificado na ação. Este comportamento só será aplicado se a resposta for cacheable. Para o cabeçalho de resposta de controlo de cache com valores "sem cache", "privado", "não armazenar", a ação não será aplicável.

### <a name="additional-fields"></a>Campos adicionais

Dias | Horas | Minutos | Segundos
-----|-------|---------|--------
int | int | int | int 

## <a name="cache-key-query-string"></a>Cadeia de consulta chave cache

Utilize esta ação para modificar a chave cache com base em cadeias de consulta.

### <a name="required-fields"></a>Campos necessários

A seguinte descrição aplica-se ao selecionar estes comportamentos e a regra corresponde:

Comportamento | Description
---------|------------
Incluir | As cadeias de consulta especificadas nos parâmetros são incluídas quando a chave de cache é gerada. 
Colocar em cache todos os URLs exclusivos | Cada URL único tem a sua própria chave cache. 
Excluir | As cadeias de consulta especificadas nos parâmetros são excluídas quando a tecla de cache é gerada.
Ignorar cadeias de consulta | As cordas de consulta não são consideradas quando a chave de cache é gerada. 

## <a name="modify-request-header"></a>Modificar o cabeçalho do pedido

Utilize esta ação para modificar os cabeçalhos que estão presentes nos pedidos enviados para a sua origem.

### <a name="required-fields"></a>Campos necessários

A seguinte descrição aplica-se ao selecionar estas ações e a regra corresponde:

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | O cabeçalho especificado no **nome cabeçalho** é adicionado ao pedido com o valor especificado. Se o cabeçalho já estiver presente, o valor é anexado ao valor existente. | String
Overwrite | O cabeçalho especificado no **nome cabeçalho** é adicionado ao pedido com o valor especificado. Se o cabeçalho já estiver presente, o valor especificado substitui o valor existente. | String
Eliminar | Se o cabeçalho especificado na regra estiver presente, o cabeçalho é apagado do pedido. | String

## <a name="modify-response-header"></a>Modificar o cabeçalho de resposta

Utilize esta ação para modificar cabeçalhos que estão presentes nas respostas devolvidas aos seus clientes.

### <a name="required-fields"></a>Campos necessários

A seguinte descrição aplica-se ao selecionar estas ações e a regra corresponde:

Ação | Nome do cabeçalho HTTP | Valor
-------|------------------|------
Acrescentar | O cabeçalho especificado no **nome cabeçalho** é adicionado à resposta utilizando o **Valor** especificado . Se o cabeçalho já estiver presente, **o valor** é anexado ao valor existente. | String
Overwrite | O cabeçalho especificado no **nome cabeçalho** é adicionado à resposta utilizando o **Valor** especificado . Se o cabeçalho já estiver presente, **o valor** substitui o valor existente. | String
Eliminar | Se o cabeçalho especificado na regra estiver presente, o cabeçalho é apagado da resposta. | String

## <a name="url-redirect"></a>Redirecionamento de URL

Use esta ação para redirecionar os clientes para um novo URL. 

### <a name="required-fields"></a>Campos necessários

Campo | Descrição 
------|------------
Tipo de redirecionamento | Selecione o tipo de resposta para voltar ao solicitador: Encontrado (302), Movido (301), Redirecionamento Temporário (307) e Redirecionamento Permanente (308).
Protocolo de redirecionamento | PEDIDO DE CORRESPONDÊNCIA, HTTP, HTTPS.
Anfitrião do destino | Selecione o nome de anfitrião para o quais pretende que o pedido seja redirecionado. Deixe em branco para preservar o hospedeiro que está a chegar.
Caminho de destino | Defina o caminho a utilizar no redirecionamento. Deixe em branco para preservar o caminho de entrada.  
Cadeias de consulta | Defina a cadeia de consulta utilizada no redirecionamento. Deixe em branco para preservar a cadeia de consulta de entrada. 
Fragmento de destino | Defina o fragmento para utilizar no redirecionamento. Deixe em branco para preservar o fragmento de entrada. 

## <a name="url-rewrite"></a>Reescrever URL

Use esta ação para reescrever o caminho de um pedido que está a caminho da sua origem.

### <a name="required-fields"></a>Campos necessários

Campo | Descrição 
------|------------
Padrão de origem | Defina o padrão de origem no caminho URL para substituir. Atualmente, o padrão de origem usa uma correspondência baseada em prefixos. Para combinar todos os caminhos de URL, utilize um corte para a frente **/** () como o valor do padrão de origem.
Destino | Defina o caminho de destino a utilizar na reescrita. O caminho de destino substitui o padrão de origem.
Preservar caminho inigualável | Se definido para **Sim,** o caminho restante após o padrão de origem é anexado à nova rota de destino. 

## <a name="server-variable"></a>Variável do servidor

### <a name="supported-variables"></a>Variáveis Apoiadas

| Nome da variável | Description                                                  |
| -------------------------- | :----------------------------------------------------------- |
| socket_ip                  | O endereço IP da ligação direta à borda da porta frontal Azure. Se o cliente usou um representante HTTP ou um equilibrador de carga para enviar o pedido, o valor do SocketIp é o endereço IP do proxy ou do equilibrador de carga. |
| client_ip                  | O endereço IP do cliente que fez o pedido original. Se houve um cabeçalho X-Forwarded-For no pedido, então o IP do Cliente é escolhido do mesmo. |
| client_port                | A porta IP do cliente que fez o pedido. |
| nome anfitrião                      | O nome do anfitrião no pedido do cliente. |
| geo_country                     | Indica o país/região de origem do solicitador através do seu código país/região. |
| http_method                | O método usado para fazer o pedido de URL. Por exemplo, GET ou POST. |
| http_version               | O protocolo de pedido. Normalmente HTTP/1.0, HTTP/1.1 ou HTTP/2.0. |
| query_string               | A lista de pares variáveis/valor que segue o "?" na URL solicitada. Exemplo: no pedido *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* , query_string valor será *id=123&título=fabrikam* |
| request_scheme             | O sistema de pedidos: http ou https. |
| request_uri                | O pedido original completo URI (com argumentos). Exemplo: no pedido *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* , request_uri valor será */article.aspx?id=123&título=fabrikam* |
| server_port                | A porta do servidor que aceitou um pedido. |
| ssl_protocol    | O protocolo de uma ligação TLS estabelecida. |
| url_path                   | Identifica o recurso específico no anfitrião a que o cliente web quer aceder. Esta é a parte do pedido URI sem os argumentos. Exemplo: no *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* pedido, uri_path valor será */artigo.aspx* |

### <a name="server-variable-format"></a>Formato variável do servidor    

**Formato:** {variável:offset}, {variable:offset:length}, {variable}

### <a name="supported-server-variable-actions"></a>Ações variáveis de servidor suportadas

* Cabeçalho do pedido
* Cabeçalho de resposta
* Cadeia de consulta chave cache
* Reescrever URL
* Redirecionamento de URL

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o conjunto de regras da porta dianteira Azure Stanard/Premium](concept-rule-set.md).
* Saiba mais sobre [as condições de jogo definidas pela regra](concept-rule-set-match-conditions.md).
