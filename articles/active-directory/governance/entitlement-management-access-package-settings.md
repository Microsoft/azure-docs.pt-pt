---
title: Link de partilha para solicitar um pacote de acesso na gestão de direitos da Azure AD - Azure Ative Directory
description: Saiba como partilhar link para solicitar um pacote de acesso na gestão de direitos do Diretório Ativo azure.
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
ms.openlocfilehash: ea90032b1f0cfe598ffdb3d35448a996f3111036
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968753"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Link de partilha para solicitar um pacote de acesso na gestão de direitos da AD Azure

A maioria dos utilizadores do seu diretório pode iniciar sessão no portal My Access e ver automaticamente uma lista de pacotes de acesso que podem solicitar. No entanto, para utilizadores parceiros de negócios externos que ainda não estejam no seu diretório, terá de enviar-lhes um link que possam usar para solicitar um pacote de acesso. 

Enquanto o catálogo do pacote de acesso estiver [ativado para utilizadores externos](entitlement-management-catalog-create.md) e tiver uma [política para o diretório do utilizador externo,](entitlement-management-access-package-request-policy.md)o utilizador externo pode utilizar o link do portal My Access para solicitar o pacote de acesso.

## <a name="share-link-to-request-an-access-package"></a>Partilhar link para solicitar um pacote de acesso

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário do catálogo ou gestor de pacotes de acesso

1. No portal Azure, clique em **Azure Ative Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Na página 'Visão Geral', copie o **link do portal My Access**.

    ![Visão geral do pacote de acesso - My Access portal link](./media/entitlement-management-shared/my-access-portal-link.png)

    É importante que copie todo o link do portal My Access ao enviá-lo a um parceiro de negócios interno. Isto garante que o parceiro terá acesso ao portal do seu diretório para fazer o seu pedido. O link começa com `myaccess`, inclui uma dica de diretório, e termina com um id de pacote de acesso.  (Para o Governo dos EUA, o domínio no portal My Access será `myaccess.microsoft.us`.)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Envie um e-mail ou envie o link para o seu parceiro de negócios externo. Podem partilhar o link com os seus utilizadores para solicitar o pacote de acesso.

## <a name="next-steps"></a>Passos seguintes

- [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Aprovar ou negar pedidos de acesso](entitlement-management-request-approve.md)