---
title: Condições de jogo no motor standard regras para Azure CDN [ Microsoft Docs
description: Documentação de referência para as condições de correspondência no motor de regras Standard para a Rede de Entrega de Conteúdos Azure (Azure CDN).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 425266e2a7ca42bb17ca598ddfc2f2b86591f32e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900183"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Condições de jogo no motor standard regras para Azure CDN

No [motor standard regras](cdn-standard-rules-engine.md) para a Rede de Entrega de Conteúdos Azure (Azure CDN), uma regra consiste em uma ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das condições de correspondência que pode utilizar no motor standard regras para O CDN Azure.

A primeira parte de uma regra é uma condição de jogo ou um conjunto de condições de jogo. No motor standard regras para O CDN Azure, cada regra pode ter até quatro condições de correspondência. Uma condição de correspondência identifica tipos específicos de pedidos para os quais são executadas ações definidas. Se utilizar várias condições de jogo, as condições de jogo são agrunadas utilizando e lógica.

Por exemplo, pode utilizar uma condição de correspondência para:

- Filtrar pedidos com base num endereço IP específico, país ou região.
- Filtrar pedidos por informação do cabeçalho.
- Filtrar pedidos de dispositivos móveis ou dispositivos de secretária.

## <a name="match-conditions"></a>Condições de jogo

As seguintes condições de jogo estão disponíveis para utilização no motor standard regras para O CDN Azure. 

### <a name="device-type"></a>Tipo de Dispositivo 

Identifica pedidos feitos a partir de um dispositivo móvel ou de um dispositivo de secretária.  

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Igual, não igual | Mobile, Desktop

### <a name="http-version"></a>Versão HTTP

Identifica pedidos com base na versão HTTP do pedido.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Igual, não igual | 2.0, 1.1, 1.0, 0.9, Tudo

### <a name="request-cookies"></a>Solicitar cookies

Identifica pedidos com base em informações de cookies no pedido de entrada.

#### <a name="required-fields"></a>Campos necessários

Nome do cookie | Operador | Valor de cookies | Transformação de caso
------------|----------|--------------|---------------
Cadeia | [Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, para minúsculas

#### <a name="key-information"></a>Informação chave

- Não pode utilizar valores wildcard (incluindo asteriscos ())\*quando especifica um nome de cookie; você deve usar um nome de cookie exato.
- Pode especificar apenas um nome de cookie por exemplo desta condição de correspondência.
- As comparações de nomes de cookies são insensíveis aos casos.
- Para especificar vários valores de cookies, utilize um único espaço entre cada valor de cookie. 
- Os valores dos cookies podem tirar partido dos valores wildcard.
- Se não tiver sido especificado um valor wildcard, apenas uma correspondência exata satisfaz esta condição de jogo. Por exemplo, "Valor" corresponderá a "Valor" mas não a "Valor1". 

### <a name="post-argument"></a>Argumento pós

Identifica pedidos com base em argumentos definidos para o método de pedido do POST que é usado no pedido. 

#### <a name="required-fields"></a>Campos necessários

Nome do argumento | Operador | Valor do argumento | Transformação de caso
--------------|----------|----------------|---------------
Cadeia | [Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, para minúsculas

### <a name="query-string"></a>Cadeias de consulta

Identifica pedidos que contêm um parâmetro específico de corda de consulta. Este parâmetro está definido para um valor que corresponde a um padrão específico. Os parâmetros de cadeia de consulta (por exemplo, **parâmetro=valor**) no URL de pedido determinam se esta condição é satisfeita. Esta condição de correspondência identifica um parâmetro de corda de consulta pelo seu nome e aceita um ou mais valores para o valor do parâmetro.

#### <a name="required-fields"></a>Campos necessários

Operador | Cadeias de consulta | Transformação de caso
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, para minúsculas

### <a name="remote-address"></a>Endereço remoto

Identifica pedidos com base na localização do solicitado ou endereço IP.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|-----------------
Qualquer | N/D
Geo Match | Código de país
Jogo IP | Endereço IP (separado sem espaço)
Não Qualquer | N/D
Não Geo Match | Código de país
Não jogo IP | Endereço IP (separado sem espaço)

#### <a name="key-information"></a>Informação chave

- Utilize notação CIDR.
- Para especificar vários endereços IP e blocos de endereços IP, utilize um único espaço entre os valores:
  - **Exemplo iPv4**: *1.2.3.4 10.20.30.40* corresponde a quaisquer pedidos que cheguem de ambos os endereços 1.2.3.4 ou 10.20.30.40.
  - **Exemplo do IPv6:** *1:2:3:4:5:6:7:8 10:20:40:50:60:70:80* corresponde a quaisquer pedidos que cheguem de ambos os endereços 1:2:3:4:5:7:8 ou 10:20:40:50:60:70:70:80.
- A sintaxe para um bloco de endereçoIP é o endereço IP base seguido de uma barra para a frente e o tamanho do prefixo. Por exemplo:
  - **Exemplo iPv4**: *5.5.5.64/26* corresponde a quaisquer pedidos que cheguem dos endereços 5.5.5.64 a 5.5.5.127.
  - **Exemplo iPv6**: *1:2:3:/48* corresponde a qualquer pedido que chegue dos endereços 1:2:3:0:0:0:0:0:0:0 a 1:2:3:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ff

### <a name="request-body"></a>Corpo do pedido

Identifica pedidos com base em texto específico que aparece no corpo do pedido.

#### <a name="required-fields"></a>Campos necessários

Operador | Corpo do pedido | Transformação de caso
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, para minúsculas

### <a name="request-header"></a>Cabeçalho do pedido

Identifica pedidos que usam um cabeçalho específico no pedido.

#### <a name="required-fields"></a>Campos necessários

Nome do cabeçalho | Operador | Valor do cabeçalho | Transformação de caso
------------|----------|--------------|---------------
Cadeia | [Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, para minúsculas

### <a name="request-method"></a>Método de pedido

Identifica pedidos que utilizam o método de pedido especificado.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Igual, não igual | OBTER, POST, COLOCAR, APAGAR, CABEÇA, OPÇÕES, RASTREAR

#### <a name="key-information"></a>Informação chave

- Apenas o método de pedido GET pode gerar conteúdo em cache em Azure CDN. Todos os outros métodos de pedido são proxid através da rede. 

### <a name="request-protocol"></a>Protocolo de pedido

Identifica pedidos que utilizam o protocolo especificado utilizado.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Igual, não igual | HTTP, HTTPS

### <a name="request-url"></a>URL do Pedido

Identifica pedidos que correspondam ao URL especificado.

#### <a name="required-fields"></a>Campos necessários

Operador | URL do Pedido | Transformação de caso
---------|-------------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, para minúsculas

#### <a name="key-information"></a>Informação chave

- Quando utilizar esta condição de regra, certifique-se de incluir informações protocolares. Por exemplo: *https://www.\<yourdomain\>.com*.

### <a name="url-file-extension"></a>Extensão de ficheiro URL

Identifica pedidos que incluem a extensão de ficheiro especificada no nome do ficheiro no URL solicitado.

#### <a name="required-fields"></a>Campos necessários

Operador | Extensão | Transformação de caso
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, para minúsculas

#### <a name="key-information"></a>Informação chave

- Para extensão, não inclua um período de liderança; por exemplo, use *html* em vez de *.html*.

### <a name="url-file-name"></a>Nome do ficheiro URL

Identifica pedidos que incluem o nome de ficheiro especificado no URL solicitado.

#### <a name="required-fields"></a>Campos necessários

Operador | Nome de ficheiro | Transformação de caso
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, para minúsculas

#### <a name="key-information"></a>Informação chave

- Para especificar vários nomes de ficheiros, separe cada nome de ficheiro com um único espaço. 

### <a name="url-path"></a>Caminho de URL

Identifica pedidos que incluem o caminho especificado no URL solicitado.

#### <a name="required-fields"></a>Campos necessários

Operador | Valor | Transformação de caso
---------|-------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, em maiúsculas, para minúsculas

#### <a name="key-information"></a>Informação chave

- Um valor de nome de ficheiro pode tirar partido dos valores wildcard. Por exemplo, cada padrão de nome de ficheiro pode consistir em um ou mais asteriscos (*), onde cada asterisco corresponde a uma sequência de um ou mais caracteres.

## <a name="reference-for-rules-engine-match-conditions"></a>Referência para regras condições de correspondência do motor

### <a name="standard-operator-list"></a>Lista de operadores padrão

Para regras que aceitam valores da lista de operadores padrão, os seguintes operadores são válidos:

- Qualquer
- É igual a 
- Contains 
- Começa com 
- Termina com 
- Menor do que
- Menos ou iguais
- Maior que
- Maior ou igual
- Não é qualquer
- Não contém
- Não começa com 
- Não termina com 
- Não menos do que
- Não menos do que ou igual
- Não maior do que
- Não maior do que ou igual

Para operadores numéricos como *Menos do que* e Maior *esquecê-lo ou iguais,* a comparação utilizada baseia-se no comprimento. Neste caso, o valor na condição de jogo deve ser um inteiro que seja igual ao comprimento que quer comparar. 

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do CDN azure](cdn-overview.md)
- [Referência do Motor de regras standard](cdn-standard-rules-engine-reference.md)
- [Ações no motor standard regras](cdn-standard-rules-engine-actions.md)
- [Impor HTTPS com o Motor de regras standard](cdn-standard-rules-engine.md)
