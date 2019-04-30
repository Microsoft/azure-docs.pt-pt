---
title: Serviço de porta de entrada do Azure - colocação em cache | Documentos da Microsoft
description: Este artigo ajuda-o a compreender como o serviço de porta de entrada do Azure monitoriza o estado de funcionamento do seu back-ends
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: fb00c09680c6a80bc093c6f02ccd8d7269b8748c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306143"
---
# <a name="caching-with-azure-front-door-service"></a>Colocação em cache com o serviço de porta de entrada do Azure
O documento seguinte especifica o comportamento de porta de entrada com regras de encaminhamento que tem ativado a colocação em cache.

## <a name="delivery-of-large-files"></a>Entrega de ficheiros grandes
O serviço de porta de entrada do Azure fornece ficheiros grandes sem um limite no tamanho do ficheiro. Porta de entrada usa uma técnica chamada segmentação do objeto. Quando é pedido um ficheiro grande, o Front Door obtém pequenas partes do ficheiro a partir do back-end. Depois de receber um pedido de ficheiro completo ou de intervalo de bytes, um ambiente do Front Door pede o ficheiro ao back-end em segmentos de 8 MB.

</br>Depois do bloco chega ao ambiente de porta de entrada, é colocado em cache e servido imediatamente para o utilizador. Em seguida, a porta da frente pré-busca o próximo bloco em paralelo. Este obtenção prévia garante que o conteúdo permanece um trecho de um passo à frente do utilizador, o que reduz a latência. Este processo continua até que todo o ficheiro é transferido (se requerido), todos os intervalos de byte estão disponíveis (se requerido), ou o cliente encerra a conexão.

</br>Para obter mais informações sobre a solicitação de intervalo de bytes, leia [RFC 7233](https://web.archive.org/web/20171009165003/ http://www.rfc-base.org/rfc-7233.html).
Porta de entrada armazena em cache quaisquer partes à medida que estão a receber e para que todo o arquivo não precisa ser colocado em cache na cache de porta de entrada. Pedidos subsequentes para os intervalos de bytes ou ficheiro são servidos a partir da cache. Se não todos os segmentos são colocadas em cache, obtenção prévia é utilizado para pedir a segmentos de back-end. Essa otimização baseia-se à capacidade de back-end para suportar pedidos de intervalo de bytes; Se o back-end não suportar pedidos de intervalo de bytes, essa otimização não é eficaz.

## <a name="file-compression"></a>Compressão de ficheiros
Porta de entrada pode dinamicamente compactar conteúdo no limite, resultando numa resposta menor e mais rápida aos seus clientes. Todos os ficheiros são elegíveis para compressão. No entanto, um ficheiro tem de ser de um tipo MIME ou elegível para a lista de compressão. Atualmente, a porta de entrada não permite esta lista para ser alterada. A lista atual é:</br>
- "aplicação/eot"
- "application/font"
- "application/font-sfnt"
- "application/javascript"
- "application/json"
- "aplicação/opentype"
- "application/otf"
- "aplicação/pkcs7-mime"
- "aplicação/truetype"
- "aplicação/ttf",
- "aplicação/vnd.ms-fontobject"
- "application/xhtml+xml"
- "application/xml"
- "application/xml+rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-ttf"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- "application/x-opentype"
- "application/x-otf"
- "application/x-perl"
- "application/x-ttf"
- "application/x-javascript"
- "tipo de letra/eot"
- "font/ttf"
- "font/otf"
- "tipo de letra/opentype"
- "image/svg+xml"
- "text/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "text/js", "text/plain"
- "text/richtext"
- "text/tab-separated-values"
- "text/xml"
- "text/x-script"
- "text/x-component"
- "text/x-java-source"

Além disso, o ficheiro também tem de ser entre 1 KB e 8 MB de tamanho, inclusive.

Estes perfis suportam as seguintes codificações de compressão:
- [gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Se um pedido de suporte gzip e compressão de Brotli, compressão de Brotli tem precedência.</br>
Quando um pedido para um recurso Especifica a compactação e os resultados do pedido numa falha de acerto na cache, porta da frente executa compressão do elemento de diretamente no servidor POP. Depois disso, o ficheiro comprimido é servido a partir da cache. O item resultante é devolvido com uma codificação de transferência: segmentado.

## <a name="query-string-behavior"></a>Comportamento de cadeia de caracteres de consulta
Com a porta de entrada, pode controlar como os ficheiros são colocados em cache para um pedido web que contém uma cadeia de consulta. Numa solicitação da web com uma cadeia de consulta, a cadeia de consulta é essa parte do pedido que ocorre depois de um ponto de interrogação (?). Uma cadeia de consulta pode conter um ou mais pares de chave-valor, em que o nome do campo e o respetivo valor são separados por um sinal de igual (=). Cada par de chave-valor é separado por um e comercial (&). Por exemplo, http://www.contoso.com/content.mov?field1=value1&field2=value2. Se existir mais do que um par de chave-valor numa cadeia de consulta de um pedido, a ordem não importa.
- **Ignorar cadeias de consulta**: Modo predefinido. Neste modo, a porta da frente transmite as cadeias de caracteres de consulta do requerente para o back-end na primeira solicitação e armazena em cache o recurso. Todas as solicitações subseqüentes para o elemento atendidos a partir do ambiente de porta de entrada ignoram as cadeias de caracteres de consulta até que o elemento em cache expire.

- **Colocar em cache todos os URL exclusivos**: Neste modo, cada solicitação com uma URL exclusiva, incluindo a cadeia de consulta é tratada como um recurso exclusivo com seu próprio cache. Por exemplo, a resposta do back-end de um pedido para `www.example.ashx?q=test1` é colocado em cache no ambiente de porta de entrada e retornada para caches subsequentes com a mesma cadeia de consulta. Um pedido para `www.example.ashx?q=test2` é colocado em cache como um recurso separado com sua própria definição de tempo de vida.

## <a name="cache-purge"></a>Remoção de cache
Porta de entrada será cache ativos até que expire time-to-live (TTL) o elemento. Depois de TTL o elemento expira, quando um cliente solicita o ativo, o ambiente de porta da frente irá obter uma nova cópia atualizada do recurso para atender à solicitação do cliente e store atualizar a cache.
</br>A melhor prática para se certificar de que os utilizadores obtêm sempre a cópia mais recente dos seus bens é para a versão seus ativos de cada atualização e publique-os como novos URLs. Porta de entrada imediatamente recuperará os novos recursos das próximas solicitações de cliente. Por vezes, pode pretender remover o conteúdo em cache de todos os nós de extremidade e forçá-los todos para obter os novos recursos atualizados. Isto pode dever-se a atualizações à sua aplicação web ou a rapidamente os recursos de atualização que contêm informações incorretas.

</br>Selecione quais recursos de que pretende remover a partir de nós de extremidade. Se quiser limpar todos os recursos, clique a remoção de caixa de verificação de todos os. Caso contrário, escreva o caminho de cada ativo a limpar a caixa de texto do caminho. Abaixo formatos são suportadas no caminho.
1. **Remoção de URL única**: Remover recurso individual, especificando o URL completo, com a extensão de arquivo, por exemplo, /pictures/strasbourg.png;
2. **Remoção de carateres universais**: Asterisco (\*) pode ser utilizada como um caráter universal. Remover todas as pastas, subpastas e arquivos num ponto final com /\* o caminho ou a remoção de todas as subpastas e ficheiros numa pasta específica, especificando a pasta seguido /\*, por exemplo, /imagens/\*.
3. **Remoção de domínio de raiz**: Remover a raiz do ponto de extremidade com "/" no caminho.

Limpezas de cache no desde início diferenciam maiúsculas de minúsculas. Além disso, eles são agnósticos relativamente da cadeia de caracteres de consulta, que significa que a remoção de um URL irá remover todas as variações de cadeia de consulta do mesmo. 

## <a name="cache-expiration"></a>Expiração do cache
Pela seguinte ordem dos cabeçalhos é utilizada para determinar quanto tempo um item será armazenado na nossa cache:</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: maxage=\<seconds>
3. Expira: \<http-date >

Cabeçalhos de resposta de Cache-Control que indicam que a resposta não ser colocado em cache, como Cache-Control: privada, Cache-Control: não-cache e Cache-Control: loja não são honradas. No entanto, se existirem várias solicitações em andamento num preenchimento para o mesmo URL, pode partilhar a resposta. Se houver sem Cache-Control o comportamento padrão é que AFD colocarão em cache o recurso para X período de tempo em que os X é escolhido aleatoriamente entre 1 a 3 dias.


## <a name="request-headers"></a>Cabeçalhos do pedido

Os seguintes cabeçalhos de pedido não serão reencaminhados para um back-end ao utilizar a colocação em cache.
- Autorização
- Content-Length
- Codificação de transferência

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
