---
title: Limitações - Base de Dados Azure para MariaDB
description: Este artigo descreve limitações na Base de Dados Azure para ODDb, tais como o número de opções de motor de ligação e armazenamento.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: df44cbefaec943a2df483f4804650b939c796cb5
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191143"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Limitações na Base de Dados Azure para MariaDB
As secções seguintes descrevem a capacidade, suporte ao mecanismo de armazenamento, o suporte de privilégio, manipulação de dados de suporte de instrução e limites funcionais no serviço de base de dados.

## <a name="maximum-connections"></a>Número máximo de ligações
Seguem-se o número máximo de ligações por vCores e escalão de preço:

|**Escalão de Preço**|**vCore(s)**| **Ligações Max**|
|---|---|---|
|Básica| 1| 50|
|Básica| 2| 100|
|Fins Gerais| 2| 600|
|Fins Gerais| 4| 1250|
|Fins Gerais| 8| 2500|
|Fins Gerais| 16| 5000|
|Fins Gerais| 32| 10000|
|Fins Gerais| 64| 20000|
|Memória Otimizada| 2| 800|
|Memória Otimizada| 4| 2500|
|Memória Otimizada| 8| 5000|
|Memória Otimizada| 16| 10000|
|Memória Otimizada| 32| 20000|

Quando as ligações excederem o limite, poderá receber o erro seguinte:
> Erro 1040 (08004): Demasiadas ligações

> [!IMPORTANT]
> Para uma melhor experiência, recomendamos que utilize um pooler de ligação como proxySQL para gerir eficientemente as ligações.

A criação de novas ligações de clientes ao MariaDB leva tempo e uma vez estabelecidas, estas ligações ocupam recursos de base de dados, mesmo quando estão inativas. A maioria das aplicações solicita muitas ligações de curta duração, o que compõe esta situação. O resultado é menos recursos disponíveis para a sua carga de trabalho real, levando a uma diminuição do desempenho. Um pooler de ligação que diminui as ligações inativas e reutiliza as ligações existentes ajudará a evitar isso. Para aprender sobre a configuração do ProxySQL, visite o nosso post de [blog.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)

## <a name="storage-engine-support"></a>Suporte ao mecanismo de armazenamento

### <a name="supported"></a>Suportado
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMÓRIA](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Não suportado
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BURACO NEGRO](https://mariadb.com/kb/en/library/blackhole/)
- [ARQUIVO](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Suporte de privilégio

### <a name="unsupported"></a>Não suportado
- Função DBA: várias definições de parâmetros do servidor e inadvertidamente podem degradar o desempenho do servidor ou negar as propriedades ACID do DBMS. Como tal, para manter a integridade de serviço e o SLA num nível de produto, este serviço não expõe a função DBA. A conta de usuário padrão, é criada quando é criada uma nova instância de base de dados, permite que o utilizador efetuar a maioria das instruções DDL e DML na instância gerida da base de dados.
- Super privilégio: Igualmente [super privilégio](https://mariadb.com/kb/en/library/grant/#global-privileges) também é restrito.
- DEFINER: Requer super privilégios para criar e é restrito. Se importar dados utilizando uma cópia de segurança, remova o `CREATE DEFINER` comandos manualmente ou utilizando o comando `--skip-definer` ao executar um mysqldump.

## <a name="data-manipulation-statement-support"></a>Suporte de instrução de manipulação de dados

### <a name="supported"></a>Suportado
- `LOAD DATA INFILE` é suportado, mas o parâmetro `[LOCAL]` deve ser especificado e direcionado para um caminho unc (armazenamento Azure montado através de SMB).

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
- Os servidores pré-criados em movimento dinâmico através de subscrição e grupo de recursos não são suportados atualmente.

### <a name="vnet-service-endpoints"></a>Pontos finais de serviço de VNet
- Suporte para pontos finais de serviço da VNet é apenas para fins gerais e memória otimizada de servidores.

### <a name="storage-size"></a>Tamanho do armazenamento
- Consulte [os níveis](concepts-pricing-tiers.md) de preços para os limites de tamanho de armazenamento por nível de preços.

## <a name="current-known-issues"></a>Atuais problemas conhecidos
- A instância do servidor MariaDB apresenta a versão do servidor incorreta após a ligação ser estabelecida. Para obter a versão correta do motor da instância do servidor, utilize o comando `select version();`.

## <a name="next-steps"></a>Passos seguintes
- [O que está disponível em cada nível de serviço](concepts-pricing-tiers.md)
- [Versões de base de dados MariaDB suportadas](concepts-supported-versions.md)
