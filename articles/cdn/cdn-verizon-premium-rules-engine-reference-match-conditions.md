---
title: Condições de correspondência do mecanismo de regras do Azure CDN do Verizon Premium | Microsoft Docs
description: Documentação de referência para a rede de distribuição de conteúdo do Azure das condições de correspondência do mecanismo de regras do Verizon Premium.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 1660dca34b2f128ef5889145fcdeed0d2523b9bb
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "67593209"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Condições de correspondência do mecanismo de regras do Azure CDN do Verizon Premium

Este artigo lista descrições detalhadas das condições de correspondência disponíveis para a CDN (rede de distribuição de conteúdo) do Azure do [mecanismo de regras](cdn-verizon-premium-rules-engine.md)do Verizon Premium.

A segunda parte de uma regra é a condição de correspondência. Uma condição de correspondência identifica tipos específicos de solicitações para as quais um conjunto de recursos será executado.

Por exemplo, você pode usar uma condição de correspondência para:

- Filtrar solicitações de conteúdo em um local específico.
- Filtrar solicitações geradas de um determinado endereço IP ou país/região.
- Filtrar solicitações por informações de cabeçalho.

## <a name="always-match-condition"></a>Condição de correspondência sempre

A condição de correspondência sempre aplica um conjunto padrão de recursos a todas as solicitações.

Nome | Objetivo
-----|--------
[Sempre](#always) | Aplica um conjunto padrão de recursos a todas as solicitações.

## <a name="device-match-condition"></a>Condição de correspondência de dispositivo

A condição de correspondência de dispositivo identifica solicitações feitas de um dispositivo móvel com base em suas propriedades.  

Nome | Objetivo
-----|--------
[Vice](#device) | Identifica solicitações feitas de um dispositivo móvel com base em suas propriedades.

## <a name="location-match-conditions"></a>Condições de correspondência de localização

As condições de correspondência de localização identificam solicitações com base no local do solicitante.

Nome | Objetivo
-----|--------
[Número AS](#as-number) | Identifica solicitações originadas de uma rede específica.
[País](#country) | Identifica solicitações originadas dos países/regiões especificados.

## <a name="origin-match-conditions"></a>Condições de correspondência de origem

As condições de correspondência de origem identificam solicitações que apontam para o armazenamento de rede de distribuição de conteúdo ou um servidor de origem do cliente.

Nome | Objetivo
-----|--------
[Origem da CDN](#cdn-origin) | Identifica solicitações de conteúdo armazenado no armazenamento de rede de distribuição de conteúdo.
[Origem do cliente](#customer-origin) | Identifica solicitações de conteúdo armazenado em um servidor de origem do cliente específico.

## <a name="request-match-conditions"></a>Condições de correspondência de solicitação

As condições de correspondência de solicitação identificam solicitações com base em suas propriedades.

Nome | Objetivo
-----|--------
[Endereço IP do cliente](#client-ip-address) | Identifica solicitações originadas de um endereço IP específico.
[Parâmetro de cookie](#cookie-parameter) | Verifica os cookies associados a cada solicitação para o valor especificado.
[Regex de parâmetro de cookie](#cookie-parameter-regex) | Verifica os cookies associados a cada solicitação para a expressão regular especificada.
[CNAME de borda](#edge-cname) | Identifica as solicitações que apontam para um CNAME de borda específico.
[Domínio de referência](#referring-domain) | Identifica solicitações que foram referenciadas dos nomes de host especificados.
[Literal de cabeçalho de solicitação](#request-header-literal) | Identifica solicitações que contêm o cabeçalho especificado definido como um valor especificado.
[Regex de cabeçalho de solicitação](#request-header-regex) | Identifica solicitações que contêm o cabeçalho especificado definido como um valor que corresponde à expressão regular especificada.
[Curinga de cabeçalho de solicitação](#request-header-wildcard) | Identifica solicitações que contêm o cabeçalho especificado definido como um valor que corresponde ao padrão especificado.
[Método de solicitação](#request-method) | Identifica solicitações por seu método HTTP.
[Esquema de solicitação](#request-scheme) | Identifica solicitações por seu protocolo HTTP.

## <a name="url-match-conditions"></a>Condições de correspondência de URL

As condições de correspondência de URL identificam solicitações com base em suas URLs.

Nome | Objetivo
-----|--------
[Diretório do caminho da URL](#url-path-directory) | Identifica solicitações por seu caminho relativo.
[Extensão do caminho da URL](#url-path-extension) | Identifica solicitações por sua extensão de nome de arquivo.
[Nome de arquivo do caminho da URL](#url-path-filename) | Identifica solicitações por nome de arquivo.
[Literal de caminho de URL](#url-path-literal) | Compara o caminho relativo de uma solicitação com o valor especificado.
[Regex de caminho de URL](#url-path-regex) | Compara o caminho relativo de uma solicitação com a expressão regular especificada.
[Curinga de caminho de URL](#url-path-wildcard) | Compara o caminho relativo de uma solicitação com o padrão especificado.
[Literal de consulta de URL](#url-query-literal) | Compara a cadeia de caracteres de consulta de uma solicitação com o valor especificado.
[Parâmetro de consulta de URL](#url-query-parameter) | Identifica solicitações que contêm o parâmetro de cadeia de caracteres de consulta especificado definido como um valor que corresponde a um padrão especificado.
[Regex de consulta de URL](#url-query-regex) | Identifica solicitações que contêm o parâmetro de cadeia de caracteres de consulta especificado definido como um valor que corresponde a uma expressão regular especificada.
[Curinga de consulta de URL](#url-query-wildcard) | Compara o valor especificado com a cadeia de caracteres de consulta da solicitação.

## <a name="reference-for-rules-engine-match-conditions"></a>Referência para condições de correspondência do mecanismo de regras

---

### <a name="always"></a>Sempre

A condição de correspondência sempre aplica um conjunto padrão de recursos a todas as solicitações.

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>Número AS

A rede de número as é definida por seu número de sistema autônomo (ASN). 

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência de número as é atendida:

- **Corresponde**a: Requer que o ASN da rede do cliente corresponda a um dos ASNs especificados. 
- Não **corresponde**a: Exige que o ASN da rede do cliente não corresponda a nenhum dos ASNs especificados.

Informações da chave:

- Especifique vários ASNs delimitando cada um deles com um único espaço. Por exemplo, 64514 64515 corresponde a solicitações que chegam de 64514 ou 64515.
- Algumas solicitações podem não retornar um ASN válido. Um ponto de interrogação (?) corresponderá a solicitações para as quais um ASN válido não pôde ser determinado.
- Especifique todo o ASN para a rede desejada. Valores parciais não serão correspondidos.
- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
  - Concluir preenchimento de cache
  - Idade máxima interna padrão
  - Forçar Max-age interna
  - Ignorar não cache de origem
  - Max-obsoleto interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>Origem da CDN

A condição de correspondência de origem da CDN é atendida quando as duas condições a seguir são atendidas:

- O conteúdo do armazenamento da CDN foi solicitado.
- O URI de solicitação usa o tipo de ponto de acesso ao conteúdo (por exemplo,/000001) que é definido nessa condição de correspondência:
  - URL DA CDN: O URI de solicitação deve conter o ponto de acesso ao conteúdo selecionado.
  - URL de CNAME de borda: A configuração de CNAME de borda correspondente deve apontar para o ponto de acesso de conteúdo selecionado.
  
Informações da chave:

- O ponto de acesso ao conteúdo identifica o serviço que deve fornecer o conteúdo solicitado.
- Não use uma instrução AND IF para combinar certas condições de correspondência. Por exemplo, a combinação de uma condição de correspondência de origem CDN com uma condição de correspondência de origem do cliente criaria um padrão de correspondência que nunca poderia ser correspondido. Por esse motivo, duas condições de correspondência de origem da CDN não podem ser combinadas por meio de uma instrução AND IF.

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Endereço IP do cliente

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência de endereço IP do cliente é atendida:

- **Corresponde**a: Requer que o endereço IP do cliente corresponda a um dos endereços IP especificados. 
- Não **corresponde**a: Requer que o endereço IP do cliente não corresponda a nenhum dos endereços IP especificados. 

Informações da chave:

- Use a notação CIDR.
- Especifique vários endereços IP e/ou blocos de endereço IP delimitando cada um deles com um único espaço. Por exemplo:
  - **Exemplo de IPv4**: 1.2.3.4 10.20.30.40 corresponde a todas as solicitações que chegam do endereço 1.2.3.4 ou 10.20.30.40.
  - **Exemplo de IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 corresponde a todas as solicitações que chegam do endereço 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.
- A sintaxe de um bloco de endereço IP é o endereço IP base seguido por uma barra e o tamanho do prefixo. Por exemplo:
  - **Exemplo de IPv4**: 5.5.5.64/26 corresponde a todas as solicitações que chegam de endereços 5.5.5.64 por meio de 5.5.5.127.
  - **Exemplo de IPv6**: 1:2:3:/48 corresponde a todas as solicitações que chegam de endereços 1:2:3:0:0:0:0:0 a 1:2: 3: ffff: ffff: ffff: ffff: ffff.
- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
  - Concluir preenchimento de cache
  - Idade máxima interna padrão
  - Forçar Max-age interna
  - Ignorar não cache de origem
  - Max-obsoleto interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Parâmetro de cookie

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência de parâmetro de cookie é atendida.

- **Corresponde**a: Requer que uma solicitação contenha o cookie especificado com um valor que corresponda a pelo menos um dos valores definidos nessa condição de correspondência.
- Não **corresponde**a: Requer que a solicitação atenda a qualquer um dos seguintes critérios:
  - Ele não contém o cookie especificado.
  - Ele contém o cookie especificado, mas seu valor não corresponde a nenhum dos valores definidos nessa condição de correspondência.
  
Informações da chave:

- Nome do cookie:
  - Como os valores curinga, incluindo asteriscos (*), não têm suporte quando você está especificando um nome de cookie, somente correspondências de nome de cookie exatas são elegíveis para comparação.
  - Apenas um único nome de cookie pode ser especificado por instância dessa condição de correspondência.
  - Comparações de nome de cookie diferenciam maiúsculas de minúsculas.
- Valor do cookie:
  - Especifique vários valores de cookie delimitando cada um deles com um único espaço.
  - Um valor de cookie pode aproveitar [os valores curinga](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Se um valor curinga não tiver sido especificado, somente uma correspondência exata atenderá a essa condição de correspondência. Por exemplo, a especificação de "valor" corresponderá a "valor", mas não a "value1" ou "value2".
  - Use a opção **Ignorar maiúsculas** para controlar se uma comparação que diferencia maiúsculas de minúsculas é feita em relação ao valor de cookie da solicitação.
- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
  - Concluir preenchimento de cache
  - Idade máxima interna padrão
  - Forçar Max-age interna
  - Ignorar não cache de origem
  - Max-obsoleto interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Regex de parâmetro de cookie

A condição de correspondência Regex de parâmetro de cookie define um nome e valor de cookie. Você pode usar [expressões regulares](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) para definir o valor de cookie desejado.

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência Regex de parâmetro de cookie são atendidas.

- **Corresponde**a: Requer que uma solicitação contenha o cookie especificado com um valor que corresponda à expressão regular especificada.
- Não **corresponde**a: Requer que a solicitação atenda a qualquer um dos seguintes critérios:
  - Ele não contém o cookie especificado.
  - Ele contém o cookie especificado, mas seu valor não corresponde à expressão regular especificada.
  
Informações da chave:

- Nome do cookie:
  - Como não há suporte para expressões regulares e valores curinga, incluindo asteriscos (*), quando você está especificando um nome de cookie, somente correspondências de nome de cookie exatas são elegíveis para comparação.
  - Apenas um único nome de cookie pode ser especificado por instância dessa condição de correspondência.
  - Comparações de nome de cookie diferenciam maiúsculas de minúsculas.
- Valor do cookie:
  - Um valor de cookie pode tirar proveito de expressões regulares.
  - Use a opção **Ignorar maiúsculas** para controlar se uma comparação que diferencia maiúsculas de minúsculas é feita em relação ao valor de cookie da solicitação.
- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
  - Concluir preenchimento de cache
  - Idade máxima interna padrão
  - Forçar Max-age interna
  - Ignorar não cache de origem
  - Max-obsoleto interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Country

Você pode especificar um país por meio de seu código de país. 

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência de país é atendida:

- **Corresponde**a: Requer que a solicitação contenha os valores de código de país especificados. 
- Não **corresponde**a: Requer que a solicitação não contenha os valores de código de país especificados.

Informações da chave:

- Especifique vários códigos de país delimitando cada um deles com um único espaço.
- Não há suporte para caracteres curinga quando você está especificando um código de país.
- Os códigos do país "EU" e "AP" não abrangem todos os endereços IP nessas regiões.
- Algumas solicitações podem não retornar um código de país válido. Um ponto de interrogação (?) corresponderá a solicitações para as quais um código de país válido não pôde ser determinado.
- Os códigos de país diferenciam maiúsculas de minúsculas.
- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
  - Concluir preenchimento de cache
  - Idade máxima interna padrão
  - Forçar Max-age interna
  - Ignorar não cache de origem
  - Max-obsoleto interno

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implementando a filtragem de país usando o mecanismo de regras

Essa condição de correspondência permite que você execute uma infinidade de personalizações com base no local do qual uma solicitação foi originada. Por exemplo, o comportamento do recurso de filtragem de país pode ser replicado por meio da seguinte configuração:

- Correspondência de curinga de caminho de URL: Defina a [condição de correspondência curinga do caminho da URL](#url-path-wildcard) para o diretório que será protegido. 
    Acrescente um asterisco ao final do caminho relativo para garantir que o acesso a todos os seus filhos será restringido por essa regra.

- Correspondência de país: Defina a condição de correspondência de país para o conjunto de países desejado.
  - Permitir Defina a condição de correspondência de país como não **corresponde** para permitir que apenas os países especificados acessem o conteúdo armazenado no local definido pela condição de correspondência curinga de caminho de URL.
  - Impeça Defina a condição de correspondência de país como correspondências para impedir que os países especificados acessem o conteúdo armazenado no local definido pela condição de correspondência curinga de caminho de URL.

- Recurso negar acesso (403): Habilite o [recurso negar acesso (403)](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) para replicar a parte permitir ou bloquear do recurso de filtragem de país.

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Origem do cliente

Informações da chave:

- A condição de correspondência de origem do cliente é atendida independentemente de o conteúdo ser solicitado por meio de uma URL CDN ou de uma URL CNAME de borda que aponta para a origem do cliente selecionada.
- Uma configuração de origem do cliente que é referenciada por uma regra não pode ser excluída da página de origem do cliente. Antes de tentar excluir uma configuração de origem do cliente, verifique se as seguintes configurações não fazem referência a ela:
  - Uma condição de correspondência de origem do cliente
  - Uma configuração de CNAME de borda
- Não use uma instrução AND IF para combinar certas condições de correspondência. Por exemplo, combinar uma condição de correspondência de origem do cliente com uma condição de correspondência de origem da CDN criaria um padrão de correspondência que nunca poderia ser correspondido. Por esse motivo, duas condições de correspondência de origem do cliente não podem ser combinadas por meio de uma instrução AND IF.

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Dispositivo

A condição de correspondência de dispositivo identifica solicitações feitas de um dispositivo móvel com base em suas propriedades. A detecção de dispositivo móvel é obtida por meio do [WURFL](http://wurfl.sourceforge.net/). 

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência do dispositivo é atendida:

- **Corresponde**a: Requer que o dispositivo do solicitante corresponda ao valor especificado. 
- Não **corresponde**a: Requer que o dispositivo do solicitante não corresponda ao valor especificado.

Informações da chave:

- Use a opção **Ignorar caso** para especificar se o valor especificado diferencia maiúsculas de minúsculas.
- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
  - Concluir preenchimento de cache
  - Idade máxima interna padrão
  - Forçar Max-age interna
  - Ignorar não cache de origem
  - Max-obsoleto interno

#### <a name="string-type"></a>Tipo de cadeia de caracteres

Um recurso WURFL normalmente aceita qualquer combinação de números, letras e símbolos. Devido à natureza flexível desse recurso, você deve escolher como o valor associado a essa condição de correspondência é interpretado. A tabela a seguir descreve o conjunto de opções disponíveis:

Type     | Descrição
---------|------------
Literal  | Selecione esta opção para impedir que a maioria dos caracteres utilize o significado especial usando seu [valor literal](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Amplia | Selecione esta opção para aproveitar todos os [caracteres curinga] ([valores curinga](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)).
Regex    | Selecione esta opção para usar [expressões regulares](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Expressões regulares são úteis para definir um padrão de caracteres.

#### <a name="wurfl-capabilities"></a>Recursos do WURFL

Um recurso WURFL se refere a uma categoria que descreve os dispositivos móveis. A funcionalidade selecionada determina o tipo de descrição de dispositivo móvel que é usado para identificar solicitações.

A tabela a seguir lista os recursos do WURFL e suas variáveis para o mecanismo de regras.

> [!NOTE]
> As variáveis a seguir têm suporte nos recursos **Modificar cabeçalho de solicitação do cliente** e **Modificar cabeçalho de resposta do cliente** .

Funcionalidade | Variável | Descrição | Valores de exemplo
-----------|----------|-------------|----------------
Nome da marca | %{wurfl_cap_brand_name} | Uma cadeia de caracteres que indica o nome da marca do dispositivo. | Samsung
SO do dispositivo | %{wurfl_cap_device_os} | Uma cadeia de caracteres que indica o sistema operacional instalado no dispositivo. | IOS
Versão do SO do Dispositivo | %{wurfl_cap_device_os_version} | Uma cadeia de caracteres que indica o número de versão do sistema operacional instalado no dispositivo. | 1.0.1
Orientação dupla | %{wurfl_cap_dual_orientation} | Um booliano que indica se o dispositivo dá suporte à orientação dupla. | true
DTD HTML preferencial | %{wurfl_cap_html_preferred_dtd} | Uma cadeia de caracteres que indica a definição de tipo de documento (DTD) preferencial do dispositivo móvel para conteúdo HTML. | nenhum<br/>xhtml_basic<br/>html5
Inalinhamento de imagem | %{wurfl_cap_image_inlining} | Um booliano que indica se o dispositivo dá suporte a imagens codificadas em base64. | false
É Android | %{wurfl_vcap_is_android} | Um booliano que indica se o dispositivo usa o sistema operacional Android. | true
É IOS | %{wurfl_vcap_is_ios} | Um booliano que indica se o dispositivo usa iOS. | false
É Smart TV | %{wurfl_cap_is_smarttv} | Um booliano que indica se o dispositivo é uma TV inteligente. | false
É smartphone | %{wurfl_vcap_is_smartphone} | Um booliano que indica se o dispositivo é um smartphone. | true
É Tablet | %{wurfl_cap_is_tablet} | Um booliano que indica se o dispositivo é um Tablet. Essa descrição é independente do sistema operacional. | true
É dispositivo sem fio | %{wurfl_cap_is_wireless_device} | Um booliano que indica se o dispositivo é considerado um dispositivo sem fio. | true
Nome do marketing | %{wurfl_cap_marketing_name} | Uma cadeia de caracteres que indica o nome de marketing do dispositivo. | BlackBerry 8100 Pearl
Navegador móvel | %{wurfl_cap_mobile_browser} | Uma cadeia de caracteres que indica o navegador que é usado para solicitar conteúdo do dispositivo. | Chrome
Versão do navegador móvel | %{wurfl_cap_mobile_browser_version} | Uma cadeia de caracteres que indica a versão do navegador que é usada para solicitar conteúdo do dispositivo. | 31
Nome do Modelo | %{wurfl_cap_model_name} | Uma cadeia de caracteres que indica o nome do modelo do dispositivo. | s3
Download progressivo | %{wurfl_cap_progressive_download} | Um booliano que indica se o dispositivo dá suporte à reprodução de áudio e vídeo enquanto ainda está sendo baixado. | true
Data de lançamento | %{wurfl_cap_release_date} | Uma cadeia de caracteres que indica o ano e o mês em que o dispositivo foi adicionado ao banco de dados WURFL.<br/><br/>Formato: `yyyy_mm` | 2013_december
Altura da resolução | %{wurfl_cap_resolution_height} | Um inteiro que indica a altura do dispositivo em pixels. | 768
Largura da resolução | %{wurfl_cap_resolution_width} | Um inteiro que indica a largura do dispositivo em pixels. | 1024

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>CNAME de borda

Informações da chave:

- A lista de CNAMEs de borda disponíveis é limitada a esses CNAMEs de borda que foram configurados na página CNAMEs de borda para a plataforma na qual o mecanismo de regras está sendo configurado.
- Antes de tentar excluir uma configuração de CNAME de borda, verifique se uma condição de correspondência CNAME de borda não faz referência a ela. As configurações de CNAME de borda que foram definidas em uma regra não podem ser excluídas da página CNAMEs de borda.
- Não use uma instrução AND IF para combinar certas condições de correspondência. Por exemplo, combinar uma condição de correspondência de CNAME de borda com uma condição de correspondência de origem do cliente criaria um padrão de correspondência que nunca poderia ser correspondido. Por esse motivo, duas condições de correspondência de CNAME de borda não podem ser combinadas por meio de uma instrução AND IF.
- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
  - Concluir preenchimento de cache
  - Idade máxima interna padrão
  - Forçar Max-age interna
  - Ignorar não cache de origem
  - Max-obsoleto interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Domínio de referência

O nome do host associado ao referenciador por meio do qual o conteúdo foi solicitado determina se a condição do domínio de referência é atendida.

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência de domínio de referência são atendidas:

- **Corresponde**a: Requer o nome do host de referência para corresponder aos valores especificados. 
- Não **corresponde**a: Requer que o nome do host de referência não corresponda ao valor especificado.

Informações da chave:

- Especifique vários nomes de host delimitando cada um deles com um único espaço.
- Essa condição de correspondência dá suporte a [valores curinga](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Se o valor especificado não contiver um asterisco, ele deverá ser uma correspondência exata para o nome de host do referenciador. Por exemplo, especificar "mydomain.com" não corresponderia a "www.mydomain.com".
- Use a opção **Ignorar maiúsculas** para controlar se uma comparação que diferencia maiúsculas de minúsculas é feita.
- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
  - Concluir preenchimento de cache
  - Idade máxima interna padrão
  - Forçar Max-age interna
  - Ignorar não cache de origem
  - Max-obsoleto interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Literal de cabeçalho de solicitação

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência literal do cabeçalho de solicitação é atendida.

- **Corresponde**a: Requer que a solicitação contenha o cabeçalho especificado. Seu valor deve corresponder ao que está definido nessa condição de correspondência.
- Não **corresponde**a: Requer que a solicitação atenda a qualquer um dos seguintes critérios:
  - Ele não contém o cabeçalho especificado.
  - Ele contém o cabeçalho especificado, mas seu valor não corresponde ao que está definido nessa condição de correspondência.
  
Informações da chave:

- Comparações de nome de cabeçalho sempre diferenciam maiúsculas de minúsculas. Use a opção **Ignorar maiúsculas** para controlar a diferenciação de maiúsculas e minúsculas das comparações de valor de cabeçalho.
- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
  - Concluir preenchimento de cache
  - Idade máxima interna padrão
  - Forçar Max-age interna
  - Ignorar não cache de origem
  - Max-obsoleto interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Regex de cabeçalho de solicitação

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência Regex de cabeçalho de solicitação são atendidas.

- **Corresponde**a: Requer que a solicitação contenha o cabeçalho especificado. Seu valor deve corresponder ao padrão definido na [expressão regular](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)especificada.
- Não **corresponde**a: Requer que a solicitação atenda a qualquer um dos seguintes critérios:
  - Ele não contém o cabeçalho especificado.
  - Ele contém o cabeçalho especificado, mas seu valor não corresponde à expressão regular especificada.

Informações da chave:

- Nome do cabeçalho:
  - Comparações de nome de cabeçalho diferenciam maiúsculas de minúsculas.
  - Substitua os espaços no nome do cabeçalho por "% 20".
- Valor do cabeçalho:
  - Um valor de cabeçalho pode tirar proveito de expressões regulares.
  - Use a opção **Ignorar maiúsculas** para controlar a diferenciação de maiúsculas e minúsculas das comparações de valor de cabeçalho.
  - A condição de correspondência é atendida somente quando um valor de cabeçalho corresponde exatamente a pelo menos um dos padrões especificados.
- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
  - Concluir preenchimento de cache
  - Idade máxima interna padrão
  - Forçar Max-age interna
  - Ignorar não cache de origem
  - Max-obsoleto interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Curinga de cabeçalho de solicitação

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência curinga do cabeçalho de solicitação é atendida.

- **Corresponde**a: Requer que a solicitação contenha o cabeçalho especificado. Seu valor deve corresponder a pelo menos um dos valores definidos nessa condição de correspondência.
- Não **corresponde**a: Requer que a solicitação atenda a qualquer um dos seguintes critérios:
  - Ele não contém o cabeçalho especificado.
  - Ele contém o cabeçalho especificado, mas seu valor não corresponde a nenhum dos valores especificados.
  
Informações da chave:

- Nome do cabeçalho:
  - Comparações de nome de cabeçalho diferenciam maiúsculas de minúsculas.
  - Os espaços no nome do cabeçalho devem ser substituídos por "% 20". Você também pode usar esse valor para especificar espaços em um valor de cabeçalho.
- Valor do cabeçalho:
  - Um valor de cabeçalho pode tirar proveito dos [valores curinga](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use a opção **Ignorar maiúsculas** para controlar a diferenciação de maiúsculas e minúsculas das comparações de valor de cabeçalho.
  - Essa condição de correspondência é atendida quando um valor de cabeçalho corresponde exatamente a pelo menos um dos padrões especificados.
  - Especifique vários valores delimitando cada um deles com um único espaço.
- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
  - Concluir preenchimento de cache
  - Idade máxima interna padrão
  - Forçar Max-age interna
  - Ignorar não cache de origem
  - Max-obsoleto interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Método de solicitação

A condição de correspondência do método de solicitação é atendida somente quando os ativos são solicitados por meio do método de solicitação selecionado. Os métodos de solicitação disponíveis são:

- GET
- CABEÇALHO
- POST
- OPÇÕES
- PUT
- DELETE
- RASTREOU
- CONNECT

Informações da chave:

- Por padrão, somente o método GET Request pode gerar conteúdo armazenado em cache na rede. Todos os outros métodos de solicitação são proxies por meio da rede.
- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
  - Concluir preenchimento de cache
  - Idade máxima interna padrão
  - Forçar Max-age interna
  - Ignorar não cache de origem
  - Max-obsoleto interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Esquema de solicitação

A condição de correspondência do esquema de solicitação é atendida somente quando os ativos são solicitados por meio do protocolo selecionado. Os protocolos disponíveis são:

- HTTP
- HTTPS

Informações da chave:

- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
  - Concluir preenchimento de cache
  - Idade máxima interna padrão
  - Forçar Max-age interna
  - Ignorar não cache de origem
  - Max-obsoleto interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>Diretório do caminho da URL

Identifica uma solicitação por seu caminho relativo, que exclui o nome de arquivo do ativo solicitado.

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência do diretório do caminho da URL é atendida.

- **Corresponde**a: Requer que a solicitação contenha um caminho de URL relativo, excluindo o nome do arquivo, que corresponda ao padrão de URL especificado.
- Não **corresponde**a: Requer que a solicitação contenha um caminho de URL relativo, exceto o nome do arquivo, que não corresponda ao padrão de URL especificado.

Informações da chave:

- Use a opção **relativo a** para especificar se a comparação de URL começa antes ou depois do ponto de acesso de conteúdo. O ponto de acesso ao conteúdo é a parte do caminho que aparece entre o nome de host Verizon CDN e o caminho relativo para o ativo solicitado (por exemplo,/800001/CustomerOrigin). Ele identifica um local por tipo de servidor (por exemplo, CDN ou origem do cliente) e o número da conta do cliente.

   Os valores a seguir estão disponíveis para a opção **relativo a** :
  - **Raiz**: Indica que o ponto de comparação de URL começa diretamente após o nome de host da CDN. 

  Por exemplo: http:\//WPC.0001.&lt; domínio&gt;800001/ **/myorigin/pasta**/index.htm

  - **Origem**: Indica que o ponto de comparação de URL começa após o ponto de acesso de conteúdo (por exemplo,/000001 ou/800001/myorigin). Como o \*CNAME. azureedge.net é criado em relação ao diretório de origem no nome de host da CDN da Verizon por padrão, os usuários da CDN do Azure devem usar o valor de **origem** . 

  Por exemplo: https:\//&lt;Endpoint&gt;. azureedge.NET/**MyFolder**/index.htm 

  Esta URL aponta para o seguinte nome de host da CDN da\/Verizon&lt; : http:/WPC.0001. domínio&gt;/800001/myorigin/**MyFolder**/index.htm

- Uma URL CNAME de borda é reescrita para uma URL CDN antes da comparação de URL.

    Por exemplo, ambas as URLs a seguir apontam para o mesmo ativo e, portanto, têm o mesmo caminho de URL.
  - URL da CDN: http\/:&lt; /WPC.0001. domínio&gt;/800001/CustomerOrigin/Path/Asset.htm
    
  - URL de CNAME de borda:\/http&gt;:/&lt;Endpoint. azureedge.net/Path/Asset.htm
    
    Informações adicionais:
  - Domínio personalizado: https:\//My.domain.com/Path/Asset.htm
    
    - Caminho da URL (relativo à raiz):/800001/CustomerOrigin/path/
    
    - Caminho da URL (relativo à origem):/Path/

- A parte da URL usada para a comparação de URL termina logo antes do nome de arquivo do ativo solicitado. Uma barra invertida é o último caractere nesse tipo de caminho.

- Substitua os espaços no padrão de caminho da URL por "% 20".

- Cada padrão de caminho de URL pode conter um ou mais asteriscos (*), onde cada asterisco corresponde a uma sequência de um ou mais caracteres.

- Especifique vários caminhos de URL no padrão delimitando cada um deles com um único espaço.

    Por exemplo: */Sales/*/marketing/

- Uma especificação de caminho de URL pode aproveitar [os valores curinga](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use a opção **Ignorar maiúsculas** para controlar se uma comparação que diferencia maiúsculas de minúsculas é executada.

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>Extensão do caminho da URL

Identifica solicitações pela extensão de arquivo do ativo solicitado.

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência de extensão de caminho de URL são atendidas.

- **Corresponde**a: Requer que a URL da solicitação contenha uma extensão de arquivo que corresponda exatamente ao padrão especificado.

   Por exemplo, se você especificar "htm", os ativos "htm" serão correspondidos, mas não os ativos "html".  

- Não **corresponde**a: Requer que a solicitação de URL contenha uma extensão de arquivo que não corresponda ao padrão especificado.

Informações da chave:

- Especifique as extensões de arquivo a serem correspondidas na caixa **valor** . Não incluir um ponto à esquerda; por exemplo, use htm em vez de. htm.

- Use a opção **Ignorar maiúsculas** para controlar se uma comparação que diferencia maiúsculas de minúsculas é executada.

- Especifique várias extensões de arquivo delimitando cada extensão com um único espaço. 

    Por exemplo: htm HTML

- Por exemplo, a especificação de "htm" corresponde aos ativos "htm", mas não aos ativos "html".

#### <a name="sample-scenario"></a>Cenário de exemplo

A configuração de exemplo a seguir pressupõe que essa condição de correspondência seja atendida quando uma solicitação corresponde a uma das extensões especificadas.

Especificação de valor: ASP aspx PHP HTML

Essa condição de correspondência é atendida quando encontra URLs que terminam com as seguintes extensões:

- . asp
- .aspx
- .php
- .html

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>Nome de arquivo do caminho da URL

Identifica solicitações pelo nome de arquivo do ativo solicitado. Para os fins dessa condição de correspondência, um nome de arquivo consiste no nome do ativo solicitado, um ponto e a extensão do arquivo (por exemplo, index. html).

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência de nome de arquivo de caminho de URL são atendidas.

- **Corresponde**a: Requer que a solicitação contenha um nome de arquivo em seu caminho de URL que corresponda ao padrão especificado.
- Não **corresponde**a: Requer que a solicitação contenha um nome de arquivo em seu caminho de URL que não corresponda ao padrão especificado.

Informações da chave:

- Use a opção **Ignorar maiúsculas** para controlar se uma comparação que diferencia maiúsculas de minúsculas é executada.

- Para especificar várias extensões de arquivo, separe cada extensão com um único espaço.

    Por exemplo: index. htm index. html

- Substitua espaços em um valor de nome de arquivo por "% 20".

- Um valor de nome de arquivo pode aproveitar [os valores curinga](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Por exemplo, cada padrão de nome de arquivo pode consistir em um ou mais asteriscos (*), onde cada asterisco corresponde a uma sequência de um ou mais caracteres.

- Se os caracteres curinga não forem especificados, somente uma correspondência exata atenderá a essa condição de correspondência.

    Por exemplo, a especificação de "Presentation. ppt" corresponde a um ativo chamado "Presentation. ppt", mas não um chamado "Presentation. pptx".

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>Literal de caminho de URL

Compara o caminho da URL de uma solicitação, incluindo o nome do arquivo, ao valor especificado.

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência literal de caminho de URL são atendidas.

- **Corresponde**a: Requer que a solicitação contenha um caminho de URL que corresponda ao padrão especificado.
- Não **corresponde**a: Requer que a solicitação contenha um caminho de URL que não corresponda ao padrão especificado.

Informações da chave:

- Use a opção **relativo a** para especificar se o ponto de comparação de URL começa antes ou depois do ponto de acesso de conteúdo. 

    Os valores a seguir estão disponíveis para a opção **relativo a** :
  - **Raiz**: Indica que o ponto de comparação de URL começa diretamente após o nome de host da CDN.

    Por exemplo: http:\//WPC.0001.&lt; &gt;**800001/myorigin/MyFolder/index.htm** de domínio/

  - **Origem**: Indica que o ponto de comparação de URL começa após o ponto de acesso de conteúdo (por exemplo,/000001 ou/800001/myorigin). Como o \*CNAME. azureedge.net é criado em relação ao diretório de origem no nome de host da CDN da Verizon por padrão, os usuários da CDN do Azure devem usar o valor de **origem** . 

    Por exemplo: https:\//&lt;Endpoint&gt;. azureedge.NET/**MyFolder/index.htm**

  Esta URL aponta para o seguinte nome de host da CDN da\/Verizon&lt; : http:/WPC.0001. /800001/myorigin/&gt;de domínio**MyFolder/index.htm**

- Uma URL CNAME de borda é reescrita para uma URL CDN antes de uma comparação de URL.

Por exemplo, ambas as URLs a seguir apontam para o mesmo ativo e, portanto, têm o mesmo caminho de URL:

- URL da CDN: http\/:&lt; /WPC.0001. domínio&gt;/800001/CustomerOrigin/Path/Asset.htm
- URL de CNAME de borda:\/http&gt;:/&lt;Endpoint. azureedge.net/Path/Asset.htm

    Informações adicionais:
    
    - Caminho da URL (relativo à raiz):/800001/CustomerOrigin/path/asset.htm
   
    - Caminho da URL (relativo à origem):/path/asset.htm

- As cadeias de caracteres de consulta na URL são ignoradas.
- Use a opção **Ignorar maiúsculas** para controlar se uma comparação que diferencia maiúsculas de minúsculas é executada.
- O valor especificado para essa condição de correspondência é comparado com o caminho relativo da solicitação exata feita pelo cliente.

- Para corresponder a todas as solicitações feitas em um diretório específico, use o [diretório de caminho da URL](#url-path-directory) ou a condição de correspondência curinga do caminho da [URL](#url-path-wildcard) .

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>Regex de caminho de URL

Compara o caminho da URL da solicitação com a [expressão regular](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)especificada.

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência Regex de caminho de URL são atendidas.

- **Corresponde**a: Requer que a solicitação contenha um caminho de URL que corresponda à expressão regular especificada.
- Não **corresponde**a: Requer que a solicitação contenha um caminho de URL que não corresponda à expressão regular especificada.

Informações da chave:

- Uma URL CNAME de borda é reescrita para uma URL CDN antes da comparação de URL.

    Por exemplo, ambas as URLs apontam para o mesmo ativo e, portanto, têm o mesmo caminho de URL.

     - URL da CDN: http\/:&lt; /WPC.0001. domínio&gt;/800001/CustomerOrigin/Path/Asset.htm

     - URL de CNAME de borda:\/http:/My.domain.com/Path/Asset.htm

    Informações adicionais:
    
     - Caminho da URL:/800001/CustomerOrigin/path/asset.htm

- As cadeias de caracteres de consulta na URL são ignoradas.
    
- Use a opção **Ignorar maiúsculas** para controlar se uma comparação que diferencia maiúsculas de minúsculas é executada.
    
- Os espaços no caminho da URL devem ser substituídos por "% 20".

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>Curinga de caminho de URL

Compara o caminho da URL relativa de uma solicitação com o padrão curinga especificado.

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência curinga de caminho de URL são atendidas.

- **Corresponde**a: Requer que a solicitação contenha um caminho de URL que corresponda ao padrão curinga especificado.
- Não **corresponde**a: Requer que a solicitação contenha um caminho de URL que não corresponda ao padrão curinga especificado.

Informações da chave:

- **Relativo à** opção: Esta opção determina se o ponto de comparação de URL começa antes ou depois do ponto de acesso de conteúdo.

   Essa opção pode ter os seguintes valores:
     - **Raiz**: Indica que o ponto de comparação de URL começa diretamente após o nome de host da CDN.

       Por exemplo: http:\//WPC.0001.&lt; &gt;**800001/myorigin/MyFolder/index.htm** de domínio/

     - **Origem**: Indica que o ponto de comparação de URL começa após o ponto de acesso de conteúdo (por exemplo,/000001 ou/800001/myorigin). Como o \*CNAME. azureedge.net é criado em relação ao diretório de origem no nome de host da CDN da Verizon por padrão, os usuários da CDN do Azure devem usar o valor de **origem** . 

       Por exemplo: https:\//&lt;Endpoint&gt;. azureedge.NET/**MyFolder/index.htm**

     Esta URL aponta para o seguinte nome de host da CDN da\/Verizon&lt; : http:/WPC.0001. /800001/myorigin/&gt;de domínio**MyFolder/index.htm**

- Uma URL CNAME de borda é reescrita para uma URL CDN antes da comparação de URL.

    Por exemplo, ambas as URLs a seguir apontam para o mesmo ativo e, portanto, têm o mesmo caminho de URL:
     - URL da CDN http://wpc.0001.&lt:;d&gt; omain/800001/CustomerOrigin/Path/Asset.htm
     - URL de CNAME de borda:\/http&gt;:/&lt;Endpoint. azureedge.net/Path/Asset.htm
    
    Informações adicionais:
    
     - Caminho da URL (relativo à raiz):/800001/CustomerOrigin/path/asset.htm
    
     - Caminho da URL (relativo à origem):/path/asset.htm
    
- Especifique vários caminhos de URL delimitando cada um deles com um único espaço.

   Por exemplo:/marketing/Asset. */Sales/*. htm

- As cadeias de caracteres de consulta na URL são ignoradas.
    
- Use a opção **Ignorar maiúsculas** para controlar se uma comparação que diferencia maiúsculas de minúsculas é executada.
    
- Substitua os espaços no caminho da URL por "% 20".
    
- O valor especificado para um caminho de URL pode aproveitar [os valores curinga](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Cada padrão de caminho de URL pode conter um ou mais asteriscos (*), onde cada asterisco pode corresponder a uma sequência de um ou mais caracteres.

#### <a name="sample-scenarios"></a>Cenários de exemplo

As configurações de exemplo na tabela a seguir pressupõem que essa condição de correspondência seja atendida quando uma solicitação corresponder ao padrão de URL especificado:

Value                   | Relativo a    | Resultado 
------------------------|----------------|-------
*/test.html */test.php  | Raiz ou origem | Esse padrão é correspondido por solicitações de ativos nomeados "Test. html" ou "Test. php" em qualquer pasta.
/80ABCD/origin/text/*   | Raiz           | Esse padrão é correspondido quando o ativo solicitado atende aos seguintes critérios: <br />-Ele deve residir em uma origem de cliente chamada "Origin". <br />-O caminho relativo deve começar com uma pasta chamada "text". Ou seja, o ativo solicitado pode residir na pasta "texto" ou em uma de suas subpastas recursivas.
*/css/* */js/*          | Raiz ou origem | Esse padrão é correspondido por todas as URLs CDN ou CNAME de borda que contêm uma pasta CSS ou js.
*.jpg *.gif *.png       | Raiz ou origem | Esse padrão é correspondido por todas as URLs CDN ou CNAME de borda que terminam com. jpg,. gif ou. png. Uma maneira alternativa de especificar esse padrão é com a [condição de correspondência de extensão de caminho de URL](#url-path-extension).
/images/*/Media/*      | Origem         | Esse padrão é correspondido pelas URLs de CDN ou de borda CNAME cujo caminho relativo começa com uma pasta "images" ou "Media". <br />-URL da CDN: http\/:&lt; /WPC.0001. /800001/myorigin/images/Sales/Event1.png&gt;de domínio<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>Literal de consulta de URL

Compara a cadeia de caracteres de consulta de uma solicitação com o valor especificado.

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência literal de consulta de URL são atendidas.

- **Corresponde**a: Requer que a solicitação contenha uma cadeia de caracteres de consulta de URL que corresponda à cadeia de caracteres de consulta especificada.
- Não **corresponde**a: Requer que a solicitação contenha uma cadeia de caracteres de consulta de URL que não corresponda à cadeia de caracteres de consulta especificada.

Informações da chave:

- Somente correspondências exatas da cadeia de caracteres de consulta atendem a essa condição de correspondência
    
- Use a opção **Ignorar caso** para controlar a diferenciação de maiúsculas e minúsculas das comparações de cadeia de caracteres de consulta.
    
- Não inclua um ponto de interrogação (?) à esquerda no texto do valor da cadeia de caracteres de consulta.
    
- Determinados caracteres exigem codificação de URL. Use o símbolo de porcentagem para codificar por URL os seguintes caracteres:

   Caráter | Codificação de URL
   ----------|---------
   Espaço     | %20
   &         | %25

- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
   - Concluir preenchimento de cache
   - Idade máxima interna padrão
   - Forçar Max-age interna
   - Ignorar não cache de origem
   - Max-obsoleto interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>Parâmetro de consulta de URL

Identifica solicitações que contêm o parâmetro de cadeia de caracteres de consulta especificado. Esse parâmetro é definido como um valor que corresponde a um padrão especificado. Os parâmetros de cadeia de caracteres de consulta (por exemplo, parâmetro = valor) na URL da solicitação determinam se essa condição é atendida. Essa condição de correspondência identifica um parâmetro de cadeia de caracteres de consulta por seu nome e aceita um ou mais valores para o valor do parâmetro. 

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência de parâmetro de consulta de URL são atendidas.

- **Corresponde**a: Requer que uma solicitação contenha o parâmetro especificado com um valor que corresponda a pelo menos um dos valores definidos nessa condição de correspondência.
- Não **corresponde**a: Requer que a solicitação atenda a qualquer um dos seguintes critérios:
  - Ele não contém o parâmetro especificado.
  - Ele contém o parâmetro especificado, mas seu valor não corresponde a nenhum dos valores definidos nessa condição de correspondência.

Essa condição de correspondência fornece uma maneira fácil de especificar combinações de nome/valor de parâmetro. Para obter mais flexibilidade se você estiver correspondendo a um parâmetro de cadeia de caracteres de consulta, considere usar a condição de correspondência [curinga de consulta de URL](#url-query-wildcard) .

Informações da chave:

- Apenas um nome de parâmetro de consulta de URL única pode ser especificado por instância dessa condição de correspondência.
    
- Como não há suporte para valores curinga quando um nome de parâmetro é especificado, somente as correspondências de nome de parâmetro exatas são elegíveis para comparação.
- [Os valores](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)de parâmetro podem incluir valor curinga.
   - Cada padrão de valor de parâmetro pode consistir em um ou mais asteriscos (*), onde cada asterisco pode corresponder a uma sequência de um ou mais caracteres.
   - Determinados caracteres exigem codificação de URL. Use o símbolo de porcentagem para codificar por URL os seguintes caracteres:

       Caráter | Codificação de URL
       ----------|---------
       Espaço     | %20
       &         | %25

- Especifique vários valores de parâmetro de cadeia de caracteres de consulta delimitando cada um deles com um único espaço. Essa condição de correspondência é atendida quando uma solicitação contém uma das combinações de nome/valor especificadas.

   - Exemplo 1:

     - Configuração:

       ValueA ValueB

     - Essa configuração corresponde aos seguintes parâmetros de cadeia de caracteres de consulta:

       Parâmetro1 = valor
    
       Parâmetro1 = valorB

   - Exemplo 2:

     - Configuração: 

        Valor% 20A valor% 20B

     - Essa configuração corresponde aos seguintes parâmetros de cadeia de caracteres de consulta:

       Parâmetro1 = valor% 20A

       Parâmetro1 = valor% 20B

- Essa condição de correspondência é atendida somente quando há uma correspondência exata com pelo menos uma das combinações de nome/valor da cadeia de caracteres de consulta especificada.

   Por exemplo, se você usar a configuração no exemplo anterior, a combinação de nome/valor de parâmetro "parâmetro1 = ValueAdd" não seria considerada uma correspondência. No entanto, se você especificar um dos seguintes valores, ele corresponderá a essa combinação de nome/valor:

   - Valor de valorB ValueAdd
   - Valor * valorB

- Use a opção **Ignorar caso** para controlar a diferenciação de maiúsculas e minúsculas das comparações de cadeia de caracteres de consulta.
    
- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
   - Concluir preenchimento de cache
   - Idade máxima interna padrão
   - Forçar Max-age interna
   - Ignorar não cache de origem
   - Max-obsoleto interno

#### <a name="sample-scenarios"></a>Cenários de exemplo

O exemplo a seguir demonstra como essa opção funciona em situações específicas:

Nome  | Valor |  Resultado
------|-------|--------
Utilizador  | Joe   | Esse padrão é correspondido quando a cadeia de caracteres de consulta para uma URL solicitada é "? User = Joe."
Utilizador  | *     | Esse padrão é correspondido quando a cadeia de caracteres de consulta para uma URL solicitada contém um parâmetro de usuário.
Email | Joe\* | Esse padrão é correspondido quando a cadeia de caracteres de consulta para uma URL solicitada contém um parâmetro de email que começa com "Joe".

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>Regex de consulta de URL

Identifica solicitações que contêm o parâmetro de cadeia de caracteres de consulta especificado. Esse parâmetro é definido como um valor que corresponde a uma [expressão regular](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)especificada.

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência Regex de consulta de URL são atendidas.

- **Corresponde**a: Requer que a solicitação contenha uma cadeia de caracteres de consulta de URL que corresponda à expressão regular especificada.
- Não **corresponde**a: Requer que a solicitação contenha uma cadeia de caracteres de consulta de URL que não corresponda à expressão regular especificada.

Informações da chave:

- Somente as correspondências exatas para a expressão regular especificada satisfazem essa condição de correspondência.
    
- Use a opção **Ignorar caso** para controlar a diferenciação de maiúsculas e minúsculas das comparações de cadeia de caracteres de consulta.
    
- Para os fins desta opção, uma cadeia de caracteres de consulta começa com o primeiro caractere após o delimitador de ponto de interrogação (?) para a cadeia de caracteres de consulta.
    
- Determinados caracteres exigem codificação de URL. Use o símbolo de porcentagem para codificar por URL os seguintes caracteres:

   Caráter | Codificação de URL | Valor
   ----------|--------------|------
   Espaço     | %20          | \%20
   &         | %25          | \%25

   Observe que os símbolos de porcentagem devem ser ignorados.

- Caracteres de expressão regular especiais de escape duplo (por exemplo \^, $. +) para incluir uma barra invertida na expressão regular.

   Por exemplo:

   Valor | Interpretado como 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
   - Concluir preenchimento de cache
   - Idade máxima interna padrão
   - Forçar Max-age interna
   - Ignorar não cache de origem
   - Max-obsoleto interno

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>Curinga de consulta de URL

Compara os valores especificados com a cadeia de caracteres de consulta da solicitação.

A opção **corresponde**/**não corresponde** determina as condições sob as quais a condição de correspondência curinga de consulta de URL são atendidas.

- **Corresponde**a: Requer que a solicitação contenha uma cadeia de caracteres de consulta de URL que corresponda ao valor curinga especificado.
- Não **corresponde**a: Requer que a solicitação contenha uma cadeia de caracteres de consulta de URL que não corresponda ao valor curinga especificado.

Informações da chave:

- Para os fins desta opção, uma cadeia de caracteres de consulta começa com o primeiro caractere após o delimitador de ponto de interrogação (?) para a cadeia de caracteres de consulta.
- Os valores de parâmetro podem incluir [valores curinga](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Cada padrão de valor de parâmetro pode consistir em um ou mais asteriscos (*), onde cada asterisco pode corresponder a uma sequência de um ou mais caracteres.
   - Determinados caracteres exigem codificação de URL. Use o símbolo de porcentagem para codificar por URL os seguintes caracteres:

     Caráter | Codificação de URL
     ----------|---------
     Espaço     | %20
     &         | %25

- Especifique vários valores delimitando cada um deles com um único espaço.

   Por exemplo: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Somente correspondências exatas para pelo menos um dos padrões de cadeia de caracteres de consulta especificados atendem a essa condição de correspondência.
    
- Use a opção **Ignorar caso** para controlar a diferenciação de maiúsculas e minúsculas das comparações de cadeia de caracteres de consulta.
    
- Devido à maneira como as configurações de cache são controladas, essa condição de correspondência é incompatível com os seguintes recursos:
   - Concluir preenchimento de cache
   - Idade máxima interna padrão
   - Forçar Max-age interna
   - Ignorar não cache de origem
   - Max-obsoleto interno

#### <a name="sample-scenarios"></a>Cenários de exemplo

O exemplo a seguir demonstra como essa opção funciona em situações específicas:

 Nome                 | Descrição
 ---------------------|------------
user=joe              | Esse padrão é correspondido quando a cadeia de caracteres de consulta para uma URL solicitada é "? User = Joe."
\*user=\* \*optout=\* | Esse padrão é correspondido quando a consulta de URL CDN contém o parâmetro User ou optou.

[Voltar ao início](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Passos Seguintes

- [Visão geral da rede de distribuição de conteúdo do Azure](cdn-overview.md)
- [Referência do motor de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Expressões condicionais do motor de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funcionalidades do motor de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Substituindo o comportamento HTTP padrão usando o mecanismo de regras](cdn-verizon-premium-rules-engine.md)
