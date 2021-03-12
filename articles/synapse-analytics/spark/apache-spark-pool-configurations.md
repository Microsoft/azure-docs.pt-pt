---
title: Conceitos de piscina de Faíscas Apache
description: Introdução aos tamanhos e configurações da piscina Apache Spark no Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 12/2/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: e68b8aff700519b6bef31e5126c91fa8bc4a3593
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103202561"
---
# <a name="apache-spark-pool-configurations-in-azure-synapse-analytics"></a>Configurações da piscina Apache Spark em Azure Synapse Analytics

Um pool spark é um conjunto de metadados que define os requisitos de recursos compute e características de comportamento associadas quando um exemplo de Faísca é instantâneo. Estas características incluem, mas não se limitam ao nome, número de nós, tamanho do nó, comportamento de escala e tempo de vida. Uma piscina de faíscas por si só não consome quaisquer recursos. Não há custos incorridos com a criação de piscinas Spark. As cargas só são incorridas quando um trabalho de Spark é executado na piscina de Spark alvo e a instância Spark é instantânea a pedido.

Você pode ler como criar uma piscina spark e ver todas as suas propriedades aqui [Começar com piscinas Spark em Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md)

## <a name="nodes"></a>Nós

A instância da piscina Apache Spark consiste num nó de cabeça e dois ou mais nós de trabalhador com um mínimo de três nós num caso de Faísca.  O nó de cabeça gere serviços de gestão adicionais como Livy, Yarn Resource Manager, Zookeeper e o motorista da Spark.  Todos os nós gerem serviços como o Node Agent e o Yarn Node Manager. Todos os nós operários dirigem o serviço de Executor de Faíscas.

## <a name="node-sizes"></a>Tamanhos dos nódoas

Uma piscina Spark pode ser definida com tamanhos de nó que variam de um nó de computação pequeno com 8 vCore e 64 GB de memória até um nó de computação XXLarge com 64 vCore e 432 GB de memória por nó. Os tamanhos dos nóleiros podem ser alterados após a criação da piscina, embora o caso possa ter de ser reiniciado.

|Tamanho | vCore | Memória|
|-----|------|-------|
|Pequeno|4|32 GB|
|Médio|8|64 GB|
|Grande|16|128 GB|
|XLarge|32|256 GB|
|XXLarge|64|432 GB|

## <a name="autoscale"></a>Dimensionamento Automático

As piscinas Apache Spark proporcionam a capacidade de aumentar e descer automaticamente os recursos computacional com base na quantidade de atividade.  Quando a função de autoescalação estiver ativada, pode definir o número mínimo e máximo de nós à escala.
Quando a função de autoescalação estiver desativada, o número de nós definidos permanecerá fixo.  Esta definição pode ser alterada após a criação da piscina, embora a instância possa ter de ser reiniciada.

## <a name="automatic-pause"></a>Pausa automática

A função de pausa automática liberta recursos após um período de inatividade definido, reduzindo o custo global de uma piscina Apache Spark.  O número de minutos de tempo inativo pode ser definido uma vez que esta função esteja ativada.  A função de pausa automática é independente da função de autoescala. Os recursos podem ser interrompidos quer a autoescalação esteja ativada ou desativada.  Esta definição pode ser alterada após a criação da piscina, embora a instância possa ter de ser reiniciada.

## <a name="next-steps"></a>Passos seguintes

* [Azure Synapse Analytics](/azure/synapse-analytics)
* [Documentação de faíscas apache](https://spark.apache.org/docs/2.4.5/)
