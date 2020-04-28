---
title: Analise as estatísticas de utilização com relatórios de HTTP avançados do Azure CDN / Microsoft Docs
description: Saiba como criar relatórios http avançados no Microsoft Azure CDN. Estes relatórios fornecem informações detalhadas sobre a atividade da CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 0b0eec2425f8a1663eb7a09c83a6bad037d1d79c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67594100"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analisar estatísticas de utilização com relatórios de HTTP avançados do Azure CDN
## <a name="overview"></a>Descrição geral
Este documento explica relatórios avançados do HTTP no Microsoft Azure CDN. Estes relatórios fornecem informações detalhadas sobre a atividade da CDN.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Aceder a relatórios avançados de HTTP
1. A partir da lâmina de perfil CDN, clique no botão **Gerir.**
   
    ![Botão de gestão da lâmina de perfil CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    Abre o portal de gestão da CDN.
2. Paire sobre o separador **Analytics** e, em seguida, paire sobre o voo **advanced HTTP Reports.**  Clique em **HTTP Grande Plataforma**.
   
    ![Portal de gestão da CDN - Menu Relatórios Avançados](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    As opções de relatório são apresentadas.

## <a name="geography-reports-map-based"></a>Relatórios de Geografia (Baseado em Mapas)
Existem cinco relatórios que aproveitam um mapa para indicar as regiões a partir das quais o seu conteúdo está a ser solicitado. Estes relatórios são World Map, United States Map, Canada Map, Europe Map e Asia Pacific Map.

Cada relatório baseado em mapas classifica as entidades geográficas (ou seja, países/regiões, um mapa é fornecido para ajudá-lo a visualizar os locais a partir dos quais o seu conteúdo está sendo solicitado. É capaz de o fazer através da codificação de cores de cada região de acordo com a quantidade de procura sentida naquela região. Regiões sombreadas mais claras indicam uma menor procura pelo seu conteúdo, enquanto as regiões mais escuras indicam níveis mais elevados de procura pelo seu conteúdo.

Informações detalhadas sobre tráfego e largura de banda para cada região são fornecidas diretamente abaixo do mapa. Isto permite-lhe visualizar o número total de acessos, a percentagem de acessos, a quantidade total de dados transferidos (em gigabytes) e a percentagem de dados transferidos para cada região. Veja uma descrição para cada uma destas métricas. Finalmente, quando se sobressai sobre uma região (ou seja, país/região, estado ou província), o nome e a percentagem de acessos que ocorreram na região serão apresentados como uma dica de ferramentas.

Uma breve descrição é fornecida abaixo para cada tipo de relatório de geografia baseado em mapas.

| Nome do Relatório | Descrição |
| --- | --- |
| Mapa do Mundo |Este relatório permite-lhe visualizar a procura mundial pelo seu conteúdo em CDN. Cada país/região está codificado por cores no mapa mundial para indicar a percentagem de acessos originários daquela região. |
| Mapa dos Estados Unidos |Este relatório permite-lhe visualizar a procura do seu conteúdo em CDN nos Estados Unidos. Cada estado é codificado por cores neste mapa para indicar a percentagem de acessos que originaram daquela região. |
| Mapa do Canadá |Este relatório permite-lhe visualizar a procura do seu conteúdo em CDN no Canadá. Cada província é codificada por cores neste mapa para indicar a percentagem de acessos que originaram daquela região. |
| Mapa da Europa |Este relatório permite-lhe visualizar a procura do seu conteúdo em CDN na Europa. Cada país/região está codificado por cores neste mapa para indicar a percentagem de acessos originários daquela região. |
| Mapa do Pacífico Asiático |Este relatório permite-lhe visualizar a procura do seu conteúdo em CDN na Ásia. Cada país/região está codificado por cores neste mapa para indicar a percentagem de acessos originários daquela região. |

## <a name="geography-reports-bar-charts"></a>Relatórios de Geografia (Gráficos de Barras)
Existem dois relatórios adicionais que fornecem informações estatísticas de acordo com a geografia, que são as principais cidades e os países de topo. Estes relatórios classificam cidades e países/regiões, respectivamente, de acordo com o número de acessos originários desses países/regiões. Ao gerar este tipo de relatório, um gráfico de barras indicará as 10 principais cidades ou países/regiões que solicitaram conteúdo através de uma plataforma específica. Este gráfico de barras permite-lhe avaliar rapidamente as regiões que geram o maior número de pedidos para o seu conteúdo.

O lado esquerdo do gráfico (eixo y) indica quantos golpes ocorreram na região especificada. Diretamente abaixo do gráfico (eixo x), você encontrará um rótulo para cada uma das 10 regiões do top 10.

### <a name="using-the-bar-charts"></a>Usando os gráficos de barras
* Se pairar sobre um bar, o nome e o número total de acessos que ocorreram na região serão apresentados como uma dica de ferramenta.
* O relatório tooltip para o relatório Das Cidades Principais identifica uma cidade pelo seu nome, estado/província e abreviatura país/região.
* Se a cidade ou região (ou seja, estado/província) de onde um pedido teve origem não puder ser determinado, então indicará que são desconhecidas. Se o país/região é desconhecido, então serão exibidos dois pontos de interrogação (isto é, ??)
* Um relatório pode incluir métricas para a "Europa" ou para a "Região Ásia/Pacífico". Estes itens não se destinam a fornecer informações estatísticas sobre todos os endereços IP dessas regiões. Pelo contrário, aplicam-se apenas a pedidos originários de endereços IP que se espalham pela Europa ou Ásia/Pacífico em vez de a uma cidade ou país/região específicos.

Os dados que foram usados para gerar o gráfico de barras podem ser vistos abaixo dele. Lá encontrará o número total de acessos, a percentagem de acessos, a quantidade de dados transferidos (em gigabytes) e a percentagem de dados transferidos para as 250 regiões do top 250. Veja uma descrição para cada uma destas métricas.

É fornecida uma breve descrição para ambos os tipos de relatórios abaixo.

| Nome do Relatório | Descrição |
| --- | --- |
| Cidades de Topo |Este relatório classifica as cidades de acordo com o número de acessos que tiveram origem naquela região. |
| Países de Topo |Este relatório classifica os países/regiões de acordo com o número de acessos originários daquele país/região. |

## <a name="daily-summary"></a>Resumo Diário
O relatório Daily Summary permite-lhe visualizar diariamente o número total de acessos e dados transferidos através de uma determinada plataforma. Esta informação pode ser usada para discernir rapidamente os padrões de atividade da CDN. Por exemplo, este relatório pode ajudá-lo a detetar quais os dias que passaram por tráfego superior ou inferior ao esperado.

Ao gerar este tipo de relatório, um gráfico de barras fornecerá uma indicação visual quanto à quantidade de procura específica da plataforma experimentada diariamente durante o período de tempo abrangido pelo relatório. Fá-lo-á exibindo um bar para cada dia no relatório. Por exemplo, a seleção do período de tempo chamado "Semana Passada" gerará um gráfico de barras com sete barras. Cada barra indicará o número total de acessos experimentados nesse dia.

O lado esquerdo do gráfico (eixo y) indica quantos golpes ocorreram na data especificada. Diretamente abaixo do gráfico (eixo x), você encontrará um rótulo que indica a data (Formato: YYYY-MM-DD) para cada dia incluído no relatório.

> [!TIP]
> Se pairar sobre uma barra, o número total de acessos que ocorreram nessa data será apresentado como uma dica de ferramenta.
> 
> 

Os dados que foram usados para gerar o gráfico de barras podem ser vistos abaixo dele. Lá encontrará o número total de acessos e a quantidade de dados transferidos (em gigabytes) para cada dia abrangido pelo relatório.

## <a name="by-hour"></a>Por Hora
O relatório By Hour permite-lhe visualizar o número total de acessos e dados transferidos numa determinada plataforma de hora a hora. Esta informação pode ser usada para discernir rapidamente os padrões de atividade da CDN. Por exemplo, este relatório pode ajudá-lo a detetar os períodos de tempo durante o dia que experimentam tráfego superior ou inferior ao esperado.

Ao gerar este tipo de relatório, um gráfico de barras fornecerá uma indicação visual quanto à quantidade de procura específica da plataforma experimentada numa base horária durante o período abrangido pelo relatório. Fá-lo-á exibindo um bar por cada hora coberta pelo relatório. Por exemplo, a seleção de um período de tempo de 24 horas gerará um gráfico de barras com vinte e quatro barras. Cada barra indicará o número total de acessos experimentados durante essa hora.

O lado esquerdo do gráfico (eixo y) indica quantos golpes ocorreram na hora especificada. Diretamente abaixo do gráfico (eixo x), encontrará uma etiqueta que indica a data/hora (Formato: YYYY-MM-DD hh:mm) por cada hora incluída no relatório. O tempo é relatado utilizando o formato 24 horas e é especificado utilizando o fuso horário UTC/GMT.

> [!TIP]
> Se pairar sobre uma barra, o número total de acessos ocorridos durante essa hora será apresentado como uma dica de ferramenta.
> 
> 

Os dados que foram usados para gerar o gráfico de barras podem ser vistos abaixo dele. Lá encontrará o número total de acessos e a quantidade de dados transferidos (em gigabytes) por cada hora abrangida pelo relatório.

## <a name="by-file"></a>Por Arquivo
O relatório By File permite-lhe visualizar a quantidade de procura e o tráfego incorrido numa determinada plataforma para os ativos mais solicitados. Ao gerar este tipo de relatório, um gráfico de barras será gerado no top 10 dos ativos mais solicitados durante o período de tempo especificado.

> [!NOTE]
> Para efeitos deste relatório, os URLs CNAME de borda são convertidos para os seus URLs CDN equivalentes. Isto permite uma contagem precisa para o número total de acessos associados a um ativo, independentemente do URL CDN ou CNAME borda usado para o solicitar.
> 
> 

O lado esquerdo do gráfico (eixo y) indica o número de pedidos de cada ativo durante o período de tempo especificado. Diretamente abaixo do gráfico (eixo x), você encontrará uma etiqueta que indica o nome de ficheiro para cada um dos 10 principais ativos solicitados.

Os dados que foram usados para gerar o gráfico de barras podem ser vistos abaixo dele. Aí encontrará as seguintes informações para cada um dos 250 principais ativos solicitados: trajetória relativa, número total de acessos, percentagem de acessos, quantidade de dados transferidos (em gigabytes) e percentagem de dados transferidos.

## <a name="by-file-detail"></a>Por Detalhe de Arquivo
O relatório Por Arquivo permite-lhe visualizar a quantidade de procura e o tráfego incorrido numa determinada plataforma para um ativo específico. No topo deste relatório está o Arquivo Detalhes para opção. Esta opção fornece uma lista dos seus ativos mais solicitados na plataforma selecionada. Para gerar um relatório por pormenor de ficheiro, terá de selecionar o ativo desejado a partir da opção Dedados de Ficheiros. Após o que, um gráfico de barras indicará a quantidade de procura diária que gerou durante o período de tempo especificado.

O lado esquerdo do gráfico (y-eixo) indica o número total de pedidos que um ativo experimentou num determinado dia. Diretamente abaixo do gráfico (eixo x), você encontrará um rótulo que indica a data (Formato: YYYY-MM-DD) para o qual a procura de CDN pelo ativo foi reportada.

Os dados que foram usados para gerar o gráfico de barras podem ser vistos abaixo dele. Lá encontrará o número total de acessos e a quantidade de dados transferidos (em gigabytes) para cada dia abrangido pelo relatório.

## <a name="by-file-type"></a>Por Tipo de Arquivo
O relatório Por Tipo de Ficheiro permite-lhe visualizar a quantidade de procura e o tráfego incorrido por tipo de ficheiro. Ao gerar este tipo de relatório, um gráfico de donuts indicará a percentagem de acessos gerados pelos 10 melhores tipos de ficheiros.

> [!TIP]
> Se pairar sobre uma fatia no gráfico de donuts, o tipo de ficheiro de mídia de Internet será apresentado como uma dica de ferramenta.
> 
> 

Os dados que foram usados para gerar o gráfico de donuts podem ser vistos abaixo dele. Aí encontrará a extensão do nome de ficheiro/tipo de mídia internet, o número total de acessos, a percentagem de acessos, a quantidade de dados transferidos (em gigabytes) e a percentagem de dados transferidos para cada um dos 250 melhores tipos de ficheiros.

## <a name="by-directory"></a>Por Diretório
O relatório By Diretório permite-lhe visualizar a quantidade de procura e o tráfego incorrido numa determinada plataforma de conteúdo a partir de um diretório específico. Ao gerar este tipo de relatório, um gráfico de barras indicará o número total de acessos gerados pelo conteúdo nos 10 melhores diretórios.

### <a name="using-the-bar-chart"></a>Usando o gráfico de barras
* Passe por cima de um bar para ver o caminho relativo para o diretório correspondente.
* Os conteúdos armazenados numa subpasta de um diretório não contam no cálculo da procura por diretório. Este cálculo baseia-se exclusivamente no número de pedidos gerados para conteúdos armazenados no diretório real.
* Para efeitos deste relatório, os URLs CNAME de borda são convertidos para os seus URLs CDN equivalentes. Isto permite uma contagem precisa para todas as estatísticas associadas a um ativo, independentemente do URL CDN ou CNAME borda usado para o solicitar.

O lado esquerdo do gráfico (y-eixo) indica o número total de pedidos para o conteúdo armazenado nos seus 10 principais diretórios. Cada bar na tabela representa um diretório. Use o esquema de codificação de cores para combinar um bar com um diretório listado na secção Top 250 Full Directories.

Os dados que foram usados para gerar o gráfico de barras podem ser vistos abaixo dele. Aí encontrará as seguintes informações para cada um dos 250 principais diretórios: trajetória relativa, número total de acessos, percentagem de acessos, quantidade de dados transferidos (em gigabytes) e percentagem de dados transferidos.

## <a name="by-browser"></a>Por Browser
O relatório By Browser permite-lhe visualizar quais os navegadores utilizados para solicitar conteúdo. Ao gerar este tipo de relatório, um gráfico de tartes indicará a percentagem de pedidos tratados pelos 10 navegadores mais altos.

### <a name="using-the-pie-chart"></a>Usando o gráfico de tortas
* Paire sobre uma fatia na tabela de tortas para ver o nome e a versão de um navegador.
* Para efeitos deste relatório, cada combinação única de navegador/versão é considerada um navegador diferente.
* A fatia denominada "Outros" indica a percentagem de pedidos tratados por todos os outros navegadores e versões.

Os dados que foram usados para gerar o gráfico de tartes podem ser vistos abaixo dele. Aí encontrará o número de tipo/versão do navegador, o número total de acessos e a percentagem de acessos para cada um dos 250 navegadores mais bem classificados.

## <a name="by-referrer"></a>Por Referrer
O relatório By Referrer permite-lhe visualizar os principais referentes aos conteúdos na plataforma selecionada. Um remetente indica o nome de anfitrião a partir do qual um pedido foi gerado. Ao gerar este tipo de relatório, um gráfico de barras indicará a quantidade de procura (isto é, hits) gerada pelos 10 melhores referers.

O lado esquerdo do gráfico (y-eixo) indica o número total de pedidos que um ativo experimentou para cada remetente. Cada barra na tabela representa um remetente. Utilize o esquema de codificação de cores para combinar uma barra com um remetente listado na secção Top 250 Referrer.

Os dados que foram usados para gerar o gráfico de barras podem ser vistos abaixo dele. Lá encontrará o URL, o número total de acessos e a percentagem de acessos gerados por cada um dos 250 melhores referers.

## <a name="by-download"></a>Por Download
O relatório Por Download permite-lhe analisar padrões de descarregamento para os conteúdos mais solicitados. O topo do relatório contém um gráfico de barras que compara as tentativas de transferências com downloads concluídos para os 10 principais ativos solicitados. Cada barra é codificada por cores de acordo com uma tentativa de download (azul) ou um download completo (verde).

> [!NOTE]
> Para efeitos deste relatório, os URLs CNAME de borda são convertidos para os seus URLs CDN equivalentes. Isto permite uma contagem precisa para todas as estatísticas associadas a um ativo, independentemente do URL CDN ou CNAME borda usado para o solicitar.
> 
> 

O lado esquerdo do gráfico (y-eixo) indica o nome de ficheiro de cada um dos 10 principais ativos solicitados. Diretamente abaixo do gráfico (eixo x), encontrará etiquetas que indicam o número total de transferências tentadas/concluídas.

Diretamente abaixo do gráfico de barras, as seguintes informações serão listadas para os 250 principais ativos solicitados: caminho relativo (incluindo nome de ficheiro), número de vezes que foi descarregado para a conclusão, o número de vezes que foi solicitado, e a percentagem de pedidos que resultaram num download completo.

> [!TIP]
> O nosso CDN não é informado por um cliente HTTP (isto é, navegador) quando um ativo foi completamente descarregado. Como resultado, temos de calcular se um ativo foi completamente descarregado de acordo com os códigos de estado e pedidos de alcance byte. A primeira coisa que procuramos ao fazer este cálculo é se o pedido resulta num código de estado de 200 OK. Em caso afirmativo, analisamos os pedidos de byte-range para garantir que cobrem todo o ativo. Finalmente, comparamos a quantidade de dados transferidos para o tamanho do ativo solicitado. Se os dados transferidos forem iguais ou superiores ao tamanho do ficheiro e os pedidos de byte-range forem adequados para esse ativo, então o impacto será contado como um download completo.
> 
> Devido à natureza interpretativa deste relatório, deve ter em conta os seguintes pontos que possam alterar a coerência e a exatidão deste relatório.
> 
> * Os padrões de tráfego não podem ser previstos com precisão quando os agentes utilizadores se comportam de forma diferente. Isto pode produzir resultados de descarregamento concluídos superiores a 100%.
> * Os ativos que aproveitam o HTTP Progressive Download podem não estar representados com precisão por este relatório. Isto deve-se aos utilizadores que procuram diferentes posições num vídeo.
> 
> 

## <a name="by-404-errors"></a>Por 404 Erros
O relatório De 404 Erros permite identificar o tipo de conteúdo que gera o maior número de 404 códigos de estado não encontrados. O topo do relatório contém um gráfico de barras para os 10 melhores ativos para os quais um código de estado 404 não encontrado foi devolvido. Este gráfico de barras compara o número total de pedidos com pedidos que resultaram num código de estado não encontrado 404 para esses ativos. Cada barra é codificada por cores. Uma barra amarela é utilizada para indicar que o pedido resultou num código de estado 404 não encontrado. Uma barra vermelha é usada para indicar o número total de pedidos para o ativo.

> [!NOTE]
> Para efeitos do presente relatório, note o seguinte:
> 
> * Um sucesso representa qualquer pedido de um ativo independentemente do código de estado.
> * Os URLs De Borda CNAME são convertidos para os seus URLs CDN equivalentes. Isto permite uma contagem precisa para todas as estatísticas associadas a um ativo, independentemente do URL CDN ou CNAME borda usado para o solicitar.
> 
> 

O lado esquerdo do gráfico (y-eixo) indica o nome de ficheiro de cada um dos 10 principais ativos solicitados que resultaram num código de estado 404 não encontrado. Diretamente abaixo do gráfico (eixo x), encontrará etiquetas que indicam o número total de pedidos e o número de pedidos que resultaram num código de estado 404 não encontrado.

Imediatamente abaixo do gráfico de barras, as seguintes informações serão listadas para os 250 principais ativos solicitados: trajetória relativa (incluindo nome de ficheiro), número de pedidos que resultaram num código de estado 404 não encontrado, o número total de vezes que o ativo foi solicitado, e a percentagem de pedidos que resultaram num código de estado 404 não encontrado.

## <a name="see-also"></a>Consulte também
* [Descrição geral da CDN do Azure](cdn-overview.md)
* [Estatísticas em tempo real no CDN do Microsoft Azure](cdn-real-time-stats.md)
* [Sobrepor o comportamento do HTTP usando o motor de regras](cdn-rules-engine.md)
* [Analisar desempenho de borda](cdn-edge-performance.md)

