---
title: Configure parâmetros de servidor - Portal Azure - Base de Dados Azure para MySQL
description: Este artigo descreve como configurar os parâmetros do servidor MySQL na Base de Dados Azure para MySQL utilizando o portal Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 715f1028597d76915d833b0ade66bc03d939030d
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546452"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Como configurar parâmetros de servidor em Base de Dados Azure para MySQL utilizando o portal Azure

A Base de Dados Azure para MySQL suporta a configuração de alguns parâmetros do servidor. Este artigo descreve como configurar estes parâmetros utilizando o portal Azure. Nem todos os parâmetros do servidor podem ser ajustados.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navegue para os parâmetros do servidor no portal Azure

1. Inscreva-se no portal Azure e, em seguida, localize a sua Base de Dados Azure para o servidor MySQL.
2. Na secção **DEFINIÇÕES,** clique nos **parâmetros do Servidor** para abrir a página de parâmetros do servidor para a Base de Dados Azure para o servidor MySQL.
![Página de parâmetros do servidor do portal Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Localize quaisquer definições que precise de ajustar. Reveja a coluna **Descrição** para compreender o propósito e os valores permitidos.
![Enumerar a queda](./media/howto-server-parameters/3-toggle_parameter.png)
4. Clique em **Guardar** para guardar as suas alterações.
![Guardar ou Descartar alterações](./media/howto-server-parameters/4-save_parameters.png)
5. Se guardou novos valores para os parâmetros, pode sempre reverter tudo de volta para os valores predefinidos selecionando **tudo para padrão**.
![Redefinir tudo para padrão](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista de parâmetros de servidor configuráveis

A lista de parâmetros de servidor suportados está em constante crescimento. Utilize o separador de parâmetros do servidor no portal Azure para obter a definição e configurar os parâmetros do servidor com base nos requisitos da sua aplicação.

## <a name="non-configurable-server-parameters"></a>Parâmetros de servidor não configuráveis

O tamanho do Pool Tampão InnoDB não é configurável e está ligado ao seu [nível de preços](concepts-service-tiers.md).

|**Nível de Preços**|**vCore(s)**|**Tamanho do Pool Tampão <br>InnoDB em MB (servidores que suportam até 4 TB de armazenamento)**| **Tamanho do Pool Tampão <br>InnoDB em MB (servidores que suportam até 16 TB de armazenamento)**|
|:---|---:|---:|---:|
|Básico| 1| 832| |
|Básico| 2| 2560| |
|Fins Gerais| 2| 3584| 7168|
|Fins Gerais| 4| 7680| 15360|
|Fins Gerais| 8| 15360| 30720|
|Fins Gerais| 16| 31232| 62464|
|Fins Gerais| 32| 62976| 125952|
|Fins Gerais| 64| 125952| 251904|
|Otimizada para Memória| 2| 7168| 14336|
|Otimizada para Memória| 4| 15360| 30720|
|Otimizada para Memória| 8| 30720| 61440|
|Otimizada para Memória| 16| 62464| 124928|
|Otimizada para Memória| 32| 125952| 251904|

Estes parâmetros adicionais do servidor não são configuráveis no sistema:

|**Parâmetro**|**Valor fixo**|
| :------------------------ | :-------- |
|innodb_file_per_table no nível básico|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

Outros parâmetros do servidor que não estão listados aqui estão definidos para os seus valores padrão fora da caixa MySQL para as versões [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) e [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

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

Consulte a documentação mySQL para funções de [data e hora.](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)

## <a name="next-steps"></a>Passos seguintes

- [Bibliotecas de ligação para base de dados Azure para MySQL](concepts-connection-libraries.md).
