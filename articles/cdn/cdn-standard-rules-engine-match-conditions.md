---
title: Condições de jogo no motor de regras standard para Azure CDN | Microsoft Docs
description: Documentação de referência para as condições de correspondência no motor de regras padrão para a Rede de Entrega de Conteúdos Azure (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b8050b973027ac91ede0ba98f4d1c76831da9828
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "81259936"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Condições de jogo no motor de regras standard para Azure CDN

No [motor de regras Standard](cdn-standard-rules-engine.md) para Azure Content Delivery Network (Azure CDN), uma regra consiste numa ou mais condições de correspondência e numa ação. Este artigo fornece descrições detalhadas das condições de jogo que pode utilizar no motor de regras Standard para Azure CDN.

A primeira parte de uma regra é uma condição de jogo ou um conjunto de condições de jogo. No motor de regras Standard para Azure CDN, cada regra pode ter até quatro condições de jogo. Uma condição de correspondência identifica tipos específicos de pedidos para os quais são realizadas ações definidas. Se utilizar várias condições de correspondência, as condições de jogo são agrupadas utilizando a lógica E.

Por exemplo, pode utilizar uma condição de correspondência para:

- Filtrar pedidos com base num endereço IP específico, país ou região.
- Filtrar os pedidos por informação do cabeçalho.
- Filtrar pedidos de dispositivos móveis ou dispositivos de ambiente de trabalho.

## <a name="match-conditions"></a>Condições de jogo

As seguintes condições de jogo estão disponíveis para utilizar no motor de regras Standard para Azure CDN. 

### <a name="device-type"></a>Tipo de Dispositivo 

Identifica os pedidos feitos a partir de um dispositivo móvel ou de um dispositivo de ambiente de trabalho.  

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Iguais, não iguais | Mobile, Desktop

### <a name="http-version"></a>Versão HTTP

Identifica os pedidos com base na versão HTTP do pedido.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Iguais, não iguais | 2.0, 1.1, 1.0, 0.9, Todos

### <a name="request-cookies"></a>Solicitar cookies

Identifica pedidos com base em informações de cookies no pedido de entrada.

#### <a name="required-fields"></a>Campos necessários

Nome do cookie | Operador | Valor de cookies | Transformação de caso
------------|----------|--------------|---------------
String | [Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, para maiúscula, para minúsculas

#### <a name="key-information"></a>Informações-chave

- Não pode utilizar valores wildcard (incluindo asteriscos \* ()) quando especificar um nome de cookies; deve usar um nome de cookies exato.
- Você pode especificar apenas um único nome de cookie por instância desta condição de jogo.
- As comparações de nomes de cookies são insensíveis a casos.
- Para especificar vários valores de cookies, utilize um único espaço entre cada valor de cookie. 
- Os valores dos cookies podem tirar partido dos valores wildcard.
- Se um valor wildcard não tiver sido especificado, apenas uma correspondência exata satisfaz esta condição de jogo. Por exemplo, "Valor" corresponderá a "Valor" mas não "Valor1". 

### <a name="post-argument"></a>Pós-argumento

Identifica os pedidos com base em argumentos definidos para o método de pedido DOM que é utilizado no pedido. 

#### <a name="required-fields"></a>Campos necessários

Nome do argumento | Operador | Valor do argumento | Transformação de caso
--------------|----------|----------------|---------------
String | [Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, para maiúscula, para minúsculas

### <a name="query-string"></a>Cadeias de consulta

Identifica pedidos que contêm um parâmetro específico de cadeia de consulta. Este parâmetro é definido para um valor que corresponde a um padrão específico. Os parâmetros de cadeia de consulta (por exemplo, **parâmetro=valor)** no URL de pedido determinam se esta condição é cumprida. Esta condição de correspondência identifica um parâmetro de cadeia de consulta pelo seu nome e aceita um ou mais valores para o valor do parâmetro.

#### <a name="required-fields"></a>Campos necessários

Operador | Cadeias de consulta | Transformação de caso
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, para maiúscula, para minúsculas

### <a name="remote-address"></a>Endereço remoto

Identifica os pedidos com base na localização ou endereço IP do solicitador.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|-----------------
Qualquer | N/D
Geo Match | Indicativo do país
Jogo IP | Endereço IP (separado pelo espaço)
Não Qualquer | N/D
Não Geo Match | Indicativo do país
Não jogo IP | Endereço IP (separado pelo espaço)

#### <a name="key-information"></a>Informações-chave

- Utilize a notação CIDR.
- Para especificar vários endereços IP e blocos de endereços IP, utilize um espaço único entre os valores:
  - **Exemplo do IPv4:** *1.2.3.4 10.20.30.40* corresponde a quaisquer pedidos que cheguem de qualquer endereço 1.2.3.4 ou 10.20.30.40.
  - **IPv6 exemplo**: *1:2:3:4:5:6:7:8 10:20:30:40:60:70:80* corresponde a qualquer pedido que chegam de qualquer endereço 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.
- A sintaxe para um bloco de endereços IP é o endereço IP base seguido de um corte dianteiro e o tamanho do prefixo. Por exemplo:
  - **IPv4 exemplo:** *5.5.5.64/26* corresponde a quaisquer pedidos que cheguem dos endereços 5.5.5.64 até 5.5.5.127.
  - **IPv6 exemplo**: *1:2:3:/48* corresponde a qualquer pedido que chegue de endereços 1:2:3:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:0:01:2:3:ff:ff:ff:ff:ff:ff.ff:

### <a name="request-body"></a>Corpo do pedido

Identifica pedidos com base em texto específico que aparece no corpo do pedido.

#### <a name="required-fields"></a>Campos necessários

Operador | Corpo do pedido | Transformação de caso
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, para maiúscula, para minúsculas

### <a name="request-header"></a>Cabeçalho do pedido

Identifica os pedidos que utilizam um cabeçalho específico no pedido.

#### <a name="required-fields"></a>Campos necessários

Nome do cabeçalho | Operador | Valor do cabeçalho | Transformação de caso
------------|----------|--------------|---------------
String | [Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, para maiúscula, para minúsculas

### <a name="request-method"></a>Método de pedido

Identifica os pedidos que utilizam o método de pedido especificado.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Iguais, não iguais | OBTER, POST, COLOCAR, APAGAR, CABEÇA, OPÇÕES, TRAÇO

#### <a name="key-information"></a>Informações-chave

- Apenas o método de pedido GET pode gerar conteúdo em cache em Azure CDN. Todos os outros métodos de pedido são proxiemente através da rede. 

### <a name="request-protocol"></a>Protocolo de pedido

Identifica os pedidos que utilizam o protocolo especificado utilizado.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Iguais, não iguais | HTTP, HTTPS

### <a name="request-url"></a>URL do Pedido

Identifica pedidos que correspondam à URL especificada.

#### <a name="required-fields"></a>Campos necessários

Operador | URL do Pedido | Transformação de caso
---------|-------------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, para maiúscula, para minúsculas

#### <a name="key-information"></a>Informações-chave

- Quando utilizar esta condição de regra, certifique-se de incluir informações de protocolo. Por exemplo: *https://www \<yourdomain\> . . com*.

### <a name="url-file-extension"></a>Extensão de ficheiro URL

Identifica os pedidos que incluem a extensão de ficheiro especificada no nome do ficheiro no URL solicitado.

#### <a name="required-fields"></a>Campos necessários

Operador | Extensão | Transformação de caso
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, para maiúscula, para minúsculas

#### <a name="key-information"></a>Informações-chave

- Para extensão, não inclua um período de liderança; por exemplo, use *html* em vez de *.html*.

### <a name="url-file-name"></a>Nome do ficheiro URL

Identifica os pedidos que incluem o nome de ficheiro especificado no URL de pedido.

#### <a name="required-fields"></a>Campos necessários

Operador | Nome de ficheiro | Transformação de caso
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, para maiúscula, para minúsculas

#### <a name="key-information"></a>Informações-chave

- Para especificar vários nomes de ficheiros, separe cada nome de ficheiro com um único espaço. 

### <a name="url-path"></a>Caminho url

Identifica os pedidos que incluem o caminho especificado na URL de pedido.

#### <a name="required-fields"></a>Campos necessários

Operador | Valor | Transformação de caso
---------|-------|---------------
[Lista de operadores padrão](#standard-operator-list) | String | Sem transformação, para maiúscula, para minúsculas

#### <a name="key-information"></a>Informações-chave

- Um valor de nome de ficheiro pode tirar partido dos valores wildcard. Por exemplo, cada padrão de nome de ficheiro pode consistir em um ou mais asteriscos (*), onde cada asterisco corresponde a uma sequência de um ou mais caracteres.

## <a name="reference-for-rules-engine-match-conditions"></a>Referência para regras condições de correspondência do motor

### <a name="standard-operator-list"></a>Lista de operadores padrão

Para as regras que aceitam valores da lista de operadores padrão, os seguintes operadores são válidos:

- Qualquer
- Igual a 
- Contains 
- Começa com 
- Termina com 
- Menor que
- Menos ou igual
- Maior que
- Maior do que ou igual
- Nem qualquer
- Não contém
- Não começa com 
- Não termina com 
- Não menos que
- Não menos do que ou iguais
- Não maior do que
- Não maior do que ou igual

Para operadores numéricos como *Menos do que* e Maior do que ou *iguais,* a comparação utilizada baseia-se no comprimento. Neste caso, o valor na condição de jogo deve ser um número inteiro que seja igual ao comprimento que se quer comparar. 

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do Azure CDN](cdn-overview.md)
- [Referência do Motor de regras standard](cdn-standard-rules-engine-reference.md)
- [Ações no motor de regras Standard](cdn-standard-rules-engine-actions.md)
- [Impor HTTPS com o Motor de regras standard](cdn-standard-rules-engine.md)
