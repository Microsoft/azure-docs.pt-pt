---
title: Publicar aplicativos gerenciados por meio do portal
description: Mostra como usar o portal do Azure para criar um aplicativo gerenciado do Azure destinado a membros da sua organização.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 05302d92f2304be35a7b88fac6fabfc17b13c63e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651725"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publicar um aplicativo de catálogo de serviços por meio de portal do Azure

Você pode usar o portal do Azure para publicar [aplicativos gerenciados](overview.md) destinados a membros da sua organização. Por exemplo, um departamento de TI pode publicar aplicações geridas que garantir a conformidade com as normas organizacionais. Estas aplicações geridas estão disponíveis através do catálogo de serviço, e não no Azure marketplace.

## <a name="prerequisites"></a>Pré-requisitos

Ao publicar um aplicativo gerenciado, você especifica uma identidade para gerenciar os recursos. Recomendamos que você especifique um grupo de usuários Azure Active Directory. Para criar um grupo de usuários Azure Active Directory, consulte [criar um grupo e adicionar membros em Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 

O arquivo. zip que contém a definição de aplicativo gerenciado deve estar disponível por meio de um URI. Recomendamos que você carregue o arquivo. zip em um blob de armazenamento. 

## <a name="create-managed-application-with-portal"></a>Criar aplicativo gerenciado com o portal

1. No canto superior esquerdo, selecione **+ novo**.

   ![Novo serviço](./media/publish-portal/new.png)

1. Pesquise o **Catálogo de serviços**.

1. Nos resultados, role até encontrar a **definição de aplicativo gerenciado pelo catálogo de serviços**. Selecione-o.

   ![Pesquisar definições de aplicativo gerenciado](./media/publish-portal/select-managed-apps-definition.png)

1. Selecione **criar** para iniciar o processo de criação da definição de aplicativo gerenciado.

   ![Criar definição de aplicativo gerenciado](./media/publish-portal/create-definition.png)

1. Forneça valores para nome, nome de exibição, descrição, localização, assinatura e grupo de recursos. Para o URI do arquivo de pacote, forneça o caminho para o arquivo zip que você criou.

   ![Fornecer valores](./media/publish-portal/fill-application-values.png)

1. Quando chegar à seção autenticação e nível de bloqueio, selecione **Adicionar autorização**.

   ![Adicionar autorização](./media/publish-portal/add-authorization.png)

1. Selecione um grupo de Azure Active Directory para gerenciar os recursos e selecione **OK**.

   ![Adicionar grupo de autorização](./media/publish-portal/add-auth-group.png)

1. Quando você tiver fornecido todos os valores, selecione **criar**.

   ![Criar aplicação gerida](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral das aplicações geridas](overview.md).
* Para obter exemplos de aplicativos gerenciados, consulte [projetos de exemplo para aplicativos gerenciados do Azure](sample-projects.md).
* Para saber como criar um ficheiro de definição de IU para uma aplicação gerida, veja [Introdução a CreateUiDefinition](create-uidefinition-overview.md).