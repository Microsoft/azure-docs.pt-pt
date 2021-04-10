---
title: Limites e limitações - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Limites atuais para grupos de servidores de Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cfd57c60c4bfd60976eb28822c696412cabda212
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023997"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Base de Dados Azure para Limites e limitações pós-escala (Citus)

A secção seguinte descreve a capacidade e os limites funcionais no serviço Hyperscale (Citus).

## <a name="maximum-connections"></a>Número máximo de ligações

Todas as ligações PostgreSQL (mesmo as ociosas) utilizam pelo menos 10 MB de memória, por isso é importante limitar as ligações simultâneas. Aqui estão os limites que escolhemos para manter os nós saudáveis:

* Nó coordenador
   * Ligações máximas: 300
   * Ligações máximas do utilizador: 297
* Nó do trabalhador
   * Ligações máximas: 600
   * Ligações máximas do utilizador: 597

> [!NOTE]
> Num grupo de servidores com [funcionalidades de pré-visualização](hyperscale-preview-features.md) ativadas, os limites de ligação ao coordenador são ligeiramente diferentes:
>
> * Ligações max do nó coordenador
>    * 300 para 0-3 vCores
>    * 500 para 4-15 vCores
>    * 1000 para 16+ vCores

As tentativas de ligação para além destes limites falharão com um erro. O sistema reserva três ligações para os nós de monitorização, razão pela qual existem menos três ligações disponíveis para consultas de utilizador do que as ligações totais.

### <a name="connection-pooling"></a>Agrupamento de conexão

Estabelecer novas ligações leva tempo. Isso funciona contra a maioria das aplicações, que solicitam muitas ligações de curta duração. Recomendamos a utilização de um pooler de ligação, tanto para reduzir as transações ociosas como para reutilizar as ligações existentes. Para saber mais, visite o nosso [blog post.](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)

Você pode executar o seu próprio pooler de conexão, ou usar PgBouncer gerido por Azure.

#### <a name="managed-pgbouncer-preview"></a>Gerido PgBouncer (pré-visualização)

> [!IMPORTANT]
> O pooler de conexão PgBouncer gerido em Hyperscale (Citus) está atualmente em pré-visualização. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
>
> Pode ver uma lista completa de outras novidades em [funcionalidades de pré-visualização para Hyperscale (Citus)](hyperscale-preview-features.md).

Os poolers de conexão, como o PgBouncer, permitem que mais clientes se conectem ao nó coordenador de uma só vez. As aplicações ligam-se ao pooler, e o pooler transmite comandos para a base de dados de destino.

Quando os clientes se conectam através do PgBouncer, o número de ligações que podem ser executados ativamente na base de dados não muda. Em vez disso, o PgBouncer faz filas de ligações em excesso e executa-as quando a base de dados está pronta.

A Hyperscale (Citus) está agora a oferecer uma instância gerida de PgBouncer para grupos de servidores (em pré-visualização). Suporta até 2.000 ligações simultâneas de clientes.
Para ligar através do PgBouncer, siga estes passos:

1. Aceda à página **de cordas De Ligação** para o seu grupo de servidor no portal Azure.
2. Ativar as **cordas de ligação pgBouncer da** caixa de verificação . (As cadeias de ligação listadas mudarão.)
3. Atualize as aplicações do cliente para se conectar com a nova cadeia.

## <a name="storage-scaling"></a>Dimensionamento de armazenamento

O armazenamento em coordenadores e nós de trabalhadores pode ser aumentado (aumentado) mas não pode ser reduzido (diminuído).

## <a name="storage-size"></a>Tamanho do armazenamento

Até 2 TiB de armazenamento é suportado em coordenadores e nós de trabalhadores. Consulte as opções de armazenamento disponíveis e o cálculo do IOPS [acima](concepts-hyperscale-configuration-options.md#compute-and-storage) para tamanhos de nó e cluster.

## <a name="database-creation"></a>Criação de base de dados

O portal Azure fornece credenciais para ligar exatamente a uma base de dados por grupo de servidores Hyperscale (Citus), a `citus` base de dados. Atualmente, não é permitida a criação de outra base de dados e o comando CREATE DATABASE falhará com um erro.

## <a name="columnar-storage"></a>Armazenamento colunar

A hiperescala (Citus) tem atualmente estas limitações com [tabelas colunaares:](concepts-hyperscale-columnar.md)

* A compressão está no disco, não na memória.
* Apenas por anexação (sem suporte ATUALIZAÇÃO/DELETE)
* Nenhuma recuperação de espaço (por exemplo, transações de roll-back ainda podem consumir espaço em disco)
* Sem suporte de índice, análises de índices ou análises de índices de bitmap
* Sem tidscans
* Sem amostras
* Sem suporte TOS (grandes valores suportados em linha)
* Sem suporte para declarações ON CONFLICT (exceto NÃO FAZER NADA ações sem alvo especificado).
* Sem suporte para bloqueios de tuple (SELECT ... PARA PARTILHAR, SELECIONE ... PARA ATUALIZAÇÃO)
* Sem suporte para um nível de isolamento serializável
* Suporte para versões de servidor PostgreSQL apenas 12+
* Sem suporte para chaves estrangeiras, restrições únicas ou restrições de exclusão
* Sem apoio à descodante lógica
* Sem suporte para varreduras paralelas intra-nólais
* Sem apoio para AFTER ... Para cada LINHA dispara
* Sem tabelas colunar UNLOGGED
* Sem tabelas de colunas TEMPORÁRIAs

## <a name="next-steps"></a>Passos seguintes

Saiba como criar um grupo de [servidores Hyperscale (Citus) no portal](quickstart-create-hyperscale-portal.md).
