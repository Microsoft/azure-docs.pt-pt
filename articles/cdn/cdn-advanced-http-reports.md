---
title: Analise as estatísticas de utilização com o Azure CDN avançado HTTP reports / Microsoft Docs
description: Saiba como criar relatórios HTTP avançados no Microsoft Azure CDN. Estes relatórios fornecem informações detalhadas sobre a atividade da CDN.
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
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c06af1cc7f068070954669fc4ec269c9e679f278
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84886062"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analisar estatísticas de utilização com relatórios HTTP avançados da CDN do Azure
## <a name="overview"></a>Descrição geral
Este documento explica relatórios HTTP avançados no Microsoft Azure CDN. Estes relatórios fornecem informações detalhadas sobre a atividade da CDN.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Aceder a relatórios HTTP avançados
1. A partir da lâmina de perfil CDN, clique no botão **Gerir.**
   
    ![Botão de gestão da lâmina de perfil CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    O portal de gestão cdn abre.
2. Passe por cima do **separador Analytics** e, em seguida, paire sobre o voo **de relatórios HTTP Avançados.**  Clique na **plataforma HTTP Large**.
   
    ![Portal de gestão CDN - Menu Relatórios Avançados](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    As opções de relatório são apresentadas.

## <a name="geography-reports-map-based"></a>Relatórios de Geografia (Baseado em Mapas)
Há cinco relatórios que aproveitam um mapa para indicar as regiões a partir das quais o seu conteúdo está a ser solicitado. Estes relatórios são World Map, United States Map, Canada Map, Europe Map e Asia Pacific Map.

Cada relatório baseado em mapas classifica as entidades geográficas (por assim dizer, países/regiões, um mapa para ajudá-lo a visualizar os locais a partir dos quais o seu conteúdo está a ser solicitado. É capaz de fazê-lo codificando cada região de acordo com a quantidade de procura sentida naquela região. Regiões sombreadas mais claras indicam uma menor procura pelo seu conteúdo, enquanto as regiões mais escuras indicam níveis mais elevados de procura para o seu conteúdo.

Informações detalhadas sobre o tráfego e largura de banda para cada região são fornecidas diretamente abaixo do mapa. Isto permite-lhe visualizar o número total de acessos, a percentagem de acessos, a quantidade total de dados transferidos (em gigabytes) e a percentagem de dados transferidos para cada região. Veja uma descrição para cada uma destas métricas. Finalmente, quando se paira sobre uma região (por exemplo, país/região, estado ou província), o nome e a percentagem de acessos ocorridos na região serão apresentados como uma ponta de ferramentas.

Uma breve descrição é fornecida abaixo para cada tipo de relatório de geografia baseado em mapas.

| Nome do Relatório | Descrição |
| --- | --- |
| Mapa do Mundo |Este relatório permite-lhe visualizar a procura mundial pelo seu conteúdo cdn. Cada país/região é codificado por cores no mapa mundial para indicar a percentagem de acessos originários daquela região. |
| Mapa dos Estados Unidos |Este relatório permite-lhe visualizar a procura do seu conteúdo de CDN nos Estados Unidos. Cada estado é codificado por cores neste mapa para indicar a percentagem de acessos que se originaram daquela região. |
| Mapa do Canadá |Este relatório permite-lhe visualizar a procura do seu conteúdo cdn no Canadá. Cada província é codificada por cores neste mapa para indicar a percentagem de acessos que se originaram daquela região. |
| Mapa da Europa |Este relatório permite-lhe visualizar a procura do seu conteúdo de CDN na Europa. Cada país/região é codificado por cores neste mapa para indicar a percentagem de acessos originários dessa região. |
| Mapa do Pacífico Asiático |Este relatório permite-lhe visualizar a procura do seu conteúdo de CDN na Ásia. Cada país/região é codificado por cores neste mapa para indicar a percentagem de acessos originários dessa região. |

## <a name="geography-reports-bar-charts"></a>Relatórios de Geografia (Gráficos de Barras)
Existem dois relatórios adicionais que fornecem informações estatísticas de acordo com a geografia, que são as principais cidades e países de topo. Estes relatórios classificam as cidades e países/regiões, respectivamente, de acordo com o número de acessos originários desses países/regiões. Ao gerar este tipo de relatório, um gráfico de barras indicará as 10 principais cidades ou países/regiões que solicitaram conteúdo através de uma plataforma específica. Este gráfico de barras permite-lhe avaliar rapidamente as regiões que geram o maior número de pedidos para o seu conteúdo.

O lado esquerdo do gráfico (eixo y) indica quantos hits ocorreram na região especificada. Diretamente abaixo do gráfico (eixo x), encontrará um rótulo para cada uma das 10 regiões mais bem classificadas.

### <a name="using-the-bar-charts"></a>Usando os gráficos de barras
* Se pairar sobre uma barra, o nome e o número total de acessos que ocorreram na região serão apresentados como uma ponta de ferramenta.
* A dica de ferramentas para o relatório das Principais Cidades identifica uma cidade pelo seu nome, estado/província e abreviatura de país/região.
* Se a cidade ou região (ou seja, estado/província) de onde foi originado um pedido não puder ser determinado, então indicará que são desconhecidas. Se o país/região for desconhecido, então serão exibidos dois pontos de interrogação (isto é, ??)
* Um relatório pode incluir métricas para a "Europa" ou para a "Região Ásia/Pacífico". Estes elementos não se destinam a fornecer informações estatísticas sobre todos os endereços IP nessas regiões. Pelo contrário, aplicam-se apenas aos pedidos originários de endereços IP que se espalham pela Europa ou Ásia/Pacífico em vez de para uma cidade ou país/região específico.

Os dados utilizados para gerar o gráfico de barras podem ser vistos abaixo. Lá encontrará o número total de acessos, a percentagem de acessos, a quantidade de dados transferidos (em gigabytes) e a percentagem de dados transferidos para as 250 regiões mais bem-conhecidas. Veja uma descrição para cada uma destas métricas.

Uma breve descrição é fornecida para ambos os tipos de relatórios abaixo.

| Nome do Relatório | Descrição |
| --- | --- |
| Principais Cidades |Este relatório classifica as cidades de acordo com o número de acessos originários daquela região. |
| Países de Topo |Este relatório classifica os países/regiões de acordo com o número de acessos originários desse país/região. |

## <a name="daily-summary"></a>Resumo Diário
O relatório Do Resumo Diário permite-lhe visualizar diariamente o número total de acessos e dados transferidos através de uma determinada plataforma. Esta informação pode ser usada para discernir rapidamente os padrões de atividade da CDN. Por exemplo, este relatório pode ajudá-lo a detetar quais dias experimentaram tráfego superior ou inferior ao esperado.

Ao gerar este tipo de relatório, um gráfico de barras fornecerá uma indicação visual sobre a quantidade de procura específica da plataforma experimentada diariamente durante o período abrangido pelo relatório. Fá-lo-á exibindo um bar para cada dia no relatório. Por exemplo, selecionar o período de tempo chamado "Semana Passada" gerará um gráfico de barras com sete barras. Cada barra indicará o número total de acessos experimentados nesse dia.

O lado esquerdo do gráfico (eixo y) indica quantos hits ocorreram na data especificada. Diretamente abaixo do gráfico (eixo x), encontrará uma etiqueta que indica a data (Formato: YYYY-MM-DD) para cada dia incluído no relatório.

> [!TIP]
> Se pairar sobre uma barra, o número total de acessos ocorridos nessa data será apresentado como uma ponta de ferramenta.
> 
> 

Os dados utilizados para gerar o gráfico de barras podem ser vistos abaixo. Lá encontrará o número total de acessos e a quantidade de dados transferidos (em gigabytes) para cada dia abrangido pelo relatório.

## <a name="by-hour"></a>De hora em hora
O relatório By Hour permite-lhe visualizar o número total de acessos e dados transferidos numa determinada plataforma numa determinada plataforma de hora em hora. Esta informação pode ser usada para discernir rapidamente os padrões de atividade da CDN. Por exemplo, este relatório pode ajudá-lo a detetar os períodos de tempo durante o dia que experimentam tráfego superior ou inferior ao esperado.

Ao gerar este tipo de relatório, um gráfico de barras fornecerá uma indicação visual sobre a quantidade de procura específica da plataforma experimentada de hora a hora durante o período abrangido pelo relatório. Fá-lo-á exibindo um bar para cada hora abrangida pelo relatório. Por exemplo, selecionar um período de 24 horas gerará um gráfico de barras com vinte e quatro barras. Cada barra indicará o número total de acessos experimentados durante essa hora.

O lado esquerdo do gráfico (eixo y) indica quantos golpes ocorreram na hora especificada. Diretamente abaixo do gráfico (eixo x), encontrará uma etiqueta que indica a data/hora (Formato: YYYYY-MM-DD hh:mm) por cada hora incluída no relatório. O tempo é relatado usando o formato 24 horas e é especificado usando o fuso horário UTC/GMT.

> [!TIP]
> Se pairar sobre uma barra, o número total de batidas que ocorreram durante essa hora será apresentado como uma ponta de ferramenta.
> 
> 

Os dados utilizados para gerar o gráfico de barras podem ser vistos abaixo. Lá encontrará o número total de acessos e a quantidade de dados transferidos (em gigabytes) por cada hora abrangida pelo relatório.

## <a name="by-file"></a>Por Arquivo
O relatório By File permite-lhe visualizar a quantidade de procura e o tráfego incorrido numa determinada plataforma para os ativos mais solicitados. Ao gerar este tipo de relatório, um gráfico de barras será gerado no top 10 dos ativos mais solicitados durante o período de tempo especificado.

> [!NOTE]
> Para efeitos deste relatório, os URLs CNAME de borda são convertidos para os seus URLs CDN equivalentes. Isto permite uma contagem precisa para o número total de acessos associados a um ativo, independentemente do URL CDN ou edge CNAME usado para o solicitar.
> 
> 

O lado esquerdo do gráfico (eixo y) indica o número de pedidos de cada ativo durante o período de tempo especificado. Diretamente abaixo do gráfico (eixo x), encontrará uma etiqueta que indica o nome do ficheiro para cada um dos 10 principais ativos solicitados.

Os dados utilizados para gerar o gráfico de barras podem ser vistos abaixo. Lá encontrará as seguintes informações para cada um dos 250 principais ativos solicitados: trajetória relativa, número total de acessos, percentagem de acessos, quantidade de dados transferidos (em gigabytes) e a percentagem de dados transferidos.

## <a name="by-file-detail"></a>Por Detalhe de Arquivo
O relatório By File Detail permite-lhe visualizar a quantidade de procura e o tráfego incorrido numa determinada plataforma para um ativo específico. No topo deste relatório está a opção Detalhes do Ficheiro. Esta opção fornece uma lista dos seus ativos mais solicitados na plataforma selecionada. Para gerar um relatório por detalhe de ficheiro, terá de selecionar o ativo pretendido a partir da opção Detalhes do Ficheiro. Após o qual, um gráfico de barras indicará a quantidade de procura diária que gerou durante o período de tempo especificado.

O lado esquerdo do gráfico (eixo y) indica o número total de pedidos que um ativo experimentou num determinado dia. Diretamente abaixo do gráfico (eixo x), encontrará uma etiqueta que indica a data (Formato: YYYY-MM-DD) para a qual a procura de CDN para o ativo foi reportada.

Os dados utilizados para gerar o gráfico de barras podem ser vistos abaixo. Lá encontrará o número total de acessos e a quantidade de dados transferidos (em gigabytes) para cada dia abrangido pelo relatório.

## <a name="by-file-type"></a>Por Tipo de Arquivo
O relatório By File Type permite-lhe visualizar a quantidade de procura e o tráfego incorrido pelo tipo de ficheiro. Ao gerar este tipo de relatório, um gráfico de donuts indicará a percentagem de acessos gerados pelos 10 melhores tipos de ficheiros.

> [!TIP]
> Se pairar sobre uma fatia na tabela de donuts, o tipo de mídia de Internet desse tipo de ficheiro será apresentado como uma ponta de ferramenta.
> 
> 

Os dados utilizados para gerar o gráfico de donuts podem ser vistos abaixo. Lá encontrará a extensão do nome do ficheiro/tipo de mídia internet, o número total de acessos, a percentagem de acessos, a quantidade de dados transferidos (em gigabytes) e a percentagem de dados transferidos para cada um dos 250 principais tipos de ficheiros.

## <a name="by-directory"></a>Por Diretório
O relatório by directory permite-lhe visualizar a quantidade de procura e o tráfego incorrido sobre uma determinada plataforma de conteúdos a partir de um diretório específico. Ao gerar este tipo de relatório, um gráfico de barras indicará o número total de acessos gerados pelo conteúdo nos 10 principais diretórios.

### <a name="using-the-bar-chart"></a>Usando o gráfico de barras
* Passe sobre uma barra para ver o caminho relativo para o diretório correspondente.
* Os conteúdos armazenados numa sub-dobradeira de um diretório não contam no cálculo da procura por diretório. Este cálculo baseia-se unicamente no número de pedidos gerados para conteúdos armazenados no diretório real.
* Para efeitos deste relatório, os URLs CNAME de borda são convertidos para os seus URLs CDN equivalentes. Isto permite uma contagem precisa para todas as estatísticas associadas a um ativo, independentemente do URL CDN ou edge CNAME usado para o solicitar.

O lado esquerdo do gráfico (eixo y) indica o número total de pedidos para o conteúdo armazenado nos seus 10 principais diretórios. Cada barra na tabela representa um diretório. Utilize o esquema de codificação de cores para combinar um bar com um diretório listado na secção Top 250 Full Directies.

Os dados utilizados para gerar o gráfico de barras podem ser vistos abaixo. Lá encontrará as seguintes informações para cada um dos 250 principais diretórios: percurso relativo, número total de acessos, percentagem de acessos, quantidade de dados transferidos (em gigabytes) e percentagem de dados transferidos.

## <a name="by-browser"></a>Por Browser
O relatório By Browser permite-lhe ver quais os navegadores utilizados para solicitar conteúdo. Ao gerar este tipo de relatório, um gráfico de tartes indicará a percentagem de pedidos tratados pelos 10 principais navegadores.

### <a name="using-the-pie-chart"></a>Usando o gráfico de tortas
* Passe por cima de uma fatia na tabela de tortas para ver o nome e a versão de um navegador.
* Para efeitos deste relatório, cada combinação de navegador/versão única é considerada um navegador diferente.
* A fatia chamada "Outro" indica a percentagem de pedidos tratados por todos os outros navegadores e versões.

Os dados que foram usados para gerar o gráfico de tartes podem ser vistos abaixo. Lá você encontrará o número de tipo/versão do navegador, o número total de acessos e a percentagem de acessos para cada um dos 250 principais navegadores.

## <a name="by-referrer"></a>Por Referrer
O relatório By Referrer permite-lhe visualizar os principais remetentes para conteúdos na plataforma selecionada. Um remetente indica o nome de anfitrião a partir do qual foi gerado um pedido. Ao gerar este tipo de relatório, um gráfico de barras indicará a quantidade de procura (isto é, acessos) gerada pelos 10 principais remetentes.

O lado esquerdo do gráfico (eixo y) indica o número total de pedidos que um ativo experimentou para cada reencaminhador. Cada barra na tabela representa um remetente. Utilize o esquema de codificação de cores para combinar uma barra com um remetente listado na secção de referreramento top 250.

Os dados utilizados para gerar o gráfico de barras podem ser vistos abaixo. Lá você encontrará o URL, o número total de acessos, e a percentagem de acessos gerados de cada um dos 250 principais remetentes.

## <a name="by-download"></a>Por Download
O relatório By Download permite-lhe analisar padrões de descarregamento para o seu conteúdo mais solicitado. O topo do relatório contém um gráfico de barras que compara as tentativas de transferências com downloads completos para os 10 principais ativos solicitados. Cada barra é codificada por cores de acordo com se se trata de uma tentativa de download (azul) ou de um download completo (verde).

> [!NOTE]
> Para efeitos deste relatório, os URLs CNAME de borda são convertidos para os seus URLs CDN equivalentes. Isto permite uma contagem precisa para todas as estatísticas associadas a um ativo, independentemente do URL CDN ou edge CNAME usado para o solicitar.
> 
> 

O lado esquerdo do gráfico (eixo y) indica o nome do ficheiro de cada um dos 10 principais ativos solicitados. Diretamente abaixo do gráfico (eixo x), encontrará etiquetas que indicam o número total de transferências tentadas/concluídas.

Diretamente abaixo da tabela de barras, serão listadas as seguintes informações para os 250 principais ativos solicitados: percurso relativo (incluindo nome de ficheiro), o número de vezes que foi descarregado para a conclusão, o número de vezes que foi solicitado, e a percentagem de pedidos que resultaram num download completo.

> [!TIP]
> O nosso CDN não é informado por um cliente HTTP (ou seja, browser) quando um ativo foi completamente descarregado. Como resultado, temos que calcular se um ativo foi completamente descarregado de acordo com códigos de estado e pedidos de byte-range. A primeira coisa que procuramos ao fazer este cálculo é se o pedido resulta num código de estado de 200 OK. Em caso afirmativo, então olhamos para os pedidos byte-range para garantir que cobrem todo o ativo. Finalmente, comparamos a quantidade de dados transferidos para o tamanho do ativo solicitado. Se os dados transferidos forem iguais ou superiores ao tamanho do ficheiro e os pedidos de byte-range forem apropriados para esse ativo, então o impacto será contado como um download completo.
> 
> Devido à natureza interpretativa deste relatório, deve ter em conta os seguintes pontos que podem alterar a coerência e a exatidão deste relatório.
> 
> * Os padrões de tráfego não podem ser previstos com precisão quando os agentes de utilizador se comportam de forma diferente. Isto pode produzir resultados de download concluídos que são superiores a 100%.
> * Os ativos que se aproveitam do DOWNLOAD Progressivo HTTP podem não ser representados com precisão por este relatório. Isto deve-se a utilizadores que procuram posições diferentes num vídeo.
> 
> 

## <a name="by-404-errors"></a>Por 404 erros
O relatório By 404 Errors permite identificar o tipo de conteúdo que gera o maior número de códigos de estado não encontrados. O topo do relatório contém um gráfico de barras para os 10 principais ativos para os quais foi devolvido um código de estado 404 Não Encontrado. Este gráfico de barras compara o número total de pedidos com pedidos que resultaram num código de estado 404 Não Encontrado para esses ativos. Cada barra é codificada por cores. Uma barra amarela é utilizada para indicar que o pedido resultou num código de estado 404 Não Encontrado. Uma barra vermelha é usada para indicar o número total de pedidos para o ativo.

> [!NOTE]
> Para efeitos do presente relatório, note-se o seguinte:
> 
> * Um sucesso representa qualquer pedido de um ativo independentemente do código de estado.
> * Os URLs CNAME edge são convertidos para os seus URLs CDN equivalentes. Isto permite uma contagem precisa para todas as estatísticas associadas a um ativo, independentemente do URL CDN ou edge CNAME usado para o solicitar.
> 
> 

O lado esquerdo do gráfico (eixo y) indica o nome do ficheiro de cada um dos 10 principais ativos solicitados que resultou num código de estado 404 Não Encontrado. Diretamente abaixo do gráfico (eixo x), encontrará etiquetas que indicam o número total de pedidos e o número de pedidos que resultaram num código de estado 404 Não Encontrado.

Diretamente abaixo da tabela de barras, serão listadas as seguintes informações para os 250 principais ativos solicitados: percurso relativo (incluindo nome de ficheiro), número de pedidos que resultaram num código de estado 404 Não Encontrados, o número total de vezes que o ativo foi solicitado, e a percentagem de pedidos que resultaram num código de estado 404 Não Encontrado.

## <a name="see-also"></a>Consulte também
* [Descrição geral da CDN do Azure](cdn-overview.md)
* [Estatísticas em tempo real no Microsoft Azure CDN](cdn-real-time-stats.md)
* [Comportamento HTTP predefinido que predefinindo usando o motor de regras](cdn-rules-engine.md)
* [Analisar desempenho da borda](cdn-edge-performance.md)

