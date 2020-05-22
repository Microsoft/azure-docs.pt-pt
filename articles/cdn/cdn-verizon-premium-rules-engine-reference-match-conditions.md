---
title: Azure CDN da Verizon Premium governa condições de correspondência do motor / Microsoft Docs
description: Documentação de referência para a Rede de Entrega de Conteúdos Azure da Verizon Premium regras condições de correspondência do motor.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 3bc439e3244be63bff1c54d3230eda17dfb9d88d
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745599"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN da Verizon Premium governa condições de correspondência do motor

Este artigo enumera descrições detalhadas das condições de jogo disponíveis para a Rede de Entrega de Conteúdos Azure (CDN) do motor de [regras](cdn-verizon-premium-rules-engine.md)Verizon Premium .

A segunda parte de uma regra é a condição de jogo. Uma condição de correspondência identifica tipos específicos de pedidos para os quais um conjunto de funcionalidades será realizado.

Por exemplo, pode utilizar uma condição de correspondência para:

- Filtrar pedidos de conteúdo num determinado local.
- Filtrar pedidos gerados a partir de um determinado endereço IP ou país/região.
- Filtrar pedidos por informação do cabeçalho.

## <a name="always-match-condition"></a>Sempre corresponder condição

A condição de jogo Always aplica um conjunto de funcionalidades padrão a todos os pedidos.

Name | Objetivo
-----|--------
[Sempre](#always) | Aplica um conjunto de funcionalidades padrão a todos os pedidos.

## <a name="device-match-condition"></a>Condição de correspondência do dispositivo

A condição de correspondência do Dispositivo identifica pedidos feitos a partir de um dispositivo móvel com base nas suas propriedades.  

Name | Objetivo
-----|--------
[Dispositivo](#device) | Identifica pedidos feitos a partir de um dispositivo móvel com base nas suas propriedades.

## <a name="location-match-conditions"></a>Condições de correspondência de localização

As condições de correspondência de localização identificam pedidos com base na localização do solicitador.

Name | Objetivo
-----|--------
[Número as](#as-number) | Identifica pedidos originários de uma determinada rede.
[País](#country) | Identifica pedidos originários dos países/regiões especificados.

## <a name="origin-match-conditions"></a>Condições de correspondência de origem

As condições de correspondência origin identificam pedidos que apontam para o armazenamento da Rede de Entrega de Conteúdos ou um servidor de origem do cliente.

Name | Objetivo
-----|--------
[Origem CDN](#cdn-origin) | Identifica pedidos de conteúdo armazenados no armazenamento da Rede de Entrega de Conteúdos.
[Origem do Cliente](#customer-origin) | Identifica pedidos de conteúdo armazenados num servidor específico de origem do cliente.

## <a name="request-match-conditions"></a>Solicitar condições de jogo

As condições de correspondência do Pedido identificam pedidos com base nas suas propriedades.

Name | Objetivo
-----|--------
[Endereço IP do Cliente](#client-ip-address) | Identifica pedidos originários de um determinado endereço IP.
[Parâmetro de cookies](#cookie-parameter) | Verifica os cookies associados a cada pedido pelo valor especificado.
[Parameter de biscoitos Regex](#cookie-parameter-regex) | Verifica os cookies associados a cada pedido para a expressão regular especificada.
[Nome de borda](#edge-cname) | Identifica pedidos que apontam para um CNAME de borda específica.
[Domínio de referimento](#referring-domain) | Identifica os pedidos que foram remetidos a partir dos nomes de acolhimento especificados.
[Pedido cabeçalho literal](#request-header-literal) | Identifica pedidos que contenham o cabeçalho especificado definido para um valor especificado.
[Pedido de cabeçalho Regex](#request-header-regex) | Identifica pedidos que contenham o cabeçalho especificado definido para um valor que corresponda à expressão regular especificada.
[Pedir Header Wildcard](#request-header-wildcard) | Identifica pedidos que contenham o cabeçalho especificado definido para um valor que corresponda ao padrão especificado.
[Método de Pedido](#request-method) | Identifica pedidos pelo seu método HTTP.
[Regime de Pedidos](#request-scheme) | Identifica pedidos pelo seu protocolo HTTP.

## <a name="url-match-conditions"></a>Condições de correspondência url

As condições de correspondência do URL identificam pedidos com base nos seus URLs.

Name | Objetivo
-----|--------
[Diretório de caminhos URL](#url-path-directory) | Identifica pedidos pelo seu caminho relativo.
[Extensão do caminho do URL](#url-path-extension) | Identifica pedidos pela extensão do nome do ficheiro.
[Nome de ficheiro de caminho URL](#url-path-filename) | Identifica pedidos pelo nome do ficheiro.
[URL Caminho Literal](#url-path-literal) | Compara o caminho relativo de um pedido com o valor especificado.
[URL Path Regex](#url-path-regex) | Compara o caminho relativo de um pedido com a expressão regular especificada.
[URL Path Wildcard](#url-path-wildcard) | Compara o caminho relativo de um pedido com o padrão especificado.
[Url Consulta Literal](#url-query-literal) | Compara a corda de consulta de um pedido com o valor especificado.
[Parâmetro de consulta de URL](#url-query-parameter) | Identifica pedidos que contenham o parâmetro de corda de consulta especificado definido para um valor que corresponda a um padrão especificado.
[URL Consulta Regex](#url-query-regex) | Identifica pedidos que contenham o parâmetro de corda de consulta especificado definido para um valor que corresponda a uma expressão regular especificada.
[URL Consulta Wildcard](#url-query-wildcard) | Compara o valor especificado com a corda de consulta do pedido.

## <a name="reference-for-rules-engine-match-conditions"></a>Referência para regras condições de correspondência do motor

---

### <a name="always"></a>Sempre

A condição de jogo Always aplica um conjunto de funcionalidades padrão a todos os pedidos.

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>Número as

A rede AS Number é definida pelo seu número de sistema autónomo (ASN). 

A opção **Match** / **Does Match** determina as condições em que a condição de jogo do número AS é satisfeita:

- **Fósforos**: Requer que a ASN da rede de clientes corresponda a uma das ASNs especificadas. 
- **Não Corresponde**: Requer que a ASN da rede de clientes não corresponda a nenhuma das ASNs especificadas.

Informação-chave:

- Especifique várias ASNs delimitando cada uma com um único espaço. Por exemplo, 64514 64515 pedidos que chegam de 64514 ou 64515.
- Certos pedidos podem não devolver uma ASN válida. Um ponto de interrogação (?) corresponderá aos pedidos para os quais não foi possível determinar uma ASN válida.
- Especifique toda a ASN para a rede desejada. Valores parciais não serão igualados.
- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
  - Enchimento completo de cache
  - Idade máxima interna padrão
  - Força Interna Max-Age
  - Ignore a Origem Sem Cache
  - Max-Stale interno

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>Origem CDN

A condição de correspondência da Origem CDN é satisfeita quando ambas as condições são satisfeitas:

- Foi solicitado conteúdo do armazenamento da CDN.
- O pedido URI utiliza o tipo de ponto de acesso ao conteúdo (por exemplo, /000001) que é definido nesta condição de jogo:
  - URL CDN: O pedido URI deve conter o ponto de acesso ao conteúdo selecionado.
  - URL Edge CNAME: A configuração CNAME de borda correspondente deve apontar para o ponto de acesso ao conteúdo selecionado.
  
Informação-chave:

- O ponto de acesso ao conteúdo identifica o serviço que deve servir o conteúdo solicitado.
- Não utilize uma declaração e if para combinar certas condições de correspondência. Por exemplo, combinar uma condição de correspondência de Origem CDN com uma condição de correspondência de Origem do Cliente criaria um padrão de correspondência que nunca poderia ser igualado. Por esta razão, duas condições de correspondência de Origem CDN não podem ser combinadas através de uma declaração e IF.

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Endereço IP do Cliente

A opção **Match** / **Does Not Match** determina as condições em que a condição de correspondência do Endereço IP do Cliente é satisfeita:

- **Fósforos**: Requer que o endereço IP do cliente corresponda a um dos endereços IP especificados. 
- **Não Corresponde**: Requer que o endereço IP do cliente não corresponda a nenhum dos endereços IP especificados. 

Informação-chave:

- Utilize notação CIDR.
- Especifique vários endereços IP e/ou blocos de endereços IP, delimitando cada um com um único espaço. Por exemplo:
  - **Exemplo iPv4**: 1.2.3.4 10.20.30.40 corresponde a quaisquer pedidos que cheguem de ambos os endereços 1.2.3.4 ou 10.20.30.40.
  - **Exemplo do IPv6:** 1:2:3:4:5:6:7:8 10:20:40:50:60:70:80 corresponde a quaisquer pedidos que cheguem de ambos os endereços 1:2:3:4:5:7:8 ou 10:20:40:50:60:70:70:80.
- A sintaxe para um bloco de endereçoIP é o endereço IP base seguido de uma barra para a frente e o tamanho do prefixo. Por exemplo:
  - **Exemplo iPv4**: 5.5.5.64/26 corresponde a quaisquer pedidos que cheguem dos endereços 5.5.5.64 a 5.5.5.127.
  - **Exemplo iPv6**: 1:2:3:/48 corresponde a qualquer pedido que chegue dos endereços 1:2:3:0:0:0:0:0:0:0 a 1:2:3:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff:ff
- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
  - Enchimento completo de cache
  - Idade máxima interna padrão
  - Força Interna Max-Age
  - Ignore a Origem Sem Cache
  - Max-Stale interno

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Parâmetro de cookies

A opção **Match** / **Does Not Match** determina as condições em que a condição de correspondência do Parâmetro de Cookie é satisfeita.

- **Fósforos**: Requer um pedido para conter o cookie especificado com um valor que corresponda a pelo menos um dos valores definidos nesta condição de jogo.
- **Não Corresponder**: Requer que o pedido satisfaça qualquer um dos seguintes critérios:
  - Não contém o cookie especificado.
  - Contém o cookie especificado, mas o seu valor não corresponde a nenhum dos valores definidos nesta condição de jogo.
  
Informação-chave:

- Nome do cookie:
  - Uma vez que os valores wildcard, incluindo asteriscos (*), não são suportados quando está a especificar um nome de cookie, apenas os jogos de nome exatos do cookie são elegíveis para comparação.
  - Apenas um único nome de cookie pode ser especificado por exemplo desta condição de correspondência.
  - As comparações de nomes de cookies são insensíveis aos casos.
- Valor do cookie:
  - Especifique vários valores de cookies delimitando cada um com um único espaço.
  - Um valor de cookie pode tirar partido dos [valores wildcard.](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)
  - Se não tiver sido especificado um valor wildcard, apenas uma correspondência exata satisfará esta condição de jogo. Por exemplo, especificar "Valor" corresponderá a "Valor", mas não "Valor1" ou "Valor2".
  - Utilize a opção **Ignore Case** para controlar se é feita uma comparação sensível a casos com o valor do cookie do pedido.
- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
  - Enchimento completo de cache
  - Idade máxima interna padrão
  - Força Interna Max-Age
  - Ignore a Origem Sem Cache
  - Max-Stale interno

[De volta ao topo](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Parameter de biscoitos Regex

A condição de correspondência do Parameter De Cookie Regex define um nome e valor de cookies. Pode utilizar [expressões regulares](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) para definir o valor desejado pelo cookie.

A opção **Match**Does Not / **Match** determina as condições em que o estado de jogo do Parameter Regex é cumprido.

- **Fósforos**: Requer um pedido para conter o cookie especificado com um valor que corresponda à expressão regular especificada.
- **Não Corresponder**: Requer que o pedido satisfaça qualquer um dos seguintes critérios:
  - Não contém o cookie especificado.
  - Contém o cookie especificado, mas o seu valor não corresponde à expressão regular especificada.
  
Informação-chave:

- Nome do cookie:
  - Como expressões regulares e valores wildcard, incluindo asteriscos (*), não são suportados quando está a especificar um nome de cookie, apenas os jogos de nome exatos do cookie são elegíveis para comparação.
  - Apenas um único nome de cookie pode ser especificado por exemplo desta condição de correspondência.
  - As comparações de nomes de cookies são insensíveis aos casos.
- Valor do cookie:
  - Um valor de cookie pode tirar partido de expressões regulares.
  - Utilize a opção **Ignore Case** para controlar se é feita uma comparação sensível a casos com o valor do cookie do pedido.
- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
  - Enchimento completo de cache
  - Idade máxima interna padrão
  - Força Interna Max-Age
  - Ignore a Origem Sem Cache
  - Max-Stale interno

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>País

Pode especificar um país através do seu código de país. 

A opção **Match** / **Does Match** determina as condições em que a condição de jogo do País é satisfeita:

- **Fósforos**: Requer o pedido para conter os valores de código do país especificados. 
- **Não Colhe**: Requer que o pedido não contenha os valores de código do país especificados.

Informação-chave:

- Especifique vários códigos de país delimitando cada um com um único espaço.
- Os wildcards não são suportados quando estás a especificar um código de país.
- Os códigos dos países "UE" e "AP" não abrangem todos os endereços IP nessas regiões.
- Certos pedidos podem não devolver um código de país válido. Um ponto de interrogação (?) corresponderá aos pedidos para os quais não foi possível determinar um código de país válido.
- Os códigos do país são sensíveis aos casos.
- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
  - Enchimento completo de cache
  - Idade máxima interna padrão
  - Força Interna Max-Age
  - Ignore a Origem Sem Cache
  - Max-Stale interno

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implementação da filtragem do país utilizando o motor de regras

Esta condição de jogo permite-lhe realizar uma infinidade de personalizações com base no local a partir do qual um pedido teve origem. Por exemplo, o comportamento da funcionalidade de filtragem do país pode ser replicado através da seguinte configuração:

- Url Path Wildcard match: Delineie a condição de [correspondência do URL Path Wildcard](#url-path-wildcard) para o diretório que será fixado. 
    Anexar um asterisco ao fim do caminho relativo para garantir que o acesso a todos os seus filhos será restringido por esta regra.

- Jogo de país: Definir a condição de jogo do País para o conjunto desejado de países/regiões.
  - Permitir: Definir a condição de correspondência do País para **Não Corresponder** para permitir apenas o acesso aos países/regiões especificados aos conteúdos armazenados no local definido pela condição de correspondência do URL Path Wildcard.
  - Bloco: Delineie a condição de correspondência do País para **as partidas** para bloquear os países/regiões especificados de aceder a conteúdos armazenados no local definido pela condição de correspondência do URL Path Wildcard.

- Recurso Deny Access (403): Ativar a [função De acesso a negar (403)](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) para replicar a parte de permitir ou bloquear a funcionalidade de filtragem do país.

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Origem do Cliente

Informação-chave:

- A condição de correspondência da Origem do Cliente é satisfeita independentemente de o conteúdo ser solicitado através de um URL CDN ou de um URL CNAME de borda que aponta para a origem do cliente selecionado.
- Uma configuração de origem do cliente referenciada por uma regra não pode ser eliminada da página de Origem do Cliente. Antes de tentar eliminar uma configuração de origem do cliente, certifique-se de que as seguintes configurações não a referenciam:
  - Uma condição de correspondência de origem do cliente
  - Uma configuração CNAME de borda
- Não utilize uma declaração e if para combinar certas condições de correspondência. Por exemplo, combinar uma condição de correspondência de Origem do Cliente com uma condição de correspondência de Origem CDN criaria um padrão de correspondência que nunca poderia ser igualado. Por esta razão, duas condições de correspondência de Origem do Cliente não podem ser combinadas através de uma declaração e IF.

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Dispositivo

A condição de correspondência do Dispositivo identifica pedidos feitos a partir de um dispositivo móvel com base nas suas propriedades. A deteção de dispositivos móveis é conseguida através da [WURFL](http://wurfl.sourceforge.net/). 

A opção **Match** / **Does Match** determina as condições em que a condição de correspondência do Dispositivo é satisfeita:

- **Fósforos**: Requer que o dispositivo do soliciter corresponda ao valor especificado. 
- **Não Colhe**: Requer que o dispositivo do soliciter não corresponda ao valor especificado.

Informação-chave:

- Utilize a opção **Ignore Case** para especificar se o valor especificado é sensível a casos.
- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
  - Enchimento completo de cache
  - Idade máxima interna padrão
  - Força Interna Max-Age
  - Ignore a Origem Sem Cache
  - Max-Stale interno

#### <a name="string-type"></a>Tipo de corda

Uma capacidade WURFL normalmente aceita qualquer combinação de números, letras e símbolos. Devido à natureza flexível desta capacidade, deve escolher como é interpretado o valor associado a esta condição de correspondência. O quadro seguinte descreve o conjunto de opções disponíveis:

Tipo     | Descrição
---------|------------
Literal  | Selecione esta opção para evitar que a maioria dos caracteres assuma um significado especial utilizando o seu [valor literal](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Selecione esta opção para tirar partido de todos [caracteres[wildcard].](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)
Rio Regex    | Selecione esta opção para utilizar [expressões regulares](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Expressões regulares são úteis para definir um padrão de caracteres.

#### <a name="wurfl-capabilities"></a>Capacidades WURFL

Uma capacidade WURFL refere-se a uma categoria que descreve dispositivos móveis. A capacidade selecionada determina o tipo de descrição do dispositivo móvel que é usado para identificar pedidos.

A tabela que se segue lista as capacidades da WURFL e as suas variáveis para o motor de regras.

> [!NOTE]
> As seguintes variáveis são suportadas no **Cabeçalho** de Pedido de Cliente Modificado e modificar as funcionalidades do Cabeçalho de Resposta ao **Cliente.**

Capacidade | Variável | Descrição | Valores da amostra
-----------|----------|-------------|----------------
Nome da marca | %{wurfl_cap_brand_name} | Uma corda que indica o nome da marca do dispositivo. | Samsung
Dispositivo OS | %{wurfl_cap_device_os} | Uma cadeia que indica o sistema operativo instalado no dispositivo. | iOS
Versão do SO do dispositivo | %{wurfl_cap_device_os_version} | Uma cadeia que indica o número de versão do sistema operativo instalado no dispositivo. | 1.0.1
Orientação Dupla | %{wurfl_cap_dual_orientation} | Um Boolean que indica se o dispositivo suporta uma orientação dupla. | true
HTML DTD preferido | %{wurfl_cap_html_preferred_dtd} | Uma cadeia que indica a definição de tipo de documento preferido do dispositivo móvel (DTD) para o conteúdo HTML. | nenhum<br/>xhtml_basic<br/>html5
Imagem Inlining | %{wurfl_cap_image_inlining} | Um Boolean que indica se o dispositivo suporta imagens codificadas base64. | false
É Android | %{wurfl_vcap_is_android} | Um Boolean que indica se o dispositivo utiliza o Sistema Operativo Android. | true
É IOS | %{wurfl_vcap_is_ios} | Um Boolean que indica se o dispositivo utiliza o iOS. | false
É smart TV | %{wurfl_cap_is_smarttv} | Um Boolean que indica se o dispositivo é uma tv inteligente. | false
É smartphone | %{wurfl_vcap_is_smartphone} | Um Boolean que indica se o dispositivo é um smartphone. | true
É Tablet | %{wurfl_cap_is_tablet} | Um Boolean que indica se o dispositivo é um tablet. Esta descrição é independente do OS. | true
É dispositivo sem fios | %{wurfl_cap_is_wireless_device} | Um Boolean que indica se o dispositivo é considerado um dispositivo sem fios. | true
Nome de Marketing | %{wurfl_cap_marketing_name} | Uma corda que indica o nome de marketing do dispositivo. | Pérola BlackBerry 8100
Navegador Móvel | %{wurfl_cap_mobile_browser} | Uma cadeia que indica o navegador que é usado para solicitar conteúdo do dispositivo. | Chrome
Versão do navegador móvel | %{wurfl_cap_mobile_browser_version} | Uma cadeia que indica a versão do navegador que é usada para solicitar conteúdo do dispositivo. | 31
Nome modelo | %{wurfl_cap_model_name} | Uma corda que indica o nome modelo do dispositivo. | s3
Download progressivo | %{wurfl_cap_progressive_download} | Um Boolean que indica se o dispositivo suporta a reprodução de áudio e vídeo enquanto ainda está a ser descarregado. | true
Data de Lançamento | %{wurfl_cap_release_date} | Uma cadeia que indica o ano e mês em que o dispositivo foi adicionado à base de dados WURFL.<br/><br/>Formato:`yyyy_mm` | 2013_december
Altura da Resolução | %{wurfl_cap_resolution_height} | Um inteiro que indica a altura do dispositivo em pixels. | 768
Largura da Resolução | %{wurfl_cap_resolution_width} | Um inteiro que indica a largura do dispositivo em pixels. | 1024

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>Nome de borda

Informação-chave:

- A lista de CNAMEs de borda disponível limita-se às CNAMEs de borda que foram configuradas na página Edge CNAMEs para a plataforma em que o motor de regras está a ser configurado.
- Antes de tentar eliminar uma configuração CNAME de borda, certifique-se de que uma condição de correspondência de Edge Cname não a refere. As configurações de Edge CNAME que tenham sido definidas numa regra não podem ser eliminadas da página EDGE CNAMEs.
- Não utilize uma declaração e if para combinar certas condições de correspondência. Por exemplo, combinar uma condição de correspondência de Edge Cname com uma condição de correspondência de Origem do Cliente criaria um padrão de correspondência que nunca poderia ser igualado. Por esta razão, duas condições de correspondência de Edge Cname não podem ser combinadas através de uma declaração e if.
- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
  - Enchimento completo de cache
  - Idade máxima interna padrão
  - Força Interna Max-Age
  - Ignore a Origem Sem Cache
  - Max-Stale interno

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Domínio de referimento

O nome do anfitrião associado ao remetente através do qual o conteúdo foi solicitado determina se a condição de Domínio De Referir é satisfeita.

A opção **Match** / **Does Not Match** determina as condições em que a condição de jogo do Domínio De Referir é satisfeita:

- **Fósforos**: Requer o nome do anfitrião de remetente para corresponder aos valores especificados. 
- **Não Corresponder**: Requer que o nome do anfitrião de reparação não corresponda ao valor especificado.

Informação-chave:

- Especifique vários nomes de anfitriões delimitando cada um com um único espaço.
- Esta condição de jogo suporta [os valores wildcard](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Se o valor especificado não contiver um asterisco, deve ser uma correspondência exata para o nome de anfitrião do remetente. Por exemplo, especificar "mydomain.com" não corresponderia a "www.mydomain.com".
- Utilize a opção **Ignore Case** para controlar se é feita uma comparação sensível a casos.
- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
  - Enchimento completo de cache
  - Idade máxima interna padrão
  - Força Interna Max-Age
  - Ignore a Origem Sem Cache
  - Max-Stale interno

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Pedido cabeçalho literal

A opção **Match** / **Does Not Match** determina as condições em que o estado de jogo literal do Cabeçalho de Pedido é cumprido.

- **Fósforos**: Requer o pedido para conter o cabeçalho especificado. O seu valor deve corresponder ao que está definido nesta condição de jogo.
- **Não Corresponder**: Requer que o pedido satisfaça qualquer um dos seguintes critérios:
  - Não contém o cabeçalho especificado.
  - Contém o cabeçalho especificado, mas o seu valor não corresponde ao que está definido nesta condição de jogo.
  
Informação-chave:

- As comparações de nomes do cabeçalho são sempre insensíveis ao caso. Utilize a opção **Ignore Case** para controlar a sensibilidade de caso das comparações de valor do cabeçalho.
- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
  - Enchimento completo de cache
  - Idade máxima interna padrão
  - Força Interna Max-Age
  - Ignore a Origem Sem Cache
  - Max-Stale interno

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Pedido de cabeçalho Regex

A opção **Match**Does Not / **Match** determina as condições em que o estado de jogo do Cabeçalho Regex de Pedido é cumprido.

- **Fósforos**: Requer o pedido para conter o cabeçalho especificado. O seu valor deve corresponder ao padrão definido na [expressão regular](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)especificada .
- **Não Corresponder**: Requer que o pedido satisfaça qualquer um dos seguintes critérios:
  - Não contém o cabeçalho especificado.
  - Contém o cabeçalho especificado, mas o seu valor não corresponde à expressão regular especificada.

Informação-chave:

- Nome do cabeçalho:
  - As comparações de nomes do cabeçalho são insensíveis aos casos.
  - Substitua os espaços no nome cabeçalho por "%20".
- Valor do cabeçalho:
  - Um valor cabeçalho pode tirar partido de expressões regulares.
  - Utilize a opção **Ignore Case** para controlar a sensibilidade de caso das comparações de valor do cabeçalho.
  - A condição de jogo só é satisfeita quando um valor do cabeçalho corresponde exatamente a pelo menos um dos padrões especificados.
- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
  - Enchimento completo de cache
  - Idade máxima interna padrão
  - Força Interna Max-Age
  - Ignore a Origem Sem Cache
  - Max-Stale interno

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Pedir Header Wildcard

A opção **Match** / **Does Not Match** determina as condições em que o estado de jogo do Cabeçalho de Pedido wildcard é cumprido.

- **Fósforos**: Requer o pedido para conter o cabeçalho especificado. O seu valor deve corresponder, pelo menos, a um dos valores definidos nesta condição de jogo.
- **Não Corresponder**: Requer que o pedido satisfaça qualquer um dos seguintes critérios:
  - Não contém o cabeçalho especificado.
  - Contém o cabeçalho especificado, mas o seu valor não corresponde a nenhum dos valores especificados.
  
Informação-chave:

- Nome do cabeçalho:
  - As comparações de nomes do cabeçalho são insensíveis aos casos.
  - Os espaços no nome cabeçalho devem ser substituídos por "%20". Também pode utilizar este valor para especificar espaços num valor cabeçalho.
- Valor do cabeçalho:
  - Um valor de cabeçalho pode tirar partido dos [valores wildcard.](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)
  - Utilize a opção **Ignore Case** para controlar a sensibilidade de caso das comparações de valor do cabeçalho.
  - Esta condição de jogo é satisfeita quando um valor do cabeçalho corresponde exatamente a pelo menos um dos padrões especificados.
  - Especifique vários valores delimitando cada um com um único espaço.
- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
  - Enchimento completo de cache
  - Idade máxima interna padrão
  - Força Interna Max-Age
  - Ignore a Origem Sem Cache
  - Max-Stale interno

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Método de Pedido

A condição de correspondência do Método de Pedido só é satisfeita quando os ativos são solicitados através do método de pedido selecionado. Os métodos de pedido disponíveis são:

- GET
- HEAD
- POST
- Opções
- PUT
- DELETE
- VESTÍGIOS
- LIGAÇÃO

Informação-chave:

- Por predefinição, apenas o método de pedido GET pode gerar conteúdo em cache na rede. Todos os outros métodos de pedido são proxid através da rede.
- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
  - Enchimento completo de cache
  - Idade máxima interna padrão
  - Força Interna Max-Age
  - Ignore a Origem Sem Cache
  - Max-Stale interno

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Regime de Pedidos

A condição de correspondência do Esquema de Pedido só é satisfeita quando os ativos são solicitados através do protocolo selecionado. Os protocolos disponíveis são:

- HTTP
- HTTPS

Informação-chave:

- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
  - Enchimento completo de cache
  - Idade máxima interna padrão
  - Força Interna Max-Age
  - Ignore a Origem Sem Cache
  - Max-Stale interno

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>Diretório de caminhos URL

Identifica um pedido pelo seu percurso relativo, que exclui o nome do ficheiro do ativo solicitado.

A opção **Match** / **Does Not Match** determina as condições em que a condição de correspondência do Diretório do Caminho de URL é satisfeita.

- **Fósforos**: Requer o pedido para conter um caminho URL relativo, excluindo o nome do ficheiro, que corresponda ao padrão de URL especificado.
- **Não Colhe**: Requer o pedido para conter um caminho URL relativo, excluindo o nome do ficheiro, que não corresponda ao padrão de URL especificado.

Informação-chave:

- Utilize a opção **Relativa para** especificar se a comparação de URL começa antes ou depois do ponto de acesso ao conteúdo. O ponto de acesso ao conteúdo é a parte do caminho que aparece entre o nome de anfitrião da Verizon CDN e o caminho relativo para o ativo solicitado (por exemplo, /800001/CustomerOrigin). Identifica uma localização por tipo de servidor (por exemplo, CDN ou origem do cliente) e o número da sua conta de cliente.

   Os seguintes valores estão disponíveis para o **Relativo à** opção:
  - **Raiz**: Indica que o ponto de comparação de URL começa diretamente após o nome de anfitrião do CDN. 

  Por exemplo: http: \/ /wpc.0001. &lt; domínio &gt; / **800001/myorigin/myfolder**/index.htm

  - **Origem**: Indica que o ponto de comparação de URL começa após o ponto de acesso ao conteúdo (por exemplo, /000001 ou /800001/myorigin). Uma vez que o \* .azureedge.net CNAME é criado em relação ao diretório de origem no nome de anfitrião da Verizon CDN por padrão, os utilizadores de CDN Azure devem usar o valor **Origem.** 

  Por exemplo: https: \/ / &lt; endpoint &gt; .azureedge.net/**myfolder**/index.htm 

  Este URL aponta para o seguinte nome de anfitrião Verizon CDN: http: \/ /wpc.0001. &lt; domínio &gt; /800001/myorigin/**myfolder**/index.htm

- Um URL CNAME de borda é reescrito para um URL CDN antes da comparação de URL.

    Por exemplo, ambos os URLs seguintes apontam para o mesmo ativo e, portanto, têm o mesmo caminho de URL.
  - URL CDN: http: \/ /wpc.0001. &lt; domínio &gt; /800001/CustomerOrigin/path/asset.htm
    
  - URL Edge CNAME: http: \/ / &lt; endpoint &gt; .azureedge.net/path/asset.htm
    
    Informações adicionais:
  - Domínio personalizado: https: \/ /my.domain.com/path/asset.htm
    
    - Caminho de URL (relativo à raiz): /800001/CustomerOrigin/path/
    
    - Caminho url (relativo à origem): /caminho/

- A parte do URL que é usada para a comparação de URL termina pouco antes do nome do ficheiro do ativo solicitado. Um corte para a frente é o último personagem neste tipo de caminho.

- Substitua quaisquer espaços no padrão de percurso url por "%20".

- Cada padrão de caminho URL pode conter um ou mais asteriscos (*), onde cada asterisco corresponde a uma sequência de um ou mais caracteres.

- Especifique múltiplos caminhos de URL no padrão, delimitando cada um com um único espaço.

    Por exemplo: */vendas/ */marketing/

- Uma especificação de caminho URL pode tirar partido dos [valores wildcard](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Utilize a opção **Ignore Case** para controlar se é efetuada uma comparação sensível a casos.

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>Extensão do caminho do URL

Identifica pedidos pela extensão do ficheiro do ativo solicitado.

A opção **Match** / **Does Not Match** determina as condições em que a condição de correspondência de extensão do caminho do URL é satisfeita.

- **Fósforos**: Requer o URL do pedido para conter uma extensão de ficheiro que corresponda exatamente ao padrão especificado.

   Por exemplo, se especificar "htm", os ativos "htm" são combinados, mas não ativos "html".  

- **Não Colhe**: Requer o pedido de URL para conter uma extensão de ficheiro que não corresponda ao padrão especificado.

Informação-chave:

- Especifique as extensões de ficheiro sintetizando na caixa **Valor.** Não inclua um período de liderança; por exemplo, use htm em vez de .htm.

- Utilize a opção **Ignore Case** para controlar se é efetuada uma comparação sensível a casos.

- Especifique várias extensões de ficheiros delimitando cada extensão com um único espaço. 

    Por exemplo: htm html

- Por exemplo, especificar "htm" corresponde a ativos "htm", mas não ativos "html".

#### <a name="sample-scenario"></a>Cenário de amostragem

A configuração da amostra seguinte pressupõe que esta condição de jogo é satisfeita quando um pedido corresponde a uma das extensões especificadas.

Especificação de valor: asp aspx php html

Esta condição de jogo é satisfeita quando encontra URLs que terminam com as seguintes extensões:

- .asp
- .aspx
- .php
- .html

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>Nome de ficheiro de caminho URL

Identifica pedidos pelo nome do ficheiro do ativo solicitado. Para efeitos desta condição de correspondência, um nome de ficheiro consiste no nome do ativo solicitado, um período e na extensão do ficheiro (por exemplo, index.html).

A opção **Match** / **Does Not Match** determina as condições em que a condição de correspondência do nome de ficheiro do caminho URL é satisfeita.

- **Fósforos**: Requer o pedido para conter um nome de ficheiro no seu caminho URL que corresponda ao padrão especificado.
- **Não Colhe**: Requer o pedido de contenção de um nome de ficheiro no seu percurso URL que não corresponda ao padrão especificado.

Informação-chave:

- Utilize a opção **Ignore Case** para controlar se é efetuada uma comparação sensível a casos.

- Para especificar várias extensões de ficheiros, separe cada extensão com um único espaço.

    Por exemplo: index.htm index.html

- Substitua os espaços num valor de nome de ficheiro por "%20".

- Um valor de nome de ficheiro pode tirar partido dos [valores wildcard](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Por exemplo, cada padrão de nome de ficheiro pode consistir em um ou mais asteriscos (*), onde cada asterisco corresponde a uma sequência de um ou mais caracteres.

- Se os caracteres wildcard não forem especificados, então apenas uma correspondência exata irá satisfazer esta condição de jogo.

    Por exemplo, especificar "presentation.ppt" corresponde a um ativo chamado "presentation.ppt", mas não um chamado "presentation.pptx".

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>URL Caminho Literal

Compara o caminho url de um pedido, incluindo o nome do ficheiro, com o valor especificado.

A opção **Match** / **Does Not Match** determina as condições em que a condição de correspondência literal do CAMINHO URL é satisfeita.

- **Fósforos**: Requer o pedido para conter um caminho URL que corresponda ao padrão especificado.
- **Não Colhe**: Requer o pedido para conter um caminho URL que não corresponda ao padrão especificado.

Informação-chave:

- Utilize a opção **Relativa para** especificar se o ponto de comparação de URL começa antes ou depois do ponto de acesso ao conteúdo. 

    Os seguintes valores estão disponíveis para o **Relativo à** opção:
  - **Raiz**: Indica que o ponto de comparação de URL começa diretamente após o nome de anfitrião do CDN.

    Por exemplo: http: \/ /wpc.0001. &lt; &gt; / **800001/myorigin/myfolder/index.htm de** domínio

  - **Origem**: Indica que o ponto de comparação de URL começa após o ponto de acesso ao conteúdo (por exemplo, /000001 ou /800001/myorigin). Uma vez que o \* .azureedge.net CNAME é criado em relação ao diretório de origem no nome de anfitrião da Verizon CDN por padrão, os utilizadores de CDN Azure devem usar o valor **Origem.** 

    Por exemplo: https: \/ / &lt; endpoint &gt; .azureedge.net/**myfolder/index.htm**

  Este URL aponta para o seguinte nome de anfitrião Verizon CDN: http: \/ /wpc.0001. &lt; domínio &gt; /800001/myorigin/**myfolder/index.htm**

- Um URL CNAME de borda é reescrito para um URL CDN antes de uma comparação de URL.

Por exemplo, ambos os seguintes URLs apontam para o mesmo ativo e, portanto, têm o mesmo caminho url:

- URL CDN: http: \/ /wpc.0001. &lt; domínio &gt; /800001/CustomerOrigin/path/asset.htm
- URL Edge CNAME: http: \/ / &lt; endpoint &gt; .azureedge.net/path/asset.htm

    Informações adicionais:
    
    - Caminho de URL (relativo à raiz): /800001/CustomerOrigin/path/asset.htm
   
    - Url caminho (relativo à origem): /path/asset.htm

- As cordas de consulta na URL são ignoradas.
- Utilize a opção **Ignore Case** para controlar se é efetuada uma comparação sensível a casos.
- O valor especificado para esta condição de jogo é comparado com o caminho relativo do pedido exato feito pelo cliente.

- Para combinar todos os pedidos feitos a um determinado diretório, utilize o [Diretório de Percursos URL](#url-path-directory) ou a condição de correspondência [url Path Wildcard.](#url-path-wildcard)

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>URL Path Regex

Compara o caminho url de um pedido com a [expressão regular](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)especificada .

A opção **Match** / **Does Not Match** determina as condições em que a condição de correspondência do URL Path Regex é satisfeita.

- **Fósforos**: Requer o pedido de contenção de um caminho URL que corresponda à expressão regular especificada.
- **Não Colhe**: Requer o pedido de contenção de um caminho URL que não corresponda à expressão regular especificada.

Informação-chave:

- Um URL CNAME de borda é reescrito para um URL CDN antes da comparação de URL.

    Por exemplo, ambos os URLs apontam para o mesmo ativo e, portanto, têm o mesmo caminho de URL.

     - URL CDN: http: \/ /wpc.0001. &lt; domínio &gt; /800001/CustomerOrigin/path/asset.htm

     - URL Edge CNAME: http: \/ /my.domain.com/path/asset.htm

    Informações adicionais:
    
     - Url caminho: /800001/CustomerOrigin/path/asset.htm

- As cordas de consulta na URL são ignoradas.
    
- Utilize a opção **Ignore Case** para controlar se é efetuada uma comparação sensível a casos.
    
- Os espaços no caminho do URL devem ser substituídos por "%20".

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>URL Path Wildcard

Compara o caminho de URL relativo de um pedido com o padrão wildcard especificado.

A opção **Match** / **Does Not Match** determina as condições em que a condição de correspondência do URL Path Wildcard é satisfeita.

- **Fósforos**: Requer o pedido para conter um caminho URL que corresponda ao padrão wildcard especificado.
- **Não Colhe**: Requer o pedido para conter um caminho URL que não corresponda ao padrão wildcard especificado.

Informação-chave:

- **Relativamente a** opção: Esta opção determina se o ponto de comparação de URL começa antes ou depois do ponto de acesso ao conteúdo.

   Esta opção pode ter os seguintes valores:
     - **Raiz**: Indica que o ponto de comparação de URL começa diretamente após o nome de anfitrião do CDN.

       Por exemplo: http: \/ /wpc.0001. &lt; &gt; / **800001/myorigin/myfolder/index.htm de** domínio

     - **Origem**: Indica que o ponto de comparação de URL começa após o ponto de acesso ao conteúdo (por exemplo, /000001 ou /800001/myorigin). Uma vez que o \* .azureedge.net CNAME é criado em relação ao diretório de origem no nome de anfitrião da Verizon CDN por padrão, os utilizadores de CDN Azure devem usar o valor **Origem.** 

       Por exemplo: https: \/ / &lt; endpoint &gt; .azureedge.net/**myfolder/index.htm**

     Este URL aponta para o seguinte nome de anfitrião Verizon CDN: http: \/ /wpc.0001. &lt; domínio &gt; /800001/myorigin/**myfolder/index.htm**

- Um URL CNAME de borda é reescrito para um URL CDN antes da comparação de URL.

    Por exemplo, ambos os seguintes URLs apontam para o mesmo ativo e, portanto, têm o mesmo caminho url:
     - URL CDN: http://wpc.0001.&lt ;domain &gt; /800001/CustomerOrigin/path/asset.htm
     - URL Edge CNAME: http: \/ / &lt; endpoint &gt; .azureedge.net/path/asset.htm
    
    Informações adicionais:
    
     - Caminho de URL (relativo à raiz): /800001/CustomerOrigin/path/asset.htm
    
     - Url caminho (relativo à origem): /path/asset.htm
    
- Especifique múltiplos caminhos de URL delimitando cada um com um único espaço.

   Por exemplo: /marketing/ativo.* /sales/*.htm

- As cordas de consulta na URL são ignoradas.
    
- Utilize a opção **Ignore Case** para controlar se é efetuada uma comparação sensível a casos.
    
- Substitua os espaços no caminho do URL por "%20".
    
- O valor especificado para um caminho DE URL pode tirar partido dos [valores wildcard](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Cada padrão de caminho URL pode conter um ou mais asteriscos (*), onde cada asterisco pode corresponder a uma sequência de um ou mais caracteres.

#### <a name="sample-scenarios"></a>Cenários de amostra

As configurações da amostra na tabela a seguir assumem que esta condição de jogo é satisfeita quando um pedido corresponde ao padrão de URL especificado:

Valor                   | Relativamente a    | Resultado 
------------------------|----------------|-------
*/test.html */test.php  | Raiz ou Origem | Este padrão é acompanhado por pedidos de ativos chamados "test.html" ou "test.php" em qualquer pasta.
/80ABCD/origem/texto/*   | Raiz           | Este padrão é acompanhado quando o ativo solicitado satisfaz os seguintes critérios: <br />- Deve residir numa origem do cliente chamada "origem". <br />- O caminho relativo deve começar com uma pasta chamada "texto". Ou seja, o ativo solicitado pode residir na pasta "text" ou numa das suas subpastas recursivas.
*/css/* */js/*          | Raiz ou Origem | Este padrão é combinado por todos os URLs CDN ou CNAME de borda que contenham uma pasta css ou js.
*.jpg *.gif *.png       | Raiz ou Origem | Este padrão é combinado por todos os URLs CDN ou CNAME de borda que terminam com .jpg, .gif ou .png. Uma forma alternativa de especificar este padrão é com a condição de correspondência de [extensão do caminho do URL](#url-path-extension).
/images/* /media/*      | Origem         | Este padrão é combinado por CDN ou URLs CNAME de borda cujo caminho relativo começa com uma pasta de "imagens" ou "media". <br />- URL CDN: http: \/ /wpc.0001. &lt; domínio &gt; /800001/myorigin/images/sales/event1.png<br />- URL CNAME de borda da amostra: http: \/ /cdn.mydomain.com/images/sales/event1.png

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>Url Consulta Literal

Compara a corda de consulta de um pedido com o valor especificado.

A opção **Match** / **Does Not Match** determina as condições em que a condição de correspondência literal de consulta url é satisfeita.

- **Fósforos**: Requer o pedido para conter uma cadeia de consulta url que corresponda à corda de consulta especificada.
- **Não Colhe**: Requer o pedido de contenção de uma cadeia de consulta url que não corresponda à corda de consulta especificada.

Informação-chave:

- Apenas os fósforos de corda de consulta exata satisfazem esta condição de jogo.
    
- Utilize a opção **Ignore Case** para controlar a sensibilidade de caso das comparações de cordas de consulta.
    
- Não inclua um ponto de interrogação (?) no texto de valor de corda de consulta.
    
- Certos caracteres requerem codificação de URL. Utilize o símbolo percentual para codificar os seguintes caracteres:

   Caráter | Codificação de URL
   ----------|---------
   Espaço     | %20
   &         | %25

- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
   - Enchimento completo de cache
   - Idade máxima interna padrão
   - Força Interna Max-Age
   - Ignore a Origem Sem Cache
   - Max-Stale interno

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>Parâmetro de consulta de URL

Identifica pedidos que contenham o parâmetro de corda de consulta especificado. Este parâmetro está definido para um valor que corresponde a um padrão especificado. Os parâmetros de cadeia de consulta (por exemplo, parâmetro=valor) no URL de pedido determinam se esta condição é satisfeita. Esta condição de correspondência identifica um parâmetro de corda de consulta pelo seu nome e aceita um ou mais valores para o valor do parâmetro. 

A opção **Match** / **Does Not Match** determina as condições em que a condição de correspondência do parâmetro de consulta url é satisfeita.

- **Partidas**: Requer um pedido para conter o parâmetro especificado com um valor que corresponda a pelo menos um dos valores definidos nesta condição de jogo.
- **Não Corresponder**: Requer que o pedido satisfaça qualquer um dos seguintes critérios:
  - Não contém o parâmetro especificado.
  - Contém o parâmetro especificado, mas o seu valor não corresponde a nenhum dos valores definidos nesta condição de jogo.

Esta condição de correspondência fornece uma maneira fácil de especificar as combinações de nome/valor do parâmetro. Para obter mais flexibilidade se estiver a combinar um parâmetro de corda de consulta, considere utilizar a condição de correspondência [url Query Wildcard.](#url-query-wildcard)

Informação-chave:

- Apenas um único nome de parâmetro de consulta URL pode ser especificado por exemplo desta condição de correspondência.
    
- Como os valores wildcard não são suportados quando um nome de parâmetro é especificado, apenas as correspondências exatas de nome do parâmetro são elegíveis para comparação.
- O valor do parâmetro pode incluir [valores wildcard.](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)
   - Cada padrão de valor do parâmetro pode consistir em um ou mais asteriscos (*), onde cada asterisco pode corresponder a uma sequência de um ou mais caracteres.
   - Certos caracteres requerem codificação de URL. Utilize o símbolo percentual para codificar os seguintes caracteres:

       Caráter | Codificação de URL
       ----------|---------
       Espaço     | %20
       &         | %25

- Especifique vários valores de parâmetros de corda de consulta, delimitando cada um com um único espaço. Esta condição de jogo é satisfeita quando um pedido contém uma das combinações de nome/valor especificadas.

   - Exemplo 1:

     - Configuração:

       ValorA ValorB

     - Esta configuração corresponde aos seguintes parâmetros de corda de consulta:

       Parâmetro1=ValueA
    
       Parâmetro1=ValorB

   - Exemplo 2:

     - Configuração: 

        Valor%20A Valor%20B

     - Esta configuração corresponde aos seguintes parâmetros de corda de consulta:

       Parâmetro1=Valor%20A

       Parâmetro1=Valor%20B

- Esta condição de jogo só é satisfeita quando há uma correspondência exata com pelo menos uma das combinações especificadas de nome/valor de consulta.

   Por exemplo, se utilizar a configuração no exemplo anterior, a combinação de nome/valor do parâmetro "Parameter1=ValueAdd" não seria considerada compatível. No entanto, se especificar um dos seguintes valores, corresponderá a essa combinação de nome/valor:

   - Valorado Valorb Valorb
   - ValorA* ValorB

- Utilize a opção **Ignore Case** para controlar a sensibilidade de caso das comparações de cordas de consulta.
    
- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
   - Enchimento completo de cache
   - Idade máxima interna padrão
   - Força Interna Max-Age
   - Ignore a Origem Sem Cache
   - Max-Stale interno

#### <a name="sample-scenarios"></a>Cenários de exemplo

O exemplo que se segue demonstra como esta opção funciona em situações específicas:

Name  | Valor |  Resultado
------|-------|--------
Utilizador  | João   | Este padrão é combinado quando a corda de consulta para um URL solicitado é "?user=joe".
Utilizador  | *     | Este padrão é combinado quando a corda de consulta para um URL solicitado contém um parâmetro do Utilizador.
E-mail | João\* | Este padrão é combinado quando a corda de consulta para um URL solicitado contém um parâmetro de e-mail que começa com "Joe".

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>URL Consulta Regex

Identifica pedidos que contenham o parâmetro de corda de consulta especificado. Este parâmetro está definido para um valor que corresponda a uma [expressão regular](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)especificada .

A opção **Match** / **Does Not Match** determina as condições em que a condição de correspondência do URL Query Regex é satisfeita.

- **Fósforos**: Requer o pedido de contenção de uma cadeia de consulta url que corresponda à expressão regular especificada.
- **Não Colhe**: Requer o pedido de contenção de uma cadeia de consulta url que não corresponda à expressão regular especificada.

Informação-chave:

- Apenas os fósforos exatos com a expressão regular especificada satisfazem esta condição de jogo.
    
- Utilize a opção **Ignore Case** para controlar a sensibilidade de caso das comparações de cordas de consulta.
    
- Para efeitos desta opção, uma corda de consulta começa com o primeiro personagem após o ponto de interrogação (?) delimitador para a corda de consulta.
    
- Certos caracteres requerem codificação de URL. Utilize o símbolo percentual para codificar os seguintes caracteres:

   Caráter | Codificação de URL | Valor
   ----------|--------------|------
   Espaço     | %20          | \%20
   &         | %25          | \%25

   Note que os símbolos percentuais devem ser escapados.

- Personagens especiais de expressão regular de fuga dupla (por exemplo, \^ $.+) para incluir um backslash na expressão regular.

   Por exemplo:

   Valor | Interpretado Como 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
   - Enchimento completo de cache
   - Idade máxima interna padrão
   - Força Interna Max-Age
   - Ignore a Origem Sem Cache
   - Max-Stale interno

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>URL Consulta Wildcard

Compara o(s) valor especificado com a corda de consulta do pedido.

A opção **Match** / **Does Not Match** determina as condições em que a condição de correspondência url Consulta Wildcard é satisfeita.

- **Fósforos**: Requer o pedido para conter uma cadeia de consulta URL que corresponda ao valor wildcard especificado.
- **Não Colhe**: Requer o pedido de contenção de uma cadeia de consulta url que não corresponda ao valor wildcard especificado.

Informação-chave:

- Para efeitos desta opção, uma corda de consulta começa com o primeiro personagem após o ponto de interrogação (?) delimitador para a corda de consulta.
- Os valores dos parâmetros podem incluir [valores wildcard:](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)
   - Cada padrão de valor do parâmetro pode consistir em um ou mais asteriscos (*), onde cada asterisco pode corresponder a uma sequência de um ou mais caracteres.
   - Certos caracteres requerem codificação de URL. Utilize o símbolo percentual para codificar os seguintes caracteres:

     Caráter | Codificação de URL
     ----------|---------
     Espaço     | %20
     &         | %25

- Especifique vários valores delimitando cada um com um único espaço.

   Por exemplo: *Parâmetro1=ValueB* *ValueB* *Parameter1=ValueC&Parameter2=Valorizado*

- Apenas correspondem exatamente a pelo menos um dos padrões de corda de consulta especificados satisfazem esta condição de correspondência.
    
- Utilize a opção **Ignore Case** para controlar a sensibilidade de caso das comparações de cordas de consulta.
    
- Devido à forma como as definições de cache são rastreadas, esta condição de jogo é incompatível com as seguintes características:
   - Enchimento completo de cache
   - Idade máxima interna padrão
   - Força Interna Max-Age
   - Ignore a Origem Sem Cache
   - Max-Stale interno

#### <a name="sample-scenarios"></a>Cenários de exemplo

O exemplo que se segue demonstra como esta opção funciona em situações específicas:

 Name                 | Descrição
 ---------------------|------------
utilizador=joe              | Este padrão é combinado quando a corda de consulta para um URL solicitado é "?user=joe".
\*utilizador= \* \* optout=\* | Este padrão é combinado quando a consulta de URL cDN contém o utilizador ou o parâmetro optout.

[De volta ao topo](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da Rede de Entrega de Conteúdos Azure](cdn-overview.md)
- [Referência do motor de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Expressões condicionais do motor de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funcionalidades do motor de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Sobrepor o comportamento do HTTP usando o motor de regras](cdn-verizon-premium-rules-engine.md)
