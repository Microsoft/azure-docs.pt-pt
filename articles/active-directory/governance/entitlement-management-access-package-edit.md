---
title: Ocultar ou excluir um pacote de acesso no gerenciamento de direitos do Azure AD (versão prévia) – Azure Active Directory
description: Saiba como ocultar ou excluir um pacote de acesso no gerenciamento de direitos Azure Active Directory (versão prévia).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab6f3a86d8e88ee2d7465fd32bde7b7555f1f326
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389171"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management-preview"></a>Ocultar ou excluir um pacote de acesso no gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> O gerenciamento de direitos do Azure Active Directory (AD do Azure) está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os pacotes do Access são detectáveis por padrão. Isso significa que, se uma política permitir que um usuário solicite o pacote de acesso, ele verá automaticamente o pacote de acesso listado em seu portal meu acesso. No entanto, você pode alterar a configuração **oculta** para que o pacote de acesso não esteja listado no meu portal de acesso do usuário.

Este artigo descreve como ocultar ou excluir um pacote de acesso.

## <a name="change-the-hidden-setting"></a>Alterar a configuração oculta

Siga estas etapas para alterar a configuração **oculta** de um pacote de acesso.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Na página Visão geral, clique em **Editar**.

1. Defina a configuração **oculta** .

    Se definido como **não**, o pacote de acesso será listado no meu portal de acesso do usuário.

    Se definido como **Sim**, o pacote de acesso não será listado no meu portal de acesso do usuário. A única maneira de um usuário exibir o pacote de acesso é se eles tiverem o **link direto meu acesso do portal** para o pacote de acesso. Para obter mais informações, consulte [compartilhar link para solicitar um pacote de acesso](entitlement-management-access-package-settings.md).

## <a name="delete-an-access-package"></a>Excluir um pacote de acesso

Um pacote do Access só poderá ser excluído se não tiver nenhuma atribuição de usuário ativa. Siga estas etapas para excluir um pacote de acesso.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu à esquerda, clique em **atribuições** e remova o acesso para todos os usuários.

1. No menu à esquerda, clique em **visão geral** e, em seguida, clique em **excluir**.

1. Na mensagem de exclusão exibida, clique em **Sim**.

## <a name="next-steps"></a>Passos seguintes

- [Exibir, adicionar e remover atribuições para um pacote de acesso](entitlement-management-access-package-assignments.md)
- [Exibir relatórios e logs](entitlement-management-reports.md)
