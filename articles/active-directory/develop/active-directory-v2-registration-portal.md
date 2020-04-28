---
title: Referência do portal de registo de aplicações [ Azure
titleSuffix: Microsoft identity platform
description: Uma descrição das funcionalidades no portal de registo de aplicações da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ms.openlocfilehash: 910aaf84d3563d4410826d3c0bdfde3d2dfc75e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885638"
---
# <a name="app-registration-reference"></a>Referência de registo de aplicação

Este documento fornece contexto e descrições de várias funcionalidades encontradas na experiência de [registos](https://aka.ms/appregistrations) da App no portal Azure.

## <a name="my-applications-or-converged-applications"></a>Minhas aplicações ou aplicações convergentes

Esta lista contém todas as suas aplicações registadas para utilização com a plataforma de identidade da Microsoft (v2.0) ponto final. Estas aplicações têm a capacidade de contratar utilizadores com contas pessoais da Microsoft e contas de trabalho/escola do Azure Ative Directory. Para saber mais sobre o ponto final da plataforma de identidade, consulte a visão geral do [v2.0.](active-directory-appmodel-v2-overview.md) Estas aplicações também podem ser utilizadas para integrar `https://login.live.com`com o ponto final de autenticação da conta Microsoft, .

## <a name="azure-ad-only-applications"></a>Apenas aplicações da Azure AD

Esta lista contém todas as suas aplicações registadas para utilização com o ponto final da AD Azure. Estas aplicações apenas têm a capacidade de contratar utilizadores com contas de trabalho/escola a partir do Azure Ative Directory. Esta lista inclui aplicações que foram registadas através da experiência de **registos** da App no [portal Azure](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Aplicações SDK ao vivo

Esta lista contém todas as suas aplicações registadas para utilização exclusivamente com a conta microsoft. Não estão habilitados para utilização com o Azure Ative Directory. É aqui que encontra quaisquer aplicações que tenham sido previamente registadas no portal de desenvolvimento da MSA em `https://account.live.com/developers/applications`. Todas as funções que `https://account.live.com/developers/applications` desempenhou anteriormente podem agora ser executadas em registos de [Aplicações.](https://aka.ms/appregistrations)

## <a name="application-secrets"></a>Segredos de Aplicação

Os segredos da aplicação são credenciais que permitem à sua aplicação realizar [autenticação](https://tools.ietf.org/html/rfc6749#section-2.3) fiável do cliente com a AD Azure. Em OAuth & OpenID Connect, um segredo de `client_secret`aplicação é comumente referido como a . No protocolo v2.0, qualquer aplicação que receba um símbolo de segurança `https` num local web endereçado (utilizando um esquema) deve utilizar um segredo de aplicação para se identificar com a AD Azure após o resgate desse token de segurança. Além disso, qualquer cliente nativo que receba fichas num dispositivo será proibido de usar um segredo de aplicação para realizar a autenticação do cliente. Isto desencoraja o armazenamento de segredos em ambientes inseguros.

Cada aplicação pode conter dois segredos de aplicação válidos a qualquer momento. Ao manter dois segredos, tem a capacidade de realizar a caducidade periódica de chaves em todo o ambiente da sua aplicação. Uma vez migrado a totalidade da sua aplicação para um novo segredo, pode apagar o velho segredo e fornecer um novo.

Neste momento, apenas dois tipos de segredos de aplicação são permitidos no portal de registo da aplicação. Escolher **Gerar Nova Palavra-passe** gera e armazena um segredo partilhado na respetiva loja de dados, que pode utilizar na sua aplicação. A escolha **do Novo Par** de Chaves cria um novo par de chaves público/privado que pode ser descarregado e usado para autenticação de clientes para a AD Azure. Escolher **a chave pública de upload** permite-lhe utilizar o seu próprio par de chaves público/privado.
É obrigado a fazer o upload de um certificado que contenha uma chave pública.

## <a name="profile"></a>Perfil

A secção de perfil do portal de registo da aplicação pode ser usada para personalizar a página de início de sessão para a sua aplicação. Neste momento pode alterar o logótipo da página de login, termos de URL de serviço e URL de declaração de privacidade. O logótipo deve ser uma imagem transparente de 48 x 48 ou 50 x 50 pixels num ficheiro GIF, PNG ou JPEG que seja de 15 KB ou menor. Tente alterar os valores e ver a página de inscrição resultante!

## <a name="live-sdk-support"></a>Suporte SDK ao vivo

Quando ativar o "Live SDK Support", quaisquer segredos de aplicação que criar serão aprovisionados nas lojas de dados Azure AD e Microsoft Account. Isto permite que a sua aplicação se integre diretamente com o serviço da Conta Microsoft (login.live.com). Se pretender construir uma aplicação utilizando diretamente a Conta Microsoft (em oposição à utilização do ponto final v2.0), deverá certificar-se de que o Live SDK Support está ativado.

O suporte de SDK ao vivo garante que o segredo da aplicação só está escrito na loja de dados Azure AD. A loja de dados Azure AD incorpora regulamentos de nível empresarial que lhe permitem cumprir determinados padrões, tais como o cumprimento do FISMA. Se ativar o suporte live SDK, a sua aplicação poderá não conseguir o cumprimento de algumas destas normas.

Se apenas planeia utilizar o ponto final v2.0, pode desativar com segurança o suporte Live SDK.
