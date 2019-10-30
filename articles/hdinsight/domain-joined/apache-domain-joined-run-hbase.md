---
title: '& Do Apache HBase Enterprise Security Package-Azure HDInsight'
description: Tutorial-saiba como configurar políticas do Apache Ranger para HBase no Azure HDInsight com o Enterprise Security Package.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 89e9faeb3c67d0fd0c57adea3a3f69ec5438e3a0
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044651"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package"></a>Tutorial: configurar políticas do Apache HBase no HDInsight com o Enterprise Security Package

Saiba como configurar políticas do Apache Ranger para clusters do Apache HBase Enterprise Security Package (ESP). Os clusters do ESP estão ligados a um domínio, permitindo que os utilizadores sejam autenticados com credenciais do domínio. Neste tutorial, você cria duas políticas de Ranger para restringir o acesso a diferentes famílias de coluna em uma tabela do HBase.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar utilizadores de domínio
> * Criar políticas do Ranger
> * Criar tabelas em um cluster HBase
> * Testar as políticas do Ranger

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Inicie sessão no [portal do Azure](https://portal.azure.com/).

* Crie um [cluster HDInsight HBase com Enterprise Security Package](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Ligar à IU do Apache Ranger Admin

1. Num browser, ligue à interface de utilizador do Ranger Admin através do URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Lembre-se de alterar `<ClusterName>` para o nome do cluster HBase.

    > [!NOTE]  
    > As credenciais do Ranger não são iguais às credenciais de cluster do Hadoop. Para impedir que os browsers utilizem credenciais em cache do Hadoop, utilize uma nova janela do browser InPrivate para ligar à IU do Ranger Admin.

2. Inicie sessão com as suas credenciais de administrador do Azure Active Directory (AD). As credenciais de administrador do Azure AD não iguais às credenciais de cluster do HDInsight ou às credenciais SSH de nó HDInsight do Linux.

## <a name="create-domain-users"></a>Criar utilizadores de domínio

Visite [criar um cluster HDInsight com o Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds)para saber como criar os usuários de domínio **sales_user1** e **marketing_user1** . Num cenário de produção, os utilizadores de domínio são provenientes do seu inquilino do Active Directory.

## <a name="create-hbase-tables-and-import-sample-data"></a>Criar tabelas do HBase e importar dados de exemplo

Você pode usar o SSH para se conectar a clusters HBase e, em seguida, usar o [shell do Apache HBase](https://hbase.apache.org/0.94/book/shell.html) para criar tabelas HBase, inserir dados e consultar dados. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="to-use-the-hbase-shell"></a>Para utilizar a shell de HBase

1. A partir do SSH, execute o seguinte comando do HBase:
   
    ```bash
    hbase shell
    ```

2. Crie uma tabela do HBase `Customers` com famílias de duas colunas: `Name` e `Contact`.

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
4. Exibir o conteúdo da tabela:
    
    ```hbaseshell
    scan 'Customers'
    ```

    ![Saída do Shell HBase do Hadoop do HDInsight](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Criar políticas do Ranger

Crie uma política de Ranger para **sales_user1** e **marketing_user1**.

1. Abra a **IU do Ranger Admin**. Clique em **\<clustername > _hbase** no **HBase**.

   ![IU de administração do Apache Ranger do HDInsight](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. A tela **lista de políticas** exibirá todas as políticas de Ranger criadas para este cluster. Poderá ser apresentada uma política pré-configurada. Clique em **Adicionar nova política**.

    ![Lista de políticas do HBase do Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. Na tela **criar política** , insira os seguintes valores:

   |**Definição**  |**Valor sugerido**  |
   |---------|---------|
   |Nome da Política  |  sales_customers_name_contact   |
   |Tabela do HBase   |  Clientes |
   |Coluna do HBase – família   |  Nome, contato |
   |Coluna do HBase   |  * |
   |Selecionar grupo  | |
   |Selecionar Utilizador  | sales_user1 |
   |Permissões  | Leitura |

   Os carateres universais seguintes podem ser incluídos no nome do tópico:

   * `*` indica zero ou mais ocorrências de caracteres.
   * `?` indica um único caractere.

   ![Política de criação de vendas do Apache Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >Aguarde um momento enquanto o Ranger sincroniza com o Azure AD, se um utilizador de domínio não for preenchido automaticamente em **Selecionar Utilizador**.

4. Clique em **Adicionar** para guardar a política.

5. Clique em **Adicionar Nova Política** e, em seguida, introduza os seguintes valores:

   |**Definição**  |**Valor sugerido**  |
   |---------|---------|
   |Nome da Política  |  marketing_customers_contact   |
   |Tabela do HBase   |  Clientes |
   |Coluna do HBase – família   |  Contacte-nos |
   |Coluna do HBase   |  * |
   |Selecionar grupo  | |
   |Selecionar Utilizador  | marketing_user1 |
   |Permissões  | Leitura |

   ![Política do Apache Ranger criar marketing](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. Clique em **Adicionar** para guardar a política.

## <a name="test-the-ranger-policies"></a>Testar as políticas do Ranger

Com base nas políticas de Ranger configuradas, o **sales_user1** pode exibir todos os dados das colunas nas famílias de colunas `Name` e `Contact`. O **marketing_user1** só pode exibir dados na família de colunas `Contact`.

### <a name="access-data-as-sales_user1"></a>Acessar dados como sales_user1

1. Abra uma nova ligação SSH ao cluster. Use o seguinte comando para entrar no cluster:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Use o comando kinit para alterar para o contexto do nosso usuário desejado.

   ```bash
   kinit sales_user1
   ```

2. Abra o Shell do HBase e verifique a tabela `Customers`.

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. Observe que o usuário de vendas pode exibir todas as colunas da tabela de `Customers`, incluindo as duas colunas na família de coluna `Name`, bem como as cinco colunas na família de colunas `Contact`.

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

### <a name="access-data-as-marketing_user1"></a>Acessar dados como marketing_user1

1. Abra uma nova ligação SSH ao cluster. Use o seguinte comando para entrar como **marketing_user1**:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Use o comando kinit para alterar para o contexto do nosso usuário desejado

   ```bash
   kinit marketing_user1
   ```

1. Abra o Shell do HBase e verifique a tabela `Customers`:

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

1. Observe que o usuário de marketing só pode exibir as cinco colunas da `Contact` família de colunas.

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

   ![Auditoria da política de IU do HDInsight Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você não for continuar a usar este aplicativo, exclua o cluster HBase criado com as seguintes etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Na caixa de **pesquisa** na parte superior, digite **HDInsight**. 
1. Selecione **clusters HDInsight** em **Serviços**.
1. Na lista de clusters HDInsight que aparece, clique em **...** ao lado do cluster que você criou para este tutorial. 
1. Clique em **Eliminar**. Clique em **Sim**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Introdução a um Apache HBase](../hbase/apache-hbase-tutorial-get-started-linux.md)
