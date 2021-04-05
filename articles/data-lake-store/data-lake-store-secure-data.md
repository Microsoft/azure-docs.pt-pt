---
title: Segurança de dados armazenados em Azure Data Lake Storage Gen1 | Microsoft Docs
description: Saiba como proteger dados na Azure Data Lake Storage Gen1 usando grupos e listas de controlo de acesso
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: ac7666f4c4e68d24499f9c097dc9bd021d270355
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370700"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Proteger dados armazenados no Azure Data Lake Storage Gen1
A obtenção de dados na Azure Data Lake Storage Gen1 é uma abordagem em três etapas.  Tanto o controlo de acesso baseado em funções Azure (Azure RBAC) como as listas de controlo de acesso (ACLs) devem ser definidos para permitir o acesso total aos dados dos utilizadores e grupos de segurança.

1. Comece por criar grupos de segurança no Azure Ative Directory (Azure AD). Estes grupos de segurança são utilizados para implementar o controlo de acesso baseado em funções Azure (Azure RBAC) no portal Azure. Para mais informações, consulte [a Azure RBAC.](../role-based-access-control/role-assignments-portal.md)
2. Atribua os grupos de segurança AZure AD à conta da Data Lake Storage Gen1. Isto controla o acesso à conta de Data Lake Storage Gen1 a partir do portal e operações de gestão a partir do portal ou APIs.
3. Atribua os grupos de segurança AZure AD como listas de controlo de acesso (ACLs) no sistema de ficheiros Data Lake Storage Gen1.
4. Além disso, também pode definir uma gama de endereços IP para clientes que podem aceder aos dados na Data Lake Storage Gen1.

Este artigo fornece instruções sobre como usar o portal Azure para executar as tarefas acima. Para obter informações aprofundadas sobre como a Data Lake Storage Gen1 implementa a segurança na conta e no nível de dados, consulte [a Segurança no Azure Data Lake Storage Gen1](data-lake-store-security-overview.md). Para obter informações sobre como os ACLs são implementados na Data Lake Storage Gen1, consulte [a visão geral do controlo de acesso na Data Lake Storage Gen1](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta gen1 de armazenamento de data lake.** Para instruções sobre como criar um, consulte [Começar com a Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Criar grupos de segurança no Azure Ative Directory
Para obter instruções sobre como criar grupos de segurança Azure AD e como adicionar utilizadores ao grupo, consulte [a Managing groups in Azure Ative Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Pode adicionar tanto os utilizadores como outros grupos a um grupo em Azure AD utilizando o portal Azure. No entanto, para adicionar um principal de serviço a um grupo, utilize [o módulo PowerShell da Azure AD](../active-directory/enterprise-users/groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>Atribuir utilizadores ou grupos de segurança às contas Gen1 de armazenamento de data lake
Quando atribui utilizadores ou grupos de segurança às contas da Data Lake Storage Gen1, controla o acesso às operações de gestão da conta utilizando o portal Azure e as APIs do Gestor de Recursos Azure. 

1. Abra uma conta gen1 de armazenamento de data lake. A partir do painel esquerdo, clique em **Todos os recursos**, e depois a partir da lâmina de todos os recursos, clique no nome da conta a que pretende atribuir um utilizador ou grupo de segurança.

2. Na sua lâmina de conta Gen1 de armazenamento de data lake, clique em **Controlo de Acesso (IAM)**. A lâmina por defeito lista os proprietários de subscrição como o proprietário.
   
    ![Atribuir grupo de segurança à conta Gen1 de armazenamento de dados do Azure Data Lake](./media/data-lake-store-secure-data/adl.select.user.icon1.png "Atribuir grupo de segurança à conta Gen1 de armazenamento de dados do Azure Data Lake")

3. Na lâmina **do Controlo de Acesso (IAM),** clique em **Adicionar** para abrir a lâmina **de permissões Adicionar.** Na lâmina **de permissões Adicionar,** selecione uma **Função** para o utilizador/grupo. Procure o grupo de segurança que criou anteriormente no Azure Ative Directory e selecione-o. Se tiver muitos utilizadores e grupos para pesquisar, utilize a caixa de texto **Select** para filtrar o nome do grupo. 
   
    ![Adicione uma função para o utilizador](./media/data-lake-store-secure-data/adl.add.user.1.png "Adicione uma função para o utilizador")
   
    A função **Proprietário** e **Colaboradora** fornece acesso a uma variedade de funções administrativas na conta do lago de dados. Para os utilizadores que interagem com os dados no lago de dados mas que ainda precisam de visualizar informações de gestão de conta, pode adicioná-los à função **Reader.** O âmbito destas funções limita-se às operações de gestão relacionadas com a conta Da Gen1 de Armazenamento de Data Lake.
   
    Para operações de dados, as permissões individuais do sistema de ficheiros definem o que os utilizadores podem fazer. Portanto, um utilizador com uma função Reader só pode ver as definições administrativas associadas à conta, mas pode potencialmente ler e escrever dados com base nas permissões do sistema de ficheiros que lhes são atribuídas. As permissões do sistema de ficheiros De armazenamento de dados Gen1 são descritas no [Grupo de Segurança Desemtribuída como ACLs ao sistema de ficheiros Azure Data Lake Storage Gen1](#filepermissions).

    > [!IMPORTANT]
    > Apenas a função **Proprietário** permite automaticamente o acesso ao sistema de ficheiros. O **Contribuinte**, **Leitor,** e todas as outras funções requerem ACLs para permitir qualquer nível de acesso a pastas e ficheiros.  A função **Proprietário** fornece permissões de ficheiros e pastas super-utilizadores que não podem ser ultrapassadas através de ACLs. Para obter mais informações sobre como as políticas do Azure RBAC mapeiam o acesso aos dados, consulte [o Azure RBAC para gestão de conta.](data-lake-store-security-overview.md#azure-rbac-for-account-management)

4. Se pretender adicionar um grupo/utilizador que não esteja listado na lâmina de **permissões Adicionar,** pode convidá-los digitando o seu endereço de e-mail na caixa de texto **Select** e, em seguida, selecionando-os da lista.
   
    ![Adicione um grupo de segurança](./media/data-lake-store-secure-data/adl.add.user.2.png "Adicione um grupo de segurança")
   
5. Clique em **Guardar**. Deve ver o grupo de segurança adicionado como mostrado abaixo.
   
    ![Grupo de segurança adicionado](./media/data-lake-store-secure-data/adl.add.user.3.png "Grupo de segurança adicionado")

6. O seu grupo de utilizador/segurança tem agora acesso à conta Desear o Armazém de Dados Gen1. Se quiser fornecer acesso a utilizadores específicos, pode adicioná-los ao grupo de segurança. Da mesma forma, se pretender revogar o acesso a um utilizador, pode removê-los do grupo de segurança. Também pode atribuir vários grupos de segurança a uma conta. 

## <a name="assign-users-or-security-groups-as-acls-to-the-data-lake-storage-gen1-file-system"></a><a name="filepermissions"></a>Atribuir utilizadores ou grupos de segurança como ACLs ao sistema de ficheiros Data Lake Storage Gen1
Ao atribuir grupos de utilizador/segurança ao sistema de ficheiros Data Lake Storage Gen1, definiu o controlo de acesso sobre os dados armazenados na Data Lake Storage Gen1.

1. Na sua lâmina de conta Gen1 de armazenamento de data lake, clique em **Data Explorer**.
   
    ![Ver dados através do Data Explorer](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Ver dados através do Data Explorer")
2. Na lâmina **Data Explorer,** clique na pasta para a qual pretende configurar o ACL e, em seguida, clique em **Access**. Para atribuir ACLs a um ficheiro, primeiro clique no ficheiro para pré-visualizar e, em seguida, clique em **Access** a partir da lâmina **de pré-visualização** de ficheiros.
   
    ![Definir ACLs no sistema de ficheiros Gen1 de armazenamento de dados do lago de dados](./media/data-lake-store-secure-data/adl.acl.1.png "Definir ACLs no sistema de ficheiros Gen1 de armazenamento de dados do lago de dados")
3. A lâmina **Access** lista os proprietários e permissões atribuídas já atribuídas à raiz. Clique no ícone **Adicionar** para adicionar ACLs de acesso adicionais.
    > [!IMPORTANT]
    > A definição de permissões de acesso para um único ficheiro não permite necessariamente um acesso de utilizador/grupo a esse ficheiro. O caminho para o ficheiro deve estar acessível ao utilizador/grupo designado. Para obter mais informações e exemplos, consulte [cenários comuns relacionados com permissões.](data-lake-store-access-control.md#common-scenarios-related-to-permissions)
   
    ![Listar acesso padrão e personalizado](./media/data-lake-store-secure-data/adl.acl.2.png "Listar acesso padrão e personalizado")
   
   * Os **Proprietários** e **Todos os outros** fornecem acesso ao estilo UNIX, onde especifica ler, escrever, executar (rwx) a três classes de utilizadores distintas: proprietário, grupo e outros.
   * **As permissões atribuídas** correspondem aos ACLs POSIX que lhe permitem definir permissões para utilizadores ou grupos específicos nomeados para além do proprietário ou grupo do ficheiro. 
     
     Para mais informações, consulte [OS ACLs HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Para obter mais informações sobre como os ACLs são implementados na Data Lake Storage Gen1, consulte [o Controlo de Acesso na Data Lake Storage Gen1](data-lake-store-access-control.md).
4. Clique no ícone **Adicionar** para abrir a lâmina **de permissões de atribuição.** Nesta lâmina, clique **em Selecionar utilizador ou grupo**, e, em seguida, em Select user ou **group** blade, procure o grupo de segurança que criou anteriormente no Azure Ative Directory. Se tiver muitos grupos para pesquisar, use a caixa de texto na parte superior para filtrar o nome do grupo. Clique no grupo que pretende adicionar e, em seguida, clique em **Selecionar**.
   
    ![Adicionar um grupo](./media/data-lake-store-secure-data/adl.acl.3.png "Adicionar um grupo")
5. Clique **em Selecionar permissões,** selecione as permissões, se as permissões devem ser aplicadas de forma recorrente e se pretende atribuir as permissões como ACL de acesso, ACL predefinido ou ambos. Clique em **OK**.
   
    ![Screenshot da lâmina de permissões 'Atribuir' com a opção de permissões Select chamada e a lâmina de permissões Select com a opção Ok chamada.](./media/data-lake-store-secure-data/adl.acl.4.png "Atribuir permissões ao grupo")
   
    Para obter mais informações sobre permissões em Data Lake Storage Gen1 e ACLs predefinidos/acesso, consulte [o Controlo de Acesso na Data Lake Storage Gen1](data-lake-store-access-control.md).
6. Depois de clicar em **Ok** na lâmina **de permissões Select,** o grupo recém-adicionado e as permissões associadas serão agora listados na lâmina **de Acesso.**
   
    ![Screenshot da lâmina de acesso com a opção de Engenharia de Dados chamada.](./media/data-lake-store-secure-data/adl.acl.5.png "Atribuir permissões ao grupo")
   
   > [!IMPORTANT]
   > Na versão atual, pode ter até 28 entradas sob **permissões atribuídas**. Se pretender adicionar mais de 28 utilizadores, deve criar grupos de segurança, adicionar utilizadores a grupos de segurança, adicionar acesso a esses grupos de segurança para a conta Desadição de Armazenamento de Dados Gen1.
   > 
   > 
7. Se necessário, também pode modificar as permissões de acesso depois de ter adicionado o grupo. Limpe ou selecione a caixa de verificação para cada tipo de permissão (Ler, Escrever, Executar) com base na questão de saber se pretende remover ou atribuir essa permissão ao grupo de segurança. Clique em **Guardar** para guardar as alterações ou **descartar** para desfazer as alterações.

## <a name="set-ip-address-range-for-data-access"></a>Definir intervalo de endereços IP para acesso a dados
A Data Lake Storage Gen1 permite-lhe bloquear ainda mais o acesso à sua loja de dados ao nível da rede. Pode ativar firewall, especificar um endereço IP ou definir um intervalo de endereço IP para os seus clientes de confiança. Uma vez ativados, apenas os clientes que tenham os endereços IP dentro do alcance definido podem ligar-se à loja.

![Definições de firewall e acesso IP](./media/data-lake-store-secure-data/firewall-ip-access.png "Definições de firewall e endereço IP")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Remova grupos de segurança para uma conta Gen1 de armazenamento de dados
Quando remove grupos de segurança das contas da Data Lake Storage Gen1, está apenas a alterar o acesso às operações de gestão da conta utilizando o portal Azure e as APIs do Gestor de Recursos Azure.  

O acesso aos dados é inalterado e ainda é gerido pelos ACLs de acesso.  A exceção a isso são utilizadores/grupos na função Proprietários.  Os utilizadores/grupos removidos da função Proprietários já não são super utilizadores e o seu acesso recai sobre as definições da ACL. 

1. Na sua lâmina de conta Gen1 de armazenamento de data lake, clique em **Controlo de Acesso (IAM)**. 
   
    ![Atribuir grupo de segurança à conta Desemarse de Armazenamento de Dados Gen1](./media/data-lake-store-secure-data/adl.select.user.icon.png "Atribuir grupo de segurança à conta Desemarse de Armazenamento de Dados Gen1")
2. Na lâmina **do Controlo de Acesso (IAM),** clique no grupo de segurança que pretende remover. Clique em **Remover**.
   
    ![Grupo de segurança removido](./media/data-lake-store-secure-data/adl.remove.group.png "Grupo de segurança removido")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>Remover ACLs do grupo de segurança de um sistema de ficheiros Gen1 de armazenamento de dados
Quando remove os ACLs do grupo de segurança de um sistema de ficheiros Data Lake Storage Gen1, altera o acesso aos dados na conta Da Gen1 de Armazenamento de Data Lake.

1. Na sua lâmina de conta Gen1 de armazenamento de data lake, clique em **Data Explorer**.
   
    ![Criar diretórios na conta Desarmas de Armazenamento de Dados Gen1](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Criar diretórios na conta Desarmas de Armazenamento de Dados Gen1")
2. Na lâmina **Data Explorer,** clique na pasta para a qual pretende remover o ACL e, em seguida, clique em **Access**. Para remover ACLs para um ficheiro, primeiro clique no ficheiro para pré-visualizar e, em seguida, clique em **Access** a partir da lâmina **de pré-visualização** de ficheiros. 
   
    ![Definir ACLs no sistema de ficheiros Gen1 de armazenamento de dados do lago de dados](./media/data-lake-store-secure-data/adl.acl.1.png "Definir ACLs no sistema de ficheiros Gen1 de armazenamento de dados do lago de dados")
3. Na lâmina **Access,** clique no grupo de segurança que pretende remover. Na lâmina de detalhes do **Acesso,** clique em **Remover**.
   
    ![Screenshot da lâmina Access com a opção de Engenharia de Dados chamada e a lâmina de detalhes de acesso com a opção Remover chamada.](./media/data-lake-store-secure-data/adl.remove.acl.png "Atribuir permissões ao grupo")

## <a name="see-also"></a>Ver também
* [Visão geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Copiar dados de Azure Storage Blobs para Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Use Azure Data Lake Analytics com data lake storage gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight com data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Introdução ao Armazenamento do Data Lake Ger1 através do PowerShell](data-lake-store-get-started-powershell.md)
* [Começar com data lake storage Gen1 usando .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Aceder a registos de diagnóstico para Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md)
