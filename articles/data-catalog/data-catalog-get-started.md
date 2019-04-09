---
title: Criar um catálogo de dados do Azure
description: Guia de introdução sobre como criar um catálogo de dados do Azure.
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: quickstart
ms.date: 04/05/2019
ms.openlocfilehash: 66d178497f3403579990340b88a1153905b2a2bf
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271038"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Início rápido: Criar um catálogo de dados do Azure

O Catálogo de Dados do Azure é um serviço em nuvem totalmente gerido que funciona como um sistema de registo e um sistema de deteção de recursos de dados empresariais. Para obter uma descrição detalhada, veja [O que é o Catálogo de Dados do Azure](overview.md).

Este início rápido ajuda-o a começar a utilizar com a criação de um catálogo de dados do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, tem de ter:

* R [Microsoft Azure](https://azure.microsoft.com/) subscrição.
* Tem de ter sua própria [inquilino do Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Para configurar o catálogo de dados, tem de ser o proprietário ou coproprietário de uma subscrição do Azure.

## <a name="create-a-data-catalog"></a>Criar um catálogo de dados

Pode aprovisionar apenas um catálogo de dados por organização (domínio do Azure Active Directory). Por conseguinte, se o proprietário ou coproprietário de uma subscrição do Azure que pertença a este domínio do Azure Active Directory já tiver criado um catálogo, em seguida, é possível criar um catálogo novamente, mesmo se tiver várias subscrições do Azure. Para testar se um catálogo de dados foi criado por um utilizador no seu domínio do Azure Active Directory, aceda à [home page do Catálogo de Dados do Azure](http://azuredatacatalog.com) e verifique se vê o catálogo. Se já tiver sido criado um catálogo para si, ignore o procedimento seguinte e avance para a próxima secção.

1. Vá para o [portal do Azure](https://portal.azure.com) > **criar um recurso** e selecione **catálogo de dados**.

    ![Criar o catálogo de dados](media/data-catalog-get-started/data-catalog-create.png)

2. Especificar um **name** para o catálogo de dados, o **subscrição** que pretende utilizar, o **localização** para o catálogo e a **escalão de preço**. Em seguida, selecione **Criar**.

3. Aceda à [home page do Catálogo de Dados do Azure](http://azuredatacatalog.com) e clique em **Publicar Dados**.

   ![Catálogo de Dados do Azure – Botão “Publicar Dados”](media/data-catalog-get-started/data-catalog-publish-data.png)

   Também pode obter a home page do catálogo de dados do [página do serviço de catálogo de dados](https://azure.microsoft.com/services/data-catalog) ao selecionar **começar**.

   ![Catálogo de Dados do Azure – página de destino de marketing](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Vá para o **definições** página.

    ![Catálogo de Dados do Azure – aprovisionar catálogo de dados](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Expanda **preços** e verifique se o seu catálogo de dados do Azure **edition** (gratuita ou Standard).

    ![Catálogo de Dados do Azure -- selecionar edição](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Se escolher *padrão* edition como o escalão de preço, pode expandir **grupos de segurança** e ativar a autorização de grupos de segurança do Active Directory para aceder ao catálogo de dados e ativar o ajuste automático do a faturação.

    ![Grupos de segurança do catálogo de dados do Azure](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Expanda **Utilizadores do Catálogo** e clique em **Adicionar** para adicionar utilizadores ao catálogo de dados. Serão adicionados automaticamente a este grupo.

    ![Catálogo de Dados do Azure -- utilizadores](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Se escolher *padrão* edition como o escalão de preço, pode expandir **administradores do glossário** e clique em **Add** para adicionar utilizadores de administrador do glossário. Serão adicionados automaticamente a este grupo.

    ![Administradores do glossário de catálogo de dados do Azure](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Expanda **Administradores do Catálogo** e clique em **Adicionar** para adicionar outros administradores ao catálogo de dados. Serão adicionados automaticamente a este grupo.

    ![Catálogo de Dados do Azure -- administradores](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Expanda **título do Portal** e adicione o texto adicional que será apresentado no título do portal.

    ![Título do Portal do catálogo de dados do Azure](media/data-catalog-get-started/data-catalog-portal-title.png)

11. Depois de concluir o **configurações** página, em seguida, navegue para o **Publish** página.

    ![Catálogo de Dados do Azure -- criado](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Encontrar um catálogo de dados no portal do Azure

1. Noutro separador do browser ou numa janela separada do browser, aceda ao [portal do Azure](https://portal.azure.com) e inicie sessão com a mesma conta que utilizou para criar o catálogo de dados no passo anterior.

2. Selecione **todos os serviços** e, em seguida, clique em **catálogo de dados**.

    ![Catálogo de dados do Azure – procurar no Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    É apresentado o catálogo de dados que criou.

    ![Catálogo de Dados do Azure – ver o catálogo na lista](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Clique no catálogo que criou. É apresentado o painel **Catálogo de Dados** no portal.

   ![Catálogo de Dados do Azure – painel no portal](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Pode ver as propriedades do catálogo de dados, bem como atualizá-las. Por exemplo, clique em **Escalão de preço** e altere a edição.

    ![Catálogo de Dados do Azure – escalão de preço](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu como criar um catálogo de dados do Azure para a sua organização. Agora, pode registar origens de dados no seu catálogo de dados.

> [!div class="nextstepaction"]
> [Registar origens de dados no catálogo de dados do Azure](data-catalog-how-to-register.md)
