---
title: Limitações - Base de Dados Azure para MySQL
description: Este artigo descreve limitações na Base de Dados Azure para o MySQL, tais como o número de opções de motor de ligação e armazenamento.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 9b18b24686908ac92f97ea0cae892369919ae4d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721025"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limitações na Base de Dados Azure para o MySQL
As secções seguintes descrevem capacidade, suporte ao motor de armazenamento, suporte ao privilégio, suporte à declaração de manipulação de dados e limites funcionais no serviço de base de dados. Consulte também [as limitações gerais](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) aplicáveis ao motor de base de dados MySQL.

## <a name="server-parameters"></a>Parâmetros do servidor

> [!NOTE]
> Se estiver à procura de valores min/max para parâmetros do servidor como `max_connections` `innodb_buffer_pool_size` e, esta informação passou para o artigo **[de parâmetros](./concepts-server-parameters.md)** do servidor.

A Azure Database for MySQL suporta afinar os valores dos parâmetros do servidor. O valor min e máximo de alguns parâmetros (ex. `max_connections`, `join_buffer_size` , é determinado pelo `query_cache_size` nível de preços e vCores do servidor. Consulte os [parâmetros do servidor](./concepts-server-parameters.md) para obter mais informações sobre estes limites.

Após a implementação inicial, um servidor Azure para o MySQL inclui tabelas de sistemas para informações de fuso horário, mas estas tabelas não são povoadas. As tabelas do fuso horário podem ser povoadas chamando o `mysql.az_load_timezone` procedimento armazenado a partir de uma ferramenta como a linha de comando MySQL ou a bancada mySQL Workbench. Consulte o [portal Azure](howto-server-parameters.md#working-with-the-time-zone-parameter) ou os artigos [do Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) para saber como ligar para o procedimento armazenado e definir os fusos horários globais ou ao nível da sessão.

Os plugins de palavras-passe como "validate_password" e "caching_sha2_password" não são suportados pelo serviço.

## <a name="storage-engines"></a>Motores de armazenamento

O MySQL suporta muitos motores de armazenamento. Na Base de Dados Azure para o MySQL, os seguintes motores de armazenamento são suportados e não suportados:

### <a name="supported"></a>Suportado
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMÓRIA](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Não suportado
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BURACO NEGRO](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARQUIVO](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERADO](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>Privilégios & suporte à manipulação de dados

Muitos parâmetros e configurações do servidor podem inadvertidamente degradar o desempenho do servidor ou anular as propriedades acid do servidor MySQL. Para manter a integridade do serviço e sLA a nível de produto, este serviço não expõe múltiplas funções. 

O serviço MySQL não permite o acesso direto ao sistema de ficheiros subjacente. Alguns comandos de manipulação de dados não são suportados. 

### <a name="unsupported"></a>Não suportado

Os seguintes não são suportados:
- Papel DBA: Restrito. Em alternativa, pode utilizar o utilizador do administrador (criado durante a criação de novos servidores), permite-lhe executar a maioria das declarações de DDL e DML. 
- SUPER privilégio: Da mesma forma, [o privilégio SUPER](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) é restrito.
- DEFINER: Requer super privilégios para criar e é restrito. Se importar dados utilizando uma cópia de segurança, remova os `CREATE DEFINER` comandos manualmente ou utilizando o `--skip-definer` comando quando efetuar uma mesqldump.
- Bases de dados do sistema: A base de [dados do sistema Mysql](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) é apenas de leitura e utilizada para suportar várias funcionalidades do PaaS. Não é possível esquirá alterações na base de dados do `mysql` sistema.
- `SELECT ... INTO OUTFILE`: Não suportado no serviço.
- `LOAD_FILE(file_name)`: Não suportado no serviço.

### <a name="supported"></a>Suportado
- `LOAD DATA INFILE` é suportado, mas o `[LOCAL]` parâmetro deve ser especificado e direcionado para um caminho UNC (armazenamento azul montado através de SMB).

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="scale-operations"></a>Operações de escala
- Atualmente, não é suportado um escalão dinâmico de e para os níveis básicos de preços.
- O tamanho de armazenamento do servidor diminui não é suportado.

### <a name="server-version-upgrades"></a>Atualizações da versão do servidor
- Atualmente, a migração automatizada entre as principais versões do motor da base de dados não é suportada. Se quiser fazer o upgrade para a próxima versão principal, faça uma [lixeira e restaure-a](./concepts-migrate-dump-restore.md) num servidor que foi criado com a nova versão do motor.

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo
- Ao utilizar a função PITR, o novo servidor é criado com as mesmas configurações que o servidor em que se baseia.
- Restaurar um servidor eliminado não é suportado.

### <a name="vnet-service-endpoints"></a>Pontos finais de serviço da VNet
- O suporte para os pontos finais do serviço VNet é apenas para servidores otimizados para fins gerais e memória.

### <a name="storage-size"></a>Tamanho do armazenamento
- Consulte [os níveis de preços](concepts-pricing-tiers.md) para os limites de tamanho de armazenamento por nível de preços.

## <a name="current-known-issues"></a>Questões conhecidas atuais
- A instância do servidor MySQL exibe a versão errada do servidor após a ligação ser estabelecida. Para obter a versão correta do motor de instância do servidor, utilize o `select version();` comando.

## <a name="next-steps"></a>Passos seguintes
- [O que está disponível em cada nível de serviço](concepts-pricing-tiers.md)
- [Versões de base de dados do MySQL suportadas](concepts-supported-versions.md)
