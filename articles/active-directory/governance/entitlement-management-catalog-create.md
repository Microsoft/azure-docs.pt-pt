---
title: Criar e gerir recursos na gestão de direitos - Azure AD
description: Saiba como criar um novo recipiente de recursos e pacotes de acesso na gestão de direitos do Diretório Ativo Azure.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5895ed256fa5f0337b74d9dbe14c4074dad4b522
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261869"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Criar e gerir um catálogo de recursos na gestão de direitos da Azure AD

## <a name="create-a-catalog"></a>Criar um catálogo

Um catálogo é um recipiente de recursos e pacotes de acesso. Cria um catálogo quando pretende agrupar recursos relacionados e pacotes de acesso. Quem cria o catálogo torna-se o primeiro dono do catálogo. Um proprietário de catálogo pode adicionar proprietários de catálogo adicional.

**Papel pré-requisito:** Administrador global, administrador de utilizador ou criador de catálogo

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Catálogos**.

    ![Catálogos de gestão de direitos no portal Azure](./media/entitlement-management-catalog-create/catalogs.png)

1. Clique em **Novo catálogo**.

1. Insira um nome único para o catálogo e forneça uma descrição.

    Os utilizadores verão esta informação nos detalhes de um pacote de acesso.

1. Se pretender que os pacotes de acesso deste catálogo estejam disponíveis para os utilizadores solicitarem logo que sejam criados, detete **com o 'Enabled** to **Yes**'

1. Se pretender permitir que os utilizadores em diretórios externos selecionados possam solicitar pacotes de acesso neste catálogo, detete **tede imediação para utilizadores externos** para **Sim**.

    ![Novo painel de catálogo](./media/entitlement-management-shared/new-catalog.png)

1. Clique em **Criar** para criar o catálogo.

### <a name="creating-a-catalog-programmatically"></a>Criar um catálogo programáticamente

Também pode criar um catálogo utilizando o Microsoft Graph.  Um utilizador numa função adequada com uma aplicação que tenha a permissão de `EntitlementManagement.ReadWrite.All` delegada pode ligar para a API para [criar um AccessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-post?view=graph-rest-beta).

## <a name="add-resources-to-a-catalog"></a>Adicione recursos a um catálogo

Para incluir recursos num pacote de acesso, os recursos devem existir num catálogo. Os tipos de recursos que pode adicionar são grupos, aplicações e sites SharePoint Online. Os grupos podem ser criados em nuvem Office 365 Groups ou grupos de segurança Azure AD criados em nuvem. As aplicações podem ser aplicações empresariais da Azure AD, incluindo aplicações SaaS e suas próprias aplicações federadas para AD Azure. Os sites podem ser sites Online SharePoint ou coleções de sites Online SharePoint.

**Papel pré-requisito:** Ver [funções necessárias para adicionar recursos a um catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu à esquerda, clique em **Catálogos** e, em seguida, abra o catálogo a que pretende adicionar recursos.

1. No menu esquerdo, clique em **Recursos.**

1. Clique em **Adicionar recursos**.

1. Clique num tipo de recurso: **Grupos e Equipas,** **Aplicações**ou **sites SharePoint**.

    Se não vir um recurso que queira adicionar ou não conseguir adicionar um recurso, certifique-se de que tem o papel de diretório e gestão de direitos exigidos pela Azure AD. Pode precisar de alguém com as funções necessárias para adicionar o recurso ao seu catálogo. Para mais informações, consulte [funções necessárias para adicionar recursos a um catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Selecione um ou mais recursos do tipo que gostaria de adicionar ao catálogo.

    ![Adicione recursos a um catálogo](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Quando terminar, clique em **Adicionar**.

    Estes recursos podem agora ser incluídos em pacotes de acesso dentro do catálogo.

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Adicionar um recurso a um catálogo programática

Também pode adicionar um recurso a um catálogo utilizando o Microsoft Graph.  Um utilizador numa função adequada, ou um catálogo e proprietário de recursos, com uma aplicação que tenha a autorização de `EntitlementManagement.ReadWrite.All` delegada pode chamar a API para [criar um accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta).

## <a name="remove-resources-from-a-catalog"></a>Remova recursos de um catálogo

Pode remover recursos de um catálogo. Um recurso só pode ser removido de um catálogo se não estiver a ser utilizado em nenhum dos pacotes de acesso do catálogo.

**Papel pré-requisito:** Ver [funções necessárias para adicionar recursos a um catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu à esquerda, clique em **Catálogos** e abra o catálogo de que pretende remover recursos.

1. No menu esquerdo, clique em **Recursos.**

1. Selecione os recursos que pretende remover.

1. Clique em **Remover** (ou clique na elipse **(...** ) e, em seguida, clique em **Remover o recurso**).

## <a name="add-additional-catalog-owners"></a>Adicione proprietários adicionais de catálogo

O utilizador que criou um catálogo torna-se o primeiro proprietário do catálogo. Para delegar a gestão de um catálogo, adicione os utilizadores ao papel de proprietário do catálogo. Isto ajuda a partilhar as responsabilidades de gestão do catálogo. 

Siga estes passos para atribuir um utilizador à função de proprietário do catálogo:

**Papel pré-requisito:** Administrador global, administrador de utilizador ou proprietário do Catálogo

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu à esquerda, clique em **Catálogos** e abra o catálogo a que pretende adicionar administradores.

1. No menu esquerdo, clique em **Papéis e administradores.**

    ![Cataloga funções e administradores](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Clique em **Adicionar proprietários** para selecionar os membros para estas funções.

1. Clique em **Selecionar** para adicionar estes membros.

## <a name="edit-a-catalog"></a>Editar um catálogo

Pode editar o nome e a descrição de um catálogo. Os utilizadores vêem esta informação nos detalhes de um pacote de acesso.

**Papel pré-requisito:** Administrador global, administrador de utilizador ou proprietário do Catálogo

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu à esquerda, clique em **Catálogos** e abra o catálogo que pretende editar.

1. Na página de **visão geral** do catálogo, clique em **Editar**.

1. Editar o nome, descrição ou definições ativadas do catálogo.

    ![Editar definições de catálogo](./media/entitlement-management-shared/catalog-edit.png)

1. Clique em **Guardar**.

## <a name="delete-a-catalog"></a>Apagar um catálogo

Pode eliminar um catálogo, mas apenas se não tiver pacotes de acesso.

**Papel pré-requisito:** Administrador global, administrador de utilizador ou proprietário do Catálogo

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu à esquerda, clique em **Catálogos** e abra o catálogo que pretende eliminar.

1. Na **visão geral**do catálogo, clique em **Eliminar**.

1. Na caixa de mensagens que aparece, clique **em Sim**.

### <a name="deleting-a-catalog-programmatically"></a>Apagar um catálogo programáticamente

Também pode eliminar um catálogo utilizando o Microsoft Graph.  Um utilizador numa função adequada com uma aplicação que tenha a permissão de `EntitlementManagement.ReadWrite.All` delegada pode ligar para a API para [eliminar um AccessPackageCatalog](https://docs.microsoft.com/graph/api/accesspackagecatalog-delete?view=graph-rest-beta).

## <a name="next-steps"></a>Passos seguintes

- [Delegar governação de acesso aos gestores de pacotes de acesso](entitlement-management-delegate-managers.md)
