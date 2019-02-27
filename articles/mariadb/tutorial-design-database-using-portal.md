---
title: 'Tutorial: Conceber uma base de dados do Azure para MariaDB com o portal do Azure'
description: Este tutorial explica como criar e gerir um servidor e uma base de dados do Azure Database for MariaDB com o portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: tutorial
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 2bbe69d114df61f6ca01add0b3459220d59d83e0
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56880520"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-database-by-using-the-azure-portal"></a>Tutorial: Criar uma base de dados do Azure para MariaDB base de dados com o portal do Azure

O Azure Database for MariaDB é um serviço gerido que pode utilizar para executar, gerir e dimensionar as bases de dados MySQL de alta disponibilidade na cloud. Ao utilizar o portal do Azure, pode facilmente gerir o seu servidor e criar uma base de dados.

Neste tutorial, irá utilizar o Portal do Azure para aprender a:

> [!div class="checklist"]
> * Criar um Azure Database for MariaDB
> * Configurar a firewall do servidor
> * Utilizar a ferramenta de linha de comandos mysql para criar uma base de dados
> * Carregar os dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

No browser, aceda ao [Portal do Azure](https://portal.azure.com/). Introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

## <a name="create-an-azure-database-for-mariadb-server"></a>Criar um Azure Database for MariaDB Server

Vai criar um Azure Database for MariaDB Server com um conjunto definido de [recursos de armazenamento e computação](concepts-pricing-tiers.md). O servidor é criado num [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

1. Selecione o botão (+) **Criar um recurso**, no canto superior esquerdo do portal.

2. Na caixa de pesquisa, introduza **Azure Database for MariaDB** para encontrar o serviço.
   
   ![Aceder ao MySQL](./media/tutorial-design-database-using-portal/1-Navigate-to-mariadb.png)

3. Selecione o mosaico **Azure Database for MariaDB** e, em seguida, **Criar**. Introduza ou selecione as informações necessárias.
   
   ![Formulário de criação](./media/tutorial-design-database-using-portal/2-create-form.png)

    Definição | Valor sugerido | Descrição do campo 
    ---|---|---
    Nome do servidor | *um nome de servidor exclusivo* | Escolha um nome exclusivo que identifique o seu Azure Database for MariaDB Server. Por exemplo, **mydemoserver**. O nome de domínio *.mariadb.database.azure.com* é anexado ao nome do servidor que introduzir. O nome do servidor pode conter apenas letras minúsculas, números e o caráter de hífen (-). Tem de conter entre 3 e 63 carateres.
    Subscrição | *a sua subscrição* | Selecione a subscrição do Azure que quer utilizar para o servidor. Se tiver várias subscrições, escolha a subscrição na qual é cobrado pelo recurso.
    Grupo de recursos | **myresourcegroup** | Introduza um novo nome do grupo de recursos ou selecione um grupo já existente.
    Selecionar origem | **Em branco** | Selecione **Em branco** para criar um novo servidor. (Se estiver a criar um servidor a partir de uma cópia de segurança de área geográfica de um servidor do Azure Database for MariaDB Server existente, selecione **Cópia de segurança**).
    Início de sessão de administrador do servidor | **myadmin** | Uma conta de início de sessão a utilizar quando se ligar ao servidor. O nome de início de sessão de administrador não pode ser **azure_superuser**, **admin**, **administrator**, **root**, **guest** ou **public**.
    Palavra-passe | *a sua escolha* | Introduza uma nova palavra-passe para a conta de administrador do servidor. Tem de conter entre 8 e 128 carateres. A palavra-passe tem de conter carateres de três das seguintes categorias: Letras em maiúscula letras, em minúscula inglesas, números (0-9) e carateres não alfanuméricos (!, $, #, % e assim por diante).
    Confirmar palavra-passe | *a sua escolha*| Confirme a palavra-passe da conta de administrador.
    Localização | *a região mais próxima dos seus utilizadores*| Selecione a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure.
    Versão | *a versão mais recente*| A versão mais recente (a não ser que tenha requisitos específicos que exijam uma versão diferente).
    Escalão de preço | Veja a descrição. | As configurações de computação, armazenamento e cópia de segurança do seu novo servidor. Selecione **Escalão de preço** > **Fins Gerais**. Mantenha os valores predefinidos para as seguintes definições:<br><ul><li>**Geração de Computação** (Ger 5)</li><li>**vCore** (2 vCores)</li><li>**Armazenamento** (5 GB)</li><li>**Período de Retenção da Cópia de Segurança** (7 dias)</li></ul><br>Para ativar as cópias de segurança do servidor no armazenamento georredundante, em **Backup Redundancy Options** (Opções de Redundância da Cópia de Segurança), selecione **Geographically Redundant** (Geograficamente Redundante). <br><br>Para guardar a seleção deste escalão de preço, selecione **OK**. A captura de ecrã seguinte captura estas seleções.
    
   ![Escalão de preço](./media/tutorial-design-database-using-portal/3-pricing-tier.png)

4. Selecione **Criar**. Em menos de dois minutos, um novo Azure Database for MariaDB Server estará em execução na cloud. Para monitorizar o processo de implementação, selecione **Notificações** na barra de ferramentas.

## <a name="configure-the-firewall"></a>Configurar a firewall

O Azure Database for MariaDB está protegido por uma firewall. Por predefinição, todas as ligações ao servidor e às bases de dados dentro do servidor são rejeitadas. Antes de ligar ao Azure Database for MariaDB pela primeira vez, configure a firewall para adicionar o endereço IP de rede público do computador cliente (ou um intervalo de endereços IP).

1. Selecione o servidor acabado de criar e, em seguida, selecione **Segurança da ligação**.
   
   ![Segurança da Ligação](./media/tutorial-design-database-using-portal/1-Connection-security.png)
2. Pode selecionar **Adicionar o Meu IP** ou configurar regras de firewall aqui. Não se esqueça de selecionar **Guardar** depois de criar as regras.

Já pode ligar ao servidor através da ferramenta de linha de comandos mysql ou do MySQL Workbench.

> [!TIP]
> O servidor do Azure Database for MariaDB comunica através da porta 3306. Se tentar ligar a partir de uma rede empresarial, a firewall da rede poderá não permitir o tráfego de saída através da porta 3306. Neste caso, para ligar ao servidor do Azure Database for MariaDB, o departamento de TI tem de abrir a porta 3306.

## <a name="get-connection-information"></a>Obter informações da ligação

Obtenha os valores de **Nome do servidor** (completamente qualificado) e **Nome de início de sessão de administrador do servidor** do servidor do Azure Database for MariaDB no portal do Azure. Utilize o nome de servidor completamente qualificado para ligar ao seu servidor com a ferramenta de linha de comandos mysql. 

1. No [portal do Azure](https://portal.azure.com/), no menu esquerdo, selecione **Todos os recursos**. Introduza o nome do servidor e procure o servidor do Azure Database for MariaDB. Selecione o nome do servidor para ver os detalhes do servidor.

2. Na página **Descrição geral**, tome nota dos valores de **Nome do servidor** e **Nome de início de sessão de administrador do servidor**. Também pode selecionar o botão **Copiar**, junto a cada campo, para copiar o valor para a área de transferência.

   ![Propriedades do servidor](./media/tutorial-design-database-using-portal/2-server-properties.png)

No nosso exemplo, o nome do servidor é **mydemoserver.mariadb.database.azure.com** e o nome de início de sessão de administrador do servidor é **myadmin@mydemoserver**.

## <a name="connect-to-the-server-by-using-mysql"></a>Ligar ao servidor com o mysql

Utilize a [ferramenta de linha de comandos mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) para estabelecer uma ligação ao servidor do Azure Database for MariaDB. Pode executar a ferramenta de linha de comandos mysql a partir do Azure Cloud Shell no browser ou a partir do seu computador com as ferramentas mysql instaladas localmente. Para abrir o Azure Cloud Shell, selecione o botão **Experimente** num bloco de código neste artigo ou aceda ao portal do Azure e clique no ícone **>_** na barra de ferramentas superior direita. 

Introduza o comando para estabelecer a ligação:

```azurecli-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Criar uma base de dados vazia

Quando estiver ligado ao servidor, crie uma base de dados vazia para trabalhar:

```sql
CREATE DATABASE mysampledb;
```

Na linha de comandos, execute o comando seguinte para mudar a ligação para a base de dados recentemente criada:

```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Criar tabelas na base de dados

Agora que sabe como ligar à base de dados do Azure Database for MariaDB, pode concluir algumas tarefas básicas.

Em primeiro lugar, crie uma tabela e carregue-a com alguns dados. Vamos criar uma tabela que armazena informações de inventário:

```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-in-the-tables"></a>Carregar dados nas tabelas

Agora que tem uma tabela, insira alguns dados na mesma. Na janela da linha de comandos aberta, execute a seguinte consulta para inserir algumas linhas de dados:

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

## <a name="query-and-update-the-data-in-the-tables"></a>Consultar e atualizar os dados nas tabelas

Execute a seguinte consulta para obter informações da tabela de base de dados:

```sql
SELECT * FROM inventory;
```

Também pode atualizar os dados nas tabelas:

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A linha é atualizada quando obtém dados:

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurar uma base de dados para um ponto anterior no tempo

Imagine que eliminou acidentalmente uma tabela de base de dados importante e não consegue recuperar os dados facilmente. No Azure Database for MariaDB, pode restaurar o servidor para um ponto anterior no tempo ao criar uma cópia das bases de dados no novo servidor. Pode utilizar este servidor novo para recuperar os dados eliminados. Os passos seguintes restauram o servidor de exemplo para um ponto anterior no tempo antes da tabela ter sido adicionada:

1. No portal do Azure, encontre o Azure Database for MariaDB. Na página **Descrição geral**, selecione **Restaurar**.

   ![Restaurar uma base de dados](./media/tutorial-design-database-using-portal/1-restore-a-db.png)

2. Na página **Restaurar**, introduza ou selecione as seguintes informações:
   
   ![Formulário de restauro](./media/tutorial-design-database-using-portal/2-restore-form.png)
   
   - **Ponto de restauro**: Selecione um ponto no tempo que pretende restaurar para, no período de tempo listado. Certifique-se de que converte o fuso horário local para UTC.
   - **Restaurar para novo servidor**: Introduza um novo nome de servidor para restaurar para.
   - **Localização**: A região é igual ao servidor de origem e não pode ser alterada.
   - **Escalão de preço**: O escalão de preço é o mesmo que o servidor de origem e não pode ser alterado.
   
3. Selecione **OK** para [restaurar o servidor para um ponto anterior no tempo](./howto-restore-server-portal.md) antes da tabela ter sido eliminada. Restaurar um servidor cria uma nova cópia do servidor no ponto anterior no tempo que selecionou. 

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, utilizou o portal do Azure para aprender a:

> [!div class="checklist"]
> * Criar um Azure Database for MariaDB
> * Configurar a firewall do servidor
> * Utilize a ferramenta da linha de comandos mysql para criar uma base de dados
> * Carregar os dados de exemplo
> * Consultar dados
> * Atualizar dados
> * Restaurar dados

> [!div class="nextstepaction"]
> [Como ligar aplicações ao Azure Database for MariaDB](./howto-connection-string.md)
