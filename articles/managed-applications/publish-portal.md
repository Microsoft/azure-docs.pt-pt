---
title: Publicar um Azure aplicação através do portal gerida | Documentos da Microsoft
description: Mostra como utilizar o Azure portal para criar um Azure managed application destina-se para os membros da sua organização.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: f27d30d4709fbf373c8458629d0c8c5af4333acf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61297015"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publicar uma aplicação do catálogo de serviços através do portal do Azure

Pode utilizar o portal do Azure para publicar [aplicativos gerenciados](overview.md) que se destinam a ser membros da sua organização. Por exemplo, um departamento de TI pode publicar aplicações geridas que garantir a conformidade com as normas organizacionais. Estas aplicações geridas estão disponíveis através do catálogo de serviço, e não no Azure marketplace.

## <a name="prerequisites"></a>Pré-requisitos

Ao publicar um aplicativo gerenciado, especificar uma identidade para gerir os recursos. Recomendamos que especificar um grupo de utilizadores do Azure Active Directory. Para criar um grupo de utilizadores do Azure Active Directory, veja [criar um grupo e adicionar membros no Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 

O ficheiro. zip que contém a definição de aplicação gerida tem de estar disponível através de um URI. Recomendamos que carregar o ficheiro. zip para um blob de armazenamento. 

## <a name="create-managed-application-with-portal"></a>Criar aplicação gerida com o portal

1. No canto superior esquerdo, selecione **+ novo**.

   ![Novo serviço](./media/publish-portal/new.png)

1. Procure **catálogo de serviços**.

1. Nos resultados, desloque-se até encontrar **catálogo de serviço gerida a definição de aplicação**. Selecione-o.

   ![Procurar definições de aplicação gerida](./media/publish-portal/select-managed-apps-definition.png)

1. Selecione **criar** para iniciar o processo de criação de definição da aplicação gerida.

   ![Criar a definição da aplicação gerida](./media/publish-portal/create-definition.png)

1. Fornece valores para nome, nome a apresentar, descrição, localização, subscrição e grupo de recursos. Para o ficheiro de pacote URI, forneça o caminho para o ficheiro zip que criou.

   ![Fornecer valores](./media/publish-portal/fill-application-values.png)

1. Quando chegar à seção de autenticação e o nível de bloqueio, selecione **adicionar autorização**.

   ![Adicionar autorização](./media/publish-portal/add-authorization.png)

1. Selecione um grupo do Azure Active Directory para gerir os recursos e selecione **OK**.

   ![Adicionar grupo de autorização](./media/publish-portal/add-auth-group.png)

1. Quando forneceu todos os valores, selecione **criar**.

   ![Criar aplicação gerida](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral das aplicações geridas](overview.md).
* Para obter exemplos de aplicação gerida, veja [aplicativos gerenciados de projetos de exemplo para o Azure](sample-projects.md).
* Para saber como criar um ficheiro de definição de IU para uma aplicação gerida, veja [Introdução a CreateUiDefinition](create-uidefinition-overview.md).