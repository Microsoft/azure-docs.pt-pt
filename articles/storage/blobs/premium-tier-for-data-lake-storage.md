---
title: Nível premium para armazenamento do Lago de Dados Azure / Microsoft Docs
description: Use o nível de desempenho premium com Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/28/2020
ms.author: normesta
ms.openlocfilehash: 9704ff7dec02a0de044863ec2da6280d65b30ef0
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043520"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Nível premium para armazenamento de lago de dados Azure

A Azure Data Lake Storage Gen2 suporta agora o [nível de desempenho premium.](storage-blob-performance-tiers.md#premium-performance) O nível de desempenho premium é ideal para aplicações de big data analytics e cargas de trabalho que requerem baixa latência consistente e têm um elevado número de transações. 

> [!NOTE]
> O nível premium para Azure Data Lake Storage está em pré-visualização pública. Ver [disponibilidade regional.](#regional-availability)

## <a name="workloads-that-can-benefit-from-the-premium-performance-tier"></a>Cargas de trabalho que podem beneficiar do nível de desempenho premium

As cargas de trabalho de exemplo incluem cargas de trabalho interativas, IoT, análise de streaming, inteligência artificial e machine learning. 

**Cargas de trabalho interativas** 

Estas cargas de trabalho requerem atualizações instantâneas e feedback do utilizador, tais como aplicações de e-commerce e mapeamento, aplicações de vídeo interativas, etc. Por exemplo, numa aplicação de e-commerce, os itens menos vistos provavelmente não são em cache. No entanto, devem ser exibidos instantaneamente ao cliente a pedido. Como outro exemplo, cientistas de dados, analistas e desenvolvedores podem obter insights sensíveis ao tempo ainda mais rapidamente, executando consultas sobre dados armazenados numa conta que usa o nível de desempenho premium. 

**Análise ioT/streaming** 

Num cenário IoT, muitas operações de escrita menores podem ser empurradas para a nuvem a cada segundo. Grandes quantidades de dados podem ser ingeridos, agregados para fins de análise, e depois eliminados quase imediatamente. As elevadas capacidades de ingestão do nível de desempenho premium tornam-no eficiente para este tipo de carga de trabalho. 

**Inteligência artificial/aprendizagem automática (IA/ML)** 

A IA/ML trata do consumo e processamento de diferentes tipos de dados, como visuais, fala e texto. Este tipo de carga de trabalho de alta qualidade lida com grandes quantidades de dados que requerem uma resposta rápida e tempos de ingestão eficientes para análise de dados. 

## <a name="cost-effectiveness"></a>Rentabilidade

O nível de desempenho premium tem um custo de armazenamento mais elevado, mas um custo de transação mais baixo em comparação com o nível de desempenho padrão. Se as suas aplicações e cargas de trabalho executarem um grande número de transações, o nível de desempenho premium pode ser rentável.

O quadro seguinte demonstra a eficácia do custo-eficácia do nível premium para o armazenamento do Lago de Dados Azure. Cada coluna representa o número de transações num mês.  Cada linha representa a percentagem de transações que são lidas. Cada célula na tabela mostra a percentagem de redução de custos associada a uma percentagem de transação de leitura e o número de transações executadas. 

Por exemplo, assumindo que a sua conta está na região leste dos EUA 2, o número de transações com a sua conta excede 90M, e 70% dessas transações são transações lidas, o nível de desempenho premium é mais rentável.

> [!div class="mx-imgBorder"]
> ![imagem vai aqui](./media/premium-tier-for-data-lake-storage/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE] 
> Se preferir avaliar a eficácia dos custos com base no número de transações por segundo para cada TB de dados, pode utilizar as rubricas de coluna que aparecem na parte inferior da tabela.

Para obter mais informações sobre preços, consulte a página [de preços da Azure Data Lake Storage Gen2.](https://azure.microsoft.com/pricing/details/storage/data-lake/)

## <a name="feature-availability"></a>Disponibilidade de funcionalidades 

Algumas funcionalidades de armazenamento Blob podem não estar disponíveis ou podem ter apenas suporte parcial com o nível de desempenho premium. Para obter uma lista completa, consulte [as funcionalidades de armazenamento Blob disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md). Em seguida, reveja uma lista de [questões conhecidas](data-lake-storage-known-issues.md) para avaliar eventuais lacunas na funcionalidade.

## <a name="enabling-the-premium-performance-tier"></a>Habilitando o nível de desempenho premium 

Pode utilizar o nível premium para o Armazenamento do Lago de Dados Azure, criando uma conta BlockBlobStorage com a definição **de espaço hierárquico** **ativada** . Para obter uma orientação completa, consulte [Criar uma conta BlockBlobStorage.](storage-blob-create-account-block-blob.md)

Ao criar a conta, certifique-se de escolher a opção de desempenho **Premium** e o tipo de conta **BlockBlobStorage.**

> [!div class="mx-imgBorder"]
> ![Criar blockblobstorageacount](./media/premium-tier-for-data-lake-storage/create-block-blob-storage-account.png)

Ative a definição **de espaço de nome hierárquico** no separador **Avançado** da página de conta de **armazenamento Criar.** Tem de ativar esta definição quando criar a conta. Não pode permitir depois.

A imagem a seguir mostra esta definição na página de **conta de armazenamento Criar.**

> [!div class="mx-imgBorder"]
> ![Definição hierárquica do espaço de nome](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="regional-availability"></a>Disponibilidade regional

O nível premium para o Azure Data Lake Storage está disponível nas seguintes regiões.

|Região|Redundância|
|--|--|
|E.U.A. Leste|LRS, ZRS|
|E.U.A. Leste 2|LRS, ZRS|
|E.U.A. Central|LRS|
|E.U.A. Oeste|LRS|
|E.U.A. Oeste 2|LRS, ZRS|
|E.U.A. Centro-Oeste|LRS|
|E.U.A. Centro-Sul|LRS|
|Canadá Central|LRS|
|Leste do Canadá|LRS|
|Europa do Norte|LRS, ZRS|
|Europa Ocidental|LRS, ZRS|
|Sul do Reino Unido|LRS|
|Oeste do Reino Unido|LRS|
|França Central|LRS|
|Ásia Oriental|LRS|
|Coreia do Sul Central|LRS|
|Sul da Coreia do Sul|LRS|
|Índia Central|LRS|
|Oeste da Índia|LRS|
|Uae Norte|LRS|
|Leste do Japão|LRS|
|Oeste do Japão|LRS|
|Ásia Sudeste|LRS, ZRS|
|Leste da Austrália|LRS, ZRS|
|Sudeste da Austrália|LRS|
|Sul do Brasil|LRS|


## <a name="next-steps"></a>Passos seguintes

Consulte o nível premium para anúncio do [blog](https://www.microsoft.com)Azure Data Lake Storage .