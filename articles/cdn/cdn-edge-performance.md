---
title: Analisar o desempenho de nó de extremidade na CDN do Azure | Documentos da Microsoft
description: Analise o desempenho de nó de extremidade na CDN do Microsoft Azure. Análise de desempenho do Edge fornece a utilização de largura de banda e de tráfego de informação granular para a CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 7dfa252c29121adca2ecc77c08b2fca81d56e575
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61070101"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analisar o desempenho do nó de extremidade na CDN do Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral
Análise de desempenho do Edge fornece a utilização de largura de banda e de tráfego de informação granular para a CDN. Estas informações, em seguida, podem ser utilizadas para gerar estatísticas de tendência, que lhe permitem obter conhecimentos aprofundados sobre como os seus ativos estão a ser colocados em cache e fornecidos aos seus clientes. Por sua vez, isto permite-lhe formar uma estratégia sobre como otimizar a entrega do seu conteúdo e determinar quais problemas devem ser resolvidos para aproveitar melhor a CDN. Como resultado, não apenas poderá melhorar o desempenho de entrega de dados, mas também poderá reduzir os custos CDN.

> [!NOTE]
> Todos os relatórios utilizam a notação de UTC/GMT ao especificar uma data/hora.
> 
> 

## <a name="reports-and-log-collection"></a>Recolha de registos e relatórios
Dados de atividade da CDN têm de ser recolhidos pelo módulo de análise de desempenho do Edge antes de pode gerar relatórios no mesmo. Este processo de coleta ocorre uma vez por dia e ele abrange a atividade que ocorria durante o dia anterior. Isso significa que as estatísticas de um relatório representam uma amostra de estatísticas do dia ao tempo ele foi processada e não necessariamente conter o conjunto completo de dados para o dia atual. É a função principal destes relatórios avaliar o desempenho. Não deve ser utilizadas para efeitos de faturação ou da estatística de numérico exata.

> [!NOTE]
> Os dados não processados a partir da qual são gerados relatórios de análise de desempenho do Edge estão disponíveis durante, pelo menos, 90 dias.
> 
> 

## <a name="dashboard"></a>Dashboard
O dashboard de análise de desempenho de borda controla atual e histórico o tráfego CDN através de um gráfico e estatísticas. Utilize este dashboard para detetar as tendências de longo prazo e mais recentes sobre o desempenho de tráfego CDN para a sua conta.

Este dashboard é composta por:

* Um gráfico interativo que permite a visualização de métricas-chave e tendências.
* Uma linha do tempo que fornece uma noção dos padrões de longo prazo para as principais métricas e tendências.
* As principais métricas e informações estatísticas sobre como nossa rede CDN melhora o tráfego do site conforme as medidas de desempenho, utilização e eficiência geral.

### <a name="accessing-the-edge-performance-dashboard"></a>Aceder ao dashboard de desempenho do edge
1. No painel de perfil da CDN, clique a **gerir** botão.
   
    ![Botão de gerir do painel do perfil CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    É aberto o portal de gestão da CDN.
2. Paire o rato sobre o **Analytics** separador, em seguida, coloque o cursor sobre o **análise de desempenho do Edge** submenu.  Clique em **Dashboard**.
   
    É apresentado o dashboard de análise de nó de extremidade.

### <a name="chart"></a>Gráfico
O dashboard contém um gráfico que controla uma métrica ao longo do período de tempo selecionado na linha da tempo que aparece diretamente abaixo da mesma.  Uma linha do tempo que gráficos a cópia de segurança para os últimos dois anos de atividade CDN é apresentada diretamente abaixo do gráfico.

#### <a name="using-the-chart"></a>Com o gráfico
* Por predefinição, a taxa de eficiência de cache para os últimos 30 dias será ser colocada em gráficos.
* Este gráfico é gerado a partir de dados agrupados numa base diária.
* Pairar o rato sobre um dia no gráfico de linha irá indicar uma data e o valor da métrica nessa data.
* Clique em realce fins de semana para ativar/desativar uma sobreposição de luz barras verticais cinza que representam os fins de semana para o gráfico. Este tipo de sobreposição é útil para identificar padrões de tráfego ao longo da semana.
* Clique em vista um ano atrás para ativar/desativar uma sobreposição de atividade do ano durante o mesmo período de tempo para o gráfico. Este tipo de comparação fornece informações sobre padrões de utilização da CDN longo prazo. Canto do canto superior direito do gráfico contém uma legenda que indica o código de cor para cada gráfico de linha.

#### <a name="updating-the-chart"></a>A atualizar o gráfico
* Intervalo de tempo: Efetue um dos seguintes procedimentos:
  * Selecione a região desejada na linha da tempo. O gráfico será atualizado com os dados que corresponde ao período de tempo selecionado.
  * Faça duplo clique no gráfico para apresentar todos os dados históricos disponíveis até um máximo de dois anos.
* Métrica: Clique no ícone de gráfico é apresentado junto a métrica pretendida. O gráfico e a linha do tempo são atualizados com os dados para a métrica correspondente.

### <a name="key-metrics-and-statistics"></a>As principais métricas e estatísticas
#### <a name="efficiency-metrics"></a>Métricas de eficiência
É a finalidade destas métricas ver se a eficiência da cache pode ser melhorada. Os principais benefícios derivados de eficiência de cache são:

* Carga reduzida no servidor de origem que pode levar a:
  * Melhor desempenho do servidor web.
  * Redução de custos operacional.
* Melhorada a aceleração de entrega de dados, uma vez que mais pedidos serão fornecidos diretamente a partir da CDN.

| Campo | Descrição |
| --- | --- |
| Eficiência da cache |Indica a percentagem dos dados transferidos que tiver sido servidos a partir da cache. Esta métrica medidas quando uma versão em cache do conteúdo pedido foi servido diretamente a partir da CDN (servidores de borda) para os solicitantes (por exemplo, navegador da web) |
| Taxa de acertos |Indica a percentagem de pedidos que foram servidos a partir da cache. Esta métrica medidas quando uma versão em cache do conteúdo pedido foi servido diretamente a partir da CDN (servidores de borda) para os solicitantes (por exemplo, navegador da web). |
| % de Bytes remotos - nenhuma configuração de Cache |Indica a percentagem de tráfego que tiver sido servido a partir de servidores de origem para a CDN (servidores de borda) que não será possível em cache devido a funcionalidade de ignorar Cache (motor de regras de HTTP). |
| % de Bytes remotos - Cache expirada |Indica a percentagem de tráfego que tiver sido servido a partir de servidores de origem para a CDN (servidores de borda) como resultado obsoleta revalidação de conteúdo. |

#### <a name="usage-metrics"></a>Métricas de utilização
É a finalidade destas métricas fornecer informações sobre as seguintes medidas de custos:

* Minimizar os custos operacionais pela CDN.
* Reduzindo as despesas da CDN através de eficiência da cache e compressão.

> [!NOTE]
> Números de volume de tráfego representam o tráfego que foi utilizado em cálculos de taxas e percentagens e pode mostrar apenas uma parte do tráfego total para clientes de grande volume.
> 
> 

| Campo | Descrição |
| --- | --- |
| Bytes de ave |Indica o número médio de bytes transferidos para cada pedido servido a partir da CDN (servidores de borda) para o autor do pedido (por exemplo, navegador da web). |
| Sem taxa de bytes de configuração de Cache |Indica a percentagem de tráfego da CDN (servidores de borda) para o autor do pedido (por exemplo, navegador da web) que não será possível em cache devido à funcionalidade de ignorar a Cache. |
| Taxa de bytes comprimido |Indica a percentagem de tráfego enviado da CDN (servidores de borda) para os solicitantes (por exemplo, navegador da web) num formato compactado. |
| Bytes de respostas |Indica a quantidade de dados, em bytes, que foram entregues da CDN (servidores de borda) para o autor do pedido (por exemplo, navegador da web). |
| Bytes em |Indica a quantidade de dados, em bytes, enviados a partir dos solicitantes (por exemplo, navegador da web) para a CDN (servidores de borda). |
| Bytes remoto |Indica a quantidade de dados, em bytes, enviados a partir de servidores de origem CDN e o cliente para a CDN (servidores de borda). |

#### <a name="performance-metrics"></a>Métricas de Desempenho
É a finalidade destas métricas controlar o desempenho de CDN global para o tráfego.

| Campo | Descrição |
| --- | --- |
| Taxa de transferência |Indica a média da taxa em que o conteúdo que foi transferido da CDN para um autor de pedido. |
| Duração |Indica o tempo médio, em milissegundos, necessário para entregar um recurso de um autor de pedido (por exemplo, navegador da web). |
| Taxa de pedidos comprimido |Indica a percentagem de pedidos com êxito na foram entregues da CDN (servidores de borda) para o autor do pedido (por exemplo, navegador da web) num formato compactado. |
| Taxa de erros de 4xx |Indica a percentagem de resultados que geraram um código de estado de 4xx. |
| Taxa de erros de 5XX |Indica a percentagem de resultados que geraram um código de estado de 5xx. |
| Acertos |Indica o número de pedidos para conteúdo da CDN. |

#### <a name="secure-traffic-metrics"></a>Proteger o tráfego métricas
É a finalidade destas métricas controlar o desempenho de CDN para tráfego HTTPS.

| Campo | Descrição |
| --- | --- |
| Proteger a eficiência da Cache |Indica a percentagem dos dados transferidos para pedidos HTTPS que foram servidos da cache. Esta métrica medidas quando uma versão em cache do conteúdo pedido foi servido diretamente a partir da CDN (servidores de borda) para os solicitantes (por exemplo, navegador da web) através de HTTPS. |
| Taxa de transferência segura |Indica a média da taxa em que o conteúdo que foi transferido da CDN (servidores de borda) para os solicitantes (por exemplo, servidores web) através de HTTPS. |
| Duração média de segura |Indica o tempo médio, em milissegundos, necessário para entregar um recurso de um autor de pedido (por exemplo, navegador da web) através de HTTPS. |
| Acertos seguros |Indica o número de pedidos HTTPS para o conteúdo da CDN. |
| Bytes de seguros |Indica a quantidade de tráfego HTTPS, em bytes, que foram entregues da CDN (servidores de borda) para o autor do pedido (por exemplo, navegador da web). |

## <a name="reports"></a>Relatórios
Cada relatório neste módulo contém um gráfico e estatísticas sobre o uso de largura de banda e o tráfego de diferentes tipos de métricas (por exemplo, códigos de estado HTTP, códigos de estado de cache, pedir URL, etc.). Estas informações podem ser utilizadas para aprofundar-se na forma como o conteúdo é que está a ser fornecido aos seus clientes e para otimizar o comportamento da CDN para melhorar o desempenho de entrega de dados.

### <a name="accessing-the-edge-performance-reports"></a>Acessando os relatórios de desempenho do edge
1. No painel de perfil da CDN, clique a **gerir** botão.
   
    ![Botão de gerir do painel do perfil CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    É aberto o portal de gestão da CDN.
2. Paire o rato sobre o **Analytics** separador, em seguida, coloque o cursor sobre o **análise de desempenho do Edge** submenu.  Clique em **HTTP de objeto grande**.
   
    É apresentado o ecrã de relatórios de análise de nó edge.

| Relatório | Descrição |
| --- | --- |
| Resumo diário |Pode ver as tendências diárias de tráfego ao longo do período de tempo especificado. Cada barra nesse gráfico representa uma data específica. O tamanho da barra de indica o número total de acertos ocorridos nessa data. |
| Resumo de hora a hora |Permite-lhe ver as tendências de tráfego de hora a hora durante um período de tempo especificado. Cada barra nesse gráfico representa uma única hora numa data específica. O tamanho da barra de indica o número total de acertos que ocorreram durante essa hora. |
| Protocolos |Mostra a divisão de tráfego entre os protocolos HTTP e HTTPS. Um gráfico em anel que indica a porcentagem de resultados que ocorreram para cada tipo de protocolo. |
| Métodos HTTP |Pode obter uma noção rápida do que HTTP métodos estão a ser utilizados para pedir os seus dados. Normalmente, os métodos de pedido HTTP mais comuns são GET, HEAD e POST. Um gráfico em anel que indica a porcentagem de resultados que ocorreram para cada tipo de método de pedido HTTP. |
| URLs |Contém um gráfico que apresenta os principais URLs pedidas 10. Uma barra é exibida para cada URL. A altura da barra de indica quantos acessos gerado URL específica sobre o intervalo de tempo coberto pelo relatório. Estatísticas para a parte superior a 100 pedida que URLs são apresentados diretamente abaixo este gráfico. |
| CNAMEs |Contém um gráfico que mostra as principais intervalo de 10 CNAMEs utilizados para pedir a ativos no tempo de um relatório. Estatísticas para a parte superior a 100 pedida que CNAMEs são apresentados diretamente abaixo este gráfico. |
| Origens |Contém um gráfico que apresenta a CDN 10 principais ou servidores de origem do cliente do qual os recursos foram solicitados num determinado período de tempo. Estatísticas para a parte superior a 100 pedida CDN ou cliente de servidores de origem são apresentados diretamente abaixo este gráfico. Servidores de origem do cliente são identificados pelo nome definido na opção de nome de diretório. |
| Geo POPs |Mostra a quantidade do tráfego é que está a ser encaminhado através de uma determinado ponto de presença (POP). A abreviatura de três letras representa um preenchimento em nossa rede CDN. |
| Clientes |Contém um gráfico que apresenta os 10 clientes principais que solicitou ativos durante um determinado período de tempo. Para efeitos deste relatório, todos os pedidos provenientes do mesmo endereço IP são considerados ser do mesmo cliente. Estatísticas para os clientes de 100 principais são apresentadas diretamente abaixo este gráfico. Este relatório é útil para determinar os padrões de atividade de download para seus clientes principais. |
| Estados da cache |Mostra uma divisão detalhada de comportamento de cache, o que pode revelar abordagens para melhorar a experiência geral do utilizador final. Uma vez que o desempenho mais rápido é proveniente de acertos na cache, pode otimizar a velocidades de entrega de dados ao minimizar erros de cache e de acertos na cache expirada. |
| NENHUM de detalhes |Contém um gráfico que apresenta os 10 principais URLs para ativos para os quais a atualização de conteúdo de cache não foi verificada num determinado período de tempo. Estatísticas para os URLs de 100 principais para estes tipos de ativos são apresentadas diretamente abaixo este gráfico. |
| Detalhes CONFIG_NOCACHE |Contém um gráfico que apresenta os URLs de 10 principais para os recursos que não foram colocados em cache devido a uma configuração do cliente da CDN. Esses tipos de recursos foram servidos diretamente a partir do servidor de origem. Estatísticas para os URLs de 100 principais para estes tipos de ativos são apresentadas diretamente abaixo este gráfico. |
| ATRIBUIR detalhes |Contém um gráfico que apresenta os 10 principais URLs para os recursos que poderiam não ser colocado em cache devido a dados de cabeçalho do pedido. Estatísticas para os URLs de 100 principais para estes tipos de ativos são apresentadas diretamente abaixo este gráfico. |
| Detalhes TCP_HIT |Contém um gráfico que apresenta os 10 principais URLs de ativos que são servidos imediatamente a partir da cache. Estatísticas para os URLs de 100 principais para estes tipos de ativos são apresentadas diretamente abaixo este gráfico. |
| Detalhes TCP_MISS |Contém um gráfico que apresenta os 10 principais URLs para os recursos de que tem um Estado de cache de TCP_MISS. Estatísticas para os URLs de 100 principais para estes tipos de ativos são apresentadas diretamente abaixo este gráfico. |
| Detalhes TCP_EXPIRED_HIT |Contém um gráfico que apresenta os 10 principais URLs para obsoletos ativos que foram servidos diretamente a partir do POP. Estatísticas para os URLs de 100 principais para estes tipos de ativos são apresentadas diretamente abaixo este gráfico. |
| Detalhes TCP_EXPIRED_MISS |Contém um gráfico que apresenta os 10 principais URLs para obsoletos ativos para o qual uma nova versão precisava ser recuperada do servidor de origem. Estatísticas para os URLs de 100 principais para estes tipos de ativos são apresentadas diretamente abaixo este gráfico. |
| Detalhes TCP_CLIENT_REFRESH_MISS |Contém um gráfico de barras apresenta os 10 principais URLs para os recursos foram obtidos a partir de um servidor de origem devido a um pedido de não-cache do cliente. Estatísticas para os URLs de 100 principais para estes tipos de pedidos são apresentadas diretamente abaixo este gráfico. |
| Tipos de pedido do cliente |Indica o tipo de pedidos que foram feitas pelos clientes HTTP (por exemplo, browsers). Este relatório inclui um gráfico em anel que fornece uma noção sobre como os pedidos estão a ser processados. Informações de tráfego e de largura de banda para cada tipo de pedido são apresentadas abaixo do gráfico. |
| Agente de utilizador |Contém um gráfico de barras apresenta os principais agentes de utilizador de 10 para pedir o seu conteúdo através da nossa CDN. Normalmente, um agente de utilizador é um navegador da web, o leitor de multimédia ou um browser do telemóvel. Estatísticas para os agentes de 100 utilizadores principais são apresentadas diretamente abaixo este gráfico. |
| Referenciadores |Contém um gráfico de barras apresenta os 10 Referenciadores Principais para o conteúdo acedido através da nossa CDN. Normalmente, uma referência é o URL da página da web ou recurso que liga ao seu conteúdo. Informações detalhadas são fornecidas abaixo o gráfico para os Referenciadores 100 principais. |
| Tipos de compressão |Contém um gráfico em anel que divide ativos solicitados por se terem sido compactadas por nossos servidores de borda. A percentagem de ativos compactados é dividida pelo tipo de compactação utilizado. Informações detalhadas são fornecidas abaixo o gráfico para cada tipo de compactação e o estado. |
| Tipos de ficheiro |Contém um gráfico de barras apresenta os tipos de 10 ficheiro principais que foram solicitados por meio de nossa CDN para a sua conta. Para efeitos deste relatório, um tipo de ficheiro é definido pela extensão de nome de ficheiro do recurso e tipo de suporte de Internet (por exemplo,. HTML \[texto/html\],. htm \[texto/html\],. aspx \[texto/html\], etc.). Informações detalhadas são apresentadas abaixo o gráfico para os principais tipos de ficheiro de 100. |
| Ficheiros exclusivos |Contém um gráfico que representa o número total de ativos exclusivos que foram solicitados num dia específico num determinado período de tempo. |
| Token de autenticação de resumo |Contém um gráfico de pizza que fornece uma descrição geral rápida sobre se os recursos solicitados foram protegidos pela autenticação baseada em Token. Ativos protegidos são apresentados no gráfico de acordo com os resultados da sua autenticação tentativa. |
| Detalhes de negação de autenticação de token |Contém um gráfico de barras que lhe permite ver os pedidos de 10 principais que foram negados devido à autenticação baseada em tokens. |
| Códigos de resposta HTTP |Fornece uma repartição dos códigos de estado de HTTP (por exemplo, 200 OK, 403 proibido, 404 não encontrado, etc.) que foram entregues aos seus clientes HTTP por nossos servidores de borda. Um gráfico de pizza permite-lhe avaliar rapidamente como os recursos foram atendidos. Dados de estatísticos detalhados são fornecidos para cada código de resposta abaixo do gráfico. |
| 404 erros |Contém um gráfico de barras que lhe permite ver os pedidos de 10 principais que resultaram num código de resposta não foi encontrado 404. |
| 403 erros |Contém um gráfico de barras que lhe permite ver os pedidos de 10 principais que resultaram num código de resposta 403 Proibido. Um código de resposta 403 Proibido ocorre quando um pedido é negado por um servidor de origem do cliente ou um servidor de borda no nosso POP. |
| Erros de 4xx |Contém um gráfico de barras que lhe permite ver os pedidos de 10 principais que resultaram num código de resposta no intervalo de 400. Excluir a partir deste relatório são 403 não foi encontrado e códigos de resposta 404 proibido. Normalmente, um código de resposta 4xx ocorre quando um pedido é negado devido a um erro de cliente. |
| 504 erros |Contém um gráfico de barras que lhe permite ver os pedidos de 10 principais que resultaram num código de resposta 504 tempo limite do Gateway. Um código de resposta 504 tempo limite do Gateway ocorre quando um tempo limite ocorre quando um proxy HTTP está a tentar comunicar com outro servidor. No caso da nossa CDN, um código de resposta 504 tempo limite do Gateway normalmente ocorre quando um servidor de borda é não é possível estabelecer comunicação com um servidor de origem do cliente. |
| 502 erros |Contém um gráfico de barras que lhe permite ver os pedidos de 10 principais que resultaram num código de resposta 502 Gateway incorreto. Um código de resposta 502 Gateway incorreto ocorre quando ocorre uma falha de protocolo HTTP entre um servidor e um proxy de HTTP. No caso da nossa CDN, um código de resposta 502 Gateway incorreto normalmente ocorre quando um servidor de origem do cliente devolve uma resposta inválida para um servidor de borda. Uma resposta é inválida, se ele não é possível analisar ou se está incompleto. |
| Erros de 5XX |Contém um gráfico de barras que lhe permite ver os pedidos de 10 principais que resultaram num código de resposta no intervalo de 500.  Excluir a partir deste relatório são 502 Gateway incorreto e códigos de resposta do 504 tempo limite do Gateway. |

## <a name="see-also"></a>Consulte também
* [Descrição geral da CDN do Azure](cdn-overview.md)
* [Estatísticas em tempo real na CDN do Microsoft Azure](cdn-real-time-stats.md)
* [Substituir o comportamento HTTP padrão usando o mecanismo de regras](cdn-rules-engine.md)
* [Relatórios HTTP avançados](cdn-advanced-http-reports.md)

