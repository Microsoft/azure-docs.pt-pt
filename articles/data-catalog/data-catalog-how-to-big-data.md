---
title: Como trabalhar com origens de dados de 'grandes volumes de dados' no catálogo de dados do Azure
description: Artigo que mostra como realce padrões para o catálogo de dados do Azure com origens de dados de "grandes volumes de dados", incluindo o armazenamento de Blobs do Azure, Azure Data Lake e Hadoop HDFS.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 626d1568-0780-4726-bad1-9c5000c6b31a
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: b6b419d575e2164fc683b8e6b5020572db74d1b4
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998673"
---
# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Como trabalhar com origens de macrodados no catálogo de dados do Azure
## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço cloud totalmente gerido que funciona como um sistema de registo e sistema de deteção de origens de dados empresariais. É tudo sobre ajudando pessoas detetar, compreender e utilizar origens de dados e as organizações de ajuda para obter mais valor das suas origens de dados existentes, incluindo grandes quantidades de dados.

**O catálogo de dados do Azure** suporta o registo de blobs de armazenamento do blogue do Azure e de diretórios, bem como de ficheiros Hadoop HDFS e de diretórios. A natureza semiestruturada destas origens de dados fornece grande flexibilidade. No entanto, para obter o máximo valor dos como registá-los com **catálogo de dados do Azure**, os utilizadores tem de considerar como as origens de dados são organizadas.

## <a name="directories-as-logical-data-sets"></a>Diretórios como conjuntos de dados lógicos
É um padrão comum para organizar as origens de macrodados tratar diretórios como conjuntos de dados lógicos. Diretórios de nível superior são utilizados para definir um conjunto de dados, enquanto as subpastas definem partições e os ficheiros que contêm armazenam os dados propriamente ditos.

Um exemplo desse padrão pode ser:

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

Neste exemplo, vehicle_maintenance_events e location_tracking_events representam conjuntos de dados lógicos. Cada uma dessas pastas contém arquivos de dados que são organizados por ano e mês em subpastas. Cada uma dessas pastas, potencialmente, poderia conter centenas ou milhares de arquivos.

Neste padrão, arquivos individuais com a registar **catálogo de dados do Azure** provavelmente não faz sentido. Em vez disso, registe os diretórios que representam os conjuntos de dados ser significativo para os usuários a trabalhar com os dados.

## <a name="reference-data-files"></a>Ficheiros de dados de referência
É um padrão complementar armazenar conjuntos de dados de referência como arquivos individuais. Estes conjuntos de dados pode ser considerados como o lado "small" de grandes volumes de dados e, muitas vezes, são semelhantes às dimensões num modelo de dados analíticos. Ficheiros de dados de referência contém registos que são utilizados para fornecer contexto para a maior parte dos ficheiros de dados armazenados em outro lugar no arquivo de macrodados.

Um exemplo desse padrão pode ser:

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

Quando uma cientista de dados ou analista está a trabalhar com os dados contidos nas estruturas de diretório maiores, os dados nesses arquivos de referência podem ser utilizados para fornecer informações mais detalhadas para entidades que são referenciadas apenas por nome ou ID no conjunto de dados maior.

Neste padrão, faz sentido para registrar os arquivos de dados de referência individuais com **catálogo de dados do Azure**. Cada ficheiro representa um conjunto de dados, e cada um deles pode ser anotado e detetado individualmente.

## <a name="alternate-patterns"></a>Padrões alternativos
Os padrões descritos na secção anterior são apenas duas maneiras possíveis, que um arquivo de macrodados pode ser organizado, mas cada implementação é diferente. Independentemente de como são estruturadas as origens de dados, ao registar as origens de macrodados com **catálogo de dados do Azure**, concentre-se sobre o registo de ficheiros e diretórios que representam os conjuntos de dados do valor para outras pessoas dentro da sua organização. Registar todos os ficheiros e diretórios pode desorganizam o catálogo, tornando mais difícil do que os utilizadores encontrem o que precisam.

## <a name="summary"></a>Resumo
Registar origens de dados com **catálogo de dados do Azure** torna mais fácil detetar e compreender. Ao registar e anotar os ficheiros de grandes volumes de dados e diretórios que representam os conjuntos de dados lógicos, pode ajudar os usuários a encontrar e utilizar as origens de macrodados que têm.
