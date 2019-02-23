---
title: Condições de correspondência do motor de regras de CDN do Azure | Documentos da Microsoft
description: Condições de correspondência do motor de regras de documentação de referência para a rede de entrega de conteúdos do Azure.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: 3ebc4d94e0bbf24895be0aeba9fef1cc7aacd6b9
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736717"
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Condições de correspondência do motor de regras CDN do Azure 
Este artigo apresenta uma lista de descrições detalhadas das condições de correspondência disponíveis para a rede do Azure da entrega de conteúdos (CDN) [motor de regras](cdn-rules-engine.md).

A segunda parte de uma regra é a condição de correspondência. Uma condição de correspondência identifica tipos específicos de pedidos para o qual um conjunto de recursos será executado.

Por exemplo, pode utilizar uma condição de correspondência para:
- Filtrar pedidos para o conteúdo numa localização específica.
- Filtrar pedidos gerados a partir de um determinado endereço IP ou país.
- Filtrar pedidos por informações de cabeçalho.

## <a name="always-match-condition"></a>Condição de correspondência sempre

A condição de correspondência sempre aplica-se um conjunto predefinido de funcionalidades para todos os pedidos.

Name | Objetivo
-----|--------
[Sempre](#always) | Aplica-se de um conjunto predefinido de funcionalidades para todos os pedidos.

## <a name="device-match-condition"></a>Condição de correspondência de dispositivo

A condição de correspondência de dispositivo identifica os pedidos efetuados a partir de um dispositivo móvel com base nas respetivas propriedades.  

Name | Objetivo
-----|--------
[dispositivo](#device) | Identifica os pedidos efetuados a partir de um dispositivo móvel com base nas respetivas propriedades.

## <a name="location-match-conditions"></a>Condições de correspondência de localização

As condições de correspondência de localização identificam solicitações com base na localização do autor do pedido.

Name | Objetivo
-----|--------
[COMO número](#as-number) | Identifica os pedidos provenientes de uma rede específica.
[País](#country) | Identifica os pedidos que têm origem nos países/regiões especificados.

## <a name="origin-match-conditions"></a>Condições de correspondência de origem

As condições de correspondência de origem identificam pedidos que apontam para o armazenamento de rede de entrega de conteúdos ou um servidor de origem do cliente.

Name | Objetivo
-----|--------
[Origem de CDN](#cdn-origin) | Identifica os pedidos para conteúdo armazenado no armazenamento de rede de entrega de conteúdos.
[Origem do cliente](#customer-origin) | Identifica os pedidos para o conteúdo armazenado num servidor de origem do cliente específico.

## <a name="request-match-conditions"></a>Condições de correspondência de pedido

As condições de correspondência de pedido de identificam pedidos com base nas respetivas propriedades.

Name | Objetivo
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

Name | Objetivo
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

[Voltar ao início](#main)

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

[Voltar ao início](#main)

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

[Voltar ao início](#main)

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

[Voltar ao início](#main)

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
  - Um valor de cookie pode tirar partido das [valores de caráter universal](cdn-rules-engine-reference.md#wildcard-values). 
  - Se não tiver sido especificado um valor de caráter universal, apenas uma correspondência exata satisfaz esta condição de correspondência. Por exemplo, a especificação de "Value" corresponderá "Valor", mas não "Value1" ou "Value2.."
  - Utilize o **ignorar caso** a opção de controle se uma comparação diferenciando maiúsculas de minúsculas é feita em relação a valor de cookie do pedido.
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
  - Concluir o preenchimento de Cache
  - Predefinição interna Max-Age
  - Forçar a duração máxima interna
  - Ignorar a origem não-Cache
  - Max-obsoleta interno

[Voltar ao início](#main)

</br>

---
### <a name="cookie-parameter-regex"></a>Cookie parâmetro Regex
A condição de correspondência de Regex do parâmetro de Cookie define um nome do cookie e um valor. Pode usar [expressões regulares](cdn-rules-engine-reference.md#regular-expressions) para definir o valor do cookie pretendido. 

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

[Voltar ao início](#main)

</br>

---
### <a name="country"></a>País
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

- Negar a funcionalidade de acesso (403): Ativar a [funcionalidade de negar acesso (403)](cdn-rules-engine-reference-features.md#deny-access-403) para replicar a parte de permissões ou de bloqueios da funcionalidade de filtragem de país.

[Voltar ao início](#main)

</br>

---
### <a name="customer-origin"></a>Origem do cliente

Informações da chave: 
- A condição de correspondência de origem do cliente é cumprida, independentemente se o conteúdo é solicitado por meio de um URL de CDN ou de uma extremidade URL de CNAME que aponta para a origem do cliente selecionado.
- Não é possível eliminar uma configuração de origem do cliente que é referenciada por uma regra a partir da página de origem do cliente. Antes de tentar eliminar uma configuração de origem do cliente, certifique-se de que as seguintes configurações não fazer referência a ele:
  - Uma condição de correspondência de origem do cliente
  - Uma configuração de CNAME do edge
- Não utilize uma instrução IF e combinar determinadas condições de correspondência. Por exemplo, a combinação de uma condição de correspondência de origem do cliente com uma condição de correspondência de CDN Origin criaria um padrão de correspondência que nunca foi possível corresponder. Por esse motivo, as duas condições de correspondência de origem do cliente não podem ser combinadas por meio de uma instrução IF e.

[Voltar ao início](#main)

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
Literal  | Selecione esta opção para impedir que a maioria dos caracteres significado especial com o respetivo [valor literal](cdn-rules-engine-reference.md#literal-values).
Caráter universal | Selecione esta opção para tirar partido de todas as [carateres universais] ([valores de caráter universal](cdn-rules-engine-reference.md#wildcard-values).
RegEx    | Selecione esta opção para utilizar [expressões regulares](cdn-rules-engine-reference.md#regular-expressions). Expressões regulares são úteis para a definição de um padrão de caracteres.

#### <a name="wurfl-capabilities"></a>Capacidades do WURFL
Um recurso do WURFL refere-se a uma categoria que descreve a dispositivos móveis. O recurso selecionado determina o tipo de descrição de dispositivo móvel que é utilizado para identificar pedidos.

A tabela seguinte lista as capacidades WURFL e as respetivas variáveis para o motor de regras.
<br>
> [!NOTE] 
> As seguintes variáveis são suportadas no **modificar cabeçalho do pedido de cliente** e **modificar cabeçalho de resposta do cliente** funcionalidades.

Capacidade | Variável | Descrição | Valores de exemplo
-----------|----------|-------------|----------------
Nome da marca | %{wurfl_cap_brand_name} | Uma cadeia que indica o nome da marca do dispositivo. | Samsung
SO do dispositivo | %{wurfl_cap_device_os} | Uma cadeia que indica o sistema operativo instalado no dispositivo. | IOS
Versão do SO do Dispositivo | %{wurfl_cap_device_os_version} | Uma cadeia que indica o número de versão do sistema operativo instalado no dispositivo. | 1.0.1
Orientação dupla | %{wurfl_cap_dual_orientation} | Um valor booleano que indica se o dispositivo oferece suporte à orientação dupla. | true
HTML preferencial DTD | %{wurfl_cap_html_preferred_dtd} | Uma cadeia que indica documento preferencial definição o dispositivo móvel (DTD) para o conteúdo HTML. | nenhum<br/>xhtml_basic<br/>html5
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
Nome do Modelo | %{wurfl_cap_model_name} | Uma cadeia que indica o nome do modelo do dispositivo. | s3
Transferência progressiva | %{wurfl_cap_progressive_download} | Um valor booleano que indica se o dispositivo a suportar a reprodução de áudio e vídeo enquanto ele ainda está a ser transferido. | true
Data de lançamento | %{wurfl_cap_release_date} | Uma cadeia que indica o ano e mês em que o dispositivo foi adicionado para a base de dados do WURFL.<br/><br/>Formato: `yyyy_mm` | 2013_december
Altura da resolução | %{wurfl_cap_resolution_height} | Um número inteiro que indica a altura do dispositivo em pixéis. | 768
Largura de resolução | %{wurfl_cap_resolution_width} | Um número inteiro que indica a largura do dispositivo em pixéis. | 1024

[Voltar ao início](#main)

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

[Voltar ao início](#main)

</br>

---
### <a name="referring-domain"></a>Domínio de referência
O nome de anfitrião associados com o Referenciador por meio do qual o conteúdo foi solicitado determina se a condição de domínio que faz referência for cumprida. 

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o domínio que faça referência corresponde à condição é cumprida:
- **Correspondências**: Requer o nome de anfitrião de referência de acordo com os valores especificados. 
- **Não corresponde ao**: Requer que o nome de anfitrião de referência não coincide com o valor especificado.

Informações da chave:
- Especifica vários nomes de anfitrião, cada um com um único espaço de delimitação.
- Esta condição de correspondência suporta [valores de caráter universal](cdn-rules-engine-reference.md#wildcard-values).
- Se o valor especificado não contém um asterisco, tem de ser uma correspondência exata para o nome de anfitrião do referenciador. Por exemplo, especificar "mydomain.com" não corresponde a "www.mydomain.com."
- Utilize o **ignorar caso** a opção de controle se for feita uma comparação diferenciando maiúsculas de minúsculas.
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
  - Concluir o preenchimento de Cache
  - Predefinição interna Max-Age
  - Forçar a duração máxima interna
  - Ignorar a origem não-Cache
  - Max-obsoleta interno

[Voltar ao início](#main)

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

[Voltar ao início](#main)

</br>

---
### <a name="request-header-regex"></a>Regex de cabeçalho do pedido
O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o Regex de cabeçalho do pedido corresponde à condição é cumprida.
- **Correspondências**: Requer o pedido para conter o cabeçalho especificado. O valor tem de corresponder ao padrão que é definido no especificado [expressão regular](cdn-rules-engine-reference.md#regular-expressions).
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

[Voltar ao início](#main)

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
  - Um valor de cabeçalho pode tirar partido das [valores de caráter universal](cdn-rules-engine-reference.md#wildcard-values).
  - Utilize o **ignorar caso** opção para controlar a sensibilidade de comparações de valor de cabeçalho.
  - Esta condição de correspondência é preenchida quando um valor de cabeçalho corresponde exatamente para, pelo menos, um dos padrões especificados.
  - Especifica vários valores, cada um com um único espaço de delimitação.
- Devido a da maneira na qual cache definições são controladas, esta condição de correspondência é incompatível com as seguintes funcionalidades:
  - Concluir o preenchimento de Cache
  - Predefinição interna Max-Age
  - Forçar a duração máxima interna
  - Ignorar a origem não-Cache
  - Max-obsoleta interno

[Voltar ao início](#main)

</br>

---
### <a name="request-method"></a>Método de pedido
A condição de correspondência do método de pedido é cumprida apenas quando os recursos são solicitados por meio do método de pedido seleccionado. Os métodos de pedido disponíveis são:
- GET
- CABEÇALHO 
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

[Voltar ao início](#main)

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

[Voltar ao início](#main)

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

- Uma especificação de caminho de URL, pode tirar partido das [valores de caráter universal](cdn-rules-engine-reference.md#wildcard-values).

- Utilize o **ignorar caso** a opção de controle se é efetuada uma comparação diferenciando maiúsculas de minúsculas.

[Voltar ao início](#main)

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

[Voltar ao início](#main)

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
    
- Um valor de nome de ficheiro, pode tirar partido das [valores de caráter universal](cdn-rules-engine-reference.md#wildcard-values). Por exemplo, cada padrão de nome de ficheiro pode incluir um ou mais asteriscos (*), onde cada asterisco corresponde a uma seqüência de caracteres de um ou mais.
    
- Se não forem especificados carateres universais, apenas uma correspondência exata satisfaz esta condição de correspondência.

    Por exemplo, a especificação de "presentation.ppt" corresponde a um recurso com o nome "presentation.ppt", mas não um com o nome "presentation.pptx."

[Voltar ao início](#main)

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

[Voltar ao início](#main)

</br>

---
### <a name="url-path-regex"></a>Regex do caminho de URL
Compara o caminho do URL de um pedido especificado [expressão regular](cdn-rules-engine-reference.md#regular-expressions).

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

[Voltar ao início](#main)

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
     - URL de CDN: http://wpc.0001.&lt; domínio&gt;/800001/CustomerOrigin/path/asset.htm
     - URL de CNAME do Edge: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Informações adicionais:
    
     - Caminho do URL (relativo à raiz do): /800001/CustomerOrigin/path/asset.htm
    
     - Caminho do URL (relativo ao origin): /path/asset.htm
    
- Especifica vários caminhos de URL, cada um com um único espaço de delimitação.

   Por exemplo: /marketing/asset.* /sales/*.htm

- Cadeias de caracteres de consulta no URL são ignoradas.
    
- Utilize o **ignorar caso** a opção de controle se é efetuada uma comparação diferenciando maiúsculas de minúsculas.
    
- Substitua espaços no caminho do URL "% 20".
    
- O valor especificado para um caminho de URL, pode tirar partido das [valores de caráter universal](cdn-rules-engine-reference.md#wildcard-values). Cada padrão de caminho de URL pode conter um ou mais asteriscos (*), onde cada asterisco pode corresponder a uma seqüência de caracteres de um ou mais.

#### <a name="sample-scenarios"></a>Cenários de exemplo

As configurações de exemplo na tabela seguinte partem do princípio de que esta condição de correspondência for cumprida, quando o padrão de URL especificado corresponde a um pedido:

Value                   | Caminho relativo    | Resultado 
------------------------|----------------|-------
*/test.html */test.php  | Origem ou de raiz | Este padrão é correspondido com pedidos de recursos com o nome "Test" ou "test.php" em qualquer pasta.
/ 80ABCD/origem/texto / *   | Raiz           | Este padrão é correspondido quando o recurso solicitado cumpre os seguintes critérios: <br />--Tem de residir numa origem do cliente chamada "origem". <br />-O caminho relativo tem de começar com uma pasta chamada "text". Ou seja, o recurso solicitado ou pode residir na pasta de "text" ou uma das respetivas subpastas recursiva.
*/css/* */js/*          | Origem ou de raiz | Este padrão é correspondido por todos os CDN ou de extremidade CNAME URLs que contêm uma pasta de css ou js.
*.jpg *.gif *.png       | Origem ou de raiz | Este padrão é correspondido por todos os URLs CNAME da CDN ou de extremidade que termina com. jpg, GIF ou. png. Uma forma alternativa de especificar este padrão é com o [extensão de caminho de URL corresponde à condição](#url-path-extension).
/ imagens / * / suporte de dados / *      | Origem         | Este padrão é correspondido por CDN ou de extremidade URLs de CNAME cujo caminho relativo que começa com uma pasta "imagens" ou "suporte de dados". <br />-URL de CDN: http:\//wpc.0001.&lt; domínio&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Voltar ao início](#main)

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

[Voltar ao início](#main)

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
- O valor ou valores de parâmetro podem incluir [valores de caráter universal](cdn-rules-engine-reference.md#wildcard-values).
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

Name  | Value |  Resultado
------|-------|--------
Utilizador  | João   | Este padrão é correspondido quando a cadeia de consulta para um URL de pedido é "? utilizador = joe."
Utilizador  | *     | Este padrão é correspondido quando a cadeia de consulta para um URL de pedido contém um parâmetro de utilizador.
Email | João\* | Este padrão é correspondido quando a cadeia de consulta para um URL de pedido contém um parâmetro de E-Mail que começa com "João".

[Voltar ao início](#main)

</br>

---
### <a name="url-query-regex"></a>Regex de consulta de URL
Identifica a solicitação que contenha o parâmetro de cadeia de caracteres de consulta especificada. Este parâmetro estiver definido como um valor que corresponde a uma determinada [expressão regular](cdn-rules-engine-reference.md#regular-expressions).

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


[Voltar ao início](#main)

</br>

---
### <a name="url-query-wildcard"></a>Caráter universal de consulta de URL
Compara o valor ou valores especificado em relação a cadeia de consulta da solicitação.

O **correspondências**/**não corresponde ao** opção determina as condições sob as quais o caráter universal de consulta de URL corresponde à condição é cumprida.
- **Correspondências**: Requer o pedido para conter uma cadeia de caracteres de consulta de URL que corresponde ao valor de caráter universal especificado.
- **Não corresponde ao**: Requer o pedido para conter uma cadeia de consulta de URL não coincide com o valor de caráter universal especificado.

Informações da chave:
- Para efeitos desta opção, uma cadeia de consulta começa com o primeiro caráter após o delimitador de ponto de interrogação (?) para a cadeia de consulta.
- Podem incluir valores de parâmetros [valores de caráter universal](cdn-rules-engine-reference.md#wildcard-values):
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

 Name                 | Descrição
 ---------------------|------------
user=joe              | Este padrão é correspondido quando a cadeia de consulta para um URL de pedido é "? utilizador = joe."
\*user=\* \*optout=\* | Este padrão é correspondido quando a consulta de URL da CDN que contém o utilizador ou o parâmetro de revogação.

[Voltar ao início](#main)

</br>

## <a name="next-steps"></a>Passos Seguintes
* [Descrição geral de rede de entrega de conteúdos do Azure](cdn-overview.md)
* [Referência do motor de regras](cdn-rules-engine-reference.md)
* [Expressões condicionais do motor de regras](cdn-rules-engine-reference-conditional-expressions.md)
* [Funcionalidades do motor de regras](cdn-rules-engine-reference-features.md)
* [Substituir o comportamento HTTP padrão usando o mecanismo de regras](cdn-rules-engine.md)

