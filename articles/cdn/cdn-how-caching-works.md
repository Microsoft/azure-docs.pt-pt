---
title: Como funciona o caching | Microsoft Docs
description: Caching é o processo de armazenamento de dados localmente para que os futuros pedidos para esses dados possam ser acedidos mais rapidamente.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: allensu
ms.openlocfilehash: a226682c2580a871e1b2fc4db71f369f3bcc3abb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010168"
---
# <a name="how-caching-works"></a>Como funciona a colocação em cache

Este artigo fornece uma visão geral dos conceitos gerais de caching e como [a Azure Content Delivery Network (CDN)](cdn-overview.md) utiliza o caching para melhorar o desempenho. Se quiser aprender como personalizar o comportamento de caching no seu ponto final cdn, consulte [o comportamento de caching do Control Azure CDN com regras de caching](cdn-caching-rules.md) e [controle o comportamento de caching CDN com cordas de consulta](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Introdução ao caching

Caching é o processo de armazenamento de dados localmente para que os futuros pedidos para esses dados possam ser acedidos mais rapidamente. No tipo mais comum de cache, caching de navegador web, um navegador web armazena cópias de dados estáticos localmente em um disco rígido local. Ao utilizar o caching, o navegador web pode evitar fazer várias viagens de ida e volta ao servidor e, em vez disso, aceder aos mesmos dados localmente, poupando assim tempo e recursos. O caching é adequado para a gestão local de pequenos dados estáticos, tais como imagens estáticas, ficheiros CSS e ficheiros JavaScript.

Da mesma forma, o caching é utilizado por uma rede de entrega de conteúdos em servidores de borda próximo do utilizador para evitar pedidos que viajam de volta à origem e reduzem a latência do utilizador final. Ao contrário de uma cache de navegador web, que é usada apenas para um único utilizador, o CDN tem uma cache partilhada. Numa cache partilhada pela CDN, um ficheiro que é solicitado por um utilizador pode ser acedido posteriormente por outros utilizadores, o que diminui consideravelmente o número de pedidos para o servidor de origem.

Os recursos dinâmicos que mudam frequentemente ou são exclusivos de um utilizador individual não podem ser em cache. Estes tipos de recursos, no entanto, podem tirar partido da otimização dinâmica da aceleração do site (DSA) na Rede de Entrega de Conteúdos Azure para melhorias de desempenho.

O caching pode ocorrer a vários níveis entre o servidor de origem e o utilizador final:

- Servidor Web: Utiliza uma cache partilhada (para vários utilizadores).
- Rede de entrega de conteúdos: Utiliza uma cache partilhada (para vários utilizadores).
- Fornecedor de serviços de Internet (ISP): Utiliza uma cache partilhada (para vários utilizadores).
- Web browser: Utiliza uma cache privada (para um utilizador).

Cada cache normalmente gere a sua própria frescura de recursos e executa a validação quando um ficheiro está em mau estado. Este comportamento é definido na especificação de caching HTTP, [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Frescura de recursos

Uma vez que um recurso em cache pode potencialmente estar desatualizado, ou velho (em comparação com o recurso correspondente no servidor de origem), é importante que qualquer mecanismo de cache controle quando o conteúdo é atualizado. Para poupar tempo e consumo de largura de banda, um recurso em cache não é comparado com a versão no servidor de origem sempre que é acedido. Em vez disso, enquanto um recurso em cache for considerado fresco, presume-se que seja a versão mais atual e é enviada diretamente para o cliente. Um recurso em cache é considerado fresco quando a sua idade é inferior à idade ou período definido por uma configuração de cache. Por exemplo, quando um navegador recarrega uma página web, verifica-se que cada recurso em cache no seu disco rígido é fresco e o carrega. Se o recurso não estiver fresco (velho), uma cópia atualizada é carregada a partir do servidor.

### <a name="validation"></a>Validação

Se um recurso for considerado velho, o servidor de origem é solicitado a validá-lo, isto é, determinar se os dados na cache ainda correspondem ao que está no servidor de origem. Se o ficheiro tiver sido modificado no servidor de origem, o cache atualiza a sua versão do recurso. Caso contrário, se o recurso estiver fresco, os dados são entregues diretamente a partir da cache sem validá-lo primeiro.

### <a name="cdn-caching"></a>Caching CDN

O caching é parte integrante da forma como um CDN opera para acelerar a entrega e reduzir a carga de origem para ativos estáticos, tais como imagens, fontes e vídeos. No cache cdN, os recursos estáticos são armazenados seletivamente em servidores estrategicamente posicionados que são mais locais para um utilizador e oferecem as seguintes vantagens:

- Como a maioria do tráfego web é estático (por exemplo, imagens, fontes e vídeos), o cache CDN reduz a latência da rede movendo o conteúdo para mais perto do utilizador, reduzindo assim a distância que os dados viajam.

- Ao descarregar trabalho para um CDN, o caching pode reduzir o tráfego de rede e a carga no servidor de origem. Ao fazê-lo, reduz os custos e os requisitos de recursos para a aplicação, mesmo quando há um grande número de utilizadores.

Tal como o cache é implementado num navegador web, pode controlar como o cache é realizado num CDN enviando cabeçalhos de diretiva de cache. Os cabeçalhos da diretiva cache são cabeçalhos HTTP, que são normalmente adicionados pelo servidor de origem. Embora a maioria destes cabeçalhos tenham sido originalmente projetados para abordar o cache nos navegadores de clientes, eles agora também são usados por todos os caches intermédios, como CDNs. 

Dois cabeçalhos podem ser usados para definir a frescura da cache: `Cache-Control` e `Expires` . `Cache-Control` é mais atual e tem precedência sobre `Expires` , se ambos existem. Existem também dois tipos de cabeçalhos utilizados para validação (chamados validadores): `ETag` e `Last-Modified` . `ETag` é mais atual e tem precedência sobre `Last-Modified` , se ambos são definidos.  

## <a name="cache-directive-headers"></a>Cabeçalhos da diretiva em cache

> [!IMPORTANT]
> Por padrão, um ponto final Azure CDN que é otimizado para DSA ignora cabeçalhos de diretiva cache e contorna o cache. Para **o Azure CDN Standard de Verizon** e **Azure CDN Standard a partir de** perfis Akamai, pode ajustar como um ponto final do Azure CDN trata estes cabeçalhos utilizando [regras de caching CDN](cdn-caching-rules.md) para permitir o caching. Apenas para **o Azure CDN Premium a partir de** perfis Verizon, utilize o motor de [regras](./cdn-verizon-premium-rules-engine.md) para permitir o caching.

O Azure CDN suporta os seguintes cabeçalhos de diretiva http cache, que definem a duração da cache e a partilha de cache.

**Cache-Control:**
- Introduzido em HTTP 1.1 para dar aos editores da web mais controlo sobre o seu conteúdo e para resolver as limitações do `Expires` cabeçalho.
- Substitui o `Expires` cabeçalho, se for `Cache-Control` definido.
- Quando utilizado num pedido HTTP do cliente para o CDN POP, `Cache-Control` é ignorado por todos os perfis Azure CDN, por padrão.
- Quando utilizado numa resposta HTTP do cliente ao CDN POP:
     - **Azure CDN Standard/Premium da Verizon** e **Azure CDN Standard da Microsoft** suporta todas as `Cache-Control` diretivas.
     - **A Azure CDN Standard da Akamai** suporta apenas as `Cache-Control` seguintes diretivas; todas as outras são ignoradas:
         - `max-age`: Uma cache pode armazenar o conteúdo durante o número de segundos especificados. Por exemplo, `Cache-Control: max-age=5`. A presente diretiva especifica o período máximo de tempo que o conteúdo é considerado fresco.
         - `no-cache`: Cache o conteúdo, mas valide o conteúdo sempre antes de o entregar na cache. Equivalente a `Cache-Control: max-age=0` .
         - `no-store`: Nunca cache o conteúdo. Remova o conteúdo se tiver sido armazenado anteriormente.

**Expira:**
- Cabeçalho legado introduzido em HTTP 1.0; suportado para retrocompatibilidade.
- Usa um tempo de validade baseado em data com segunda precisão. 
- Semelhante a `Cache-Control: max-age` .
- Usado quando `Cache-Control` não existe.

**Pragma:**
   - Não é honrado pela Azure CDN, por defeito.
   - Cabeçalho legado introduzido em HTTP 1.0; suportado para retrocompatibilidade.
   - Utilizado como cabeçalho de pedido de cliente com a seguinte diretiva: `no-cache` . Esta diretiva instrui o servidor a entregar uma nova versão do recurso.
   - `Pragma: no-cache` é equivalente a `Cache-Control: no-cache`.

## <a name="validators"></a>Validadores

Quando a cache está em estado de s paliação, os validadores de cache HTTP são utilizados para comparar a versão em cache de um ficheiro com a versão no servidor de origem. **O Azure CDN Standard/Premium da Verizon** suporta ambos `ETag` e `Last-Modified` validadores por padrão, enquanto **o Azure CDN Standard da Microsoft** e a **Azure CDN Standard da Akamai** suporta apenas por `Last-Modified` padrão.

**ETag:**
- **O Azure CDN Standard/Premium da Verizon** suporta `ETag` por padrão, enquanto **o Azure CDN Standard da Microsoft** e o **Azure CDN Standard da Akamai** não.
- `ETag` define uma cadeia que é única para cada ficheiro e versão de um ficheiro. Por exemplo, `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Introduzido em HTTP 1.1 e mais atual do que `Last-Modified` . Útil quando a última data modificada é difícil de determinar.
- Suporta a validação forte e a fraca validação; no entanto, a Azure CDN suporta apenas uma validação forte. Para uma validação forte, as duas representações de recursos devem ser idênticas. 
- Um cache valida um ficheiro que utiliza `ETag` enviando um `If-None-Match` cabeçalho com um ou mais `ETag` validadores no pedido. Por exemplo, `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Se a versão do servidor corresponder a um `ETag` validador na lista, envia o código de estado 304 (Não Modificado) na sua resposta. Se a versão for diferente, o servidor responde com o código de estado 200 (OK) e o recurso atualizado.

**Última modificação:**
- Para **o Azure CDN Standard/Premium apenas da Verizon,** `Last-Modified` é utilizado se não fizer parte da resposta `ETag` HTTP. 
- Especifica a data e a hora em que o servidor de origem determinou que o recurso foi modificado pela última vez. Por exemplo, `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Um cache valida um ficheiro utilizando `Last-Modified` enviando um `If-Modified-Since` cabeçalho com uma data e hora no pedido. O servidor de origem compara essa data com o `Last-Modified` cabeçalho do recurso mais recente. Se o recurso não tiver sido modificado desde o tempo especificado, o servidor devolve o código de estado 304 (Não Modificado) na sua resposta. Se o recurso tiver sido modificado, o servidor devolve o código de estado 200 (OK) e o recurso atualizado.

## <a name="determining-which-files-can-be-cached"></a>Determinando quais os ficheiros que podem ser em cache

Nem todos os recursos podem ser cached. A tabela a seguir mostra quais os recursos que podem ser cached, com base no tipo de resposta HTTP. Os recursos entregues com respostas HTTP que não satisfaçam todas estas condições não podem ser cached. Para **o Azure CDN Premium apenas da Verizon,** pode utilizar o motor de regras para personalizar algumas destas condições.

|                       | Azure CDN da Microsoft          | Azure CDN de Verizon | Azure CDN da Akamai        |
|-----------------------|-----------------------------------|------------------------|------------------------------|
| **Códigos de estado HTTP** | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| **Métodos HTTP**      | GET, CABEÇA                         | GET                    | GET                          |
| **Limites de tamanho do ficheiro**  | 300 GB                            | 300 GB                 | - Otimização geral da entrega web: 1,8 GB<br />- Otimizações de streaming de mídia: 1,8 GB<br />- Otimização de ficheiros grandes: 150 GB |

Para **o Azure CDN Standard da Microsoft** para trabalhar num recurso, o servidor de origem deve suportar quaisquer pedidos HEAD e GET HTTP e os valores de comprimento do conteúdo devem ser os mesmos para qualquer resposta HEAD e GET HTTP para o ativo. Para um pedido DE CABEÇA, o servidor de origem deve apoiar o pedido DE CABEÇA, e deve responder com os mesmos cabeçalhos como se tivesse recebido um pedido GET.

## <a name="default-caching-behavior"></a>Comportamento padrão do caching

A tabela seguinte descreve o comportamento padrão de caching para os produtos Azure CDN e suas otimizações.

|    | Microsoft: Entrega geral da web | Verizon: Entrega geral da web | Verizon: DSA | Akamai: Entrega geral da web | Akamai: DSA | Akamai: Grande download de ficheiros | Akamai: streaming de meios de comunicação geral ou VOD |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Origem de honra**       | Yes    | Yes   | No   | Yes    | No   | Yes   | Yes    |
| **Duração da cache CDN** | 2 dias |7 dias | Nenhum | 7 dias | Nenhum | 1 dia | 1 ano |

**Origem de honra**: Especifica se deve honrar os cabeçalhos de diretiva de cache suportados se existirem na resposta HTTP do servidor de origem.

**Duração da cache cdN**: Especifica a quantidade de tempo para a qual um recurso está em cache no Azure CDN. No entanto, se **a origem honor** é Sim e a resposta HTTP do servidor de origem inclui o cabeçalho da diretiva cache `Expires` ou , `Cache-Control: max-age` Azure CDN usa o valor de duração especificado pelo cabeçalho. 

## <a name="next-steps"></a>Passos seguintes

- Para aprender a personalizar e sobrepor o comportamento padrão de caching no CDN através de regras de caching, consulte o [comportamento do caching do Control Azure CDN com regras de caching](cdn-caching-rules.md). 
- Para aprender a usar cordas de consulta para controlar o comportamento do caching, consulte [o comportamento do Caching Do Control Azure CDN com cordas de consulta](cdn-query-string.md).