---
title: Limitações-banco de dados do Azure para MariaDB
description: Este artigo descreve as limitações no banco de dados do Azure para MariaDB, como o número de opções de mecanismo de armazenamento e conexão.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: fc89b6233602c81ea622a528c223adf2003f0f68
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772501"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Limitações no banco de dados do Azure para MariaDB
As seções a seguir descrevem a capacidade, o suporte ao mecanismo de armazenamento, o suporte a privilégios, o suporte à instrução de manipulação de dados e os limites funcionais no serviço de banco de dados

## <a name="maximum-connections"></a>Máximo de conexões
O número máximo de conexões por tipo de preço e vCores são os seguintes:

|**Escalão de Preço**|**vCore (s)**| **Máximo de conexões**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
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

Quando as conexões excederem o limite, você poderá receber o seguinte erro:
> ERRO 1040 (08004): muitas conexões

## <a name="storage-engine-support"></a>Suporte ao mecanismo de armazenamento

### <a name="supported"></a>Suportadas
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMÓRIA](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Não suportado
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [OPERAÇÃO](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Suporte a privilégios

### <a name="unsupported"></a>Não suportado
- Função DBA: muitos parâmetros e configurações de servidor podem degradar inadvertidamente o desempenho do servidor ou negar as propriedades ACID do DBMS. Dessa forma, para manter a integridade do serviço e o SLA em um nível de produto, esse serviço não expõe a função DBA. A conta de usuário padrão, que é construída quando uma nova instância de banco de dados é criada, permite que o usuário execute a maioria das instruções DDL e DML na instância do banco de dados gerenciado.
- SUPER privilégio: da mesma forma, o [privilégio super](https://mariadb.com/kb/en/library/grant/#global-privileges) também é restrito.
- O desfinamento: requer a criação de privilégios e é restrito. Se importar dados usando um backup, remova os comandos `CREATE DEFINER` manualmente ou usando o comando `--skip-definer` ao executar um mysqldump.

## <a name="data-manipulation-statement-support"></a>Suporte à instrução de manipulação de dados

### <a name="supported"></a>Suportadas
- Há suporte para `LOAD DATA INFILE`, mas o parâmetro `[LOCAL]` deve ser especificado e direcionado para um caminho UNC (armazenamento do Azure montado por meio de SMB).

### <a name="unsupported"></a>Não suportado
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="scale-operations"></a>Operações de escala
- No momento, não há suporte para o dimensionamento dinâmico de e para os tipos de preço básicos.
- Não há suporte para a redução do tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Atualizações de versão do servidor
- Atualmente, não há suporte para migração automatizada entre as versões do mecanismo de banco de dados principal.

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo
- Ao usar o recurso PITR, o novo servidor é criado com as mesmas configurações que o servidor no qual ele se baseia.
- Não há suporte para a restauração de um servidor excluído.

### <a name="subscription-management"></a>Gestão de subscrições
- Atualmente, não há suporte para a movimentação dinâmica de servidores previamente criados na assinatura e no grupo de recursos.

### <a name="vnet-service-endpoints"></a>Pontos de extremidade de serviço de VNet
- O suporte para pontos de extremidade de serviço de VNet é apenas para servidores Uso Geral e com otimização de memória.

### <a name="storage-size"></a>Tamanho do armazenamento
- Veja os [tipos de preço](concepts-pricing-tiers.md) para os limites de tamanho de armazenamento por tipo de preço.

## <a name="current-known-issues"></a>Problemas conhecidos atuais
- A instância do servidor MariaDB exibe a versão incorreta do servidor após a conexão ser estabelecida. Para obter a versão correta do mecanismo da instância do servidor, use o comando `select version();`.

## <a name="next-steps"></a>Passos seguintes
- [O que está disponível em cada camada de serviço](concepts-pricing-tiers.md)
- [Versões do banco de dados MariaDB com suporte](concepts-supported-versions.md)
