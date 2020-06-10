---
title: Configurar parâmetros de servidor - Portal Azure - Base de Dados Azure para MariaDB
description: Este artigo descreve como configurar os parâmetros do servidor MariaDB na Base de Dados Azure para MariaDB utilizando o portal Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: 674ae5c60b7e897f43d28f5813641ddc833b3002
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636075"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Como configurar os parâmetros do servidor na Base de Dados Azure para MariaDB utilizando o portal Azure

A Base de Dados Azure para MariaDB suporta a configuração de alguns parâmetros do servidor. Este artigo descreve como configurar estes parâmetros utilizando o portal Azure. Nem todos os parâmetros do servidor podem ser ajustados.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navegue para parâmetros do servidor no portal Azure

1. Inscreva-se no portal Azure e, em seguida, localize a sua Base de Dados Azure para o servidor MariaDB.
2. Na secção **DEFINIÇÕES,** clique nos **parâmetros do Servidor** para abrir a página de parâmetros do servidor para o servidor Azure Database for MariaDB.
![Página de parâmetros do servidor do portal Azure](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Localize as definições necessárias para ajustar. Reveja a coluna **Descrição** para compreender a finalidade e os valores permitidos.
![Enumerar queda](./media/howto-server-parameters/3-toggle_parameter.png)
4. Clique **em Guardar** para guardar as suas alterações.
![Guardar ou descartar alterações](./media/howto-server-parameters/4-save_parameters.png)
5. Se tiver guardado novos valores para os parâmetros, pode sempre reverter tudo de volta para os valores predefinidos selecionando **Reset all to predefinido**.
![Redefinir tudo para o padrão](./media/howto-server-parameters/5-reset_parameters.png)

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

![Definir parâmetro de fuso horário](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Definição do fuso horário do nível da sessão

O fuso horário de nível de sessão pode ser definido executando o `SET time_zone` comando a partir de uma ferramenta como a linha de comando MySQL ou a bancada mySQL Workbench. O exemplo abaixo define o fuso horário para o **fuso horário EUA/Pacífico.**

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação MariaDB para [funções de data e hora](https://mariadb.com/kb/en/library/convert_tz/).

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [os parâmetros do servidor](concepts-server-parameters.md)