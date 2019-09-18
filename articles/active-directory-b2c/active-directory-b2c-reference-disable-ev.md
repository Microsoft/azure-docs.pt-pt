---
title: Desabilitar a verificação de email durante a inscrição do consumidor no Azure Active Directory B2C | Microsoft Docs
description: Um tópico que demonstra como desabilitar a verificação de email durante a inscrição do consumidor no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d827843bdbaff1ad95dce3318f39867abe8b2d0f
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065628"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Desabilitar a verificação de email durante a inscrição do consumidor no Azure Active Directory B2C
Quando habilitado, o Azure Active Directory B2C (Azure AD B2C) fornece a um consumidor a capacidade de se inscrever para aplicativos fornecendo um endereço de email e criando uma conta local. O Azure AD B2C garante endereços de email válidos exigindo que os consumidores os verifiquem durante o processo de inscrição. Ele também impede que um processo automatizado mal-intencionado gere contas falsas para os aplicativos.

Alguns desenvolvedores de aplicativos preferem ignorar a verificação de email durante o processo de inscrição e, em vez disso, fazer com que os consumidores verifiquem o endereço de email mais tarde. Para dar suporte a isso, Azure AD B2C pode ser configurado para desabilitar a verificação de email. Isso cria um processo de inscrição mais suave e dá aos desenvolvedores a flexibilidade para diferenciar os consumidores que verificaram seu endereço de email desses consumidores que não têm.

Por padrão, os fluxos de usuário de inscrição têm a verificação de email ativada. Use as etapas a seguir para desativá-lo:

1. Clique em **fluxos de usuário**.
2. Clique em seu fluxo de usuário (por exemplo, "B2C_1_SiUp") para abri-lo.
3. Clique em **layouts de página**.
4. Clique em **página de inscrição da conta local**.
5. Clique em **endereço de email** na coluna **nome** na seção atributos de **usuário** .
6. Em **requer verificação**, selecione **não**.
7. Clique em **Guardar**, na parte superior do painel. Você terminou!

> [!NOTE]
> Desabilitar a verificação de email no processo de inscrição pode levar a spam. Se você desabilitar o padrão, recomendamos adicionar seu próprio sistema de verificação.
>
>
