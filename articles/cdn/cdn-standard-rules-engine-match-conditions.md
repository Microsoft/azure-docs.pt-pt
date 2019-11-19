---
title: Condições de correspondência no mecanismo de regras padrão para a CDN do Azure | Microsoft Docs
description: Documentação de referência para condições de correspondência no mecanismo de regras padrão para a rede de distribuição de conteúdo do Azure (CDN do Azure).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: c4c2b1f334e37691655b18d2c629fbd8edc95382
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171597"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Condições de correspondência no mecanismo de regras padrão para a CDN do Azure

No [mecanismo de regras padrão](cdn-standard-rules-engine.md) para a rede de distribuição de conteúdo do Azure (CDN do Azure), uma regra consiste em uma ou mais condições de correspondência e uma ação. Este artigo fornece descrições detalhadas das condições de correspondência que você pode usar no mecanismo de regras padrão para a CDN do Azure.

A primeira parte de uma regra é uma condição de correspondência ou um conjunto de condições de correspondência. No mecanismo de regras padrão para a CDN do Azure, cada regra pode ter até quatro condições de correspondência. Uma condição de correspondência identifica tipos específicos de solicitações para as quais as ações definidas são executadas. Se você usar várias condições de correspondência, as condições de correspondência serão agrupadas em conjunto usando AND lógica.

Por exemplo, você pode usar uma condição de correspondência para:

- Filtrar solicitações com base em um endereço IP, país ou região específico.
- Filtrar solicitações por informações de cabeçalho.
- Filtrar solicitações de dispositivos móveis ou dispositivos de desktop.

## <a name="match-conditions"></a>Condições de correspondência

As seguintes condições de correspondência estão disponíveis para uso no mecanismo de regras padrão para a CDN do Azure. 

### <a name="device-type"></a>Tipo de dispositivo 

Identifica solicitações feitas de um dispositivo móvel ou dispositivo de desktop.  

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores suportados
---------|----------------
Equals, não é igual a | Móvel, área de trabalho

### <a name="http-version"></a>Versão de HTTP

Identifica solicitações com base na versão HTTP da solicitação.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores suportados
---------|----------------
Equals, não é igual a | 2,0, 1,1, 1,0, 0,9, todos

### <a name="request-cookies"></a>Solicitar cookies

Identifica solicitações com base nas informações de cookie na solicitação de entrada.

#### <a name="required-fields"></a>Campos obrigatórios

Nome do cookie | Operador | Valor do cookie | Transformação de caso
------------|----------|--------------|---------------
Cadeia | [Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

#### <a name="key-information"></a>Informações de chave

- Você não pode usar valores curinga (incluindo asteriscos (\*)) ao especificar um nome de cookie; Você usa um nome de cookie exato.
- Você pode especificar apenas um único nome de cookie por instância dessa condição de correspondência.
- Comparações de nome de cookie diferenciam maiúsculas de minúsculas.
- Para especificar vários valores de cookie, use um único espaço entre cada valor de cookie. 
- Os valores de cookie podem tirar proveito dos valores curinga.
- Se um valor curinga não tiver sido especificado, apenas uma correspondência exata atenderá a essa condição de correspondência. Por exemplo, "value" corresponderá a "value", mas não a "value1". 

### <a name="post-argument"></a>Argumento post

Identifica solicitações com base em argumentos definidos para o método POST Request que é usado na solicitação. 

#### <a name="required-fields"></a>Campos obrigatórios

Nome do argumento | Operador | Valor do argumento | Transformação de caso
--------------|----------|----------------|---------------
Cadeia | [Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

### <a name="query-string"></a>Cadeia de consulta

Identifica solicitações que contêm um parâmetro de cadeia de caracteres de consulta específico. Esse parâmetro é definido como um valor que corresponde a um padrão específico. Os parâmetros de cadeia de caracteres de consulta (por exemplo, **parâmetro = valor**) na URL da solicitação determinam se essa condição é atendida. Essa condição de correspondência identifica um parâmetro de cadeia de caracteres de consulta por seu nome e aceita um ou mais valores para o valor do parâmetro.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Cadeia de consulta | Transformação de caso
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

### <a name="remote-address"></a>Endereço remoto

Identifica solicitações com base no local ou endereço IP do solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores suportados
---------|-----------------
Qualquer | N/D
Correspondência geográfica | Indicativo do país
Correspondência de IP | Endereço IP (separado por espaços)
Não qualquer | N/D
Não correspondência geográfica | Indicativo do país
Não correspondência de IP | Endereço IP (separado por espaços)

#### <a name="key-information"></a>Informações de chave

- Use a notação CIDR.
- Para especificar vários endereços IP e blocos de endereço IP, use um único espaço entre os valores:
  - **Exemplo de IPv4**: *1.2.3.4 10.20.30.40* corresponde a todas as solicitações que chegam de um endereço 1.2.3.4 ou 10.20.30.40.
  - **Exemplo de IPv6**: *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:8*0 corresponde a todas as solicitações que chegam de um dos endereços 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.
- A sintaxe de um bloco de endereço IP é o endereço IP base seguido por uma barra e o tamanho do prefixo. Por exemplo:
  - **Exemplo de IPv4**: *5.5.5.64/26* corresponde a todas as solicitações que chegam de endereços 5.5.5.64 por meio de 5.5.5.127.
  - **Exemplo de IPv6**: *1:2:3:/48* corresponde a todas as solicitações que chegam de endereços 1:2:3:0:0:0:0:0 a 1:2: 3: ffff: ffff: ffff: ffff: ffff.

### <a name="request-body"></a>Corpo do pedido

Identifica solicitações com base em um texto específico que aparece no corpo da solicitação.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Corpo do pedido | Transformação de caso
---------|--------------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

### <a name="request-header"></a>Cabeçalho do pedido

Identifica as solicitações que usam um cabeçalho específico na solicitação.

#### <a name="required-fields"></a>Campos obrigatórios

Nome do cabeçalho | Operador | Valor do cabeçalho | Transformação de caso
------------|----------|--------------|---------------
Cadeia | [Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

### <a name="request-method"></a>Método de solicitação

Identifica as solicitações que usam o método de solicitação especificado.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores suportados
---------|----------------
Equals, não é igual a | OBTER, POSTAR, COLOCAR, EXCLUIR, CABEÇALHO, OPÇÕES, RASTREAMENTO

#### <a name="key-information"></a>Informações de chave

- Somente o método GET Request pode gerar conteúdo armazenado em cache na CDN do Azure. Todos os outros métodos de solicitação são proxies por meio da rede. 

### <a name="request-protocol"></a>Protocolo de solicitação

Identifica as solicitações que usam o protocolo especificado usado.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valores suportados
---------|----------------
Equals, não é igual a | HTTP, HTTPS

### <a name="request-url"></a>URL do Pedido

Identifica as solicitações que correspondem à URL especificada.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | URL do Pedido | Transformação de caso
---------|-------------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

#### <a name="key-information"></a>Informações de chave

- Ao usar essa condição de regra, certifique-se de incluir informações de protocolo. Por exemplo: *https://www.\<yourdomain\>.com* .

### <a name="url-file-extension"></a>Extensão de arquivo de URL

Identifica solicitações que incluem a extensão de arquivo especificada no nome do arquivo na URL solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Extensão | Transformação de caso
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

#### <a name="key-information"></a>Informações de chave

- Para extensão, não inclua um ponto à esquerda; por exemplo, use *HTML* em vez de *. html*.

### <a name="url-file-name"></a>Nome do arquivo da URL

Identifica solicitações que incluem o nome de arquivo especificado na URL solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Nome de ficheiro | Transformação de caso
---------|-----------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

#### <a name="key-information"></a>Informações de chave

- Para especificar vários nomes de arquivo, separe cada nome de arquivo com um único espaço. 

### <a name="url-path"></a>Caminho da URL

Identifica solicitações que incluem o caminho especificado na URL solicitante.

#### <a name="required-fields"></a>Campos obrigatórios

Operador | Valor | Transformação de caso
---------|-------|---------------
[Lista de operadores padrão](#standard-operator-list) | Cadeia de caracteres, int | Nenhuma transformação, para letras maiúsculas, para minúsculas

#### <a name="key-information"></a>Informações de chave

- Um valor de nome de arquivo pode aproveitar os valores curinga. Por exemplo, cada padrão de nome de arquivo pode consistir em um ou mais asteriscos (*), onde cada asterisco corresponde a uma sequência de um ou mais caracteres.

## <a name="reference-for-rules-engine-match-conditions"></a>Referência para condições de correspondência do mecanismo de regras

### <a name="standard-operator-list"></a>Lista de operadores padrão

Para regras que aceitam valores da lista de operadores padrão, os seguintes operadores são válidos:

- Qualquer
- É igual a 
- Contains 
- Começa com 
- termina com 
- Menor que
- Menor ou igual a
- Maior que
- Maior ou igual a
- Não qualquer
- Não contém
- Não começa com 
- Não termina com 
- Não é menor que
- Não é menor ou igual a
- Não é maior que
- Não é maior ou igual a

Para operadores numéricos como *menor* que e *maior ou igual*a, a comparação usada é baseada no comprimento. Nesse caso, o valor na condição de correspondência deve ser um inteiro igual ao comprimento que você deseja comparar. 

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da CDN do Azure](cdn-overview.md)
- [Referência do mecanismo de regras padrão](cdn-standard-rules-engine-reference.md)
- [Ações no mecanismo de regras padrão](cdn-standard-rules-engine-actions.md)
- [Impor o HTTPS usando o mecanismo de regras padrão](cdn-standard-rules-engine.md)
