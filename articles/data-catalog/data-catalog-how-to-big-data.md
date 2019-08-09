---
title: Como trabalhar com fontes de dados ' Big Data ' no catálogo de dados do Azure
description: Artigo de instruções destacando padrões para usar o catálogo de dados do Azure com fontes de dados ' Big Data ', incluindo o armazenamento de BLOBs do Azure, Azure Data Lake e HDFS do Hadoop.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 00b9d6ab6ca8d9b4154e0fba491081597dc08402
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882527"
---
# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Como trabalhar com fontes de Big Data no catálogo de dados do Azure

## <a name="introduction"></a>Introdução

**Microsoft Azure catálogo de dados** é um serviço de nuvem totalmente gerenciado que serve como um sistema de registro e sistema de descoberta para fontes de dados empresariais. Trata-se de ajudar as pessoas a descobrir, entender e usar fontes de dados e ajudar as organizações a obter mais valor de suas fontes de dados existentes, incluindo Big Data.

O **Catálogo de dados do Azure** dá suporte ao registro de BLOBs e diretórios do armazenamento de blog do Azure, bem como diretórios e arquivos HDFS do Hadoop. A natureza semiestruturada dessas fontes de dados fornece grande flexibilidade. No entanto, para obter o máximo valor de registrá-los com o **Catálogo de dados do Azure**, os usuários devem considerar como as fontes de dados são organizadas.

## <a name="directories-as-logical-data-sets"></a>Diretórios como conjuntos de dados lógicos

Um padrão comum para organizar Big Data fontes é tratar diretórios como conjuntos de dados lógicos. Os diretórios de nível superior são usados para definir um conjunto de dados, enquanto as subpastas definem partições e os arquivos que eles contêm armazenam os dados em si.

Um exemplo desse padrão pode ser:

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

Neste exemplo, vehicle_maintenance_events e location_tracking_events representam conjuntos de dados lógicos. Cada uma dessas pastas contém arquivos de dados organizados por ano e mês em subpastas. Cada uma dessas pastas pode conter centenas ou milhares de arquivos.

Nesse padrão, o registro de arquivos individuais com o **Catálogo de dados do Azure** provavelmente não faz sentido. Em vez disso, registre os diretórios que representam os conjuntos de dados que são significativos para os usuários que trabalham com os dados.

## <a name="reference-data-files"></a>Arquivos de dados de referência

Um padrão complementar é armazenar conjuntos de dados de referência como arquivos individuais. Esses conjuntos de dados podem ser considerados como o lado "pequeno" de Big Data e geralmente são semelhantes às dimensões em um modelo de dados analíticos. Os arquivos de dados de referência contêm registros que são usados para fornecer contexto para a massa dos arquivos de dados armazenados em outro lugar no repositório de Big Data.

Um exemplo desse padrão pode ser:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

Quando um analista ou cientista de dados está trabalhando com os dados contidos nas estruturas de diretório maiores, os dados nesses arquivos de referência podem ser usados para fornecer informações mais detalhadas para entidades que são referenciadas apenas por nome ou ID no conjunto de dados maior.

Nesse padrão, faz sentido registrar os arquivos de dados de referência individuais com o **Catálogo de dados do Azure**. Cada arquivo representa um conjunto de dados, e cada um pode ser anotado e descoberto individualmente.

## <a name="alternate-patterns"></a>Padrões alternativos

Os padrões descritos na seção anterior são apenas duas maneiras possíveis pelas quais um Big Data repositório pode ser organizado, mas cada implementação é diferente. Independentemente de como suas fontes de dados são estruturadas, ao registrar Big Data fontes com o **Catálogo de dados do Azure**, concentre-se em registrar os arquivos e diretórios que representam os conjuntos de dados que são de valor para outros em sua organização. Registrar todos os arquivos e diretórios pode obstruir o catálogo, tornando mais difícil para os usuários encontrar o que precisam.

## <a name="summary"></a>Resumo

O registro de fontes de dados com o **Catálogo de dados do Azure** torna mais fácil descobrir e entender. Ao registrar e anotar os arquivos de Big Data e os diretórios que representam conjuntos de dados lógicos, você pode ajudar os usuários a localizar e usar as fontes de Big Data de que precisam.
