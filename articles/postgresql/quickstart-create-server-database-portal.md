---
title: 'Quickstart: Criar servidor - Portal Azure - Base de Dados Azure para PostgreSQL - servidor único'
description: Neste guia de arranque rápido, irá criar e gerir uma Base de Dados Azure para servidor PostgreSQL utilizando o portal Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/27/2020
ms.openlocfilehash: 2c340f9e7d2c44082adc67eeb816276449602035
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92484496"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Quickstart: Criar uma base de dados Azure para servidor PostgreSQL utilizando o portal Azure

A Base de Dados do Azure para o PostgreSQL é um serviço gerido com o qual pode executar, gerir e dimensionar as bases de dados de elevada disponibilidade do PostgreSQL na cloud. Este quickstart mostra-lhe como criar uma única Base de Dados Azure para o servidor PostgreSQL em cerca de cinco minutos, utilizando o portal Azure.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Abra o browser e aceda ao [portal do Azure](https://portal.azure.com/). Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um servidor da Base de Dados do Azure para PostgreSQL

É criado um servidor da Base de Dados do Azure para PostgreSQL com um conjunto de [recursos de armazenamento e computação](./concepts-pricing-tiers.md) configurado. O servidor é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md).

Para criar uma base de dados Azure para o servidor PostgreSQL:

1. **Selecione Criar um recurso** no canto superior esquerdo do portal.

2. Selecione **Base de Dados**  >  **Azure Databases para PostgreSQL**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="Screenshot que mostra Azure Database para PostgreSQL no menu.":::

3. Selecione a opção de implementação **do servidor único.**

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/select-deployment-option.png" alt-text="Screenshot que mostra Azure Database para PostgreSQL no menu.":::

4. Preencha o formulário Basics com as **seguintes** informações.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Screenshot que mostra Azure Database para PostgreSQL no menu.":::

   Definição|Valor sugerido|Descrição
   ---|---|---
   Subscrição|Nome da sua subscrição|A subscrição do Azure que quer utilizar para o servidor. Se tiver várias subscrições, escolha a subscrição na qual o recurso é cobrado.
   Grupo de recursos|*grupo myresource*| Um nome de grupo de recursos novo ou um já existente na sua subscrição.
   Nome do servidor |*mydemoserver*|Um nome exclusivo que identifique a sua Base de Dados do Azure para o servidor PostgreSQL. O nome de domínio *postgres.database.azure.com* é anexado ao nome do servidor que fornece. O servidor só pode conter letras minúsculas, números e o caráter de hífen (-). Deve conter 3 a 63 caracteres.
   Origem de dados | **Nenhuma** | Selecione **Nenhum** para criar um novo servidor de raiz. (Você selecionaria **Backup** se estivesse a criar um servidor a partir de um geo-backup de uma base de dados Azure existente para servidor PostgreSQL.)
   Nome de utilizador de administrador |*myadmin*| A sua própria conta de início de sessão quando se ligar ao servidor. O nome de login não pode ser **azure_superuser**, **azure_pg_admin,** **administrador,** **administrador,** **raiz,** **hóspede,** ou **público.** Não pode começar com **pg_.**
   Palavra-passe |A sua palavra-passe| Uma palavra-passe nova para a conta de administrador do servidor. Deve conter 8 a 128 caracteres de três das seguintes categorias: letras maiúsculas inglesas, letras minúsculas inglesas, números (0 a 9) e caracteres não alfanuméricos (por exemplo, !, $, #, %).
   Localização|A região mais próxima dos seus utilizadores| A localização que esteja mais próxima dos seus utilizadores.
   Versão|A versão principal mais recente| A versão principal mais recente do PostgreSQL, a não ser que tenha requisitos específicos.
   Computação e armazenamento | **Fins Gerais**, **Geração 5**, **2 vCores**, **5 GB**, **7 dias**, **Geograficamente Redundante** | As configurações de computação, armazenamento e cópia de segurança do seu novo servidor. Selecione **o servidor Configurar**. Em seguida, selecione o nível de preços apropriado. Para mais informações, consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/postgresql/server/) Para ativar as cópias de segurança do servidor no armazenamento geo-redundante, selecione **Geographically Redundante** das **Opções de Redundância de Backup**. Selecione **OK**.

   > [!NOTE]
   > Considere usar o nível de preços básico se o cálculo leve e a I/O forem adequados para a sua carga de trabalho. Note que os servidores criados no nível de preços básicos não podem ser posteriormente dimensionados para Final Geral ou Memória Otimizada. 
   
5. Selecione **Review + crie** para rever as suas seleções. Selecione **Criar** para aprovisionar o servidor. Esta operação pode demorar alguns minutos.

6. Na barra de ferramentas, selecione o ícone **Notificações** (um sino) para monitorizar o processo de implementação. Depois de concluída a implementação, selecione **Ir para** o recurso para abrir a página **geral** do servidor.

Uma base de dados vazia, **postgres,** é criada. Também encontrará uma base **de dados azure_maintenance** que é usada para separar os processos de serviço geridos das ações do utilizador. Não pode aceder à base **de dados azure_maintenance.**

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor
Por predefinição, o servidor que cria não é acessível ao público. Tem de dar permissões ao seu endereço IP. Aceda ao recurso do seu servidor no portal Azure e selecione a segurança de **ligação** do menu do lado esquerdo para o seu recurso de servidor. Se não sabe como encontrar o seu recurso, consulte os [recursos abertos.](../azure-resource-manager/management/manage-resources-portal.md#open-resources)

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="Screenshot que mostra Azure Database para PostgreSQL no menu.":::
  
**Selecione Adicionar o endereço IP do cliente atual**e, em seguida, selecione **Guardar**. Pode adicionar mais endereços IP ou fornecer uma gama IP para ligar ao seu servidor a partir desses endereços IP. Para obter mais informações, consulte [as regras de Firewall na Base de Dados Azure para PostgreSQL](./concepts-firewall-rules.md).
   
> [!NOTE]
> Para evitar problemas de conectividade, verifique se a sua rede permite o tráfego de saída sobre a porta 5432. A base de dados Azure para PostgreSQL utiliza esta porta.  

## <a name="connect-to-azure-database-for-postgresql-server-by-using-psql"></a>Ligue à Base de Dados de Azure para servidor PostgreSQL utilizando o psql

Você pode usar [psql](http://postgresguide.com/utilities/psql.html) ou [pgAdmin,](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html)que são clientes postgreSQL populares. Para este arranque rápido, vamos conectar-nos usando psql em [Azure Cloud Shell](../cloud-shell/overview.md) dentro do portal Azure.

1. Tome nota do nome do seu servidor, nome de login de administrador do servidor, palavra-passe e ID de subscrição para o seu servidor recém-criado a partir da secção **visão geral** do seu servidor.

2. Abra o Azure Cloud Shell no portal selecionando o ícone no lado superior esquerdo.

   > [!NOTE]
   > Se estiver a abrir a Cloud Shell pela primeira vez, verá um pedido para criar um grupo de recursos e uma conta de armazenamento. Este é um passo único e será automaticamente anexado para todas as sessões. 

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="Screenshot que mostra Azure Database para PostgreSQL no menu.":::

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

## <a name="clean-up-resources"></a>Limpar os recursos
Criou com sucesso uma Base de Dados Azure para servidor PostgreSQL num grupo de recursos. Se não espera precisar destes recursos no futuro, pode eliminá-los eliminando o grupo de recursos ou o servidor PostgreSQL. 

Para eliminar o grupo de recursos:

1. No portal Azure, procure e selecione **grupos de Recursos.** 
2. Na lista de grupos de recursos, escolha o nome do seu grupo de recursos.
3. Na página **'Visão Geral'** do seu grupo de recursos, selecione **Delete resource group**.
4. Na caixa de diálogo de confirmação, insira o nome do seu grupo de recursos e, em seguida, selecione **Delete**.

Para eliminar o servidor, selecione o botão **Eliminar** na página **'Vista Geral'** do seu servidor:

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="Screenshot que mostra Azure Database para PostgreSQL no menu.":::

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Migrar a sua base de dados utilizando exportação e importação](./howto-migrate-using-export-and-import.md)