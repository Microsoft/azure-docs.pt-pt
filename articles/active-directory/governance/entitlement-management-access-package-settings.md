---
title: Compartilhar link para solicitar um pacote de acesso no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba como compartilhar o link para solicitar um pacote de acesso em Azure Active Directory gerenciamento de direitos.
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
ms.openlocfilehash: 94d32bf26def6832f51b4e09c697bca885162dc2
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174412"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Compartilhar link para solicitar um pacote de acesso no gerenciamento de direitos do Azure AD

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