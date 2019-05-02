---
title: Desativar a verificação de e-mail durante consumidor Inscreva-se no Azure Active Directory B2C | Documentos da Microsoft
description: Um tópico que demonstram como desativar a verificação de e-mail durante a inscrição no Azure Active Directory B2C do consumidor.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 01d96370fc7ce601ba5626ca0f1ec28871bf311a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703350"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Desativar a verificação de e-mail durante a inscrição no Azure Active Directory B2C do consumidor 
Quando ativada, o Azure Active Directory (Azure AD) B2C dá um consumidor a capacidade para se inscrever para aplicações, fornecendo um endereço de e-mail e criar uma conta local. O Azure AD B2C garante que os endereços de e-mail válidos, exigindo que os consumidores verificá-los durante o processo de inscrição. Ele também evita que um processo automatizado malicioso gerar falsas contas para os aplicativos.

Alguns desenvolvedores de aplicativo preferem ignorar a verificação de e-mail durante o processo de inscrição e em vez disso, ter consumidores, verifique se o endereço de e-mail mais tarde. Para suportar isto, o Azure AD B2C pode ser configurado para desativar a verificação de e-mail. Se o fizer, cria um processo de inscrição mais suave e oferece aos desenvolvedores a flexibilidade para diferenciar os consumidores que tem verificado o respetivo endereço de e-mail desses consumidores que não o tiver feito.

Por predefinição, os fluxos de utilizador de inscrição têm a verificação de e-mail ativada. Utilize os seguintes passos para desativá-la:

1. Clique em **fluxos de utilizador**.
2. Clique em seu fluxo de utilizador (por exemplo, "B2C_1_SiUp") para abri-lo. 
3. Clique em **layouts de página**.
4. Clique em **página de inscrição de conta Local**.
5. Clique em **endereço de E-Mail** no **nome** coluna com o **atributos de utilizador** secção.
6. Sob **necessita de verificação**, selecione **não**.
7. Clique em **Guardar**, na parte superior do painel. Já está!

> [!NOTE]
> Desativar a verificação de e-mail no processo de inscrição pode levar para lhe enviar spam. Se desativar o predefinido, recomendamos que adicione o seu próprio sistema de verificação.
> 
>
