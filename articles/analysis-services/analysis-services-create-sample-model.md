---
title: Tutorial - Adicione um modelo de amostra- Serviços de Análise Azure [ Microsoft Docs
description: Nesta aula de tutorial, aprenda a adicionar um modelo de exemplo no Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 88ed58ea1eea80b4b890c31260fc65153377eaaa
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697518"
---
# <a name="tutorial-add-a-sample-model-from-the-portal"></a>Tutorial: Adicionar um modelo de exemplo a partir do portal

Neste tutorial, irá adicionar uma base de dados de modelo tabular do Adventure Works de exemplo ao seu servidor. O modelo de exemplo é uma versão completa do modelo de dados de exemplo de Vendas do Adventure Works Internet (1200). Um modelo de exemplo é útil para testar a gestão de modelos, ao ligar a ferramentas e aplicações de cliente e consultar dados do modelo. Este tutorial utiliza o [portal do Azure](https://portal.azure.com) e o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) para: 

> [!div class="checklist"]
> * Adicionar um modelo de dados tabular de exemplo concluído para um servidor 
> * Ligar ao modelo com o SSMS

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este tutorial, precisa de:

- Um servidor do Azure Analysis Services. Para saber mais, veja [Criar um servidor - portal](analysis-services-create-server.md).
- Permissões de administrador de servidor
- [Estúdio de Gestão de Servidores SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal](https://portal.azure.com/).

## <a name="add-a-sample-model"></a>Adicionar um modelo de exemplo

1. No servidor **Descrição geral**, clique em **Novo modelo**.

    ![Criar um modelo de exemplo](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. No **novo modelo**Escolha uma fonte de dados, verifique se os dados da  >  **Choose a data source** **amostra** são selecionados e, em seguida, clique em **Adicionar**.

    ![Selecionar dados de exemplo](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. Na **Descrição geral**, certifique-se de que o modelo de exemplo `adventureworks` é adicionado.

    ![Selecionar dados de exemplo](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)


## <a name="clean-up-resources"></a>Limpar recursos

O modelo de exemplo está a utilizar recursos de memória de cache. Se não estiver a utilizar o seu modelo de exemplo para fins de teste, deve removê-lo do seu servidor.

Estes passos descrevem como eliminar um modelo de um servidor com o SSMS.

1. No SSMS > **Object Explorer**, clique em **Ligar** > **Analysis Services**.

2. Em **Ligar ao Servidor**, cole o nome do servidor e, em seguida, na **Autenticação**, escolha **Active Directory - Universal com suporte MFA**, introduza o seu nome de utilizador e clique em **Ligar**.

    ![Iniciar sessão](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. No **Object Explorer**, clique com o botão direito do rato na base de dados de amostra `adventureworks` e, em seguida, clique em **Eliminar**.

    ![Eliminar base de dados de amostra](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>Próximos passos 

Neste tutorial, aprendeu a adicionar um modelo de exemplo básico ao seu servidor. Agora que tem uma base de dados modelo, pode ligar-se à mesma a partir do SQL Server Management Studio e adicionar funções de utilizador. Para obter mais informações, avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Configurar funções de utilizador e de administrador de servidor](tutorials/analysis-services-tutorial-roles.md)


