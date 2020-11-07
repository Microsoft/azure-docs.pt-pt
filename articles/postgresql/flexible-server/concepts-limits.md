---
title: Limites - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Este artigo descreve limites na Base de Dados Azure para PostgreSQL - Servidor Flexível, como o número de opções de motor de ligação e armazenamento.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d1405bce6761b6702146418296cb7b47bb9124ee
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357186"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>Limites na Base de Dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

As seguintes secções descrevem limites de capacidade e funcionais no serviço de base de dados. Se quiser aprender sobre os níveis de recursos (computação, memória, armazenamento), consulte o artigo [de computação e armazenamento.](concepts-compute-storage.md)

## <a name="maximum-connections"></a>Número máximo de ligações

O número máximo de ligações por nível de preços e vCores são mostrados abaixo. O sistema Azure requer três ligações para monitorizar a Base de Dados Azure para o PostgreSQL - Servidor Flexível.

| Nome SKU             | vCores | Tamanho da memória | Máx. Ligações | Conexões de utilizador max |
|----------------------|--------|-------------|-----------------|----------------------|
| **Rebentado**        |        |             |                 |                      |
| B1ms                 | 1      | 2 GiB       | 50              | 47                   |
| B2s                  | 2      | 4 GiB       | 100             | 97                   |
| **Fins Gerais**  |        |             |                 |                      |
| D2s_v3               | 2      | 8 GiB       | 214             | 211                  |
| D4s_v3               | 4      | 16 GiB      | 429             | 426                  |
| D8s_v3               | 8      | 32 GiB      | 859             | 856                  |
| D16s_v3              | 16     | 64 GiB      | 1718            | 1715                 |
| D32s_v3              | 32     | 128 GiB     | 3437            | 3434                 |
| D48s_v3              | 48     | GiB de 192     | 5000            | 4997                 |
| D64s_v3              | 64     | 256 GiB     | 5000            | 4997                 |
| **Memória Otimizada** |        |             |                 |                      |
| E2s_v3               | 2      | 16 GiB      | 1718            | 1715                 |
| E4s_v3               | 4      | 32 GiB      | 3437            | 3434                 |
| E8s_v3               | 8      | 64 GiB      | 5000            | 4997                 |
| E16s_v3              | 16     | 128 GiB     | 5000            | 4997                 |
| E32s_v3              | 32     | 256 GiB     | 5000            | 4997                 |
| E48s_v3              | 48     | 384 GiB     | 5000            | 4997                 |
| E64s_v3              | 64     | 432 GiB     | 5000            | 4997                 |

Quando as ligações excederem o limite, poderá receber o seguinte erro:
> FATAL: desculpe, muitos clientes já

> [!IMPORTANT]
> Para uma melhor experiência, recomendamos que utilize um pooler de ligação como o PgBouncer para gerir eficientemente as ligações.

Uma ligação PostgreSQL, mesmo inativa, pode ocupar cerca de 10 MB de memória. Além disso, criar novas ligações leva tempo. A maioria das aplicações solicita muitas ligações de curta duração, o que agrava esta situação. O resultado é menos recursos disponíveis para a sua carga de trabalho real, levando à diminuição do desempenho. A ligação pode ser utilizada para diminuir as ligações ociosas e reutilizar as ligações existentes. Para saber mais, visite o nosso [blog post.](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="scale-operations"></a>Operações de escala

- A escala do armazenamento do servidor requer um reinício do servidor.
- O armazenamento do servidor só pode ser dimensionado em incrementos de 2x, consulte [Compute e Storage](concepts-compute-storage.md) para obter detalhes.
- O tamanho de armazenamento do servidor diminuindo não é suportado atualmente.

### <a name="server-version-upgrades"></a>Atualizações da versão do servidor

- Atualmente, a migração automatizada entre as principais versões do motor da base de dados não é suportada. Se quiser fazer o upgrade para a próxima versão principal, faça uma [lixeira e restaure-a](../howto-migrate-using-dump-and-restore.md) num servidor que foi criado com a nova versão do motor.

### <a name="networking"></a>Rede

- Atualmente, a deslocação para dentro e para fora do VNET não é suportada.
- A combinação do acesso público com a implantação dentro de um VNET não é atualmente suportada.
- As regras de firewall não são suportadas no VNET, os grupos de segurança da rede podem ser usados em vez disso.
- Os servidores de bases de dados de acesso público podem ligar-se à internet pública, por exemplo `postgres_fdw` através de , e este acesso não pode ser restringido. Os servidores baseados em VNET podem ter acesso de saída restrito usando grupos de segurança de rede.

### <a name="high-availability-ha"></a>Alta disponibilidade (HA)

- Zone-Redundant HA não é suportado para servidores Burstable.
- O endereço IP do servidor de base de dados muda quando o seu servidor falha no standby HA. Certifique-se de que utiliza o registo DNS em vez do endereço IP do servidor.
- Se a replicação lógica for configurada com um servidor flexível configurado ha, no caso de uma falha no servidor de espera, as ranhuras de replicação lógica não são copiadas para o servidor de espera. 
- Para mais limitações de HA, consulte os conceitos - página [de documentação HA.](concepts-high-availability.md)

### <a name="availability-zones"></a>Zonas de disponibilidade

- Atualmente, os servidores de deslocação manual para uma zona de disponibilidade diferente não são suportados.
- A zona de disponibilidade do servidor de espera HA não pode ser configurada manualmente.

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Motor de pós-gres, extensões e PgBouncer

- Os postais com 10 ou mais anos não são suportados. Recomendamos a utilização da opção [Single Server](../overview-single-server.md) se necessitar de versões postgres mais antigas.
- O suporte de extensão está atualmente limitado às `contrib` extensões postgres.
- O pooler de conexão PgBouncer incorporado não está atualmente disponível para servidores de base de dados dentro de um VNET, ou para servidores Burstable.

### <a name="stopstart-operation"></a>Operação stop/start

- O servidor não pode ser detido por mais de sete dias.

### <a name="scheduled-maintenance"></a>Manutenção agendada

- Mudar a janela de manutenção menos de cinco dias antes de uma atualização já planeada não afetará essa atualização. As alterações só fazem efeito com a próxima manutenção programada.

### <a name="backing-up-a-server"></a>Backup de um servidor

- As cópias de segurança são geridas pelo sistema, não existe atualmente forma de executar estas cópias de segurança manualmente. Recomendamos a `pg_dump` utilização.
- As cópias de segurança são sempre cópias de segurança completas baseadas em instantâneos (não cópias de segurança diferenciais), possivelmente levando a uma utilização mais elevada do armazenamento de backup. Note que os registos de transações (escrever registos à frente - WAL) são separados das cópias de segurança completas/diferenciais e são arquivados continuamente.

### <a name="restoring-a-server"></a>Restaurar um servidor

- Ao utilizar a função Point-in-Time-Restore, o novo servidor é criado com as mesmas configurações de computação e armazenamento em que o servidor se baseia.
- Os servidores de base de dados baseados em VNET são restaurados no mesmo VNET quando restauram a partir de uma cópia de segurança.
- O novo servidor criado durante uma restauração não possui as regras de firewall que existiam no servidor original. As regras de firewall precisam de ser criadas separadamente para o novo servidor.
- Restaurar um servidor eliminado não é suportado.
- A restauração da região transversal não é apoiada.

### <a name="other-features"></a>Outras funcionalidades

* A autenticação AD AD ainda não está suportada. Recomendamos a utilização da opção [Single Server](../overview-single-server.md) se necessitar de autenticação Azure AD.
* As réplicas de leitura ainda não estão suportadas. Recomendamos a utilização da opção [Single Server](../overview-single-server.md) se necessitar de réplicas de leitura.


## <a name="next-steps"></a>Passos seguintes

- Compreenda [o que está disponível para opções de computação e armazenamento](concepts-compute-storage.md)
- Saiba mais sobre [versões de base de dados postgresql suportadas](concepts-supported-versions.md)
- [Reveja como fazer o back up e restaurar um servidor na Base de Dados Azure para PostgreSQL utilizando o portal Azure](how-to-restore-server-portal.md)
