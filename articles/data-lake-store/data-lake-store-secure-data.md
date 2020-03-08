---
title: Proteger os dados armazenados no Azure Data Lake Storage Gen1  Microsoft Docs
description: Saiba como proteger dados em Azure Data Lake Storage Gen1 usando grupos e listas de controlo de acesso
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: cebdff5ed233516683df3330e8fd3332ded664e5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361397"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Obtenção de dados armazenados em Azure Data Lake Storage Gen1
A obtenção de dados no Azure Data Lake Storage Gen1 é uma abordagem em três etapas.  Tanto o controlo de acesso baseado em funções (RBAC) como as listas de controlo de acesso (ACLs) devem ser definidos para permitir o acesso total aos dados dos utilizadores e dos grupos de segurança.

1. Comece por criar grupos de segurança no Azure Ative Directory (AAD). Estes grupos de segurança são utilizados para implementar o controlo de acesso baseado em funções (RBAC) no portal Azure. Para mais informações, consulte o [Controlo de Acesso baseado em Role no Microsoft Azure](../role-based-access-control/role-assignments-portal.md).
2. Designar os grupos de segurança da AAD na conta Data Lake Storage Gen1. Isto controla o acesso à conta Data Lake Storage Gen1 a partir do portal e operações de gestão a partir do portal ou APIs.
3. Atribuir os grupos de segurança AAD como listas de controlo de acesso (ACLs) no sistema de ficheiros Data Lake Storage Gen1.
4. Além disso, também pode definir uma gama de endereços IP para clientes que podem aceder aos dados em Data Lake Storage Gen1.

Este artigo fornece instruções sobre como usar o portal Azure para executar as tarefas acima referidas. Para obter informações aprofundadas sobre como o Data Lake Storage Gen1 implementa a segurança a nível de conta e dados, consulte a Segurança no Armazenamento de [Lagos De dados Azure Gen1](data-lake-store-security-overview.md). Para obter informações sobre como os ACLs são implementados em Data Lake Storage Gen1, consulte [a visão geral do Controlo de Acesso em Data Lake Storage Gen1](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição do Azure**. Veja [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta Gen1 de Armazenamento de Lago**de Dados. Para obter instruções sobre como criar um, consulte [Começar com Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Criar grupos de segurança no Diretório Ativo azure
Para obter instruções sobre como criar grupos de segurança AAD e como adicionar utilizadores ao grupo, consulte Gerir grupos de [segurança no Diretório Ativo do Azure](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Pode adicionar utilizadores e outros grupos a um grupo em Azure AD utilizando o portal Azure. No entanto, para adicionar um principal de serviço a um grupo, utilize o [módulo PowerShell da Azure AD](../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>Atribuir utilizadores ou grupos de segurança às contas da Gen1 de Armazenamento de Data Lake
Ao atribuir utilizadores ou grupos de segurança a contas da Data Lake Storage Gen1, controla o acesso às operações de gestão na conta utilizando o portal Azure e o Azure Resource Manager APIs. 

1. Abra uma conta Data Lake Storage Gen1. A partir do painel esquerdo, clique em **Todos os recursos**, e depois na lâmina de todos os recursos, clique no nome da conta a que pretende atribuir um utilizador ou grupo de segurança.

2. Na sua lâmina de conta Data Lake Storage Gen1, clique em Controle de **Acesso (IAM)** . A lâmina por padrão lista os proprietários de subscrição como proprietário.
   
    ![Atribuir grupo de segurança à conta Azure Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.select.user.icon1.png "Atribuir grupo de segurança à conta Azure Data Lake Storage Gen1")

3. Na lâmina de Controlo de **Acesso (IAM),** clique em **Adicionar** para abrir a lâmina **de permissões Adicionar.** Na lâmina **de permissões Adicionar,** selecione uma **Função** para o utilizador/grupo. Procure o grupo de segurança que criou anteriormente no Azure Ative Directory e selecione-o. Se tiver muitos utilizadores e grupos para procurar, utilize a caixa de texto **Select** para filtrar o nome do grupo. 
   
    ![Adicione um papel para o utilizador](./media/data-lake-store-secure-data/adl.add.user.1.png "Adicione um papel para o utilizador")
   
    A função **Proprietário** e **Contribuinte** fornece acesso a uma variedade de funções administrativas na conta do lago de dados. Para os utilizadores que irão interagir com dados no lago de dados mas ainda precisam de ver informações de gestão de contas, pode adicioná-los à função **De Leitor.** O âmbito destas funções limita-se às operações de gestão relacionadas com a conta Data Lake Storage Gen1.
   
    Para operações de dados, as permissões individuais do sistema de ficheiros definem o que os utilizadores podem fazer. Portanto, um utilizador com uma função de Leitor só pode ver configurações administrativas associadas à conta, mas pode potencialmente ler e escrever dados com base em permissões do sistema de ficheiros que lhes são atribuídas. As permissões do sistema de ficheiros Data Lake Storage Gen1 são descritas no grupo de [segurança Atribuir como ACLs para o sistema de ficheiros Azure Data Lake Storage Gen1](#filepermissions).

    > [!IMPORTANT]
    > Apenas a função **Proprietário** permite automaticamente o acesso ao sistema de ficheiros. O **Colaborador,** **Leitor,** e todas as outras funções requerem ACLs para permitir qualquer nível de acesso a pastas e ficheiros.  A função **Proprietário** fornece permissões de ficheiros e pastas de super-utilizador que não podem ser ultrapassadas através de ACLs. Para obter mais informações sobre como as políticas rBAC mapeiam para o acesso de dados, consulte [rBAC para gestão de conta](data-lake-store-security-overview.md#rbac-for-account-management).

4. Se pretender adicionar um grupo/utilizador que não esteja listado na lâmina de **permissões Add,** pode convidá-los digitando o seu endereço de e-mail na caixa de texto **Select** e, em seguida, selecionando-os da lista.
   
    ![Adicione um grupo de segurança](./media/data-lake-store-secure-data/adl.add.user.2.png "Adicione um grupo de segurança")
   
5. Clique em **Guardar**. Deve ver o grupo de segurança adicionado como mostrado abaixo.
   
    ![Grupo de segurança adicionado](./media/data-lake-store-secure-data/adl.add.user.3.png "Grupo de segurança adicionado")

6. O seu utilizador/grupo de segurança tem agora acesso à conta Data Lake Storage Gen1. Se pretender fornecer acesso a utilizadores específicos, pode adicioná-los ao grupo de segurança. Da mesma forma, se quiser revogar o acesso a um utilizador, pode removê-los do grupo de segurança. Também pode atribuir vários grupos de segurança a uma conta. 

## <a name="filepermissions"></a>Atribuir utilizadores ou grupos de segurança como ACLs ao sistema de ficheiros Gen1 de Armazenamento de Data Lake
Ao atribuir grupos de utilizador/segurança ao sistema de ficheiros Data Lake Storage Gen1, definiu o controlo de acesso dos dados armazenados no Data Lake Storage Gen1.

1. Na sua lâmina de conta Data Lake Storage Gen1, clique no **Data Explorer**.
   
    ![Ver dados via Data Explorer](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Ver dados via Data Explorer")
2. Na lâmina **do Data Explorer,** clique na pasta para a qual pretende configurar o ACL e, em seguida, clique em **Access**. Para atribuir OS ACLs a um ficheiro, tem primeiro de clicar no ficheiro para pré-visualizar e, em seguida, clicar em **Aceder** a partir da lâmina de **pré-visualização** do ficheiro.
   
    ![Delineie ACLs no sistema de ficheiros Gen1 de Armazenamento de Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Delineie ACLs no sistema de ficheiros Gen1 de Armazenamento de Data Lake")
3. A lâmina **de acesso** lista os proprietários e as permissões atribuídas já atribuídas à raiz. Clique no ícone **Adicionar** para adicionar ACLs de acesso adicionais.
    > [!IMPORTANT]
    > Definir permissões de acesso para um único ficheiro não concede necessariamente a um utilizador/grupo acesso a esse ficheiro. O caminho para o ficheiro deve ser acessível ao utilizador/grupo designado. Para mais informações e exemplos, consulte [cenários comuns relacionados com permissões](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Lista de acesso sintetizado e personalizado](./media/data-lake-store-secure-data/adl.acl.2.png "Lista de acesso sintetizado e personalizado")
   
   * Os **Proprietários** e **Todos os outros** fornecem acesso ao estilo UNIX, onde especifica ler, escrever, executar (rwx) a três classes distintas de utilizadores: proprietário, grupo e outros.
   * **As permissões atribuídas** correspondem aos ACLs POSIX que lhe permitem definir permissões para utilizadores ou grupos específicos nomeados para além do proprietário ou grupo do ficheiro. 
     
     Para mais informações, consulte [HDFS ACLs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Para obter mais informações sobre como os ACLs são implementados em Data Lake Storage Gen1, consulte [Control de Acesso em Data Lake Storage Gen1](data-lake-store-access-control.md).
4. Clique no ícone **Adicionar** para abrir a lâmina de **permissões de atribuição.** Nesta lâmina, clique em **Selecionar o utilizador ou grupo**, e depois em Selecionar o utilizador ou a lâmina de **grupo,** procure o grupo de segurança que criou anteriormente no Diretório Ativo Azure. Se tiver muitos grupos para procurar, utilize a caixa de texto na parte superior para filtrar o nome do grupo. Clique no grupo que pretende adicionar e, em seguida, clique em **Selecionar**.
   
    ![Adicione um grupo](./media/data-lake-store-secure-data/adl.acl.3.png "Adicionar um grupo")
5. Clique em **Selecionar permissões**, selecione as permissões, se as permissões devem ser aplicadas de forma recorrente e se pretende atribuir as permissões como ACL de acesso, ACL padrão ou ambos. Clique em **OK**.
   
    ![Atribuir permissões para agrupar](./media/data-lake-store-secure-data/adl.acl.4.png "Atribuir permissões para agrupar")
   
    Para obter mais informações sobre permissões em Data Lake Storage Gen1, e Atpre/Access ACLs, consulte [Control de Acesso em Data Lake Storage Gen1](data-lake-store-access-control.md).
6. Depois de clicar em **Ok** na lâmina de **permissões Select,** o grupo recém-adicionado e as permissões associadas serão agora listados na lâmina **de Acesso.**
   
    ![Atribuir permissões para agrupar](./media/data-lake-store-secure-data/adl.acl.5.png "Atribuir permissões para agrupar")
   
   > [!IMPORTANT]
   > No lançamento atual, pode ter até 28 entradas ao abrigo de **permissões atribuídas**. Se pretender adicionar mais de 28 utilizadores, deve criar grupos de segurança, adicionar utilizadores a grupos de segurança, adicionar acesso a esses grupos de segurança para a conta Data Lake Storage Gen1.
   > 
   > 
7. Se necessário, também pode modificar as permissões de acesso depois de ter adicionado o grupo. Limpe ou selecione a caixa de verificação para cada tipo de permissão (Ler, Escrever, Executar) com base no facto de pretender remover ou atribuir essa permissão ao grupo de segurança. Clique em **Guardar** para guardar as alterações ou **descartar** para desfazer as alterações.

## <a name="set-ip-address-range-for-data-access"></a>Definir intervalo de endereçoIP para acesso a dados
Data Lake Storage Gen1 permite-lhe bloquear ainda mais o acesso à sua loja de dados ao nível da rede. Pode ativar a firewall, especificar um endereço IP ou definir um intervalo de endereçoIP para os seus clientes de confiança. Uma vez ativados, apenas os clientes que possuem os endereços IP dentro do intervalo definido podem ligar-se à loja.

![Definições de firewall e acesso IP](./media/data-lake-store-secure-data/firewall-ip-access.png "Definições de firewall e endereço IP")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Remova os grupos de segurança para uma conta Gen1 de Armazenamento de Data Lake
Ao remover os grupos de segurança das contas da Data Lake Storage Gen1, está apenas a alterar o acesso às operações de gestão na conta utilizando as APIs do Portal Azure e do Gestor de Recursos Azure.  

O acesso aos dados é inalterado e ainda é gerido pelos ACLs de acesso.  A exceção a isto são utilizadores/grupos na função Proprietários.  Os utilizadores/grupos removidos da função Owners deixaram de ser super utilizadores e o seu acesso volta a aceder às definições da ACL. 

1. Na sua lâmina de conta Data Lake Storage Gen1, clique em Controle de **Acesso (IAM)** . 
   
    ![Atribuir grupo de segurança à conta Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.select.user.icon.png "Atribuir grupo de segurança à conta Data Lake Storage Gen1")
2. Na lâmina de Controlo de **Acesso (IAM),** clique nos grupos de segurança que pretende remover. Clique em **Remover**.
   
    ![Grupo de segurança removido](./media/data-lake-store-secure-data/adl.remove.group.png "Grupo de segurança removido")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>Remova os ACLs do grupo de segurança ACLs de um sistema de ficheiros Gen1 de Armazenamento de Data Lake
Ao remover os ACLs do grupo de segurança de um sistema de ficheiros Gen1 de Armazenamento de Data Lake, altere o acesso aos dados na conta Data Lake Storage Gen1.

1. Na sua lâmina de conta Data Lake Storage Gen1, clique no **Data Explorer**.
   
    ![Criar diretórios na conta Gen1 de Armazenamento de Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Criar diretórios na conta Gen1 de Armazenamento de Data Lake")
2. Na lâmina **do Data Explorer,** clique na pasta para a qual pretende remover o ACL e, em seguida, clique em **Access**. Para remover OS ACLs para um ficheiro, tem primeiro de clicar no ficheiro para pré-visualizar e, em seguida, clicar no **Access** a partir da lâmina **de pré-visualização** do ficheiro. 
   
    ![Delineie ACLs no sistema de ficheiros Gen1 de Armazenamento de Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Delineie ACLs no sistema de ficheiros Gen1 de Armazenamento de Data Lake")
3. Na lâmina **De Acesso,** clique no grupo de segurança que pretende remover. Na lâmina de detalhes de **acesso,** clique em **Remover**.
   
    ![Atribuir permissões para agrupar](./media/data-lake-store-secure-data/adl.remove.acl.png "Atribuir permissões para agrupar")

## <a name="see-also"></a>Consulte também
* [Visão geral do Armazenamento de Lagos De Dados Azure Gen1](data-lake-store-overview.md)
* [Copiar dados de Blobs de Armazenamento Azure para Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Use Azure Data Lake Analytics com Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Começar com data lake storage Gen1 usando PowerShell](data-lake-store-get-started-powershell.md)
* [Começar com data lake storage Gen1 usando .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Aceda a registos de diagnóstico para Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md)

