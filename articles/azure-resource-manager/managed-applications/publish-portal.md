---
title: Publicar aplicativos geridos através do portal
description: Mostra como usar o portal Azure para criar uma aplicação gerida pelo Azure que se destina a membros da sua organização.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 05302d92f2304be35a7b88fac6fabfc17b13c63e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75651725"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publique uma aplicação de catálogo de serviços através do portal Azure

Pode utilizar o portal Azure para publicar [aplicações geridas](overview.md) que se destinam a membros da sua organização. Por exemplo, um departamento de TI pode publicar aplicações geridas que garantir a conformidade com as normas organizacionais. Estas aplicações geridas estão disponíveis através do catálogo de serviço, e não no Azure marketplace.

## <a name="prerequisites"></a>Pré-requisitos

Ao publicar uma aplicação gerida, especifique uma identidade para gerir os recursos. Recomendamos que especifique um grupo de utilizadores do Azure Ative Directory. Para criar um grupo de utilizadores do Azure Ative Directory, consulte [criar um grupo e adicionar membros no Azure Ative Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 

O ficheiro .zip que contém a definição de aplicação gerida deve estar disponível através de um URI. Recomendamos que faça o upload do seu ficheiro .zip para uma bolha de armazenamento. 

## <a name="create-managed-application-with-portal"></a>Criar aplicação gerida com portal

1. No canto superior esquerdo, selecione **+ Novo**.

   ![Novo serviço](./media/publish-portal/new.png)

1. Procure por **catálogo de serviços.**

1. Nos resultados, desloque-se até encontrar **a Definição de Aplicação Gerida do Catálogo de Serviços**. Selecione-a.

   ![Pesquisa de definições de aplicações geridas](./media/publish-portal/select-managed-apps-definition.png)

1. Selecione **Criar** para iniciar o processo de criação da definição de aplicação gerida.

   ![Criar definição de aplicação gerida](./media/publish-portal/create-definition.png)

1. Forneça valores para nome, nome de exibição, descrição, localização, subscrição e grupo de recursos. Para o ficheiro de pacote URI, forneça o caminho para o ficheiro zip que criou.

   ![Fornecer valores](./media/publish-portal/fill-application-values.png)

1. Quando chegar à secção de Autenticação e Nível de Bloqueio, **selecione Adicionar Autorização**.

   ![Adicionar autorização](./media/publish-portal/add-authorization.png)

1. Selecione um grupo de Diretório Ativo Azure para gerir os recursos e selecione **OK**.

   ![Adicionar grupo de autorização](./media/publish-portal/add-auth-group.png)

1. Quando tiver fornecido todos os valores, **selecione Criar**.

   ![Criar aplicação gerida](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral das aplicações geridas](overview.md).
* Para exemplos de aplicações geridos, consulte [projetos de amostra para aplicações geridas pela Azure](sample-projects.md).
* Para saber como criar um ficheiro de definição de IU para uma aplicação gerida, veja [Introdução a CreateUiDefinition](create-uidefinition-overview.md).