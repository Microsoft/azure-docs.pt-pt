---
title: Problema ao guardar as credenciais de administrador ao configurar o aprovisionamento de utilizadores a uma aplicação da galeria do Azure AD | Documentos da Microsoft
description: Como resolver problemas comuns enfrentados ao configurar aprovisionamento de utilizador para uma aplicação já listados na Galeria de aplicações do Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e4af70ae8628f612b8858b99c0d5ae57e78ace4
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963614"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Problema ao guardar as credenciais de administrador ao configurar o aprovisionamento de utilizadores para uma aplicação da Galeria de diretório do Azure Active Directory 

Ao utilizar o portal do Azure para configurar [aprovisionamento automático de utilizadores](user-provisioning.md) para uma aplicação empresarial, pode encontrar uma situação em que:

* O **credenciais de administrador** introduziu para a aplicação são válidos e o **Testar ligação** botão funciona. No entanto, não não possível guardar as credenciais e o portal do Azure devolve uma mensagem de erro genérica.

Se baseado em SAML início de sessão único também é configurado para a mesma aplicação, a causa mais provável do erro é o limite de armazenamento interno, por aplicação do Azure AD para certificados e credenciais foi excedido.

Azure AD atualmente tem a capacidade de armazenamento máximo de 1024 bytes para todos os certificados, tokens secretas, credenciais e dados de configuração relacionados associados a uma única instância de um aplicativo (também conhecido como um serviço principal registo no Azure AD).

Quando baseado em SAML início de sessão único é configurado, o certificado utilizado para assinar os tokens SAML é armazenado aqui e, muitas vezes, consome mais 50% por cento do espaço.

Quaisquer tokens secretas, URIs, endereços de e-mail de notificação, os nomes de utilizador e palavras-passe que obterem introduzidas durante a configuração do aprovisionamento de utilizadores podem fazer com que o limite de armazenamento ser excedido.

## <a name="how-to-work-around-this-issue"></a>Como resolver este problema 

Existem duas maneiras possíveis de contornar este problema hoje:

1. **Utilizar instâncias de aplicações de galeria dois, um para início de sessão único e uma para o aprovisionamento de utilizadores** -colocar o aplicativo de galeria [LinkedIn elevar](../saas-apps/linkedinelevate-tutorial.md) como exemplo, pode adicionar o LinkedIn elevar a partir da Galeria e configurar -lo para início de sessão único. Para o aprovisionamento, adicione outra instância do LinkedIn elevar a partir da Galeria de aplicações do Azure AD e dê-lhe o nome "LinkedIn elevação (aprovisionamento)." Para esta instância segundo, configure [aprovisionamento](../saas-apps/linkedinelevate-provisioning-tutorial.md), mas não início de sessão único. Quando utilizar esta solução, os utilizadores e grupos mesmo tem de ser [atribuídos](assign-user-or-group-access-portal.md) para ambas as aplicações. 

2. **Reduzir a quantidade de dados de configuração armazenadas** -todos os dados introduzidos no [credenciais de administrador](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) secção do separador aprovisionamento é armazenada no mesmo local como o certificado SAML. Embora talvez não seja possível reduzir o comprimento de todos esses dados, alguns campos de configuração opcionais, como o **notificação por E-Mail** pode ser removido.

## <a name="next-steps"></a>Passos Seguintes
[Configurar utilizador provisionamento e desprovisionamento a aplicações SaaS](user-provisioning.md)
