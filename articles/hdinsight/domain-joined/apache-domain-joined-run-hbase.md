---
title: Configurar políticas de Apache HBase no HDInsight com o Enterprise Security Package - Azure
description: Saiba como configurar políticas do Apache Ranger para o HBase no HDInsight do Azure com o Enterprise Security Package.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: tutorial
ms.date: 02/01/2019
ms.openlocfilehash: 1421b142fbca83d2de46f52f8390d0c25f22780c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117290"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Tutorial: Configurar políticas de Apache HBase no HDInsight com o Enterprise Security Package (pré-visualização)

Saiba como configurar políticas do Apache Ranger para clusters do HBase do Apache de pacote de segurança da empresa (ESP). Os clusters do ESP estão ligados a um domínio, permitindo que os utilizadores sejam autenticados com credenciais do domínio. Neste tutorial, vai criar duas políticas do Ranger para restringir o acesso para famílias de colunas diferentes numa tabela de HBase.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar utilizadores de domínio
> * Criar políticas do Ranger
> * Criar tabelas num cluster do HBase
> * Testar as políticas do Ranger

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Inicie sessão no [portal do Azure](https://portal.azure.com/).

* Criar uma [cluster de HDInsight HBase com o Enterprise Security Package](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Ligar à IU do Apache Ranger Admin

1. Num browser, ligue à interface de utilizador do Ranger Admin através do URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Não se esqueça de alterar `<ClusterName>` para o nome do cluster do HBase.

    > [!NOTE]  
    > As credenciais do Ranger não são iguais às credenciais de cluster do Hadoop. Para impedir que os browsers utilizem credenciais em cache do Hadoop, utilize uma nova janela do browser InPrivate para ligar à IU do Ranger Admin.

2. Inicie sessão com as suas credenciais de administrador do Azure Active Directory (AD). As credenciais de administrador do Azure AD não iguais às credenciais de cluster do HDInsight ou às credenciais SSH de nó HDInsight do Linux.

## <a name="create-domain-users"></a>Criar utilizadores de domínio

Visite [criar um cluster do HDInsight com o Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds), para saber como criar o **sales_user1** e **marketing_user1** utilizadores de domínio. Num cenário de produção, os utilizadores de domínio são provenientes do seu inquilino do Active Directory.

## <a name="create-hbase-tables-and-import-sample-data"></a>Criar tabelas de HBase e importar dados de exemplo

Pode utilizar o SSH para ligar a HBase clusters e, em seguida, utilizar [Shell do HBase Apache](https://hbase.apache.org/0.94/book/shell.html) para criar tabelas de HBase, inserir dados e consultar dados. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="to-use-the-hbase-shell"></a>Para utilizar a shell de HBase

1. A partir do SSH, execute o seguinte comando do HBase:
   
    ```bash
    hbase shell
    ```

2. Criar uma tabela de HBase `Customers` com duas famílias de coluna: `Name` e `Contact`.

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
    scan 'Contacts'
    ```
    ![HDInsight Hadoop HBase shell](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Criar políticas do Ranger

Criar uma política Ranger **sales_user1** e **marketing_user1**.

1. Abra a **IU do Ranger Admin**. Clique em  **\<ClusterName > _hbase** sob **HBase**.

   ![IU do Apache Ranger Admin](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. O **lista de políticas** tela exibirá todas as políticas do Ranger criadas para este cluster. Poderá ser apresentada uma política pré-configurada. Clique em **Adicionar nova política**.

    ![Criar Política da IU do Apache Ranger Admin](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. Sobre o **criar política** ecrã, introduza os seguintes valores:

   |**Definição**  |**Valor sugerido**  |
   |---------|---------|
   |Nome da Política  |  sales_customers_name_contact   |
   |Tabela de HBase   |  Clientes |
   |Família de colunas de HBase   |  Nome do contacto |
   |HBase Column   |  * |
   |Selecionar Grupo  | |
   |Selecionar Utilizador  | sales_user1 |
   |Permissões  | Leitura |

   Os carateres universais seguintes podem ser incluídos no nome do tópico:

   * `*` indica ocorrências de zero ou mais dos caracteres.
   * `?` indica o caractere único.

   ![Criar Política da IU do Apache Ranger Admin](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >Aguarde um momento enquanto o Ranger sincroniza com o Azure AD, se um utilizador de domínio não for preenchido automaticamente em **Selecionar Utilizador**.

4. Clique em **Adicionar** para guardar a política.

5. Clique em **Adicionar Nova Política** e, em seguida, introduza os seguintes valores:

   |**Definição**  |**Valor sugerido**  |
   |---------|---------|
   |Nome da Política  |  marketing_customers_contact   |
   |Tabela de HBase   |  Clientes |
   |Família de colunas de HBase   |  Contacto |
   |HBase Column   |  * |
   |Selecionar Grupo  | |
   |Selecionar Utilizador  | marketing_user1 |
   |Permissões  | Leitura |

   ![Criar Política da IU do Apache Ranger Admin](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. Clique em **Adicionar** para guardar a política.

## <a name="test-the-ranger-policies"></a>Testar as políticas do Ranger

Com base em políticas do Ranger configuradas, **sales_user1** pode ver todos os dados das colunas em ambas as `Name` e `Contact` famílias de colunas. O **marketing_user1** apenas pode ver os dados no `Contact` família de colunas.

### <a name="access-data-as-salesuser1"></a>Aceder a dados como sales_user1

1. Abra uma nova ligação SSH ao cluster. Utilize o seguinte comando para iniciar sessão para o cluster:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Utilize o comando de kinit para alterar para o contexto de nosso usuário que desejarem.

   ```bash
   kinit sales_user1
   ```

2. Abra a shell de hbase e a tabela de análise `Customers`.

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. Tenha em atenção que o usuário de vendas pode ver todas as colunas do `Customers` tabela, incluindo as duas colunas no `Name` família de colunas, bem como as cinco colunas no `Contact` família de colunas.

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

### <a name="access-data-as-marketinguser1"></a>Aceder a dados como marketing_user1

1. Abra uma nova ligação SSH ao cluster. Utilize o seguinte comando para iniciar sessão como **marketing_user1**:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Utilize o comando de kinit para alterar para o contexto de nosso utilizador pretendido

   ```bash
   kinit marketing_user1
   ```

2. Abra a shell de hbase e a tabela de análise `Customers`:

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

3. Tenha em atenção que o utilizador de marketing pode visualizar apenas cinco colunas do `Contact` família de colunas.

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

9. Veja os eventos de acesso de auditoria da IU do Ranger.

   ![Auditoria à Política da IU do Ranger](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, elimine o cluster do HBase que criou com os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Na **pesquisa** caixa na parte superior, tipo **HDInsight**. 
1. Selecione **clusters do HDInsight** sob **serviços**.
1. Na lista de clusters do HDInsight que aparece, clique o **...**  junto do cluster que criou para este tutorial. 
1. Clique em **Eliminar**. Clique em **Sim**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Começar com uma Apache HBase](../hbase/apache-hbase-tutorial-get-started-linux.md)
