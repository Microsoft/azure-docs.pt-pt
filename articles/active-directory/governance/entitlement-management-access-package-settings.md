---
title: Partilhar o link para solicitar um pacote de acesso na gestão de direitos Azure AD - Azure Ative Directory
description: Saiba como partilhar o link para solicitar um pacote de acesso na gestão de direitos do Azure Ative Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32973d063c36a45eee79db0b4da4e7419cb70013
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798551"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Partilhar o link para solicitar um pacote de acesso na gestão de direitos Azure AD

A maioria dos utilizadores do seu diretório pode iniciar sposição no portal My Access e ver automaticamente uma lista de pacotes de acesso que podem solicitar. No entanto, para utilizadores de parceiros de negócio externos que ainda não estão no seu diretório, terá de enviar-lhes um link que possam utilizar para solicitar um pacote de acesso. 

Desde que o catálogo do pacote de acesso esteja [ativado para utilizadores externos](entitlement-management-catalog-create.md) e tenha uma [política para o diretório do utilizador externo,](entitlement-management-access-package-request-policy.md)o utilizador externo pode utilizar o link do portal My Access para solicitar o pacote de acesso.

## <a name="share-link-to-request-an-access-package"></a>Partilhar link para solicitar um pacote de acesso

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo ou gestor de pacotes access

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de Acesso** e, em seguida, abra o pacote de acesso.

1. Na página 'Visão Geral', copie o **link do portal My Access**.

    ![Visão geral do pacote de acesso - O link do portal Do Meu Acesso](./media/entitlement-management-shared/my-access-portal-link.png)

    É importante que copie todo o link do portal My Access ao enviá-lo para um parceiro de negócios interno. Isto garante que o parceiro terá acesso ao portal do seu diretório para fazer o seu pedido. O link começa com `myaccess` , inclui uma dica de diretório, e termina com um pacote de acesso ID.  (Para o Governo dos EUA, o domínio no link do portal My Access será `myaccess.microsoft.us` .)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Envie e-mail ou envie o link para o seu parceiro de negócios externo. Podem partilhar a ligação com os seus utilizadores para solicitar o pacote de acesso.

## <a name="next-steps"></a>Passos seguintes

- [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Aprovar ou negar pedidos de acesso](entitlement-management-request-approve.md)