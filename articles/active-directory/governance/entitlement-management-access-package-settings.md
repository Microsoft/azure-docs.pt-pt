---
title: Compartilhar link para solicitar um pacote de acesso no gerenciamento de direitos do Azure AD (versão prévia) – Azure Active Directory
description: Saiba como compartilhar o link para solicitar um pacote de acesso em Azure Active Directory gerenciamento de direitos (versão prévia).
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
ms.openlocfilehash: 91b98df1f93991d4781283bc38b02dc20d11268d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392335"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management-preview"></a>Compartilhar link para solicitar um pacote de acesso no gerenciamento de direitos do Azure AD (versão prévia)

> [!IMPORTANT]
> O gerenciamento de direitos do Azure Active Directory (AD do Azure) está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A maioria dos usuários em seu diretório pode entrar no portal meu acesso e ver automaticamente uma lista de pacotes de acesso que eles podem solicitar. No entanto, para usuários externos de parceiros comerciais que ainda não estão em seu diretório, você precisará enviar um link que eles possam usar para solicitar um pacote de acesso. 

Desde que o catálogo do pacote de acesso esteja [habilitado para usuários externos](entitlement-management-catalog-create.md) e você tenha uma [política para o diretório do usuário externo](entitlement-management-access-package-request-policy.md), o usuário externo poderá usar o link meu portal de acesso para solicitar o pacote de acesso.

## <a name="share-link-to-request-an-access-package"></a>Compartilhar link para solicitar um pacote de acesso

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. Na portal do Azure, clique em **Azure Active Directory** e, em seguida, clique em **governança de identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Na página Visão geral, copie o **link meu portal de acesso**.

    ![Visão geral do pacote de acesso – link meu portal de acesso](./media/entitlement-management-shared/my-access-portal-link.png)

    É importante que você copie todo o link meu portal de acesso ao enviá-lo para um parceiro comercial interno. Isso garante que o parceiro terá acesso ao portal do seu diretório para fazer sua solicitação. O link começa com `myaccess`, inclui uma dica de diretório e termina com uma ID de pacote de acesso.

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Envie um email para o parceiro comercial externo. Eles podem compartilhar o link com seus usuários para solicitar o pacote de acesso.

## <a name="next-steps"></a>Passos seguintes

- [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Aprovar ou negar solicitações de acesso](entitlement-management-request-approve.md)