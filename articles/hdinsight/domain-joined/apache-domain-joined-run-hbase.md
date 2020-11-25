---
title: Apache HBase & Pacote de Segurança Empresarial - Azure HDInsight
description: Tutorial - Saiba como configurar as políticas apache ranger para a HBase em Azure HDInsight com Pacote de Segurança Empresarial.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 5747de399e7ae0cfe99ba013f8da376be0ba1b2a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993640"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package"></a>Tutorial: Configurar as políticas apache HBase em HDInsight com pacote de segurança empresarial

Saiba como configurar as políticas apache ranger para pacotes de segurança empresarial (ESP) apache hbase clusters. Os clusters do ESP estão ligados a um domínio, permitindo que os utilizadores sejam autenticados com credenciais do domínio. Neste tutorial, você cria duas políticas ranger para restringir o acesso a diferentes colunas-famílias em uma tabela HBase.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Criar utilizadores de domínio
> * Criar políticas do Ranger
> * Criar tabelas num cluster HBase
> * Testar as políticas do Ranger

## <a name="before-you-begin"></a>Before you begin

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Inicie sessão no [portal do Azure](https://portal.azure.com/).

* Crie um [cluster HBase HDInsight com pacote de segurança empresarial.](apache-domain-joined-configure-using-azure-adds.md)

## <a name="connect-to-apache-ranger-admin-ui"></a>Ligar à IU do Apache Ranger Admin

1. Num browser, ligue à interface de utilizador do Ranger Admin através do URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Lembre-se de alterar `<ClusterName>` o nome do seu cluster HBase.

    > [!NOTE]  
    > As credenciais do Ranger não são iguais às credenciais de cluster do Hadoop. Para impedir que os browsers utilizem credenciais em cache do Hadoop, utilize uma nova janela do browser InPrivate para ligar à IU do Ranger Admin.

2. Inicie sessão com as suas credenciais de administrador do Azure Active Directory (AD). As credenciais de administrador do Azure AD não iguais às credenciais de cluster do HDInsight ou às credenciais SSH de nó HDInsight do Linux.

## <a name="create-domain-users"></a>Criar utilizadores de domínio

Visite [Criar um cluster HDInsight com pacote de segurança empresarial,](./apache-domain-joined-configure-using-azure-adds.md)para aprender a criar os **sales_user1** e **marketing_user1** utilizadores de domínio. Num cenário de produção, os utilizadores de domínio são provenientes do seu inquilino do Active Directory.

## <a name="create-hbase-tables-and-import-sample-data"></a>Criar tabelas HBase e importar dados de amostras

Pode utilizar o SSH para ligar aos clusters HBase e, em seguida, utilizar [a Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) para criar tabelas HBase, inserir dados e dados de consulta. Para obter mais informações, consulte [Use SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="to-use-the-hbase-shell"></a>Para utilizar a shell de HBase

1. A partir do SSH, execute o seguinte comando do HBase:
   
    ```bash
    hbase shell
    ```

2. Criar uma tabela HBase `Customers` com famílias de duas colunas: `Name` e `Contact` .

    ```hbaseshell   
    create 'Customers', 'Name', 'Contact'
    list
    ```
3. Insira alguns dados:
    
    ```hbaseshell   
    put 'Customers','1001','Name:First','Alice'
    put 'Customers','1001','Name:Last','Johnson'
    put 'Customers','1001','Contact:Phone','333-333-3333'
    put 'Customers','1001','Contact:Address','313 133rd Place'
    put 'Customers','1001','Contact:City','Redmond'
    put 'Customers','1001','Contact:State','WA'
    put 'Customers','1001','Contact:ZipCode','98052'
    put 'Customers','1002','Name:First','Robert'
    put 'Customers','1002','Name:Last','Stevens'
    put 'Customers','1002','Contact:Phone','777-777-7777'
    put 'Customers','1002','Contact:Address','717 177th Ave'
    put 'Customers','1002','Contact:City','Bellevue'
    put 'Customers','1002','Contact:State','WA'
    put 'Customers','1002','Contact:ZipCode','98008'
    ```
4. Ver o conteúdo da tabela:
    
    ```hbaseshell
    scan 'Customers'
    ```

    ![Saída da concha HDInsight Hadoop HBase](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Criar políticas do Ranger

Crie uma política ranger para **sales_user1** e **marketing_user1.**

1. Abra a **IU do Ranger Admin**. Clique **\<ClusterName> _hbase** em **HBase**.

   ![HDInsight Apache Ranger Admin UI](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. O ecrã **da Lista de Políticas** apresentará todas as políticas ranger criadas para este cluster. Poderá ser apresentada uma política pré-configurada. Clique **em Adicionar Nova Política**.

    ![Lista de políticas do Apache Ranger HBase](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. No ecrã **'Criar Política',** insira os seguintes valores:

   |**Definição**  |**Valor sugerido**  |
   |---------|---------|
   |Nome da Política  |  sales_customers_name_contact   |
   |Tabela HBase   |  Clientes |
   |HBase Column-família   |  Nome, Contacto |
   |Coluna HBase   |  * |
   |Grupo selecionado  | |
   |Selecionar Utilizador  | sales_user1 |
   |Permissões  | Ler |

   Os carateres universais seguintes podem ser incluídos no nome do tópico:

   * `*` indica zero ou mais ocorrências de caracteres.
   * `?` indica um único caráter.

   ![A política da Apache Ranger cria vendas](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >Aguarde um momento enquanto o Ranger sincroniza com o Azure AD, se um utilizador de domínio não for preenchido automaticamente em **Selecionar Utilizador**.

4. Clique em **Adicionar** para guardar a política.

5. Clique em **Adicionar Nova Política** e, em seguida, introduza os seguintes valores:

   |**Definição**  |**Valor sugerido**  |
   |---------|---------|
   |Nome da Política  |  marketing_customers_contact   |
   |Tabela HBase   |  Clientes |
   |HBase Column-família   |  Contacto |
   |Coluna HBase   |  * |
   |Grupo selecionado  | |
   |Selecionar Utilizador  | marketing_user1 |
   |Permissões  | Ler |

   ![A política apache ranger cria marketing](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. Clique em **Adicionar** para guardar a política.

## <a name="test-the-ranger-policies"></a>Testar as políticas do Ranger

Com base nas políticas dos Rangers configuradas, **sales_user1** podem ver todos os dados para as colunas nas `Name` famílias e `Contact` colunas. O **marketing_user1** só pode ver dados na família da `Contact` coluna.

### <a name="access-data-as-sales_user1"></a>Aceder aos dados como sales_user1

1. Abra uma nova ligação SSH ao cluster. Utilize o seguinte comando para iniciar sação no cluster:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Utilize o comando kinit para alterar o contexto do nosso utilizador pretendido.

   ```bash
   kinit sales_user1
   ```

2. Abra a concha HBase e digitalize a tabela `Customers` .

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. Note que o utilizador de vendas pode visualizar todas as colunas da `Customers` tabela, incluindo as duas colunas na `Name` coluna-família, bem como as cinco colunas da `Contact` coluna-família.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1001                              column=Name:First, timestamp=1548894871561, value=Alice
     1001                              column=Name:Last, timestamp=1548894871707, value=Johnson
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
     1002                              column=Name:First, timestamp=1548894897419, value=Robert
     1002                              column=Name:Last, timestamp=1548894897487, value=Stevens
    2 row(s) in 0.1000 seconds
    ```

### <a name="access-data-as-marketing_user1"></a>Aceder aos dados como marketing_user1

1. Abra uma nova ligação SSH ao cluster. Utilize o seguinte comando para iniciar sinse como **marketing_user1:**

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Use o comando kinit para alterar o contexto do nosso utilizador desejado

   ```bash
   kinit marketing_user1
   ```

1. Abra a concha HBase e digitalize a `Customers` tabela:

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

1. Note que o utilizador de marketing só pode ver as cinco colunas da `Contact` coluna-família.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
    2 row(s) in 0.0730 seconds
    ```

1. Veja os eventos de acesso de auditoria da IU do Ranger.

   ![Auditoria política do HDInsight Ranger UI](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine o cluster HBase que criou com os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Na caixa **de pesquisa** no topo, escreva **HDInsight**. 
1. Selecione **clusters HDInsight** em **Serviços**.
1. Na lista de clusters HDInsight que aparecem, clique no **...** ao lado do cluster que criou para este tutorial. 
1. Clique em **Eliminar**. Clique em **Sim**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começa com um Apache HBase](../hbase/apache-hbase-tutorial-get-started-linux.md)