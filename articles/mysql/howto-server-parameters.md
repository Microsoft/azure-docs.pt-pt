---
title: Configurar parâmetros de servidor - Portal Azure - Base de Dados Azure para o MySQL
description: Este artigo descreve como configurar os parâmetros do servidor MySQL na Base de Dados Azure para o MySQL utilizando o portal Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/11/2020
ms.openlocfilehash: a37fbee4361d4a87c43a42cae66c425eba1e0877
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90887043"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Configurar parâmetros de servidor na Base de Dados Azure para o MySQL utilizando o portal Azure

A Azure Database for MySQL suporta a configuração de alguns parâmetros do servidor. Este artigo descreve como configurar estes parâmetros utilizando o portal Azure. Nem todos os parâmetros do servidor podem ser ajustados.

## <a name="configure-server-parameters"></a>Configurar parâmetros do servidor

1. Inscreva-se no portal Azure e, em seguida, localize a sua Base de Dados Azure para o servidor MySQL.
2. Na secção **DEFINIÇÕES,** clique nos **parâmetros do Servidor** para abrir a página de parâmetros do servidor para o servidor Azure Database for MySQL.
:::image type="content" source="./media/howto-server-parameters/auzre-portal-server-parameters.png" alt-text="Página de parâmetros do servidor do portal Azure":::
3. Localize as definições necessárias para ajustar. Reveja a coluna **Descrição** para compreender a finalidade e os valores permitidos.
:::image type="content" source="./media/howto-server-parameters/3-toggle_parameter.png" alt-text="Enumerar queda":::
4. Clique  **em Guardar** para guardar as suas alterações.
:::image type="content" source="./media/howto-server-parameters/4-save_parameters.png" alt-text="Guardar ou descartar alterações":::
5. Se tiver guardado novos valores para os parâmetros, pode sempre reverter tudo de volta para os valores predefinidos selecionando **Reset all to predefinido**.
:::image type="content" source="./media/howto-server-parameters/5-reset_parameters.png" alt-text="Redefinir tudo para o padrão":::

## <a name="setting-parameters-not-listed"></a>Definição de parâmetros não listados

Se o parâmetro do servidor que pretende atualizar não estiver listado no portal Azure, pode configurar opcionalmente o parâmetro ao nível de ligação utilizando `init_connect` . Isto define os parâmetros do servidor para cada cliente que se conecta ao servidor. 

1. Na secção **DEFINIÇÕES,** clique nos **parâmetros do Servidor** para abrir a página de parâmetros do servidor para o servidor Azure Database for MySQL.
2. Pesquisar `init_connect`
3. Adicione os parâmetros do servidor no formato: `SET parameter_name=YOUR_DESIRED_VALUE` em valor a coluna de valor.

    Por exemplo, pode alterar o conjunto de caracteres do seu servidor definindo `init_connect` para `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Clique em **Guardar** para guardar as alterações.

>[!Note]
> Pode utilizar `init_connect` para alterar parâmetros que não exigem SUPER privilégio(s) ao nível da sessão. Para verificar se pode definir o parâmetro através de `init_connect`, execute o comando `set session parameter_name=YOUR_DESIRED_VALUE;` e se tiver erros como **Acesso negado; precisa de SUPER privilégio(s)** , não pode definir o parâmetro com “init_connect”.

## <a name="working-with-the-time-zone-parameter"></a>Trabalhar com o parâmetro do fuso horário

### <a name="populating-the-time-zone-tables"></a>Povoando as tabelas do fuso horário

As tabelas de fuso horário do seu servidor podem ser povoadas chamando o `mysql.az_load_timezone` procedimento armazenado a partir de uma ferramenta como a linha de comando MySQL ou a bancada mySQL Workbench.

> [!NOTE]
> Se estiver a executar o `mysql.az_load_timezone` comando a partir da bancada MySQL Workbench, poderá ter de desligar o modo de atualização segura primeiro utilizando `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Deve reiniciar o servidor para garantir que as tabelas de fuso horário estão devidamente povoadas. Para reiniciar o servidor, utilize o [portal Azure](howto-restart-server-portal.md) ou [CLI](howto-restart-server-cli.md).

Para visualizar os valores do fuso horário disponível, executar o seguinte comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Definição do fuso horário de nível global

O fuso horário de nível global pode ser definido a partir da página de **parâmetros do Servidor** no portal Azure. O abaixo define o fuso horário global para o valor "EUA/Pacífico".

:::image type="content" source="./media/howto-server-parameters/timezone.png" alt-text="Definir parâmetro de fuso horário":::

### <a name="setting-the-session-level-time-zone"></a>Definição do fuso horário do nível da sessão

O fuso horário de nível de sessão pode ser definido executando o `SET time_zone` comando a partir de uma ferramenta como a linha de comando MySQL ou a bancada mySQL Workbench. O exemplo abaixo define o fuso horário para o **fuso horário EUA/Pacífico.**

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação mySQL para [funções de data e hora](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Passos seguintes

- [Bibliotecas de conexão para Azure Database para MySQL](concepts-connection-libraries.md).
