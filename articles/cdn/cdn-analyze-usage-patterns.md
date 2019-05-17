---
title: Núcleos de relatórios da Verizon | Documentos da Microsoft
description: 'Pode ver padrões de utilização da sua CDN utilizando os seguintes relatórios: Largura de banda, os dados transferidos, visitas, Estados de Cache, a taxa, dados de IPV4/IPV6 transferidos de acerto na Cache.'
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 88cbd942413757388278d69d728d407271e4c4a3
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606381"
---
# <a name="core-reports-from-verizon"></a>Relatórios de núcleos da Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Ao utilizar relatórios de núcleos da Verizon através do portal de gerenciar para Verizon perfis, pode ver padrões de utilização da sua CDN com os seguintes relatórios:

* Largura de banda
* Dados transferidos
* Acertos
* Estados da cache
* Taxa de acerto na cache
* Dados de IPv4/IPV6 transferidos

## <a name="accessing-verizon-core-reports"></a>Aceder a relatórios de núcleos da Verizon
1. No painel de perfil da CDN, clique a **gerir** botão.
   
    ![Botão de gerir do perfil CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    É aberto o portal de gestão da CDN.
2. Paire o rato sobre o **Analytics** separador, em seguida, coloque o cursor sobre o **relatórios de núcleos** submenu. Clique num relatório no menu.
   
    ![Portal de gestão de CDN - menu de relatórios de núcleos](./media/cdn-reports/cdn-core-reports.png)

3. Para cada relatório, selecione um intervalo de datas do **intervalo de datas** lista. Pode selecionar um intervalo de datas predefinidas, como **hoje** ou **esta semana**, ou pode selecionar **Custom** e introduzir manualmente um intervalo de datas clicando nos ícones do calendário. 

4. Depois de selecionar um intervalo de datas, clique em **ir** para gerar o relatório. 

4. Se quiser exportar os dados no formato Excel, clique no ícone de Excel acima a **ir** botão.

## <a name="bandwidth"></a>Largura de banda
O relatório de largura de banda consiste numa tabela de dados e gráficos que indica a utilização de largura de banda da CDN para HTTP e HTTPS durante um período de tempo específico, em Mbps. Pode ver a utilização de largura de banda em todos os POPs ou para um determinado POP. Este relatório permite-lhe ver a picos de tráfego e a distribuição para os POPs.

Partir do **nós periféricos** lista, selecione **todos os nós de extremidade** para ver o tráfego de todos os nós ou selecione uma região específica.

O relatório é atualizado a cada cinco minutos.

![Relatório de largura de banda](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Dados transferidos
Este relatório é constituído por uma tabela de dados e gráficos que indica a utilização de tráfego CDN para HTTP e HTTPS durante um período de tempo específico, em GB. Pode ver a utilização de tráfego em todos os POPs ou para um determinado POP. Este relatório permite-lhe ver a picos de tráfego e a distribuição em POPs.

Partir do **nós periféricos** lista, selecione **todos os nós de extremidade** para ver o tráfego de todos os nós ou selecione uma região específica.

O relatório é atualizado a cada cinco minutos.

![Dados transferidos de relatório](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Acertos (códigos de estado)
Este relatório descreve a distribuição de códigos de estado do pedido para o seu conteúdo. Cada solicitação de conteúdo gera um código de estado HTTP. O código de estado descreve como o edge POPs processada o pedido. Por exemplo, um código de estado 2xx indica que o pedido foi servido com êxito para um cliente, enquanto um código de estado de 4xx indica que ocorreu um erro. Para obter mais informações sobre códigos de estado HTTP, consulte [códigos de estado de lista de HTTP](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![Relatório de resultados](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Estados da cache
Este relatório descreve a distribuição de acertos na cache e falhas de acerto na cache para pedidos de cliente. Uma vez que o desempenho mais rápido resulta de acertos na cache, pode otimizar velocidades de entrega de dados ao minimizar erros de cache e de acertos na cache expirada. 

Para reduzir falhas de acerto na cache, configure o servidor de origem para minimizar a utilização dos seguintes procedimentos: 
 * `no-cache` Cabeçalhos de resposta
 * Cache de cadeia de consulta, a menos que o estritamente necessário  
 * Códigos de resposta em cache não

Para reduzir acertos na cache expirada, defina um recurso `max-age` para um longo período para minimizar o número de pedidos para o servidor de origem.

![Relatório de Estados de cache](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Estados da cache principal incluem:
* TCP_HIT: Servidos a partir do servidor de borda. O objeto foi na cache e não excedeu a respetiva duração máxima.
* TCP_MISS: Servidos a partir do servidor de origem. O objeto não estava no cache e a resposta era para a origem.
* TCP_EXPIRED _MISS: Servido a partir do servidor de origem após a revalidação com origem. O objeto foi na cache, mas tinha excedeu a respetiva duração máxima. Uma revalidação com origem resultou no objeto de cache a ser substituído por uma nova resposta de origem.
* TCP_EXPIRED _HIT: Servido a partir do Edge após a revalidação com origem. O objeto foi na cache, mas tinha excedeu a respetiva duração máxima. Uma revalidação com o servidor de origem resultou no objeto de cache que está a ser sem modificações.

### <a name="full-list-of-cache-statuses"></a>Lista completa de Estados de cache
* TCP_HIT - este estado é comunicado quando uma solicitação será atendida diretamente a partir do POP ao cliente. Um recurso é servido imediatamente a partir de um preenchimento de quando ele é armazenado em cache no POP de mais próximo para o cliente e possui um válido time-to-live (TTL). TTL é determinado pelos seguintes cabeçalhos de resposta:
  
  * Cache-Control: s-maxage
  * Cache-Control: idade máxima
  * Expira
* TCP_MISS: Este status indica que uma versão em cache do recurso solicitado não foi encontrada no POP de mais próximo ao cliente. O elemento é pedido a partir de um servidor de origem ou de um servidor de escudo de origem. Se o servidor de origem ou o servidor de origem do escudo retornar um recurso, é fornecido ao cliente e colocados em cache no cliente e o servidor de borda. Caso contrário, um código de estado que não 200 (por exemplo, 403 proibido ou 404 não encontrado) é devolvido.
* TCP_EXPIRED_HIT: Este estado é comunicado quando um pedido que tenha como destino um recurso com um valor de TTL expirado tiver sido servido diretamente a partir do POP ao cliente. Por exemplo, quando o elemento da duração máxima expirou. 
  
   Um pedido de expirada normalmente resulta numa solicitação de revalidação para o servidor de origem. Para obter o estado TCP_EXPIRED_HIT ocorra, o servidor de origem tem de indicar que uma versão mais recente do recurso não existe. Essa situação geralmente resulta numa atualização de Cache-Control e cabeçalhos de Expires do recurso.
* TCP_EXPIRED_MISS: Este estado é comunicado quando uma versão mais recente de um ativo em cache expirada é servida a partir POP ao cliente. Este estado ocorre quando o valor de TTL para um ativo em cache é a expirou (por exemplo, a expirou duração máxima) e o servidor de origem devolve uma versão mais recente desse ativo. Esta nova versão do recurso é fornecido ao cliente em vez da versão em cache. Além disso, ele será armazenado no servidor edge e no cliente.
* CONFIG_NOCACHE: Este status indica que uma configuração de cliente específicas a borda POP impediu o elemento que está a ser colocados em cache.
* NENHUM - este estado indica que não foi efetuada uma verificação de atualização de conteúdo do cache.
* TCP_CLIENT_REFRESH_MISS: Este estado é comunicado quando um cliente HTTP, como um navegador, força uma borda POP, para obter uma nova versão de um ativo obsoleto a partir do servidor de origem. Por predefinição, os servidores de impedir que um cliente HTTP forçando os servidores de borda para obter uma nova versão do recurso do servidor de origem.
* TCP_PARTIAL_HIT: Este estado é comunicado quando uma solicitação de intervalo de bytes resulta num impacto para um ativo parcialmente em cache. O intervalo de bytes pedido imediatamente é servido a partir do POP ao cliente.
* ATRIBUIR: Este estado é comunicado quando um recurso `Cache-Control` e `Expires` cabeçalhos indicam que ele deve não ser armazenados em cache num POP ou pelo cliente HTTP. Esses tipos de pedidos são servidos a partir do servidor de origem.

## <a name="cache-hit-ratio"></a>Taxa de acerto na cache
Este relatório indica a percentagem de pedidos em cache que foram servidos diretamente a partir da cache.

O relatório disponibiliza os seguintes detalhes:

* O conteúdo solicitado foi colocado em cache no POP de mais próximo para o autor do pedido.
* O pedido foi servido diretamente a partir do limite da nossa rede.
* O pedido não obrigava a revalidação com o servidor de origem.

O relatório não inclui:

* Pedidos que são negados devido a opções de filtragem de país/região.
* Pedidos ativos cujos cabeçalhos indicam que eles devem não ser colocados em cache. Por exemplo, `Cache-Control: private`, `Cache-Control: no-cache`, ou `Pragma: no-cache` cabeçalhos de impedem que um recurso que está a ser colocados em cache.
* Pedidos de intervalo de bytes do conteúdo parcialmente em cache.

A fórmula é: (TCP_ HIT/(TCP_ HIT+TCP_MISS))*100

![Relatório de taxa de acertos da cache](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>IPv4/IPV6 dados transferidos
Este relatório mostra a distribuição de utilização de tráfego de IPV4 vs IPV6.

![IPv4/IPV6 dados transferidos](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Considerações
Apenas podem ser gerados relatórios nos últimos 18 meses.

