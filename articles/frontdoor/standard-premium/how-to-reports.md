---
title: Relatórios Padrão/Premium da Porta Frontal Azure (Pré-visualização)
description: Este artigo explica como funciona a reportagem na Porta frontal Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 9670d8204d04fc770bf3fe98a270a3f6ccbf234b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101100267"
---
# <a name="azure-front-door-standardpremium-preview-reports"></a>Relatórios Padrão/Premium da Porta Frontal Azure (Pré-visualização)

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os relatórios Azure Front Door Standard/Premium Analytics reportes fornecem uma visão incorporada e de todo o lado de como você Azure Front Door se comporta juntamente com métricas associadas de Firewall de aplicação web. Também pode aproveitar os Registos de Acesso para fazer mais resolução de problemas e depuração. Os relatórios do Azure Front Door Analytics incluem relatórios de tráfego e relatórios de segurança.

| Relatórios | Detalhes |
|---------|---------|
| Visão geral das métricas-chave | Mostra dados globais que foram enviados das bordas da Porta frontal Azure para os clientes<br/>- Largura de banda do pico<br/>- Pedidos <br/>- Rácio de batida de cache<br/> - Latência total<br/>- Taxa de erro 5XX |
| Tráfego por Domínio | - Fornece uma visão geral de todos os domínios sob o perfil<br/>- Repartição dos dados transferidos da borda da AFD para o cliente<br/>- Total de pedidos<br/>- Código de resposta 3XX/4XX/5XX por domínios |
| Tráfego por Localização | - Mostra uma visão do mapa do pedido e da utilização pelos países de topo<br/>- Visão de tendência dos países de topo |
| Utilização | - Exibe transferência de dados da borda da Porta Frontal Azure para os clientes<br/>- Transferência de dados da origem para a borda da AFD<br/>- Largura de banda da borda da AFD para os clientes<br/>- Largura de banda da origem até à borda da AFD<br/>- Pedidos<br/>- Latência total<br/>- Tendência de contagem de pedidos por código de estado HTTP |
| Colocação em cache | - Mostra a relação de atingido de cache por contagem de pedidos<br/>- Visão de tendência dos pedidos de atropelamento e falta |
| URL superior | - Mostra contagem de pedidos <br/>- Dados transferidos <br/>- Rácio de batida de cache <br/>- Distribuição do código de estado de resposta para os 50 ativos mais solicitados. |
| Top Referrer | - Mostra contagem de pedidos <br/>- Dados transferidos <br/>- Rácio de batida de cache <br/>- Distribuição de código de estado de resposta para os 50 principais remetentes que geram tráfego. |
| Melhor agente utilizador | - Mostra contagem de pedidos <br/>- Dados transferidos <br/>- Rácio de batida de cache <br/>- Distribuição de código de estado de resposta para os 50 principais agentes utilizadores que foram utilizados para solicitar conteúdo. |

| Relatórios de segurança | Detalhes |
|---------|---------|
| Visão geral das métricas-chave | - Mostra regras de WAF compatíveis<br/>- Regras OWASP compatíveis<br/>- Regras BOT compatíveis<br/>- Regras personalizadas compatíveis |
| Métricas por dimensões | - Repartição da tendência das regras da WAF em correspondência por ação<br/>- Gráfico de donuts de eventos por Tipo de Conjunto de Regras e evento por grupo de regras<br/>- Decompor a lista de eventos de topo por iD de regra, país, endereço IP, URL e agente de utilizadores  |

> [!NOTE]
> Os relatórios de segurança só estão disponíveis com a Azure Front Door Premium SKU.

A maioria dos relatórios baseia-se em registos de acesso e são oferecidos gratuitamente aos clientes na Azure Front Door. O cliente não tem de ativar registos de acesso ou fazer qualquer configuração para visualizar estes relatórios. Os relatórios são acessíveis através do portal e da API. O download do CSV também é suportado. 

Os relatórios suportam qualquer data selecionada dos 90 dias anteriores. Com pontos de dados de cada 5 minutos, a cada hora, ou todos os dias com base no intervalo de datas selecionado. Normalmente, pode ver dados com atraso de uma hora e ocasionalmente com atraso de até algumas horas. 

## <a name="access-reports-using-the-azure-portal"></a>Relatórios de Acesso utilizando o portal Azure

1. Faça o sessão no [portal Azure](https://portal.azure.com) e selecione o seu perfil Azure Front Door Standard/Premium.

1. No painel de navegação, selecione **Relatórios ou Segurança** em *Analytics*.

   :::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Screenshot da página de aterragem de relatórios":::

1. Existem sete separadores para diferentes dimensões, selecione a dimensão do interesse.

   * Tráfego por domínio
   * Utilização 
   * Tráfego por local
   * Cache
   * Url superior
   * Principal remetente
   * Melhor agente utilizador

1. Depois de escolher a dimensão, pode selecionar filtros diferentes.
  
    1. **Mostrar dados para** - Selecione o intervalo de datas para o qual pretende visualizar o tráfego por domínio. As gamas disponíveis são:
        
        * Últimas 24 horas
        * Últimos 7 dias
        * Últimos 30 dias
        * Últimos 90 dias
        * This month
        * Último mês
        * Data personalizada

         Por predefinição, os dados são apresentados nos últimos sete dias. Para separadores com gráficos de linha, a granularidade de dados vai com os intervalos de data selecionados como o comportamento padrão. 
    
        * 5 minutos - um ponto de dados a cada 5 minutos para data varia menos ou igual a 24 horas.
        * Por hora – um dado a cada hora para data varia entre 24 horas e 30 dias
        * De dia – um dado por dia para datas varia mais de 30 dias.

        Pode sempre utilizar a agregação para alterar a granularidade de agregação predefinido. Nota: 5 minutos não funcionam para o intervalo de dados por mais de 14 dias. 

    1. **Localização** - Selecione localizações individuais ou múltiplas por país. Os países estão agrupados em seis regiões: América do Norte, Ásia, Europa, África, Oceânia e América do Sul. Consulte o [mapeamento região/país.](https://en.wikipedia.org/wiki/Subregion) Por padrão, todos os países são selecionados.
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-locations.png" alt-text="Screenshot de Relatórios para a dimensão da localização.":::
   
    1. **Protocolo** - Selecione HTTP ou HTTPS para visualizar os dados de tráfego.
 
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-protocol.png" alt-text="Screenshot de Relatórios para a dimensão do protocolo.":::
    
    1. **Domínios** - Selecione pontos finais individuais ou multi-endpoints ou domínios personalizados. Por padrão, todos os pontos finais e domínios personalizados são selecionados. 
    
        * Se eliminar um ponto final ou um domínio personalizado num perfil e, em seguida, recriar o mesmo ponto final ou domínio noutro perfil. O ponto final será considerado um segundo ponto final.  
        * Se estiver a visualizar relatórios por domínio personalizado - quando eliminar um domínio personalizado e ligá-lo a um ponto final diferente. Serão tratados como um domínio personalizado. Se a vista por ponto final - serão tratados como itens separados. 
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-domain.png" alt-text="Screenshot de Relatórios para a dimensão do domínio.":::

1. Se pretender exportar os dados para um ficheiro CSV, selecione o link *Download CSV* no separador selecionado.

    :::image type="content" source="../media/how-to-reports/front-door-reports-download-csv.png" alt-text="Screenshot do download do ficheiro CSV para Relatórios.":::

### <a name="key-metrics-for-all-reports"></a>Métricas-chave para todos os relatórios

| Metric | Descrição |
|---------|---------|
| Dados transferidos | Mostra dados transferidos de POPs de borda AFD para cliente para o tempo selecionado, localizações do cliente, domínios e protocolos. |
| Largura de banda do pico | Pico de utilização da largura de banda em bits por segundo, desde POPs de borda da porta frontal Azure ao cliente para o tempo, localizações do cliente, domínios e protocolos selecionados. | 
| Total de Pedidos | O número de pedidos que os POPs de borda AFD responderam ao cliente para o período de tempo selecionado, localizações do cliente, domínios e protocolos. |
| Taxa de acerto na cache | A percentagem de todos os pedidos cacheables para os quais a AFD serviu o conteúdo das suas caches de borda para o período de tempo selecionado, localizações do cliente, domínios e protocolos. |
| Taxa de erro 5XX | A percentagem de pedidos para os quais o código de estado HTTP para o cliente era um 5XX para o período de tempo selecionado, localizações do cliente, domínios e protocolos. |
| Latência total | Latência média de todos os pedidos para o prazo selecionado, localizações do cliente, domínios e protocolos. A latência de cada pedido é medida como o momento total de quando o pedido do cliente é recebido pela Azure Front Door até à última resposta enviada da Azure Front Door para o cliente. |

## <a name="traffic-by-domain"></a>Tráfego por Domínio

O tráfego por domínio proporciona uma vista de grelha de todos os domínios sob este perfil da Porta Frontal Azure. Neste relatório pode ver: 
* Pedidos
* Dados transferidos da Porta Frontal Azure para o cliente
* Pedidos com código de estado (3XX, 4Xx e 5XX) de cada domínio

Os domínios incluem Endpoint e Domínios Personalizados, conforme explicado na sessão 'Relatório de Acesso'.  

Pode ir a outros separadores para investigar mais ou ver o registo de acesso para obter mais informações se encontrar as métricas abaixo das suas expectativas. 

:::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Screenshot da página de aterragem para relatórios":::


## <a name="usage"></a>Utilização

Este relatório mostra as tendências do código de estado de tráfego e resposta por diferentes dimensões, incluindo:

* Dados transferidos da borda para o cliente e de origem para borda na tabela de linha. 

* Dados transferidos da borda para o cliente por protocolo na tabela de linha. 

* Número de pedidos de borda para clientes na tabela de linha.  

* Número de pedidos de edge para clientes por protocolo, HTTP e HTTPS, na tabela de linha. 

* Largura de banda da borda para o cliente na tabela de linha. 

* Latência total, que mede o tempo total do pedido de cliente recebido pela Porta frontal até à última resposta byte enviada da Porta frontal para o cliente.

* Número de pedidos de borda para clientes por código de estado HTTP, na tabela de linha. Cada pedido gera um código de estado HTTP. O código de estado HTTP aparece no HTTPStatusCode em Registo Bruto. O código de estado descreve como a borda da CDN lidou com o pedido. Por exemplo, um código de estado de 2xx indica que o pedido foi servido com sucesso a um cliente. Enquanto um código de estado 4xx indica que ocorreu um erro. Para obter mais informações sobre os códigos de estado HTTP, consulte Lista de códigos de estado HTTP. 

* Número de pedidos da borda para os clientes por código de estado HTTP. Percentagem de pedidos por código de estado HTTP entre todos os pedidos na grelha. 

:::image type="content" source="../media/how-to-reports/front-door-reports-usage.png" alt-text="Screenshot de Relatórios por utilização" lightbox="../media/how-to-reports/front-door-reports-usage-expanded.png":::

## <a name="traffic-by-location"></a>Tráfego por Localização

Este relatório exibe as 50 melhores localizações do país dos visitantes que mais acedem ao seu ativo. O relatório também fornece uma repartição das métricas por país e dá-lhe uma visão geral dos países onde o maior tráfego é gerado. Por último, pode ver qual o país que está a ter uma relação de impacto de cache mais alta ou códigos de erro 4XX/5XX.

:::image type="content" source="../media/how-to-reports/front-door-reports-by-location.png" alt-text="Screenshot de Relatórios por locais" lightbox="../media/how-to-reports/front-door-reports-by-location-expanded.png":::

Os relatórios são incluídos nos relatórios:

* Uma visão do mapa mundial dos 50 melhores países por dados transferidos ou pedidos à sua escolha.
* Duas linhas de gráficos de tendênciavisão dos cinco principais países por dados transferidos e pedidos à sua escolha. 
* Uma rede dos países de topo com dados correspondentes transferidos da AFD para os clientes, dados transferidos % de todos os países, pedidos, pedidos de pedidos % entre todos os países, rácio de sucesso de cache, código de resposta 4XX e código de resposta 5XX.

## <a name="caching"></a>Colocação em cache

Os relatórios de caching fornecem uma visão de gráfico de cache hits/misses e rácio de batida de cache com base em pedidos. Estas métricas-chave explicam como a CDN está a cache conteúdos, uma vez que o desempenho mais rápido resulta dos sucessos de cache. Pode otimizar as velocidades de entrega de dados minimizando as falhas de cache. Este relatório inclui:

* Cache bateu e perder a tendência de contagem, na tabela de linha.

* Rácio de batida de cache na tabela de linha.

Cache Hits/Misses descreve os cache hits de número de pedido e cache falha para pedidos do cliente.

* Hits: os pedidos do cliente que são servidos diretamente a partir de servidores de borda Azure CDN. Consulte os pedidos cujos valores para CacheStatus em troncos brutos são HIT, PARTIAL_HIT ou REMOTE HIT. 

* Miss: os pedidos do cliente que são servidos por servidores de borda Azure CDN que recolhem conteúdos de origem. Consulte os pedidos cujos valores para o campo CacheStatus em troncos brutos são MISS. 

**A relação de impacto da cache** descreve a percentagem de pedidos em cache que são servidos diretamente a partir da borda. A fórmula do rácio de batida de cache é: `(PARTIAL_HIT +REMOTE_HIT+HIT/ (HIT + MISS + PARTIAL_HIT + REMOTE_HIT)*100%` . 

Este relatório toma em consideração os cenários de caching e os pedidos que satisfaçam os seguintes requisitos são tomados em cálculo. 

* O conteúdo solicitado foi colocado no POP mais próximo do solicitador ou escudo de origem. 

* Conteúdo parcial em cache para o pedaço de objeto.

Exclui todos os seguintes casos: 

* Pedidos que são negados por causa do Regulamento Definido.

* Pedidos que contenham regras correspondentes Conjunto que foi definido para cache desativado. 

* Pedidos que são bloqueados pela WAF. 

* Os cabeçalhos de resposta à origem indicam que não devem ser em cache. Por exemplo, Cache-Control: privado, Cache-Control: no-cache, ou Pragma: os cabeçalhos sem cache impedirão que um ativo seja em cache. 

:::image type="content" source="../media/how-to-reports/front-door-reports-caching.png" alt-text="Screenshot de Relatórios para caching.":::

## <a name="top-urls"></a>URLs de topo

Os URLs de topo permitem-lhe visualizar a quantidade de tráfego incorrido sobre um determinado ponto final ou domínio personalizado. Verá os dados dos 50 ativos mais solicitados durante qualquer período dos últimos 90 dias. Os URLs populares serão exibidos com os seguintes valores. O utilizador pode ordenar URLs por contagem de pedidos, pedido %, dados transferidos e dados transferidos . Todas as métricas são agregadas por hora e podem variar de acordo com o prazo selecionado. URL refere-se ao valor do RequestUri no registo de acesso. 

:::image type="content" source="../media/how-to-reports/front-door-reports-top-url.png" alt-text="Screenshot de Relatórios para URL superior.":::

* URL, refere-se ao percurso completo do ativo solicitado no formato de `http(s)://contoso.com/index.html/images/example.jpg` . 
* O pedido conta. 
* Solicito % do total de pedidos servidos pela Porta frontal Azure. 
* Dados transferidos. 
* Dados transferidos %. 
* Rácio de sucesso de cache %
* Pedidos com código de resposta como 4XX
* Pedidos com código de resposta como 5XX

> [!NOTE]
> Os URLs de topo podem mudar ao longo do tempo e para obter uma lista precisa dos 50 URLs mais bem-dispostos, a Porta Frontal Azure conta todos os seus pedidos de URL por hora e mantém o total de execução ao longo de um dia. Os URLs na parte inferior dos 500 URLs podem subir ou deixar a lista ao longo do dia, de modo que o número total destes URLs são aproximações.  
>
> Os 50 URLs do top 50 podem subir e cair na lista, mas raramente desaparecem da lista, por isso os números dos URLs de topo são geralmente fiáveis. Quando um URL sai da lista e se levanta novamente ao longo de um dia, o número de pedidos durante o período em que estão ausentes da lista é estimado com base no número de pedido do URL que aparece nesse período.  
>
> A mesma lógica aplica-se ao Agente utilizador superior. 

## <a name="top-referrers"></a>Principais Remetentes

Os Principais Referrers permitem que os clientes vejam o principal remetente de 50 que originou mais pedidos para o conteúdo num determinado ponto final ou domínio personalizado. Pode ver os dados durante qualquer período nos últimos 90 dias. Um remetente indica o URL a partir do qual foi gerado um pedido. O remetente pode vir de um motor de busca ou de outros websites. Se um utilizador escreve um URL (por exemplo, http(s)://contoso.com/index.html) diretamente na linha de endereço de um browser, o remetente para o pedido é "Vazio". O relatório dos principais remetentes inclui os valores seguintes. Pode ordenar por contagem de pedidos, solicitar%, dados transferidos e dados transferidos %. Todas as métricas são agregadas por hora e podem variar de acordo com o prazo selecionado. 

* Referrer, o valor do Referrer em troncos brutos 
* Contagens de pedidos 
* Pedido % do total de pedidos servidos pela Azure CDN no período de tempo selecionado. 
* Dados transferidos 
* Dados transferidos % 
* Rácio de sucesso de cache %
* Pedidos com código de resposta como 4XX
* Pedidos com código de resposta como 5XX

:::image type="content" source="../media/how-to-reports/front-door-reports-top-referrer.png" alt-text="Screenshot de Relatórios para o principal remetente.":::

## <a name="top-user-agent"></a>Melhor agente utilizador

Este relatório permite-lhe ter uma visão gráfica e estatística dos 50 principais agentes utilizadores que foram utilizados para solicitar conteúdo. Por exemplo,
* Mozilla/5.0 (Windows NT 10.0; WOW64) 
* AppleWebKit/537.36 (KHTML, como Gecko) 
* Cromado/86.0.4240.75 
* Safari/537.36.  

Uma grelha exibe as contagens de pedidos, pedidos %, dados transferidos e dados transferidos, cache Hit Ratio %, solicita com código de resposta como 4XX e solicita com código de resposta como 5XX. O Agente utilizador refere-se ao valor do UserAgent nos registos de acesso.

## <a name="security-report"></a>Relatório de Segurança

Este relatório permite-lhe ter uma visão gráfica e estatística dos padrões WAF por diferentes dimensões.

| Dimensões | Description |
|---------|---------|
| Métricas gerais - Regras de WAF compatíveis | Pedidos que correspondam às regras personalizadas da WAF, regras da WAF geridas e gestor de bots. |
| Métricas de visão geral - Pedidos bloqueados | A percentagem de pedidos que são bloqueados pelas regras da WAF entre todos os pedidos que correspondam às regras da WAF. |
| Métricas de visão geral - Regras geridas em correspondência | Quatro linhas-charts tendência para pedidos que são Bloco, Log, Allow e Redirect. |
| Métricas de visão geral - Regra personalizada de correspondência | Pedidos que correspondam às regras personalizadas da WAF. |
| Métricas de visão geral - Regra do bot combinado | Pedidos que correspondam ao Bot Manager. |
| Tendência de pedido da WAF por ação | Quatro linhas-charts tendência para pedidos que são Bloco, Log, Allow e Redirect. |
| Eventos por Tipo de Regra | Gráfico de donuts da WAF solicita distribuição por Tipo de Regra, por exemplo Bot, regras personalizadas e regras geridas. |
| Eventos por Grupo de Regras | Gráfico de donuts da WAF solicita distribuição por Grupo rule. |
| Pedidos por ações | Uma tabela de pedidos por ações, por ordem descendente. |
| Pedidos por IDs de regra superior | Uma tabela de pedidos por iDs de regra top 50, por ordem descendente. |
| Pedidos por países de topo |  Uma tabela de pedidos dos 50 melhores países, por ordem descendente. |
| Pedidos por IPs de cliente de topo |  Uma tabela de pedidos pelos 50 melhores IPs, em ordem descendente. |
| Pedidos por URL de pedido superior |  Uma tabela de pedidos dos 50 melhores URLs, em ordem descendente. |
| Pedido por hostnames de topo | Uma tabela de pedidos pelo top 50 nome de anfitrião, em ordem descendente. |
| Pedidos por agentes de utilizadores de topo | Uma tabela de pedidos dos 50 principais agentes utilizadores, em ordem descendente. |

## <a name="cvs-format"></a>Formato CVS

Pode descarregar ficheiros CSV para diferentes separadores em relatórios. Esta secção descreve os valores em cada ficheiro CSV.

### <a name="general-information-about-the-cvs-report"></a>Informações gerais sobre o relatório do CVS

Cada relatório CSV inclui algumas informações gerais e a informação está disponível em todos os ficheiros CSV. com variáveis baseadas no relatório que descarrega. 


| Valor | Descrição |
|---------|---------|
| Relatório | O nome do relatório. | 
| Domínios | A lista dos pontos finais ou domínios personalizados para o relatório. |
| StartDateUTC | O início da gama de datas para a qual gerou o relatório, em Tempo Universal Coordenado (UTC) |
| EndDateUTC | O fim da gama de datas para a qual gerou o relatório, em Tempo Universal Coordenado (UTC) |
| GeradoTimeuTC | A data e a hora em que gerou o relatório, em Tempo Universal Coordenado (UTC) |
| Localização | A lista dos países onde os pedidos de clientes se originaram. O valor é ALL por padrão. Não aplicável ao relatório de segurança. |
| Protocolo | O protocolo do pedido, HTTP ou HTTPs. Não aplicável ao TOP URL e Tráfego por Agente utilizador em Relatórios e Relatório de Segurança. |
| Agregação | A granularidade da agregação de dados em cada linha, a cada 5 minutos, a cada hora, e todos os dias. Não aplicável ao Relatório de Tráfego por Domínio, URL Superior e Tráfego por Agente utilizador em Relatórios e Segurança. |

### <a name="data-in-traffic-by-domain"></a>Dados em Tráfego por Domínio

* Domínio 
* Pedido Total 
* Taxa de acerto na cache 
* 3XX Pedidos 
* 4XX Pedidos 
* 5XX Pedidos 
* ByteTransferredFromEdgeToClient 

### <a name="data-in-traffic-by-location"></a>Dados em Tráfego por Localização

* Localização
* TotalRequests
* Solicitar%
* BytesTransferredFromEdgeToClient

### <a name="data-in-usage"></a>Dados em Utilização

Há três relatórios neste ficheiro CSV. Um para protocolo HTTPS, um para protocolo HTTPS e outro para Código de Estado HTTP. 

Os relatórios de HTTP e HTTPs partilham o mesmo conjunto de dados. 

* Hora 
* Protocolo 
* DataTransferido (bytes) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest 

Relatório para HTTP Status Code. 

* Hora 
* DataTransferido (bytes) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest

### <a name="data-in-caching"></a>Dados em Caching 

* Hora
* CacheHitRatio 
* HitRequests 
* MissRequests 

### <a name="data-in-top-url"></a>Dados em URL superior 

* URL 
* TotalRequests 
* Solicitar% 
* DataTransferido (bytes) 
* DadosTransferidos% 

### <a name="data-in-user-agent"></a>Dados no Agente utilizador 

* UserAgent 
* TotalRequests 
* Solicitar% 
* DataTransferido (bytes) 
* DadosTransferidos% 

### <a name="security-report"></a>Relatório de Segurança 

Há sete mesas todas com os mesmos campos abaixo.  

* Requestos Bloqueados 
* Requess permitidos 
* Reques registados 
* Redirecionados Requestras 
* OWASPRuleRequests 
* Personalizadorequests 
* BotRequests 

As sete tabelas são para tempo, ID regra, país, endereço IP, URL, nome de anfitrião, agente de utilizadores. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [as métricas de monitorização em tempo real standard/Premium](how-to-monitor-metrics.md)da porta frontal.
