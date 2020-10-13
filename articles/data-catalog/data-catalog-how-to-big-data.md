---
title: Como catalogar big data no Catálogo de Dados Azure
description: Como-a-lei destacando padrões para a utilização do Catálogo de Dados Azure com fontes de dados de "big data", incluindo Azure Blob Storage, Azure Data Lake e Hadoop HDFS.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: e79e5e16b300fd02b9c9124f9677747834f22813
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89443148"
---
# <a name="how-to-catalog-big-data-in-azure-data-catalog"></a>Como catalogar big data no Catálogo de Dados Azure

## <a name="introduction"></a>Introdução

**O Microsoft Azure Data Catalog** é um serviço de cloud totalmente gerido que serve como um sistema de registo e sistema de descoberta para fontes de dados empresariais. Trata-se de ajudar as pessoas a descobrir, compreender e usar fontes de dados, e ajudar as organizações a obter mais valor a partir das suas fontes de dados existentes, incluindo big data.

**O Azure Data Catalog** suporta o registo de bolhas e diretórios de armazenamento Azure, bem como ficheiros e diretórios Hadoop HDFS. A natureza semi-estruturada destas fontes de dados proporciona uma grande flexibilidade. No entanto, para obter o maior valor ao registar os mesmos no **Azure Data Catalog,** os utilizadores devem considerar como as fontes de dados são organizadas.

## <a name="directories-as-logical-data-sets"></a>Diretórios como conjuntos de dados lógicos

Um padrão comum para organizar grandes fontes de dados é tratar os diretórios como conjuntos de dados lógicos. Os diretórios de alto nível são usados para definir um conjunto de dados, enquanto as sub-dobradeiras definem divisórias, e os ficheiros que contêm armazenam os dados em si.

Um exemplo deste padrão pode ser:

```text
    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...
```

Neste exemplo, vehicle_maintenance_events e location_tracking_events representam conjuntos de dados lógicos. Cada uma destas pastas contém ficheiros de dados que são organizados por ano e mês em sub-dobradutos. Cada uma destas pastas pode potencialmente conter centenas ou milhares de ficheiros.

Neste padrão, registar ficheiros individuais com **catálogo de dados Azure** provavelmente não faz sentido. Em vez disso, registe os diretórios que representam os conjuntos de dados que sejam significativos para os utilizadores que trabalham com os dados.

## <a name="reference-data-files"></a>Ficheiros de dados de referência

Um padrão complementar é armazenar conjuntos de dados de referência como ficheiros individuais. Estes conjuntos de dados podem ser considerados como o lado 'pequeno' dos big data, e são muitas vezes semelhantes às dimensões de um modelo de dados analíticos. Os ficheiros de dados de referência contêm registos que são utilizados para fornecer contexto para a maior parte dos ficheiros de dados armazenados noutros locais da grande loja de dados.

Um exemplo deste padrão pode ser:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

Quando um analista ou cientista de dados está a trabalhar com os dados contidos nas estruturas de diretório maiores, os dados nestes ficheiros de referência podem ser utilizados para fornecer informações mais detalhadas para entidades que são referidas apenas pelo nome ou ID no conjunto de dados maior.

Neste padrão, faz sentido registar os ficheiros individuais de dados de referência com **o Catálogo de Dados Azure.** Cada ficheiro representa um conjunto de dados, e cada um pode ser anotado e descoberto individualmente.

## <a name="alternate-patterns"></a>Padrões alternativos

Os padrões descritos na secção anterior são apenas duas formas possíveis de organizar uma grande loja de dados, mas cada implementação é diferente. Independentemente da forma como as suas fontes de dados são estruturadas, ao registar grandes fontes de dados com o **Azure Data Catalog,** foque-se em registar os ficheiros e diretórios que representam os conjuntos de dados que são de valor para outros dentro da sua organização. Registar todos os ficheiros e diretórios pode atrapalhar o catálogo, dificultando a utilizadores a encontrarem o que precisam.

## <a name="summary"></a>Resumo

Registar fontes de dados com **o Azure Data Catalog** torna-os mais fáceis de descobrir e compreender. Ao registar e anotar os grandes ficheiros de dados e diretórios que representam conjuntos de dados lógicos, pode ajudar os utilizadores a encontrar e utilizar as grandes fontes de dados de que necessitam.
