---
title: Configure parâmetros de servidor - Portal Azure - Base de Dados Azure para MariaDB
description: Este artigo descreve como configurar parâmetros do servidor MariaDB na Base de Dados Azure para MariaDB utilizando o portal Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/16/2020
ms.openlocfilehash: f39e9450fb922e5b93d7b4b809df73cf5ab007c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81602395"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Como configurar parâmetros de servidor em Base de Dados Azure para MariaDB utilizando o portal Azure

A Base de Dados Azure para MariaDB suporta a configuração de alguns parâmetros do servidor. Este artigo descreve como configurar estes parâmetros utilizando o portal Azure. Nem todos os parâmetros do servidor podem ser ajustados.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navegue para os parâmetros do servidor no portal Azure

1. Inscreva-se no portal Azure e, em seguida, localize a sua Base de Dados Azure para o servidor MariaDB.
2. Na secção **DEFINIÇÕES,** clique nos **parâmetros do Servidor** para abrir a página de parâmetros do servidor para a Base de Dados Azure para o servidor MariaDB.
![Página de parâmetros do servidor do portal Azure](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Localize quaisquer definições que precise de ajustar. Reveja a coluna **Descrição** para compreender o propósito e os valores permitidos.
![Enumerar a queda](./media/howto-server-parameters/3-toggle_parameter.png)
4. Clique em **Guardar** para guardar as suas alterações.
![Guardar ou Descartar alterações](./media/howto-server-parameters/4-save_parameters.png)
5. Se guardou novos valores para os parâmetros, pode sempre reverter tudo de volta para os valores predefinidos selecionando **tudo para padrão**.
![Redefinir tudo para padrão](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista de parâmetros de servidor configuráveis

A lista de parâmetros de servidor suportados está em constante crescimento. Utilize o separador de parâmetros do servidor no portal Azure para obter a definição e configurar os parâmetros do servidor com base nos requisitos da sua aplicação.

## <a name="non-configurable-server-parameters"></a>Parâmetros de servidor não configuráveis

O InnoDB Buffer Pool e as Ligações Max não são configuráveis e ligados ao seu [nível de preços](concepts-pricing-tiers.md).

|**Nível de Preços**| **vCore(s)**|**Piscina tampão InnoDB (MB)**|
|---|---|---|
|Básico| 1| 1024|
|Básico| 2| 2560|
|Fins Gerais| 2| 3584|
|Fins Gerais| 4| 7680|
|Fins Gerais| 8| 15360|
|Fins Gerais| 16| 31232|
|Fins Gerais| 32| 62976|
|Fins Gerais| 64| 125952|
|Otimizada para Memória| 2| 7168|
|Otimizada para Memória| 4| 15360|
|Otimizada para Memória| 8| 30720|
|Otimizada para Memória| 16| 62464|
|Otimizada para Memória| 32| 125952|

Estes parâmetros adicionais do servidor não são configuráveis no sistema:

|**Parâmetro**|**Valor fixo**|
| :------------------------ | :-------- |
|innodb_file_per_table no nível básico|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256MB|
|innodb_log_files_in_group|2|

Outros parâmetros do servidor que não estão listados aqui estão definidos para os seus valores padrão MariaDB fora da caixa para [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

## <a name="working-with-the-time-zone-parameter"></a>Trabalhar com o parâmetro do fuso horário

### <a name="populating-the-time-zone-tables"></a>Povoar as tabelas do fuso horário

As tabelas de fuso horário no `mysql.az_load_timezone` seu servidor podem ser povoadas ligando para o procedimento armazenado a partir de uma ferramenta como a linha de comando MySQL ou a bancada de trabalho MySQL.

> [!NOTE]
> Se estiver a `mysql.az_load_timezone` executar o comando a partir da Bancada de Trabalho MySQL, poderá ter de desligar o modo de atualização seguro primeiro utilizando `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Deve reiniciar o servidor para garantir que as tabelas de fuso horário estão corretamente povoadas. Para reiniciar o servidor, utilize o [portal Azure](howto-restart-server-portal.md) ou [o CLI](howto-restart-server-cli.md).
Para ver os valores disponíveis do fuso horário, execute o seguinte comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Definição do fuso horário de nível global

O fuso horário de nível global pode ser definido a partir da página de **parâmetros do Servidor** no portal Azure. A seguir define o fuso horário global para o valor "EUA/Pacífico".

![Definir parâmetro de fuso horário](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Definição do fuso horário do nível da sessão

O fuso horário do nível `SET time_zone` da sessão pode ser definido executando o comando a partir de uma ferramenta como a linha de comando MySQL ou a bancada de trabalho MySQL. O exemplo abaixo define o fuso horário para o fuso horário **EUA/Pacífico.**

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação MariaDB para funções de [data e hora.](https://mariadb.com/kb/en/library/convert_tz/)

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
