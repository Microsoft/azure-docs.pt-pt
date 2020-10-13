---
title: Aceleração da consulta de armazenamento do lago de dados Azure
description: A aceleração de consultas permite que aplicações e quadros de análise otimizem dramaticamente o processamento de dados, recuperando apenas os dados necessários para uma operação de processamento.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 09/09/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: ae92828f08ae4abf9cc28f18872cca27ce747be4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89657645"
---
# <a name="azure-data-lake-storage-query-acceleration"></a>Aceleração da consulta de armazenamento do lago de dados Azure

A aceleração de consultas permite que aplicações e quadros de análise otimizem dramaticamente o processamento de dados, recuperando apenas os dados que necessitam para realizar uma determinada operação. Isto reduz o tempo e o poder de processamento necessários para obter informações críticas sobre os dados armazenados.

## <a name="overview"></a>Descrição geral

A aceleração da consulta aceita a filtragem *de predicados* e *projeções de colunas* que permitem às aplicações filtrar linhas e colunas no momento em que os dados são lidos a partir do disco. Apenas os dados que satisfaçam as condições de um predicado são transferidos pela rede para a aplicação. Isto reduz a latência da rede e o custo do cálculo.  

Pode utilizar o SQL para especificar os predicados do filtro de linha e as projeções das colunas num pedido de aceleração de consulta. Um pedido processa apenas um ficheiro. Portanto, características relacionais avançadas de SQL, tais como junções e grupo por agregados, não são suportados. A aceleração da consulta suporta dados formatados de CSV e JSON como entrada para cada pedido.

A funcionalidade de aceleração de consulta não se limita ao Armazenamento do Lago de Dados (contas de armazenamento que têm o espaço hierárquico ativado). A aceleração da consulta é completamente compatível com as bolhas nas contas de armazenamento que **não** têm um espaço hierárquico de nome ativado nelas. Isto significa que pode obter a mesma redução na latência da rede e custos de cálculo quando processa dados que já armazenou como bolhas nas contas de armazenamento.

Para um exemplo de como utilizar a aceleração de consulta numa aplicação do cliente, consulte [os dados do Filtro utilizando a aceleração da consulta de armazenamento do Azure Data Lake](data-lake-storage-query-acceleration-how-to.md).

## <a name="data-flow"></a>Fluxo de dados

O diagrama seguinte ilustra como uma aplicação típica usa a aceleração da consulta para processar dados.

> [!div class="mx-imgBorder"]
> ![Visão geral da aceleração da consulta](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. A aplicação do cliente solicita dados de ficheiros especificando predicados e projeções de colunas.

2. A aceleração da consulta analisa a consulta SQL especificada e distribui o trabalho para analisar e filtrar dados.

3. Os processadores lêem os dados do disco, analisam os dados utilizando o formato apropriado e, em seguida, filtram os dados aplicando os predicados especificados e as projeções das colunas.

4. A aceleração da consulta combina os fragmentos de resposta para transmitir de volta para a aplicação do cliente.

5. A aplicação do cliente recebe e analisa a resposta transmitida. A aplicação não necessita de filtrar quaisquer dados adicionais e pode aplicar o cálculo ou transformação pretendido diretamente.

## <a name="better-performance-at-a-lower-cost"></a>Melhor desempenho a um custo mais baixo

A aceleração da consulta otimiza o desempenho reduzindo a quantidade de dados que são transferidos e processados pela sua aplicação.

Para calcular um valor agregado, as aplicações geralmente recuperam **todos** os dados de um ficheiro e, em seguida, processam e filtram os dados localmente. Uma análise dos padrões de entrada/saída para cargas de trabalho analíticas revela que as aplicações normalmente requerem apenas 20% dos dados que lêem para realizar qualquer cálculo. Esta estatística é verdadeira mesmo depois de aplicar técnicas como [a poda de partição.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning) Isto significa que 80% desses dados são desnecessariamente transferidos através da rede, analisados e filtrados por aplicações. Este padrão, essencialmente concebido para remover dados não eduptados, incorre num custo computacional significativo.  

Apesar de a Azure ter uma rede líder no setor, tanto em termos de produção como de latência, a transferência desnecessariamente de dados através dessa rede ainda é dispendiosa para o desempenho da aplicação. Ao filtrar os dados indesejados durante o pedido de armazenamento, a aceleração da consulta elimina este custo.

Além disso, a carga de CPU que é necessária para analisar e filtrar dados não necessários requer a sua aplicação para obter um maior número e VMs maiores para fazer o seu trabalho. Ao transferir esta carga de cálculo para a aceleração da consulta, as aplicações podem realizar poupanças significativas de custos.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>Aplicações que podem beneficiar da aceleração da consulta

A aceleração de consultas é projetada para quadros de análise distribuídos e aplicações de processamento de dados. 

Quadros de análise distribuídos, tais como Apache Spark e Apache Hive, incluem uma camada de abstração de armazenamento dentro do quadro. Estes motores também incluem otimizadores de consulta que podem incorporar conhecimento das capacidades do serviço de E/S subjacente ao determinar um plano de consulta ideal para consultas de utilizador. Estes quadros estão a começar a integrar a aceleração da consulta. Como resultado, os utilizadores destes quadros verão uma maior latência de consulta e um menor custo total de propriedade sem ter de fazer quaisquer alterações às consultas. 

A aceleração da consulta também é projetada para aplicações de processamento de dados. Estes tipos de aplicações normalmente realizam transformações de dados em larga escala que podem não levar diretamente a insights de análise para que nem sempre utilizem quadros de análise distribuídos estabelecidos. Estas aplicações têm muitas vezes uma relação mais direta com o serviço de armazenamento subjacente para que possam beneficiar diretamente de funcionalidades como a aceleração da consulta. 

Para um exemplo de como uma aplicação pode integrar a aceleração da consulta, consulte [os dados do Filtro utilizando a aceleração da consulta de armazenamento do Lago de Dados Azure](data-lake-storage-query-acceleration-how-to.md).

## <a name="pricing"></a>Preços

Devido ao aumento da carga de cálculo dentro do serviço de armazenamento de data de dados Azure, o modelo de preços para a utilização da aceleração de consulta difere do modelo normal de transação de armazenamento de data data lake. A aceleração da consulta cobra um custo pela quantidade de dados digitalizados, bem como um custo para a quantidade de dados devolvidos ao chamador. Para mais informações, consulte [o preço da Azure Data Lake Storage Gen2.](https://azure.microsoft.com/pricing/details/storage/data-lake/)

Apesar da alteração do modelo de faturação, o modelo de preços da Query Acceleration foi concebido para reduzir o custo total de propriedade para uma carga de trabalho, dada a redução dos custos de VM muito mais caros.

## <a name="next-steps"></a>Passos seguintes

- [Filtrar dados utilizando a aceleração da consulta de armazenamento do lago de dados Azure](data-lake-storage-query-acceleration-how-to.md)
- [Referência linguística SQL de aceleração de consulta](query-acceleration-sql-reference.md)


