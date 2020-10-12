---
title: Relatórios Core de Verizon ! Microsoft Docs
description: Saiba como aceder e ver Os Relatórios Core da Verizon através do portal Manage para perfis Verizon.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5353fa70be4705b28dab9350fcdf7819b22faf54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985536"
---
# <a name="core-reports-from-verizon"></a>Relatórios de Núcleos da Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Ao utilizar os Relatórios Core verizon através do portal Manage para perfis Verizon, pode visualizar padrões de utilização para o seu CDN com os seguintes relatórios:

* Largura de banda
* Dados transferidos
* Acertos
* Estatuto cache
* Taxa de acerto na cache
* Dados IPV4/IPV6 transferidos

## <a name="accessing-verizon-core-reports"></a>Aceder aos relatórios do núcleo de Verizon
1. A partir da lâmina de perfil CDN, clique no botão **Gerir.**
   
    ![Botão de gestão de perfil CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    O portal de gestão cdn abre.
2. Pairar sobre o **separador Analytics,** em seguida, pairar sobre o **voo do Core Reports.** Clique num relatório no menu.
   
    ![Portal de gestão CDN - Menu Core Reports](./media/cdn-reports/cdn-core-reports.png)

3. Para cada relatório, selecione uma gama de datas da lista **de gama de datas.** Pode selecionar uma gama de datas pré-definida, como **hoje** ou **esta semana,** ou pode selecionar **Personalizado** e introduzir manualmente um intervalo de datas clicando nos ícones do calendário. 

4. Depois de ter selecionado um intervalo de data, clique em **Go** para gerar o relatório. 

4. Se pretender exportar os dados no formato Excel, clique no ícone Excel acima do botão **Go.**

## <a name="bandwidth"></a>Largura de banda
O relatório de largura de banda consiste num gráfico e numa tabela de dados que indica a utilização da largura de banda cdn para HTTP e HTTPS durante um determinado período de tempo, em Mbps. Pode ver o uso da largura de banda em todos os POPs ou para um POP em particular. Este relatório permite-lhe visualizar os picos de tráfego e distribuição de POPs.

Na lista **de Nodes de Borda,** selecione **Todos os Nós de Borda** para ver o tráfego de todos os nós ou selecione uma região específica.

O relatório é atualizado a cada cinco minutos.

![Relatório de largura de banda](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Dados transferidos
Este relatório consiste num gráfico e numa tabela de dados que indica a utilização do tráfego de CDN para HTTP e HTTPS durante um determinado período de tempo, em GB. Pode ver o uso do tráfego em todos os POPs ou para um POP em particular. Este relatório permite-lhe visualizar os picos de tráfego e distribuição através de POPs.

Na lista **de Nodes de Borda,** selecione **Todos os Nós de Borda** para ver o tráfego de todos os nós ou selecione uma região específica.

O relatório é atualizado a cada cinco minutos.

![Relatório de transferência de dados](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Acessos (códigos de estado)
Este relatório descreve a distribuição de códigos de estado do pedido para o seu conteúdo. Cada pedido de conteúdo gera um código de estado HTTP. O código de estado descreve como os POPs de borda manuseavam o pedido. Por exemplo, um código de estado de 2xx indica que o pedido foi servido com sucesso a um cliente, enquanto um código de estado 4xx indica que ocorreu um erro. Para obter mais informações sobre os códigos de estado HTTP, consulte [lista de códigos de estado HTTP](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![Relatório de sucessos](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Estatutos cache
Este relatório descreve a distribuição de cache hits e cache falha para pedidos de clientes. Como o desempenho mais rápido resulta dos sucessos de cache, pode otimizar as velocidades de entrega de dados minimizando falhas de cache e acessos de cache expirados. 

Para reduzir as falhas de cache, configuure o seu servidor de origem para minimizar a utilização do seguinte: 
 * `no-cache` cabeçalhos de resposta
 * Caching de cordas de consulta, a menos que estritamente necessário  
 * Códigos de resposta não cacheáveis

Para reduzir os acessos de cache expirados, desacerte os de um ativo `max-age` para um longo período para minimizar o número de pedidos ao servidor de origem.

![Relatório de estados de cache](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Os principais estados de cache incluem:
* TCP_HIT: Servido a partir do servidor edge. O objeto estava na cache e não excedeu a sua idade máxima.
* TCP_MISS: Servido a partir do servidor de origem. O objeto não estava na cache e a resposta voltou à origem.
* TCP_EXPIRED _MISS: Servido a partir do servidor de origem após revalidação com origem. O objeto estava na cache, mas tinha excedido a sua idade máxima. Uma revalidação com origem resultou na substituição do objeto de cache por uma nova resposta de origem.
* TCP_EXPIRED _HIT: Servido de Edge após revalidação com origem. O objeto estava em cache, mas tinha excedido a sua idade máxima. Uma revalidação com o servidor de origem resultou na desmodificado objeto de cache.

### <a name="full-list-of-cache-statuses"></a>Lista completa de estatutos de cache
* TCP_HIT - Este estado é reportado quando um pedido é servido diretamente do POP ao cliente. Um ativo é imediatamente servido a partir de um POP quando está em cache no POP mais próximo do cliente e tem um tempo de vida válido (TTL). A TTL é determinada pelos seguintes cabeçalhos de resposta:
  
  * Cache-Control: s-maxage
  * Cache-Control: idade máxima
  * Validade
* TCP_MISS: Este estado indica que uma versão em cache do ativo solicitado não foi encontrada no POP mais próximo do cliente. O ativo é solicitado a partir de um servidor de origem ou de um servidor de escudo de origem. Se o servidor de origem ou o servidor do escudo de origem devolverem um ativo, este é servido ao cliente e em cache tanto no cliente como no servidor edge. Caso contrário, é devolvido um código de estado não-200 (por exemplo, 403 Proibido ou 404 Não Encontrados).
* TCP_EXPIRED_HIT: Este estado é reportado quando um pedido que visa um ativo com um TTL caducado foi servido diretamente do POP para o cliente. Por exemplo, quando a idade máxima do ativo tiver expirado. 
  
   Um pedido expirado normalmente resulta num pedido de revalidação para o servidor de origem. Para que um estado TCP_EXPIRED_HIT ocorra, o servidor de origem deve indicar que não existe uma versão mais recente do ativo. Esta situação normalmente resulta numa atualização dos cabeçalhos Cache-Control e Expira do ativo.
* TCP_EXPIRED_MISS: Este estado é relatado quando uma versão mais recente de um ativo em cache expirado é servida do POP para o cliente. Este estado ocorre quando o TTL de um ativo em cache é expirado (por exemplo, a idade máxima expirada) e o servidor de origem devolve uma versão mais recente desse ativo. Esta nova versão do ativo é servida ao cliente em vez da versão em cache. Além disso, está em cache no servidor de borda e no cliente.
* CONFIG_NOCACHE: Este estado indica que uma configuração específica do cliente a borda POP impediu que o ativo fosse em cache.
* NENHUM - Este estado indica que não foi efetuada uma verificação de frescura de conteúdo de cache.
* TCP_CLIENT_REFRESH_MISS: Este estado é relatado quando um cliente HTTP, como um browser, força um POP de borda a recuperar uma nova versão de um ativo velho a partir do servidor de origem. Por predefinição, os servidores impedem que um cliente HTTP obrigue os servidores de borda a recuperar uma nova versão do ativo a partir do servidor de origem.
* TCP_PARTIAL_HIT: Este estado é reportado quando um pedido de alcance byte resulta num sucesso para um ativo parcialmente em cache. A gama byte solicitada é imediatamente servida do POP ao cliente.
* UNCACHEABLE: Este estado é reportado quando um ativo `Cache-Control` e `Expires` cabeçalhos indicam que não deve ser em cache num POP ou pelo cliente HTTP. Estes tipos de pedidos são servidos a partir do servidor de origem.

## <a name="cache-hit-ratio"></a>Taxa de acerto na cache
Este relatório indica a percentagem de pedidos em cache que foram servidos diretamente a partir de cache.

O relatório apresenta os seguintes pormenores:

* O conteúdo solicitado foi colocado em cache no POP mais próximo do solicitador.
* O pedido foi servido diretamente a partir da borda da nossa rede.
* O pedido não requer revalidação com o servidor de origem.

O relatório não inclui:

* Pedidos que são negados devido a opções de filtragem país/região.
* Pedidos de ativos cujos cabeçalhos indicam que não devem ser em cache. Por exemplo, `Cache-Control: private` `Cache-Control: no-cache` , ou `Pragma: no-cache` cabeçalhos impedem que um ativo seja em cache.
* Pedidos de alcance byte para conteúdo parcialmente em cache.

A fórmula é: (TCP_ HIT/(TCP_ HIT+TCP_MISS)))*100

![Relatório de rácio de sucesso de cache](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Dados IPV4/IPV6 transferidos
Este relatório mostra a distribuição de utilização do tráfego no IPV4 vs IPV6.

![Dados IPV4/IPV6 transferidos](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Considerações
Os relatórios só podem ser gerados nos últimos 18 meses.

