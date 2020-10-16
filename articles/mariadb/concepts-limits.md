---
title: Limitações - Base de Dados Azure para MariaDB
description: Este artigo descreve limitações na Base de Dados Azure para MariaDB, tais como o número de opções de motor de ligação e armazenamento.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/2/2020
ms.openlocfilehash: c3bef7a368c6c0f2a08acdfd8da9236899a51a27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650991"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Limitações na Base de Dados Azure para MariaDB
As secções seguintes descrevem capacidade, suporte ao motor de armazenamento, suporte ao privilégio, suporte à declaração de manipulação de dados e limites funcionais no serviço de base de dados.

## <a name="server-parameters"></a>Parâmetros do servidor

> [!NOTE]
> Se estiver à procura de valores min/max para parâmetros do servidor como `max_connections` `innodb_buffer_pool_size` e, esta informação passou para o artigo **[de parâmetros](./concepts-server-parameters.md)** do servidor.

A Base de Dados Azure para MariaDB suporta afinação dos valores dos parâmetros do servidor. O valor min e máximo de alguns parâmetros (ex. `max_connections`, `join_buffer_size` , é determinado pelo `query_cache_size` nível de preços e vCores do servidor. Consulte os [parâmetros do servidor](./concepts-server-parameters.md) para obter mais informações sobre estes limites.

Após a implementação inicial, um servidor Azure para o servidor MariaDB inclui tabelas de sistemas para informações de fuso horário, mas estas tabelas não são povoadas. As tabelas do fuso horário podem ser povoadas chamando o `mysql.az_load_timezone` procedimento armazenado a partir de uma ferramenta como a linha de comando MySQL ou a bancada mySQL Workbench. Consulte o [portal Azure](howto-server-parameters.md#working-with-the-time-zone-parameter) ou os artigos [do Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) para saber como ligar para o procedimento armazenado e definir os fusos horários globais ou ao nível da sessão.

Os plugins de palavras-passe como "validate_password" e "caching_sha2_password" não são suportados pelo serviço.

## <a name="storage-engine-support"></a>Suporte ao motor de armazenamento

### <a name="supported"></a>Suportado
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMÓRIA](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Não suportado
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BURACO NEGRO](https://mariadb.com/kb/en/library/blackhole/)
- [ARQUIVO](https://mariadb.com/kb/en/library/archive/)

## <a name="privileges--data-manipulation-support"></a>Privilégios & suporte à manipulação de dados

Muitos parâmetros e configurações do servidor podem inadvertidamente degradar o desempenho do servidor ou anular as propriedades acid do servidor MariaDB. Para manter a integridade do serviço e sLA a nível de produto, este serviço não expõe múltiplas funções. 

O serviço MariaDB não permite o acesso direto ao sistema de ficheiros subjacente. Alguns comandos de manipulação de dados não são suportados. 

## <a name="privilege-support"></a>Apoio ao privilégio

### <a name="unsupported"></a>Não suportado

Os seguintes não são suportados:
- Papel DBA: Restrito. Em alternativa, pode utilizar o utilizador do administrador (criado durante a criação de novos servidores), permite-lhe executar a maioria das declarações de DDL e DML. 
- Super privilégio: Da mesma forma, [o privilégio SUPER](https://mariadb.com/kb/en/library/grant/#global-privileges) também é restrito.
- DEFINER: Requer super privilégios para criar e é restrito. Se importar dados utilizando uma cópia de segurança, remova os `CREATE DEFINER` comandos manualmente ou utilizando o `--skip-definer` comando quando efetuar uma mesqldump.
- Bases de dados do sistema: A base de [dados do sistema Mysql](https://mariadb.com/kb/en/the-mysql-database-tables/) é apenas de leitura e utilizada para suportar várias funcionalidades do PaaS. Não é possível esquirá alterações na base de dados do `mysql` sistema.
- `SELECT ... INTO OUTFILE`: Não suportado no serviço.

### <a name="supported"></a>Suportado
- `LOAD DATA INFILE` é suportado, mas o `[LOCAL]` parâmetro deve ser especificado e direcionado para um caminho UNC (armazenamento azul montado através de SMB).

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="scale-operations"></a>Operações de escala
- Atualmente, não é suportado um escalão dinâmico de e para os níveis básicos de preços.
- O tamanho de armazenamento do servidor diminui não é suportado.

### <a name="server-version-upgrades"></a>Atualizações da versão do servidor
- Atualmente, a migração automatizada entre as principais versões do motor da base de dados não é suportada.

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo
- Ao utilizar a função PITR, o novo servidor é criado com as mesmas configurações que o servidor em que se baseia.
- Restaurar um servidor eliminado não é suportado.

### <a name="subscription-management"></a>Gestão de subscrições
- Atualmente, os servidores pré-criados em movimento dinâmico em todo o grupo de subscrição e recursos não são suportados.

### <a name="vnet-service-endpoints"></a>Pontos finais de serviço da VNet
- O suporte para os pontos finais do serviço VNet é apenas para servidores otimizados para fins gerais e memória.

### <a name="storage-size"></a>Tamanho do armazenamento
- Consulte [os níveis de preços](concepts-pricing-tiers.md) para os limites de tamanho de armazenamento por nível de preços.

## <a name="current-known-issues"></a>Questões conhecidas atuais
- A instância do servidor MariaDB exibe a versão incorreta do servidor após a ligação ser estabelecida. Para obter a versão correta do motor de instância do servidor, utilize o `select version();` comando.

## <a name="next-steps"></a>Passos seguintes
- [O que está disponível em cada nível de serviço](concepts-pricing-tiers.md)
- [Versões de base de dados MariaDB suportadas](concepts-supported-versions.md)
