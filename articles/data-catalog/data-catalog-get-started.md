---
title: 'Quickstart: Criar um Catálogo de Dados Azure'
description: Este quickstart descreve como criar um Catálogo de Dados Azure utilizando o portal Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: a56e5a4cae7c5a8e931b074f08a7152e53a8eb31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674738"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Quickstart: Criar um Catálogo de Dados Azure

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

O Catálogo de Dados do Azure é um serviço em nuvem totalmente gerido que funciona como um sistema de registo e um sistema de deteção de recursos de dados empresariais. Para obter uma descrição detalhada, veja [O que é o Catálogo de Dados do Azure](overview.md).

Este quickstart ajuda-o a começar a criar um Catálogo de Dados Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

> [!Note]
> Devido aos requisitos de segurança da Azure, o Azure Data Catalog tem aforado a Segurança da Camada de Transporte (TLS) 1.2. Os TLS 1.0 e TLS 1.1 foram desativados. Pode encontrar erros na execução da ferramenta de registo se a sua máquina não estiver atualizada para o TLS 1.2. Consulte [a Segurança da Camada de Transporte (1.2)](/mem/configmgr/core/plan-design/security/enable-tls-1-2) para atualizar a sua máquina para TLS 1.2.

Para começar, é preciso ter:

* Uma [subscrição do Microsoft Azure](https://azure.microsoft.com/).
* Você precisa ter o seu próprio [inquilino Azure Ative Directory.](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

Para configurar o Catálogo de Dados, tem de ser o proprietário ou coproprietário de uma subscrição da Azure.

## <a name="create-a-data-catalog"></a>Criar um catálogo de dados

Pode aprovisionar apenas um catálogo de dados por organização (domínio do Azure Active Directory). Portanto, se o proprietário ou coproprietário de uma subscrição Azure que pertence a este domínio do Azure Ative Directory já criou um catálogo, então não pode criar um catálogo novamente mesmo que tenha várias subscrições Azure. Para testar se um catálogo de dados foi criado por um utilizador no seu domínio do Azure Active Directory, aceda à [home page do Catálogo de Dados do Azure](http://azuredatacatalog.com) e verifique se vê o catálogo. Se já tiver sido criado um catálogo para si, ignore o procedimento seguinte e avance para a próxima secção.

1. Vá ao [portal Azure](https://portal.azure.com)  >  **Criar um recurso** e selecione Catálogo de **Dados.**

    ![Catálogo de dados Azure cria botão](media/data-catalog-get-started/data-catalog-create.png)

2. Especifique um **nome** para o catálogo de dados, a **subscrição** que pretende utilizar, a **localização** do catálogo e o **nível de preços.** Em seguida, selecione **Criar**.

3. Aceda à [home page do Catálogo de Dados do Azure](http://azuredatacatalog.com) e clique em **Publicar Dados**.

   ![Catálogo de Dados do Azure – Botão “Publicar Dados”](media/data-catalog-get-started/data-catalog-publish-data.png)

   Também pode chegar à página inicial do Catálogo de Dados a partir da página de serviço do Catálogo de [Dados,](https://azure.microsoft.com/services/data-catalog) selecionando **Começar.**

   ![Catálogo de Dados do Azure – página de destino de marketing](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Aceda à página **Definições.**

    ![Catálogo de Dados do Azure -- aprovisionar catálogo de dados](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Expanda **os preços** e verifique a sua **edição** do Catálogo de Dados Azure (Grátis ou Standard).

    ![Catálogo de Dados do Azure -- selecionar edição](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Se escolher a edição *Standard* como o seu nível de preços, pode expandir **os Grupos** de Segurança e permitir autorizar grupos de segurança do Ative Directory a aceder ao Catálogo de Dados e permitir o ajuste automático da faturação.

    ![Grupos de segurança do catálogo de dados Azure](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Expanda **Utilizadores do Catálogo** e clique em **Adicionar** para adicionar utilizadores ao catálogo de dados. És automaticamente adicionado a este grupo.

    ![Catálogo de Dados do Azure -- utilizadores](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Se escolher a edição *Standard* como o seu nível de preços, pode expandir **administradores glossários** e clicar em **Adicionar** aos utilizadores de administrador glossário. És automaticamente adicionado a este grupo.

    ![Administradores glossários do catálogo de dados Azure](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Expanda **Administradores do Catálogo** e clique em **Adicionar** para adicionar outros administradores ao catálogo de dados. És automaticamente adicionado a este grupo.

    ![Catálogo de Dados do Azure -- administradores](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Expanda **o Título do Portal** e adicione texto adicional que será exibido no título do portal.

    ![Título de Catalog-Portal de dados do Azure](media/data-catalog-get-started/data-catalog-portal-title.png)

11. Assim que completar a página **Definições,** navegue em seguida para a página **Publicar.**

    ![Catálogo de Dados do Azure -- criado](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Encontrar um catálogo de dados no portal do Azure

1. Noutro separador do browser ou numa janela separada do browser, aceda ao [portal do Azure](https://portal.azure.com) e inicie sessão com a mesma conta que utilizou para criar o catálogo de dados no passo anterior.

2. Selecione **Todos os serviços** e, em seguida, clique no **Catálogo de Dados.**

    ![Catálogo de dados Azure-- navegue Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Vê o catálogo de dados que criou.

    ![Catálogo de Dados do Azure - ver o catálogo na lista](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Clique no catálogo que criou. É apresentado o painel **Catálogo de Dados** no portal.

   ![Catálogo de Dados do Azure -- painel no portal](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Pode ver as propriedades do catálogo de dados, bem como atualizá-las. Por exemplo, clique em **Escalão de preço** e altere a edição.

    ![Catálogo de Dados do Azure – escalão de preço](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar um Catálogo de Dados Azure para a sua organização. Pode agora registar fontes de dados no seu catálogo de dados.

> [!div class="nextstepaction"]
> [Registar fontes de dados no Catálogo de Dados do Azure](data-catalog-how-to-register.md)