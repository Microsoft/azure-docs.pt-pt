---
title: Como catalogar big data no Catálogo de Dados Do Azure
description: Como fazer artigo sintetizar padrões para usar o Catálogo de Dados Azure com fontes de dados de 'big data', incluindo armazenamento Azure Blob, Lago de Dados Azure e Hadoop HDFS.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 88dc85003fa2a3e41d8a31055ff8ba9b0fcc7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71300577"
---
# <a name="how-to-catalog-big-data-in-azure-data-catalog"></a>Como catalogar big data no Catálogo de Dados Do Azure

## <a name="introduction"></a>Introdução

O **Microsoft Azure Data Catalog** é um serviço de cloud totalmente gerido que serve como um sistema de registo e sistema de descoberta para fontes de dados da empresa. Trata-se de ajudar as pessoas a descobrir, compreender e usar fontes de dados, e ajudar as organizações a obter mais valor a partir das suas fontes de dados existentes, incluindo big data.

**O Azure Data Catalog** suporta o registo de blobs e diretórios de armazenamento de blogs do Azure, bem como ficheiros e diretórios Hadoop HDFS. A natureza semi-estruturada destas fontes de dados proporciona uma grande flexibilidade. No entanto, para obter o maior valor ao registá-los com o Catálogo de **Dados Azure,** os utilizadores devem considerar como as fontes de dados são organizadas.

## <a name="directories-as-logical-data-sets"></a>Diretórios como conjuntos de dados lógicos

Um padrão comum para a organização de grandes fontes de dados é tratar os diretórios como conjuntos de dados lógicos. Os diretórios de alto nível são usados para definir um conjunto de dados, enquanto as subpastas definem divisórias, e os ficheiros que contêm armazenam os próprios dados.

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

Neste exemplo, vehicle_maintenance_events e location_tracking_events representam conjuntos de dados lógicos. Cada uma destas pastas contém ficheiros de dados organizados por ano e mês em subpastas. Cada uma destas pastas pode potencialmente conter centenas ou milhares de ficheiros.

Neste padrão, registar ficheiros individuais com o Catálogo de **Dados Azure** provavelmente não faz sentido. Em vez disso, registe os diretórios que representam os conjuntos de dados que sejam significativos para os utilizadores que trabalham com os dados.

## <a name="reference-data-files"></a>Ficheiros de dados de referência

Um padrão complementar é armazenar conjuntos de dados de referência como ficheiros individuais. Estes conjuntos de dados podem ser considerados como o lado 'pequeno' do big data, e são muitas vezes semelhantes às dimensões de um modelo de dados analíticos. Os ficheiros de dados de referência contêm registos que são usados para fornecer contexto para a maior parte dos ficheiros de dados armazenados em outros lugares da loja de big data.

Um exemplo deste padrão pode ser:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

Quando um analista ou cientista de dados está a trabalhar com os dados contidos nas estruturas de diretórios maiores, os dados nestes ficheiros de referência podem ser usados para fornecer informações mais detalhadas para entidades que são referidas apenas pelo nome ou ID no conjunto de dados maior.

Neste padrão, faz sentido registar os ficheiros de dados de referência individuais com o Catálogo de **Dados Azure**. Cada ficheiro representa um conjunto de dados, e cada um pode ser anotado e descoberto individualmente.

## <a name="alternate-patterns"></a>Padrões alternativos

Os padrões descritos na secção anterior são apenas duas maneiras possíveis de uma grande loja de dados ser organizada, mas cada implementação é diferente. Independentemente da forma como as suas fontes de dados são estruturadas, ao registar grandes fontes de dados com o Catálogo de **Dados Do Azure,** foque-se no registo dos ficheiros e diretórios que representam os conjuntos de dados que são de valor para outros dentro da sua organização. Registar todos os ficheiros e diretórios pode desabar o catálogo, dificultando aos utilizadores encontrar o que precisam.

## <a name="summary"></a>Resumo

Registar fontes de dados com o Catálogo de **Dados Azure** torna-as mais fáceis de descobrir e compreender. Ao registar e anotar os ficheiros e diretórios de big data que representam conjuntos de dados lógicos, pode ajudar os utilizadores a encontrar e utilizar as grandes fontes de dados de que necessitam.
