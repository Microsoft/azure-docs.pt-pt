---
title: Funciona como o cache | Documentos da Microsoft
description: Colocação em cache é o processo de armazenamento de dados localmente para que as solicitações futuras que os dados podem ser acedidos mais rapidamente.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: magattus
ms.openlocfilehash: f82675f1e93a5471f98c1778e9394f9eaec1a07b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60636814"
---
# <a name="how-caching-works"></a>Como funciona a colocação em cache

Este artigo fornece uma visão geral dos conceitos gerais de colocação em cache e como [rede de entrega de conteúdos (CDN)](cdn-overview.md) utiliza a colocação em cache para melhorar o desempenho. Se gostaria de saber mais sobre como personalizar o comportamento de colocação em cache no ponto final da CDN, consulte [comportamento de cache com regras de colocação em cache de CDN do Azure de controlo](cdn-caching-rules.md) e [comportamento com cadeias de consulta de cache de CDN do Azure de controle](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Introdução ao armazenamento em cache

Colocação em cache é o processo de armazenamento de dados localmente para que as solicitações futuras que os dados podem ser acedidos mais rapidamente. O tipo mais comuns de colocação em cache, cache de navegador da web, um web browser armazena cópias de dados estáticos localmente num disco rígido local. Ao utilizar a colocação em cache, o navegador da web pode evitar fazer várias viagens de ida e volta para o servidor e em vez disso, os mesmos dados localmente, poupando assim o tempo e recursos de acesso. Colocação em cache é bem adequada para gerir localmente os dados de pequenos e estáticos como imagens estáticas, arquivos CSS e arquivos JavaScript.

Da mesma forma, a colocação em cache é utilizada por uma rede de entrega de conteúdos em servidores edge próximo do utilizador para evitar pedidos em viagem volta para a origem e reduzindo a latência de utilizador final. Ao contrário de uma cache de navegador da web, que é utilizada apenas para um único utilizador, a CDN tem um cache compartilhado. Num cache CDN partilhado, um ficheiro que é solicitado por um utilizador pode ser acessado posteriormente por outros utilizadores, que reduz significativamente o número de pedidos para o servidor de origem.

Não podem ser colocado em cache recursos dinâmicos que mudam frequentemente ou que são exclusivas para um utilizador individual. Esses tipos de recursos, no entanto, podem tirar partido da Otimização do dynamic site acceleration (DSA) sobre a rede de entrega de conteúdos do Azure para melhorar o desempenho.

Colocação em cache pode ocorrer em vários níveis entre o servidor de origem e o utilizador final:

- Servidor Web: Utiliza uma cache partilhada (para vários usuários).
- Rede de entrega de conteúdos: Utiliza uma cache partilhada (para vários usuários).
- Fornecedor de serviços de Internet (ISP): Utiliza uma cache partilhada (para vários usuários).
- Navegador da Web: Utiliza uma cache privada (para um usuário).

Cada cache normalmente gere a sua própria atualização de recursos e realiza a validação, quando um ficheiro está obsoleto. Este comportamento é definido no HTTP especificação, a colocação em cache [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Atualização de recursos

Como um recurso em cache pode ser desatualizada ou obsoletos (em comparação com o recurso correspondente no servidor de origem), é importante para qualquer mecanismo de colocação em cache para controlar quando o conteúdo é atualizado. Para guardar o consumo de largura de banda e de tempo, um recurso em cache não é em comparação com a versão no servidor de origem sempre que ele é acessado. Em vez disso, desde que um recurso em cache é considerado como zero, ele é considerado como a versão mais recente e é enviado diretamente para o cliente. Um recurso em cache é considerado atualizados quando deixa a idade é inferior a idade ou período definido por uma definição de cache. Por exemplo, quando um navegador recarrega uma página da web, verifica se cada recurso em cache no disco rígido está fresco e carrega-lo. Se o recurso não é zero (obsoleto), uma cópia atualizada é carregada do servidor.

### <a name="validation"></a>Validação

Se um recurso é considerado como obsoletos, o servidor de origem é-lhe pedido para validá-la, ou seja, determinar se os dados na cache ainda corresponde ao que está no servidor de origem. Se o ficheiro foi modificado no servidor de origem, o cache atualiza a versão do recurso. Caso contrário, se o recurso for zero, os dados são entregues diretamente a partir da cache sem validá-lo pela primeira vez.

### <a name="cdn-caching"></a>Colocação em cache de CDN

Colocação em cache é parte integral da forma que funciona de uma CDN para acelerar a entrega e reduzir a carga de origem para elementos estáticos como imagens, fontes e vídeos. No cache de CDN, os recursos estáticos seletivamente estão armazenados nos servidores estrategicamente colocadas de modo que são mais locais para um usuário e oferece as seguintes vantagens:

- Porque a maioria do tráfego web forem estático (por exemplo, imagens, fontes e vídeos), colocação em cache de CDN reduz latência de rede ao mover conteúda mais próximo ao utilizador, reduzindo, assim, a distância que viaja de dados.

- Ao descarregar trabalho a uma CDN, a colocação em cache pode reduzir o tráfego de rede e a carga no servidor de origem. Esse procedimento reduz os requisitos de custos e recursos para a aplicação, mesmo quando há um grande número de utilizadores.

Semelhante a como a colocação em cache é implementada num navegador da web, pode controlar como a colocação em cache é realizada numa CDN ao enviar cabeçalhos de diretivas de cache. Cabeçalhos de diretivas de cache são cabeçalhos HTTP, que normalmente são adicionados pelo servidor de origem. Embora a maioria desses cabeçalhos foram originalmente criada para atender a colocação em cache em browsers do cliente, eles agora também são utilizados por todas as caches intermediárias, por exemplo, as CDNs. 

Dois cabeçalhos podem ser utilizados para definir a atualização de cache: `Cache-Control` e `Expires`. `Cache-Control` é mais atual e tem precedência sobre `Expires`, se ambos existem. Também existem dois tipos de cabeçalhos usados para validação (chamada de validadores): `ETag` e `Last-Modified`. `ETag` é mais atual e tem precedência sobre `Last-Modified`, se ambos estiverem definidas.  

## <a name="cache-directive-headers"></a>Cabeçalhos de diretivas de cache

> [!IMPORTANT]
> Por predefinição, um ponto de final de CDN do Azure está otimizado para o DSA ignora os cabeçalhos de diretivas de cache e ignora a colocação em cache. Para **CDN do Azure Standard da Verizon** e **CDN do Azure Standard da Akamai** perfis, pode ajustar a forma como um ponto de final de CDN do Azure trata esses cabeçalhos utilizando [regras de cache de CDN](cdn-caching-rules.md)para ativar a colocação em cache. Para **CDN do Azure Premium da Verizon** perfis, utilize o [motor de regras](cdn-rules-engine.md) para ativar a colocação em cache.

A CDN do Azure suporta os seguintes cabeçalhos de diretivas de cache HTTP, o que definem a duração da cache e compartilhamento de cache.

**Cache-Control:**
- Introduzido no HTTP 1.1 para dar mais controle sobre seu conteúdo de publicadores de web e resolver as limitações do `Expires` cabeçalho.
- Substitui o `Expires` cabeçalho, se ambas-lo e `Cache-Control` são definidos.
- Quando usado numa solicitação HTTP do cliente para o POP de CDN, `Cache-Control` é ignorada por todos os perfis de CDN do Azure, por predefinição.
- Quando usado numa resposta HTTP do cliente para o POP de CDN:
     - **CDN do Azure Standard/Premium da Verizon** e **CDN Standard do Microsoft Azure** suportam todos `Cache-Control` diretivas.
     - **CDN Standard do Azure da Akamai** suporta apenas os seguintes `Cache-Control` diretivas; todos os outros são ignorados:
         - `max-age`: Uma cache pode armazenar o conteúdo para o número de segundos especificado. Por exemplo, `Cache-Control: max-age=5`. Essa diretiva Especifica a quantidade máxima de tempo que o conteúdo é considerado como zero.
         - `no-cache`: Colocar em cache o conteúdo, mas validar o conteúdo sempre que entregar antes da cache. Equivalente a `Cache-Control: max-age=0`.
         - `no-store`: Nunca colocar em cache o conteúdo. Remova o conteúdo se ele foi anteriormente armazenado.

**Expira em:**
- Cabeçalho herdado, introduzido no HTTP 1.0; suportado para retrocompatibilidade compatibilidade.
- Utiliza uma hora de expiração baseadas em dados com precisão. 
- Semelhante à `Cache-Control: max-age`.
- Utilizado quando `Cache-Control` não existe.

**Pragma:**
   - Não liquidadas pelo CDN do Azure, por predefinição.
   - Cabeçalho herdado, introduzido no HTTP 1.0; suportado para retrocompatibilidade compatibilidade.
   - Utilizado como um cabeçalho de pedido de cliente com a seguinte diretiva: `no-cache`. Essa diretiva instrui o servidor para fornecer uma nova versão do recurso.
   - `Pragma: no-cache` é equivalente a `Cache-Control: no-cache`.

## <a name="validators"></a>Validadores

Quando o cache está parado, validadores de cache HTTP são utilizados para comparar a versão em cache de um ficheiro com a versão no servidor de origem. **CDN do Azure Standard/Premium da Verizon** suporta `ETag` e `Last-Modified` validadores por predefinição, enquanto **CDN Standard do Microsoft Azure** e **CDN do Azure Standard da Akamai** só suporta `Last-Modified` por predefinição.

**ETag:**
- **CDN do Azure Standard/Premium da Verizon** suporta `ETag` por predefinição, enquanto **CDN Standard do Microsoft Azure** e **CDN do Azure Standard da Akamai** não o fizer.
- `ETag` Define uma cadeia de caracteres que seja exclusiva para todos os ficheiros e a versão de um arquivo. Por exemplo, `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Introduzido no HTTP 1.1 e são mais atuais que `Last-Modified`. Útil quando a data da última modificação é difícil determinar.
- Suporta a validação forte e fraca validação; No entanto, a CDN do Azure suporta apenas forte validação. As representações de dois recursos tem de ser para bytes para validação forte, idênticos. 
- Uma cache valida um ficheiro que utiliza `ETag` enviando um `If-None-Match` cabeçalho com um ou mais `ETag` validadores no pedido. Por exemplo, `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Se corresponder a versão do servidor um `ETag` validador na lista, envia o código de estado 304 (não modificado) na respetiva resposta. Se a versão é diferente, o servidor responde com o código de estado 200 (OK) e o recurso atualizado.

**Last-Modified:**
- Para **do Azure CDN Standard/Premium da Verizon** apenas `Last-Modified` é utilizado se `ETag` não é parte da resposta HTTP. 
- Especifica a data e hora em que o servidor de origem tem determinou que o recurso foi modificado pela última vez. Por exemplo, `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Uma cache valida um arquivo usando `Last-Modified` enviando um `If-Modified-Since` cabeçalho com uma data e hora no pedido. O servidor de origem compara essa data com a `Last-Modified` cabeçalho do recurso mais recente. Se o recurso não foi modificado desde o período de tempo especificado, o servidor devolve o código de estado 304 (não modificado) na respetiva resposta. Se o recurso foi modificado, o servidor devolve estado 200 (OK) e o recurso atualizado de código.

## <a name="determining-which-files-can-be-cached"></a>Determinar quais arquivos podem ser colocadas em cache

Nem todos os recursos podem ser colocadas em cache. A tabela seguinte mostra que recursos podem ser colocadas em cache, com base no tipo de resposta HTTP. Recursos fornecidos com as respostas HTTP que não correspondam a todas essas condições não podem ser colocado em cache. Para **CDN do Azure Premium da Verizon** apenas, pode usar o mecanismo de regras para personalizar algumas dessas condições.

|                   | CDN do Azure da Microsoft          | CDN do Azure da Verizon | CDN do Azure da Akamai        |
|-------------------|-----------------------------------|------------------------|------------------------------|
| Códigos de estado HTTP | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| Métodos HTTP      | GET, PRINCIPAL                         | GET                    | GET                          |
| Limites de tamanho de ficheiro  | 300 GB                            | 300 GB                 | -Otimização da entrega web geral: 1.8 GB<br />-Otimizações de multimédia transmissão em fluxo: 1.8 GB<br />-Otimização de ficheiros grandes: 150 GB |

Para **CDN Standard do Microsoft Azure** colocação em cache para trabalhar num recurso, o servidor de origem tem de suportar qualquer HEAD e solicitações GET HTTP e os valores de conteúdo-comprimento tem de ser igual para todas as respostas de cabeça e GET HTTP para o elemento. Para um pedido HEAD, o servidor de origem tem de suportar o pedido HEAD e deverá responder com os mesmos cabeçalhos como se ele recebeu um pedido GET.

## <a name="default-caching-behavior"></a>Comportamento de colocação em cache predefinido

A tabela seguinte descreve o comportamento para os produtos do CDN do Azure e suas otimizações de cache padrão.

|    | Microsoft: Entrega geral Web | Verizon: Entrega geral Web | Verizon: DSA | Akamai: Entrega geral Web | Akamai: DSA | Akamai: Transferência de ficheiro grande | Akamai: geral ou VOD suporte de dados de transmissão em fluxo |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Origem de honor**       | Sim    | Sim   | Não   | Sim    | Não   | Sim   | Sim    |
| **Duração da cache CDN** | 2 dias |7 dias | Nenhuma | 7 dias | Nenhuma | 1 dia | um ano |

**Respeite origem**: Especifica se deve respeitar os cabeçalhos de diretivas de cache suportados, caso existam na resposta HTTP do servidor de origem.

**Duração da cache CDN**: Especifica a quantidade de tempo para o qual um recurso é armazenado em cache na CDN do Azure. No entanto, se **honrar origin** é Sim e a resposta HTTP do servidor de origem inclui o cabeçalho de diretivas de cache `Expires` ou `Cache-Control: max-age`, da CDN do Azure utiliza o valor de duração especificado pelo cabeçalho em vez disso. 

## <a name="next-steps"></a>Passos Seguintes

- Para saber como personalizar e substituir a predefinição de comportamento na CDN por meio de regras de colocação em cache a colocação em cache, veja [comportamento de cache com regras de colocação em cache de CDN do Azure de controle](cdn-caching-rules.md). 
- Para saber como utilizar as cadeias de caracteres de consulta ao controle de comportamento de colocação em cache, veja [comportamento com cadeias de consulta de cache de CDN do Azure de controle](cdn-query-string.md).



