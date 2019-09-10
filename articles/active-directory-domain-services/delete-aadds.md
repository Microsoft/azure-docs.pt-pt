---
title: Desabilitar Azure Active Directory Domain Services | Microsoft Docs '
description: Saiba como desabilitar Azure Active Directory Domain Services usando o portal do Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: iainfou
ms.openlocfilehash: 05c4e73c56b79c6e313ea15124bd0f3d17b2fc70
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842605"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Desabilitar Azure Active Directory Domain Services usando o portal do Azure

Se você não precisar mais de um domínio gerenciado, poderá excluir uma instância do Azure Active Directory Domain Services (AD DS do Azure). Não há nenhuma opção para desativar ou desabilitar temporariamente um domínio gerenciado AD DS do Azure. Excluir o domínio gerenciado AD DS do Azure não exclui ou, de outra forma, afeta negativamente o locatário do Azure AD. Este artigo mostra como usar o portal do Azure para excluir um domínio gerenciado do Azure AD DS.

> [!WARNING]
> **A exclusão é permanente e não pode ser revertida.**
> Quando você exclui um domínio gerenciado do Azure AD DS, ocorrem as seguintes etapas:
>   * Os controladores de domínio para o domínio gerenciado são desprovisionados e removidos da rede virtual.
>   * Os dados no domínio gerenciado são excluídos permanentemente. Esses dados incluem UOs personalizadas, GPOs, registros DNS personalizados, entidades de serviço, GMSAs, etc. que você criou.
>   * Os computadores ingressados no domínio gerenciado perdem sua relação de confiança com o domínio e precisam ser retirados do domínio.
>       * Você não pode entrar nesses computadores usando credenciais corporativas do AD. Em vez disso, você deve usar as credenciais de administrador local para o computador.

## <a name="delete-the-managed-domain"></a>Excluir o domínio gerenciado

Para excluir um domínio gerenciado AD DS do Azure, conclua as seguintes etapas:

1. Na portal do Azure, procure e selecione **Azure AD Domain Services**.
1. Selecione o nome do seu domínio gerenciado AD DS do Azure, como *contoso.com*.
1. Na página **Descrição geral**, selecione **Eliminar**. Para confirmar a exclusão, digite o nome de domínio do domínio gerenciado novamente e, em seguida, selecione **excluir**.

Pode levar de 15-20 minutos ou mais para excluir o domínio gerenciado AD DS do Azure.

## <a name="next-steps"></a>Passos seguintes

Considere [compartilhar comentários][feedback] para os recursos que você gostaria de ver no Azure AD DS.

Se você quiser começar a usar o Azure AD DS novamente, consulte [criar e configurar uma instância de Azure Active Directory Domain Services][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
