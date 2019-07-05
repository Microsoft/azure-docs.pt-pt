---
title: FAQs de acesso condicional do Azure Active Directory | Documentos da Microsoft
description: Obtenha respostas às perguntas mais frequentes sobre o acesso condicional no Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad0494868c494b488a238a81e504c58552813907
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508984"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>FAQs de acesso condicional do Azure Active Directory

## <a name="which-applications-work-with-conditional-access-policies"></a>As aplicações que funcionam com políticas de acesso condicional?

Para obter informações sobre aplicações que funcionam com políticas de acesso condicional, consulte [aplicativos e os browsers que utilizam regras de acesso condicional no Azure Active Directory](technical-reference.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Políticas de acesso condicional são aplicadas para colaboração B2B e os utilizadores convidados?

As políticas são impostas para utilizadores de colaboração empresa-empresa (B2B). No entanto, em alguns casos, um utilizador poderá não ser capaz de satisfazer os requisitos da política. Por exemplo, a organização de um utilizador convidado não suportem autenticação multifator. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Uma política do SharePoint Online também se aplica ao OneDrive para empresas?

Sim. Uma política do SharePoint Online também se aplica ao OneDrive para empresas.

## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Por que não é possível definir uma política em aplicações de cliente, como o Word ou do Outlook?

Uma política de acesso condicional define os requisitos para aceder a um serviço. Ele é imposto quando ocorre a autenticação para esse serviço. A política não está definida diretamente num aplicativo de cliente. Em vez disso, esta é aplicada quando um cliente chama um serviço. Por exemplo, uma política definida na SharePoint aplica-se a clientes chamando o SharePoint. Uma política definida na Exchange aplica-se ao Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Uma política de acesso condicional é aplicável a contas de serviço?

Aplicam políticas de acesso condicional a todas as contas de utilizador. Isto inclui contas de utilizador que são utilizadas como contas de serviço. Muitas vezes, uma conta de serviço que executa automática não é possível satisfazer os requisitos de uma política de acesso condicional. Por exemplo, a autenticação multifator poderá ser necessária. Contas de serviço podem ser excluídas da política de uma utilizando as definições de gestão de política de acesso condicional. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Graph APIs estão disponíveis para configurar políticas de acesso condicional?

Atualmente, não. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>O que é a política de exclusão predefinida para plataformas de dispositivos não suportados?

Atualmente, as políticas de acesso condicional são seletivamente impostas nos utilizadores de dispositivos iOS e Android. Aplicações em outras plataformas de dispositivos, por predefinição, não são afetadas pela política de acesso condicional para dispositivos iOS e Android. Um administrador de inquilinos pode optar por substituir a política global para não permitir o acesso a utilizadores em plataformas que não são suportadas.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Como funcionam as políticas de acesso condicional para o Microsoft Teams?

Microsoft Teams baseia no Exchange Online e SharePoint Online para cenários de produtividade básicos, como reuniões, calendários e compartilhamento de arquivos. Aplicam políticas de acesso condicional que estão definidas para estas aplicações na cloud para o Microsoft Teams quando um utilizador inicia sessão diretamente no Microsoft Teams.

Microsoft Teams também é suportado em separado como uma aplicação da cloud nas políticas do Azure Active Directory condicional acesso. As políticas de acesso condicional que estão definidas para uma aplicação na cloud aplicam-se ao Microsoft Teams quando um utilizador inicia sessão. No entanto, sem as políticas corretas em outros aplicativos como o Exchange Online e SharePoint Online os utilizadores podem ainda ser capazes de aceder diretamente a esses recursos.

Os clientes de ambiente de trabalho Microsoft Teams para Windows e Mac suportam a autenticação moderna. Autenticação moderna inclui o início de sessão com base no Azure Active Directory Authentication Library (ADAL) para aplicações de cliente do Microsoft Office em todas as plataformas.

## <a name="next-steps"></a>Passos Seguintes

- Para configurar políticas de acesso condicional para o seu ambiente, consulte a [melhores práticas para acesso condicional no Azure Active Directory](best-practices.md). 
