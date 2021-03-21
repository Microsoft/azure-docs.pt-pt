---
title: Azure CDN das características do motor de regras Verizon Premium
description: Documentação de referência para Azure CDN das características do motor de regras Verizon Premium.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/02/2020
ms.author: allensu
ms.openlocfilehash: 0ea4f167b992ccfbc4156ac06c8f636d2ef4a355
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020420"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN das características do motor de regras Verizon Premium

Este artigo enumera descrições detalhadas das funcionalidades disponíveis para o Motor de [Regras](cdn-verizon-premium-rules-engine.md)da Rede de Entrega de Conteúdos Azure (CDN).

A terceira parte de uma regra é a característica. Uma característica define o tipo de ação que é aplicada ao tipo de pedido que é identificado por um conjunto de condições de jogo.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a><a name="top"></a>Azure CDN do motor de regras Verizon Premium apresenta referência

Os tipos de funcionalidades disponíveis são:

* [Acesso](#access)
* [Colocação em cache](#caching)
* [Comentário](#comment)
* [Cabeçalhos](#headers)
* [Registos](#logs)
* [Otimizar](#optimize)
* [Origem](#origin)
* [Especialidade](#specialty)
* [URL](#url)
* [Firewall de Aplicação Web](#waf)

### <a name="access"></a><a name="access"></a>Access

Estas funcionalidades são concebidas para controlar o acesso aos conteúdos.

| Name       | Objetivo                                                           |
|------------|-------------------------------------------------------------------|
| [Negar acesso (403)](https://docs.vdms.com/cdn/Content/HRE/F/Deny-Access-403.htm) | Determina se todos os pedidos são rejeitados com uma resposta 403 Proibida. |
| [Token Auth](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm) | Determina se Token-Based Autenticação será aplicada a um pedido. |
| [Código de Negação token Auth](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm) | Determina o tipo de resposta que será devolvida a um utilizador quando um pedido é negado devido a Token-Based Autenticação. |
| [Token Auth Ignore CASO URL](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm) | Determina se as comparações de URL efetuadas por Token-Based Autenticação serão sensíveis a casos. |
| [Parâmetro Token Auth](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm) | Determina se o parâmetro de cadeia de Token-Based de autenticação deve ser renomeado. |

**[De volta ao topo](#top)**

### <a name="caching"></a><a name="caching"></a>Colocação em cache

Estas funcionalidades são concebidas para personalizar quando e como o conteúdo é em cache.

| Name       | Objetivo                                                           |
|------------|-------------------------------------------------------------------|
| [Parâmetros de largura de banda](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Parameters.htm) | Determina se os parâmetros de aceleração da largura de banda (isto é, ec_rate e ec_prebuf) estarão ativos. |
| [Aceleração da largura de banda](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Throttling.htm) | Acelera a largura de banda para a resposta fornecida pelos nossos servidores de borda. |
| [Bypass Cache](https://docs.vdms.com/cdn/Content/HRE/F/Bypass-Cache.htm) | Determina se o pedido pode alavancar a nossa tecnologia de caching. |
| [Tratamento do cabeçalho de controlo de cache](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Control-Header-Treatment.htm) |  Controla a geração de cabeçalhos Cache-Control pelo servidor de bordas quando a função external Max-Age está ativa. |
| [Cadeia de consulta cache-key](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Query-String.htm) | Determina se a **chave de cache** _ incluirá ou excluirá parâmetros de cadeia de consulta associados a um pedido. <br> __ Um caminho relativo que identifica exclusivamente um ativo para o propósito de caching.  Os nossos servidores de borda usam este caminho relativo ao verificar se há conteúdo em cache.  Por predefinição, uma chave de cache não conterá a cadeia de consulta parameters._ |
| [Reescrita de chave de cache](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm) | Reescreve a chave de cache associada a um pedido. |
| [Preenchimento completo da cache](https://docs.vdms.com/cdn/Content/HRE/F/Complete-Cache-Fill.htm) | Determina o que acontece quando um pedido resulta numa falha parcial de cache num servidor de borda. |
| [Tipos de ficheiros compressivos](https://docs.vdms.com/cdn/Content/HRE/F/Compress-File-Types.htm) | Define os formatos de ficheiro que serão comprimidos no servidor. | 
| [Idade Máxima Interna padrão](https://docs.vdms.com/cdn/Content/HRE/F/Default-Internal-Max-Age.htm) | Determina o intervalo de idade máxima padrão para o servidor de borda para a revalidação do cache do servidor de origem. |
| [Expira o tratamento do cabeçalho](https://docs.vdms.com/cdn/Content/HRE/F/Expires-Header-Treatment.htm) | Controla a geração de cabeçalhos Expira por um servidor de borda quando a função external Max-Age está ativa. |
| [Max-Age externo](https://docs.vdms.com/cdn/Content/HRE/F/External-Max-Age.htm) | Determina o intervalo de idade máxima para o navegador para revalidação do cache do servidor de borda. |
| [Força Interna Max-Age](https://docs.vdms.com/cdn/Content/HRE/F/Force-Internal-Max-Age.htm) | Determina o intervalo de idade máxima para o servidor de borda para a revalidação do cache do servidor de origem. |
| [suporte h.264 (DOWNLOAD PROGRESSIVO HTTP)](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-HPD.htm) | Determina os tipos de formatos de ficheiro H.264 que podem ser usados para transmitir conteúdo. |
| [H.264 Support Video Seek Params](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-VSP-HPD.htm) | Substitui os nomes atribuídos a parâmetros que controlam a procura através de meios H.264 ao utilizar o DOWNLOAD Progressivo HTTP. |
| [Pedido de No-Cache de Honra](https://docs.vdms.com/cdn/Content/HRE/F/Honor-No-Cache-Request.htm) | Determina se os pedidos de não cache de um cliente HTTP serão reencaminhados para o servidor de origem. |
| [Ignorar Origem No-Cache](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Origin-No-Cache.htm) | Determina se o nosso CDN ignorará certas diretivas servidas a partir de um servidor de origem. |
| [Ignorar gamas insatisfadíveis](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Unsatisfiable-Ranges.htm) | Determina a resposta que será devolvida aos clientes quando um pedido gera um código de estado de alcance 416 não satisfatório. |
| [Max-Stale Interno](https://docs.vdms.com/cdn/Content/HRE/F/Internal-Max-Stale.htm) | Controla quanto tempo além do tempo normal de validade um ativo em cache pode ser servido a partir de um servidor de borda quando o servidor de borda é incapaz de revalidar o ativo em cache com o servidor de origem. |
| [Partilha parcial de Cache](https://docs.vdms.com/cdn/Content/HRE/F/Partial-Cache-Sharing.htm) | Determina se um pedido pode gerar conteúdo parcialmente em cache. |
| [Pré-triização do conteúdo em cache](https://docs.vdms.com/cdn/Content/HRE/F/Prevalidate-Cached-Content.htm) | Determina se o conteúdo em cache será elegível para revalidação antecipada antes do seu TTL expirar. |
| [Atualizar Zero-Byte cache arquivos](https://docs.vdms.com/cdn/Content/HRE/F/Refresh-Zero-Byte-Cache-Files.htm) | Determina como o pedido de um cliente HTTP para um ativo cache de 0 byte é tratado pelos nossos servidores de borda. |
| [Definir códigos de estado cacheáveis](https://docs.vdms.com/cdn/Content/HRE/F/Set-Cacheable-Status-Codes.htm) | Define o conjunto de códigos de estado que podem resultar em conteúdo em cache. |
| [Entrega de conteúdo em erro](https://docs.vdms.com/cdn/Content/HRE/F/Stale-Content-Delivery-on-Error.htm) | Determina se o conteúdo em cache expirado será entregue quando ocorrer um erro durante a revalidação da cache ou ao recuperar o conteúdo solicitado do servidor de origem do cliente. | 
| [Velho enquanto Revalidate](https://docs.vdms.com/cdn/Content/HRE/F/Stale-While-Revalidate.htm) | Melhora o desempenho permitindo que os nossos servidores de borda sirvam o cliente velho ao solicitador enquanto a revalidação ocorre. |

**[De volta ao topo](#top)**

### <a name="comment"></a><a name="comment"></a>Comentário

A função 'Comentário' permite a adição de uma nota dentro de uma regra.

**[De volta ao topo](#top)**

### <a name="headers"></a><a name="headers"></a>Cabeçalhos

Estas funcionalidades são concebidas para adicionar, modificar ou eliminar cabeçalhos do pedido ou resposta.

| Name       | Objetivo                                                           |
|------------|-------------------------------------------------------------------|
| [Cabeçalho de resposta à idade](https://docs.vdms.com/cdn/Content/HRE/F/Age-Response-Header.htm) | Determina se um cabeçalho de resposta age será incluído na resposta enviada ao solicitador. |
| [Cabeçalhos de resposta cache de depurar](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) | Determina se uma resposta pode incluir o [cabeçalho de resposta X-EC-Debug](https://docs.vdms.com/cdn/Content/Knowledge_Base/X_EC_Debug.htm) que fornece informações sobre a política de cache para o ativo solicitado. |
| [Modificar o cabeçalho de pedido do cliente](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm) | Substitui, apêndice ou elimina um cabeçalho de um pedido. |
| [Modificar o cabeçalho de resposta do cliente](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm) | Substitui, apêndice ou elimina um cabeçalho de uma resposta. |
| [Definir cabeçalho personalizado IP do cliente](https://docs.vdms.com/cdn/Content/HRE/F/Set-Client-IP-Custom-Header.htm) | Permite que o endereço IP do cliente que solicita seja adicionado ao pedido como cabeçalho de pedido personalizado. |

**[De volta ao topo](#top)**

### <a name="logs"></a><a name="logs"></a>Registos

Estas funcionalidades são concebidas para personalizar os dados armazenados em ficheiros de registo bruto.

| Name       | Objetivo                                                           |
|------------|-------------------------------------------------------------------|
| [Campo de registo personalizado 1](https://docs.vdms.com/cdn/Content/HRE/F/Custom-Log-Field-1.htm) | Determina o formato e o conteúdo que será atribuído ao campo de registo personalizado num ficheiro de registo bruto. |
| [Cadeia de consulta de registo](https://docs.vdms.com/cdn/Content/HRE/F/Log-Query-String.htm) | Determina se uma cadeia de consulta será armazenada juntamente com o URL nos registos de acesso. |

**[De volta ao topo](#top)**

### <a name="optimize"></a><a name="optimize"></a>Otimizar

Estas funcionalidades determinam se um pedido será submetido às otimizações fornecidas pelo Edge Optimizer.

| Name       | Objetivo                                                           |
|------------|-------------------------------------------------------------------|
| [Otimizador de bordas](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer.htm) | Determina se o Edge Optimizer pode ser aplicado a um pedido. |
| [Edge Optimizer – Configuração Instantânea](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer-Instantiate-Configuration.htm) | Instantiize ou ativa a configuração edge optimizer associada a um site. |

**[De volta ao topo](#top)**

### <a name="origin"></a><a name="origin"></a>Origem

Estas funcionalidades são concebidas para controlar a forma como o CDN comunica com um servidor de origem.

| Name       | Objetivo                                                           |
|------------|-------------------------------------------------------------------|
| [Pedidos máximos de Keep-Alive](https://docs.vdms.com/cdn/Content/HRE/F/Maximum-Keep-Alive-Requests.htm) | Define o número máximo de pedidos de ligação Keep-Alive antes de ser fechado. |
| [Cabeçalhos especiais proxy](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) | Define o conjunto de [cabeçalhos de pedido específicos](https://docs.vdms.com/cdn/Content/Knowledge_Base/Request-Format.htm#RequestHeaders) do CDN que serão reencaminhados de um servidor de borda para um servidor de origem. |

**[De volta ao topo](#top)**

### <a name="specialty"></a><a name="specialty"></a>Especialidade

Estas funcionalidades fornecem funcionalidade avançada que deve ser utilizada apenas por utilizadores avançados.

| Name       | Objetivo                                                           |
|------------|-------------------------------------------------------------------|
| [Métodos HTTP cacheable](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-HTTP-Methods.htm) | Determina o conjunto de métodos HTTP adicionais que podem ser em cache na nossa rede. |
| [Tamanho do corpo do pedido cacheable](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-Request-Body-Size.htm) | Define o limiar para determinar se uma resposta POST pode ser em cache. |
| [QUIC](https://docs.vdms.com/cdn/Content/HRE/F/QUIC.htm) | Determina se o cliente será informado de que o nosso serviço CDN suporta QUIC. |
| [Otimização de streaming](https://docs.vdms.com/cdn/Content/HRE/F/Streaming-Optimization.htm) | Sintoniza a sua configuração de caching para otimizar o desempenho para streams ao vivo e para reduzir a carga no servidor de origem. |
| [Variável de utilizador](https://docs.vdms.com/cdn/Content/HRE/F/User-Variable.htm) | Atribui um valor a uma variável definida pelo utilizador que é passada para a sua solução de processamento de tráfego sob medida. |

**[De volta ao topo](#top)**

### <a name="url"></a><a name="url"></a>URL

Estas funcionalidades permitem que um pedido seja redirecionado ou reescrito para um URL diferente.

| Name       | Objetivo                                                           |
|------------|-------------------------------------------------------------------|
| [Seguir redirecionamentos](https://docs.vdms.com/cdn/Content/HRE/F/Follow-Redirects.htm) | Determina se os pedidos podem ser redirecionados para o nome anfitrião definido no cabeçalho Localização devolvido por um servidor de origem do cliente. |
| [Redirecionamento de URL](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm) | Redireciona os pedidos através do cabeçalho localização. |
| [Reescrita de URL](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm) | Reescreve a URL de pedido. |

**[De volta ao topo](#top)**

### <a name="web-application-firewall"></a><a name="waf"></a>Firewall de Aplicações Web

A funcionalidade [De Firewall de Aplicação Web](https://docs.vdms.com/cdn/Content/HRE/F/Web_Application_Firewall.htm) determina se um pedido será exibido pela Web Application Firewall.

**[De volta ao topo](#top)**

Para as características mais recentes, consulte a [documentação](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions)do Verizon Rules Engine .

## <a name="next-steps"></a>Passos seguintes

- [Referência do motor de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Expressões condicionais do motor de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Condições de correspondência do motor de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Anular o comportamento http usando o motor de regras](cdn-verizon-premium-rules-engine.md)
- [Visão geral do Azure CDN](cdn-overview.md)
