---
title: Determinar tipo de aplicação - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Identifique a sua aplicação para modelação eficaz de dados distribuídos
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 92333857177d33307d6997bfcbdf79787d3ab127
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90895951"
---
# <a name="determining-application-type"></a>Tipo de aplicação determinante

Executar consultas eficientes num grupo de servidores Hyperscale (Citus) requer que as tabelas sejam devidamente distribuídas pelos servidores. A distribuição recomendada varia em dose pelo tipo de aplicação e pelos seus padrões de consulta.

Existem, em geral, dois tipos de aplicações que funcionam bem em Hyperscale (Citus). O primeiro passo na modelação de dados é identificar qual deles se assemelha mais à sua aplicação.

## <a name="at-a-glance"></a>At a Glance

| Aplicações multi-arrendatários                                 | Aplicações em Tempo Real                                |
|-----------------------------------------------------------|-------------------------------------------------------|
| Por vezes, dezenas ou centenas de tabelas no esquema          | Pequeno número de tabelas                                |
| Consultas relativas a um inquilino (empresa/loja) de cada vez | Consultas de análise relativamente simples com agregações |
| Cargas de trabalho OLTP para servir clientes Web                    | Alto volume de ingestão de dados maioritariamente imutáveis           |
| Cargas de trabalho OLAP que apresentam consultas analíticas por inquilino   | Normalmente, centralizadas numa grande tabela de eventos            |

## <a name="examples-and-characteristics"></a>Exemplos e Características

**Aplicação multi-arrendatário**

> Estas são aplicações tipicamente SaaS que servem outras empresas, contas ou organizações. A maioria das aplicações saaS são inerentemente relacionais. Têm uma dimensão natural para distribuir dados por nós: apenas fragmento por \_ id inquilino.
>
> A Hiperescala (Citus) permite-lhe escalar a sua base de dados para milhões de inquilinos sem ter de re-arquitectar a sua candidatura. Você pode manter a semântica relacional que você precisa, como juntas, restrições de chaves estrangeiras, transações, ACID e consistência.
>
> -   **Exemplos**: Websites que acolhem fachadas de lojas para outras empresas, como uma solução de marketing digital ou uma ferramenta de automação de vendas.
> -   **Características**: Consultas relativas a um único inquilino em vez de juntar informações entre inquilinos. Isto inclui cargas de trabalho OLTP para servir clientes web, e cargas de trabalho OLAP que servem consultas analíticas por inquilino. Ter dezenas ou centenas de tabelas no seu esquema de base de dados é também um indicador para o modelo de dados multi-inquilinos.
>
> Escalar uma aplicação multi-inquilino com Hyperscale (Citus) também requer alterações mínimas no código da aplicação. Temos apoio para quadros populares como Ruby on Rails e Django.

**Análise em Tempo Real**

> Aplicações que precisam de paralelismo maciço, coordenando centenas de núcleos para resultados rápidos para consultas numéricas, estatísticas ou contando.  Ao esfarrapar e paralelizar consultas SQL através de múltiplos nós, a Hyperscale (Citus) permite realizar consultas em tempo real através de milhares de milhões de discos em menos de um segundo.
>
> As tabelas em modelos de dados de análise em tempo real são normalmente distribuídas por colunas como \_ id de utilizador, id de anfitrião \_ ou id do \_ dispositivo.
>
> -   **Exemplos**: Painéis de análise virados para o cliente que requerem tempos de resposta de segundo.
> -   **Características**: Poucas tabelas, muitas vezes centradas em torno de uma grande tabela de eventos de dispositivos, locais ou utilizadores e que requerem um elevado volume de ingerir dados maioritariamente imutáveis. Consultas de análise relativamente simples (mas computacionalmente intensivas) que envolvem várias agregações e grupos BYs.

Se a sua situação se assemelha a qualquer dos casos acima, então o próximo passo é decidir como fragmentos os seus dados no grupo de servidor. A escolha das colunas de distribuição do administrador de base \' de dados tem de corresponder aos padrões de acesso das consultas típicas para garantir o desempenho.

## <a name="next-steps"></a>Passos seguintes

* [Escolha uma coluna de distribuição](concepts-hyperscale-choose-distribution-column.md) para tabelas na sua aplicação para distribuir dados de forma eficiente
