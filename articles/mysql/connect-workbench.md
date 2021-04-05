---
title: 'Quickstart: Connect - MySQL Workbench - Azure Database for MySQL'
description: Este Guia de introdução disponibiliza os passos para utilizar o MySQL Workbench para se ligar e consultar dados da Base de Dados do Azure para MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 5e27cfec0a3f0a58c1e94a822e0c831f4efa1b32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94535542"
---
# <a name="quickstart-use-mysql-workbench-to-connect-and-query-data-in-azure-database-for-mysql"></a>Quickstart: Use a bancada mySQL workbench para ligar e consultar dados na Base de Dados Azure para o MySQL

Este guia de introdução explica como se pode ligar a uma Base de Dados do Azure para MySQL através de uma aplicação do MySQL Workbench.

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido utiliza os recursos criados em qualquer um desTes guias como ponto de partida:
- [Criar uma Base de Dados do Azure para o servidor MySQL com o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar uma Base de Dados do Azure para o servidor MySQL com a CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT] 
> Certifique-se de que o endereço IP de que está a ligar foi adicionado as regras de firewall do servidor utilizando o [portal Azure](./howto-manage-firewall-using-portal.md) ou [O CLI do Azure](./howto-manage-firewall-using-cli.md)

## <a name="install-mysql-workbench"></a>Instalar MySQL Workbench
Transfira e instale o MySQL Workbench no seu computador a partir do [site do MySQL](https://dev.mysql.com/downloads/workbench/).

## <a name="get-connection-information"></a>Obter informações da ligação
Obtenha as informações de ligação necessárias para se ligar à Base de Dados do Azure para MySQL. Necessita do nome do servidor e das credenciais de início de sessão totalmente qualificados.

1. Faça login no [portal Azure](https://portal.azure.com/).

2. No menu esquerdo do portal do Azure, clique em **Todos os recursos** e, em seguida, procure o servidor que acabou de criar, (por exemplo, **mydemoserver**).

3. Clique no nome do servidor.

4. No painel **Descrição geral** do servidor, tome nota do **Nome do servidor** e do **Nome de início de sessão de administrador do servidor**. Caso se esqueça da sua palavra-passe, também pode repor a palavra-passe neste painel.
 :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Nome do servidor da Base de Dados do Azure para o MySQL":::

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Ligar ao servidor com o MySQL Workbench 
Para ligar ao Servidor MySQL do Azure com a ferramenta da GUI MySQL Workbench:

1.    Inicie a aplicação MySQL Workbench no computador. 

2.    Na caixa de diálogo **Configurar Ligação Nova**, introduza as informações seguintes no separador **Parâmetros**:

:::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="configurar ligação nova":::

| **Definição** | **Valor sugerido** | **Descrição do campo** |
|---|---|---|
|    Nome da Ligação | Ligação de Demonstração | Especifique uma etiqueta para esta ligação. |
| Connection Method (Método de ligação) | Standard (TCP/IP) | Standard (TCP/IP) é suficiente. |
| Hostname (Nome do anfitrião) | *nome do servidor* | Especifique o valor de nome de servidor que foi utilizado quando criou a Base de Dados do Azure para MySQL anteriormente. O nosso servidor de exemplo mostrado é mydemoserver.mysql.database.azure.com. Utilize o nome de domínio completamente qualificado (\*.mysql.database.azure.com), conforme mostrado no exemplo. Siga os passos na secção anterior para obter as informações da ligação, se não se lembrar do nome do servidor.  |
| Porta | 3306 | Utilize sempre a porta 3306 para se ligar à Base de Dados do Azure para MySQL. |
| Nome de utilizador |  *nome de login de administrador do servidor* | Introduza o nome de utilizador de início de sessão de administrador do servidor que foi fornecido quando criou a Base de Dados do Azure para MySQL anteriormente. O nosso nome de utilizador de exemplo é myadmin@mydemoserver. Siga os passos na secção anterior para obter as informações da ligação, se não se lembrar do nome de utilizador. O formato é *nome de utilizador \@ servername*.
| Palavra-passe | a sua palavra-passe | Clique **em Loja no Cofre...** botão para guardar a senha. |

3.   Clique em **Testar Ligação** para testar se todos os parâmetros estão configurados corretamente. 

4.   Clique **em OK** para guardar a ligação. 

5.   Na listagem das **Ligações do MySQL**, clique no mosaico correspondente ao seu servidor e, em seguida, aguarde que a ligação seja estabelecida.

        É aberto um novo separador do SQL com um editor em branco, onde pode escrever as suas consultas.
    
        > [!NOTE]
        > Por predefinição, a segurança da ligação SSL é necessária e imposta no seu servidor da Base de Dados do Azure para MySQL. Apesar de, normalmente, não ser precisa nenhuma configuração adicional com certificados SSL para o MySQL Workbench ligar ao seu servidor, recomendamos vincular a certificação AC SSL com o MySQL Workbench. Para obter mais informações sobre como transferir e vincular a certificação, consulte [Configurar a conectividade SSL na sua aplicação para ligar em segurança à Base de Dados do Azure para MySQL](./howto-configure-ssl.md).  Se precisar de desativar o SSL, visite o portal do Azure e clique na página de segurança da Ligação para desativar o botão para ativar/desativar Impor ligação SLL.

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>Criar uma tabela, inserir dados, ler dados, atualizar dados, eliminar dados
1. Copie e cole o código de SQL de exemplo num separador de SQL em branco para ilustrar alguns dados de exemplo.

    Este código cria uma base de dados vazia com o nome quickstartdb e, em seguida, cria uma tabela de exemplo com o nome de inventário. Irá inserir algumas linhas e, em seguida, efetuar a respetiva leitura. Altera os dados com uma instrução de atualização e lê as linhas novamente. Por fim, elimina uma linha e lê as linhas novamente.
    
    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;
    
    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    
    -- Read
    SELECT * FROM inventory;
    
    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;
    
    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    A captura de ecrã mostra um exemplo do código de SQL no SQL Workbench e o resultado após ter sido executado.
    
    :::image type="content" source="media/connect-workbench/3-workbench-sql-tab.png" alt-text="Separador do SQL MySQL Workbench para executar o código do SQL de exemplo":::

2. Para executar o código do SQL de exemplo, clique no ícone de relâmpago na barra de ferramentas do separador **Ficheiro SQL**.
3. Repare nos três resultados em separadores na secção **Grelha de Resultado** no meio da página. 
4. Repare na lista de **Resultados** na parte inferior da página. É apresentado o estado de cada comando. 

Agora, ligou-se à Base de Dados do Azure para MySQL com o MySQL Workbench e consultou dados com linguagem do SQL.

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar todos os recursos utilizados durante este arranque rápido, elimine o grupo de recursos utilizando o seguinte comando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a base de dados com Exportar e Importar](./concepts-migrate-import-export.md)
