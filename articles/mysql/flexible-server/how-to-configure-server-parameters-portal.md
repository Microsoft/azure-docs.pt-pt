---
title: Configurar parâmetros de servidor - Portal Azure - Base de Dados Azure para O Servidor Flexível MySQL
description: Este artigo descreve como configurar os parâmetros do servidor MySQL na Base de Dados Azure para o servidor flexível MySQL utilizando o portal Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 618862e12bd62fbe37ef5e621c89babd7942c04b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105106960"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Configurar parâmetros de servidor na Base de Dados Azure para o MySQL - Servidor Flexível utilizando o portal Azure

> [!IMPORTANT] 
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Pode gerir a base de dados Azure para a configuração do Servidor Flexível MySQL utilizando parâmetros do servidor. Os parâmetros do servidor são configurados com o valor predefinido e recomendado quando cria o servidor.  

Este artigo descreve como visualizar e configurar os parâmetros do servidor utilizando o portal Azure. A lâmina do parâmetro do servidor no portal Azure mostra o parâmetro do servidor modificável e não modificável. Os parâmetros do servidor não modificáveis estão acinzentados.

>[!Note]
> Os parâmetros do servidor podem ser atualizados globalmente ao nível do servidor, utilizar o portal [Azure CLI](./how-to-configure-server-parameters-cli.md) ou [Azure](./how-to-configure-server-parameters-portal.md).

## <a name="configure-server-parameters"></a>Configurar parâmetros do servidor

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, localize a sua Base de Dados Azure para o servidor flexível MySQL.
2. Na secção **DEFINIÇÕES,** clique nos **parâmetros do Servidor** para abrir a página de parâmetros do servidor para o servidor flexível Azure Database for MySQL.
[:::image type="content" source="./media/how-to-server-parameters/azure-portal-server-parameters.png" alt-text="Página de parâmetros do servidor do portal Azure":::](./media/how-to-server-parameters/azure-portal-server-parameters.png#lightbox)
3. Localize qualquer parâmetro do servidor que necessite de ajustar. Reveja a coluna **Descrição** para compreender a finalidade e os valores permitidos.
[:::image type="content" source="./media/how-to-server-parameters/3-toggle-parameter.png" alt-text="Enumerar queda":::](./media/how-to-server-parameters/3-toggle-parameter.png#lightbox)
4. Clique  **em Guardar** para guardar as suas alterações.
[:::image type="content" source="./media/how-to-server-parameters/4-save-parameters.png" alt-text="Guardar ou descartar alterações":::](./media/how-to-server-parameters/4-save-parameters.png#lightbox)
5. Os parâmetros estáticos são os que exigem que o reboot do servidor entre em vigor. Se estiver a modificar o parâmetro estático, será solicitado para **reiniciar agora** ou **reiniciar mais tarde**.
[:::image type="content" source="./media/how-to-server-parameters/5-save-parameter.png" alt-text="Reiniciar na economia de parâmetros estáticos":::](./media/how-to-server-parameters/5-save-parameter.png#lightbox)
6. Se tiver guardado novos valores para os parâmetros, pode sempre reverter tudo de volta para os valores predefinidos selecionando **Reset all to predefinido**.
[:::image type="content" source="./media/how-to-server-parameters/6-reset-parameters.png" alt-text="Redefinir tudo para o padrão":::](./media/how-to-server-parameters/6-reset-parameters.png#lightbox)

## <a name="setting-non-modifiable-server-parameters"></a>Definição de parâmetros de servidor não modificáveis

Se o parâmetro do servidor que pretende atualizar não for modificável, pode configurar opcionalmente o parâmetro ao nível de ligação utilizando `init_connect` . Isto define os parâmetros do servidor para cada cliente que se conecta ao servidor. 

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
> Deve reiniciar o servidor para garantir que as tabelas de fuso horário estão devidamente povoadas.<!-- FIX ME To restart the server, use the [Azure portal](how-to-restart-server-portal.md) or [CLI](how-to-restart-server-cli.md).-->

Para visualizar os valores do fuso horário disponível, executar o seguinte comando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Definição do fuso horário de nível global

O fuso horário de nível global pode ser definido a partir da página de **parâmetros do Servidor** no portal Azure. O abaixo define o fuso horário global para o valor "EUA/Pacífico".

[:::image type="content" source="./media/how-to-server-parameters/timezone.png" alt-text="Definir parâmetro de fuso horário":::](./media/how-to-server-parameters/timezone.png#lightbox)

### <a name="setting-the-session-level-time-zone"></a>Definição do fuso horário do nível da sessão

O fuso horário de nível de sessão pode ser definido executando o `SET time_zone` comando a partir de uma ferramenta como a linha de comando MySQL ou a bancada mySQL Workbench. O exemplo abaixo define o fuso horário para o **fuso horário EUA/Pacífico.**

```sql
SET time_zone = 'US/Pacific';
```

Consulte a documentação mySQL para [funções de data e hora](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Passos seguintes

- Como configurar [os parâmetros do servidor no Azure CLI](./how-to-configure-server-parameters-cli.md)
