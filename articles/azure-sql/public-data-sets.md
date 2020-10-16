---
title: Conjuntos de dados públicos para análise de Azure
description: Saiba mais sobre os conjuntos de dados públicos que pode utilizar para protótipo e testar serviços e soluções de análise Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2018
ms.openlocfilehash: 30f6bf7629152050bc1249a1e22aff0aa30642de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88209539"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Conjuntos de dados públicos para testes e prototipagem
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Consulte esta lista de conjuntos de dados públicos para obter dados que pode utilizar para protótipo e testar serviços e soluções de armazenamento e análise.

## <a name="us-government-and-agency-data"></a>Dados do Governo e da agência dos EUA

| Origem de dados | Sobre os dados | Sobre os ficheiros |
|---|---|---|
| [Dados do Governo dos EUA](https://catalog.data.gov/dataset) | Mais de 250.000 conjuntos de dados que abrangem a agricultura, o clima, o consumidor, os ecossistemas, a educação, a energia, as finanças, a saúde, o governo local, a indústria transformadora, o mar, a segurança pública e a ciência e investigação nos EUA. | Arquivos de vários tamanhos em vários formatos, incluindo HTML, XML, CSV, JSON, Excel, entre muitos outros. Pode filtrar os conjuntos de dados disponíveis pelo formato de ficheiro. |
| [Dados do Censo dos EUA](https://www.census.gov/data.html) | Dados estatísticos sobre a população dos EUA | Os conjuntos de dados estão em vários formatos. |
| [Dados científicos da Terra da NASA](https://earthdata.nasa.gov/) | Mais de 32.000 recolhas de dados abrangendo agricultura, atmosfera, biosfera, clima, criosfera, dimensões humanas, hidrosfera, superfície terrestre, oceanos, interações sol-terra, e muito mais. | Os conjuntos de dados estão em vários formatos. |
| [Atrasos nos voos das companhias aéreas e outros dados de transporte](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "O Departamento de Estatísticas dos Transportes (DOT) do Departamento de Transportes dos Estados Unidos (BTS) acompanha o desempenho atemal dos voos domésticos operados por grandes transportadoras aéreas. Informações sumárias sobre o número de voos on-time, atrasados, cancelados e desviados aparecem... em tabelas sumárias publicadas neste site." | Os ficheiros estão no formato CSV. |
| [Mortes no tráfego - Sistema de Reporte de Análise de Fatalidade dos EUA (FARS)](https://www.nhtsa.gov/FARS) | "Fars é um recenseamento nacional que fornece nhTSA, Congresso, e o público americano dados anual sobre ferimentos fatais sofridos em acidentes de tráfego de veículos automóveis." | "Crie os seus próprios dados de fatalidade executados online utilizando o Sistema de Consulta FARS. Ou descarregue todos os dados fars de 1975 para apresentar a partir do Site FTP." |
| [Dados químicos tóxicos - EPA Toxicity ForeCaster (ToxCast) &trade;](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "Os dados de toxicidade de alta produção mais atualizados e publicamente disponíveis da EPA sobre milhares de produtos químicos. Estes dados são gerados através do esforço de pesquisa toxCast da EPA." | Os conjuntos de dados estão disponíveis em vários formatos, incluindo folhas de cálculo, pacotes R e ficheiros de base de dados MySQL. |
| [Dados químicos tóxicos - NIH Tox21 Data Challenge 2014](https://tripod.nih.gov/tox21) | "O desafio de dados tox21 de 2014 foi concebido para ajudar os cientistas a compreender o potencial dos químicos e compostos que estão a ser testados através da toxicologia na iniciativa do século XXI para interromper as vias biológicas de formas que possam resultar em efeitos tóxicos." | Os conjuntos de dados estão disponíveis nos formatos SMILES e SDF. Os dados fornecem "dados de atividade de ensaio e estruturas químicas na recolha tox21 de ~10.000 compostos (Tox21 10K)". |
| [Dados biotecnológicos e do genoma do NCBI](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Vários conjuntos de dados que cobrem genes, genomas e proteínas. | Os conjuntos de dados estão em texto, XML, BLAST e outros formatos. Está disponível um aplicativo BLAST. |

## <a name="other-statistical-and-scientific-data"></a>Outros dados estatísticos e científicos

| Origem de dados | Sobre os dados | Sobre os ficheiros |
|---|---|---|
| [Dados do táxi de Nova Iorque](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "Os registos de viagem de táxi incluem campos que captam datas/horários de recolha e entrega, locais de recolha e entrega, distâncias de viagem, tarifas, tipos de tarifas, tipos de pagamento e contagens de passageiros reportadas pelo condutor." | Os conjuntos de dados estão nos ficheiros CSV por mês. |
| [Conjuntos de dados da Microsoft Research - "Data Science for Research"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Múltiplos conjuntos de dados que abrangem a interação humano-computador, áudio/vídeo, extração de dados/recolha de informação, geoespacial/localização, processamento de linguagem natural e robótica/visão computacional. | Os conjuntos de dados estão em vários formatos, com fecho para download. |
| [Dados da Nuvem de Dados de Ciência Aberta](https://www.opensciencedatacloud.org/projects/) | "A Nuvem de Dados de Ciência Aberta fornece à comunidade científica recursos para armazenar, partilhar e analisar conjuntos de dados científicos à escala de terabytes e petabytes."| Os conjuntos de dados estão em vários formatos. |
| [Dados climáticos globais - WorldClim](https://worldclim.org/) | "O WorldClim é um conjunto de camadas climáticas globais (dados climáticos grelhados) com uma resolução espacial de cerca de 1 km2. Estes dados podem ser usados para mapeamento e modelação espacial." | Estes ficheiros contêm dados geoespaciais. Para mais informações, consulte [o formato Data](https://worldclim.org/formats1). |
| [Dados sobre a sociedade humana - O Projeto GDELT](https://www.gdeltproject.org/data.html) | "O Projeto GDELT é a maior, mais abrangente e mais alta resolução de base de dados aberta da sociedade humana alguma vez criada." | Os ficheiros de dados brutos estão no formato CSV. |
| [Publicidade clique dados de previsão para aprendizagem de máquinas a partir de Criteo](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "O maior conjunto de dados da ML publicamente divulgado." Para obter mais informações, consulte [o conjunto de dados de previsão de clique de 1 TB do Criteo.](https://blogs.technet.microsoft.com/machinelearning/20../../now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/) | |
| [Conjunto de dados de mineração de texto ClueWeb09 do The Lemur Project](https://www.lemurproject.org/clueweb09.php/) | "O conjunto de dados ClueWeb09 foi criado para apoiar a investigação sobre a recuperação de informação e tecnologias de linguagem humana relacionadas. Consiste em cerca de mil milhões de páginas web em 10 línguas que foram recolhidas em janeiro e fevereiro de 2009." | Consulte [informações do conjunto de dados](https://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Dados de serviços online

| Origem de dados | Sobre os dados | Sobre os ficheiros |
|---|---|---|
| [Arquivo GitHub](https://www.githubarchive.org/) | "O GitHub Archive é um projeto para registar a linha temporal do GitHub [dos eventos], arquivá-la e torná-la facilmente acessível para análises mais aprofundadas." | Descarregue os arquivos de eventos codificados JSON em formato .gz (Gzip) a partir de um cliente web. |
| [Dados de atividade do GitHub do projeto GHTorrent](http://ghtorrent.org/) | "O projeto GHTorrent [é] um esforço para criar um espelho expansível, questionável e offline de dados oferecidos através da API GitHub REST. GHTorrent monitoriza a linha de tempo do evento público GitHub. Para cada evento, recupera o seu conteúdo e as suas dependências, exaustivamente." | As lixeiras da base de dados MySQL estão em formato CSV. |
| [Despejo de dados de fluxo de pilha](https://archive.org/details/stackexchange) | "Esta é uma descarga anonimizada de todos os conteúdos contribuídos pelo utilizador na rede Stack Exchange [incluindo Stack Overflow]." | "Cada site [como Stack Overflow] é formatado como um arquivo separado composto por ficheiros XML fechados via 7-zip usando a compressão bzip2. Cada arquivo do site inclui Posts, Utilizadores, Votos, Comentários, Pós-História e PostLinks." |
