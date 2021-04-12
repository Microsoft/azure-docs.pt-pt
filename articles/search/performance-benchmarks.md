---
title: Testes de referência do desempenho
titleSuffix: Azure Cognitive Search
description: Conheça o desempenho da Azure Cognitive Search através de vários referenciais de desempenho
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 9f4473d6c8a584bf60e5c8fe2d69d6a56a55e71d
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108443"
---
# <a name="azure-cognitive-search-performance-benchmarks"></a>Referências de desempenho da Pesquisa Cognitiva Azure

O desempenho da Azure Cognitive Search depende de uma [variedade de fatores,](search-performance-tips.md) incluindo o tamanho do seu serviço de pesquisa e os tipos de consultas que está a enviar. Para ajudar a estimar o tamanho do serviço de pesquisa necessário para a sua carga de trabalho, executámos vários parâmetros de referência para documentar o desempenho de diferentes serviços de pesquisa e configurações. Estes parâmetros de referência não garantem de forma alguma um certo nível de desempenho do seu serviço, mas podem dar-lhe uma ideia do desempenho que pode esperar.

Para cobrir uma série de casos de uso diferentes, fizemos benchmarks para dois cenários principais:

* **Pesquisa de e-commerce** - Este benchmark imita um cenário real de e-commerce e baseia-se na empresa nórdica de comércio eletrónico [CDON](https://cdon.com).
* **Pesquisa de documentos** - Este cenário é composto por pesquisa de palavras-chave sobre documentos de texto completos do [Académico Semântico](http://s2-public-api-prod.us-west-2.elasticbeanstalk.com/corpus/download/). Isto imita uma solução típica de pesquisa de documentos.

Embora estes cenários reflitam casos de uso diferentes, cada cenário é diferente, por isso recomendamos sempre que o desempenho teste a sua carga de trabalho individual. Publicámos uma solução de [teste de desempenho utilizando o JMeter](https://github.com/Azure-Samples/azure-search-performance-testing) para que possas fazer testes semelhantes contra o teu próprio serviço.

## <a name="testing-methodology"></a>Metodologia de teste

Para comparar o desempenho da Azure Cognitive Search, fizemos testes para dois cenários diferentes em diferentes níveis e combinações de replicação/partição.

Para criar estes parâmetros de referência, utilizou-se a seguinte metodologia:

1. O teste começa em `X` consultas por segundo (QPS) durante 180 segundos. Isto era geralmente 5 ou 10 QPS.
2. QPS, em seguida, aumentou `X` e correu por mais 180 segundos
3. A cada 180 segundos, o teste aumentou por QPS até que a `X` latência média aumentou acima de 1000 ms ou menos de 99% das consultas conseguidas.

O gráfico abaixo dá um exemplo visual de como é a carga de consulta do teste:

![Teste de exemplo](./media/performance-benchmarks/example-test.png)

Cada cenário usou pelo menos 10.000 consultas únicas para evitar que os testes fossem excessivamente distorcidos pelo caching.

> [!IMPORTANT]
> Estes testes incluem apenas cargas de trabalho de consulta. Se espera ter um elevado volumne de operações de indexação, certifique-se de que isso se inserta na sua estimativa e teste de desempenho. O código de amostra para simular a indexação pode ser encontrado neste [tutorial](tutorial-optimize-indexing-push-api.md).

### <a name="definitions"></a>Definições

- **QPS máximo** - os números máximos de QPS abaixo baseiam-se no QPS mais elevado alcançado num teste onde 99% das consultas concluídas com sucesso sem estrangulamento e latência média permaneceram abaixo dos 1000 ms.

- **Percentagem de QPS máximo** - Uma percentagem do QPS máximo alcançado para um determinado teste. Por exemplo, se um dado teste atingisse um máximo de 100 QPS, 20% do QPS máximo seria de 20 QPS.

- **Latência** - A latência do servidor para uma consulta; estes números não incluem [atraso de ida e volta (RTT)](https://en.wikipedia.org/wiki/Round-trip_delay). Os valores abaixo estão em milissegundos (ms).

### <a name="disclaimer"></a>Disclaimer

O código que usámos para executar estes parâmetros de referência está disponível [aqui.](https://github.com/Azure-Samples/azure-search-performance-testing/tree/main/other_tools) Vale a pena notar que observámos níveis de QPS ligeiramente mais baixos com a [solução de teste de desempenho JMeter](https://github.com/Azure-Samples/azure-search-performance-testing) do que nos parâmetros de referência abaixo. As diferenças podem ser atribuídas a diferenças no estilo dos testes. Isto fala da importância de tornar os seus testes de desempenho o mais semelhantes ao seu trabalho de produção.

Estes benchmarks não garantem de forma alguma um determinado nível de desempenho do seu serviço, mas podem dar-lhe uma ideia do desempenho que pode esperar com base no seu cenário.

Se tiver alguma dúvida ou preocupação, contacte-nos em azuresearch_contact@microsoft.com .

## <a name="benchmark-1-e-commerce-search"></a>Benchmark 1: Pesquisa de e-commerce

:::row:::
   :::column span="1":::
      ![Logotipo do CDON](./media/performance-benchmarks/cdon-logo-160px2.png)
   :::column-end:::
   :::column span="3":::
      Este referencial foi criado em parceria com a empresa de comércio eletrónico [CDON,](https://cdon.com)o maior mercado online da região nórdica, com operações na Suécia, Finlândia, Noruega e Dinamarca. Através dos seus 1.500 comerciantes, a CDON oferece uma vasta gama de produtos que inclui mais de 8 milhões de produtos. Em 2020, a CDON teve mais de 120 milhões de visitantes e 2 milhões de clientes ativos. Pode saber mais sobre o uso da Azure Cognitive Search neste [artigo.](https://pulse.microsoft.com/transform/na/fa1-how-cdon-has-been-using-technology-to-become-the-leading-marketplace-in-the-nordics/)
   :::column-end:::
:::row-end:::

Para realizar estes testes, usamos uma foto do índice de pesquisa de produção da CDON e milhares de consultas únicas a partir do seu [website.](https://cdon.com)

### <a name="scenario-details"></a>Detalhes do cenário

- **Contagem de documentos**: 6.000.000 
- **Tamanho do índice**: 20 GB
- **Índice Schema**: um índice alargado com 250 campos no total, 25 campos pescáveis e 200 campos facetable/filtrados
- **Tipos de consulta**: consultas completas de pesquisa de texto, incluindo facetas, filtros, pedidos e perfis de pontuação

### <a name="s1-performance"></a>S1 Performance

#### <a name="queries-per-second"></a>Consultas por segundo

O gráfico abaixo mostra a carga de consulta mais alta que um serviço poderia suportar por um longo período de tempo em termos de consultas por segundo (QPS).

![Ecommerce QPS mais elevado e sustentável s1](./media/performance-benchmarks/s1-ecom-qps.png)

#### <a name="query-latency"></a>Latência consulta

A latência da consulta varia em função da carga do serviço e os serviços sob maior stress terão uma latência média mais elevada. A tabela abaixo mostra os percentí destes 25, 50, 75, 90º, 95º e 99º percentílquos de latência de consulta para três níveis de utilização diferentes.

| Percentagem de QPS máximo  | Latência média | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- | 
| 20%  | 104 ms  | 35 ms  | 115 ms   | 177 ms | 257 ms | 738 ms |
| 50%  | 140 ms  | 47 ms  | 144 ms   | 241 ms | 400 ms | 1175 ms |
| 80%  | 239 ms  | 77 ms  | 248 ms   | 466 ms | 763 ms | 1752 ms | 


### <a name="s2-performance"></a>S2 Performance

#### <a name="queries-per-second"></a>Consultas por segundo

O gráfico abaixo mostra a carga de consulta mais alta que um serviço poderia suportar por um longo período de tempo em termos de consultas por segundo (QPS).

![Ecommerce QPS mais elevado e sustentável s2](./media/performance-benchmarks/s2-ecom-qps.png)

#### <a name="query-latency"></a>Latência consulta

A latência da consulta varia em função da carga do serviço e os serviços sob maior stress terão uma latência média mais elevada. A tabela abaixo mostra os percentí destes 25, 50, 75, 90º, 95º e 99º percentílquos de latência de consulta para três níveis de utilização diferentes.

| Percentagem de QPS máximo  | Latência média | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- | 
| 20%  | 56 ms | 21 ms | 68 ms  | 106 ms  | 132 ms | 210 ms | 
| 50%  | 71 ms  | 26 ms  | 83 ms   | 132 ms | 177 ms | 329 ms |
| 80%  | 140 ms  | 47 ms  | 153 ms   | 293 ms | 452 ms | 924 ms | 

### <a name="s3-performance"></a>S3 Performance

#### <a name="queries-per-second"></a>Consultas por segundo

O gráfico abaixo mostra a carga de consulta mais alta que um serviço poderia suportar por um longo período de tempo em termos de consultas por segundo (QPS).

![Ecommerce QPS mais elevado e sustentável s3](./media/performance-benchmarks/s3-ecom-qps.png)

Neste caso, vemos que adicionar uma segunda partição aumenta significativamente o QPS máximo, mas adicionar uma terceira partição proporciona retornos marginais diminuídos. A melhoria menor é provável porque todos os dados já estão sendo puxados para a memória ativa do S3 com apenas duas divisórias.

#### <a name="query-latency"></a>Latência consulta

A latência da consulta varia em função da carga do serviço e os serviços sob maior stress terão uma latência média mais elevada. A tabela abaixo mostra os percentí destes 25, 50, 75, 90º, 95º e 99º percentílquos de latência de consulta para três níveis de utilização diferentes.

| Percentagem de QPS máximo  | Latência média | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 50 ms  | 20 ms  | 64 ms   | 83 ms | 98 ms | 160 ms |
| 50%  | 62 ms  | 24 ms  | 80 ms   | 107 ms | 130 ms | 253 ms |
| 80%  | 115 ms  | 38 ms  | 121 ms   | 218 ms | 352 ms | 828 ms |

## <a name="benchmark-2-document-search"></a>Benchmark 2: Pesquisa de documentos

### <a name="scenario-details"></a>Detalhes do cenário

- **Contagem de documentos**: 7,5 milhões
- **Tamanho do índice**: 22 GB
- **Esquema de Índice**: 23 campos; 8 pescável, 10 filtratáveis/facetable
- **Tipos de Consulta**: pesquisas de palavras-chave com facetas e destaques de sucesso

### <a name="s1-performance"></a>S1 Performance

#### <a name="queries-per-second"></a>Consultas por segundo

O gráfico abaixo mostra a carga de consulta mais alta que um serviço poderia suportar por um longo período de tempo em termos de consultas por segundo (QPS).

![Pesquisa de doc QPS mais elevada e sustentável s1](./media/performance-benchmarks/s1-docsearch-qps.png)

#### <a name="query-latency"></a>Latência consulta

A latência da consulta varia em função da carga do serviço e os serviços sob maior stress terão uma latência média mais elevada. A tabela abaixo mostra os percentí destes 25, 50, 75, 90º, 95º e 99º percentílquos de latência de consulta para três níveis de utilização diferentes.

| Percentagem de QPS máximo  | Latência média | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 67 ms  | 44 ms  | 77 ms   | 103 ms | 126 ms | 216 ms |
| 50%  | 93 ms  | 59 ms  | 110 ms   | 150 ms | 184 ms | 304 ms |
| 80%  | 150 ms  | 96 ms  | 184 ms   | 248 ms | 297 ms | 424 ms |

### <a name="s2-performance"></a>S2 Performance

#### <a name="queries-per-second"></a>Consultas por segundo

O gráfico abaixo mostra a carga de consulta mais alta que um serviço poderia suportar por um longo período de tempo em termos de consultas por segundo (QPS).

![Pesquisa de doc QPS mais elevada e sustentável s2](./media/performance-benchmarks/s2-docsearch-qps.png)

#### <a name="query-latency"></a>Latência consulta

A latência da consulta varia em função da carga do serviço e os serviços sob maior stress terão uma latência média mais elevada. A tabela abaixo mostra os percentí destes 25, 50, 75, 90º, 95º e 99º percentílquos de latência de consulta para três níveis de utilização diferentes.

| Percentagem de QPS máximo  | Latência média | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 45 ms  | 31 ms  | 55 ms   | 73 ms | 84 ms | 109 ms |
| 50%  | 63 ms  | 39 ms  | 81 ms   | 106 ms | 123 ms | 163 ms |
| 80%  | 115 ms  | 73 ms  | 145 ms   | 191 ms | 224 ms | 291 ms |

## <a name="takeaways"></a>Conclusões

Através destes referenciais, você pode obter uma ideia do desempenho que a Azure Cognitive Search oferece. Também pode ver diferença entre serviços em diferentes níveis.

Algumas formas fundamentais de tirar em conta estes critérios de referência são:

* Um S2 normalmente pode lidar com pelo menos quatro vezes o volume de consulta como um S1
* Um S2 normalmente terá uma latência mais baixa do que um S1 em volumes de consulta comparáveis
* À medida que adiciona réplicas, o QPS um serviço pode manusear tipicamente escalas lineares (por exemplo, se uma réplica pode lidar com 10 QPS, então cinco réplicas geralmente podem lidar com 50 QPS)
* Quanto maior for a carga sobre o serviço, maior será a latência média

Também pode ver que o desempenho pode variar drasticamente entre cenários. Se não está a conseguir o desempenho que espera, confira as dicas para um [melhor desempenho](search-performance-tips.md).

## <a name="next-steps"></a>Passos seguintes

Agora que viu os referenciais de desempenho, pode aprender mais sobre como analisar o desempenho da Cognitive Search e fatores-chave que influenciam o desempenho.

> [!div class="nextstepaction"]
> [Analisar desempenho](search-performance-analysis.md) 
>  [Dicas para um melhor desempenho](search-performance-tips.md)