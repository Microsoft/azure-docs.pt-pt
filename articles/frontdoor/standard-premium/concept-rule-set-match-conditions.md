---
title: Configurar Azure Front Door Standard/Premium regra definida condições de jogo
description: Este artigo fornece uma lista das várias condições de jogo disponíveis com o conjunto de regras Azure Front Door Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4c65d0e7f80fab59ca7df4849df7117d482352c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100042"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Azure Front Door Standard/Premium (Pré-visualização) Regra Definida condições de jogo

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

Este tutorial mostra-lhe como criar um Conjunto de Regras com o seu primeiro conjunto de regras no portal Azure. No Conjunto de [Normas/Regras](concept-rule-set.md)Premium da Porta Frontal azul, uma regra consiste em zero ou mais condições de jogo e uma ação. Este artigo fornece descrições detalhadas das condições de jogo que pode utilizar no Conjunto de Regras Standard/Premium da Porta Frontal Azure.

A primeira parte de uma regra é uma condição de jogo ou um conjunto de condições de jogo. Uma regra pode consistir em até 10 condições de jogo. Uma condição de correspondência identifica tipos específicos de pedidos para os quais são feitas ações definidas. Se utilizar várias condições de correspondência, as condições de jogo são agrupadas utilizando a lógica E. Para todas as condições de correspondência que suportam múltiplos valores (notados como "separados pelo espaço"), assume-se o operador "OR".

Por exemplo, pode utilizar uma condição de correspondência para:

* Filtrar pedidos com base num endereço IP específico, país ou região.
* Filtrar os pedidos por informação do cabeçalho.
* Filtrar pedidos de dispositivos móveis ou dispositivos de ambiente de trabalho.
* Filtrar pedidos do nome do ficheiro de pedido e extensão do ficheiro.
* Filtrar pedidos de SOLICITAÇÃO, protocolo, caminho, cadeia de consulta, pós-args, etc.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

As seguintes condições de jogo estão disponíveis para utilizar no Conjunto de Regras Standard/Premium da porta frontal Azure:

## <a name="device-type"></a>Tipo de Dispositivo

Identifica os pedidos feitos a partir de um dispositivo móvel ou de um dispositivo de ambiente de trabalho.  

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Iguais, não iguais | Mobile, Desktop

## <a name="post-argument"></a>Pós-argumento

Identifica os pedidos com base em argumentos definidos para o método de pedido DOM que é utilizado no pedido.

#### <a name="required-fields"></a>Campos necessários

Nome do argumento | Operador | Valor do argumento | Transformação de caso
--------------|----------|----------------|---------------
String | [Lista de operadores](#operator-list) | String | Minúscula, Maiúscula

## <a name="query-string"></a>Cadeias de consulta

Identifica pedidos que contêm um parâmetro específico de cadeia de consulta. Este parâmetro é definido para um valor que corresponde a um padrão específico. Os parâmetros de cadeia de consulta (por exemplo, **parâmetro=valor)** no URL de pedido determinam se esta condição é cumprida. Esta condição de correspondência identifica um parâmetro de cadeia de consulta pelo seu nome e aceita um ou mais valores para o valor do parâmetro.

#### <a name="required-fields"></a>Campos necessários

Operador | Cadeias de consulta | Transformação de caso
---------|--------------|---------------
[Lista de operadores](#operator-list) | String | Minúscula, Maiúscula

## <a name="remote-address"></a>Endereço remoto

Identifica os pedidos com base na localização ou endereço IP do solicitador.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|-----------------
Geo Match | Indicativo do país
Jogo IP | Endereço IP (separado pelo espaço)
Não Geo Match | Indicativo do país
Não jogo IP | Endereço IP (separado pelo espaço)

#### <a name="key-information"></a>Informações-chave

* Utilize a notação CIDR.
* Para vários endereços IP e blocos de endereços IP, a lógica 'OR' é operada.
    * **Exemplo iPv4**: se adicionar dois endereços IP *1.2.3.4* e *10.20.30.40*, a condição é correspondida se quaisquer pedidos que cheguem de qualquer endereço 1.2.3.4 ou 10.20.30.40.
    * **Exemplo IPv6**: se adicionar dois endereços IP *1:2:3:4:5:6:7:8* e *10:20:30:40:50:60:70:80*, a condição é correspondida se quaisquer pedidos que cheguem de qualquer endereço 1:2:3:4:5:6:7:8 ou 10:20:40:50:60:70:80.
* A sintaxe para um bloco de endereços IP é o endereço IP base seguido de um corte dianteiro e o tamanho do prefixo. Por exemplo:
    * **IPv4 exemplo:** *5.5.5.64/26* corresponde a quaisquer pedidos que cheguem dos endereços 5.5.5.64 até 5.5.5.127.
    * **IPv6 exemplo**: *1:2:3:/48* corresponde a qualquer pedido que chegue de endereços 1:2:3:0:0:0:0:0:0:0:0:0:0:0:0:0:0:3: ffff:ff:ff:ff:ff:ff:ff.

## <a name="request-body"></a>Corpo do pedido

Identifica pedidos com base em texto específico que aparece no corpo do pedido.

#### <a name="required-fields"></a>Campos necessários

Operador | Corpo do pedido | Transformação de caso
---------|--------------|---------------
[Lista de operadores](#operator-list) | String | Minúscula, Maiúscula

## <a name="request-header"></a>Cabeçalho do pedido

Identifica os pedidos que utilizam um cabeçalho específico no pedido.

#### <a name="required-fields"></a>Campos necessários

Nome do cabeçalho | Operador | Valor do cabeçalho | Transformação de caso
------------|----------|--------------|---------------
String | [Lista de operadores](#operator-list) | String | Minúscula, Maiúscula

## <a name="request-method"></a>Método de pedido

Identifica os pedidos que utilizam o método de pedido especificado.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Iguais, não iguais | OBTER, POST, COLOCAR, APAGAR, CABEÇA, OPÇÕES, TRAÇO

#### <a name="key-information"></a>Informações-chave

Apenas o método de pedido GET pode gerar conteúdo em cache na Porta Frontal Azure. Todos os outros métodos de pedido são proxiemente através da rede.

## <a name="request-protocol"></a>Protocolo de pedido

Identifica os pedidos que utilizam o protocolo especificado utilizado.

#### <a name="required-fields"></a>Campos necessários

Operador | Valores suportados
---------|----------------
Iguais, não iguais | HTTP, HTTPS

## <a name="request-url"></a>URL do Pedido

Identifica pedidos que correspondam à URL especificada.

#### <a name="required-fields"></a>Campos necessários

Operador | URL do Pedido | Transformação de caso
---------|-------------|---------------
[Lista de operadores](#operator-list) | String | Minúscula, Maiúscula

#### <a name="key-information"></a>Informações-chave

Quando utilizar esta condição de regra, certifique-se de incluir informações de protocolo. Por exemplo: *https://www \<yourdomain\> . . com*.

## <a name="request-file-extension"></a>Pedido de extensão de ficheiro

Identifica os pedidos que incluem a extensão de ficheiro especificada no nome do ficheiro no URL solicitado.

#### <a name="required-fields"></a>Campos necessários

Operador | Extensão | Transformação de caso
---------|-----------|---------------
[Lista de operadores](#operator-list)  | String | Minúscula, Maiúscula

#### <a name="key-information"></a>Informações-chave

Para extensão, não inclua um período de liderança; por exemplo, use *html* em vez de *.html*.

## <a name="request-file-name"></a>Solicitar nome de ficheiro

Identifica os pedidos que incluem o nome de ficheiro especificado no URL de pedido.

#### <a name="required-fields"></a>Campos necessários

Operador | Nome de ficheiro | Transformação de caso
---------|-----------|---------------
[Lista de operadores](#operator-list)| String | Minúscula, Maiúscula

## <a name="request-path"></a>Caminho do pedido

Identifica os pedidos que incluem o caminho especificado na URL de pedido.

#### <a name="required-fields"></a>Campos necessários

Operador | Valor | Transformação de caso
---------|-------|---------------
[Lista de operadores](#operator-list) | String | Minúscula, Maiúscula

## <a name="operator-list"></a><a name = "operator-list"></a>Lista de operadores

Para as regras que aceitam valores da lista de operadores padrão, os seguintes operadores são válidos:

* Qualquer
* Igual a
* Contains
* Começa com
* Termina com
* Menor que
* Menos ou igual
* Maior que
* Maior do que ou igual
* Nem qualquer
* Não contém
* Não começa com
* Não termina com
* Não menos que
* Não menos do que ou iguais
* Não maior do que
* Não maior do que ou igual
* Expressão Regular

Para operadores numéricos como *Menos do que* e Maior do que ou *iguais,* a comparação utilizada baseia-se no comprimento. O valor na condição de jogo deve ser um inteiro que seja igual ao comprimento que se quer comparar.

## <a name="regular-expression"></a>Expressão Regular

A Regex não suporta as seguintes operações:

* Retroreferências e captura de subexpressões
* Afirmações arbitrárias de zero largura
* Referências subroutinas e padrões recursivos
* Padrões condicionais
* Verbos de controlo de retrocesso
* A diretiva \C single byte
* A diretiva \R newline match
* O início da diretiva de reset de correspondência \K
* Callouts e código incorporado
* Agrupamentos atómicos e quantificadores possessivos

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Conjunto de Regras.](concept-rule-set.md)
* Saiba como [configurar o seu primeiro Conjunto de Regras](how-to-configure-rule-set.md).
* Saiba mais sobre [as ações do Conjunto de Regras](concept-rule-set-actions.md).
