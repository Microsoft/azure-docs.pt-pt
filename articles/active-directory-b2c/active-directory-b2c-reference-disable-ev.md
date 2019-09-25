---
title: Desabilitar a verificação de email durante a inscrição do cliente no Azure Active Directory B2C
description: Saiba como desabilitar a verificação de email durante a inscrição do cliente no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9a7adc04991dd4a472bdaf1aa47aacaf6cdeb190
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256920"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Desabilitar a verificação de email durante a inscrição do cliente no Azure Active Directory B2C

Por padrão, Azure Active Directory B2C (Azure AD B2C) verifica o endereço de email do cliente para contas locais (contas para usuários que se inscrevem com endereço de email ou nome de usuário). O Azure AD B2C garante endereços de email válidos exigindo que os clientes os verifiquem durante o processo de inscrição. Ele também impede que os atores mal-intencionados usem processos automatizados para gerar contas fraudulentas em seus aplicativos.

Alguns desenvolvedores de aplicativos preferem ignorar a verificação de email durante o processo de inscrição e, em vez disso, fazer com que os clientes verifiquem seu endereço de email posteriormente. Para dar suporte a isso, Azure AD B2C pode ser configurado para desabilitar a verificação de email. Isso cria um processo de inscrição mais suave e dá aos desenvolvedores a flexibilidade para diferenciar os clientes que verificaram seu endereço de email dos clientes que não têm.

Siga estas etapas para desabilitar a verificação de email:

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Use o filtro **diretório + assinatura** no menu superior para selecionar o diretório que contém seu locatário Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Selecione **fluxos de usuário**.
1. Selecione o fluxo de usuário para o qual você deseja desabilitar a verificação de email. Por exemplo, *B2C_1_signinsignup*.
1. Selecione **layouts de página**.
1. Selecione **a página de inscrição da conta local**.
1. Em **atributos de usuário**, selecione **endereço de email**.
1. Na lista suspensa **requer verificação** , selecione **não**.
1. Selecione **Guardar**. A verificação de email agora está desabilitada para este fluxo de usuário.

> [!WARNING]
> Desabilitar a verificação de email no processo de inscrição pode levar a spam. Se você desabilitar a verificação de email padrão fornecida pelo Azure AD B2C, recomendamos que implemente um sistema de verificação de substituição.
