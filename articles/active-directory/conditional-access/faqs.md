---
title: FaQs de acesso condicional do Diretório Ativo Azure Microsoft Docs
description: Obtenha respostas a perguntas frequentes sobre acesso condicional no Diretório Ativo Azure.
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
ms.custom: has-adal-ref
ms.openlocfilehash: f2df8140d2eb791e83af5ae47b947d614ac2b899
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199408"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>FaQs de acesso condicional do Diretório Ativo Azure

## <a name="which-applications-work-with-conditional-access-policies"></a>Quais as aplicações que funcionam com políticas de Acesso Condicional?

Para obter informações sobre aplicações que funcionem com políticas de Acesso Condicional, consulte [Aplicações e navegadores que utilizem regras de Acesso Condicional no Diretório Ativo do Azure.](concept-conditional-access-cloud-apps.md)

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>As políticas de Acesso Condicional são aplicadas à colaboração B2B e aos utilizadores convidados?

As políticas são aplicadas aos utilizadores de colaboração entre empresas (B2B). No entanto, em alguns casos, um utilizador pode não ser capaz de satisfazer os requisitos de política. Por exemplo, a organização de um utilizador convidado pode não suportar a autenticação de vários fatores. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Uma política SharePoint Online também se aplica ao OneDrive para Negócios?

Sim. Uma política SharePoint Online também se aplica ao OneDrive for Business.

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Por que não posso definir uma política diretamente em aplicações de clientes, como o Word ou o Outlook?

Uma política de acesso condicional define requisitos para aceder a um serviço. É aplicada quando a autenticação ao serviço ocorre. A apólice não está definida diretamente numa aplicação do cliente. Em vez disso, é aplicado quando um cliente chama um serviço. Por exemplo, uma política definida no SharePoint aplica-se aos clientes que chamam SharePoint. Uma política definida no Exchange aplica-se ao Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Uma política de Acesso Condicional aplica-se às contas de serviço?

As políticas de Acesso Condicional aplicam-se a todas as contas dos utilizadores. Isto inclui contas de utilizador que são usadas como contas de serviço. Muitas vezes, uma conta de serviço que funciona sem supervisão não satisfaz os requisitos de uma política de Acesso Condicional. Por exemplo, pode ser necessária a autenticação de vários fatores. As contas de serviço podem ser excluídas de uma política utilizando as definições de gestão da política de Acesso Condicional. 

## <a name="are-microsoft-graph-apis-available-for-configuring-conditional-access-policies"></a>As APIs do Microsoft Graph estão disponíveis para configurar as políticas de acesso condicional?

Atualmente, não. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Qual é a política de exclusão padrão para plataformas de dispositivos não suportadas?

Atualmente, as políticas de Acesso Condicional são aplicadas seletivamente aos utilizadores de dispositivos iOS e Android. As aplicações noutras plataformas de dispositivos não são, por defeito, afetadas pela política de Acesso Condicional para dispositivos iOS e Android. Um administrador de inquilinos pode optar por anular a política global para não permitir o acesso aos utilizadores em plataformas que não são suportadas.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Como funcionam as políticas de Acesso Condicional para as Equipas microsoft?

As Equipas Microsoft dependem fortemente do Exchange Online e do SharePoint Online para cenários de produtividade fundamentais, como reuniões, calendários e partilha de ficheiros. As políticas de Acesso Condicional que são definidas para estas aplicações na nuvem aplicam-se às Equipas microsoft quando um utilizador assina diretamente nas Equipas microsoft.

O Microsoft Teams também é suportado separadamente como uma aplicação de cloud nas políticas de Acesso Condicional do Diretório Ativo do Azure. As políticas de Acesso Condicional que estão definidas para uma aplicação na nuvem aplicam-se às Equipas microsoft quando um utilizador faz o sinal de sintetiza. No entanto, sem as políticas corretas noutras aplicações, como o Exchange Online e o SharePoint Online, ainda poderão aceder diretamente a esses recursos.

Os clientes de desktop da Microsoft Teams para Windows e Mac suportam a autenticação moderna. A autenticação moderna traz o login com base na Biblioteca de Autenticação de Diretórios Ativos do Azure (ADAL) às aplicações de clientes do Microsoft Office através das plataformas.

## <a name="next-steps"></a>Passos seguintes

- Para configurar as políticas de Acesso Condicional para o seu ambiente, consulte as [melhores práticas de acesso condicional no Diretório Ativo Azure.](best-practices.md) 
