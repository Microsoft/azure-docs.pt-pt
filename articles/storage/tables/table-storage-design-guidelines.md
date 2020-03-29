---
title: Diretrizes para design de mesa de armazenamento Azure [ Microsoft Docs
description: Desenhe o seu serviço de mesa Azure para apoiar as operações de leitura de forma eficiente.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: d056d29469ad9a60fceeee307aca3c0e1319283c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61269852"
---
# <a name="guidelines-for-table-design"></a>Orientações do design da tabela

Desenhar tabelas para utilização com o serviço de mesa de armazenamento Azure é muito diferente das considerações de design para uma base de dados relacional. Este artigo descreve as diretrizes para conceber a sua solução de serviço table para ser lida eficiente mente e escrever eficientemente.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Desenhe a sua solução de serviço de mesa para ser eficiente em leitura

* ***Design para consulta em aplicações pesadas de leitura.*** Quando estiver a desenhar as suas tabelas, pense nas consultas (especialmente as sensíveis à latência) que executará antes de pensar em como irá atualizar as suas entidades. Isto normalmente resulta numa solução eficiente e executante.  
* ***Especifique tanto partitionKey como RowKey nas suas consultas.*** Consultas de *pontos* como estas são as consultas de serviço de mesa mais eficientes.  
* ***Considere armazenar cópias duplicadas de entidades.*** O armazenamento de mesa é barato, por isso considere armazenar a mesma entidade várias vezes (com teclas diferentes) para permitir consultas mais eficientes.  
* ***Considere desnormalizar os seus dados.*** O armazenamento de mesa é barato por isso considere desnormalizar os seus dados. Por exemplo, armazenar entidades sumárias para que as consultas de dados agregados só precisem de aceder a uma única entidade.  
* ***Utilize valores-chave compostos.*** As únicas chaves que tem são **PartitionKey** e **RowKey**. Por exemplo, utilize valores-chave compostos para permitir caminhos de acesso alternativos às entidades.  
* ***Use a projeção de consulta.*** Pode reduzir a quantidade de dados que transfere através da rede utilizando consultas que selecionam apenas os campos de que necessita.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Desenhe a sua solução de serviço de mesa para ser eficiente em termos de escrita  

* ***Não crie divisórias quentes.*** Escolha as chaves que lhe permitem espalhar os seus pedidos por várias divisórias a qualquer momento.  
* ***Evite picos no trânsito.*** Suavize o tráfego durante um período de tempo razoável e evite picos de tráfego.
* ***Não crie necessariamente uma tabela separada para cada tipo de entidade.*** Quando você requer transações atómicas através de tipos de entidades, você pode armazenar estes vários tipos de entidades na mesma tabela na mesma tabela.
* ***Considere o máximo de entrada que deve conseguir.*** Deve estar ciente dos alvos de escalabilidade do serviço Mesa e garantir que o seu design não fará com que exceda-os.  

Ao ler este guia, verá exemplos que põem em prática todos estes princípios. 

## <a name="next-steps"></a>Passos seguintes

- [Padrões de design da tabela](table-storage-design-patterns.md)
- [Design das consultas](table-storage-design-for-query.md)
- [Encriptar dados de tabela](table-storage-design-encrypt-data.md)
- [Design da modificação de dados](table-storage-design-for-modification.md)
