---
title: Diretrizes para design de mesa de armazenamento Azure | Microsoft Docs
description: Compreenda as diretrizes para conceber o seu serviço de mesa de armazenamento Azure para suportar operações de leitura e escrita de forma eficiente.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: f84707e454a8b1f5d5947478fe65108a142a9757
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88236323"
---
# <a name="guidelines-for-table-design"></a>Orientações do design da tabela

A conceção de tabelas para utilização com o serviço de mesa de armazenamento Azure é muito diferente das considerações de design para uma base de dados relacional. Este artigo descreve orientações para a conceção da sua solução de serviço de mesa para ser lida de forma eficiente e escrever eficientemente.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Desenhe a sua solução de serviço de mesa para ser eficiente em leitura

* ***Design para consulta em aplicações pesadas de leitura.*** Ao desenhar as suas tabelas, pense nas consultas (especialmente nas sensíveis à latência) que irá executar antes de pensar em como atualizar as suas entidades. Isto normalmente resulta numa solução eficiente e performante.  
* ***Especifique o PartitionKey e o RowKey nas suas consultas.** _ _Point consultas* como estas são as consultas de serviço de mesa mais eficientes.  
* ***Considere armazenar cópias duplicadas de entidades.*** O armazenamento de mesa é barato, por isso considere armazenar a mesma entidade várias vezes (com teclas diferentes) para permitir consultas mais eficientes.  
* ***Considere desnormalizar os seus dados.*** O armazenamento de mesa é barato, por isso considere desnormalizar os seus dados. Por exemplo, armazenar entidades sumárias para que as consultas por dados agregados apenas precisem de aceder a uma única entidade.  
* ***Utilize valores-chave compostos.** _ As únicas chaves que tem são _ *PartitionKey** e **RowKey**. Por exemplo, utilize valores-chave compostos para permitir caminhos de acesso com chave suplente às entidades.  
* ***Use a projeção de consulta.*** Pode reduzir a quantidade de dados que transfere através da rede utilizando consultas que selecionam apenas os campos de que necessita.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Desenhe a sua solução de serviço de tabela para ser eficiente em termos de escrita  

* ***Não crie divisórias quentes.*** Escolha as teclas que lhe permitem espalhar os seus pedidos através de várias divisórias a qualquer momento.  
* ***Evite picos no trânsito.*** Suavize o tráfego durante um período de tempo razoável e evite picos de tráfego.
* ***Não crie necessariamente uma tabela separada para cada tipo de entidade.*** Quando necessitar de transações atómicas entre tipos de entidades, pode armazenar estes múltiplos tipos de entidades na mesma divisória na mesma tabela.
* ***Considere a produção máxima que deve alcançar.*** Deve estar ciente dos alvos de escalabilidade do serviço Table e certifique-se de que o seu design não os fará ultrapassar.  

Ao ler este guia, verá exemplos que colocam todos estes princípios em prática. 

## <a name="next-steps"></a>Passos seguintes

- [Padrões de design da tabela](table-storage-design-patterns.md)
- [Design das consultas](table-storage-design-for-query.md)
- [Criptografe dados de tabela](table-storage-design-encrypt-data.md)
- [Design da modificação de dados](table-storage-design-for-modification.md)
