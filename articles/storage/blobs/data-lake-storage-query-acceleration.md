---
title: Aceleração da consulta de armazenamento do lago Azure Data (pré-visualização)
description: A aceleração da consulta (pré-visualização) é uma nova capacidade para o Armazenamento de Lagos de Dados Azure que permite que aplicações e quadros de análise otimizem drasticamente o processamento de dados, recuperando apenas os dados necessários para uma operação de processamento.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 04/21/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 42eec4a0796a7f07c7e7d1c35571d9d4ddcf69d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176707"
---
# <a name="azure-data-lake-storage-query-acceleration-preview"></a>Aceleração da consulta de armazenamento do lago Azure Data (pré-visualização)

A aceleração da consulta (pré-visualização) é uma nova capacidade para o Armazenamento de Lagos de Dados Azure que permite que aplicações e quadros de análise otimizem drasticamente o processamento de dados, recuperando apenas os dados que necessitam para realizar uma determinada operação. Isto reduz o tempo e o poder de processamento necessários para obter informações críticas sobre os dados armazenados.

> [!NOTE]
> A funcionalidade de aceleração da consulta está em pré-visualização pública, e está disponível nas regiões centrais do Canadá e da França Central. Para rever as limitações, consulte o artigo sobre [questões conhecidas.](data-lake-storage-known-issues.md) Para se inscrever na pré-visualização, consulte [este formulário](https://aka.ms/adls/qa-preview-signup).  

## <a name="overview"></a>Descrição geral

A aceleração da consulta aceita filtrar *predicados* e projeções de *colunas* que permitem às aplicações filtrar linhas e colunas no momento em que os dados são lidos a partir do disco. Apenas os dados que satisfaça as condições de um predicado são transferidos para a rede para a aplicação. Isto reduz a latência da rede e o custo da computação.  

Pode utilizar o SQL para especificar os predicados do filtro de linha e as projeções da coluna num pedido de aceleração de consulta. Um pedido processa apenas um ficheiro. Portanto, as características relacionais avançadas do SQL, tais como adesões e grupo por agregados, não são suportadas. A aceleração da consulta suporta os dados formatados CSV e JSON como entrada para cada pedido.

A funcionalidade de aceleração da consulta não se limita ao Armazenamento de Data Lake (contas de armazenamento que têm o espaço hierárquico de nomes habilitado neles). A aceleração da consulta é completamente compatível com as bolhas nas contas de armazenamento que **não** têm um espaço de nome hierárquico habilitado nelas. Isto significa que pode obter a mesma redução na latência da rede e nos custos de computação quando processa dados que já armazenou como bolhas em contas de armazenamento.

Para um exemplo de como usar a aceleração da consulta numa aplicação de cliente, consulte [os dados do Filtro utilizando a aceleração](data-lake-storage-query-acceleration-how-to.md)da consulta de armazenamento do Lago de Dados Azure .

## <a name="data-flow"></a>Fluxo de dados

O diagrama que se segue ilustra como uma aplicação típica utiliza a aceleração da consulta para processar dados.

> [!div class="mx-imgBorder"]
> ![Visão geral da aceleração da consulta](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. A aplicação do cliente solicita dados de ficheiros especificando predicados e projeções de colunas.

2. A aceleração da consulta analisa a consulta sql especificada e distribui trabalhos para analisar e filtrar dados.

3. Os processadores lêem os dados do disco, analisam os dados utilizando o formato apropriado e, em seguida, filtram os dados aplicando os predicados especificados e as projeções da coluna.

4. A aceleração da consulta combina os fragmentos de resposta para transmitir de volta para a aplicação do cliente.

5. A aplicação do cliente recebe e analisa a resposta transmitida. A aplicação não precisa de filtrar quaisquer dados adicionais e pode aplicar diretamente o cálculo ou transformação desejados.

## <a name="better-performance-at-a-lower-cost"></a>Melhor desempenho a um custo mais baixo

A aceleração da consulta otimiza o desempenho reduzindo a quantidade de dados que são transferidos e processados pela sua aplicação.

Para calcular um valor agregado, as aplicações geralmente recuperam **todos os** dados de um ficheiro e, em seguida, processam e filtram os dados localmente. Uma análise dos padrões de entrada/saída para cargas de trabalho analíticas revela que as aplicações normalmente requerem apenas 20% dos dados que lêem para realizar qualquer cálculo. Esta estatística é verdadeira mesmo depois de aplicar técnicas como [a poda de partição.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning) Isto significa que 80% desses dados são transferidos desnecessariamente através da rede, analisados e filtrados por aplicações. Este padrão, essencialmente concebido para remover dados desnecessários, incorre num custo significativo da computação.  

Embora o Azure tenha uma rede líder no setor, tanto em termos de entrada como de latência, transferir desnecessariamente dados para toda essa rede ainda é dispendioso para o desempenho da aplicação. Filtrando os dados indesejados durante o pedido de armazenamento, a aceleração da consulta elimina este custo.

Além disso, a carga cpu que é necessária para analisar e filtrar dados desnecessários requer a sua aplicação para fornecer um maior número e VMs maiores para fazer o seu trabalho. Ao transferir esta carga computacional para a aceleração da consulta, as aplicações podem realizar poupanças significativas de custos.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>Aplicações que podem beneficiar da aceleração da consulta

A aceleração da consulta foi concebida para quadros de análise distribuídos e aplicações de processamento de dados. 

Quadros de análise distribuídos, como Apache Spark e Apache Hive, incluem uma camada de abstração de armazenamento dentro do quadro. Estes motores também incluem otimizadores de consultas que podem incorporar conhecimento das capacidades do serviço de I/S subjacente ao determinar um plano de consulta ideal para consultas de utilizador. Estes quadros começam a integrar a aceleração da consulta. Como resultado, os utilizadores destes quadros verão uma maior latência de consultas e um menor custo total de propriedade sem ter que fazer quaisquer alterações nas consultas. 

A aceleração da consulta também é projetada para aplicações de processamento de dados. Estes tipos de aplicações normalmente realizam transformações de dados em larga escala que podem não levar diretamente a insights de análise para que nem sempre utilizem quadros de análise distribuídos estabelecidos. Estas aplicações têm frequentemente uma relação mais direta com o serviço de armazenamento subjacente para que possam beneficiar diretamente de funcionalidades como aceleração de consultas. 

Para um exemplo de como uma aplicação pode integrar a aceleração da consulta, consulte [os dados do Filtro utilizando a aceleração](data-lake-storage-query-acceleration-how-to.md)da consulta de armazenamento do Lago de Dados Azure .

## <a name="pricing"></a>Preços

Devido ao aumento da carga computacional dentro do serviço de armazenamento de lagos de dados Azure, o modelo de preços para a utilização da aceleração da consulta difere do modelo normal de transação de armazenamento de lagos de dados do Azure. A aceleração da consulta cobra um custo pela quantidade de dados digitalizados, bem como um custo pela quantidade de dados devolvidos ao chamador. Para mais informações, consulte o preço do [Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

Apesar da alteração do modelo de faturação, o modelo de preços da Query Acceleration foi concebido para reduzir o custo total de propriedade para uma carga de trabalho, dada a redução dos custos de VM muito mais caros.

## <a name="next-steps"></a>Passos seguintes

- [Formulário de inscrição de aceleração de consulta](https://aka.ms/adls/qa-preview-signup)    
- [Filtrar dados utilizando a aceleração da consulta de armazenamento do lago de dados do Azure (pré-visualização)](data-lake-storage-query-acceleration-how-to.md)
- [Referência linguística SQL de aceleração de consulta (pré-visualização)](query-acceleration-sql-reference.md)


