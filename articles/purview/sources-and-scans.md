---
title: Origens de dados e tipos de ficheiro suportados
description: Este artigo fornece detalhes conceptuais sobre fontes de dados suportadas e tipos de ficheiros em Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 9a73f9b734d5404d07e05dd37d5ad8571c1aab2e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383896"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Fontes de dados suportadas e tipos de ficheiros em Azure Purview

Este artigo discute fontes de dados suportadas, tipos de ficheiros e conceitos de digitalização em Purview.

## <a name="supported-data-sources"></a>Supported data sources (Origens de dados suportadas)

A Azure Purview suporta as seguintes fontes:

| Tipo de loja | Tipo de auth suportado | Configurar digitalizações via UX/PowerShell |
| ---------- | ------------------- | ------------------------------ |
| Servidor SQL no local                   | SQL Auth                        | UX                                |
| Azure Synapse Analytics (anteriormente SQL DW)            | SQL Auth, Diretor de Serviços, MSI               | UX                             |
| Base de Dados Azure SQL (DB)                  | SQL Auth, Diretor de Serviços, MSI               | UX |
| Instância Gerida da Base de Dados SQL do Azure      | SQL Auth, Diretor de Serviços, MSI               | UX    |
| Armazenamento de Blobs do Azure                       | Chave de Conta, Diretor de Serviço, MSI | UX            |
| Azure Data Explorer                      | Principal de Serviço                              | UX            |
| Azure Data Lake Storage Gen1 (ADLS Gen1) | Diretor de Serviço, MSI                              | UX            |
| Azure Data Lake Storage Gen2 (ADLS Gen2) | Chave de Conta, Diretor de Serviço, MSI            | UX            |
| Azure Cosmos DB                          | Chave de conta                                    | UX            |


> [!Note]
> O Azure Data Lake Storage Gen2 já está em disponibilidade geral. Recomendamos que comece a utilizar hoje. Para mais informações, consulte a página do [produto.](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/)

## <a name="file-types-supported-for-scanning"></a>Tipos de ficheiros suportados para digitalização

Os seguintes tipos de ficheiros são suportados para digitalização, para extração e classificação de esquemas, se aplicável:

- Formatos de ficheiros estruturados suportados por extensão: AVRO, ORC, PARQUET, CSV, JSON, PSV, SSV, TSV, TXT, XML
- Formatos de ficheiros de documento suportados por extensão: DOC, DOCM, DOCX, DOT, ODP, ODS, ODT, PDF, POT, PPS, PPSX, PPT, PPTM, PPTX, XLC, XLSB, XLSM, XLSX, XLT, XLT
- A Purview também suporta extensões de ficheiros personalizadas e parsers personalizados.

## <a name="sampling-within-a-file"></a>Amostragem dentro de um ficheiro

Na terminologia de Purview,
- Varredura L1: Extrai informações básicas e meta dados como nome de ficheiro, tamanho e nome totalmente qualificado
- Varredura L2: Extrai esquema para tipos de ficheiros estruturados e tabelas de bases de dados
- L3 digitalização: Extrai esquema quando aplicável e sujeita o ficheiro amostrado ao sistema e às regras de classificação personalizadas

Para todos os formatos de ficheiros estruturados, os ficheiros de amostras de scanner purview da seguinte forma:

- Para tipos de ficheiros estruturados, ele amostra 128 linhas em cada coluna ou 1 MB, o que for menor.
- Para formatos de ficheiros de documentos, ele amostra 20 MB de cada ficheiro.
    - Se um ficheiro de documento for superior a 20 MB, então não está sujeito a uma verificação profunda (sujeito a classificação). Nesse caso, o Purview captura apenas metadados básicos como nome de ficheiro e nome totalmente qualificado.

## <a name="resource-set-file-sampling"></a>Amostragem de ficheiros conjunto de recursos

Uma pasta ou grupo de ficheiros de partição é detetado como um *conjunto de recursos* em Purview, se corresponder a uma política definida por recursos do sistema ou a uma política definida pelo cliente de conjunto de recursos. Se for detetado um conjunto de recursos, o Purview amostrará cada pasta que contém. Saiba mais sobre conjuntos de recursos [aqui.](concept-resource-sets.md)

Amostragem de ficheiros para conjuntos de recursos por tipos de ficheiros:

- **Ficheiros delimitados (CSV, PSV, SSV, TSV)** - 1 em cada 100 ficheiros são amostrados (varredura L3) dentro de uma pasta ou grupo de ficheiros de partição que são considerados um 'conjunto de recursos'
- **Os tipos de ficheiros Data Lake (Parquet, Avro, Orc)** - 1 em 18446744073709551615 (long max) são amostrados (varredura L3) dentro de uma pasta ou grupo de ficheiros de partição que são considerados um conjunto de *recursos*
- **Outros tipos de ficheiros estruturados (JSON, XML, TXT)** - 1 em cada 100 ficheiros são amostrados (varredura L3) dentro de uma pasta ou grupo de ficheiros de partição que são considerados um 'conjunto de recursos'
- **Objetos SQL e entidades CosmosDB** - Cada ficheiro é digitalizado L3.
- **Tipos de ficheiros de** documentos - Cada ficheiro é digitalizado L3. Os padrões de definição de recursos não se aplicam a estes tipos de ficheiros.

## <a name="scan-regions"></a>Regiões de digitalização
Segue-se uma lista de todas as regiões de fonte de dados Azure (data center) onde funciona o scanner de Purview. Se a sua fonte de dados Azure estiver numa região fora desta lista, o scanner será executado na região da sua instância Desres visão.
 
### <a name="purview-scanner-regions"></a>Regiões de scanner de competência

- EastUs
- EastUs2 
- Sul-Centro
- WestUs
- WestUs2
- Sudeste da Ásia
- Europa Ocidental
- NorthEurope
- UkSouth
- AustráliaEast
- Centro do Canadá
- BrazilSouth
- CentralIndia
- JapãoEast
- Sul-AfricaNorth
- FranceCentral

## <a name="classification"></a>Classificação

Todas as 105 regras de classificação do sistema aplicam-se aos formatos de ficheiros estruturados. Apenas as regras de classificação MCE se aplicam aos tipos de ficheiros de documentos (não os padrões de regex nativos, deteção baseada em filtros de flor). Para obter mais informações sobre classificações apoiadas, consulte [classificações apoiadas em Azure Purview](supported-classifications.md).

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Executar o kit de arranque e digitalizar dados](tutorial-scan-data.md)
- [Gerir fontes de dados em Azure Purview (Pré-visualização)](manage-data-sources.md)