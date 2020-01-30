---
title: Desativar a verificação de e-mail durante o atendimento ao cliente
titleSuffix: Azure AD B2C
description: Saiba como desativar a verificação de e-mail durante a inscrição do cliente no Azure Ative Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c5b3598e33dac131c8881248a5f4b740a6302e20
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848021"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Desativar a verificação de e-mail durante a inscrição do cliente no Diretório Ativo Azure B2C

Por predefinição, o Azure Ative Directory B2C (Azure AD B2C) verifica o endereço de e-mail do seu cliente para contas locais (contas para utilizadores que se inscrevam com endereço de e-mail ou nome de utilizador). O Azure AD B2C garante endereços de e-mail válidos, exigindo que os clientes os verifiquem durante o processo de inscrição. Também impede que um ator malicioso utilize processos automatizados para gerar contas fraudulentas nas suas aplicações.

Alguns desenvolvedores de aplicações preferem ignorar a verificação de e-mail durante o processo de inscrição e, em vez disso, os clientes verificarem o seu endereço de e-mail mais tarde. Para suportar isto, o Azure AD B2C pode ser configurado para desativar a verificação de e-mail. Fazê-lo cria um processo de inscrição mais suave e dá aos programadores a flexibilidade para diferenciar os clientes que verificaram o seu endereço de e-mail de clientes que não o tenham feito.

Siga estes passos para desativar a verificação de e-mail:

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Utilize o filtro de **subscrição Diretório +** no menu superior para selecionar o diretório que contém o seu inquilino Azure AD B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Selecione **fluxos de utilizador**.
1. Selecione o fluxo do utilizador para o qual pretende desativar a verificação de e-mail. Por exemplo, *B2C_1_signinsignup.*
1. Selecione **os layouts da página**.
1. Selecione **página de inscrição da conta local**.
1. Em **atributos do Utilizador,** selecione **Endereço de E-mail**.
1. Na **verificação requerida,** selecione **Nº**.
1. Selecione **Guardar**. A verificação por e-mail está agora desativada para este fluxo de utilizador.

> [!WARNING]
> A desativação da verificação de e-mails no processo de inscrição pode levar ao spam. Se desativar a verificação de e-mail fornecida por Acesso AD B2C por predefinido, recomendamos que implemente um sistema de verificação de substituição.
