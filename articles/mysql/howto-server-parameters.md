---
title: Configure parâmetros de servidor - Portal Azure - Base de Dados Azure para MySQL
description: Este artigo descreve como configurar parâmetros do servidor MySQL na base de dados do Azure para MySQL com o portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 8ec6f32d7db0161cef00330aa38601ba9bdb309d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392702"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Como configurar os parâmetros do servidor na base de dados do Azure para MySQL com o portal do Azure

Base de dados do Azure para MySQL suporta a configuração de alguns parâmetros de servidor. Este artigo descreve como configurar esses parâmetros com o portal do Azure. Nem todos os parâmetros do servidor podem ser ajustados.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navegue para os parâmetros do servidor no portal do Azure

1. Inicie sessão no portal do Azure, em seguida, localize a base de dados do Azure para o servidor MySQL.
2. Na secção **DEFINIÇÕES,** clique nos **parâmetros do Servidor** para abrir a página de parâmetros do servidor para a Base de Dados Azure para o servidor MySQL.
![portal do portal](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Localize quaisquer definições que precisa de ajustar. Reveja a coluna **Descrição** para compreender o propósito e os valores permitidos.
![Enumerar cair](./media/howto-server-parameters/3-toggle_parameter.png)
4. Clique em **Guardar** para guardar as suas alterações.
![Guardar ou Descartar alterações](./media/howto-server-parameters/4-save_parameters.png)
5. Se guardou novos valores para os parâmetros, pode sempre reverter tudo de volta para os valores predefinidos selecionando **tudo para padrão**.
![Redefinir tudo para](./media/howto-server-parameters/5-reset_parameters.png) padrão

## <a name="list-of-configurable-server-parameters"></a>Lista de parâmetros do servidor configurável

A lista de parâmetros de servidor com suporte está constantemente a crescer. Utilize o separador de parâmetros de servidor no portal do Azure para obter a definição e configurar os parâmetros do servidor com base nos requisitos da sua aplicação.

## <a name="non-configurable-server-parameters"></a>Parâmetros do servidor não configuráveis

O tamanho do Pool Tampão InnoDB não é configurável e está ligado ao seu [nível de preços](concepts-service-tiers.md).

|**Escalão de Preço**|**vCore(s)**|**Tamanho do Pool Tampão InnoDB em MB <br>(servidores que suportam até 4 TB de armazenamento)**| **Tamanho do Pool Tampão InnoDB em MB <br>(servidores que suportam até 16 TB de armazenamento)**|
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

|**Parâmetro**|**Valor fixo**|
| :------------------------ | :-------- |
|innodb_file_per_table no escalão básico|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

Outros parâmetros do servidor que não estão listados aqui estão definidos para os seus valores padrão fora da caixa MySQL para as versões [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) e [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="working-with-the-time-zone-parameter"></a>Trabalhar com o parâmetro de fuso horário

### <a name="populating-the-time-zone-tables"></a>Preencher as tabelas de fuso horário

As tabelas de fuso horário no seu servidor podem ser povoadas ligando para o `az_load_timezone` procedimento armazenado a partir de uma ferramenta como a linha de comando MySQL ou a bancada de trabalho MySQL.

> [!NOTE]
> Se estiver a executar o comando `az_load_timezone` da Bancada de Trabalho MySQL, poderá ter de desligar primeiro o modo de atualização seguro utilizando `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Deve reiniciar o servidor para garantir que as tabelas de fuso horário estão corretamente povoadas. Para reiniciar o servidor, utilize o [portal Azure](howto-restart-server-portal.md) ou [o CLI](howto-restart-server-cli.md).

Para ver os valores de fuso horário disponível, execute o seguinte comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Definir o fuso de horário de nível global

O fuso horário de nível global pode ser definido a partir da página de **parâmetros do Servidor** no portal Azure. A seguir define o fuso horário global para o valor "E.U.A. / Pacífico".

![Parâmetro de fuso horário do conjunto](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Definir o fuso de horário de nível de sessão

O fuso horário de nível de sessão pode ser definido executando o comando `SET time_zone` a partir de uma ferramenta como a linha de comando MySQL ou a bancada de trabalho MySQL. O exemplo abaixo define o fuso horário para o fuso horário **EUA/Pacífico.**

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação mySQL para funções de [data e hora.](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)

## <a name="next-steps"></a>Passos seguintes

- [Bibliotecas de ligação para base de dados Azure para MySQL](concepts-connection-libraries.md).
