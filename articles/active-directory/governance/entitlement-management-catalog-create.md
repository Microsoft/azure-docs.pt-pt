---
title: Criar e gerir um catálogo na gestão de direitos do Azure AD (pré-visualização) - Azure Active Directory
description: Saiba como criar um novo contentor de recursos e pacotes de acesso na gestão de direitos do Azure Active Directory (pré-visualização).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d9220cd2162b4c8cb77c1e7abd0372052f5454
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541619"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Criar e gerir um catálogo na gestão de direitos do Azure AD (pré-visualização)

> [!IMPORTANT]
> Gestão de direitos do Active Directory (Azure AD) do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Criar um catálogo

Um catálogo é um contentor de recursos e pacotes de acesso. Criar um catálogo de quando deseja agrupar recursos relacionados e aceder aos pacotes. Quem cria o catálogo, torna-se o primeiro proprietário de catálogo. Um proprietário de catálogo pode adicionar os proprietários de catálogo adicionais.

**Função de pré-requisitos:** Administrador de utilizadores ou de criador de catálogo

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **catálogos**.

    ![Catálogos de gestão de direitos no portal do Azure](./media/entitlement-management-catalog-create/catalogs.png)

1. Clique em **novo catálogo**.

1. Introduza um nome exclusivo para o catálogo e forneça uma descrição.

    Os utilizadores verão estas informações nos detalhes de um pacote de acesso.

1. Se pretender que os pacotes de acesso neste catálogo estar disponível para que os utilizadores peçam assim que forem criados, defina **Enabled** ao **Sim**.

1. Se quiser permitir que os usuários em diretórios externos selecionados para poder pedir acesso pacotes neste catálogo, defina **ativada para utilizadores externos** ao **Sim**.

    ![Novo painel de catálogo](./media/entitlement-management-catalog-create/new-catalog.png)

1. Clique em **criar** para criar o catálogo.

## <a name="add-resources-to-a-catalog"></a>Adicionar recursos a um catálogo

Para incluir recursos num pacote de acesso, os recursos tem de existir num catálogo. Os tipos de recursos, que pode adicionar são grupos, aplicações e sites do SharePoint Online.

**Função de pré-requisitos:** Administrador de utilizador ou proprietário de catálogo

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo de que pretende adicionar recursos para.

1. No menu à esquerda, clique em **recursos**.

1. Clique em **adicionar recursos**.

1. Clique num tipo de recurso: **Grupos**, **aplicativos**, ou **sites do SharePoint**.

    Se for um criador de catálogo, pode adicionar qualquer grupo do Office 365 ou o grupo de segurança do Azure AD que é proprietário para o seu catálogo. Se existe um grupo que pretende atribuir aos utilizadores, mas não é o proprietário do grupo, terá de ter um administrador do utilizador adicione esse grupo ao seu catálogo.

    Se for um criador de catálogo, pode adicionar qualquer aplicação de enterprise do Azure AD for o proprietário, incluindo aplicações SaaS e suas próprias aplicações federadas com o Azure AD, para o seu catálogo. Se existir uma aplicação que pretende atribuir aos utilizadores, mas não é o proprietário, terá de ter um administrador de utilizadores que adicionar essa aplicação para o seu catálogo. Depois do aplicativo faz parte do catálogo, pode selecionar qualquer uma das funções do aplicativo num pacote de acesso.

1. Selecione um ou mais recursos do tipo que pretende adicionar ao catálogo.

1. Quando terminar, clique em **adicionar**.

    Esses recursos agora podem ser incluídos em pacotes de acesso no catálogo.

## <a name="remove-resources-from-a-catalog"></a>Remover recursos de um catálogo

Pode remover recursos de um catálogo. Um recurso só pode ser removido de um catálogo, se não está a ser utilizado em qualquer um dos pacotes de acesso ao catálogo.

**Função de pré-requisitos:** Administrador de utilizador ou proprietário de catálogo

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo de que pretende remover os recursos a partir de.

1. No menu à esquerda, clique em **recursos**.

1. Selecione os recursos que pretende remover.

1. Clique em **Remova** (ou clique nas reticências ( **...** ) e, em seguida, clique em **remover recurso**).

## <a name="add-catalog-owners-or-access-package-managers"></a>Adicionar proprietários de catálogo ou gestores de pacotes de acesso

Se quiser delegar a gestão de catálogo ou os pacotes de acesso no catálogo, adicionar proprietários de catálogo ou aceder a gestores de pacotes. Quem cria um catálogo, torna-se o primeiro proprietário de catálogo.

**Função de pré-requisitos:** Administrador de utilizador ou proprietário de catálogo

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo de que pretende adicionar os administradores.

1. No menu à esquerda, clique em **funções e os administradores**.

1. Clique em **adicionar proprietários** ou **adicionar gestores de pacotes de acesso** para selecionar os membros destas funções.

1. Clique em **selecione** para adicionar esses membros.

## <a name="edit-a-catalog"></a>Editar um catálogo

Pode editar o nome e descrição para um catálogo. Os utilizadores verão estas informações nos detalhes de um pacote de acesso.

**Função de pré-requisitos:** Administrador de utilizador ou proprietário de catálogo

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo de que pretende editar.

1. Sobre o catálogo **descrição geral** página, clique em **editar**.

1. Edite nome ou descrição do catálogo.

1. Clique em **Guardar**.

## <a name="delete-a-catalog"></a>Eliminar um catálogo

Pode eliminar um catálogo, mas apenas se não tem quaisquer pacotes de acesso.

**Função de pré-requisitos:** Administrador de utilizador ou proprietário de catálogo

1. No portal do Azure, clique em **do Azure Active Directory** e, em seguida, clique em **governação de identidade**.

1. No menu à esquerda, clique em **catálogos** e, em seguida, abra o catálogo de que pretende eliminar.

1. Sobre o catálogo **descrição geral**, clique em **eliminar**.

1. Na caixa de mensagem que aparece, clique em **Sim**.

## <a name="next-steps"></a>Passos Seguintes

- [Crie e gerencie um pacote de acesso](entitlement-management-access-package-create.md)
