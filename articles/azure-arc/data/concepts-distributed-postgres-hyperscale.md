---
title: Conceitos para distribuir dados e escalonamento com o grupo de servidores de hiperescala postgreSQL habilitado para o Arquegre
titleSuffix: Azure Arc enabled data services
description: Conceitos para distribuir dados com o grupo de servidores de hiperescala postgreSQL ativados
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: c01da4aed9e27296ea7b570420bb190b16749848
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939781"
---
# <a name="concepts-for-distributing-data-with-arc-enabled-postgresql-hyperscale-server-group"></a>Conceitos para distribuir dados com o grupo de servidores de hiperescala postgreSQL ativados

Este artigo explica conceitos-chave que são importantes para beneficiar mais com a Hiperescala PósgreSQL ativada pela Azure Arc.
Os artigos ligados abaixo apontam para os conceitos explicados para a Base de Dados Azure para a Hiperescala Pós-SQL (Citus). É a mesma tecnologia que a Azure Arc habilitada a PósgreSQL Hyperscale para que os mesmos conceitos e perspetivas se apliquem.

**Qual é a diferença entre eles?**
- _Hyperscale (Citus) da Base de Dados do Azure para PostgreSQL_

Este é o fator de forma de hiperescala do motor de base de dados Postgres disponível como base de dados como um serviço em Azure (PaaS). É alimentado pela extensão Citus que permite a experiência Hyperscale. Neste fator de formulário, o serviço funciona nos datacenters da Microsoft e é operado pela Microsoft.

- _Azure Arc habilitado a hiperescala pósgresql_

Este é o fator de forma de hiperescala do motor de base de dados Postgres oferecido disponível com o Azure Arc habilitado para o Serviço de Dados. Neste fator de forma, os nossos clientes fornecem a infraestrutura que acolhe os sistemas e os opera.

Os conceitos-chave em torno da hiperescala pós-altura ativada por Azure Arc são resumidos abaixo:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>Nós e tabelas
É importante saber sobre os seguintes conceitos para beneficiar mais da Azure Arc habilitada a Postgres Hyperscale:
- Os gádes especializados em Azure Arc habilitados pós-escala PósgreSQL: coordenador e trabalhadores
- Tipos de tabelas: mesas distribuídas, tabelas de referência e mesas locais
- Fragmentos

Consulte mais informações [em Nodes e tabelas na Base de Dados Azure para PostgreSQL – Hyperscale (Citus)](../../postgresql/concepts-hyperscale-nodes.md). 

## <a name="determine-the-application-type"></a>Determinar o tipo de aplicação
Identificar claramente o tipo de aplicação que está a construir é importante. Porquê? Uma vez que executar consultas eficientes num grupo de servidores de hiperescala PostgreSQL ativado requer que as tabelas sejam devidamente distribuídas pelos servidores. A distribuição recomendada varia em dose pelo tipo de aplicação e pelos seus padrões de consulta. Existem, em geral, dois tipos de aplicações que funcionam bem em Azure Arc habilitado a Pósgres Hyperscale:
- Aplicações multi-arrendatários
- Aplicações Real-Time

O primeiro passo na modelação de dados é identificar qual deles se assemelha mais à sua aplicação.

Consulte os detalhes no [tipo de aplicação Determinante](../../postgresql/concepts-hyperscale-app-type.md).


## <a name="choose-a-distribution-column"></a>Escolher uma coluna de distribuição
Porquê escolher uma coluna distribuída?

Esta é uma das decisões de modelo mais importantes que vais tomar. O Azure Arc permitiu que as filas de hiperescala PostgreSQL em fragmentos com base no valor da coluna de distribuição das linhas. A escolha correta agruba dados relacionados com os mesmos nós físicos, o que torna as consultas rápidas e adiciona suporte para todas as funcionalidades SQL. Uma escolha incorreta faz com que o sistema seja executado lentamente e não suporta todas as funcionalidades SQL em nós. Este artigo dá dicas de coluna de distribuição para os dois cenários de hiperescala mais comuns.

Consulte os detalhes nas [colunas de distribuição Escolha.](../../postgresql/concepts-hyperscale-choose-distribution-column.md)


## <a name="table-colocation"></a>Colocalização de tabela

A colocação tem a ver com armazenar informações relacionadas nos mesmos nós. As consultas podem ser rápidas quando todos os dados necessários estão disponíveis sem qualquer tráfego de rede. A colocação de dados relacionados em diferentes nós permite que as consultas corram eficientemente em paralelo em cada nó.

Consulte os detalhes na [tabela de colocação](../../postgresql/concepts-hyperscale-colocation.md).


## <a name="next-steps"></a>Passos seguintes
- [Leia sobre a criação de Azure Arc ativado em hiperescala pósgreSQL](create-postgresql-hyperscale-server-group.md)
- [Leia sobre a escala de Azure Arc ativado por grupos de servidores de hiperescala postgreSQL criados no seu Controlador de Dados do Arco](scale-out-postgresql-hyperscale-server-group.md)
- [Ler sobre Azure Arc habilitado serviços de dados](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [Leia sobre Azure Arc](https://aka.ms/azurearc)

