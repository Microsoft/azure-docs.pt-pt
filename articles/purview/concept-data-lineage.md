---
title: Linhagem de dados em Azure Purview (pré-visualização)
description: Descreve os conceitos de linhagem de dados.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: 476355f41de5e0e6aaffdedea8947cab5221767a
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200736"
---
# <a name="data-lineage-in-azure-purview-data-catalog-client"></a>Linhagem de dados no cliente do Catálogo de Dados Azure Purview

Este artigo fornece uma visão geral da linhagem de dados no Catálogo de Dados do Azure Purview. Também detalha como os sistemas de dados podem integrar-se com o catálogo para capturar a linhagem de dados. A visão pode capturar a linhagem de dados em diferentes partes do espólio de dados da sua organização, e em diferentes níveis de preparação, incluindo:

- Dados completamente brutos encenados a partir de várias plataformas
- Dados transformados e preparados
- Dados utilizados pelas plataformas de visualização.

## <a name="use-cases"></a>Casos de Utilização

A linhagem de dados é amplamente entendida como o ciclo de vida que abrange a origem dos dados, e onde se move ao longo do tempo através da propriedade de dados. É usado para diferentes tipos de cenários de retrospetiva, tais como resolução de problemas, rastreio de raiz em gasodutos de dados e depuração. A linhagem também é usada para análise de qualidade de dados, conformidade e cenários de "e se" muitas vezes referidos como análise de impacto. A linhagem é representada visualmente para mostrar dados que se deslocam de origem para destino, incluindo a forma como os dados foram transformados. Dada a complexidade da maioria dos ambientes de dados empresariais, estas opiniões podem ser difíceis de entender sem fazer alguma consolidação ou mascaramento de pontos de dados periféricos.

## <a name="lineage-experience-in-azure-purview-data-catalog"></a>Experiência de linhagem no Catálogo de Dados Azure Purview

O Purview Data Catalog irá ligar-se a outros sistemas de processamento, armazenamento e análise de dados para extrair informações de linhagem. A informação é combinada para representar uma experiência genérica e específica de cenário no Catálogo.

:::image type="content" source="media/concept-lineage/lineage-end-end.png" alt-text="linhagem end-end mostrando dados copiados da loja blob todo o caminho para power BI dashboard":::

A sua propriedade de dados pode incluir sistemas que estão a fazer a extração de dados, a transformação (sistemas ETL/ELT), sistemas de análise e visualização. Cada um dos sistemas captura metadados estáticos e operacionais ricos que descrevem o estado e a qualidade dos dados dentro da fronteira dos sistemas. O objetivo da linhagem num catálogo de dados é extrair os metadados de movimento, transformação e operacional de cada sistema de dados no menor grão possível.

O exemplo a seguir é um caso típico de utilização de dados que se movem em vários sistemas, onde o Catálogo de Dados se ligaria a cada um dos sistemas de linhagem.

- A Data Factory copia dados da zona on-prem/raw para uma zona de aterragem na nuvem. 
- Sistemas de processamento de dados como o Synapse, Databricks processariam e transformariam dados da zona de aterragem para a zona curada usando cadernos.
- Processamento adicional de dados em modelos analíticos para um melhor desempenho e agregação de consultas. 
- Os sistemas de visualização de dados consumirão os conjuntos de dados e processarão através do seu modelo de meta para criar um bi dashboard, experiências ML e assim por diante.

## <a name="lineage-granularity"></a>Granularidade da linhagem

Esta secção abrange os detalhes sobre a granularidade de que a informação da linhagem é recolhida por um catálogo de dados. Esta granularidade pode variar em função dos sistemas de dados que estão sendo.

### <a name="entity-level-lineage-sources--process--targets"></a>Linhagem de nível de entidade: Origem(s) > Processo > Alvo(s) 

- A linhagem é representada como um gráfico, normalmente contém entidades de origem e alvo em sistemas de armazenamento de dados que estão ligadas por um processo invocado por um sistema de computação. 
- Os sistemas de dados conectam-se ao catálogo de dados para gerar e reportar um objeto único que faz referência ao objeto físico do sistema de dados subjacente, por exemplo: SQL Procedimento armazenado, cadernos, e assim por diante.
- A linhagem de alta fidelidade com metadados adicionais como a propriedade é capturada para mostrar a linhagem num formato legível humano para entidades alvo & de origem. por exemplo: linhagem a um nível de mesa de colmeia em vez de divisórias ou nível de ficheiro.

### <a name="column-or-attribute-level-lineage"></a>Linhagem de nível de coluna ou atributo

Identificar atributos de uma entidade-fonte que é utilizada para criar ou derivar atributos na entidade-alvo. O nome do atributo de origem pode ser retido ou renomeado num alvo. Sistemas como o ADF podem fazer uma cópia um-um do ambiente no local para a nuvem. Por exemplo: `Table1/ColumnA -> Table2/ColumnA`.

### <a name="process-execution-status"></a>Estado da execução do processo

Para apoiar a análise de causas de raiz e cenários de qualidade de dados, capturamos o estado de execução dos postos de trabalho nos sistemas de processamento de dados. Este requisito nada tem a ver com a substituição das capacidades de monitorização de outros sistemas de processamento de dados, nem o objetivo é substituí-los. 

## <a name="summary"></a>Resumo

A linhagem é uma característica crítica do Catálogo de Dados da Purview para suportar cenários de qualidade, confiança e auditoria. O objetivo de um catálogo de dados é construir uma estrutura robusta onde todos os sistemas de dados dentro do seu ambiente possam naturalmente ligar e reportar a linhagem. Uma vez disponibilizados os metadados, o catálogo de dados pode reunir os metadados fornecidos pelos sistemas de dados para potenciar casos de utilização da governação de dados.

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Criar uma conta Azure Purview no portal Azure](create-catalog-portal.md)
* [Quickstart: Criar uma conta Azure Purview utilizando Azure PowerShell/Azure CLI](create-catalog-powershell.md)
* [Quickstart: Use o Estúdio De Visão](use-purview-studio.md)
