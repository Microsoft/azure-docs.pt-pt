---
title: Proteger dados armazenados na geração 1 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Saiba como proteger os dados na geração 1 de armazenamento do Azure Data Lake através de grupos e listas de controlo de acesso
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
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885677"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Proteger dados armazenados na geração 1 de armazenamento do Azure Data Lake
Proteger dados no Gen1 de armazenamento do Azure Data Lake é uma abordagem de três passos.  Ambos baseada em funções (RBAC) de controlo de acesso e listas de controlo de acesso (ACLs) tem de ser definidas para habilitar completamente o acesso a dados para os utilizadores e grupos de segurança.

1. Comece por criar grupos de segurança no Azure Active Directory (AAD). Estes grupos de segurança são usados para implementar o controlo de acesso baseado em funções (RBAC) no portal do Azure. Para obter mais informações, consulte [controlo de acesso baseado em funções no Microsoft Azure](../role-based-access-control/role-assignments-portal.md).
2. Atribua os grupos de segurança do AAD para a conta de geração 1 de armazenamento do Data Lake. Isso controla o acesso à conta de geração 1 de armazenamento do Data Lake das operações de gestão de portal e do portal ou APIs.
3. Atribua os grupos de segurança do AAD, como listas de controlo de acesso (ACL) no sistema de ficheiros de geração 1 de armazenamento do Data Lake.
4. Além disso, também pode definir um intervalo de endereços IP para clientes que podem acessar os dados na geração 1 de armazenamento do Data Lake.

Este artigo fornece instruções sobre como utilizar o portal do Azure para executar as tarefas acima. Para obter informações detalhadas sobre como geração 1 de armazenamento do Data Lake implementa a segurança ao nível da conta e os dados, consulte [segurança no Azure Data Lake Storage Gen1](data-lake-store-security-overview.md). Para informações mais aprofundadas sobre como as ACLs estão implementadas na geração 1 de armazenamento do Data Lake, veja [descrição geral de controlo de acesso no Data Lake Storage Gen1](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Data Lake Storage Gen1**. Para obter instruções sobre como criar um, consulte [introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Criar grupos de segurança no Azure Active Directory
Para obter instruções sobre como criar grupos de segurança do AAD e como adicionar utilizadores ao grupo, consulte [gestão de grupos de segurança no Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Pode adicionar utilizadores e outros grupos a um grupo no Azure AD no portal do Azure. No entanto, para poder adicionar um principal de serviço a um grupo, utilize [módulo do PowerShell do Azure AD](../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>Atribuir utilizadores ou grupos de segurança para contas de geração 1 de armazenamento do Data Lake
Quando atribui a utilizadores ou grupos de segurança para contas de geração 1 de armazenamento do Data Lake, controlar o acesso às operações de gestão da conta com o portal do Azure e a APIs do Azure Resource Manager. 

1. Abra uma conta de geração 1 de armazenamento do Data Lake. No painel esquerdo, clique em **todos os recursos**e, em seguida, no painel de todos os recursos, clique no nome de conta para o qual pretende atribuir um utilizador ou grupo de segurança.

2. No painel da conta do Data Lake Storage Gen1, clique em **controlo de acesso (IAM)**. O painel por predefinição lista os proprietários de subscrições, como o proprietário.
   
    ![Atribua o grupo de segurança à conta de geração 1 de armazenamento do Azure Data Lake](./media/data-lake-store-secure-data/adl.select.user.icon1.png "atribuir o grupo de segurança à conta de geração 1 de armazenamento do Azure Data Lake")

3. Na **controlo de acesso (IAM)** painel, clique em **Add** para abrir o **adicionar permissões** painel. Na **adicionar permissões** painel, selecione um **função** para o utilizador/grupo. Procure o grupo de segurança que criou anteriormente no Azure Active Directory e selecioná-lo. Se tiver muitos utilizadores e grupos a pesquisa, utilize o **selecione** caixa de texto para filtrar no nome do grupo. 
   
    ![Adicionar uma função para o usuário](./media/data-lake-store-secure-data/adl.add.user.1.png "adicionar uma função do utilizador")
   
    O **proprietário** e **contribuinte** função fornecer acesso a uma variedade de funções de administração sobre a conta do data lake. Para os utilizadores que irão interagir com dados no data lake, mas ainda preciso ver informações de gestão de conta, pode adicioná-los para o **leitor** função. O âmbito destas funções é limitado para as operações de gestão relacionados com a conta de geração 1 de armazenamento do Data Lake.
   
    Para operações de dados, permissões de sistema de arquivos individuais definem o que os utilizadores podem fazer. Por conseguinte, um usuário ter uma função de leitor pode ver apenas as definições administrativas associadas à conta, mas pode potencialmente ler e escrever dados com base nas permissões do sistema de ficheiros atribuídas. Permissões do sistema de ficheiros do Data Lake Storage Gen1 são descritas em [atribuir grupo de segurança como ACLs ao sistema de arquivos de geração 1 de armazenamento do Azure Data Lake](#filepermissions).

    > [!IMPORTANT]
    > Apenas os **proprietário** função ativa automaticamente o acesso do sistema de arquivos. O **contribuinte**, **leitor**, e todas as outras funções exigem ACLs permitir qualquer nível de acesso a pastas e ficheiros.  O **proprietário** função fornece o ficheiro de Superutilizador e permissões de pasta que não podem ser substituídas através das ACLs. Para obter mais informações sobre como o mapeamento de políticas RBAC, acesso a dados, consulte [RBAC para a gestão de conta](data-lake-store-security-overview.md#rbac-for-account-management).

4. Se pretender adicionar um grupo/utilizador em que não esteja listado no **adicionar permissões** painel, poderá convidá-los, escrevendo o respetivo endereço de e-mail no **selecione** caixa de texto e, em seguida, selecionando-os partir da lista.
   
    ![Adicionar um grupo de segurança](./media/data-lake-store-secure-data/adl.add.user.2.png "adicionar um grupo de segurança")
   
5. Clique em **Guardar**. Deverá ver o grupo de segurança adicionado conforme mostrado abaixo.
   
    ![Grupo de segurança adicionado](./media/data-lake-store-secure-data/adl.add.user.3.png "grupo de segurança adicionado")

6. O grupo de segurança do usuário tem agora acesso à conta de geração 1 de armazenamento do Data Lake. Se quiser fornecer acesso a utilizadores específicos, pode adicioná-los ao grupo de segurança. Da mesma forma, se pretender revogar o acesso de um utilizador, pode removê-los do grupo de segurança. Também pode atribuir vários grupos de segurança para uma conta. 

## <a name="filepermissions"></a>Atribuir utilizadores ou grupos de segurança como ACLs ao sistema de arquivos de geração 1 de armazenamento do Data Lake
Ao atribuir utilizador/grupos de segurança para o sistema de ficheiros de geração 1 de armazenamento do Data Lake, definir o controlo de acesso em dados armazenados no Data Lake Storage Gen1.

1. No painel da conta do Data Lake Storage Gen1, clique em **Data Explorer**.
   
    ![Ver os dados através do Data Explorer](./media/data-lake-store-secure-data/adl.start.data.explorer.png "ver os dados através do Explorador de dados")
2. Na **Data Explorer** painel, clique na pasta para o qual pretende configurar a ACL e, em seguida, clique em **acesso**. Para atribuir as ACLs num arquivo, primeiro tem de clicar o ficheiro para pré-visualização e, em seguida, clique em **acesso** partir do **pré-visualização do ficheiro** painel.
   
    ![Definir ACLs no sistema de ficheiros de geração 1 de armazenamento do Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "definir ACLs no sistema de arquivos de geração 1 de armazenamento do Data Lake")
3. O **acesso** painel lista os proprietários e receber permissões já atribuídas para a raiz. Clique nas **adicionar** ícone para adicionar ACLs de acesso adicionais.
    > [!IMPORTANT]
    > Definir permissões de acesso para um único arquivo não necessariamente conceder um acesso de utilizador/grupo a esse ficheiro. O caminho para o ficheiro tem de estar acessível para o utilizador/grupo atribuído. Para obter mais informações e exemplos, consulte [cenários comuns relacionados com as permissões](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Lista de acesso padrão e personalizado](./media/data-lake-store-secure-data/adl.acl.2.png "lista acesso padrão e personalizado")
   
   * O **proprietários** e **todos os outros** fornecer acesso de estilo UNIX, onde pode especificar ler, escrever, executar (rwx) para três classes de utilizador distintos: proprietário, grupo e outras pessoas.
   * **Receber permissões** corresponde ao ACLs POSIX que permitem-lhe definir permissões para utilizadores nomeados específicos ou grupos para além do arquivo proprietário ou grupo. 
     
     Para obter mais informações, consulte [HDFS ACLs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Para obter mais informações sobre como as ACLs estão implementadas na geração 1 de armazenamento do Data Lake, veja [controlo de acesso no Data Lake Storage Gen1](data-lake-store-access-control.md).
4. Clique nas **Add** ícone para abrir o **atribuir permissões** painel. Neste painel, clique em **selecionar utilizador ou grupo**e, em seguida, no **selecionar utilizador ou grupo** painel, procure o grupo de segurança que criou anteriormente no Azure Active Directory. Se tiver muitos grupos a pesquisa, utilize a caixa de texto na parte superior para filtrar no nome do grupo. Clique no grupo que pretende adicionar e, em seguida, clique em **selecione**.
   
    ![Adicionar um grupo](./media/data-lake-store-secure-data/adl.acl.3.png "adicionar um grupo")
5. Clique em **selecionar permissões**, selecione as permissões, se as permissões devem ser aplicadas recursivamente e se pretende atribuir as permissões como uma ACL, de acesso predefinida de ACL, ou ambos. Clique em **OK**.
   
    ![Atribuir permissões para agrupar](./media/data-lake-store-secure-data/adl.acl.4.png "atribuir permissões ao grupo")
   
    Para obter mais informações sobre as permissões no Data Lake Storage Gen1 e ACLs de acesso/predefinido, consulte [controlo de acesso no Data Lake Storage Gen1](data-lake-store-access-control.md).
6. Depois de clicar em **Ok** no **selecionar permissões** painel, o grupo recentemente adicionado e permissões associadas agora serão listadas no **acesso** painel.
   
    ![Atribuir permissões para agrupar](./media/data-lake-store-secure-data/adl.acl.5.png "atribuir permissões ao grupo")
   
   > [!IMPORTANT]
   > Na versão atual, pode ter até 28 entradas em **receber permissões**. Se quiser adicionar mais do que 28 utilizadores, deve criar grupos de segurança, adicionar utilizadores a grupos de segurança, adicione fornecer acesso a esses grupos de segurança para a conta de geração 1 de armazenamento do Data Lake.
   > 
   > 
7. Se for necessário, também pode modificar as permissões de acesso depois de ter adicionado o grupo. Desmarque ou selecione a caixa de verificação para cada tipo de permissão (leitura, escrita, execução) com base em se desejar remover ou o atribuir essa permissão para o grupo de segurança. Clique em **salvar** para guardar as alterações, ou **descartar** para desfazer as alterações.

## <a name="set-ip-address-range-for-data-access"></a>Definir o intervalo de endereços IP para acesso a dados
Geração 1 de armazenamento do Data Lake permite-lhe para bloquear melhor acesso ao seu arquivo de dados no nível de rede. Pode ativar a firewall, especifique um endereço IP ou definir um intervalo de endereços IP para os seus clientes fidedignos. Uma vez ativada, podem ligar apenas os clientes que tenham endereços IP dentro do intervalo definido para o arquivo.

![As definições da firewall e IP de acesso](./media/data-lake-store-secure-data/firewall-ip-access.png "endereço IP e definições da Firewall")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Remover grupos de segurança para uma conta de geração 1 de armazenamento do Data Lake
Quando remover grupos de segurança de contas de geração 1 de armazenamento do Data Lake, apenas estão mudando o acesso para as operações de gestão da conta com o Portal do Azure e as APIs do Azure Resource Manager.  

Acesso a dados não é alterado e ainda é gerenciado pelo ACLs de acesso.  A exceção são os utilizadores/grupos na função de proprietários.  Os utilizadores/grupos removidos da função proprietários deixaram de ser superutilizadores e o acesso vai para as definições da ACL de acesso. 

1. No painel da conta do Data Lake Storage Gen1, clique em **controlo de acesso (IAM)**. 
   
    ![Atribua o grupo de segurança à conta do Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.select.user.icon.png "atribuir o grupo de segurança à conta de geração 1 de armazenamento do Data Lake")
2. Na **controlo de acesso (IAM)** painel, clique nos grupos de segurança que pretende remover. Clique em **remover**.
   
    ![Grupo de segurança removido](./media/data-lake-store-secure-data/adl.remove.group.png "removido de grupo de segurança")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>Remover o grupo de segurança de ACLs de um sistema de ficheiros de geração 1 de armazenamento do Data Lake
Quando remover o grupo de segurança de ACLs de um sistema de ficheiros de geração 1 de armazenamento do Data Lake, alterar o acesso aos dados na conta de geração 1 de armazenamento do Data Lake.

1. No painel da conta do Data Lake Storage Gen1, clique em **Data Explorer**.
   
    ![Criar os diretórios numa conta do Data Lake Storage Gen1](./media/data-lake-store-secure-data/adl.start.data.explorer.png "criar diretórios na conta de geração 1 de armazenamento do Data Lake")
2. Na **Data Explorer** painel, clique na pasta para o qual pretende remover a ACL e, em seguida, clique em **acesso**. Para remover ACLs para um ficheiro, tem de clicar primeiro o ficheiro para pré-visualização e, em seguida, clique em **acesso** partir do **pré-visualização do ficheiro** painel. 
   
    ![Definir ACLs no sistema de ficheiros de geração 1 de armazenamento do Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "definir ACLs no sistema de arquivos de geração 1 de armazenamento do Data Lake")
3. Na **acesso** painel, clique no grupo de segurança que pretende remover. Na **aceder aos detalhes** painel, clique em **remover**.
   
    ![Atribuir permissões para agrupar](./media/data-lake-store-secure-data/adl.remove.acl.png "atribuir permissões ao grupo")

## <a name="see-also"></a>Consulte também
* [Descrição geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Copiar dados dos Blobs de armazenamento do Azure para a geração 1 de armazenamento do Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utilizar o Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Introdução ao Data Lake Storage Gen1 com o PowerShell](data-lake-store-get-started-powershell.md)
* [Introdução ao Data Lake Storage Gen1 com o .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Aceder aos registos de diagnóstico para a geração 1 de armazenamento do Data Lake](data-lake-store-diagnostic-logs.md)

