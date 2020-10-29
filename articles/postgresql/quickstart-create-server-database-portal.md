---
title: 'Quickstart: Criar servidor - Portal Azure - Base de Dados Azure para PostgreSQL - servidor único'
description: Neste guia de arranque rápido, irá criar e gerir uma Base de Dados Azure para servidor PostgreSQL utilizando o portal Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 42e15da2fd31d163bc8822a347101704b27e1222
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913421"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Quickstart: Criar uma base de dados Azure para servidor PostgreSQL utilizando o portal Azure

A Base de Dados do Azure para o PostgreSQL é um serviço gerido com o qual pode executar, gerir e dimensionar as bases de dados de elevada disponibilidade do PostgreSQL na cloud. Este quickstart mostra-lhe como criar uma única Base de Dados Azure para servidor PostgreSQL e ligar-se a ele.

## <a name="prerequisites"></a>Pré-requisitos
É necessária uma subscrição Azure. Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um servidor da Base de Dados do Azure para PostgreSQL
Vá ao [portal Azure](https://portal.azure.com/) para criar uma base de dados Azure para a base de dados de servidor único postgreSQL. Procure e selecione *Azure Database para servidores PostgreSQL* .

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/search-postgres.png" alt-text="Encontre a base de dados Azure para postgreSQL.":::

1. Selecione **Adicionar** .

2. Na base de dados De Azure para a página PostgreSQL , selecione  **Servidor Único** .

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/select-single-server.png" alt-text="Encontre a base de dados Azure para postgreSQL.":::

3. Agora introduza o formulário Basics com as **seguintes** informações.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Encontre a base de dados Azure para postgreSQL.":::

   |Definição|Valor sugerido|Descrição|
   |:---|:---|:---|
   |Subscrição|seu nome de assinatura|selecione a subscrição Azure desejada.|
   |Grupo de recursos|*grupo myresource*| Um novo ou um grupo de recursos existente da sua subscrição.|
   |Nome do servidor |*mydemoserver*|Um nome exclusivo que identifique a sua Base de Dados do Azure para o servidor PostgreSQL. O nome de domínio *postgres.database.azure.com* é anexado ao nome do servidor que fornece. O servidor só pode conter letras minúsculas, números e o caráter de hífen (-). Deve conter 3 a 63 caracteres.|
   |Origem de dados | Nenhum | Selecione **Nenhum** para criar um novo servidor de raiz. Selecione **Backup** apenas se estiver a restaurar a partir de um geo-backup de um servidor existente.|
   |Nome de utilizador de administrador |*myadmin*| Insira o nome de utilizador do seu administrador de serviço. Não pode começar com **pg_** e estes valores não são permitidos: **azure_superuser** , **azure_pg_admin,** **administrador,** **administrador,** **raiz,** **hóspede,** ou **público.**|
   |Palavra-passe |a sua palavra-passe| Uma nova palavra-passe para o utilizador de administração do servidor. Deve conter 8 a 128 caracteres de três das seguintes categorias: letras maiúsculas inglesas, letras minúsculas inglesas, números (0 a 9) e caracteres não alfanuméricos (por exemplo, !, $, #, %).|
   |Localização|sua localização desejada| Selecione uma localização da lista de dropdown.|
   |Versão|A versão principal mais recente| A versão principal mais recente do PostgreSQL, a não ser que tenha requisitos específicos.|
   |Computação e armazenamento | *usar os predefinidos*| O nível de preços predefinidos é **Finalidade Geral** com **4 vCores** e armazenamento **de 100 GB.** A retenção de backup está definida para **7 dias** com a opção de backup **geograficamente redundante.**<br/>Saiba mais sobre os [preços](https://azure.microsoft.com/pricing/details/postgresql/server/) e atualize os predefinidos se necessário.|


   > [!NOTE]
   > Considere usar o nível de preços básico se o cálculo leve e a I/O forem adequados para a sua carga de trabalho. Note que os servidores criados no nível de preços básicos não podem ser posteriormente dimensionados para Final Geral ou Memória Otimizada.

5. Selecione **Review + crie** para rever as suas seleções. Selecione **Criar** para aprovisionar o servidor. Esta operação pode demorar alguns minutos.
    > [!NOTE]
    > Uma base de dados vazia, **postgres,** é criada. Também encontrará uma base **de dados azure_maintenance** que é usada para separar os processos de serviço geridos das ações do utilizador. Não pode aceder à base **de dados azure_maintenance.**

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/deployment-success.png" alt-text="Encontre a base de dados Azure para postgreSQL.":::

[Tendo problemas? Deixe-nos saber.](https://aka.ms/postgres-doc-feedback)

## <a name="configure-a-firewall-rule"></a>Configurar uma regra de firewall
Por predefinição, o servidor que cria não é acessível ao público. Tem de dar permissões ao seu endereço IP. Aceda ao recurso do seu servidor no portal Azure e selecione a segurança de **ligação** do menu do lado esquerdo para o seu recurso de servidor. Se não sabe como encontrar o seu recurso, consulte os [recursos abertos.](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources)

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="Encontre a base de dados Azure para postgreSQL.":::

**Selecione Adicionar o endereço IP do cliente atual** e, em seguida, selecione **Guardar** . Pode adicionar mais endereços IP ou fornecer uma gama IP para ligar ao seu servidor a partir desses endereços IP. Para obter mais informações, consulte [as regras de Firewall na Base de Dados Azure para PostgreSQL](./concepts-firewall-rules.md).

> [!NOTE]
> Para evitar problemas de conectividade, verifique se a sua rede permite o tráfego de saída sobre a porta 5432. A base de dados Azure para PostgreSQL utiliza esta porta.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/postgres-doc-feedback)

## <a name="connect-to-the-server-with-psql"></a>Conecte-se ao servidor com o psql

Você pode usar [psql](http://postgresguide.com/utilities/psql.html) ou [pgAdmin,](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html)que são clientes postgreSQL populares. Para este arranque rápido, vamos conectar-nos usando psql em [Azure Cloud Shell](../cloud-shell/overview.md) dentro do portal Azure.

1. Tome nota do nome do seu servidor, nome de login de administrador do servidor, palavra-passe e ID de subscrição para o seu servidor recém-criado a partir da secção **visão geral** do seu servidor.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/overview-new.png" alt-text="Encontre a base de dados Azure para postgreSQL.":::


2. Abra o Azure Cloud Shell no portal selecionando o ícone no lado superior esquerdo.

   > [!NOTE]
   > Se estiver a abrir a Cloud Shell pela primeira vez, verá um pedido para criar um grupo de recursos e uma conta de armazenamento. Este é um passo único e será automaticamente anexado para todas as sessões.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="Encontre a base de dados Azure para postgreSQL.":::

3. Executar o seguinte comando no terminal Azure Cloud Shell. Substitua os valores pelo nome real do servidor e pelo nome de início de sessão do utilizador administrativo. Utilize as **postagens vazias** da base de dados com o utilizador administrativo neste formato: `<admin-username>@<servername>` .

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   Eis como a experiência parece no terminal Cloud Shell:

   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. No mesmo terminal Azure Cloud Shell, crie uma base de dados chamada **hóspede.**

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Altere as ligações para a base de dados **de hóspedes** recém-criada.

   ```bash
   \c guest
   ```
6. Digite `\q` , e, em seguida, selecione a tecla Enter para fechar o psql.

[Tendo problemas? Deixe-nos saber.](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Limpar recursos
Criou com sucesso uma Base de Dados Azure para servidor PostgreSQL num grupo de recursos. Se não espera precisar destes recursos no futuro, pode eliminá-los eliminando o grupo de recursos ou o servidor PostgreSQL.

Para eliminar o grupo de recursos:

1. No portal Azure, procure e selecione **grupos de Recursos.**
2. Na lista de grupos de recursos, escolha o nome do seu grupo de recursos.
3. Na página **'Visão Geral'** do seu grupo de recursos, selecione **Delete resource group** .
4. Na caixa de diálogo de confirmação, insira o nome do seu grupo de recursos e, em seguida, selecione **Delete** .

Para eliminar o servidor, selecione o botão **Eliminar** na página **'Vista Geral'** do seu servidor:

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="Encontre a base de dados Azure para postgreSQL.":::

[Tendo problemas? Deixe-nos saber.](https://aka.ms/postgres-doc-feedback)

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a sua base de dados utilizando exportação e importação](./howto-migrate-using-export-and-import.md) <br/>

> [!div class="nextstepaction"]
> [Estruturar uma base de dados](./tutorial-design-database-using-azure-portal.md#create-tables-in-the-database)

[Não consegue encontrar o que procura? Deixe-nos saber.](https://aka.ms/postgres-doc-feedback)
