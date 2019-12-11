---
title: Configurar parâmetros do servidor-portal do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como configurar parâmetros de servidor MariaDB no banco de dados do Azure para MariaDB usando o portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: ba091d05aa243fab08138c96827d2f657d9755de
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976303"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Como configurar parâmetros de servidor no banco de dados do Azure para MariaDB usando o portal do Azure

O banco de dados do Azure para MariaDB dá suporte à configuração de alguns parâmetros de servidor. Este artigo descreve como configurar esses parâmetros com o portal do Azure. Nem todos os parâmetros do servidor podem ser ajustados.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navegue para os parâmetros do servidor no portal do Azure

1. Entre no portal do Azure e localize o banco de dados do Azure para o servidor MariaDB.
2. Na seção **configurações** , clique em **parâmetros do servidor** para abrir a página parâmetros do servidor do banco de dados do Azure para o servidor MariaDB.
![Página de parâmetros do servidor do portal do Azure](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Localize quaisquer definições que precisa de ajustar. Reveja os **Descrição** coluna para compreender a finalidade e os valores permitidos.
![Enumerar para baixo](./media/howto-server-parameters/3-toggle_parameter.png)
4. Clique em **guardar** para guardar as alterações.
![Guardar ou alterações de rejeição](./media/howto-server-parameters/4-save_parameters.png)
5. Se guardar novos valores para os parâmetros, poderá sempre reverter tudo volta para os valores predefinidos, selecionando **repor tudo para predefinição**.
![Repor tudo para predefinição](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista de parâmetros do servidor configurável

A lista de parâmetros de servidor com suporte está constantemente a crescer. Utilize o separador de parâmetros de servidor no portal do Azure para obter a definição e configurar os parâmetros do servidor com base nos requisitos da sua aplicação.

## <a name="non-configurable-server-parameters"></a>Parâmetros do servidor não configuráveis

Conjunto de memória intermédia de InnoDB e máx. ligações não são configuráveis e empatados para sua [escalão de preço](concepts-pricing-tiers.md).

|**Escalão de Preço**| **vCore(s)**|**Pool de buffers de InnoDB (MB)**|
|---|---|---|
|Basic| 1| 1024|
|Basic| 2| 2560|
|Fins Gerais| 2| 3584|
|Fins Gerais| 4| 7680|
|Fins Gerais| 8| 15360|
|Fins Gerais| 16| 31232|
|Fins Gerais| 32| 62976|
|Fins Gerais| 64| 125952|
|Memória Otimizada| 2| 7168|
|Memória Otimizada| 4| 15360|
|Memória Otimizada| 8| 30720|
|Memória Otimizada| 16| 62464|
|Memória Otimizada| 32| 125952|

Esses parâmetros de servidor adicionais não são configuráveis no sistema:

|**Parâmetro**|**Valor fixo**|
| :------------------------ | :-------- |
|innodb_file_per_table no escalão básico|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

Outros parâmetros de servidor que não estão listados aqui são definidos como seus valores padrão MariaDB prontos para uso para [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

## <a name="working-with-the-time-zone-parameter"></a>Trabalhar com o parâmetro de fuso horário

### <a name="populating-the-time-zone-tables"></a>Preencher as tabelas de fuso horário

As tabelas de fuso horário no seu servidor podem ser preenchidas chamando o `az_load_timezone` procedimento armazenado a partir de uma ferramenta como a linha de comandos MySQL ou o MySQL Workbench.

> [!NOTE]
> Se estiver a executar o `az_load_timezone` comando a partir do MySQL Workbench, poderá ter de desativar o modo de atualização segura primeiro usando `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Você deve reiniciar o servidor para garantir que as tabelas de fuso horário sejam populadas corretamente. Para reiniciar o servidor, use o [portal do Azure](howto-restart-server-portal.md) ou a [CLI](howto-restart-server-cli.md).
Para ver os valores de fuso horário disponível, execute o seguinte comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Definir o fuso de horário de nível global

O fuso de horário de nível global pode ser definido entre o **parâmetros do servidor** página no portal do Azure. A seguir define o fuso horário global para o valor "E.U.A. / Pacífico".

![Parâmetro de fuso horário do conjunto](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Definir o fuso de horário de nível de sessão

A sessão de nível de tempo de zona pode ser definida ao executar o `SET time_zone` comando a partir de uma ferramenta como a linha de comandos MySQL ou o MySQL Workbench. O exemplo abaixo define o fuso horário o **E.U.A. / Pacífico** fuso horário.

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação do MariaDB para [funções de data e hora](https://mariadb.com/kb/en/library/convert_tz/).

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
