---
title: 'Quickstart: Criar servidor - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Flexível'
description: Quickstart guia para criar e gerir uma Base de Dados Azure para PostgreSQL - Servidor Flexível utilizando a interface de utilizador do portal Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 6d10298b016cbcf362af2d272f9fa822db6e569d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604758"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---flexible-server-in-the-azure-portal"></a>Quickstart: Criar uma Base de Dados Azure para PostgreSQL - Servidor Flexível no portal Azure

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

A Base de Dados do Azure para o PostgreSQL é um serviço gerido com o qual pode executar, gerir e dimensionar as bases de dados de elevada disponibilidade do PostgreSQL na cloud. Este Quickstart mostra-lhe como criar uma Base de Dados Azure para PostgreSQL - Servidor Flexível em cerca de cinco minutos utilizando o portal Azure.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Abra o browser e aceda ao [portal do Azure](https://portal.azure.com/). Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um servidor da Base de Dados do Azure para PostgreSQL

É criado um servidor da Base de Dados do Azure para PostgreSQL com um conjunto de [recursos de armazenamento e computação](./concepts-compute-storage.md) configurado. O servidor é criado dentro de um [grupo de recursos do Azure](../../azure-resource-manager/management/overview.md).

Para criar uma Base de Dados do Azure para o servidor PostgreSQL, siga os passos abaixo:

1. **Selecione Criar um recurso** (+) no canto superior esquerdo do portal.

2. Selecione **Base de Dados**  >  **Azure Databases para PostgreSQL**.

    :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="A Base de Dados Azure para PostgreSQL no menu":::

3. Selecione a opção de implementação **do servidor flexível.**

   :::image type="content" source="./media/quickstart-create-database-portal/2-select-deployment-option.png" alt-text="Selecione Azure Database para postgreSQL - Opção de implementação flexível do servidor":::

4. Preencha o formulário Básico com as **seguintes** informações:

    :::image type="content" source="./media/quickstart-create-database-portal/3-create-basics.png" alt-text="Criar um servidor":::

    Definição|Valor sugerido|Descrição
    ---|---|---
    Subscrição|Nome da sua subscrição|A subscrição do Azure que quer utilizar para o servidor. Se tiver várias subscrições, escolha a subscrição na qual gostaria de ser faturada pelo recurso.
    Grupo de recursos|*grupo myresource*| Um nome de grupo de recursos novo ou um já existente na sua subscrição.
    Nome do servidor |*mydemoserver*|Um nome exclusivo que identifique a sua Base de Dados do Azure para o servidor PostgreSQL. O nome de domínio *postgres.database.azure.com* é acrescentado ao nome de servidor que indicar. O servidor só pode conter letras minúsculas, números e o caráter de hífen (-). Tem de conter, pelo menos, 3 a 63 carateres.
    Nome de utilizador de administrador |*myadmin*| A sua própria conta de início de sessão quando se ligar ao servidor. O nome de login não pode ser **azure_superuser**, **azure_pg_admin,** **administrador,** **administrador,** **raiz,** **hóspede,** ou **público.** Não pode começar com **pg_.**
    Palavra-passe |A sua palavra-passe| Uma palavra-passe nova para a conta de administrador do servidor. Tem de conter entre 8 e 128 carateres. A sua palavra-passe tem de conter carateres de três das categorias seguintes: letras em maiúscula inglesas, letras em minúscula inglesas, números (0 a 9) e carateres não alfanuméricos (!, $, #, %, etc.).
    Localização|A região mais próxima dos seus utilizadores| A localização que esteja mais próxima dos seus utilizadores.
    Versão|A versão principal mais recente| A versão principal mais recente do PostgreSQL, a não ser que tenha requisitos específicos.
    Computação e armazenamento | **Final geral**, **4 vCores**, **512 GB**, **7 dias** | As configurações de computação, armazenamento e cópia de segurança do seu novo servidor. Selecione **o servidor Configurar**. *Finalidade Geral*, *4 vCores*, *512 GB* e *7 dias* são os valores padrão para o **nível de Computação**, **vCore,** **Armazenamento** e Período **de Retenção de Backup**. Pode deixar os sliders como está ou ajustá-los. Para guardar a seleção deste escalão de preço, selecione **OK**. A captura de ecrã seguinte captura estas seleções.

    :::image type="content" source="./media/quickstart-create-database-portal/4-pricing-tier.png" alt-text="O painel de preços":::
    
5. Opções de configuração de rede

    No separador 'Rede', pode escolher como o seu servidor é acessível. A Base de Dados do Azure para o PostgreSQL cria uma firewall ao nível do servidor. Impede que as aplicações e ferramentas externas se liguem ao servidor e a quaisquer bases de dados no mesmo, a menos que crie uma regra de firewall para abrir a firewall aos endereços IP específicos. Recomendamos tornar o servidor acessível ao público:

    :::image type="content" source="./media/quickstart-create-database-portal/5-networking.png" alt-text="O painel de networking":::

    E, em seguida, restringi-lo ao seu próprio endereço IP cliente:

    :::image type="content" source="./media/quickstart-create-database-portal/6-add-client-ip.png" alt-text="Selecione Adicionar o endereço IP do cliente atual":::

6. Selecione **Review + crie** para rever as suas seleções. Selecione **Criar** para aprovisionar o servidor. Esta operação poderá demorar alguns minutos.

7. Na barra de ferramentas, selecione o ícone **Notificações** (um sino) para monitorizar o processo de implementação. Uma vez concluída a implementação, pode selecionar **Afixar ao dashboard**, o que cria um mosaico para este servidor no dashboard do portal do Azure como um atalho para a página **Descrição geral** do servidor. Selecionar **Ir para recurso** abre a página **Descrição geral** do servidor.

    :::image type="content" source="./media/quickstart-create-database-portal/7-notifications.png" alt-text="O painel de notificações":::

   Por predefinição, é criada uma base de dados **postgres** no servidor. A base de dados [postgres](https://www.postgresql.org/docs/12/static/app-initdb.html) é uma base de dados predefinida que se destina a ser utilizada por utilizadores, utilitários e aplicações de terceiros. (A outra base de dados predefinida é **azure_maintenance**. A sua função é separar os processos de serviço geridos das ações dos utilizadores. Não pode aceder a esta base de dados.)

    > [!NOTE]
    > As ligações à Base de Dados do Azure para o servidor PostgreSQL comunicam através da porta 5432. Quando tenta ligar a partir de uma rede empresarial, a firewall da rede poderá não permitir o tráfego de saída através da porta 5432. Se for este o caso, não pode ligar ao servidor, a menos que o seu departamento de TI abra a porta 5432.
    >

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Quando cria a Base de Dados do Azure para o servidor PostgreSQL, também é criada uma base de dados predefinida, com o nome **postgres**. Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e as credenciais de início de sessão de administrador. Poderá ter apontado esses valores anteriormente no artigo Guia de Introdução. Se não, pode encontrar facilmente o nome do servidor e as informações de início de sessão na página **Descrição Geral** do servidor, no portal.

Abra a página **Descrição Geral** do servidor. Anote o **Nome do servidor** e o **Nome de início de sessão de administrador do servidor**. Coloque o cursor sobre cada campo e o símbolo de cópia é apresentado à direita do texto. Selecione o símbolo de cópia conforme necessário para copiar os valores.

 :::image type="content" source="./media/quickstart-create-database-portal/8-server-name.png" alt-text="A página geral do servidor":::

## <a name="connect-to-the-postgresql-database-using-psql"></a>Ligar à base de dados do PostgreSQL com psql

Estão disponíveis diversas aplicações para ligar à sua Base de Dados do Azure para o servidor PostSQL. Se o seu computador cliente tiver o PostgreSQL instalado, pode utilizar uma instância local de [psql](https://www.postgresql.org/docs/current/static/app-psql.html) para ligar a um servidor PostgreSQL do Azure. Vamos utilizar agora o utilitário da linha de comandos psql para ligar ao servidor PostgreSQL do Azure.

1. Execute o comando psql seguinte para ligar a uma Base de Dados do Azure para o servidor PostgreSQL

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Por exemplo, o comando seguinte liga à base de dados predefinida com o nome **postgres** no servidor PostgreSQL **mydemoserver.postgres.database.azure.com** com as credenciais de acesso. Introduza o `<server_admin_password>` que escolheu quando lhe for pedida a palavra-passe.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Quando estiver ligado, o utilitário psql apresenta uma linha de comandos postgres onde escreve os comandos de sql. Na saída da ligação inicial, poderá aparecer um aviso, porque o psql que está a utilizar pode ter uma versão diferente da versão do servidor da Base de Dados do Azure para PostgreSQL.

   Exemplo de saída psql:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Se a firewall não estiver configurada para permitir o endereço IP do seu cliente, ocorre o seguinte erro:
   >
   > "psql: FATAL: no pg_hba.conf entrada para hospedeiro `<IP address>` , utilizador "myadmin", base de dados "postgres", SSL em FATAL: Ligação SSL é necessária. Especifique as opções de SSL e volte a tentar.
   >
   > Confirme que o IP do seu cliente é permitido nas regras de firewall.

2. Crie uma base de dados vazia com o nome "mypgsqldb" ao escrever o seguinte comando:

    ```bash
    CREATE DATABASE mypgsqldb;
    ```

3. Na linha de comandos, execute o comando seguinte para mudar a ligação para a base de dados **mypgsqldb** recentemente criada:

    ```bash
    \c mypgsqldb
    ```

4. Escreva `\q` e selecione a tecla Enter para sair do psql.

Ligou-se ao servidor da Base de Dados do Azure para PostgreSQL através do psql e criou uma base de dados de utilizador em branco.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode limpar os recursos que criou no Início Rápido de duas formas. Pode eliminar o grupo de recursos do Azure, que inclui todos os recursos no grupo de recursos. Se quiser manter os outros recursos intactos, elimine apenas o recurso do servidor.

> [!TIP]
> Outros Guias de Introdução desta coleção compilados com base neste Guia de Introdução. Se pretender continuar a trabalhar com Guias de Introdução, não limpe os recursos que criou neste Guia de Introdução. Se não quiser continuar, siga estes passos para eliminar os recursos criados por este Início Rápido no portal.

Para eliminar o grupo de recursos inteiro, incluindo o servidor criado recentemente:

1. Localize o grupo de recursos no portal. No menu do lado esquerda, selecione **Grupos de recursos**. Em seguida, selecione o nome do grupo de recursos, como o exemplo **myresourcegroup**.

2. Na página do grupo de recursos, selecione **Eliminar**. Insira o nome do seu grupo de recursos, como o exemplo, **myresourcegroup,** na caixa de texto para confirmar a eliminação. Selecione **Eliminar**.

Para eliminar o servidor criado recentemente:

1. Localize o seu servidor no portal, se não estiver aberto. No menu do lado esquerdo, selecione **Todos os recursos**. Em seguida, procure o servidor que criou.

2. Na página **Descrição geral**, selecione **Eliminar**.

    :::image type="content" source="./media/quickstart-create-database-portal/9-delete.png" alt-text="O botão Eliminar":::

3. Confirme o nome do servidor que quer eliminar e veja as bases de dados incluídas que são afetadas. Insira o nome do seu servidor na caixa de texto, tal como o exemplo, **mydemoserver**. Selecione **Eliminar**.

## <a name="next-steps"></a>Próximos passos
> [!div class="nextstepaction"]
> [Implementar uma aplicação Django com App Service e PostgreSQL](tutorial-django-app-service-postgres.md)