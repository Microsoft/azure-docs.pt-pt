---
title: Fluxos de dados Wrangling em Azure Data Factory
description: Uma visão geral dos fluxos de dados do Wrangling no Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 7b46b1108246f0b83fcfce69844d19d01b1994c4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665651"
---
# <a name="what-are-wrangling-data-flows"></a>O que são fluxos de dados Wrangling?

As organizações precisam fazer a preparação de dados e Wrangling para uma análise precisa de dados complexos que continuam crescendo todos os dias. A preparação de dados é necessária para que as organizações possam usar os dados em vários processos de negócios e reduzir o tempo de implantação.

Os fluxos de dados do Wrangling no Azure Data Factory permitem que você faça uma preparação de dados sem código em escala de nuvem iterativamente. Os fluxos de dados do Wrangling integram-se ao [Power Query online](https://docs.microsoft.com/power-query/) e tornam Power Query funções M disponíveis para usuários data Factory.

O fluxo de dados Wrangling converte M gerado pelo editor de mashup do Power Query online no código do Spark para execução em escala de nuvem.

Os fluxos de dados do Wrangling são especialmente úteis para engenheiros de dados ou "integradores de dados do cidadão".

## <a name="use-cases"></a>Casos de utilização

### <a name="fast-interactive-data-exploration-and-preparation"></a>Exploração e preparação rápida de dados interativos

Vários engenheiros de dados e integradores de dados do cidadão podem explorar e preparar interativamente conjuntos de dados em escala de nuvem. Com o aumento do volume, variedade e velocidade dos dados em data lagos, os usuários precisam de uma maneira eficaz de explorar e preparar conjuntos de dados. Por exemplo, talvez seja necessário criar um conjunto de dados que ' tenha todas as informações demográficas do cliente para novos clientes desde 2017 '. Você não está mapeando para um destino conhecido. Você está explorando, Wrangling e preparando conjuntos de valores para atender a um requisito antes de publicá-lo no Lake. Os fluxos de dados Wrangling geralmente são usados para cenários de análise menos formal. Os conjuntos de informações de uso podem ser usados para fazer transformações e operações de aprendizado de máquina downstream.

### <a name="code-free-agile-data-preparation"></a>Preparação de dados ágil sem código

Os integradores de dados do cidadão gastam mais de 60% do tempo procurando e preparando dados. Eles estão procurando fazer isso de forma gratuita de código para melhorar a produtividade operacional. Permitir que os integradores de dados do cidadão enriquecem, formam e publiquem dados usando ferramentas conhecidas como Power Query online de forma escalonável aumentam drasticamente sua produtividade. O fluxo de dados do Wrangling no Azure Data Factory permite que o conhecido editor de mashup do Power Query online permita que os integradores de dados do cidadão corrijam erros rapidamente, padronize dados e produzam dados de alta qualidade para dar suporte às decisões de negócios.

### <a name="data-validation"></a>Validação de dados

Examine visualmente seus dados de forma livre de código para remover exceções, anomalias e ti de acordo com uma forma para análise rápida.

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar um fluxo de dados do Wrangling](wrangling-data-flow-tutorial.md).