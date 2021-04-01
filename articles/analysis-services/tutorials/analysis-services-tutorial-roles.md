---
title: Tutorial - Configurar funções de Serviços de Análise Azure | Microsoft Docs
description: Neste tutorial, aprenda a configurar o administrador e as funções de utilizador do Azure Analysis Services utilizando o portal Azure ou o SQL Server Management Studio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: aea2196671a136145671b977a6d925849b635b73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92018700"
---
# <a name="tutorial-configure-server-administrator-and-user-roles"></a>Tutorial: Configurar funções de utilizador e de administrador de servidor

 Neste tutorial, irá utilizar o SQL Server Management Studio (SSMS) para ligar ao seu servidor do Azure, para configurar as funções da base de dados modelo e de administrador de servidor. Também irá conhecer a [Linguagem de Scripting de Modelo Tabular (TMSL)](/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200). A TMSL é uma linguagem de scripting baseada em JSON para modelos tabulares ao nível 1200 e níveis superiores de compatibilidade. Pode servir para automatizar muitas tarefas de modelação tabular. A TMSL é frequentemente utilizada com o PowerShell, mas neste tutorial, irá utilizar o editor de consultas XMLA no SSMS. Com este tutorial irá concluir estas tarefas: 
  
> [!div class="checklist"]
> * Obter o nome do seu servidor a partir do portal
> * Ligar ao seu servidor com o SSMS
> * Adicionar um utilizador ou grupo à função de administrador do servidor 
> * Adicionar um utilizador ou grupo à função de administrador de base de dados modelo
> * Adicionar uma nova função de base de dados modelo e adicionar um utilizador ou grupo

Para saber mais sobre a segurança do utilizador no Azure Analysis Services, veja [Permissões de autenticação e utilizador](../analysis-services-manage-users.md). 

## <a name="prerequisites"></a>Pré-requisitos

- Um Azure Active Directory na sua subscrição.
- Criou um [servidor do Azure Analysis Services](../analysis-services-create-server.md) na sua subscrição.
- Ter permissões de [administrador de servidor](../analysis-services-server-admins.md).
- [Adicionar o modelo de exemplo adventureworks](../analysis-services-create-sample-model.md) ao seu servidor.
- [Instalar a versão mais recente do SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no [portal.](https://portal.azure.com/)

## <a name="get-server-name"></a>Obter nome do servidor
Para ligar ao seu servidor do SSMS, primeiro precisa do nome do servidor. Pode obter o nome do servidor a partir do portal.

No **portal do Azure** > servidor > **Descrição geral** > **Nome do servidor**, copie o nome do servidor.
   
   ![Obter o nome do servidor no Azure](./media/analysis-services-tutorial-roles/aas-copy-server-name.png)

## <a name="connect-in-ssms"></a>Ligar no SSMS

Para as tarefas restantes, utilize o SSMS para ligar e para gerir o seu servidor.

1. No SSMS > **Object Explorer**, clique em **Ligar** > **Analysis Services**.

    ![Ligar](./media/analysis-services-tutorial-roles/aas-ssms-connect.png)

2. Na caixa de **Ligar ao Servidor**, no **Nome do servidor**, cole o nome do servidor que copiou do portal. Na **Autenticação**, escolha **Active Directory Universal com Suporte de MFA**, introduza a sua conta de utilizador e, em seguida, prima **Ligar**.
   
    ![Ligar no SSMS](./media/analysis-services-tutorial-roles/aas-connect-ssms-auth.png)

    > [!TIP]
    > Recomenda-se que escolha o Active Directory Universal com Suporte de MFA. Este tipo de autenticação suporta [autenticação não interativa e multifator](../../azure-sql/database/authentication-mfa-ssms-overview.md). 

3. No **Object Explorer**, expanda para ver os objetos do servidor. Clique com o botão direito do rato para ver as propriedades do servidor.
   
    ![Explorador de Objetos em SSMS](./media/analysis-services-tutorial-roles/aas-connect-ssms-objexp.png)

## <a name="add-a-user-account-to-the-server-administrator-role"></a>Adicionar uma conta de utilizador à função de administrador do servidor

Nesta tarefa, irá adicionar uma conta de utilizador ou de grupo a partir do seu Azure AD para a função de administrador de servidor. Se especificar um grupo de segurança, utilize `obj:groupid@tenantid` .

1. No **Object Explorer**, clique com o botão direito do rato no nome do servidor e, em seguida, clique em **Propriedades**. 
2. Na janela **Propriedades do Analysis Server**, clique em **Segurança** > **Adicionar**.
3. Na janela **Selecionar um Utilizador ou Grupo**, introduza uma conta de utilizador ou grupo no Azure AD e, em seguida, clique em **Adicionar**. 
   
     ![Adicionar administrador de servidor](./media/analysis-services-tutorial-roles/aas-add-server-admin.png)

4. Clique em **OK** para fechar as **Propriedades do Analysis Server**.

    > [!TIP]
    > Também pode adicionar os administradores de servidores através do portal **Administradores do Analysis Services** no portal. 

## <a name="add-a-user-to-the-model-database-administrator-role"></a>Adicionar um utilizador à função de administrador de base de dados modelo

Nesta tarefa, irá adicionar uma conta de utilizador ou de grupo à função de Administrador de Vendas de Internet que já existe no modelo. Esta função tem permissões de Controlo total (Administrador) para a base de dados do modelo de exemplo adventureworks. Esta tarefa utiliza o comando TMSL [CreateOrReplace](/analysis-services/tmsl/createorreplace-command-tmsl) num script criado para si.

1. No **Object Explorer**, expanda **Bases de Dados** > **adventureworks** > **Funções**. 
2. Clique com botão direito do rato em **Administrador de Vendas da Internet** e, em seguida, clique em **Função de Script como** > **CRIAR OU SUBSTITUIR PARA** > **Nova Janela do Editor de Consulta**.

    ![Nova Janela do Editor de Consultas](./media/analysis-services-tutorial-roles/aas-add-db-admin.png)

3. No **XMLAQuery**, altere o valor para **"memberName":** para uma conta de utilizador ou de grupo no seu Azure AD. Por predefinição, a conta com que iniciou sessão está incluída; no entanto, não precisa de adicionar a sua própria conta porque já é um administrador de servidor.

    ![Script TMSL na consulta XMLA](./media/analysis-services-tutorial-roles/aas-add-db-admin-script.png)

4. Prima **F5** para executar o script.


## <a name="add-a-new-model-database-role-and-add-a-user-or-group"></a>Adicionar uma nova função de base de dados modelo e adicionar um utilizador ou grupo

Nesta tarefa, irá utilizar o comando [Criar](/analysis-services/tmsl/create-command-tmsl) num script TMSL para criar uma nova função Global de Vendas da Internet, especificar permissões de *leitura* para a função e adicionar uma conta de utilizador ou de grupo a partir do seu Azure AD.

1. No **Object Explorer**, clique com o botão direito do rato em **adventureworks** e, em seguida, clique em **Nova Consulta** > **XMLA**. 
2. Copie e cole o seguinte script TMSL no editor de consultas:

    ```JSON
    {
    "create": {
      "parentObject": {
        "database": "adventureworks",
       },
       "role": {
         "name": "Internet Sales Global",
         "description": "All users can query model data",
         "modelPermission": "read",
         "members": [
           {
             "memberName": "globalsales@adventureworks.com",
             "identityProvider": "AzureAD"
           }
         ]
       }
      }
    }
    ```

3. Altere o valor do objeto `"memberName": "globalsales@adventureworks.com"` para uma conta de utilizador ou de grupo no seu Azure AD.
4. Prima **F5** para executar o script.

## <a name="verify-your-changes"></a>Verifique as suas alterações

1. No **Object Explorer**, clique no servername e, em seguida, clique em **Atualizar** ou prima **F5**.
2. Expandir **bases de dados**  >  **funções de**  >  **aventura.** Verifique se as alterações adicionadas à conta de utilizador e à nova função nas tarefas anteriores são apresentadas.   

    ![Verificar no Object Explorer](./media/analysis-services-tutorial-roles/aas-connect-ssms-verify.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for preciso, elimine as funções e contas de utilizador ou de grupo. Para tal, utilize a **Role Properties**  >  **Membership** para remover contas de utilizador ou clique com o botão direito de uma função e, em seguida, clique em **Eliminar**.


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a ligar ao seu servidor AS do Azure e a explorar as propriedades e as bases de dados modelo e propriedades de exemplo adventureworks no SSMS. Também aprendeu a utilizar scripts SSMS e TMSL para adicionar utilizadores ou grupos às funções novas e atuais. Agora que tem permissões de utilizador configuradas para o seu servidor e base de dados modelo de exemplo, o utilizador e outros utilizadores podem ligar-se ao mesmo ao utilizar aplicações cliente, como o Power BI. Para obter mais informações, avance para o próximo tutorial. 

> [!div class="nextstepaction"]
> [Tutorial: Ligar com o Power BI Desktop](analysis-services-tutorial-pbid.md)