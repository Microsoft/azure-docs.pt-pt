---
title: Design de tabela de diretrizes para o armazenamento do Azure | Documentos da Microsoft
description: Projetar o serviço de tabela do Azure para suportar operações de leitura com eficiência.
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 04/23/2018
ms.date: 12/10/2018
ms.author: v-jay
ms.component: tables
ms.openlocfilehash: d056d29469ad9a60fceeee307aca3c0e1319283c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269852"
---
# <a name="guidelines-for-table-design"></a>Orientações do design da tabela

A criação de tabelas para utilização com o serviço de tabela de armazenamento do Azure é muito diferente de considerações de design para uma base de dados relacional. Este artigo descreve as diretrizes para conceber a sua solução de serviço de tabela para ser lido eficiente e escrever eficiente.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Conceber a sua solução de serviço de tabela para ser eficiente de leitura

* ***Design para a consulta em aplicativos de leitura intensiva.*** Durante o design de suas tabelas, pensar sobre as consultas (especialmente a latência aqueles confidenciais), que executará a antes de pensar a respeito de como atualizar as entidades. Isso normalmente resulta numa solução eficiente e de elevado desempenho.  
* ***Especifique PartitionKey e RowKey nas suas consultas.*** *Do ponto de consultas* como estas são as consultas mais eficientes de serviço tabela.  
* ***Considere armazenar cópias duplicadas de entidades.*** Armazenamento de tabelas é barato por isso considere armazenar a mesma entidade várias vezes (com chaves diferentes) para ativar as consultas mais eficientes.  
* ***Considere desnormalizar os dados.*** Armazenamento de tabelas é barato por isso considere desnormalizar os dados. Por exemplo, armazene entidades de resumidas para que as consultas para agregar dados apenas precisam de aceder a uma única entidade.  
* ***Utilize valores de chave compostas.*** As chaves só tiver são **PartitionKey** e **RowKey**. Por exemplo, utilize os valores de chave compostos para ativar caminhos de com chave de acesso alternativo para entidades.  
* ***Utilize a projeção da consulta.*** Pode reduzir a quantidade de dados que transferir através da rede através de consultas que selecionar apenas os campos que precisa.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Conceber a sua solução de serviço de tabela para ser eficiente de escrita  

* ***Não crie partições muito ativas.*** Escolha as chaves que permitem que distribuir os pedidos por várias partições em qualquer ponto no tempo.  
* ***Evite picos no tráfego.*** Suavizar o tráfego de um período de tempo razoável e evitar picos no tráfego.
* ***Não necessariamente criam uma tabela separada para cada tipo de entidade.*** Quando necessitar de transações atómicas em todos os tipos de entidade, pode armazenar esses vários tipos de entidade na mesma partição na mesma tabela.
* ***Considere o débito máximo que deve ser atingida.*** Tem de ter em consideração os destinos de escalabilidade para o serviço de tabela e certifique-se de que o seu design não irá causar que exceda-los.  

Enquanto lê este guia, verá exemplos que colocar todos esses princípios em prática. 

## <a name="next-steps"></a>Passos Seguintes

- [Padrões de design de tabela](table-storage-design-patterns.md)
- [Design para a consulta](table-storage-design-for-query.md)
- [Encriptar dados da tabela](table-storage-design-encrypt-data.md)
- [Design para modificação de dados](table-storage-design-for-modification.md)