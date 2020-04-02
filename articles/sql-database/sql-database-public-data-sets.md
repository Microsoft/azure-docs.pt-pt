---
title: Conjuntos de dados públicos para analítica Azure
description: Saiba mais sobre conjuntos de dados públicos que pode usar para protótipo e testar serviços e soluções de análise Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2018
ms.openlocfilehash: 0fe20c90cf857ce09a83c6ac53e6fe77eb512153
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528199"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Conjuntos de dados públicos para testes e prototipagem

Navegue nesta lista de conjuntos de dados públicos para obter dados que pode utilizar para protótipo e teste de armazenamento e serviços e soluções de análise.

## <a name="us-government-and-agency-data"></a>Governo dos EUA e dados da agência

| Origem de dados | Sobre os dados | Sobre os ficheiros |
|---|---|---|
| [Dados do Governo dos EUA](https://catalog.data.gov/dataset) | Mais de 250.000 conjuntos de dados abrangendo agricultura, clima, consumo, ecossistemas, educação, energia, finanças, saúde, governo local, fabrico, marítimo, oceano, segurança pública e ciência e investigação nos EUA. | Ficheiros de vários tamanhos em vários formatos, incluindo HTML, XML, CSV, JSON, Excel, entre muitos outros. Pode filtrar os conjuntos de dados disponíveis por formato de ficheiro. |
| [Dados do Censo sucense dos EUA](https://www.census.gov/data.html) | Dados estatísticos sobre a população dos EUA | Os conjuntos de dados estão em vários formatos. |
| [Dados da ciência da Terra da NASA](https://earthdata.nasa.gov/) | Mais de 32.000 recolhas de dados abrangendo agricultura, atmosfera, biosfera, clima, criosfera, dimensões humanas, hidrosfera, superfície terrestre, oceanos, interações entre o sol e a terra, e muito mais. | Os conjuntos de dados estão em vários formatos. |
| [Atrasos nos voos das companhias aéreas e outros dados de transporte](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "O Departamento de Estatísticas dos Transportes (DOT) dos EUA acompanha o desempenho pontual dos voos domésticos operados por grandes transportadoras aéreas. Sumário sumário sobre o número de voos a tempo, atrasados, cancelados e desviados aparecem... em tabelas sumárias publicadas neste site. | Os ficheiros estão no formato CSV. |
| [Mortes no tráfego - Sistema de Relatórios de Análise de Fatalidade dos EUA (FARS)](https://www.nhtsa.gov/FARS) | "Fars é um recenseamento nacional que fornece dados anuais da NHTSA, Congresso e do público americano sobre ferimentos fatais sofridos em acidentes de viação." | "Crie os seus próprios dados de fatalidade executados online utilizando o Sistema de Consulta fars. Ou descarregue todos os dados fars de 1975 para apresentar a partir do Site FTP." |
| [Dados químicos tóxicos - Dados do ForeCaster de Toxicidade EPA (ToxCast&trade;)](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "Os dados de toxicidade de alto consumo mais atualizados e disponíveis publicamente da EPA sobre milhares de produtos químicos. Estes dados são gerados através do esforço de pesquisa toxCast da EPA." | Os conjuntos de dados estão disponíveis em vários formatos, incluindo folhas de cálculo, pacotes R e ficheiros de base de dados MySQL. |
| [Dados químicos tóxicos - NIH Tox21 Data Challenge 2014](https://tripod.nih.gov/tox21/challenge/) | "O desafio de dados tox21 de 2014 foi concebido para ajudar os cientistas a entender o potencial dos químicos e compostos que estão a ser testados através da toxicologia na iniciativa do século XXI para perturbar as vias biológicas de forma a resultar em efeitos tóxicos." | Os conjuntos de dados estão disponíveis em formatoS SMILES e SDF. Os dados fornecem "dados de atividade de ensaio e estruturas químicas na recolha de Tox21 de ~10.000 compostos (Tox21 10K)". |
| [Dados da biotecnologia e do genoma do NCBI](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Vários conjuntos de dados que cobrem genes, genomas e proteínas. | Os conjuntos de dados estão em texto, XML, BLAST e outros formatos. Está disponível uma aplicação BLAST. |

## <a name="other-statistical-and-scientific-data"></a>Outros dados estatísticos e científicos

| Origem de dados | Sobre os dados | Sobre os ficheiros |
|---|---|---|
| [Dados de táxi da cidade de Nova Iorque](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "Os registos de viagens de táxi incluem campos que captam datas/horas de recolha e entrega, locais de recolha e entrega, distâncias de viagem, tarifas emitidas, tipos de tarifas, tipos de pagamento e contagens de passageiros reportadas pelo condutor." | Os conjuntos de dados estão em ficheiros CSV por mês. |
| [Conjuntos de dados da Microsoft Research - "Data Science for Research"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Múltiplos conjuntos de dados que abrangem a interação humano-computador, áudio/vídeo, mineração de dados/recuperação de informação, geoespacial/localização, processamento de linguagem natural e visão robótica/computador. | Os conjuntos de dados estão em vários formatos, com fecho para download. |
| [Dados da Nuvem de Dados da Ciência Aberto](https://www.opensciencedatacloud.org/projects/) | "A Nuvem de Dados de Ciência Aberta fornece à comunidade científica recursos para armazenar, partilhar e analisar conjuntos de dados científicos à escala de terabyte e petabyte."| Os conjuntos de dados estão em vários formatos. |
| [Dados climáticos globais - WorldClim](https://worldclim.org/) | "O WorldClim é um conjunto de camadas climáticas globais (dados climáticos regrididos) com uma resolução espacial de cerca de 1 km2. Estes dados podem ser utilizados para mapeamento e modelação espacial." | Estes ficheiros contêm dados geoespaciais. Para mais informações, consulte o [formato Data](https://worldclim.org/formats1). |
| [Dados sobre a sociedade humana - O Projeto GDELT](https://www.gdeltproject.org/data.html) | "O Projeto GDELT é a maior, mais abrangente e mais alta resolução de bases de dados abertas da sociedade humana alguma vez criada." | Os ficheiros de dados brutos estão no formato CSV. |
| [Dados de previsão de cliquede publicidade para aprendizagem automática a partir de Criteo](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "O maior conjunto de dados ml já divulgado publicamente." Para mais informações, consulte o [conjunto de dados de previsão de 1 TB de Criteo](https://blogs.technet.microsoft.com/machinelearning/20../../now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/). | |
| [Conjunto de dados de mineração de texto ClueWeb09 do Projeto Lemur](https://www.lemurproject.org/clueweb09.php/) | "O conjunto de dados ClueWeb09 foi criado para apoiar a investigação sobre a recuperação de informação e tecnologias de linguagem humana relacionadas. É constituída por cerca de mil milhões de páginas web em 10 línguas que foram recolhidas em janeiro e fevereiro de 2009." | Consulte [informações sobre conjuntode dados](https://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Dados de serviços online

| Origem de dados | Sobre os dados | Sobre os ficheiros |
|---|---|---|
| [Arquivo GitHub](https://www.githubarchive.org/) | "O GitHub Archive é um projeto para gravar a linha temporal pública do GitHub [de eventos], arquivá-la e torná-la facilmente acessível para análise seletiva." | Descarregue os arquivos de eventos codificados pela JSON em formato .gz (Gzip) a partir de um cliente web. |
| [Dados de atividade gitHub do projeto GHTorrent](http://ghtorrent.org/) | "O projeto GHTorrent [é] um esforço para criar um espelho escalável, consultadoe, offline de dados oferecidos através da API DO REST GitHub. GHTorrent monitoriza a linha de tempo do evento público GitHub. Para cada evento, recupera exaustivamente o seu conteúdo e as suas dependências." | Os depósitos de base de dados MySQL estão em formato CSV. |
| [Depósito de dados de transbordo de pilhas](https://archive.org/details/stackexchange) | "Esta é uma descarga anoinizada de todos os conteúdos contribuídos pelo utilizador na rede Stack Exchange [incluindo Stack Overflow]." | "Cada site [como Stack Overflow] é formatado como um arquivo separado composto por ficheiros XML com fecho de 7-zip usando compressão bzip2. Cada arquivo de site inclui Posts, Utilizadores, Votos, Comentários, Pós-História e PostLinks." |
