---
title: Condições de correspondência do motor de regras de CDN do Azure da Verizon Premium | Documentos da Microsoft
description: Condições de correspondência do motor de regras de documentação de referência para a rede entrega de conteúdos do Azure da Verizon Premium.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 1660dca34b2f128ef5889145fcdeed0d2523b9bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593209"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>A CDN do Azure do motor de regras de Premium da Verizon coincidam com condições

Este artigo apresenta uma lista de descrições detalhadas das condições de correspondência disponíveis para o conteúdo entrega de rede (CDN) do Azure Premium da Verizon [motor de regras](cdn-verizon-premium-rules-engine.md).

A segunda parte de uma regra é a condição de correspondência. Uma condição de correspondência identifica tipos específicos de pedidos para o qual um conjunto de recursos será executado.

Por exemplo, pode utilizar uma condição de correspondência para:

- Filtrar pedidos para o conteúdo numa localização específica.
- Filtrar pedidos gerados a partir de um determinado endereço IP ou país/região.
- Filtrar pedidos por informações de cabeçalho.

## <a name="always-match-condition"></a>Condição de correspondência sempre

A condição de correspondência sempre aplica-se um conjunto predefinido de funcionalidades para todos os pedidos.

Nome | Objetivo
-----|--------
[Sempre](#always) | Aplica-se de um conjunto predefinido de funcionalidades para todos os pedidos.

## <a name="device-match-condition"></a>Condição de correspondência de dispositivo

A condição de correspondência de dispositivo identifica os pedidos efetuados a partir de um dispositivo móvel com base nas respetivas propriedades.  

Nome | Objetivo
-----|--------
[dispositivo](#device) | Identifica os pedidos efetuados a partir de um dispositivo móvel com base nas respetivas propriedades.

## <a name="location-match-conditions"></a>Condições de correspondência de localização

As condições de correspondência de localização identificam solicitações com base na localização do autor do pedido.

Nome | Objetivo
-----|--------
[COMO número](#as-number) | Identifica os pedidos provenientes de uma rede específica.
[País](#country) | Identifica os pedidos que têm origem nos países/regiões especificados.

## <a name="origin-match-conditions"></a>Condições de correspondência de origem

As condições de correspondência de origem identificam pedidos que apontam para o armazenamento de rede de entrega de conteúdos ou um servidor de origem do cliente.

Nome | Objetivo
-----|--------
[Origem de CDN](#cdn-origin) | Identifica os pedidos para conteúdo armazenado no armazenamento de rede de entrega de conteúdos.
[Origem do cliente](#customer-origin) | Identifica os pedidos para o conteúdo armazenado num servidor de origem do cliente específico.

## <a name="request-match-conditions"></a>Condições de correspondência de pedido

As condições de correspondência de pedido de identificam pedidos com base nas respetivas propriedades.

Nome | Objetivo
-----|--------
[Endereço IP do cliente](#client-ip-address) | Identifica os pedidos provenientes de um endereço IP específico.
[Parâmetro de cookie](#cookie-parameter) | Verifica os cookies associados a cada solicitação para o valor especificado.
[Cookie parâmetro Regex](#cookie-parameter-regex) | Verifica os cookies associados a cada solicitação para a expressão regular especificada.
[Edge Cname](#edge-cname) | Identifica os pedidos que apontam para um limite específico CNAME.
[Domínio de referência](#referring-domain) | Identifica os pedidos que eram referidos dos nomes de anfitrião especificado.
[Literal de cabeçalho do pedido](#request-header-literal) | Identifica a solicitação que contenha o cabeçalho especificado definido como um valor especificado.
[Regex de cabeçalho do pedido](#request-header-regex) | Identifica a solicitação que contenha o cabeçalho especificado definido como um valor que corresponda à expressão regular especificada.
[Caráter universal de cabeçalho do pedido](#request-header-wildcard) | Identifica a solicitação que contenha o cabeçalho especificado definido como um valor que corresponde ao padrão especificado.
[Método de pedido](#request-method) | Identifica os pedidos pelo respetivo método HTTP.
[Esquema de pedido](#request-scheme) | Identifica pedidos através dos respetivos protocolos HTTP.

## <a name="url-match-conditions"></a>Condições de correspondência de URL

As condições de correspondência de URL identificam solicitações com base nos respetivos URLs.

Nome | Objetivo
-----|--------
[Diretório de caminho do URL](#url-path-directory) | Identifica os pedidos pelo seu caminho relativo.
[Extensão do caminho de URL](#url-path-extension) | Identifica os pedidos através da respetiva extensão de nome de ficheiro.
[Nome de ficheiro de caminho de URL](#url-path-filename) | Identifica os pedidos pelo respetivo nome de ficheiro.
[Literal de caminho do URL](#url-path-literal) | Compara o caminho relativo de um pedido para o valor especificado.
[Regex do caminho de URL](#url-path-regex) | Compara o caminho relativo de um pedido para a expressão regular especificada.
[Caminho de URL com carateres universais](#url-path-wildcard) | Compara o caminho relativo de um pedido para o padrão especificado.
[Literal de consulta de URL](#url-query-literal) | Compara a cadeia de caracteres de consulta de um pedido para o valor especificado.
[Parâmetro de consulta de URL](#url-query-parameter) | Identifica a solicitação que contenha o parâmetro de cadeia de caracteres de consulta especificada definido como um valor que corresponde a um padrão especificado.
[Regex de consulta de URL](#url-query-regex) | Identifica a solicitação que contenha o parâmetro de cadeia de caracteres de consulta especificada definido como um valor que corresponde a uma expressão regular especificada.
[Caráter universal de consulta de URL](#url-query-wildcard) | Compara o valor especificado em relação a cadeia de consulta da solicitação.

## <a name="reference-for-rules-engine-match-conditions"></a>Referência para condições de correspondência do motor de regras

---

### <a name="always"></a>Sempre

A condição de correspondência sempre aplica-se um conjunto predefinido de funcionalidades para todos os pedidos.

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>COMO número

A rede de número é definida por seu número de sistema autónomo (ASN). 

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o número corresponde à condição é cumprida:

- **Correspondências**: Requer que o ASN da rede de cliente corresponde a uma dos ASNs especificados. 
- **Não corresponde ao**: Requer que o ASN da rede de cliente não corresponde a nenhum dos ASNs especificados.

Informações da chave:

- Especifica ASNs vários, cada um com um único espaço de delimitação. Por exemplo, 64514 64515 corresponde solicitações que chegam de 64514 ou 64515.
- Determinados pedidos podem não devolver um ASN válido. Um ponto de interrogação (?) corresponderá ao pedidos para o qual não foi possível determinar um ASN válido.
- Especifique o ASN todo para a rede desejada. Não serão possível corresponder valores parciais.
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
  - Concluir o preenchimento de Cache
  - Predefinição interna Max-Age
  - Forçar a duração máxima interna
  - Ignorar a origem não-Cache
  - Max-obsoleta interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>Origem de CDN

A condição de correspondência de CDN Origin é cumprida quando ambas as condições seguintes forem cumpridas:

- Conteúdo do armazenamento CDN foi solicitado.
- O URI de solicitação utiliza o tipo de ponto de acesso ao conteúdo (por exemplo, /000001) que é definido nesta condição de correspondência:
  - CDN URL: O URI do pedido tem de conter o ponto de acesso ao conteúdo selecionado.
  - URL de CNAME do Edge: A configuração do CNAME edge correspondente tem de apontar para o ponto de acesso ao conteúdo selecionado.
  
Informações da chave:

- O ponto de acesso ao conteúdo identifica o serviço que deve servir os conteúdos solicitados.
- Não utilize uma instrução IF e combinar determinadas condições de correspondência. Por exemplo, a combinação de uma condição de correspondência de origem de CDN com uma condição de correspondência de origem do cliente criaria um padrão de correspondência que nunca foi possível corresponder. Por esse motivo, as duas condições de correspondência de origem de CDN não podem ser combinadas por meio de uma instrução IF e.

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Endereço IP do cliente

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o endereço IP do cliente corresponde à condição é cumprida:

- **Correspondências**: Requer que endereço IP do cliente corresponde a um dos endereços IP especificados. 
- **Não corresponde ao**: Requer que endereço IP do cliente não corresponde a nenhum dos endereços IP especificados. 

Informações da chave:

- Utilize a notação CIDR.
- Especifica vários endereços IP e/ou blocos de endereços IP por cada um com um único espaço de delimitação. Por exemplo:
  - **Exemplo de IPv4**: 1.2.3.4 10.20.30.40 corresponde a quaisquer pedidos que chegam de endereço 1.2.3.4 ou 10.20.30.40.
  - **Exemplo de IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 corresponde a quaisquer pedidos que chegam de endereço 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.
- A sintaxe de um bloco de endereços IP é o endereço IP base seguido por uma barra e o tamanho de prefixo. Por exemplo:
  - **Exemplo de IPv4**: 5.5.5.64/26 corresponde a quaisquer pedidos que chegam de endereços 5.5.5.64 por meio de 5.5.5.127.
  - **Exemplo de IPv6**: 1:2:3: / 48 corresponde a quaisquer pedidos que chegam de endereços 1:2:3:0:0:0:0:0 por meio de 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
  - Concluir o preenchimento de Cache
  - Predefinição interna Max-Age
  - Forçar a duração máxima interna
  - Ignorar a origem não-Cache
  - Max-obsoleta interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Parâmetro de cookie

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o parâmetro de Cookie corresponde à condição é cumprida.

- **Correspondências**: Requer um pedido para conter o cookie especificado com um valor que corresponde a, pelo menos, um dos valores definidos nesta condição de correspondência.
- **Não corresponde ao**: Requer que o pedido de atende a um dos seguintes critérios:
  - Não contém o cookie especificado.
  - Contém o cookie especificado, mas o respetivo valor não corresponde a nenhum dos valores definidos nesta condição de correspondência.
  
Informações da chave:

- Nome do cookie:
  - Uma vez que os valores de caráter universal, incluindo asteriscos (*), não são suportados quando especifica um nome de cookie, apenas correspondências de nome do cookie exata são elegíveis para comparação.
  - Pode ser especificado apenas um nome de cookie único por instância desta condição de correspondência.
  - Comparações de nome do cookie diferenciam maiúsculas de minúsculas.
- Valor do cookie:
  - Especifica vários valores de cookie, cada um com um único espaço de delimitação.
  - Um valor de cookie pode tirar partido das [valores de caráter universal](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Se não tiver sido especificado um valor de caráter universal, apenas uma correspondência exata satisfaz esta condição de correspondência. Por exemplo, a especificação de "Value" corresponderá "Valor", mas não "Value1" ou "Value2.."
  - Utilize o **ignorar caso** a opção de controle se uma comparação diferenciando maiúsculas de minúsculas é feita em relação a valor de cookie do pedido.
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
  - Concluir o preenchimento de Cache
  - Predefinição interna Max-Age
  - Forçar a duração máxima interna
  - Ignorar a origem não-Cache
  - Max-obsoleta interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Cookie parâmetro Regex

A condição de correspondência de Regex do parâmetro de Cookie define um nome do cookie e um valor. Pode usar [expressões regulares](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) para definir o valor do cookie pretendido.

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o Regex de parâmetro de Cookie corresponde à condição é cumprida.

- **Correspondências**: Requer um pedido para conter o cookie especificado com um valor que corresponda à expressão regular especificada.
- **Não corresponde ao**: Requer que o pedido de atende a um dos seguintes critérios:
  - Não contém o cookie especificado.
  - Contém o cookie especificado, mas o respetivo valor não corresponde à expressão regular especificada.
  
Informações da chave:

- Nome do cookie:
  - Uma vez que as expressões regulares e valores de caráter universal, incluindo asteriscos (*), não são suportados quando especifica um nome de cookie, apenas correspondências de nome do cookie exata são elegíveis para comparação.
  - Pode ser especificado apenas um nome de cookie único por instância desta condição de correspondência.
  - Comparações de nome do cookie diferenciam maiúsculas de minúsculas.
- Valor do cookie:
  - Um valor de cookie pode tirar partido das expressões regulares.
  - Utilize o **ignorar caso** a opção de controle se uma comparação diferenciando maiúsculas de minúsculas é feita em relação a valor de cookie do pedido.
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
  - Concluir o preenchimento de Cache
  - Predefinição interna Max-Age
  - Forçar a duração máxima interna
  - Ignorar a origem não-Cache
  - Max-obsoleta interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Country

Pode especificar um país por meio de seu código de país. 

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o país corresponde à condição é cumprida:

- **Correspondências**: Requer o pedido para conter os valores de código de país especificado. 
- **Não corresponde ao**: Requer que o pedido não contém os valores de código de país especificado.

Informações da chave:

- Especifica vários códigos de país, cada um com um único espaço de delimitação.
- Carateres universais não são suportadas quando especifica um código de país.
- Os códigos de país "EU" e "AP" não abranger todos os endereços IP nessas regiões.
- Determinados pedidos podem não devolver um código de país válido. Um ponto de interrogação (?) corresponderá ao pedidos para o qual não foi possível determinar um código de país válido.
- Códigos de país diferenciam maiúsculas de minúsculas.
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
  - Concluir o preenchimento de Cache
  - Predefinição interna Max-Age
  - Forçar a duração máxima interna
  - Ignorar a origem não-Cache
  - Max-obsoleta interno

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implementando a filtragem de país de mensagens em fila por meio do mecanismo de regras

Esta condição de correspondência permite que realize uma infinidade de personalizações com base na localização a partir do qual um pedido teve origem. Por exemplo, o comportamento da funcionalidade de filtragem de país pode ser replicado através da configuração seguinte:

- Correspondência de carateres universais do caminho de URL: Definir o [universal do caminho de URL corresponde à condição](#url-path-wildcard) para o diretório que esteja protegido. 
    Acrescente um asterisco ao final do caminho relativo para se certificar de que o acesso a todos os seus filhos irá ser restringido por esta regra.

- Correspondência de país: Defina a condição de correspondência de país para o conjunto pretendido de países.
  - Permitir: Definir a condição de correspondência de país **não corresponde ao** para permitir o acesso de países especificado ao conteúdo armazenado no local definido pela condição de correspondência de caminho de URL com carateres universais.
  - Bloco: Definir a condição de correspondência de país **correspondências** para bloquear os países especificados de aceder ao conteúdo armazenado no local definido pela condição de correspondência de caminho de URL com carateres universais.

- Negar a funcionalidade de acesso (403): Ativar a [funcionalidade de negar acesso (403)](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) para replicar a parte de permissões ou de bloqueios da funcionalidade de filtragem de país.

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Origem do cliente

Informações da chave:

- A condição de correspondência de origem do cliente é cumprida, independentemente se o conteúdo é solicitado por meio de um URL de CDN ou de uma extremidade URL de CNAME que aponta para a origem do cliente selecionado.
- Não é possível eliminar uma configuração de origem do cliente que é referenciada por uma regra a partir da página de origem do cliente. Antes de tentar eliminar uma configuração de origem do cliente, certifique-se de que as seguintes configurações não fazer referência a ele:
  - Uma condição de correspondência de origem do cliente
  - Uma configuração de CNAME do edge
- Não utilize uma instrução IF e combinar determinadas condições de correspondência. Por exemplo, a combinação de uma condição de correspondência de origem do cliente com uma condição de correspondência de CDN Origin criaria um padrão de correspondência que nunca foi possível corresponder. Por esse motivo, as duas condições de correspondência de origem do cliente não podem ser combinadas por meio de uma instrução IF e.

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Dispositivo

A condição de correspondência de dispositivo identifica os pedidos efetuados a partir de um dispositivo móvel com base nas respetivas propriedades. Deteção de dispositivos móveis é assegurada através da [WURFL](http://wurfl.sourceforge.net/). 

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o dispositivo corresponde à condição é cumprida:

- **Correspondências**: Requer que o dispositivo do autor do pedido para corresponder ao valor especificado. 
- **Não corresponde ao**: Requer que o dispositivo do autor do pedido não coincide com o valor especificado.

Informações da chave:

- Utilize o **ignorar caso** opção para especificar se o valor especificado diferencia maiúsculas de minúsculas.
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
  - Concluir o preenchimento de Cache
  - Predefinição interna Max-Age
  - Forçar a duração máxima interna
  - Ignorar a origem não-Cache
  - Max-obsoleta interno

#### <a name="string-type"></a>Tipo de cadeia

Um recurso do WURFL normalmente aceita qualquer combinação de números, letras e símbolos. Devido à natureza flexível desta capacidade, tem de escolher a forma como o valor associado esta condição de correspondência é interpretado. A tabela seguinte descreve o conjunto de disponibilidade de opções:

Type     | Descrição
---------|------------
Literal  | Selecione esta opção para impedir que a maioria dos caracteres significado especial com o respetivo [valor literal](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Caráter universal | Selecione esta opção para tirar partido de todas as [carateres universais] ([valores de caráter universal](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
RegEx    | Selecione esta opção para utilizar [expressões regulares](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Expressões regulares são úteis para a definição de um padrão de caracteres.

#### <a name="wurfl-capabilities"></a>Capacidades do WURFL

Um recurso do WURFL refere-se a uma categoria que descreve a dispositivos móveis. O recurso selecionado determina o tipo de descrição de dispositivo móvel que é utilizado para identificar pedidos.

A tabela seguinte lista as capacidades WURFL e as respetivas variáveis para o motor de regras.

> [!NOTE]
> As seguintes variáveis são suportadas no **modificar cabeçalho do pedido de cliente** e **modificar cabeçalho de resposta do cliente** funcionalidades.

Funcionalidade | Variável | Descrição | Valores de exemplo
-----------|----------|-------------|----------------
Nome da marca | %{wurfl_cap_brand_name} | Uma cadeia que indica o nome da marca do dispositivo. | Samsung
SO do dispositivo | %{wurfl_cap_device_os} | Uma cadeia que indica o sistema operativo instalado no dispositivo. | IOS
Versão de SO do dispositivo | %{wurfl_cap_device_os_version} | Uma cadeia que indica o número de versão do sistema operativo instalado no dispositivo. | 1.0.1
Orientação dupla | %{wurfl_cap_dual_orientation} | Um valor booleano que indica se o dispositivo oferece suporte à orientação dupla. | true
HTML preferencial DTD | %{wurfl_cap_html_preferred_dtd} | Uma cadeia que indica documento preferencial definição o dispositivo móvel (DTD) para o conteúdo HTML. | Nenhum<br/>xhtml_basic<br/>html5
Imagem Inlining | %{wurfl_cap_image_inlining} | Um valor booleano que indica se o dispositivo suporta Base64 codificado imagens. | false
Is Android | %{wurfl_vcap_is_android} | Um valor booleano que indica se o dispositivo utiliza o SO Android. | true
Is IOS | %{wurfl_vcap_is_ios} | Um valor booleano que indica se o dispositivo utiliza iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | Um valor booleano que indica se o dispositivo é uma smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | Um valor booleano que indica se o dispositivo é um smartphone. | true
É o Tablet | %{wurfl_cap_is_tablet} | Um valor booleano que indica se o dispositivo é um tablet. Esta descrição é independente de sistema operacional. | true
É dispositivo sem fio | %{wurfl_cap_is_wireless_device} | Um valor booleano que indica se o dispositivo é considerado um dispositivo sem fio. | true
Nome de marketing | %{wurfl_cap_marketing_name} | Uma cadeia que indica o nome do dispositivo marketing. | BlackBerry 8100 Pearl
Browser para dispositivos móveis | %{wurfl_cap_mobile_browser} | Uma cadeia que indica o navegador que é utilizado para pedir o conteúdo do dispositivo. | Chrome
Versão do Browser para dispositivos móveis | %{wurfl_cap_mobile_browser_version} | Uma cadeia que indica a versão do navegador que é utilizado para pedir o conteúdo do dispositivo. | 31
Nome do modelo | %{wurfl_cap_model_name} | Uma cadeia que indica o nome do modelo do dispositivo. | s3
Transferência progressiva | %{wurfl_cap_progressive_download} | Um valor booleano que indica se o dispositivo a suportar a reprodução de áudio e vídeo enquanto ele ainda está a ser transferido. | true
Data de lançamento | %{wurfl_cap_release_date} | Uma cadeia que indica o ano e mês em que o dispositivo foi adicionado para a base de dados do WURFL.<br/><br/>Formato: `yyyy_mm` | 2013_december
Altura da resolução | %{wurfl_cap_resolution_height} | Um número inteiro que indica a altura do dispositivo em pixéis. | 768
Largura de resolução | %{wurfl_cap_resolution_width} | Um número inteiro que indica a largura do dispositivo em pixéis. | 1024

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>Edge Cname

Informações da chave:

- A lista de extremidade disponível CNAMEs está limitada a esses CNAMEs edge que tenham sido configuradas na página de Edge CNAMEs para a plataforma na qual está a ser configurado o mecanismo de regras.
- Antes de tentar eliminar uma configuração de CNAME do edge, certifique-se de que uma condição de correspondência de Cname do Edge não fazer referência a ele. Configurações de CNAME do Edge que foram definidas numa regra não não possível eliminar a partir da página de Edge CNAMEs.
- Não utilize uma instrução IF e combinar determinadas condições de correspondência. Por exemplo, a combinação de uma condição de correspondência de Cname de borda com uma condição de correspondência de origem do cliente criaria um padrão de correspondência que nunca foi possível corresponder. Por esse motivo, as duas condições de correspondência de Cname do Edge não podem ser combinadas por meio de uma instrução IF e.
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
  - Concluir o preenchimento de Cache
  - Predefinição interna Max-Age
  - Forçar a duração máxima interna
  - Ignorar a origem não-Cache
  - Max-obsoleta interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Domínio de referência

O nome de anfitrião associados com o Referenciador por meio do qual o conteúdo foi solicitado determina se a condição de domínio que faz referência for cumprida.

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o domínio que faça referência corresponde à condição é cumprida:

- **Correspondências**: Requer o nome de anfitrião de referência de acordo com os valores especificados. 
- **Não corresponde ao**: Requer que o nome de anfitrião de referência não coincide com o valor especificado.

Informações da chave:

- Especifica vários nomes de anfitrião, cada um com um único espaço de delimitação.
- Esta condição de correspondência suporta [valores de caráter universal](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Se o valor especificado não contém um asterisco, tem de ser uma correspondência exata para o nome de anfitrião do referenciador. Por exemplo, especificar "mydomain.com" não corresponde a "www.mydomain.com."
- Utilize o **ignorar caso** a opção de controle se for feita uma comparação diferenciando maiúsculas de minúsculas.
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
  - Concluir o preenchimento de Cache
  - Predefinição interna Max-Age
  - Forçar a duração máxima interna
  - Ignorar a origem não-Cache
  - Max-obsoleta interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Literal de cabeçalho do pedido

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o pedido de cabeçalho Literal corresponde à condição é cumprida.

- **Correspondências**: Requer o pedido para conter o cabeçalho especificado. O valor tem de corresponder ao que é definida nesta condição de correspondência.
- **Não corresponde ao**: Requer que o pedido de atende a um dos seguintes critérios:
  - Não contém o cabeçalho especificado.
  - Contém o cabeçalho especificado, mas o respetivo valor não corresponde ao que é definida nesta condição de correspondência.
  
Informações da chave:

- Comparações de nome de cabeçalho sempre diferenciam maiúsculas de minúsculas. Utilize o **ignorar caso** opção para controlar a sensibilidade de comparações de valor de cabeçalho.
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
  - Concluir o preenchimento de Cache
  - Predefinição interna Max-Age
  - Forçar a duração máxima interna
  - Ignorar a origem não-Cache
  - Max-obsoleta interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Regex de cabeçalho do pedido

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o Regex de cabeçalho do pedido corresponde à condição é cumprida.

- **Correspondências**: Requer o pedido para conter o cabeçalho especificado. O valor tem de corresponder ao padrão que é definido no especificado [expressão regular](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Não corresponde ao**: Requer que o pedido de atende a um dos seguintes critérios:
  - Não contém o cabeçalho especificado.
  - Contém o cabeçalho especificado, mas o respetivo valor não corresponde à expressão regular especificada.

Informações da chave:

- Nome do cabeçalho:
  - Comparações de nome de cabeçalho diferenciam maiúsculas de minúsculas.
  - Substitua os espaços no nome do cabeçalho de "% 20".
- Valor do cabeçalho:
  - Um valor de cabeçalho pode tirar partido das expressões regulares.
  - Utilize o **ignorar caso** opção para controlar a sensibilidade de comparações de valor de cabeçalho.
  - A condição de correspondência é cumprida apenas quando um valor de cabeçalho corresponde exatamente a, pelo menos, um dos padrões especificados.
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
  - Concluir o preenchimento de Cache
  - Predefinição interna Max-Age
  - Forçar a duração máxima interna
  - Ignorar a origem não-Cache
  - Max-obsoleta interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Caráter universal de cabeçalho do pedido

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o curinga de cabeçalho do pedido corresponde à condição é cumprida.

- **Correspondências**: Requer o pedido para conter o cabeçalho especificado. O valor tem de corresponder, pelo menos, um dos valores definidos nesta condição de correspondência.
- **Não corresponde ao**: Requer que o pedido de atende a um dos seguintes critérios:
  - Não contém o cabeçalho especificado.
  - Contém o cabeçalho especificado, mas o respetivo valor não corresponde a nenhum dos valores especificados.
  
Informações da chave:

- Nome do cabeçalho:
  - Comparações de nome de cabeçalho diferenciam maiúsculas de minúsculas.
  - Espaços no nome do cabeçalho devem ser substituídos por "% 20". Também pode utilizar este valor para especificar espaços de um valor de cabeçalho.
- Valor do cabeçalho:
  - Um valor de cabeçalho pode tirar partido das [valores de caráter universal](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Utilize o **ignorar caso** opção para controlar a sensibilidade de comparações de valor de cabeçalho.
  - Esta condição de correspondência é preenchida quando um valor de cabeçalho corresponde exatamente para, pelo menos, um dos padrões especificados.
  - Especifica vários valores, cada um com um único espaço de delimitação.
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
  - Concluir o preenchimento de Cache
  - Predefinição interna Max-Age
  - Forçar a duração máxima interna
  - Ignorar a origem não-Cache
  - Max-obsoleta interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Método de pedido

A condição de correspondência do método de pedido é cumprida apenas quando os recursos são solicitados por meio do método de pedido seleccionado. Os métodos de pedido disponíveis são:

- GET
- HEAD
- POST
- OPÇÕES
- PUT
- DELETE
- RASTREIO
- LIGAR

Informações da chave:

- Por predefinição, apenas o método de solicitação GET pode gerar conteúdo em cache na rede. Todos os outros métodos de pedido são transmitidas por proxy através da rede.
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
  - Concluir o preenchimento de Cache
  - Predefinição interna Max-Age
  - Forçar a duração máxima interna
  - Ignorar a origem não-Cache
  - Max-obsoleta interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Esquema de pedido

A condição de correspondência do esquema do pedido é cumprida apenas quando os recursos são solicitados por meio do protocolo selecionado. Os protocolos disponíveis são:

- HTTP
- HTTPS

Informações da chave:

- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
  - Concluir o preenchimento de Cache
  - Predefinição interna Max-Age
  - Forçar a duração máxima interna
  - Ignorar a origem não-Cache
  - Max-obsoleta interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>Diretório de caminho do URL

Identifica um pedido ao seu caminho relativo, que exclui o nome do arquivo do recurso solicitado.

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o diretório de caminho de URL corresponde à condição é cumprida.

- **Correspondências**: Requer o pedido para conter um caminho relativo de URL, excluindo o nome de ficheiro, que corresponde ao padrão de URL especificado.
- **Não corresponde ao**: Requer o pedido para conter um caminho relativo de URL, excluindo o nome de ficheiro, que não corresponde ao padrão de URL especificado.

Informações da chave:

- Utilize o **relativamente à** opção para especificar se a comparação de URL começa antes ou depois do ponto de acesso ao conteúdo. O ponto de acesso ao conteúdo é a parte do caminho que aparece entre o nome de anfitrião do CDN da Verizon e o caminho relativo para o recurso solicitado (por exemplo, /800001/CustomerOrigin). Identifica um local por tipo de servidor (por exemplo, origin CDN ou cliente) e o número de conta de cliente.

   Os seguintes valores estão disponíveis para o **relativamente à** opção:
  - **Raiz**: Indica que o ponto de comparação de URL começa imediatamente após o nome de anfitrião do CDN. 

  Por exemplo: http:\//wpc.0001.&lt; domínio&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indica que o ponto de comparação de URL começa depois do ponto de acesso ao conteúdo (por exemplo, myorigin /000001 ou/800001 /). Uma vez que o \*. azureedge.net CNAME é criado relativos ao diretório de origem no nome do anfitrião de CDN da Verizon por predefinição, os utilizadores da CDN do Azure devem utilizar o **origem** valor. 

  Por exemplo: https:\//&lt;ponto final&gt;.azureedge.net/**myfolder**/index.htm 

  Este URL aponta para o nome de anfitrião de CDN da Verizon seguinte: http:\//wpc.0001.&lt; domínio&gt;/800001/myorigin/**myfolder**/index.htm

- Um URL de CNAME do edge é reescrito para um URL de CDN antes da comparação de URL.

    Por exemplo, ambos os seguintes URLs apontam para o mesmo elemento e, portanto, tem o mesmo caminho de URL.
  - URL de CDN: http:\//wpc.0001.&lt; domínio&gt;/800001/CustomerOrigin/path/asset.htm
    
  - URL de CNAME do Edge: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Informações adicionais:
  - Domínio personalizado: https:\//my.domain.com/path/asset.htm
    
    - Caminho do URL (relativo à raiz do): / / 800001 CustomerOrigin/caminho /
    
    - Caminho do URL (relativo ao origin): /path/

- A parte do URL que é utilizado para as extremidades de comparação de URL apenas antes do nome de ficheiro do recurso solicitado. Uma barra à direita é o último caráter neste tipo de caminho.

- Substituir quaisquer espaços no padrão de caminho de URL com "% 20".

- Cada padrão de caminho de URL pode conter um ou mais asteriscos (*), onde cada asterisco corresponde a uma seqüência de caracteres de um ou mais.

- Especifica vários caminhos de URL no padrão, cada um com um único espaço de delimitação.

    Por exemplo: * /sales/ * /marketing/

- Uma especificação de caminho de URL, pode tirar partido das [valores de caráter universal](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Utilize o **ignorar caso** a opção de controle se é efetuada uma comparação diferenciando maiúsculas de minúsculas.

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>Extensão do caminho de URL

Identifica pedidos pela extensão de arquivo do recurso solicitado.

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais a extensão do caminho de URL corresponde à condição é cumprida.

- **Correspondências**: Requer o URL do pedido para conter uma extensão de ficheiro que corresponde exatamente o padrão especificado.

   Por exemplo, se especificar "htm", "htm" ativos são correspondidos, mas não os ativos de "html".  

- **Não corresponde ao**: Requer o pedido de URL para conter uma extensão de ficheiro que não coincide com o padrão especificado.

Informações da chave:

- Especificar as extensões de ficheiro a correspondência da **valor** caixa. Não inclua um ponto à esquerda; Por exemplo, utilize htm em vez. htm.

- Utilize o **ignorar caso** a opção de controle se é efetuada uma comparação diferenciando maiúsculas de minúsculas.

- Especifica várias extensões de ficheiros por cada extensão com um único espaço de delimitação. 

    Por exemplo: htm html

- Por exemplo, especificar "htm" corresponde a "htm" ativos, mas não os ativos de "html".

#### <a name="sample-scenario"></a>Cenário de exemplo

A seguinte configuração de exemplo parte do princípio de que esta condição de correspondência for cumprida, quando uma das extensões especificadas corresponde a um pedido.

Especificação de valor: asp aspx php html

Esta condição de correspondência é cumprida quando encontra URLs que terminam com as seguintes extensões:

- .asp
- .aspx
- .php
- .html

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>Nome de ficheiro de caminho de URL

Identifica os pedidos com o nome de ficheiro do recurso solicitado. Para efeitos desta condição de correspondência, um nome de ficheiro é composta pelo nome do recurso de pedido, um período e a extensão de ficheiro (por exemplo, index. HTML).

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o nome de ficheiro do caminho de URL corresponde à condição é cumprida.

- **Correspondências**: Requer o pedido para conter um nome de ficheiro no seu caminho de URL que corresponda ao padrão especificado.
- **Não corresponde ao**: Requer o pedido para conter um nome de ficheiro no seu caminho de URL não coincide com o padrão especificado.

Informações da chave:

- Utilize o **ignorar caso** a opção de controle se é efetuada uma comparação diferenciando maiúsculas de minúsculas.

- Para especificar várias extensões de ficheiro, separe cada extensão com um único espaço.

    Por exemplo: Index htm

- Substitua os espaços num valor de nome de ficheiro "% 20".

- Um valor de nome de ficheiro, pode tirar partido das [valores de caráter universal](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Por exemplo, cada padrão de nome de ficheiro pode incluir um ou mais asteriscos (*), onde cada asterisco corresponde a uma seqüência de caracteres de um ou mais.

- Se não forem especificados carateres universais, apenas uma correspondência exata satisfaz esta condição de correspondência.

    Por exemplo, a especificação de "presentation.ppt" corresponde a um recurso com o nome "presentation.ppt", mas não um com o nome "presentation.pptx."

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>Literal de caminho do URL

Compara o caminho do URL de um pedido, incluindo o nome de ficheiro, com o valor especificado.

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o Literal de caminho do URL corresponde à condição é cumprida.

- **Correspondências**: Requer o pedido para conter um caminho de URL que corresponda ao padrão especificado.
- **Não corresponde ao**: Requer o pedido para conter um caminho de URL não coincide com o padrão especificado.

Informações da chave:

- Utilize o **relativamente à** opção para especificar se o ponto de comparação de URL começa antes ou depois do ponto de acesso ao conteúdo. 

    Os seguintes valores estão disponíveis para o **relativamente à** opção:
  - **Raiz**: Indica que o ponto de comparação de URL começa imediatamente após o nome de anfitrião do CDN.

    Por exemplo: http:\//wpc.0001.&lt; domínio&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indica que o ponto de comparação de URL começa depois do ponto de acesso ao conteúdo (por exemplo, myorigin /000001 ou/800001 /). Uma vez que o \*. azureedge.net CNAME é criado relativos ao diretório de origem no nome do anfitrião de CDN da Verizon por predefinição, os utilizadores da CDN do Azure devem utilizar o **origem** valor. 

    Por exemplo: https:\//&lt;ponto final&gt;.azureedge.net/**myfolder/index.htm**

  Este URL aponta para o nome de anfitrião de CDN da Verizon seguinte: http:\//wpc.0001.&lt; domínio&gt;/800001/myorigin/**myfolder/index.htm**

- Um URL de CNAME do edge é reescrito para um URL de CDN antes de uma comparação de URL.

Por exemplo, ambos os seguintes URLs apontam para o mesmo elemento e, portanto, tem o mesmo caminho de URL:

- URL de CDN: http:\//wpc.0001.&lt; domínio&gt;/800001/CustomerOrigin/path/asset.htm
- URL de CNAME do Edge: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Informações adicionais:
    
    - Caminho do URL (relativo à raiz do): /800001/CustomerOrigin/path/asset.htm
   
    - Caminho do URL (relativo ao origin): /path/asset.htm

- Cadeias de caracteres de consulta no URL são ignoradas.
- Utilize o **ignorar caso** a opção de controle se é efetuada uma comparação diferenciando maiúsculas de minúsculas.
- O valor especificado para esta condição de correspondência é comparado com o caminho relativo do pedido exato efetuado pelo cliente.

- Para fazer corresponder a todos os pedidos efetuados para um diretório específico, utilize o [diretório de caminho de URL](#url-path-directory) ou o [caminho de URL com carateres universais](#url-path-wildcard) corresponde à condição.

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>Regex do caminho de URL

Compara o caminho do URL de um pedido especificado [expressão regular](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o Regex do caminho de URL corresponde à condição é cumprida.

- **Correspondências**: Requer o pedido para conter um caminho de URL que corresponda à expressão regular especificada.
- **Não corresponde ao**: Requer o pedido para conter um caminho de URL não corresponde à expressão regular especificada.

Informações da chave:

- Um URL de CNAME do edge é reescrito para um URL de CDN antes de comparação de URL.

    Por exemplo, ambos os URLs de apontam para o mesmo elemento e, portanto, tem o mesmo caminho de URL.

     - URL de CDN: http:\//wpc.0001.&lt; domínio&gt;/800001/CustomerOrigin/path/asset.htm

     - URL de CNAME do Edge: http:\//my.domain.com/path/asset.htm

    Informações adicionais:
    
     - Caminho do URL: /800001/CustomerOrigin/path/asset.htm

- Cadeias de caracteres de consulta no URL são ignoradas.
    
- Utilize o **ignorar caso** a opção de controle se é efetuada uma comparação diferenciando maiúsculas de minúsculas.
    
- Espaços no caminho do URL devem ser substituídos por "% 20".

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>Caminho de URL com carateres universais

Compara caminho URL relativo de um pedido para o padrão de caráter universal especificado.

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o curinga de caminho de URL corresponde à condição é cumprida.

- **Correspondências**: Requer o pedido para conter um caminho de URL que corresponda ao padrão de caráter universal especificado.
- **Não corresponde ao**: Requer o pedido para conter um caminho de URL não coincide com o padrão de caráter universal especificado.

Informações da chave:

- **Relativamente à** opção: Esta opção determina se o ponto de comparação de URL começa antes ou depois do ponto de acesso ao conteúdo.

   Esta opção pode ter os seguintes valores:
     - **Raiz**: Indica que o ponto de comparação de URL começa imediatamente após o nome de anfitrião do CDN.

       Por exemplo: http:\//wpc.0001.&lt; domínio&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indica que o ponto de comparação de URL começa depois do ponto de acesso ao conteúdo (por exemplo, myorigin /000001 ou/800001 /). Uma vez que o \*. azureedge.net CNAME é criado relativos ao diretório de origem no nome do anfitrião de CDN da Verizon por predefinição, os utilizadores da CDN do Azure devem utilizar o **origem** valor. 

       Por exemplo: https:\//&lt;ponto final&gt;.azureedge.net/**myfolder/index.htm**

     Este URL aponta para o nome de anfitrião de CDN da Verizon seguinte: http:\//wpc.0001.&lt; domínio&gt;/800001/myorigin/**myfolder/index.htm**

- Um URL de CNAME do edge é reescrito para um URL de CDN antes de comparação de URL.

    Por exemplo, ambos os seguintes URLs apontam para o mesmo elemento e, portanto, tem o mesmo caminho de URL:
     - URL de CDN: http://wpc.0001.&lt ; domínio&gt; /800001/CustomerOrigin/path/asset.htm
     - URL de CNAME do Edge: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Informações adicionais:
    
     - Caminho do URL (relativo à raiz do): /800001/CustomerOrigin/path/asset.htm
    
     - Caminho do URL (relativo ao origin): /path/asset.htm
    
- Especifica vários caminhos de URL, cada um com um único espaço de delimitação.

   Por exemplo: /marketing/asset.* /sales/*.htm

- Cadeias de caracteres de consulta no URL são ignoradas.
    
- Utilize o **ignorar caso** a opção de controle se é efetuada uma comparação diferenciando maiúsculas de minúsculas.
    
- Substitua espaços no caminho do URL "% 20".
    
- O valor especificado para um caminho de URL, pode tirar partido das [valores de caráter universal](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Cada padrão de caminho de URL pode conter um ou mais asteriscos (*), onde cada asterisco pode corresponder a uma seqüência de caracteres de um ou mais.

#### <a name="sample-scenarios"></a>Cenários de exemplo

As configurações de exemplo na tabela seguinte partem do princípio de que esta condição de correspondência for cumprida, quando o padrão de URL especificado corresponde a um pedido:

Valor                   | Caminho relativo    | Resultado 
------------------------|----------------|-------
*/test.html */test.php  | Origem ou de raiz | Este padrão é correspondido com pedidos de recursos com o nome "Test" ou "test.php" em qualquer pasta.
/ 80ABCD/origem/texto / *   | raiz           | Este padrão é correspondido quando o recurso solicitado cumpre os seguintes critérios: <br />--Tem de residir numa origem do cliente chamada "origem". <br />-O caminho relativo tem de começar com uma pasta chamada "text". Ou seja, o recurso solicitado ou pode residir na pasta de "text" ou uma das respetivas subpastas recursiva.
*/css/* */js/*          | Origem ou de raiz | Este padrão é correspondido por todos os CDN ou de extremidade CNAME URLs que contêm uma pasta de css ou js.
*.jpg *.gif *.png       | Origem ou de raiz | Este padrão é correspondido por todos os URLs CNAME da CDN ou de extremidade que termina com. jpg, GIF ou. png. Uma forma alternativa de especificar este padrão é com o [extensão de caminho de URL corresponde à condição](#url-path-extension).
/ imagens / * / suporte de dados / *      | Origem         | Este padrão é correspondido por CDN ou de extremidade URLs de CNAME cujo caminho relativo que começa com uma pasta "imagens" ou "suporte de dados". <br />-URL de CDN: http:\//wpc.0001.&lt; domínio&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>Literal de consulta de URL

Compara a cadeia de caracteres de consulta de um pedido para o valor especificado.

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o Literal de consulta de URL corresponde à condição é cumprida.

- **Correspondências**: Requer o pedido para conter uma cadeia de caracteres de consulta de URL que corresponde à cadeia de consulta especificada.
- **Não corresponde ao**: Requer o pedido para conter uma cadeia de consulta de URL não coincide com a cadeia de caracteres de consulta especificada.

Informações da chave:

- Correspondências de cadeia de consulta exata apenas satisfazem essa condição de correspondência.
    
- Utilize o **ignorar caso** opção para controlar a sensibilidade de comparações de seqüência de caracteres de consulta.
    
- Não inclua um líder do ponto de interrogação (?) no texto do valor de cadeia de caracteres da consulta.
    
- Determinados caracteres exigem codificação do URL. Utilize o símbolo de percentagem para URL codificar os seguintes carateres:

   Caráter | Codificação do URL
   ----------|---------
   Espaço     | %20
   &         | %25

- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
   - Concluir o preenchimento de Cache
   - Predefinição interna Max-Age
   - Forçar a duração máxima interna
   - Ignorar a origem não-Cache
   - Max-obsoleta interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>Parâmetro de consulta de URL

Identifica a solicitação que contenha o parâmetro de cadeia de caracteres de consulta especificada. Este parâmetro estiver definido como um valor que corresponde a um padrão especificado. Parâmetros de cadeia de caracteres de consulta (por exemplo, o parâmetro = value) no pedido de URL determinar se esta condição é cumprida. Esta condição de correspondência identifica um parâmetro de cadeia de caracteres de consulta pelo respetivo nome e aceita um ou mais valores para o valor do parâmetro. 

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o parâmetro de consulta de URL corresponde à condição é cumprida.

- **Correspondências**: Requer um pedido para conter o parâmetro especificado com um valor que corresponde a, pelo menos, um dos valores definidos nesta condição de correspondência.
- **Não corresponde ao**: Requer que o pedido de atende a um dos seguintes critérios:
  - Não contém o parâmetro especificado.
  - Contém o parâmetro especificado, mas o respetivo valor não corresponde a nenhum dos valores definidos nesta condição de correspondência.

Esta condição de correspondência fornece uma forma fácil para especificar as combinações de nome/valor do parâmetro. Para obter mais flexibilidade se correspondem a um parâmetro de cadeia de caracteres de consulta, considere a utilização a [universal de consulta de URL](#url-query-wildcard) corresponde à condição.

Informações da chave:

- Apenas um único URL consulta nome de parâmetro pode ser especificado por instância desta condição de correspondência.
    
- Uma vez que os valores de caráter universal não são suportados quando é especificado um nome de parâmetro, apenas correspondências de nome de parâmetro exata são elegíveis para comparação.
- O valor ou valores de parâmetro podem incluir [valores de caráter universal](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Cada padrão de valor de parâmetro pode incluir um ou mais asteriscos (*), onde cada asterisco pode corresponder a uma seqüência de caracteres de um ou mais.
   - Determinados caracteres exigem codificação do URL. Utilize o símbolo de percentagem para URL codificar os seguintes carateres:

       Caráter | Codificação do URL
       ----------|---------
       Espaço     | %20
       &         | %25

- Especifica vários valores de parâmetro de cadeia de caracteres de consulta, cada um com um único espaço de delimitação. Esta condição de correspondência é preenchida quando um pedido contém um das combinações de nome/valor especificado.

   - Exemplo 1:

     - Configuração:

       ValueA ValueB

     - Os seguintes parâmetros de cadeia de caracteres de consulta corresponde a esta configuração:

       Parameter1 = Valora
    
       Parameter1 = Valorb

   - Exemplo 2:

     - Configuração: 

        Valor % 20A valor % 20B

     - Os seguintes parâmetros de cadeia de caracteres de consulta corresponde a esta configuração:

       Parameter1 = % do valor 20A

       Parameter1=Value%20B

- Esta condição de correspondência é cumprida apenas quando existe uma correspondência exata com, pelo menos, um das combinações de nome/valor de cadeia de caracteres de consulta especificada.

   Por exemplo, se utilizar a configuração no exemplo anterior, o parâmetro de nome/valor combinação "Parameter1 = ValueAdd" não seria considerado uma correspondência. No entanto, se especificar qualquer um dos seguintes valores, corresponderá essa combinação de nome/valor:

   - Valora Valorb ValueAdd
   - Valorb Valora *

- Utilize o **ignorar caso** opção para controlar a sensibilidade de comparações de seqüência de caracteres de consulta.
    
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
   - Concluir o preenchimento de Cache
   - Predefinição interna Max-Age
   - Forçar a duração máxima interna
   - Ignorar a origem não-Cache
   - Max-obsoleta interno

#### <a name="sample-scenarios"></a>Cenários de exemplo

O exemplo seguinte demonstra como esta opção funciona em situações específicas:

Nome  | Valor |  Resultado
------|-------|--------
Utilizador  | João   | Este padrão é correspondido quando a cadeia de consulta para um URL de pedido é "? utilizador = joe."
Utilizador  | *     | Este padrão é correspondido quando a cadeia de consulta para um URL de pedido contém um parâmetro de utilizador.
Email | João\* | Este padrão é correspondido quando a cadeia de consulta para um URL de pedido contém um parâmetro de E-Mail que começa com "João".

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>Regex de consulta de URL

Identifica a solicitação que contenha o parâmetro de cadeia de caracteres de consulta especificada. Este parâmetro estiver definido como um valor que corresponde a uma determinada [expressão regular](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o Regex de consulta de URL corresponde à condição é cumprida.

- **Correspondências**: Requer o pedido para conter uma cadeia de caracteres de consulta de URL que corresponda à expressão regular especificada.
- **Não corresponde ao**: Requer o pedido para conter uma cadeia de caracteres de consulta de URL que não corresponde à expressão regular especificada.

Informações da chave:

- Apenas a correspondências exatas para a expressão regular especificada satisfazem essa condição de correspondência.
    
- Utilize o **ignorar caso** opção para controlar a sensibilidade de comparações de seqüência de caracteres de consulta.
    
- Para efeitos desta opção, uma cadeia de consulta começa com o primeiro caráter após o delimitador de ponto de interrogação (?) para a cadeia de consulta.
    
- Determinados caracteres exigem codificação do URL. Utilize o símbolo de percentagem para URL codificar os seguintes carateres:

   Caráter | Codificação do URL | Value
   ----------|--------------|------
   Espaço     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`20
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`25
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   Tenha em atenção que os símbolos de percentagem devem ser escritos.

- Carateres de escape duplo especial de expressão regular (por exemplo, \^$. +) para incluir uma barra invertida na expressão regular.

   Por exemplo:

   Value | Interpretado como 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
   - Concluir o preenchimento de Cache
   - Predefinição interna Max-Age
   - Forçar a duração máxima interna
   - Ignorar a origem não-Cache
   - Max-obsoleta interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>Caráter universal de consulta de URL

Compara o valor ou valores especificado em relação a cadeia de consulta da solicitação.

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o caráter universal de consulta de URL corresponde à condição é cumprida.

- **Correspondências**: Requer o pedido para conter uma cadeia de caracteres de consulta de URL que corresponde ao valor de caráter universal especificado.
- **Não corresponde ao**: Requer o pedido para conter uma cadeia de consulta de URL não coincide com o valor de caráter universal especificado.

Informações da chave:

- Para efeitos desta opção, uma cadeia de consulta começa com o primeiro caráter após o delimitador de ponto de interrogação (?) para a cadeia de consulta.
- Podem incluir valores de parâmetros [valores de caráter universal](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Cada padrão de valor de parâmetro pode incluir um ou mais asteriscos (*), onde cada asterisco pode corresponder a uma seqüência de caracteres de um ou mais.
   - Determinados caracteres exigem codificação do URL. Utilize o símbolo de percentagem para URL codificar os seguintes carateres:

     Caráter | Codificação do URL
     ----------|---------
     Espaço     | %20
     &         | %25

- Especifica vários valores, cada um com um único espaço de delimitação.

   Por exemplo: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Apenas a correspondências exatas para, pelo menos, um dos padrões de cadeia de caracteres de consulta especificada satisfazem essa condição de correspondência.
    
- Utilize o **ignorar caso** opção para controlar a sensibilidade de comparações de seqüência de caracteres de consulta.
    
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
   - Concluir o preenchimento de Cache
   - Predefinição interna Max-Age
   - Forçar a duração máxima interna
   - Ignorar a origem não-Cache
   - Max-obsoleta interno

#### <a name="sample-scenarios"></a>Cenários de exemplo

O exemplo seguinte demonstra como esta opção funciona em situações específicas:

 Nome                 | Descrição
 ---------------------|------------
user=joe              | Este padrão é correspondido quando a cadeia de consulta para um URL de pedido é "? utilizador = joe."
\*user=\* \*optout=\* | Este padrão é correspondido quando a consulta de URL da CDN que contém o utilizador ou o parâmetro de revogação.

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral de rede de entrega de conteúdos do Azure](cdn-overview.md)
- [Referência do motor de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Expressões condicionais do motor de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funcionalidades do motor de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Substituir o comportamento HTTP padrão usando o mecanismo de regras](cdn-verizon-premium-rules-engine.md)
