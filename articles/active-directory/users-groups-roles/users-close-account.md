---
title: Fechar sua conta corporativa ou de estudante em um diretório não gerenciado-Azure Active Directory | Microsoft Docs
description: Como fechar sua conta corporativa ou de estudante em um Azure Active Directory não gerenciado.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da4c6b1c1434dae564ab4876a3ab3f341a87097
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891960"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Fechar sua conta corporativa ou de estudante em um diretório não gerenciado

Se você for um usuário em uma organização não gerenciada do Azure Active Directory (Azure AD) e não precisar mais usar aplicativos dessa organização ou manter qualquer associação com ele, poderá fechar sua conta a qualquer momento. Um diretório não gerenciado não tem um administrador global. Os usuários em um diretório não gerenciado podem fechar suas contas por conta própria, sem precisar contatar um administrador.

Os usuários em um diretório não gerenciado são frequentemente criados durante a inscrição de autoatendimento. Um exemplo pode ser um operador de informações em uma organização que se inscreve em um serviço gratuito. Para obter mais informações sobre a inscrição de autoatendimento, consulte [o que é inscrição de autoatendimento para Azure Active Directory?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Antes de começar

Antes de poder fechar sua conta, você deve confirmar os seguintes itens:

* Verifique se você é um usuário de um diretório do Azure AD não gerenciado. Você não poderá fechar sua conta se você pertencer a um diretório gerenciado. Se você pertencer a um diretório gerenciado e quiser fechar sua conta, deverá contatar o administrador. Para obter informações sobre como determinar se você pertence a um diretório não gerenciado, consulte [excluir o usuário do locatário não gerenciado](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Salve os dados que você deseja manter. Para obter informações sobre como enviar uma solicitação de exportação, consulte Acessando [e exportando logs gerados pelo sistema para locatários não gerenciados](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Fechar sua conta é irreversível. Quando você fechar sua conta, todos os dados pessoais serão removidos. Você não terá mais acesso à sua conta e aos dados associados à sua conta.

## <a name="close-your-account"></a>Fechar a sua conta

Para fechar uma conta corporativa ou de estudante não gerenciada, siga estas etapas:

1. Entre para [fechar sua conta](https://go.microsoft.com/fwlink/?linkid=873123), usando a conta que você deseja fechar.

1. Em **minhas solicitações de dados**, selecione **fechar conta**.

    ![Minhas solicitações de dados-fechar conta](./media/users-close-account/close-account.png)

1. Examine a mensagem de confirmação e selecione **Sim**.

    ![Minhas solicitações de dados-confirmar fechamento](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Passos seguintes

- [O que é a inscrição de autoatendimento para o Azure Active Directory?](directory-self-service-signup.md)
- [Excluir o usuário do locatário não gerenciado](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Acessando e exportando logs gerados pelo sistema para locatários não gerenciados](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
