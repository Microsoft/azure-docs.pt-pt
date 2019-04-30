---
title: Analisar as estatísticas de utilização com a CDN do Azure, relatórios HTTP avançados | Documentos da Microsoft
description: Saiba como criar relatórios HTTP avançados no Microsoft Azure CDN. Estes relatórios fornecem informações detalhadas sobre a atividade CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c8cb4713e38ca0da610c687325f3810f57da2b26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216156"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analisar as estatísticas de utilização com a CDN do Azure, relatórios HTTP avançados
## <a name="overview"></a>Descrição geral
Este documento explica relatórios HTTP avançados na CDN do Microsoft Azure. Estes relatórios fornecem informações detalhadas sobre a atividade CDN.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Aceder a relatórios HTTP avançados
1. No painel de perfil da CDN, clique a **gerir** botão.
   
    ![Botão de gerir do painel do perfil CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    É aberto o portal de gestão da CDN.
2. Paire o rato sobre o **Analytics** separador, em seguida, coloque o cursor sobre o **avançadas de relatórios de HTTP** submenu.  Clique em **plataforma grandes de HTTP**.
   
    ![Portal de gestão de CDN - menu de relatórios avançada](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Opções de relatórios são apresentadas.

## <a name="geography-reports-map-based"></a>Relatórios de geografia (baseada em mapa)
Há cinco relatórios que tiram partido de um mapa para indicar as regiões a partir do qual o seu conteúdo está a ser requerido. Estes relatórios são mapa-mundo, mapa dos Estados Unidos, Canadá mapa, mapa de Europa e Ásia Pacífico mapa.

Cada relatório baseada em mapa classificações entidades geográficas (ou seja, países, Estados e províncias), de acordo com a percentagem de ocorrências que se originou essa região. Além disso, um mapa é fornecido para o ajudar a visualizar as localizações a partir do qual o seu conteúdo está a ser requerido. É capaz de fazer isso, codificação por cores cada região, de acordo com a quantidade de procura experiente nessa região. Mais leves sombreadas regiões indicam menor procura para o seu conteúdo, embora mais escura regiões indicam níveis mais altos de procura para o seu conteúdo.

As informações detalhadas sobre o tráfego e largura de banda para cada região são fornecidas diretamente abaixo do mapa. Isto permite-lhe ver o número total de acertos, a percentagem de resultados, a quantidade total de dados transferidos (em gigabytes) e a percentagem de dados transferidos para cada região. Ver uma descrição para cada um destas métricas. Por fim, quando coloque o cursor sobre uma região (ou seja, país, estado ou província), o nome e a percentagem de pedidos com êxito na que ocorreram na região serão apresentadas como uma descrição.

Uma breve descrição é apresentada abaixo para cada tipo de relatório de geografia baseada em mapa.

| Nome do relatório | Descrição |
| --- | --- |
| Mapa-mundo |Este relatório permite-lhe ver a procura em todo o mundo para o seu conteúdo da CDN. Cada país é codificado por cores do mapa do mundo para indicar a percentagem de ocorrências que se originou essa região. |
| Mapa dos Estados Unidos |Este relatório permite-lhe ver o pedido para o seu conteúdo da CDN nos Estados Unidos. Cada Estado é codificada com cores deste mapa para indicar a percentagem de ocorrências que se originou essa região. |
| Mapa do Canadá |Este relatório permite-lhe ver o pedido para o seu conteúdo da CDN no Canadá. Cada província é codificada com cores deste mapa para indicar a percentagem de ocorrências que se originou essa região. |
| Mapa da Europa |Este relatório permite-lhe ver o pedido para o seu conteúdo CDN na Europa. Cada país é codificada com cores deste mapa para indicar a percentagem de ocorrências que se originou essa região. |
| Mapa de Ásia-Pacífico |Este relatório permite-lhe ver o pedido para o seu conteúdo CDN na Ásia. Cada país é codificada com cores deste mapa para indicar a percentagem de ocorrências que se originou essa região. |

## <a name="geography-reports-bar-charts"></a>Relatórios de geografia (gráficos de barras)
Existem dois relatórios adicionais que fornecem informações estatísticas de acordo com a geografia, que são cidades da parte superior e países da parte superior. Estes relatórios classificar cidades e países, respectivamente, consoante o número de ocorrências que se originou essas regiões. Após gerar este tipo de relatório, um gráfico de barras indicará os 10 principais cidades ou países que solicitou o conteúdo ao longo de uma plataforma específica. Este gráfico de barras permite-lhe avaliar rapidamente as regiões que geram o maior número de pedidos para o seu conteúdo.

O lado esquerdo do gráfico (eixo y) indica quantos acessos ocorreu na região especificada. Diretamente abaixo do gráfico (eixo x), encontrará uma etiqueta para cada uma das principais 10 regiões.

### <a name="using-the-bar-charts"></a>Utilizar gráficos de barras
* Se coloque o cursor sobre uma barra, o nome e o número total de acertos que ocorreram na região serão apresentadas como uma descrição.
* A descrição para o relatório de cidades da parte superior identifica uma cidade por seu nome, o estado/província e a abreviatura de país.
* Se não foi possível determinar o cidade ou a região (ou seja, estado/província) a partir do qual um pedido teve origem, irá indicar que são desconhecidos. Se o país é desconhecido, em seguida, dois pontos de interrogação (ou seja,??), será apresentada.
* Um relatório pode incluir as métricas para "Europa" ou "Região Ásia-Pacífico." Esses itens não se destinam a fornecer informações estatísticas sobre todos os endereços IP nessas regiões. Em vez disso, só se aplicam a pedidos provenientes de endereços IP que são rinat Europa ou Ásia-Pacífico, em vez de uma cidade específica ou país.

Os dados que foi utilizados para gerar o gráfico de barras podem ser visualizados abaixo dele. Lá encontrará o número total de acertos, a percentagem de resultados, a quantidade de dados transferidos (em gigabytes) e a percentagem de dados transferidos para as regiões da parte superior a 250. Ver uma descrição para cada um destas métricas.

Uma breve descrição é fornecida para ambos os tipos de relatórios abaixo.

| Nome do relatório | Descrição |
| --- | --- |
| Cidades da parte superior |Este relatório classifica cidades, de acordo com o número de resultados que originou nessa região. |
| Principais países |Este relatório classifica os países, de acordo com o número de resultados que originou nessa região. |

## <a name="daily-summary"></a>Resumo diário
O relatório de resumo diário permite-lhe ver o número total de acertos e os dados transferidos por uma plataforma específica numa base diária. Estas informações podem ser utilizadas de discernir rapidamente padrões de atividade da CDN. Por exemplo, este relatório pode ajudá-lo a detectar qual dias experientes superior ou inferiores ao tráfego esperado.

Após gerar este tipo de relatório, um gráfico de barras fornecerá uma indicação visual a quantidade de procura específica da plataforma experiente diariamente, ao longo do período de tempo coberto pelo relatório. Ela fará isso, apresentando uma barra para cada dia do relatório. Por exemplo, selecionar o período de tempo designado "Semana passada" irá gerar um gráfico de barras com sete barras. Cada barra indica o número total de acertos experientes nesse dia.

O lado esquerdo do gráfico (eixo y) indica quantos acessos ocorreu na data especificada. Diretamente abaixo do gráfico (eixo x), encontrará uma etiqueta que indica a data (formato: YYYY-MM-DD) para cada dia incluído no relatório.

> [!TIP]
> Se coloque o cursor sobre uma barra, o número total de acertos que ocorreu a essa data será apresentado como uma descrição.
> 
> 

Os dados que foi utilizados para gerar o gráfico de barras podem ser visualizados abaixo dele. Lá encontrará o número total de acertos e a quantidade de dados transferidos (em gigabytes) para cada dia no relatório.

## <a name="by-hour"></a>Por hora
O relatório por hora permite-lhe ver o número total de acertos e os dados transferidos por uma plataforma específica numa base horária. Estas informações podem ser utilizadas de discernir rapidamente padrões de atividade da CDN. Por exemplo, este relatório pode ajudá-lo a detetar os períodos de tempo durante o dia experiência superior ou inferior tráfego esperado.

Após gerar este tipo de relatório, um gráfico de barras fornecerá uma indicação visual a quantidade de procura específica da plataforma ocorrida numa base horária ao longo do período de tempo coberto pelo relatório. Ela fará isso, apresentando uma barra para cada hora no relatório. Por exemplo, selecionar uma 24 horas por período de tempo irá gerar um gráfico de barras com vinte quatro barras. Cada barra indica o número total de acertos experientes durante essa hora.

O lado esquerdo do gráfico (eixo y) indica quantos acessos ocorreu na hora especificada. Diretamente abaixo do gráfico (eixo x), encontrará uma etiqueta que indica a data/hora (formato: YYYY-MM-DD HH: mm) para cada hora incluída no relatório. Tempo comunicado com o formato de 24 horas e for especificado com o fuso horário UTC/GMT.

> [!TIP]
> Se coloque o cursor sobre uma barra, o número total de acertos que ocorreram durante essa hora será apresentado como uma descrição.
> 
> 

Os dados que foi utilizados para gerar o gráfico de barras podem ser visualizados abaixo dele. Lá encontrará o número total de acertos e a quantidade de dados transferidos (em gigabytes) para cada hora no relatório.

## <a name="by-file"></a>Por arquivo
O relatório por ficheiro permite-lhe ver a quantidade de tráfego incorridos através de uma plataforma específica para os recursos mais solicitados e a pedido. Após gerar este tipo de relatório, um gráfico de barras será gerado o ativos mais pedidas 10 principais ao longo do período de tempo especificado.

> [!NOTE]
> Para efeitos deste relatório, os URLs de CNAME do edge são convertidos em seus equivalentes URLs de CDN. Isso permite que uma contagem precisa para o número total de acertos associados a um ativo, independentemente do CDN ou de extremidade URL CNAME, utilizada para solicitá-la.
> 
> 

O lado esquerdo do gráfico (eixo y) indica o número de pedidos para cada elemento ao longo do período de tempo especificado. Diretamente abaixo do gráfico (eixo x), encontrará uma etiqueta que indica o nome de ficheiro para cada um dos principais 10 ativos pedidos.

Os dados que foi utilizados para gerar o gráfico de barras podem ser visualizados abaixo dele. Lá encontrará as seguintes informações para cada um dos principais 250 ativos pedidos: o caminho relativo, o número total de acertos, a percentagem de resultados, a quantidade de dados transferidos (em gigabytes) e a percentagem de dados transferidos.

## <a name="by-file-detail"></a>Por detalhes de ficheiros
O relatório de detalhes do ficheiro pelo permite-lhe ver a quantidade de tráfego incorridos através de uma plataforma específica para um recurso específico e a pedido. Na parte superior deste relatório é a opção de detalhes do ficheiro. Esta opção fornece uma lista dos seus bens mais solicitados na plataforma. Para gerar um relatório por detalhes de ficheiros, terá de selecionar o elemento pretendido da opção os detalhes do ficheiro. Após o qual, um gráfico de barras indicará a quantidade de procura diária que gerou durante o período de tempo especificado.

O lado esquerdo do gráfico (eixo y) indica o número total de pedidos que um recurso de verificarem num determinado dia. Diretamente abaixo do gráfico (eixo x), encontrará uma etiqueta que indica a data (formato: YYYY-MM-DD) para a CDN foi comunicado um pedido para o elemento.

Os dados que foi utilizados para gerar o gráfico de barras podem ser visualizados abaixo dele. Lá encontrará o número total de acertos e a quantidade de dados transferidos (em gigabytes) para cada dia no relatório.

## <a name="by-file-type"></a>Por tipo de arquivo
O relatório por tipo de ficheiro permite-lhe ver a quantidade de tráfego incorridos por tipo de arquivo e a pedido. Após gerar este tipo de relatório, um gráfico em anel indica a percentagem de acertos gerado pelos principais tipos de ficheiro de 10.

> [!TIP]
> Se coloque o cursor sobre um setor num gráfico de anel, tipo de mídia a Internet de que tipo de ficheiro será apresentado como uma descrição.
> 
> 

Os dados que foi utilizados para gerar o gráfico em anel podem ser visualizados abaixo dele. Lá encontrará o tipo de suporte de Internet/extensão de nome de ficheiro, o número total de acertos, a percentagem de resultados, a quantidade de dados transferidos (em gigabytes) e a percentagem de dados transferidos para cada um dos tipos de ficheiro a parte superior a 250.

## <a name="by-directory"></a>Por diretório
O relatório por diretório permite-lhe ver a quantidade de tráfego incorridos através de uma plataforma específica para conteúdo a partir de um diretório específico e a pedido. Após gerar este tipo de relatório, um gráfico de barras indica o número total de acertos gerado pelo conteúdo nos 10 diretórios principais.

### <a name="using-the-bar-chart"></a>Usando o gráfico de barras
* Coloque o cursor sobre uma barra para ver o caminho relativo para o diretório correspondente.
* Conteúdo armazenado numa subpasta de um diretório não conta ao calcular a pedido pelo diretório. Este cálculo baseia-se unicamente no número de pedidos gerados para o conteúdo armazenado no diretório atual.
* Para efeitos deste relatório, os URLs de CNAME do edge são convertidos em seus equivalentes URLs de CDN. Isso permite que uma contagem precisa para todas as estatísticas associados a um ativo, independentemente do CDN ou de extremidade URL CNAME, utilizada para solicitá-la.

O lado esquerdo do gráfico (eixo y) indica o número total de pedidos para os conteúdos armazenados nos seus diretórios de 10 principais. Cada barra no gráfico representa um diretório. Utilize o esquema de codificação de cores para harmonizar uma barra para um diretório listado na seção diretórios completa de 250 parte superior.

Os dados que foi utilizados para gerar o gráfico de barras podem ser visualizados abaixo dele. Lá encontrará as seguintes informações para cada um dos diretórios a parte superior a 250: caminho relativo, o número total de acertos, a percentagem de resultados, a quantidade de dados transferidos (em gigabytes) e a percentagem de dados transferidos.

## <a name="by-browser"></a>Por Browser
O relatório por Browser permite-lhe ver quais os browsers foram utilizados para solicitar o conteúdo. Após gerar este tipo de relatório, um gráfico de pizza indica a percentagem de pedidos processados pelos 10 principais navegadores.

### <a name="using-the-pie-chart"></a>Com o gráfico de pizza
* Coloque o cursor sobre um setor no gráfico circular para ver o nome e a versão de um browser.
* Para efeitos deste relatório, cada combinação exclusiva/versão do navegador é considerada um browser diferente.
* O setor chamado "Outros" indica a percentagem de pedidos processados por todos os outros navegadores e versões.

Os dados que foi utilizados para gerar o gráfico de pizza podem ser visualizados abaixo dele. Lá encontrará o número de versão do tipo de navegador, o número total de acertos e a percentagem de resultados para cada um dos navegadores principais 250.

## <a name="by-referrer"></a>Por Referenciador
O relatório por Referenciador permite-lhe ver os Referenciadores superior para o conteúdo na plataforma. Um Referenciador indica o nome de anfitrião a partir do qual foi gerado um pedido. Após gerar este tipo de relatório, um gráfico de barras indicará a quantidade de pedido (ou seja, acertos) gerado pelos 10 Referenciadores Principais.

O lado esquerdo do gráfico (eixo y) indica o número total de pedidos que um recurso teve para cada referência. Cada barra no gráfico representa uma referência. Utilize o esquema de codificação de cores para harmonizar uma barra para um Referenciador indicado na secção Referenciador de 250 da parte superior.

Os dados que foi utilizados para gerar o gráfico de barras podem ser visualizados abaixo dele. Lá encontrará o URL, o número total de acertos e a percentagem de resultados gerados a partir de cada um dos 250 Referenciadores superior.

## <a name="by-download"></a>Por transferência
O relatório ao baixar permite-lhe analisar padrões de download para o seu conteúdo mais pedido. Parte superior do relatório contém um gráfico de barras que compara tentado downloads com downloads concluídos para os recursos de pedido de 10 principais. Cada barra é codificado por cores, de acordo com quer se trate de uma tentativa de transferência (azul) ou um download concluído (verde).

> [!NOTE]
> Para efeitos deste relatório, os URLs de CNAME do edge são convertidos em seus equivalentes URLs de CDN. Isso permite que uma contagem precisa para todas as estatísticas associados a um ativo, independentemente do CDN ou de extremidade URL CNAME, utilizada para solicitá-la.
> 
> 

O lado esquerdo do gráfico (eixo y) indica o nome de ficheiro para cada um dos principais 10 ativos pedidos. Diretamente abaixo do gráfico (eixo x), encontrará as etiquetas que indicam o número total de downloads de tentativa/concluída.

Diretamente abaixo o gráfico de barras, as seguintes informações serão listadas para os principais recursos de pedido 250: caminho relativo (incluindo o nome de ficheiro), o número de vezes que este foi transferido para conclusão, o número de vezes que foi solicitada e a percentagem de pedidos que resultaram num download concluído.

> [!TIP]
> Nossa CDN não é informado por um cliente HTTP (ou seja, o navegador) quando um recurso já foi completamente baixado. Como resultado, precisamos calcular se um recurso foi completamente baixado, de acordo com códigos de estado e o intervalo de bytes pedidos. A primeira coisa que procuramos quando efetuar esse cálculo é se o pedido resulta num código de 200 estado OK. Se assim for, em seguida, vamos ver pedidos de intervalo de bytes para garantir que eles abrangem o elemento inteiro. Por fim, podemos comparar a quantidade de dados transferidos para o tamanho do recurso solicitado. Se os dados transferidos são igual ou maior do que o tamanho do ficheiro e as solicitações de intervalo de bytes são adequadas para esse recurso, em seguida, o impacto é contado como uma transferência completa.
> 
> Devido à natureza interpretativo deste relatório, deve ter em mente os seguintes pontos, que podem alterar a consistência e a precisão deste relatório.
> 
> * Padrões de tráfego não não possível prever com precisão quando os agentes do usuário ter um comportamento diferente. Isso pode produzir resultados de transferência concluída são maiores que 100%.
> * Não podem ser representados com precisão ativos que tiram partido do HTTP faça a transferência progressiva por este relatório. Isso é devido a utilizadores que procuram posições diferentes de um vídeo.
> 
> 

## <a name="by-404-errors"></a>Por 404 erros
O relatório de erros de 404 por permite-lhe identificar o tipo de conteúdo que gera o maior número de 404 códigos de estado não encontrado. Parte superior do relatório contém um gráfico de barras para os recursos de 10 principais para o qual foi devolvido um código de estado não encontrado 404. Este gráfico de barras compara o número total de pedidos com solicitações que resultaram num 404 não encontrado código de estado para esses ativos. Cada barra é codificado por cores. Uma barra amarela é utilizada para indicar que o pedido resultou num código de estado não encontrado 404. Uma barra vermelha é utilizada para indicar o número total de pedidos para o elemento.

> [!NOTE]
> Para efeitos deste relatório, tenha em atenção o seguinte:
> 
> * Um problema de representa qualquer pedido para um ativo, independentemente do código de estado.
> * URLs de CNAME do Edge são convertidos em seus equivalentes URLs de CDN. Isso permite que uma contagem precisa para todas as estatísticas associados a um ativo, independentemente do CDN ou de extremidade URL CNAME, utilizada para solicitá-la.
> 
> 

O lado esquerdo do gráfico (eixo y) indica o nome de ficheiro para cada um dos principais 10 ativos solicitados que resultaram num código de estado não encontrado 404. Diretamente abaixo do gráfico (eixo x), encontrará as etiquetas que indicam o número total de pedidos e o número de pedidos que resultaram num código de estado não encontrado 404.

Diretamente abaixo o gráfico de barras, as seguintes informações serão listadas para os principais recursos de pedido 250: caminho relativo (incluindo nome de ficheiro), o número de pedidos que resultaram num 404 não encontrado código de estado, o número total de vezes que o recurso foi pedido, e a percentagem de pedidos que resultaram num código de estado não encontrado 404.

## <a name="see-also"></a>Consulte também
* [Descrição geral da CDN do Azure](cdn-overview.md)
* [Estatísticas em tempo real na CDN do Microsoft Azure](cdn-real-time-stats.md)
* [Substituir o comportamento HTTP padrão usando o mecanismo de regras](cdn-rules-engine.md)
* [Analisar o desempenho Edge](cdn-edge-performance.md)

