---
title: Como configurar parâmetros do servidor na base de dados do Azure para MySQL
description: Este artigo descreve como configurar parâmetros do servidor MySQL na base de dados do Azure para MySQL com o portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: d589800f62f96510a09d23cb6e8794177121c6dd
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419711"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Como configurar os parâmetros do servidor na base de dados do Azure para MySQL com o portal do Azure

Base de dados do Azure para MySQL suporta a configuração de alguns parâmetros de servidor. Este artigo descreve como configurar esses parâmetros com o portal do Azure. Nem todos os parâmetros do servidor podem ser ajustados.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navegue para os parâmetros do servidor no portal do Azure

1. Inicie sessão no portal do Azure, em seguida, localize a base de dados do Azure para o servidor MySQL.
2. Na seção **configurações** , clique em **parâmetros do servidor** para abrir a página parâmetros do servidor para o servidor do banco de dados do Azure para MySQL.
![página de parâmetros do portal do Azure Server](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Localize quaisquer definições que precisa de ajustar. Examine a coluna **Descrição** para entender a finalidade e os valores permitidos.
![lista suspensa enumerar](./media/howto-server-parameters/3-toggle_parameter.png)
4. Clique em **salvar** para salvar as alterações.
![salvar ou descartar alterações](./media/howto-server-parameters/4-save_parameters.png)
5. Se você salvou novos valores para os parâmetros, sempre será possível reverter tudo para os valores padrão selecionando **Redefinir tudo para o padrão**.
![redefinir tudo para o](./media/howto-server-parameters/5-reset_parameters.png) padrão

## <a name="list-of-configurable-server-parameters"></a>Lista de parâmetros do servidor configurável

A lista de parâmetros de servidor com suporte está constantemente a crescer. Utilize o separador de parâmetros de servidor no portal do Azure para obter a definição e configurar os parâmetros do servidor com base nos requisitos da sua aplicação.

## <a name="non-configurable-server-parameters"></a>Parâmetros do servidor não configuráveis

O tamanho do pool de buffers InnoDB não é configurável e está vinculado ao seu [tipo de preço](concepts-service-tiers.md).

|**Escalão de Preço**|**vCore (s)**|**Tamanho do pool de buffers InnoDB em MB <br>(servidores com suporte para até 4 TB de armazenamento)**| **Tamanho do pool de buffers InnoDB em MB <br>(servidores com suporte para até 16 TB de armazenamento)**|
|:---|---:|---:|---:|
|Básica| 1| 832| |
|Básica| 2| 2560| |
|Fins Gerais| 2| 3584| 7168|
|Fins Gerais| 4| 7680| 15360|
|Fins Gerais| 8| 15360| 30720|
|Fins Gerais| 16| 31232| 62464|
|Fins Gerais| 32| 62976| 125952|
|Fins Gerais| 64| 125952| 251904|
|Memória Otimizada| 2| 7168| 14336|
|Memória Otimizada| 4| 15360| 30720|
|Memória Otimizada| 8| 30720| 61440|
|Memória Otimizada| 16| 62464| 124928|
|Memória Otimizada| 32| 125952| 251904|

Esses parâmetros de servidor adicionais não são configuráveis no sistema:

|**Meter**|**Valor fixo**|
| :------------------------ | :-------- |
|innodb_file_per_table no escalão básico|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

Outros parâmetros de servidor que não estão listados aqui são definidos para seus valores padrão do MySQL prontos para uso para as versões [5,7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) e [5,6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="working-with-the-time-zone-parameter"></a>Trabalhar com o parâmetro de fuso horário

### <a name="populating-the-time-zone-tables"></a>Preencher as tabelas de fuso horário

As tabelas de fuso horário no servidor podem ser populadas chamando o procedimento armazenado `az_load_timezone` de uma ferramenta como a linha de comando do MySQL ou o MySQL Workbench.

> [!NOTE]
> Se você estiver executando o comando `az_load_timezone` do MySQL Workbench, talvez seja necessário desativar o modo de atualização segura primeiro usando `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Para ver os valores de fuso horário disponível, execute o seguinte comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Definir o fuso de horário de nível global

O fuso horário de nível global pode ser definido na página de **parâmetros do servidor** na portal do Azure. A seguir define o fuso horário global para o valor "E.U.A. / Pacífico".

![Parâmetro de fuso horário do conjunto](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Definir o fuso de horário de nível de sessão

O fuso horário do nível de sessão pode ser definido executando o comando `SET time_zone` de uma ferramenta como a linha de comando do MySQL ou o MySQL Workbench. O exemplo a seguir define o fuso horário para o fuso horário **dos EUA/Pacífico** .

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação do MySQL para [funções de data e hora](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Passos seguintes

- [Bibliotecas de conexão para o banco de dados do Azure para MySQL](concepts-connection-libraries.md).
