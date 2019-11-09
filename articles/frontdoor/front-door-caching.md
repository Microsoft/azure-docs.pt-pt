---
title: Serviço de porta frontal do Azure-Caching | Microsoft Docs
description: Este artigo ajuda você a entender como o serviço de porta frontal do Azure monitora a integridade dos back-ends
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
ms.openlocfilehash: 70ee0af0b39e80aa90d143303b3c522fbb3cc780
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839223"
---
# <a name="caching-with-azure-front-door-service"></a>Caching com o serviço de porta frontal do Azure
O documento a seguir especifica o comportamento da porta frontal com as regras de roteamento que habilitaram o Caching.

## <a name="delivery-of-large-files"></a>Entrega de arquivos grandes
O serviço de porta frontal do Azure fornece arquivos grandes sem limite de tamanho de arquivo. A porta frontal usa uma técnica chamada agrupamento de objetos. Quando é pedido um ficheiro grande, o Front Door obtém pequenas partes do ficheiro a partir do back-end. Depois de receber um pedido de ficheiro completo ou de intervalo de bytes, um ambiente do Front Door pede o ficheiro ao back-end em segmentos de 8 MB.

</br>Depois que a parte chega ao ambiente de porta frontal, ela é armazenada em cache e imediatamente fornecida ao usuário. A porta frontal, em seguida, busca previamente a próxima parte em paralelo. Essa pré-busca garante que o conteúdo permaneça uma parte à frente do usuário, o que reduz a latência. Esse processo continua até que todo o arquivo seja baixado (se solicitado), todos os intervalos de bytes estão disponíveis (se solicitado) ou o cliente encerra a conexão.

</br>Para obter mais informações sobre a solicitação de intervalo de bytes, leia [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
A porta frontal armazena em cache todas as partes conforme elas são recebidas e, portanto, o arquivo inteiro não precisa ser armazenado em cache no cache da porta frontal. As solicitações subsequentes para os intervalos de arquivo ou de bytes são servidas do cache. Se nem todas as partes forem armazenadas em cache, a busca prévia será usada para solicitar partes do back-end. Essa otimização depende da capacidade do back-end de dar suporte a solicitações de intervalo de bytes; Se o back-end não der suporte a solicitações de intervalo de bytes, essa otimização não será eficaz.

## <a name="file-compression"></a>Compressão de ficheiros
A porta frontal pode compactar dinamicamente o conteúdo na borda, resultando em uma resposta menor e mais rápida para seus clientes. Todos os arquivos são elegíveis para compactação. No entanto, um arquivo deve ser de um tipo MIME qualificado para a lista de compactação. Atualmente, a porta da frente não permite que essa lista seja alterada. A lista atual é:</br>
- "Application/EOT"
- "aplicativo/fonte"
- "aplicativo/fonte-sfnt"
- "aplicativo/JavaScript"
- "Application/JSON"
- "aplicativo/OpenType"
- "Application/OTF"
- "Application/PKCS7-MIME"
- "aplicativo/TrueType"
- "Application/ttf",
- "application/vnd. ms-fontobject"
- "Application/XHTML + XML"
- "application/xml"
- "Application/XML + RSS"
- "aplicativo/x-fonte-OpenType"
- "application/x-Font-TrueType"
- "application/x-Font-ttf"
- "application/x-httpd-CGI"
- "application/x-mpegurl"
- "application/x-OpenType"
- "application/x-OTF"
- "application/x-perl"
- "application/x-ttf"
- "application/x-JavaScript"
- "font/EOT"
- "font/ttf"
- "font/OTF"
- "fonte/OpenType"
- "Image/SVG + XML"
- "texto/CSS"
- "texto/CSV"
- "texto/HTML"
- "texto/JavaScript"
- "Text/js", "text/plain"
- "Text/RichText"
- "valores separados por texto/Tabulação"
- "text/xml"
- "Text/x-script"
- "Text/x-component"
- "Text/x-Java-Source"

Além disso, o arquivo também deve ter entre 1 KB e 8 MB de tamanho, inclusive.

Esses perfis dão suporte às seguintes codificações de compactação:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Se uma solicitação oferecer suporte à compactação Gzip e Brotli, a compactação Brotli terá precedência.</br>
Quando uma solicitação de um ativo especifica a compactação e a solicitação resulta em um erro de cache, a porta frontal executa a compactação do ativo diretamente no servidor POP. Depois disso, o arquivo compactado é servido do cache. O item resultante é retornado com uma codificação de transferência: em partes.

## <a name="query-string-behavior"></a>Comportamento da cadeia de caracteres de consulta
Com a porta frontal, você pode controlar como os arquivos são armazenados em cache para uma solicitação da Web que contém uma cadeia de caracteres de consulta. Em uma solicitação da Web com uma cadeia de caracteres de consulta, a cadeia de caracteres de consulta é aquela parte da solicitação que ocorre após um ponto de interrogação (?). Uma cadeia de caracteres de consulta pode conter um ou mais pares chave-valor, nos quais o nome do campo e seu valor são separados por um sinal de igual (=). Cada par chave-valor é separado por um e comercial (&). Por exemplo, `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Se houver mais de um par chave-valor em uma cadeia de caracteres de consulta de uma solicitação, sua ordem não importa.
- **Ignorar cadeias de caracteres de consulta**: modo padrão. Nesse modo, a porta frontal passa as cadeias de caracteres de consulta do solicitante para o back-end na primeira solicitação e armazena o ativo em cache. Todas as solicitações subsequentes para o ativo que são atendidas do ambiente de porta frontal ignoram as cadeias de caracteres de consulta até que o ativo em cache expire.

- **Armazenar em cache cada URL exclusiva**: nesse modo, cada solicitação com uma URL exclusiva, incluindo a cadeia de caracteres de consulta, é tratada como um ativo exclusivo com seu próprio cache. Por exemplo, a resposta do back-end para uma solicitação de `www.example.ashx?q=test1` é armazenada em cache no ambiente de porta frontal e retornada para caches subsequentes com a mesma cadeia de caracteres de consulta. Uma solicitação de `www.example.ashx?q=test2` é armazenada em cache como um ativo separado com sua própria configuração de vida útil.

## <a name="cache-purge"></a>Limpeza de cache
A porta frontal armazenará ativos em cache até que a TTL (vida útil) do ativo expire. Depois que a TTL do ativo expira, quando um cliente solicita o ativo, o ambiente de porta de front-end recupera uma nova cópia atualizada do ativo para atender à solicitação do cliente e armazenar a atualização do cache.
</br>A prática recomendada para garantir que os usuários sempre obtenham a cópia mais recente de seus ativos é fazer a versão de seus ativos para cada atualização e publicá-los como novas URLs. A porta frontal recuperará imediatamente os novos ativos para as próximas solicitações de cliente. Às vezes, você pode desejar limpar o conteúdo em cache de todos os nós de borda e forçá-los a recuperar novos ativos atualizados. Isso pode ser devido a atualizações em seu aplicativo Web ou para atualizar ativos rapidamente que contenham informações incorretas.

</br>Selecione quais ativos você deseja limpar dos nós de borda. Se você quiser limpar todos os ativos, clique na caixa de seleção limpar tudo. Caso contrário, digite o caminho de cada ativo que você deseja limpar na caixa de texto caminho. Os formatos a seguir têm suporte no caminho.
1. **Limpeza de URL única**: Limpe o ativo individual ESPECIFICANDO a URL completa, com a extensão de arquivo, por exemplo,/Pictures/Strasbourg.png;
2. **Limpeza de curinga**: asterisco (\*) pode ser usado como um curinga. Limpe todas as pastas, subpastas e arquivos em um ponto de extremidade com/\* no caminho ou limpe todas as subpastas e arquivos em uma pasta específica, especificando a pasta seguida por/\*, por exemplo,/Pictures/\*.
3. **Limpeza de domínio raiz**: Limpe a raiz do ponto de extremidade com "/" no caminho.

As limpezas de cache na porta de frente não diferenciam maiúsculas de minúsculas. Além disso, eles são independentes de cadeia de caracteres de consulta, o que significa que a limpeza de uma URL limpará todas as variações de cadeia de caracteres de consulta. 

## <a name="cache-expiration"></a>Expiração do cache
A ordem de cabeçalhos a seguir é usada para determinar por quanto tempo um item será armazenado em nosso cache:</br>
1. Cache-Control: s-maxage =\<segundos >
2. Cache-Control: Max-age =\<segundos >
3. Expira em: \<http-Date >

Cabeçalhos de resposta de controle de cache que indicam que a resposta não será armazenada em cache, como Cache-Control: privado, Cache-Control: no-cache e Cache-Control: no-Store são respeitados. No entanto, se houver várias solicitações em andamento em um POP para a mesma URL, elas poderão compartilhar a resposta. Se nenhum controle de cache estiver presente, o comportamento padrão é que AFD armazenará em cache o recurso por X quantidade de tempo em que X é separado aleatoriamente entre 1 e 3 dias.


## <a name="request-headers"></a>Cabeçalhos de solicitação

Os cabeçalhos de solicitação a seguir não serão encaminhados para um back-end ao usar o Caching.
- Autorização
- Comprimento do conteúdo
- Codificação de transferência

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
