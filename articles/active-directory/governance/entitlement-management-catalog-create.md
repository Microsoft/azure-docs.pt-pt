---
title: Criar & gerir um catálogo de recursos na gestão de direitos - Azure AD
description: Saiba como criar um novo recipiente de recursos e pacotes de acesso na gestão de direitos do Azure Ative Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf6e02ebad2ac5150be4775ad0ff6069fe3db89f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90980042"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Criar e gerir um catálogo de recursos na gestão de direitos Azure AD

## <a name="create-a-catalog"></a>Criar um catálogo

Um catálogo é um contentor de recursos e pacotes de acesso. Cria um catálogo quando pretende agrupar recursos relacionados e aceder a pacotes. Quem cria o catálogo torna-se o primeiro dono do catálogo. Um proprietário de catálogo pode adicionar proprietários adicionais de catálogo.

**Papel pré-requisito:** Administrador global, administrador de utilizadores ou criador de catálogo

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Catálogos.**

    ![Catálogos de gestão de direitos no portal Azure](./media/entitlement-management-catalog-create/catalogs.png)

1. Clique **em Novo catálogo.**

1. Insira um nome único para o catálogo e forneça uma descrição.

    Os utilizadores verão esta informação nos detalhes de um pacote de acesso.

1. Se pretender que os pacotes de acesso deste catálogo estejam disponíveis para os utilizadores solicitarem assim que estes forem criados, **desembale Ativado** para **Sim.**

1. Se pretender permitir que os utilizadores em diretórios externos selecionados possam solicitar pacotes de acesso neste catálogo, definir **Enabled para utilizadores externos** para **Sim**.

    ![Novo painel de catálogo](./media/entitlement-management-shared/new-catalog.png)

1. Clique em **Criar** para criar o catálogo.

### <a name="creating-a-catalog-programmatically"></a>Criar um catálogo programático

Também pode criar um catálogo utilizando o Microsoft Graph.  Um utilizador numa função adequada com uma aplicação que tenha a permissão delegada `EntitlementManagement.ReadWrite.All` pode ligar para a API para criar um [AccessPackageCatalog](/graph/api/accesspackagecatalog-post?view=graph-rest-beta).

## <a name="add-resources-to-a-catalog"></a>Adicionar recursos a um catálogo

Para incluir recursos num pacote de acesso, os recursos devem existir num catálogo. Os tipos de recursos que pode adicionar são grupos, aplicações e sites SharePoint Online. Os grupos podem ser criados em nuvem Grupos Microsoft 365 ou grupos de segurança Azure AD criados na nuvem. As aplicações podem ser aplicações empresariais Azure AD, incluindo aplicações SaaS e suas próprias aplicações federadas para Azure AD. Os sites podem ser sites SharePoint Online ou coleções de sites SharePoint Online.

**Papel pré-requisito:** Ver [funções necessárias para adicionar recursos a um catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Catálogos** e, em seguida, abra o catálogo a que pretende adicionar recursos.

1. No menu esquerdo, clique em **Recursos.**

1. Clique **em Adicionar recursos.**

1. Clique num tipo de recurso: **Grupos e Equipas,** **Aplicações**ou **sites SharePoint**.

    Se não vir um recurso que pretenda adicionar ou se não conseguir adicionar um recurso, certifique-se de que tem a função de diretório AD AD exigida e a função de gestão de direitos. Pode ser necessário que alguém com as funções necessárias adicione o recurso ao seu catálogo. Para obter mais informações, consulte [as funções necessárias para adicionar recursos a um catálogo.](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Selecione um ou mais recursos do tipo que gostaria de adicionar ao catálogo.

    ![Adicionar recursos a um catálogo](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Quando terminar, clique em **Adicionar**.

    Estes recursos podem agora ser incluídos em pacotes de acesso dentro do catálogo.

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Adicionar um recurso a um catálogo programático

Também pode adicionar um recurso a um catálogo utilizando o Microsoft Graph.  Um utilizador numa função adequada, ou um catálogo e proprietário de recursos, com uma aplicação que tenha a permissão delegada `EntitlementManagement.ReadWrite.All` pode ligar para a API para criar um [AccessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta).

## <a name="remove-resources-from-a-catalog"></a>Remover recursos de um catálogo

Pode remover recursos de um catálogo. Um recurso só pode ser removido de um catálogo se não estiver a ser utilizado em nenhum dos pacotes de acesso do catálogo.

**Papel pré-requisito:** Ver [funções necessárias para adicionar recursos a um catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Catálogos** e, em seguida, abra o catálogo de onde pretende remover recursos.

1. No menu esquerdo, clique em **Recursos.**

1. Selecione os recursos que pretende remover.

1. Clique **em Remover** (ou clique na elipse **(...)** e, em seguida, clique em **Remover o recurso).**


## <a name="add-additional-catalog-owners"></a>Adicionar proprietários de catálogos adicionais

O utilizador que criou um catálogo torna-se o primeiro proprietário do catálogo. Para delegar a gestão de um catálogo, adicione os utilizadores à função de proprietário do catálogo. Isto ajuda a partilhar as responsabilidades de gestão do catálogo. 

Siga estes passos para atribuir um utilizador à função de proprietário do catálogo:

**Papel pré-requisito:** Administrador global, administrador de utilizadores ou proprietário do catálogo

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Catálogos** e, em seguida, abra o catálogo a que pretende adicionar administradores.

1. No menu esquerdo, clique em **Funções e administradores.**

    ![Funções de catálogos e administradores](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Clique **em Adicionar os proprietários** para selecionar os membros para estas funções.

1. Clique **em Selecionar** para adicionar estes membros.

## <a name="edit-a-catalog"></a>Editar um catálogo

Pode editar o nome e a descrição de um catálogo. Os utilizadores vêem esta informação nos detalhes de um pacote de acesso.

**Papel pré-requisito:** Administrador global, administrador de utilizadores ou proprietário do catálogo

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Catálogos** e, em seguida, abra o catálogo que pretende editar.

1. Na página **'Overview'** do catálogo, clique em **Editar**.

1. Editar o nome, descrição ou configurações ativadas do catálogo.

    ![Editar configurações de catálogo](./media/entitlement-management-shared/catalog-edit.png)

1. Clique em **Guardar**.

## <a name="delete-a-catalog"></a>Excluir um catálogo

Pode apagar um catálogo, mas apenas se não tiver pacotes de acesso.

**Papel pré-requisito:** Administrador global, administrador de utilizadores ou proprietário do catálogo

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Catálogos** e, em seguida, abra o catálogo que pretende eliminar.

1. No resumo **do**catálogo, clique em **Apagar**.

1. Na caixa de mensagens que aparece, clique em **Sim**.

### <a name="deleting-a-catalog-programmatically"></a>Apagar um catálogo programáticamente

Também pode eliminar um catálogo utilizando o Microsoft Graph.  Um utilizador numa função adequada com uma aplicação que tenha a permissão delegada `EntitlementManagement.ReadWrite.All` pode ligar para a API para apagar um [AccessPackageCatalog](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta).

## <a name="next-steps"></a>Passos seguintes

- [Delegar governação de acesso a gestores de pacotes de acesso](entitlement-management-delegate-managers.md)