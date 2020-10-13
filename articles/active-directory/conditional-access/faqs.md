---
title: Azure Ative Directy Acesso Condicionado FAQs / Microsoft Docs
description: Obtenha respostas para perguntas frequentes sobre acesso condicional no Diretório Ativo Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 9694dd06b67219e0f02c3fe1c7c53d1fc103a0c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258630"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Perguntas de acesso condicionado a Azure Ative

## <a name="which-applications-work-with-conditional-access-policies"></a>Quais aplicações funcionam com políticas de Acesso Condicional?

Para obter informações sobre aplicações que funcionem com políticas de Acesso Condicional, consulte [aplicações e navegadores que utilizam regras de Acesso Condicional no Diretório Ativo Azure.](concept-conditional-access-cloud-apps.md)

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>As políticas de acesso condicional são aplicadas para a colaboração B2B e para os utilizadores convidados?

As políticas são aplicadas aos utilizadores de colaboração entre empresas (B2B). No entanto, em alguns casos, um utilizador pode não ser capaz de satisfazer os requisitos de política. Por exemplo, a organização de um utilizador convidado pode não suportar a autenticação de vários fatores. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Uma política SharePoint Online também se aplica ao OneDrive for Business?

Sim. Uma política SharePoint Online também se aplica ao OneDrive for Business. Para obter mais informações, consulte o artigo, [dependências do serviço de acesso condicional](service-dependencies.md) e considere como direcionar as políticas para a [aplicação office 365.](concept-conditional-access-cloud-apps.md#office-365)

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Por que não posso definir uma política diretamente em aplicativos de clientes, como o Word ou o Outlook?

Uma política de acesso condicional define requisitos para o acesso a um serviço. É aplicado quando a autenticação para esse serviço ocorre. A apólice não está definida diretamente numa aplicação de cliente. Em vez disso, é aplicado quando um cliente chama um serviço. Por exemplo, uma política definida no SharePoint aplica-se aos clientes que ligam para o SharePoint. Uma política definida em Exchange aplica-se ao Outlook. Para obter mais informações, consulte o artigo, [dependências do serviço de acesso condicional](service-dependencies.md) e considere como direcionar as políticas para a [aplicação office 365.](concept-conditional-access-cloud-apps.md#office-365)

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Uma política de acesso condicional aplica-se às contas de serviço?

As políticas de acesso condicional aplicam-se a todas as contas dos utilizadores. Isto inclui contas de utilizador que são usadas como contas de serviço. Muitas vezes, uma conta de serviço que funciona sem vigilância não pode satisfazer os requisitos de uma política de acesso condicional. Por exemplo, pode ser necessária autenticação de vários fatores. As contas de serviço podem ser excluídas de uma política utilizando uma [exclusão de utilizador ou grupo.](concept-conditional-access-users-groups.md#exclude-users) 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Qual é a política de exclusão padrão para plataformas de dispositivos não suportadas?

Atualmente, as políticas de Acesso Condicional são aplicadas seletivamente aos utilizadores de dispositivos iOS e Android. As aplicações noutras plataformas de dispositivos não são, por padrão, afetadas pela política de Acesso Condicional para dispositivos iOS e Android. Um administrador inquilino pode optar por anular a política global para não permitir o acesso aos utilizadores em plataformas que não sejam suportadas.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Como funcionam as políticas de Acesso Condicional para as Equipas microsoft?

As Equipas microsoft dependem fortemente do Exchange Online e do SharePoint Online para cenários de produtividade fundamentais, como reuniões, calendários e partilha de ficheiros. As políticas de Acesso Condicional que são definidas para estas aplicações na nuvem aplicam-se às Equipas da Microsoft quando um utilizador assina diretamente nas Equipas da Microsoft.

O Microsoft Teams também é suportado separadamente como uma aplicação em nuvem em políticas de Acesso Condicional. As políticas de Acesso Condicional que estão definidas para uma aplicação em nuvem aplicam-se às Equipas da Microsoft quando um utilizador se inscreve. No entanto, sem as políticas corretas em outras aplicações como o Exchange Online e o SharePoint Online, os utilizadores ainda poderão aceder diretamente a esses recursos.

Os clientes de desktop da Microsoft Teams para Windows e Mac suportam a autenticação moderna. A autenticação moderna traz o login com base na Azure Ative Directory Authentication Library (ADAL) para aplicações de clientes do Microsoft Office através das plataformas.

Para obter mais informações, consulte o artigo, [dependências do serviço de acesso condicional](service-dependencies.md) e considere como direcionar as políticas para a [aplicação office 365.](concept-conditional-access-cloud-apps.md#office-365)

## <a name="why-are-some-tabs-not-working-in-microsoft-teams-after-enabling-conditional-access-policies"></a>Porque é que alguns separadores não funcionam nas Equipas da Microsoft depois de permitirem políticas de acesso condicional?

Depois de permitir algumas políticas de acesso condicional ao inquilino em Microsoft Teams, certos separadores podem deixar de funcionar no cliente do ambiente de trabalho como esperado. No entanto, os separadores afetados funcionam quando utilizam o cliente web do Microsoft Teams. Os separadores afetados podem incluir Power BI, Forms, VSTS, PowerApps e SharePoint List.

Para ver os separadores afetados tem de utilizar o cliente web do Teams no Edge, No Internet Explorer ou no Chrome com a extensão das Contas Windows 10 instalada. Alguns separadores dependem da autenticação web, o que não funciona no cliente de desktop da Microsoft Teams quando o Acesso Condicional está ativado. A Microsoft está a trabalhar com parceiros para ativar estes cenários. Até à data, temos ativado cenários envolvendo Planner, OneNote e Stream.

## <a name="next-steps"></a>Passos seguintes

- Para configurar políticas de acesso condicional para o seu ambiente, consulte as [melhores práticas de acesso condicional no Diretório Ativo Azure.](best-practices.md) 
