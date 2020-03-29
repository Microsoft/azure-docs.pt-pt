---
title: Como funciona o cache [ Microsoft Docs
description: Caching é o processo de armazenamento de dados localmente para que futuros pedidos para esses dados possam ser acedidos mais rapidamente.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: magattus
ms.openlocfilehash: 92d93fbf9fa2f8df15acb62802d7ac53db836dc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593850"
---
# <a name="how-caching-works"></a>Como funciona a colocação em cache

Este artigo fornece uma visão geral dos conceitos gerais de cache e como a Rede de Entrega de [Conteúdos Azure (CDN)](cdn-overview.md) usa o cache para melhorar o desempenho. Se quiser aprender como personalizar o comportamento de cache no seu ponto final do CDN, consulte [control azure CDN comportamento de cache com regras](cdn-caching-rules.md) de cache e [controle de](cdn-query-string.md)comportamento de cache com cordas de consulta .

## <a name="introduction-to-caching"></a>Introdução ao cache

Caching é o processo de armazenamento de dados localmente para que futuros pedidos para esses dados possam ser acedidos mais rapidamente. No tipo mais comum de cache, o navegador web caching, um navegador web armazena cópias de dados estáticos localmente em um disco rígido local. Ao utilizar o cache, o navegador web pode evitar fazer várias viagens de ida e volta ao servidor e, em vez disso, aceder aos mesmos dados localmente, poupando assim tempo e recursos. O caching é adequado para gerir dados pequenos e estáticos locais, tais como imagens estáticas, ficheiros CSS e ficheiros JavaScript.

Da mesma forma, o cache é usado por uma rede de entrega de conteúdo em servidores de borda perto do utilizador para evitar pedidos que viajam de volta à origem e reduzir a latência do utilizador final. Ao contrário de um cache de navegador web, que é usado apenas para um único utilizador, o CDN tem uma cache partilhada. Numa cache partilhada cdN, um ficheiro que é solicitado por um utilizador pode ser acedido posteriormente por outros utilizadores, o que diminui consideravelmente o número de pedidos para o servidor de origem.

Os recursos dinâmicos que mudam frequentemente ou são exclusivos de um utilizador individual não podem ser cached. Este tipo de recursos, no entanto, podem tirar partido da otimização dinâmica da aceleração do site (DSA) na Rede de Entrega de Conteúdos Azure para melhorias de desempenho.

O caching pode ocorrer em vários níveis entre o servidor de origem e o utilizador final:

- Servidor web: Utiliza uma cache partilhada (para vários utilizadores).
- Rede de entrega de conteúdos: Utiliza uma cache partilhada (para vários utilizadores).
- Fornecedor de serviços de Internet (ISP): Utiliza uma cache partilhada (para vários utilizadores).
- Navegador web: Utiliza uma cache privada (para um utilizador).

Cada cache normalmente gere a sua própria frescura de recursos e executa validação quando um ficheiro é velho. Este comportamento é definido na especificação de cache HTTP, [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Frescura de recursos

Uma vez que um recurso em cache pode estar potencialmente desatualizado, ou velho (em comparação com o recurso correspondente no servidor de origem), é importante que qualquer mecanismo de cache controle quando o conteúdo é atualizado. Para poupar tempo e consumo de largura de banda, um recurso em cache não é comparado com a versão no servidor de origem sempre que é acedido. Em vez disso, enquanto um recurso em cache for considerado fresco, presume-se que seja a versão mais atual e é enviado diretamente para o cliente. Considera-se que um recurso em cache é fresco quando a sua idade é inferior à idade ou período definido por uma definição de cache. Por exemplo, quando um navegador recarrega uma página web, verifica que cada recurso em cache no seu disco rígido é fresco e carrega-o. Se o recurso não estiver fresco (velho), uma cópia atualizada é carregada a partir do servidor.

### <a name="validation"></a>Validação

Se um recurso for considerado estando velho, o servidor de origem é solicitado a validá-lo, isto é, determinar se os dados na cache ainda correspondem ao que está no servidor de origem. Se o ficheiro tiver sido modificado no servidor de origem, o cache atualiza a sua versão do recurso. Caso contrário, se o recurso estiver fresco, os dados são entregues diretamente a partir da cache sem validá-lo primeiro.

### <a name="cdn-caching"></a>CDN cache

O caching é parte integrante da forma como um CDN opera para acelerar a entrega e reduzir a carga de origem para ativos estáticos, como imagens, fontes e vídeos. No caching cDN, os recursos estáticos são armazenados seletivamente em servidores estrategicamente posicionados que são mais locais para um utilizador e oferece as seguintes vantagens:

- Como a maioria do tráfego web é estática (por exemplo, imagens, fontes e vídeos), o cachecto cDN reduz a latência da rede movendo os conteúdos mais próximos do utilizador, reduzindo assim a distância que os dados viajam.

- Ao descarregar trabalho para um CDN, o cache pode reduzir o tráfego de rede e a carga no servidor de origem. Ao fazê-lo reduz os requisitos de custos e recursos para a aplicação, mesmo quando há um grande número de utilizadores.

Semelhante à forma como o cacheching é implementado num navegador web, você pode controlar como o cacheching é realizado em um CDN enviando cabeçalhos de cache-directiva. Os cabeçalhos da diretiva cache são cabeçalhos HTTP, que são normalmente adicionados pelo servidor de origem. Embora a maioria destes cabeçalhos tenham sido originalmente projetados para lidar com o cache nos navegadores de clientes, eles agora também são usados por todos os caches intermédios, como CDNs. 

Dois cabeçalhos podem ser usados `Cache-Control` `Expires`para definir a frescura da cache: e . `Cache-Control`é mais atual e `Expires`tem precedência sobre, se ambos existirem. Existem também dois tipos de cabeçalhos utilizados `ETag` `Last-Modified`para validação (chamados validadores): e . `ETag`é mais atual e `Last-Modified`tem precedência sobre, se ambos forem definidos.  

## <a name="cache-directive-headers"></a>Cabeçalhos de diretiva cache

> [!IMPORTANT]
> Por padrão, um ponto final de CDN Azure que é otimizado para a DSA ignora cabeçalhos de cache-directiva e contorna o cache.o cache. Para **o Azure CDN Standard da Verizon** e **azure CDN Standard a partir de** perfis Akamai, pode ajustar como um ponto final azure CDN trata estes cabeçalhos usando regras de cache [CDN](cdn-caching-rules.md) para permitir o cache. Para **o Azure CDN Premium apenas a partir dos** perfis da Verizon, utiliza o motor de [regras](cdn-rules-engine.md) para permitir o cache.

O Azure CDN suporta os seguintes cabeçalhos http cache-directive, que definem a duração do cache e a partilha de cache.

**Cache-Control:**
- Introduzido em HTTP 1.1 para dar aos editores da Web mais `Expires` controlo sobre o seu conteúdo e para resolver as limitações do cabeçalho.
- Sobrepõe-se ao `Expires` cabeceamento, `Cache-Control` se ambos e são definidos.
- Quando utilizado num pedido http do cliente para `Cache-Control` o CDN POP, é ignorado por todos os perfis De CDN Azure, por padrão.
- Quando utilizado numa resposta http do cliente ao CDN POP:
     - **A Azure CDN Standard/Premium da Verizon** e **da Azure CDN Standard da Microsoft** suportam todas as `Cache-Control` diretivas.
     - **A Norma Azure CDN da** `Cache-Control` Akamai apoia apenas as seguintes diretivas; todos os outros são ignorados:
         - `max-age`: Uma cache pode armazenar o conteúdo durante o número de segundos especificados. Por exemplo, `Cache-Control: max-age=5`. Esta diretiva especifica o tempo máximo que o conteúdo considera ser fresco.
         - `no-cache`: Cache o conteúdo, mas valide o conteúdo sempre antes de o entregar a partir da cache. Equivalente `Cache-Control: max-age=0`a .
         - `no-store`: Nunca cache o conteúdo. Remova o conteúdo se tiver sido previamente armazenado.

**Expira:**
- Cabeçalho legado introduzido em HTTP 1.0; suportado para retroceder compatibilidade.
- Usa um tempo de validade baseado em data com segunda precisão. 
- Semelhante `Cache-Control: max-age`a .
- Usado `Cache-Control` quando não existe.

**Pragma:**
   - Não foi homenageado pela Azure CDN, por defeito.
   - Cabeçalho legado introduzido em HTTP 1.0; suportado para retroceder compatibilidade.
   - Utilizado como cabeçalho de pedido `no-cache`de cliente com a seguinte diretiva: . Esta diretiva instrui o servidor a entregar uma nova versão do recurso.
   - `Pragma: no-cache`é equivalente `Cache-Control: no-cache`a .

## <a name="validators"></a>Validadores

Quando a cache está velha, os validadores de cache HTTP são usados para comparar a versão em cache de um ficheiro com a versão no servidor de origem. **O Azure CDN Standard/Premium** `ETag` da `Last-Modified` Verizon suporta tanto como os validadores por padrão, enquanto o **Azure CDN Standard da Microsoft** e **o Azure CDN Standard da Akamai** suporta apenas `Last-Modified` por defeito.

**ETag:**
- **O Azure CDN Standard/Premium da Verizon** suporta `ETag` por padrão, enquanto o **Azure CDN Standard da Microsoft** e o **Azure CDN Standard da Akamai** não o fazem.
- `ETag`define uma cadeia que é única para cada ficheiro e versão de um ficheiro. Por exemplo, `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Introduzido em HTTP 1.1 e `Last-Modified`é mais atual do que . Útil quando a última data modificada é difícil de determinar.
- Suporta validação forte e validação fraca; no entanto, o Azure CDN suporta apenas uma validação forte. Para uma validação forte, as duas representações de recursos devem ser idênticas. 
- Uma cache valida um `ETag` ficheiro que `If-None-Match` utiliza enviando `ETag` um cabeçalho com um ou mais validadores no pedido. Por exemplo, `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Se a versão do `ETag` servidor corresponder a um validador da lista, envia o código de estado 304 (não modificado) na sua resposta. Se a versão for diferente, o servidor responde com o código de estado 200 (OK) e o recurso atualizado.

**Última Modificação:**
- Para **azure CDN Standard/Premium apenas da Verizon,** `Last-Modified` é usado se `ETag` não fizer parte da resposta HTTP. 
- Especifica a data e a hora em que o servidor de origem determinou que o recurso foi modificado pela última vez. Por exemplo, `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Uma cache valida um `Last-Modified` ficheiro `If-Modified-Since` utilizando enviando um cabeçalho com uma data e hora no pedido. O servidor de origem compara essa data com o `Last-Modified` cabeçalho do recurso mais recente. Se o recurso não tiver sido modificado desde o tempo especificado, o servidor devolve o código de estado 304 (não modificado) na sua resposta. Se o recurso tiver sido modificado, o servidor devolve o código de estado 200 (OK) e o recurso atualizado.

## <a name="determining-which-files-can-be-cached"></a>Determinar quais ficheiros podem ser cache

Nem todos os recursos podem ser emcache. A tabela que se segue mostra quais os recursos que podem ser emcache, com base no tipo de resposta HTTP. Os recursos entregues com respostas HTTP que não satisfazem todas estas condições não podem ser protegidos. Para **o Azure CDN Premium apenas da Verizon,** pode utilizar o motor de regras para personalizar algumas destas condições.

|                   | Azure CDN da Microsoft          | Azure CDN de Verizon | Azure CDN da Akamai        |
|-------------------|-----------------------------------|------------------------|------------------------------|
| Códigos de estado HTTP | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| Métodos HTTP      | GET, CABEÇA                         | GET                    | GET                          |
| Limites de tamanho de ficheiro  | 300 GB                            | 300 GB                 | - Otimização geral da entrega web: 1.8 GB<br />- Otimizações de streaming de mídia: 1.8 GB<br />- Grande otimização de ficheiros: 150 GB |

Para que o **Azure CDN Standard da Microsoft** funcione num recurso, o servidor de origem deve suportar quaisquer pedidos HEAD e GET HTTP e os valores de comprimento de conteúdo devem ser os mesmos para qualquer resposta HEAD e GET HTTP para o ativo. Para um pedido HEAD, o servidor de origem deve suportar o pedido HEAD, e deve responder com os mesmos cabeçalhos como se tivesse recebido um pedido GET.

## <a name="default-caching-behavior"></a>Comportamento de cache padrão

A tabela seguinte descreve o comportamento predefinido para os produtos Azure CDN e as suas otimizações.

|    | Microsoft: Entrega geral da web | Verizon: Entrega geral da web | Verizon: DSA | Akamai: Entrega geral da web | Akamai: DSA | Akamai: Grande download de ficheiros | Akamai: transmissão geral ou vod media |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Origem de honra**       | Sim    | Sim   | Não   | Sim    | Não   | Sim   | Sim    |
| **Duração da cache do CDN** | 2 dias |7 dias | Nenhuma | 7 dias | Nenhuma | 1 dia | 1 ano |

**Origem de honra**: Especifica se deve honrar os cabeçalhos de cache-directiva suportados se existirem na resposta HTTP do servidor de origem.

**Duração da cache CDN**: Especifica a quantidade de tempo para a qual um recurso é emcache no CDN Azure. No entanto, se a **origem honor** é Sim e a `Expires` `Cache-Control: max-age`resposta HTTP do servidor de origem inclui o cabeçalho da diretiva cache ou , O CDN Azure usa o valor de duração especificado pelo cabeçalho. 

## <a name="next-steps"></a>Passos seguintes

- Para aprender a personalizar e anular o comportamento padrão de cache no CDN através de regras de cache, consulte control e control [e cDN comportamento de cache com regras](cdn-caching-rules.md)de cache . 
- Para aprender a usar cordas de consulta para controlar o comportamento do cache, consulte controle de comportamento de cache do [Control Azure CDN com cordas](cdn-query-string.md)de consulta .



