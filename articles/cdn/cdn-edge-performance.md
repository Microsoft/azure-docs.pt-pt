---
title: Analise o desempenho do nó de borda no Azure CDN Microsoft Docs
description: Analise o desempenho do nó de borda no Microsoft Azure CDN. Edge Performance Analytics fornece tráfego de informação granular e utilização de largura de banda para o CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d8119c56c26ed32db136064d93775d21a71f238f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778653"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analisar o desempenho do nó de extremidade na CDN do Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral
A análise do desempenho da borda fornece tráfego de informação granular e utilização de largura de banda para o CDN. Esta informação pode então ser usada para gerar estatísticas de tendência, que lhe permitem obter informações sobre como os seus ativos estão a ser em cache e entregues aos seus clientes. Por sua vez, isto permite-lhe formar uma estratégia sobre como otimizar a entrega do seu conteúdo e determinar quais as questões que devem ser abordadas para melhor alavancar o CDN. Como resultado, não só poderá melhorar o desempenho da entrega de dados, como também será capaz de reduzir os seus custos de CDN.

> [!NOTE]
> Todos os relatórios utilizam a notação UTC/GMT ao especificar uma data/hora.
> 
> 

## <a name="reports-and-log-collection"></a>Relatórios e recolha de registos
Os dados de atividade do CDN devem ser recolhidos pelo módulo Edge Performance Analytics antes de poder gerar relatórios sobre o mesmo. Este processo de recolha ocorre uma vez por dia e abrange a atividade que ocorreu durante o dia anterior. Isto significa que as estatísticas de um relatório representam uma amostra das estatísticas do dia no momento em que foi processada e não contêm necessariamente o conjunto completo de dados para o dia atual. A função principal destes relatórios é avaliar o desempenho. Não devem ser utilizados para efeitos de faturação ou estatísticas numéricas exatas.

> [!NOTE]
> Os dados brutos a partir dos quais são gerados relatórios analíticos do Edge Performance estão disponíveis durante pelo menos 90 dias.
> 
> 

## <a name="dashboard"></a>Dashboard
O dashboard Edge Performance Analytics rastreia o tráfego atual e histórico de CDN através de um gráfico e estatísticas. Utilize este dashboard para detetar tendências recentes e a longo prazo sobre o desempenho do tráfego de CDN para a sua conta.

Este painel é composto por:

* Um gráfico interativo que permite a visualização de métricas e tendências chave.
* Uma linha do tempo que proporciona uma sensação de padrões de longo prazo para métricas e tendências chave.
* Métricas-chave e informação estatística sobre como a nossa rede CDN melhora o tráfego do site medido pelo desempenho, utilização e eficiência globais.

### <a name="accessing-the-edge-performance-dashboard"></a>Aceder ao painel de desempenho da borda
1. A partir da lâmina de perfil CDN, clique no botão **Gerir.**
   
    ![Botão de gestão da lâmina de perfil CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    O portal de gestão cdn abre.
2. Passe por cima do **separador Analytics** e, em seguida, paire sobre o flyout **Edge Performance Analytics.**  Clique no **Dashboard** .
   
    É apresentado o painel de análise do nó de borda.

### <a name="chart"></a>Gráfico
O painel contém um gráfico que rastreia uma métrica durante o período de tempo selecionado na linha de tempo que aparece diretamente abaixo dele.  Uma linha temporal que gráfico até aos últimos dois anos de atividade da CDN é exibida diretamente abaixo da tabela.

#### <a name="using-the-chart"></a>Usando o gráfico
* Por padrão, a taxa de eficiência da cache dos últimos 30 dias será cartografada.
* Este gráfico é gerado a partir de dados colhidos diariamente.
* Pairar ao longo de um dia no gráfico de linha indicará uma data e o valor da métrica nessa data.
* Clique em Highlight Weekends para alternar uma sobreposição de barras verticais cinzentas claras que representam fins de semana na tabela. Este tipo de sobreposição é útil para identificar padrões de tráfego durante os fins de semana.
* Clique em Ver Há um ano para alternar uma sobreposição da atividade do ano anterior durante o mesmo período de tempo na tabela. Este tipo de comparação fornece uma visão sobre padrões de utilização de CDN a longo prazo. O canto superior direito da tabela contém uma lenda que indica o código de cores para cada gráfico de linha.

#### <a name="updating-the-chart"></a>Atualizar o gráfico
* Intervalo de tempo: Realizar um dos seguintes:
  * Selecione a região desejada na linha do tempo. O gráfico será atualizado com dados que correspondam ao período de tempo selecionado.
  * Clique duas vezes na tabela para exibir todos os dados históricos disponíveis até um máximo de dois anos.
* Métrica: Clique no ícone de gráfico que aparece ao lado da métrica desejada. O gráfico e a linha do tempo serão atualizados com dados para a métrica correspondente.

### <a name="key-metrics-and-statistics"></a>Principais métricas e estatísticas
#### <a name="efficiency-metrics"></a>Métricas de eficiência
O objetivo destas métricas é ver se a eficiência da cache pode ser melhorada. Os principais benefícios derivados da eficiência da cache são:

* Carga reduzida no servidor de origem que pode levar a:
  * Melhor desempenho do servidor web.
  * Redução dos custos operacionais.
* Melhor aceleração da entrega de dados, uma vez que mais pedidos serão servidos diretamente do CDN.

| Campo | Descrição |
| --- | --- |
| Eficiência cache |Indica a percentagem de dados transferidos que foi servido a partir de cache. Esta métrica mede quando uma versão em cache do conteúdo solicitado foi servida diretamente do CDN (servidores de borda) aos solicitadores (por exemplo, navegador web) |
| Taxa de sucesso |Indica a percentagem de pedidos que foram servidos a partir de cache. Esta métrica mede quando uma versão em cache do conteúdo solicitado foi servida diretamente do CDN (servidores de borda) aos solicitadores (por exemplo, navegador web). |
| % de Bytes Remotos - Sem Cache Config |Indica a percentagem de tráfego que foi servida desde servidores de origem até ao CDN (servidores de borda) que não será em cache como resultado da funcionalidade Cache bypass (HTTP Rules Engine). |
| % de Bytes Remotos - Cache expirada |Indica a percentagem de tráfego que foi servido de servidores de origem para o CDN (servidores de borda) como resultado da revalidação de conteúdo estagnado. |

#### <a name="usage-metrics"></a>Métricas de utilização
Estas métricas têm por objetivo fornecer informações sobre as seguintes medidas de redução de custos:

* Minimizar os custos operacionais através do CDN.
* Redução das despesas de CDN através da eficiência e compressão da cache.

> [!NOTE]
> Os números do volume de tráfego representam o tráfego que foi utilizado em cálculos de rácios e percentagens, e só pode mostrar uma parte do tráfego total para clientes de grande volume.
> 
> 

| Campo | Descrição |
| --- | --- |
| Ave Bytes out |Indica o número médio de bytes transferidos para cada pedido servido do CDN (servidores de borda) para o solicitador (por exemplo, navegador web). |
| Sem Taxa de Byte Cache Config |Indica a percentagem de tráfego servido do CDN (servidores de borda) para o solicitador (por exemplo, navegador web) que não será cached devido à funcionalidade Cache bypass. |
| Taxa de byte comprimido |Indica a percentagem de tráfego enviado do CDN (servidores de borda) para os solicitadores (por exemplo, navegador da Web) num formato comprimido. |
| Bytes out |Indica a quantidade de dados, em bytes, que foram entregues do CDN (servidores de borda) ao solicitador (por exemplo, navegador web). |
| Bytes In |Indica a quantidade de dados, em bytes, enviados de solicitadores (por exemplo, navegador da Web) para o CDN (servidores de borda). |
| Bytes Remoto |Indica a quantidade de dados, em bytes, enviados de CDN e servidores de origem do cliente para o CDN (servidores de borda). |

#### <a name="performance-metrics"></a>Métricas de Desempenho
O objetivo destas métricas é acompanhar o desempenho global da CDN para o seu tráfego.

| Campo | Descrição |
| --- | --- |
| Taxa de Transferência |Indica a taxa média a que o conteúdo foi transferido do CDN para um solicitador. |
| Duração |Indica o tempo médio, em milissegundos, que levou para entregar um ativo a um solicitador (por exemplo, navegador web). |
| Taxa de Pedido Comprimido |Indica a percentagem de acessos que foram entregues do CDN (servidores de borda) ao solicitador (por exemplo, navegador web) num formato comprimido. |
| Taxa de erro 4xx |Indica a percentagem de acessos que gerou um código de estado de 4xx. |
| Taxa de erro de 5xx |Indica a percentagem de acessos que gerou um código de estado de 5xx. |
| Acertos |Indica o número de pedidos de conteúdo cdn. |

#### <a name="secure-traffic-metrics"></a>Métricas de tráfego seguro
O objetivo destas métricas é rastrear o desempenho da CDN para o tráfego HTTPS.

| Campo | Descrição |
| --- | --- |
| Eficiência segura da cache |Indica a percentagem de dados transferidos para pedidos HTTPS que foram servidos a partir de cache. Esta métrica mede quando uma versão em cache do conteúdo solicitado foi servida diretamente do CDN (servidores de borda) aos solicitadores (por exemplo, navegador da Web) sobre HTTPS. |
| Taxa de transferência segura |Indica a taxa média a que o conteúdo foi transferido do CDN (servidores de borda) para os solicitadores (por exemplo, servidores web) sobre HTTPS. |
| Duração média segura |Indica o tempo médio, em milissegundos, que levou para entregar um ativo a um solicitador (por exemplo, navegador web) sobre HTTPS. |
| Hits Seguros |Indica o número de pedidos HTTPS para o conteúdo da CDN. |
| Secure Bytes out |Indica a quantidade de tráfego HTTPS, em bytes, que foram entregues do CDN (servidores de borda) ao solicitador (por exemplo, navegador web). |

## <a name="reports"></a>Relatórios
Cada relatório deste módulo contém um gráfico e estatísticas sobre a largura de banda e a utilização do tráfego para diferentes tipos de métricas (por exemplo, códigos de estado HTTP, códigos de estado de cache, URL de pedido, etc.). Estas informações podem ser usadas para aprofundar a forma como os conteúdos estão a ser servidos aos seus clientes e para afinar o comportamento da CDN para melhorar o desempenho da entrega de dados.

### <a name="accessing-the-edge-performance-reports"></a>Aceder aos relatórios de desempenho da borda
1. A partir da lâmina de perfil CDN, clique no botão **Gerir.**
   
    ![Botão de gestão da lâmina de perfil CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    O portal de gestão cdn abre.
2. Passe por cima do **separador Analytics** e, em seguida, paire sobre o flyout **Edge Performance Analytics.**  Clique em **HTTP Large Object** .
   
    É apresentado o ecrã de relatórios de análise de nó de borda.

| Relatório | Descrição |
| --- | --- |
| Resumo Diário |Permite-lhe visualizar as tendências diárias de tráfego durante um período de tempo especificado. Cada barra deste gráfico representa uma data particular. O tamanho da barra indica o número total de acessos ocorridos nessa data. |
| Resumo horário |Permite-lhe visualizar as tendências de tráfego de hora em hora durante um período de tempo especificado. Cada barra neste gráfico representa uma única hora numa data específica. O tamanho da barra indica o número total de acessos ocorridos durante aquela hora. |
| Protocolos |Apresenta a avaria de tráfego entre os protocolos HTTP e HTTPS. Um gráfico de donuts indica a percentagem de acessos que ocorreram para cada tipo de protocolo. |
| Métodos HTTP |Permite-lhe ter uma rápida noção dos métodos HTTP que estão a ser utilizados para solicitar os seus dados. Tipicamente, os métodos de pedido HTTP mais comuns são GET, HEAD e POST. Um gráfico de donuts indica a percentagem de acessos que ocorreram para cada tipo de método de pedido HTTP. |
| URLs |Contém um gráfico que exibe os 10 URLs solicitados. É apresentada uma barra para cada URL. A altura da barra indica quantos acessos esse URL em particular gerou ao longo do tempo coberto pelo relatório. As estatísticas dos 100 URLs solicitados são apresentadas diretamente abaixo deste gráfico. |
| CNAMEs |Contém um gráfico que exibe as 10 melhores CNAMEs utilizadas para solicitar ativos durante o período de tempo de um relatório. As estatísticas relativas às 100 CNAMEs solicitadas são apresentadas diretamente abaixo deste gráfico. |
| Origens |Contém um gráfico que exibe os 10 melhores servidores de origem do CDN ou do cliente a partir dos quais os ativos foram solicitados durante um determinado período de tempo. As estatísticas dos 100 melhores cdn solicitados ou servidores de origem do cliente são apresentadas diretamente abaixo deste gráfico. Os servidores de origem do cliente são identificados pelo nome definido na opção Nome do Diretório. |
| Geo POPs |Mostra quanto do seu tráfego está a ser encaminhado através de um determinado ponto de presença (POP). A abreviatura de três letras representa um POP na nossa rede CDN. |
| Clientes |Contém um gráfico que exibe os 10 melhores clientes que solicitaram ativos durante um determinado período de tempo. Para efeitos deste relatório, todos os pedidos originários do mesmo endereço IP são considerados do mesmo cliente. As estatísticas dos 100 melhores clientes são apresentadas diretamente abaixo deste gráfico. Este relatório é útil para determinar padrões de atividade de descarregamento para os seus clientes de topo. |
| Estatuto cache |Dá uma desagregação detalhada do comportamento da cache, que pode revelar abordagens para melhorar a experiência geral do utilizador final. Uma vez que o desempenho mais rápido vem dos hits de cache, você pode otimizar velocidades de entrega de dados minimizando falhas de cache e cache expirado hits de cache. |
| NENHUM Detalhe |Contém um gráfico que exibe os 10 URLs mais altos para ativos para os quais a frescura do conteúdo da cache não foi verificada durante um determinado período de tempo. As estatísticas dos 100 URLs mais altos para este tipo de ativos são apresentadas diretamente abaixo deste gráfico. |
| Detalhes CONFIG_NOCACHE |Contém um gráfico que exibe os 10 URLs mais altos para ativos que não foram em cache devido à configuração do CDN do cliente. Estes tipos de ativos foram servidos diretamente a partir do servidor de origem. As estatísticas dos 100 URLs mais altos para este tipo de ativos são apresentadas diretamente abaixo deste gráfico. |
| Detalhes incaqueáveis |Contém um gráfico que exibe os 10 URLs mais altos para ativos que não puderam ser em cache devido a dados de cabeçalho solicitados. As estatísticas dos 100 URLs mais altos para este tipo de ativos são apresentadas diretamente abaixo deste gráfico. |
| Detalhes TCP_HIT |Contém um gráfico que exibe os 10 URLs mais altos para ativos que são servidos imediatamente a partir de cache. As estatísticas dos 100 URLs mais altos para este tipo de ativos são apresentadas diretamente abaixo deste gráfico. |
| Detalhes TCP_MISS |Contém um gráfico que exibe os 10 URLs mais elevados para ativos que tenham um estatuto de cache de TCP_MISS. As estatísticas dos 100 URLs mais altos para este tipo de ativos são apresentadas diretamente abaixo deste gráfico. |
| Detalhes TCP_EXPIRED_HIT |Contém um gráfico que exibe os 10 URLs mais antigos para ativos antigos que foram servidos diretamente do POP. As estatísticas dos 100 URLs mais altos para este tipo de ativos são apresentadas diretamente abaixo deste gráfico. |
| Detalhes TCP_EXPIRED_MISS |Contém um gráfico que exibe os 10 URLs mais antigos para os ativos em contrao qual uma nova versão teve de ser recuperada a partir do servidor de origem. As estatísticas dos 100 URLs mais altos para este tipo de ativos são apresentadas diretamente abaixo deste gráfico. |
| Detalhes TCP_CLIENT_REFRESH_MISS |Contém um gráfico de barras que exibe os 10 MELHOREs URLs para ativos foram recuperados de um servidor de origem devido a um pedido sem cache do cliente. As estatísticas dos 100 URLs mais altos para este tipo de pedidos são apresentadas diretamente abaixo deste gráfico. |
| Tipos de Pedidos de Cliente |Indica o tipo de pedidos que foram feitos por clientes HTTP (por exemplo, navegadores). Este relatório inclui um gráfico de donuts que dá uma ideia da forma como os pedidos estão a ser tratados. A largura de banda e as informações de tráfego para cada tipo de pedido são apresentadas abaixo da tabela. |
| Agente utilizador |Contém um gráfico de barras que mostra os 10 principais agentes de utilizador para solicitar o seu conteúdo através do nosso CDN. Normalmente, um agente de utilizadores é um navegador web, leitor de mídia ou um navegador de telemóvel. As estatísticas dos 100 principais agentes utilizadores são apresentadas diretamente abaixo deste gráfico. |
| Remetentes |Contém um gráfico de barras que mostra os 10 principais remetentes aos conteúdos acedidos através do nosso CDN. Normalmente, um remetente é o URL da página web ou recurso que se liga ao seu conteúdo. Informações detalhadas são fornecidas abaixo do gráfico para os 100 melhores remetentes. |
| Tipos de compressão |Contém um gráfico de donuts que decompõe os ativos solicitados se foram comprimidos pelos nossos servidores de borda. A percentagem de ativos comprimidos é decompoda pelo tipo de compressão utilizada. Informações detalhadas são fornecidas abaixo do gráfico para cada tipo e estado de compressão. |
| Tipos de Ficheiros |Contém um gráfico de barras que exibe os 10 melhores tipos de ficheiros que foram solicitados através do nosso CDN para a sua conta. Para efeitos deste relatório, um tipo de ficheiro é definido pela extensão do nome do ficheiro do ativo e pelo tipo de mídia da Internet (por exemplo, .html \[ text/html \] , .htm \[ text/html \] , .aspx \[ text/html \] , etc.). Informações detalhadas são fornecidas abaixo do gráfico para os 100 melhores tipos de ficheiros. |
| Ficheiros Únicos |Contém um gráfico que traça o número total de ativos únicos que foram solicitados num determinado dia durante um determinado período de tempo. |
| Resumo token Auth |Contém um gráfico de tartes que fornece uma visão geral rápida sobre se os ativos solicitados foram protegidos por Token-Based Autenticação. Os ativos protegidos são apresentados na tabela de acordo com os resultados da sua tentativa de autenticação. |
| Token Auth Negar Detalhes |Contém um gráfico de barras que lhe permite ver os 10 principais pedidos que foram negados devido à Token-Based Autenticação. |
| Códigos de resposta HTTP |Fornece uma desagregação dos códigos de estado HTTP (por exemplo, 200 OK, 403 Proibidos, 404 Não Encontrados, etc.) que foram entregues aos seus clientes HTTP pelos nossos servidores de borda. Um gráfico de tartes permite-lhe avaliar rapidamente como os seus bens foram servidos. São fornecidos dados estatísticos detalhados para cada código de resposta abaixo do gráfico. |
| 404 Erros |Contém um gráfico de barras que lhe permite visualizar os 10 principais pedidos que resultaram num código de resposta 404 Não Encontrado. |
| 403 Erros |Contém um gráfico de barras que lhe permite ver os 10 principais pedidos que resultaram num código de resposta 403 Proibido. Um código de resposta 403 Proibido ocorre quando um pedido é negado por um servidor de origem do cliente ou por um servidor de borda no nosso POP. |
| Erros 4xx |Contém um gráfico de barras que lhe permite ver os 10 principais pedidos que resultaram num código de resposta na gama 400. Excluídos deste relatório estão 403 Não Encontrados e 404 códigos de resposta proibidos. Normalmente, um código de resposta 4xx ocorre quando um pedido é negado como resultado de um erro do cliente. |
| 504 Erros |Contém um gráfico de barras que lhe permite visualizar os 10 principais pedidos que resultaram num código de resposta de tempo de gateway 504. Um código de resposta de tempo de gateway 504 ocorre quando ocorre um tempo limite quando um representante http está a tentar comunicar com outro servidor. No caso do nosso CDN, um código de resposta de tempo de gateway 504 ocorre normalmente quando um servidor de borda é incapaz de estabelecer comunicação com um servidor de origem do cliente. |
| 502 Erros |Contém um gráfico de barras que lhe permite ver os 10 principais pedidos que resultaram num código de resposta Bad Gateway 502. Um código de resposta Bad Gateway 502 ocorre quando ocorre uma falha do protocolo HTTP entre um servidor e um representante HTTP. No caso do nosso CDN, um código de resposta Bad Gateway 502 ocorre normalmente quando um servidor de origem do cliente devolve uma resposta inválida a um servidor de borda. Uma resposta é inválida se não puder ser analisada ou se estiver incompleta. |
| Erros 5xx |Contém um gráfico de barras que lhe permite ver os 10 principais pedidos que resultaram num código de resposta na gama 500.  Excluídos deste relatório estão 502 Bad Gateway e 504 códigos de resposta gateway timeout. |

## <a name="see-also"></a>Ver também
* [Descrição geral da CDN do Azure](cdn-overview.md)
* [Estatísticas em tempo real no Microsoft Azure CDN](cdn-real-time-stats.md)
* [Comportamento HTTP predefinido que predefinindo usando o motor de regras](./cdn-verizon-premium-rules-engine.md)
* [Advanced HTTP Reports](cdn-advanced-http-reports.md) (Relatórios HTTP avançados)