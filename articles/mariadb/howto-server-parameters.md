---
title: Configurar parâmetros do servidor-portal do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como configurar parâmetros de servidor MariaDB no banco de dados do Azure para MariaDB usando o portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3477820cb20d856c2e979cdfbe5528113bf4b562
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769409"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Como configurar parâmetros de servidor no banco de dados do Azure para MariaDB usando o portal do Azure

O banco de dados do Azure para MariaDB dá suporte à configuração de alguns parâmetros de servidor. Este artigo descreve como configurar esses parâmetros usando o portal do Azure. Nem todos os parâmetros de servidor podem ser ajustados.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navegue até parâmetros de servidor em portal do Azure

1. Entre no portal do Azure e localize o banco de dados do Azure para o servidor MariaDB.
2. Na seção **configurações** , clique em **parâmetros do servidor** para abrir a página parâmetros do servidor do banco de dados do Azure para o servidor MariaDB.
![página de parâmetros do portal do Azure Server](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Localize as configurações que você precisa ajustar. Examine a coluna **Descrição** para entender a finalidade e os valores permitidos.
![lista suspensa enumerar](./media/howto-server-parameters/3-toggle_parameter.png)
4. Clique em **salvar** para salvar as alterações.
![salvar ou descartar alterações](./media/howto-server-parameters/4-save_parameters.png)
5. Se você salvou novos valores para os parâmetros, sempre será possível reverter tudo para os valores padrão selecionando **Redefinir tudo para o padrão**.
![redefinir tudo para o](./media/howto-server-parameters/5-reset_parameters.png) padrão

## <a name="list-of-configurable-server-parameters"></a>Lista de parâmetros de servidor configuráveis

A lista de parâmetros de servidor com suporte está crescendo constantemente. Use a guia parâmetros de servidor no portal do Azure para obter a definição e configurar parâmetros de servidor com base em seus requisitos de aplicativo.

## <a name="non-configurable-server-parameters"></a>Parâmetros de servidor não configuráveis

O pool de buffers InnoDB e as conexões máximas não são configuráveis e estão ligadas ao seu [tipo de preço](concepts-pricing-tiers.md).

|**Escalão de Preço**| **vCore (s)**|**Pool de buffers InnoDB (MB)**| **Máximo de conexões**|
|---|---|---|---|
|Basic| 1| 1024| 50|
|Basic| 2| 2560| 100|
|Fins Gerais| 2| 3584| 300|
|Fins Gerais| 4| 7680| 625|
|Fins Gerais| 8| 15360| 1250|
|Fins Gerais| 16| 31232| 2500|
|Fins Gerais| 32| 62976| 5000|
|Fins Gerais| 64| 125952| 10000|
|Memória Otimizada| 2| 7168| 600|
|Memória Otimizada| 4| 15360| 1250|
|Memória Otimizada| 8| 30720| 2500|
|Memória Otimizada| 16| 62464| 5000|
|Memória Otimizada| 32| 125952| 10000|

Esses parâmetros de servidor adicionais não são configuráveis no sistema:

|**Meter**|**Valor fixo**|
| :------------------------ | :-------- |
|innodb_file_per_table na camada básica|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

Outros parâmetros de servidor que não estão listados aqui são definidos como seus valores padrão MariaDB prontos para uso para [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

## <a name="working-with-the-time-zone-parameter"></a>Trabalhando com o parâmetro de fuso horário

### <a name="populating-the-time-zone-tables"></a>Populando as tabelas de fuso horário

As tabelas de fuso horário no servidor podem ser populadas chamando o procedimento armazenado `az_load_timezone` de uma ferramenta como a linha de comando do MySQL ou o MySQL Workbench.

> [!NOTE]
> Se você estiver executando o comando `az_load_timezone` do MySQL Workbench, talvez seja necessário desativar o modo de atualização segura primeiro usando `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Para exibir os valores de fuso horário disponíveis, execute o seguinte comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Configurando o fuso horário de nível global

O fuso horário de nível global pode ser definido na página de **parâmetros do servidor** na portal do Azure. O abaixo define o fuso horário global para o valor "US/Pacífico".

![Definir parâmetro de fuso horário](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Definindo o fuso horário do nível de sessão

O fuso horário do nível de sessão pode ser definido executando o comando `SET time_zone` de uma ferramenta como a linha de comando do MySQL ou o MySQL Workbench. O exemplo a seguir define o fuso horário para o fuso horário **dos EUA/Pacífico** .

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação do MariaDB para [funções de data e hora](https://mariadb.com/kb/en/library/convert_tz/).

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
