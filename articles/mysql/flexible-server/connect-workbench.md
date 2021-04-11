---
title: 'Quickstart: Connect - MySQL Workbench - Azure Database for MySQL - Servidor Flexível'
description: Este Quickstart fornece os passos para utilizar a bancada de trabalho MySQL para ligar e consultar dados da Base de Dados Azure para o MySQL - Servidor Flexível.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: 95ecb64165075a7effe0c6eaf568ee172ad67b9b
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226299"
---
# <a name="quickstart-use-mysql-workbench-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server-preview"></a>Quickstart: Use a bancada mySQL workbench para ligar e consultar dados na Base de Dados Azure para MySQL - Servidor Flexível (Pré-visualização)


> [!IMPORTANT] 
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Este quickstart demonstra como ligar-se a uma Base de Dados Azure para o MySQL Flexible Server utilizando a aplicação MySQL Workbench.

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido utiliza os recursos criados em qualquer um desTes guias como ponto de partida:

- [Criar uma base de dados Azure para o MySQL Flexible Server utilizando o portal Azure](./quickstart-create-server-portal.md)
- [Criar uma base de dados Azure para o MySQL Flexible Server utilizando o Azure CLI](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>Preparar a estação de trabalho do seu cliente
- Se criou o seu servidor flexível com *acesso privado (VNet Integration)*, terá de se ligar ao seu servidor a partir de um recurso dentro do mesmo VNet que o seu servidor. Pode criar uma máquina virtual e adicioná-la ao VNet criado com o seu servidor flexível. Consulte a [Criação e gestão da Base de Dados Azure para a rede virtual MySQL Flexible Server utilizando o Azure CLI](./how-to-manage-virtual-network-cli.md).
- Se criou o seu servidor flexível com *acesso público (endereços IP autorizados)*, pode adicionar o seu endereço IP local à lista de regras de firewall no seu servidor. Consulte para [criar e gerir a Base de Dados Azure para as regras de firewall do Servidor Flexível MySQL utilizando o Azure CLI](./how-to-manage-firewall-cli.md).

- Transfira e instale o MySQL Workbench no seu computador a partir do [site do MySQL](https://dev.mysql.com/downloads/workbench/).

## <a name="get-connection-information"></a>Obter informações da ligação

Obtenha as informações de ligação necessárias para ligar ao servidor flexível. Precisa do nome do servidor totalmente qualificado e assinar em credenciais.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, procure o servidor que criou (como o **mydemoserver).**
3. Selecione o nome do servidor.
4. No painel **Descrição geral** do servidor, tome nota do **Nome do servidor** e do **Nome de início de sessão de administrador do servidor**. Caso se esqueça da sua palavra-passe, também pode repor a palavra-passe neste painel.
<!--- :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connect-to-the-server-using-mysql-workbench"></a>Conecte-se ao servidor utilizando a bancada mySQL Workbench

Para ligar à Base de Dados Azure para o MySQL Flexible Server utilizando a bancada mySQL Workbench:

1. Inicie a aplicação MySQL Workbench no computador.

2. Na caixa de diálogo **Configurar Ligação Nova**, introduza as informações seguintes no separador **Parâmetros**:

    :::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="configurar ligação nova":::

    | **Parâmetros** | **Valor sugerido** | **Descrição do campo** |
    |---|---|---|
    |    Nome da Ligação | Ligação de Demonstração | Especifique uma etiqueta para esta ligação. |
    | Connection Method (Método de ligação) | Standard (TCP/IP) | Standard (TCP/IP) é suficiente. |
    | Hostname (Nome do anfitrião) | *nome do servidor* | Especifique o valor de nome de servidor que foi utilizado quando criou a Base de Dados do Azure para MySQL anteriormente. O nosso servidor de exemplo mostrado é mydemoserver.mysql.database.azure.com. Utilize o nome de domínio completamente qualificado (\*.mysql.database.azure.com), conforme mostrado no exemplo. Siga os passos na secção anterior para obter as informações da ligação, se não se lembrar do nome do servidor.  |
    | Porta | 3306 | Utilize sempre a porta 3306 para se ligar à Base de Dados do Azure para MySQL. |
    | Nome de utilizador |  *nome de login de administrador do servidor* | Introduza o nome de utilizador de início de sessão de administrador do servidor que foi fornecido quando criou a Base de Dados do Azure para MySQL anteriormente. O nosso nome de utilizador exemplo é myadmin. Siga os passos na secção anterior para obter as informações da ligação, se não se lembrar do nome de utilizador.
    | Palavra-passe | a sua palavra-passe | Clique **em Loja no Cofre...** botão para guardar a senha. |

3. Clique em **Testar Ligação** para testar se todos os parâmetros estão configurados corretamente.

4. Clique **em OK** para guardar a ligação.

5. Na listagem das **Ligações do MySQL**, clique no mosaico correspondente ao seu servidor e, em seguida, aguarde que a ligação seja estabelecida.

    É aberto um novo separador do SQL com um editor em branco, onde pode escrever as suas consultas.

> [!NOTE]
> A ligação encriptada utilizando o TLS 1.2 é necessária e executada na sua Base de Dados Azure para o MySQL Flexible Server. Embora normalmente não seja necessária nenhuma configuração adicional com certificados TLS/SSL para que o MySQL Workbench se conecte ao seu servidor, recomendamos que se ligue a certificação TLS/SSL CA com a bancada MySQL Workbench. Para obter mais informações, consulte [a ligação utilizando tls/SSL](./how-to-connect-tls-ssl.md)

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

    :::image type="content" source="./media/connect-workbench/3-workbench-sql-tab.png" alt-text="Separador do SQL MySQL Workbench para executar o código do SQL de exemplo":::

2. Para executar o código do SQL de exemplo, clique no ícone de relâmpago na barra de ferramentas do separador **Ficheiro SQL**.
3. Repare nos três resultados em separadores na secção **Grelha de Resultado** no meio da página.
4. Repare na lista de **Resultados** na parte inferior da página. É apresentado o estado de cada comando.

Agora, ligou-se à Base de Dados Azure para o MySQL Flexible Server utilizando a bancada mySQL Workbench, e já questionou dados utilizando o idioma SQL.

## <a name="next-steps"></a>Passos seguintes
- [Conectividade encriptada utilizando a Segurança da Camada de Transporte (TLS 1.2) na Base de Dados Azure para o MySQL - Servidor Flexível](./how-to-connect-tls-ssl.md).
- Saiba mais sobre [networking na Base de Dados Azure para o MySQL Flexible Server](./concepts-networking.md).
- [Crie e gere a base de dados Azure para as regras de firewall do Servidor Flexível MySQL utilizando o portal Azure](./how-to-manage-firewall-portal.md).
- [Crie e gere a base de dados Azure para a rede virtual mySQL Flexible Server utilizando o portal Azure](./how-to-manage-virtual-network-portal.md).
