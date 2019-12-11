---
title: Limitações-banco de dados do Azure para MySQL
description: Este artigo descreve as limitações na base de dados do Azure para MySQL, como o número de ligação e opções de motor de armazenamento.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 8b3d6ea46c4a88187b70b520457ad34f7e7f36ba
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975147"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limitações na base de dados do Azure para MySQL
As secções seguintes descrevem a capacidade, suporte ao mecanismo de armazenamento, o suporte de privilégio, manipulação de dados de suporte de instrução e limites funcionais no serviço de base de dados. Consulte também [limitações gerais](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) aplicável para o motor de base de dados MySQL.

## <a name="maximum-connections"></a>Número máximo de ligações
Seguem-se o número máximo de ligações por vCores e escalão de preço: 

|**Escalão de Preço**|**vCore(s)**| **Máx. ligações**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
|Fins Gerais| 2| 600|
|Fins Gerais| 4| 1250|
|Fins Gerais| 8| 2500|
|Fins Gerais| 16| 5000|
|Fins Gerais| 32| 10000|
|Fins Gerais| 64| 20000|
|Memória Otimizada| 2| 1250|
|Memória Otimizada| 4| 2500|
|Memória Otimizada| 8| 5000|
|Memória Otimizada| 16| 10000|
|Memória Otimizada| 32| 20000|

Quando as ligações excederem o limite, poderá receber o erro seguinte:
> Erro 1040 (08004): Demasiadas ligações

## <a name="storage-engine-support"></a>Suporte ao mecanismo de armazenamento

### <a name="supported"></a>Suportadas
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMÓRIA](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Não suportado
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARQUIVO](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERADO](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Suporte de privilégio

### <a name="unsupported"></a>Não suportado
- Função DBA: várias definições de parâmetros do servidor e inadvertidamente podem degradar o desempenho do servidor ou negar as propriedades ACID do DBMS. Como tal, para manter a integridade de serviço e o SLA num nível de produto, este serviço não expõe a função DBA. A conta de usuário padrão, é criada quando é criada uma nova instância de base de dados, permite que o utilizador efetuar a maioria das instruções DDL e DML na instância gerida da base de dados. 
- Privilégio SUPER: da mesma forma [privilégio SUPER](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) também é restrito.
- O desfinamento: requer a criação de privilégios e é restrito. Se importar dados usando um backup, remova os comandos `CREATE DEFINER` manualmente ou usando o comando `--skip-definer` ao executar um mysqldump.

## <a name="data-manipulation-statement-support"></a>Suporte de instrução de manipulação de dados

### <a name="supported"></a>Suportadas
- `LOAD DATA INFILE` é suportada, mas o `[LOCAL]` parâmetro tem de ser especificado e direcionado para um caminho UNC (armazenamento do Azure montado através de SMB).

### <a name="unsupported"></a>Não suportado
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="scale-operations"></a>Operações de dimensionamento
- Dimensionamento dinâmico de e para os escalões de preços básicos não é atualmente suportada.
- Não é suportada a diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Atualização de versão do servidor
- Migração automatizada entre as versões do motor de base de dados principal não é atualmente suportada. Se gostaria de atualizar para a próxima versão principal, dê uma [cópia de segurança e restaurar](./concepts-migrate-dump-restore.md) -lo para um servidor que foi criado com a nova versão do motor.

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo
- Quando utilizar a funcionalidade PITR, é criado o novo servidor com as mesmas configurações que o servidor que baseia-se.
- Não é suportado restaurar um servidor foi eliminado.

### <a name="vnet-service-endpoints"></a>Pontos finais de serviço de VNet
- Suporte para pontos finais de serviço da VNet é apenas para fins gerais e memória otimizada de servidores.

### <a name="storage-size"></a>Tamanho do armazenamento
- Veja os [tipos de preço](concepts-pricing-tiers.md) para os limites de tamanho de armazenamento por tipo de preço.

## <a name="current-known-issues"></a>Atuais problemas conhecidos
- Instância do servidor MySQL apresenta a versão de servidor incorreto depois de ligação é estabelecida. Para obter versão do motor de instância de servidor correto, utilize o `select version();` comando.

## <a name="next-steps"></a>Passos seguintes
- [O que está disponível em cada escalão de serviço](concepts-pricing-tiers.md)
- [Versões suportadas do banco de dados MySQL](concepts-supported-versions.md)
