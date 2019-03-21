---
title: 'Tutorial: Conceber uma base de dados do Azure para PostgreSQL com o portal do Azure'
description: Este tutorial mostra como Conceber a sua primeira Base de Dados do Azure para PostgreSQL com o portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: tutorial, mvc
ms.topic: tutorial
ms.date: 03/20/2018
ms.openlocfilehash: aed539484ac01d1b18b8374ffb57456364f9bd2c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119272"
---
# <a name="tutorial-design-an-azure-database-for-postgresql-using-the-azure-portal"></a>Tutorial: Conceber uma base de dados do Azure para PostgreSQL com o portal do Azure

A Base de Dados do Azure para o PostgreSQL é um serviço gerido que lhe permite executar, gerir e dimensionar as bases de dados de alta disponibilidade do PostgreSQL na cloud. No portal do Azure, pode facilmente gerir o seu servidor e criar uma base de dados.

Neste tutorial, irá utilizar o Portal do Azure para aprender a:
> [!div class="checklist"]
> * Criar uma Base de Dados do Azure para o servidor PostgreSQL
> * Configurar a firewall do servidor
> * Utilize o utilitário [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) para criar uma base de dados
> * Carregar os dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

## <a name="prerequisites"></a>Pré-requisitos
Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Criar uma Base de Dados do Azure para o PostgreSQL

É criada uma Base de Dados do Azure para o servidor PostgreSQL com um conjunto definido de [recursos de armazenamento e computação](./concepts-compute-unit-and-storage.md). O servidor é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

Siga estes passos para criar uma Base de Dados do Azure para o servidor PostgreSQL:
1. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.
2. Selecione **Bases de Dados**, na página **Nova**, e selecione **Base de Dados do Azure para o PostgreSQL**, na página **Bases de Dados**.
   ![Base de Dados do Azure para o PostgreSQL – Criar a base de dados](./media/tutorial-design-database-using-azure-portal/1-create-database.png)

3. Preencha o formulário com os detalhes do novo servidor com as seguintes informações:

   ![Criar um servidor](./media/tutorial-design-database-using-azure-portal/2-create.png)

   - Nome do servidor: **mydemoserver** (o nome de um servidor é mapeado para o nome DNS e, por conseguinte, é necessário que seja globalmente exclusivo) 
   - Subscrição: Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso existe ou é cobrado.
   - Grupo de recursos: **myresourcegroup**
   - Início de sessão e palavra-passe de administrador do servidor à sua escolha
   - Localização
   - Versão do PostgreSQL

   > [!IMPORTANT]
   > O início de sessão e a palavra-passe de administrador de servidor que especificar aqui serão necessários para iniciar sessão no servidor e nas respetivas bases de dados mais adiante neste tutorial. Lembre-se ou grave estas informações para utilização posterior.

4. Clique em **Escalão de preço** para especificar o escalão de preço do seu novo servidor. Neste tutorial, selecione **fins gerais**, **fins 5** geração de computação e 2 **vCores**, 5 GB de **armazenamento** e 7 dias  **período de retenção de cópia de segurança**. Selecione a opção de redundância da cópia de segurança **Geograficamente Redundante** para que as cópias de segurança automáticas do seu servidor sejam armazenadas no armazenamento georredundante.
   ![Base de Dados do Azure para PostgreSQL – escolher o escalão de preço](./media/tutorial-design-database-using-azure-portal/2-pricing-tier.png)

5. Clique em **OK**.

6. Clique em **Criar** para aprovisionar o servidor. O aprovisionamento demora alguns minutos.

7. Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.
   ![Base de Dados do Azure para o PostgreSQL – Ver as notificações](./media/tutorial-design-database-using-azure-portal/3-notifications.png)

   > [!TIP]
   > Marque a opção **Afixar ao dashboard** para poder controlar de forma fácil as implementações.

   Por predefinição, é criada a base de dados **postgres** no servidor. A base de dados [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) é uma base de dados predefinida que se destina a ser utilizada por utilizadores, utilitários e aplicações de terceiros. 

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

O serviço Base de Dados do Azure para PostgreSQL utiliza uma firewall ao nível do servidor. Por predefinição, esta firewall impede que todas as aplicações e ferramentas externas se liguem ao servidor e a quaisquer bases de dados no servidor, a menos que seja criada uma regra de firewall para abrir a firewall a uma gama de endereços IP específicos. 

1. Uma vez concluída a implementação, clique em **Todos os Recursos** no menu da esquerda e escreva o nome **mydemoserver** para procurar o servidor que acabou de criar. Clique no nome do servidor listado no resultado da pesquisa. É apresentada a página **Descrição Geral** do servidor, que fornece opções para configuração adicional.

   ![Base de Dados do Azure para o PostgreSQL – Procurar o servidor](./media/tutorial-design-database-using-azure-portal/4-locate.png)

2. Na página do servidor, selecione **Segurança da ligação**. 

3. Clique na caixa de texto em **Nome da Regra** e adicione uma nova regra de firewall para colocar na lista de permissões o intervalo IP para conectividade. Introduza o seu intervalo de IP. Clique em **Guardar**.

   ![Base de Dados do Azure para o PostgreSQL – Criar a Regra de Firewall](./media/tutorial-design-database-using-azure-portal/5-firewall-2.png)

4. Clique em **Guardar** e, em seguida, no **X** para fechar a página **Segurança das ligações**.

   > [!NOTE]
   > O servidor PostgreSQL do Azure comunica através da porta 5432. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao servidor da Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 5432.
   >

## <a name="get-the-connection-information"></a>Obter as informações da ligação

Quando criou o servidor da Base de Dados do Azure para PostgreSQL, também criou a base de dados **postgres** predefinida. Para ligar ao servidor da base de dados, terá de fornecer as credenciais de acesso e as informações de anfitrião.

1. No menu da esquerda no portal do Azure, clique em **Todos os recursos** e procure o servidor que acabou de criar.

   ![Base de Dados do Azure para o PostgreSQL – Procurar o servidor](./media/tutorial-design-database-using-azure-portal/4-locate.png)

2. Clique no nome do servidor **mydemoserver**.

3. Selecione a página **Descrição Geral** do servidor. Anote o **Nome do servidor** e **Nome de início de sessão de administrador do servidor**.

   ![Base de Dados do Azure para o PostgreSQL – Início de Sessão de Administrador do Servidor](./media/tutorial-design-database-using-azure-portal/6-server-name.png)


## <a name="connect-to-postgresql-database-using-psql-in-cloud-shell"></a>Ligar-se à base de dados do PostgreSQL com o psql no Cloud Shell

Agora, vamos utilizar o utilitário da linha de comandos [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) para ligar ao servidor da Base de Dados do Azure para PostgreSQL. 
1. Inicie o Azure Cloud Shell através do ícone de terminal no painel de navegação superior.

   ![Base de Dados de Azure para o PostgreSQL – ícone do terminal do Azure Cloud Shell](./media/tutorial-design-database-using-azure-portal/7-cloud-shell.png)

2. O Azure Cloud Shell é aberto no browser, onde poderá escrever os comandos bash.

   ![Base de Dados do Azure para o PostgreSQL – Linha de Comandos Bash do Azure Shell](./media/tutorial-design-database-using-azure-portal/8-bash.png)

3. Na linha de comandos do Cloud Shell, estabeleça ligação à Base de Dados do Azure para o servidor PostgreSQL com os comandos psql. O formato seguinte é utilizado para estabelecer ligação a uma Base de Dados do Azure para o servidor PostgreSQL com o utilitário [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --port=<port> --username=<server admin login> --dbname=<database name>
   ```

   Por exemplo, o comando seguinte liga à base de dados predefinida com o nome **postgres** no servidor PostgreSQL **mydemoserver.postgres.database.azure.com**, com as credenciais de acesso. Quando lhe for pedido, introduza a palavra-passe de administrador do servidor.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

## <a name="create-a-new-database"></a>Criar uma nova base de dados
Assim que estiver ligado ao servidor, crie uma base de dados vazia na linha de comandos.
```bash
CREATE DATABASE mypgsqldb;
```

Na linha de comandos, execute o comando seguinte para mudar a ligação para a base de dados **mypgsqldb** recentemente criada.
```bash
\c mypgsqldb
```
## <a name="create-tables-in-the-database"></a>Criar tabelas na base de dados
Agora que sabe como ligar à Base de Dados do Azure para PostgreSQL, pode concluir algumas tarefas básicas:

Em primeiro lugar, crie uma tabela e carregue-a com alguns dados. Vamos criar uma tabela que controla as informações de inventário com este código SQL:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Agora pode ver a tabela criada recentemente na lista de tabelas ao escrever:
```sql
\dt
```

## <a name="load-data-into-the-tables"></a>Carregar dados para as tabelas
Agora que tem uma tabela, insira alguns dados na mesma. Na janela da linha de comandos aberta, execute a seguinte consulta para inserir algumas linhas de dados.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Tem agora duas linhas de dados de exemplo na tabela de inventário que criou anteriormente.

## <a name="query-and-update-the-data-in-the-tables"></a>Consultar e atualizar os dados nas tabelas
Execute a seguinte consulta para obter informações da tabela de base de dados de inventário. 
```sql
SELECT * FROM inventory;
```

Também pode atualizar os dados nas tabelas.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Pode ver os valores atualizados ao obter os dados.
```sql
SELECT * FROM inventory;
```

## <a name="restore-data-to-a-previous-point-in-time"></a>Restaurar dados para um ponto anterior no tempo
Imagine que eliminou acidentalmente esta tabela. Esta situação é algo de que não é fácil recuperar. A Base de Dados do Azure para PostgreSQL permite-lhe regressar a qualquer ponto anterior no tempo para o qual o seu servidor tenha cópias de segurança (determinado pelo período de retenção da cópia de segurança configurado por si) e restaurar este ponto anterior no tempo num novo servidor. Pode utilizar este servidor novo para recuperar os dados eliminados. Os passos seguintes restauram o servidor **mydemoserver** para um ponto anterior à adição da tabela de inventário.

1. Na página de **Descrição Geral** da Base de Dados do Azure para PostgreSQL para o seu servidor, clique em **Restaurar** na barra de ferramentas. É aberta a página **Restaurar**.

   ![Portal do Azure – Opções do formulário de restauro](./media/tutorial-design-database-using-azure-portal/9-azure-portal-restore.png)

2. Preencha o formulário **Restaurar** com as informações necessárias:

   ![Portal do Azure – Opções do formulário de restauro](./media/tutorial-design-database-using-azure-portal/10-azure-portal-restore.png)

   - **Ponto de restauro**: Selecione um ponto anterior no tempo que ocorre antes do servidor foi alterado
   - **Servidor de destino**: Forneça um novo nome de servidor que pretende restaurar para
   - **Localização**: Não é possível selecionar a região, por predefinição é igual ao servidor de origem
   - **Escalão de preço**: Não é possível alterar este valor quando restaurar um servidor. É igual ao servidor de origem. 
3. Clique em **OK** para [restaurar o servidor para um ponto anterior no tempo](./howto-restore-server-portal.md) antes da eliminação da tabela. Restaurar um servidor para outro ponto anterior no tempo cria um novo servidor duplicado como servidor original a partir do ponto anterior no tempo que especificar, desde que esteja dentro do período de retenção do seu [escalão de preço](./concepts-pricing-tiers.md).

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu a utilizar o portal do Azure e outros utilitários para:
> [!div class="checklist"]
> * Criar uma Base de Dados do Azure para o servidor PostgreSQL
> * Configurar a firewall do servidor
> * Utilize o utilitário [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) para criar uma base de dados
> * Carregar os dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

Em seguida, para saber como utilizar a CLI do Azure para realizar tarefas semelhantes, reveja este tutorial: [Conceber a sua primeira base de dados do Azure para PostgreSQL com a CLI do Azure](tutorial-design-database-using-azure-cli.md)
