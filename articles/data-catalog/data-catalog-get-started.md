---
title: Criar um Catálogo de Dados do Azure
description: Este guia de início rápido descreve como criar um catálogo de dados do Azure usando o portal do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 08/01/2019
ms.openlocfilehash: a834c3c20f11ee4dbf070ae7c836f302c82c9def
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950291"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Início rápido: Criar um Catálogo de Dados do Azure

O Catálogo de Dados do Azure é um serviço em nuvem totalmente gerido que funciona como um sistema de registo e um sistema de deteção de recursos de dados empresariais. Para obter uma descrição detalhada, veja [O que é o Catálogo de Dados do Azure](overview.md).

Este guia de início rápido ajuda você a começar a criar um catálogo de dados do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa ter:

* Uma assinatura [Microsoft Azure](https://azure.microsoft.com/) .
* Você precisa ter seu próprio [locatário Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Para configurar o catálogo de dados, você deve ser o proprietário ou coproprietário de uma assinatura do Azure.

## <a name="create-a-data-catalog"></a>Criar um catálogo de dados

Pode aprovisionar apenas um catálogo de dados por organização (domínio do Azure Active Directory). Portanto, se o proprietário ou o coproprietário de uma assinatura do Azure que pertence a este Azure Active Directory domínio já tiver criado um catálogo, você não poderá criar um catálogo novamente mesmo se tiver várias assinaturas do Azure. Para testar se um catálogo de dados foi criado por um utilizador no seu domínio do Azure Active Directory, aceda à [home page do Catálogo de Dados do Azure](http://azuredatacatalog.com) e verifique se vê o catálogo. Se já tiver sido criado um catálogo para si, ignore o procedimento seguinte e avance para a próxima secção.

1. Vá para o [portal do Azure](https://portal.azure.com) > **criar um recurso** e selecione **Catálogo de dados**.

    ![Criar catálogo de dados](media/data-catalog-get-started/data-catalog-create.png)

2. Especifique um **nome** para o catálogo de dados, a **assinatura** que você deseja usar, o **local** do catálogo e o **tipo de preço**. Em seguida, selecione **Criar**.

3. Aceda à [home page do Catálogo de Dados do Azure](http://azuredatacatalog.com) e clique em **Publicar Dados**.

   ![Catálogo de Dados do Azure – Botão “Publicar Dados”](media/data-catalog-get-started/data-catalog-publish-data.png)

   Você também pode acessar o catálogo de dados home page da [página de serviço do catálogo de dados](https://azure.microsoft.com/services/data-catalog) selecionando **introdução**.

   ![Catálogo de Dados do Azure – página de destino de marketing](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Vá para a página **configurações** .

    ![Catálogo de Dados do Azure – aprovisionar catálogo de dados](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Expanda **preços** e verifique sua **edição** do catálogo de dados do Azure (gratuita ou padrão).

    ![Catálogo de Dados do Azure -- selecionar edição](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Se você escolher *Standard* Edition como seu tipo de preço, poderá expandir **grupos de segurança** e habilitar a autorização Active Directory grupos de segurança para acessar o catálogo de dados e habilitar o ajuste automático de cobrança.

    ![Grupos de segurança do catálogo de dados do Azure](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Expanda **Utilizadores do Catálogo** e clique em **Adicionar** para adicionar utilizadores ao catálogo de dados. Você é adicionado automaticamente a este grupo.

    ![Catálogo de Dados do Azure -- utilizadores](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Se você escolher *Standard* Edition como seu tipo de preço, poderá expandir **os administradores do glossário** e clicar em **Adicionar** para adicionar usuários administradores do glossário. Você é adicionado automaticamente a este grupo.

    ![Administradores do glossário do catálogo de dados do Azure](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Expanda **Administradores do Catálogo** e clique em **Adicionar** para adicionar outros administradores ao catálogo de dados. Você é adicionado automaticamente a este grupo.

    ![Catálogo de Dados do Azure -- administradores](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Expanda **título do portal** e adicione texto adicional que será exibido no título do Portal.

    ![Catálogo de dados do Azure-título do portal](media/data-catalog-get-started/data-catalog-portal-title.png)

11. Depois de concluir a página **configurações** , navegue até a página **publicar** .

    ![Catálogo de Dados do Azure -- criado](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Encontrar um catálogo de dados no portal do Azure

1. Noutro separador do browser ou numa janela separada do browser, aceda ao [portal do Azure](https://portal.azure.com) e inicie sessão com a mesma conta que utilizou para criar o catálogo de dados no passo anterior.

2. Selecione **todos os serviços** e clique em **Catálogo de dados**.

    ![Catálogo de dados do Azure – procurar no Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Você verá o catálogo de dados que criou.

    ![Catálogo de Dados do Azure – ver o catálogo na lista](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Clique no catálogo que criou. É apresentado o painel **Catálogo de Dados** no portal.

   ![Catálogo de Dados do Azure – painel no portal](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Pode ver as propriedades do catálogo de dados, bem como atualizá-las. Por exemplo, clique em **Escalão de preço** e altere a edição.

    ![Catálogo de Dados do Azure – escalão de preço](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você aprendeu a criar um catálogo de dados do Azure para sua organização. Agora você pode registrar fontes de dados em seu catálogo de dados.

> [!div class="nextstepaction"]
> [Registrar fontes de dados no catálogo de dados do Azure](data-catalog-how-to-register.md)
