---
title: Acesso condicional-exigir dispositivos em conformidade-Azure Active Directory
description: Criar uma política de acesso condicional personalizada para exigir dispositivos em conformidade
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 255d1be1c8cfaec366aee6964e41537f6b6c50d8
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576618"
---
# <a name="conditional-access-require-compliant-devices"></a>Acesso condicional: Exigir dispositivos em conformidade

As organizações que implantaram Microsoft Intune podem usar as informações retornadas de seus dispositivos para identificar os dispositivos que atendem aos requisitos de conformidade, como:

* Exigindo um PIN para desbloquear
* Exigindo criptografia do dispositivo
* Exigindo uma versão mínima ou máxima do sistema operacional
* Exigir um dispositivo não está desbloqueado ou com raiz

Essas informações de conformidade de política são encaminhadas ao Azure AD, em que o acesso condicional pode tomar decisões para conceder ou bloquear o acesso a recursos.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para exigir que os dispositivos que acessam os recursos sejam marcados como em conformidade com as políticas de conformidade do Intune da sua organização.

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** > **acesso condicional**.
1. Selecione **nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **atribuições**, selecione **usuários e grupos**
   1. Em **incluir**, selecione **todos os usuários**.
   1. Em **excluir**, selecione **usuários e grupos** e escolha o acesso de emergência da sua organização ou contas de vidro. 
   1. Selecione **Done** (Concluído).
1. Em **aplicativos de nuvem ou ações** > **incluem**, selecione **todos os aplicativos de nuvem**.
   1. Se você precisar excluir aplicativos específicos de sua política, poderá selecioná-los na guia **excluir** em **selecionar aplicativos de nuvem excluídos** e escolher **selecionar**.
   1. Selecione **Done** (Concluído).
1. Em**concessão**de **controles** > de acesso, selecione **exigir que o dispositivo seja marcado como compatível**.
   1. Selecione **selecionar**.
1. Confirme suas configurações e defina **habilitar política** como **ativado**.
1. Selecione **criar** para criar para habilitar a política.

## <a name="next-steps"></a>Passos Seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Simular comportamento de entrada usando a ferramenta de What If de acesso condicional](troubleshoot-conditional-access-what-if.md)

[As políticas de conformidade do dispositivo funcionam com o Azure AD](/intune/device-compliance-get-started.md#device-compliance-policies-work-with-azure-ad)
