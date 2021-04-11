---
title: Ler réplicas - Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
description: Este artigo descreve a funcionalidade de réplica de leitura na Base de Dados Azure para PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cf8c64bf3858f0d3b7d633b94bc7302fbe563f87
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024000"
---
# <a name="read-replicas-in-azure-database-for-postgresql---hyperscale-citus"></a>Leia réplicas na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

> [!IMPORTANT]
> As réplicas de leitura em Hyperscale (Citus) estão atualmente em pré-visualização. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
>
> Pode ver uma lista completa de outras novidades em [funcionalidades de pré-visualização para Hyperscale (Citus)](hyperscale-preview-features.md).

A funcionalidade de réplica de leitura permite-lhe replicar dados de um grupo de servidor de Hiperescala (Citus) para um grupo de servidor apenas de leitura. As réplicas são **atualizadas assíncroneamente** com a tecnologia de replicação física PostgreSQL. Pode replicar-se do servidor primário para um número ilimitado de réplicas.

As réplicas são novos grupos de servidores que geres semelhantes aos grupos regulares de servidores Hyperscale (Citus). Para cada réplica de leitura, você é cobrado para o cálculo provisionado em vCores e armazenamento em GB/mês.

Saiba como [criar e gerir réplicas.](howto-hyperscale-read-replicas-portal.md)

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura

A funcionalidade de réplica de leitura ajuda a melhorar o desempenho e a escala das cargas de trabalho intensivas de leitura. As cargas de trabalho de leitura podem ser isoladas às réplicas, enquanto as cargas de trabalho de escrita podem ser direcionadas para as primárias.

Um cenário comum é fazer com que as cargas de trabalho bi e analíticas utilizem a réplica de leitura como fonte de dados para reportar.

Como as réplicas são apenas de leitura, não reduzem diretamente os encargos de capacidade de escrita nas primárias.

### <a name="considerations"></a>Considerações

A funcionalidade destina-se a cenários onde o lag de replicação é aceitável, e destina-se a descarregar consultas. Não se destina a cenários de replicação sincronizados onde se espera que os dados de réplica estejam atualizados. Haverá um atraso mensurável entre as primárias e a réplica. O atraso pode ser de minutos ou mesmo horas dependendo da carga de trabalho e da latência entre a primária e a réplica.  Os dados sobre a réplica eventualmente tornam-se consistentes com os dados sobre o primário. Utilize esta funcionalidade para cargas de trabalho que possam acomodar este atraso. 

## <a name="create-a-replica"></a>Criar uma réplica

Quando inicia o fluxo de trabalho de réplicas, é criado um grupo de servidores de Hiperescala (Citus) em branco. O novo grupo está cheio dos dados que estavam no grupo de servidores primários. O tempo de criação depende da quantidade de dados sobre o primário e o tempo desde o último backup completo semanal. O tempo pode variar entre alguns minutos e várias horas.

A funcionalidade de réplica de leitura utiliza a replicação física postgreSQL, não a replicação lógica. O modo predefinido está a transmitir replicação utilizando ranhuras de replicação.
Quando necessário, o envio de registos é utilizado para o seu catch up.

Saiba como [criar uma réplica de leitura no portal Azure.](howto-hyperscale-read-replicas-portal.md)

## <a name="connect-to-a-replica"></a>Ligar-se a uma réplica

Quando se cria uma réplica, não herda as regras de firewall do grupo de servidor primário. Estas regras devem ser criadas de forma independente para a réplica.

A réplica herda a conta de administração ("citus") do grupo de servidor primário.
Todas as contas de utilizador são replicadas nas réplicas de leitura. Só é possível ligar-se a uma réplica de leitura utilizando as contas de utilizador que estão disponíveis no servidor primário.

Pode ligar-se ao nó coordenador da réplica utilizando o seu nome de anfitrião e uma conta de utilizador válida, como faria num grupo regular de servidores Hyperscale (Citus).
Para um servidor chamado **minha réplica** com o nome de utilizador **de administração citus,** pode ligar-se ao nó coordenador da réplica utilizando psql:

```bash
psql -h c.myreplica.postgres.database.azure.com -U citus@myreplica -d postgres
```

A pedido, introduza a palavra-passe para a conta de utilizador.

## <a name="considerations"></a>Considerações

Esta secção resume considerações sobre a funcionalidade de réplica de leitura.

### <a name="new-replicas"></a>Novas réplicas

Uma réplica de leitura é criada como um novo grupo de servidores Hyperscale (Citus). Um grupo de servidores existente não pode ser transformado numa réplica. Não se pode criar uma réplica de outra réplica lida.

### <a name="replica-configuration"></a>Configuração de réplica

Uma réplica é criada utilizando as mesmas definições de computação, armazenamento e nóiro do trabalhador que o principal. Após a criação de uma réplica, várias definições podem ser alteradas, incluindo o período de armazenamento e retenção de backup. Outras configurações não podem ser alteradas em réplicas, tais como o tamanho do armazenamento e o número de nós dos trabalhadores.

Lembre-se de manter as réplicas fortes o suficiente para manter as mudanças que chegam das primárias. Por exemplo, certifique-se de aumentar a potência de cálculo em réplicas se o aumentar nas primárias.

As regras de firewall e as definições de parâmetros não são herdadas do servidor primário para a réplica quando a réplica é criada ou posteriormente.

### <a name="regions"></a>Regiões

Os grupos de servidores de hiperescala (Citus) suportam apenas a replicação da mesma região.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar e gerir réplicas de leitura no portal Azure.](howto-hyperscale-read-replicas-portal.md)
