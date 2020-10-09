---
title: Níveis de desempenho de armazenamento de blob de blocos - Azure Storage
description: Discute a diferença entre os níveis de desempenho premium e standard para o armazenamento do bloco Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ff82986b27d038c536872b07e1308b0d48fadaef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74270230"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Níveis de desempenho do armazenamento dos blobs de blocos

À medida que as empresas implementam aplicações nativas de nuvem sensíveis ao desempenho, é importante ter opções para armazenamento de dados rentáveis em diferentes níveis de desempenho.

O armazenamento de blob de bloco azure oferece dois níveis de desempenho diferentes:

- **Premium**: otimizado para altas taxas de transação e latência de armazenamento consistente de um dígito
- **Standard**: otimizado para alta capacidade e alta produção

Aplicam-se as seguintes considerações aos diferentes níveis de desempenho:

| Área |Desempenho padrão  |Desempenho premium  |
|---------|---------|---------|
|Disponibilidade de região     |   Todas as regiões      | Em [regiões selecionadas](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|Tipos de [conta de armazenamento suportados](../common/storage-account-overview.md#types-of-storage-accounts)     |     Finalidade geral v2, BlobStorage, Finalidade Geral v1    |    BlockBlobStorage     |
|Suporta [bolhas de blocos de produção elevadas](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    Sim, com mais de 4 tamanhos MiB PutBlock ou PutBlob     |    Sim, com mais de 256 tamanhos KiB PutBlock ou PutBlob    |
|Redundância     |     Ver [Tipos de contas de armazenamento](../common/storage-account-overview.md#types-of-storage-accounts)   |  Atualmente, suporta apenas o armazenamento localmente redundante (LRS) e o armazenamento de zona-redudante (ZRS)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup> O armazenamento redundante de zona (ZRS) está disponível em regiões selecionadas para contas de armazenamento de blocos de desempenho premium.</div>

No que diz respeito ao custo, o desempenho premium fornece preços otimizados para aplicações com altas taxas de transação para ajudar a [reduzir o custo total de armazenamento](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) destas cargas de trabalho.

## <a name="premium-performance"></a>Desempenho premium

O armazenamento de blob de bloco de desempenho premium disponibiliza os dados através de hardware de alto desempenho. Os dados são armazenados em unidades de estado sólido (SSDs) que são otimizadas para baixa latência. Os SSDs fornecem uma produção mais elevada em comparação com os discos rígidos tradicionais.

O armazenamento de desempenho premium é ideal para cargas de trabalho que requerem tempos de resposta rápidos e consistentes. É melhor para cargas de trabalho que realizam muitas pequenas transações. Exemplo de cargas de trabalho incluem:

- **Cargas de trabalho interativas.** Estas cargas de trabalho requerem atualizações instantâneas e feedback do utilizador, como aplicações de e-commerce e mapeamento. Por exemplo, numa aplicação de e-commerce, os itens menos vistos provavelmente não são em cache. No entanto, devem ser exibidos instantaneamente ao cliente a pedido.

- **Análise**. Num cenário IoT, muitas operações de escrita menores podem ser empurradas para a nuvem a cada segundo. Grandes quantidades de dados podem ser recolhidos, agregados para fins de análise, e depois eliminados quase imediatamente. As elevadas capacidades de ingestão de blocos de armazenamento premium tornam-no eficiente para este tipo de carga de trabalho.

- **Inteligência artificial/aprendizagem automática (IA/ML)**. A IA/ML trata do consumo e processamento de diferentes tipos de dados, como visuais, fala e texto. Este tipo de carga de trabalho de alta qualidade lida com grandes quantidades de dados que requerem uma resposta rápida e tempos de ingestão eficientes para análise de dados.

- **Transformação de dados.** Processos que requerem edição, modificação e conversão constante de dados requerem atualizações instantâneas. Para uma representação precisa dos dados, os consumidores destes dados devem ver estas alterações refletidas imediatamente.

## <a name="standard-performance"></a>Desempenho padrão

O desempenho padrão suporta diferentes níveis de acesso para armazenar [dados](storage-blob-storage-tiers.md) da forma mais rentável. É otimizado para alta capacidade e alta produção em grandes conjuntos de dados.

- **Conjuntos de dados de backup e recuperação de desastres**. O armazenamento padrão de desempenho oferece níveis eficientes em termos de custos, tornando-o um caso de uso perfeito tanto para conjuntos de dados de recuperação de desastres a curto e longo prazo, backups secundários e arquivo de dados de conformidade.

- **Conteúdo sonoro**. As imagens e vídeos são frequentemente acedidos quando criados e armazenados pela primeira vez, mas este tipo de conteúdo é usado menos frequentemente à medida que envelhece. O armazenamento padrão de desempenho oferece níveis adequados para as necessidades de conteúdo dos meios de comunicação. 

- **Processamento de dados em massa.** Este tipo de cargas de trabalho são adequados para armazenamento padrão porque requerem armazenamento de alta produção rentável em vez de uma latência consistente e baixa. Conjuntos de dados grandes e crus são encenados para processamento e eventualmente migram para níveis mais frios.

## <a name="migrate-from-standard-to-premium"></a>Migrar de padrão para premium

Não é possível converter uma conta de armazenamento de desempenho padrão existente numa conta de armazenamento de blob com desempenho premium. Para migrar para uma conta de armazenamento de desempenho premium, você deve criar uma conta BlockBlobStorage, e migrar os dados para a nova conta. Para obter mais informações, consulte [Criar uma conta BlockBlobStorage](storage-blob-create-account-block-blob.md).

Para copiar bolhas entre contas de armazenamento, pode utilizar a versão mais recente da ferramenta de linha de comando [AzCopy.](../common/storage-use-azcopy-blobs.md) Outras ferramentas como a Azure Data Factory também estão disponíveis para o movimento e transformação de dados.

## <a name="blob-lifecycle-management"></a>Gestão do ciclo de vida blob

A gestão do ciclo de vida de armazenamento de blob oferece uma política rica e baseada em regras:

- **Premium**: Expire dados no final do seu ciclo de vida.
- **Standard**: Transitar dados para o melhor nível de acesso e expirar dados no final do seu ciclo de vida.

Para saber mais, consulte [Gerir o ciclo de vida de armazenamento Azure Blob](storage-lifecycle-management-concepts.md).

Não é possível mover dados que são armazenados numa conta de armazenamento de blocos premium entre camadas quentes, frias e de arquivo. No entanto, pode copiar bolhas de uma conta de armazenamento de blob de bloco para o nível de acesso quente numa conta *diferente.* Para copiar dados para uma conta diferente, utilize o [Bloco de Put From URL](/rest/api/storageservices/put-block-from-url) API ou [AzCopy v10](../common/storage-use-azcopy-v10.md). O **Bloco de Colocação da API url** copia sincronizadamente os dados no servidor. A chamada só termina depois de todos os dados terem sido transferidos da localização original do servidor para a localização do destino.

## <a name="next-steps"></a>Passos seguintes

Avalie o calor, o frio e o arquivo nas contas de armazenamento GPv2 e Blob.

- [Saiba mais sobre a reidratação de dados blob a partir do nível de arquivo](storage-blob-rehydration.md)
- [Avaliar a utilização das suas contas do Storage atuais ao ativar as métricas do Storage do Azure](../common/storage-enable-and-view-metrics.md)
- [Verifique preços quentes, frescos e de arquivo nas contas blob e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)
- [Verificar os preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
