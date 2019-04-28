---
title: Como configurar parâmetros do servidor na base de dados do Azure para MySQL
description: Este artigo descreve como configurar parâmetros do servidor MySQL na base de dados do Azure para MySQL com o portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 103e09a0e2b9dd409fa2ddaff1c5311ef9936d22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61422172"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Como configurar os parâmetros do servidor na base de dados do Azure para MySQL com o portal do Azure

Base de dados do Azure para MySQL suporta a configuração de alguns parâmetros de servidor. Este artigo descreve como configurar esses parâmetros com o portal do Azure. Nem todos os parâmetros do servidor podem ser ajustados.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navegue para os parâmetros do servidor no portal do Azure

1. Inicie sessão no portal do Azure, em seguida, localize a base de dados do Azure para o servidor MySQL.
2. Sob o **configurações** secção, clique em **parâmetros do servidor** para abrir a página de parâmetros do servidor da base de dados do Azure para o servidor MySQL.
![Página de parâmetros do servidor do portal do Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Localize quaisquer definições que precisa de ajustar. Reveja os **Descrição** coluna para compreender a finalidade e os valores permitidos.
![Enumerar para baixo](./media/howto-server-parameters/3-toggle_parameter.png)
4. Clique em **guardar** para guardar as alterações.
![Guardar ou alterações de rejeição](./media/howto-server-parameters/4-save_parameters.png)
5. Se guardar novos valores para os parâmetros, poderá sempre reverter tudo volta para os valores predefinidos, selecionando **repor tudo para predefinição**.
![Repor tudo para predefinição](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista de parâmetros do servidor configurável

A lista de parâmetros de servidor com suporte está constantemente a crescer. Utilize o separador de parâmetros de servidor no portal do Azure para obter a definição e configurar os parâmetros do servidor com base nos requisitos da sua aplicação.

## <a name="non-configurable-server-parameters"></a>Parâmetros do servidor não configuráveis

Conjunto de memória intermédia de InnoDB e máx. ligações não são configuráveis e empatados para sua [escalão de preço](concepts-service-tiers.md).

|**Escalão de Preço**| **Geração de computação**|**vCore(s)**|**Pool de buffers de InnoDB (MB)**| **Máx. ligações**|
|---|---|---|---|--|
|Básica| Geração 4| 1| 960| 50|
|Básica| Geração 4| 2| 2560| 100|
|Básica| Geração 5| 1| 960| 50|
|Básica| Geração 5| 2| 2560| 100|
|Fins Gerais| Geração 4| 2| 3584| 300|
|Fins Gerais| Geração 4| 4| 7680| 625|
|Fins Gerais| Geração 4| 8| 15360| 1250|
|Fins Gerais| Geração 4| 16| 31232| 2500|
|Fins Gerais| Geração 4| 32| 62976| 5000|
|Fins Gerais| Geração 5| 2| 3584| 300|
|Fins Gerais| Geração 5| 4| 7680| 625|
|Fins Gerais| Geração 5| 8| 15360| 1250|
|Fins Gerais| Geração 5| 16| 31232| 2500|
|Fins Gerais| Geração 5| 32| 62976| 5000|
|Fins Gerais| Geração 5| 64| 125952| 10000|
|Memória Otimizada| Geração 5| 2| 7168| 600|
|Memória Otimizada| Geração 5| 4| 15360| 1250|
|Memória Otimizada| Geração 5| 8| 30720| 2500|
|Memória Otimizada| Geração 5| 16| 62464| 5000|
|Memória Otimizada| Geração 5| 32| 125952| 10000|

Esses parâmetros de servidor adicionais não são configuráveis no sistema:

|**Parâmetro**|**Valor fixo**|
| :------------------------ | :-------- |
|innodb_file_per_table no escalão básico|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

Outros parâmetros de servidor que não estão listados aqui são definidos nos valores predefinidos de out-of-box MySQL para versões [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) e [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="working-with-the-time-zone-parameter"></a>Trabalhar com o parâmetro de fuso horário

### <a name="populating-the-time-zone-tables"></a>Preencher as tabelas de fuso horário

As tabelas de fuso horário no seu servidor podem ser preenchidas chamando o `az_load_timezone` procedimento armazenado a partir de uma ferramenta como a linha de comandos MySQL ou o MySQL Workbench.

> [!NOTE]
> Se estiver a executar o `az_load_timezone` comando a partir do MySQL Workbench, poderá ter de desativar o modo de atualização segura primeiro usando `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

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

Consulte a documentação do MySQL para [funções de tempo de data e](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Passos Seguintes

- [Bibliotecas de ligação para base de dados do Azure para MySQL](concepts-connection-libraries.md).
