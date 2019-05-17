---
title: Limitações na base de dados do Azure para MariaDB
description: Este artigo descreve as limitações na base de dados do Azure para MariaDB, como o número de ligação e opções de motor de armazenamento.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: b78671cc61a4fe755b908ed9f71052cbd0a70b38
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550513"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Limitações na base de dados do Azure para MariaDB
As secções seguintes descrevem a capacidade, suporte ao mecanismo de armazenamento, o suporte de privilégio, manipulação de dados de suporte de instrução e limites funcionais no serviço de base de dados.

## <a name="maximum-connections"></a>Número máximo de ligações
Seguem-se o número máximo de ligações por vCores e escalão de preço:

|**Escalão de Preço**|**vCore(s)**| **Máx. ligações**|
|---|---|---|
|Básica| 1| 50|
|Básica| 2| 100|
|Fins Gerais| 2| 300|
|Fins Gerais| 4| 625|
|Fins Gerais| 8| 1250|
|Fins Gerais| 16| 2500|
|Fins Gerais| 32| 5000|
|Fins Gerais| 64| 10000|
|Memória Otimizada| 2| 600|
|Memória Otimizada| 4| 1250|
|Memória Otimizada| 8| 2500|
|Memória Otimizada| 16| 5000|
|Memória Otimizada| 32| 10000|

Quando as ligações excederem o limite, poderá receber o erro seguinte:
> ERROR 1040 (08004): Demasiadas ligações

## <a name="storage-engine-support"></a>Suporte ao mecanismo de armazenamento

### <a name="supported"></a>Suportadas
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMÓRIA](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Não suportado
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARQUIVO](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Suporte de privilégio

### <a name="unsupported"></a>Não suportado
- Função DBA: Várias definições de parâmetros do servidor e inadvertidamente podem degradar o desempenho do servidor ou negar as propriedades ACID do DBMS. Como tal, para manter a integridade de serviço e o SLA num nível de produto, este serviço não expõe a função DBA. A conta de usuário padrão, é criada quando é criada uma nova instância de base de dados, permite que o utilizador efetuar a maioria das instruções DDL e DML na instância gerida da base de dados.
- Privilégio SUPER: Da mesma forma [privilégio SUPER](https://mariadb.com/kb/en/library/grant/#global-privileges) também é restrito.
- DEFINER: Requer privilégios superutilizadores para criar e é restrito. Se importar dados através de uma cópia de segurança, remova a `CREATE DEFINER` comandos manualmente ou utilizando o `--skip-definer` ao realizar um mysqldump de comando.

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
- Migração automatizada entre as versões do motor de base de dados principal não é atualmente suportada.

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo
- Quando utilizar a funcionalidade PITR, é criado o novo servidor com as mesmas configurações que o servidor que baseia-se.
- Não é suportado restaurar um servidor foi eliminado.

### <a name="subscription-management"></a>Gestão de subscrições
- Dinamicamente movendo servidores previamente criadas pela subscrição e grupo de recursos não é atualmente suportada.

### <a name="vnet-service-endpoints"></a>Pontos finais de serviço de VNet
- Suporte para pontos finais de serviço da VNet é apenas para fins gerais e memória otimizada de servidores.

### <a name="storage-size"></a>Tamanho de armazenamento
- Consulte a [escalões de preço](concepts-pricing-tiers.md) para os limites de tamanho de armazenamento por escalão de preço.

## <a name="current-known-issues"></a>Atuais problemas conhecidos
- Instância de servidor MariaDB apresenta a versão de servidor incorreto depois de ligação é estabelecida. Para obter versão do motor de instância de servidor correto, utilize o `select version();` comando.

## <a name="next-steps"></a>Passos Seguintes
- [O que está disponível em cada escalão de serviço](concepts-pricing-tiers.md)
- [Versões de base de dados suportadas MariaDB](concepts-supported-versions.md)
