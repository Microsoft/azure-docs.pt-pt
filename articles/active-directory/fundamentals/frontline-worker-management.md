---
title: Gestão de trabalhadores da linha da frente - Azure Ative Directory
description: Conheça as capacidades de gestão dos trabalhadores da linha da frente que são fornecidas através do portal My Staff.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: cchiedo
author: Chrispine-Chiedo
manager: CelesteDG
ms.reviewer: stevebal
ms.openlocfilehash: 32cee4ca0558166c44ff83c5ce9d61360e79e535
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969368"
---
# <a name="frontline-worker-management"></a>Gestão de trabalhadores da linha da frente

Os trabalhadores da linha da frente representam mais de 80% da força de trabalho global. No entanto, devido aos processos de alta escala, rápidos e fragmentados, os trabalhadores da linha da frente muitas vezes não dispõem de ferramentas para facilitar um pouco os seus empregos exigentes. A gestão dos trabalhadores da linha da frente traz a transformação digital para toda a força de trabalho da linha da frente. A força de trabalho pode incluir gestores, trabalhadores da linha da frente, operações e TI.

A gestão dos trabalhadores da linha da frente capacita a força de trabalho da linha da frente, facilitando a realização das seguintes atividades:
- Racionalizar tarefas de TI comuns com o Meu Pessoal
- Fácil embarque dos trabalhadores da linha da frente através da autenticação simplificada
- Fornecimento sem emenda de dispositivos partilhados e aprovação segura dos trabalhadores da linha da frente

## <a name="delegated-user-management-through-my-staff"></a>Gestão delegada de utilizadores através do Meu Pessoal

O Azure Ative Directory (Azure AD) oferece a capacidade de delegar a gestão de utilizadores para gestores de linha da frente através do [portal My Staff,](../roles/my-staff-configure.md)ajudando a economizar tempo valioso e reduzir riscos. Ao permitir resets de senha simplificados e gestão de telefone diretamente da loja ou do piso da fábrica, os gestores podem conceder acesso aos funcionários sem encaminhar o pedido através do help-desk, TI ou operações.

![Gestão delegada de utilizadores no portal My Staff](media/concept-fundamentals-frontline-worker/delegated-user-management.png)

## <a name="accelerated-onboarding-with-simplified-authentication"></a>A bordo acelerado com autenticação simplificada

O meu pessoal também permite que os gestores da linha da frente registem os números de telefone dos seus membros da equipa para [a sposição por SMS.](../authentication/howto-authentication-sms-signin.md) Em muitas verticais, os trabalhadores da linha da frente mantêm um nome de utilizador local e uma combinação de palavra-passe, uma solução que é muitas vezes pesada, cara e propensa a erros. Quando o TI permite a autenticação utilizando o login de SMS, os trabalhadores da linha da frente podem iniciar sessão com [uma única sessão de sessão (SSO)](../manage-apps/what-is-single-sign-on.md) para equipas da Microsoft e outras aplicações utilizando apenas o seu número de telefone e uma senha única (OTP) enviada via SMS. Isto torna a inscrição para os trabalhadores da linha da frente simples e segura, proporcionando acesso rápido às aplicações de que mais precisam.

![S-in SMS](media/concept-fundamentals-frontline-worker/sms-signin.png)

Os gestores da linha da frente também podem usar a aplicação Managed Home Screen (MHS) para permitir que os trabalhadores tenham acesso a um conjunto específico de aplicações nos seus dispositivos dedicados ao Android matriculados no Intune. Os dispositivos dedicados estão matriculados com [o modo de dispositivo partilhado Azure AD](../develop/msal-shared-devices.md). Quando configurado no modo quiosque multi-aplicações na consola Microsoft Endpoint Manager (MEM), o MHS é automaticamente lançado como o ecrã inicial padrão do dispositivo e aparece para o utilizador final como o *único* ecrã principal. Para saber mais, veja como [configurar a aplicação de ecrã doméstico gerido pela Microsoft para Android Enterprise.](/mem/intune/apps/app-configuration-managed-home-screen-app)

## <a name="secure-sign-out-of-frontline-workers-from-shared-devices"></a>Segurança de saída de trabalhadores da linha da frente de dispositivos partilhados

Muitas empresas utilizam dispositivos partilhados para que os trabalhadores da linha da frente possam fazer gestão de inventário e transações de ponto de venda, sem o fardo de TI de provisão e rastreio de dispositivos individuais. Com a assinatura do dispositivo partilhado, é fácil para um trabalhador da linha da frente assinar com segurança todas as aplicações em qualquer dispositivo partilhado antes de entregá-lo de volta a um hub ou passá-lo para um colega de equipa no próximo turno. O Microsoft Teams é uma das aplicações que atualmente é suportada em dispositivos partilhados e permite que os trabalhadores da linha da frente vejam tarefas que lhes são atribuídas. Uma vez que um trabalhador assina fora de um dispositivo compartilhado, Intune e Azure AD limpam todos os dados da empresa para que o dispositivo possa ser entregue com segurança ao próximo associado. Pode optar por integrar esta capacidade em todas as suas aplicações [iOS](../develop/msal-ios-shared-devices.md) e [Android](../develop/msal-android-shared-devices.md) da linha de negócios utilizando a [Microsoft Authentication Library](../develop/msal-overview.md).

![Sinalização partilhada do dispositivo](media/concept-fundamentals-frontline-worker/shared-device-signout.png)

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a gestão delegada do utilizador, consulte [a documentação do utilizador do Meu Pessoal](../user-help/my-staff-team-manager.md).
- Para o fornecimento de utilizadores de entrada a partir de SAP SuccessFactors, consulte o tutorial sobre [a configuração do SAP SuccessFactors para o fornecimento de utilizadores do Ative Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
- Para o fornecimento de utilizadores de entrada a partir do Workday, consulte o tutorial sobre [a configuração do Dia de Trabalho para o fornecimento automático do utilizador](../saas-apps/workday-inbound-tutorial.md).
