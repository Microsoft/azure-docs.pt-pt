---
title: Referência do portal de registo de aplicações Rio Azure
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
ROBOTS: NOINDEX
ms.openlocfilehash: b165cd4abd55026e10aa43eb20faa85b887de194
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065048"
---
# <a name="app-registration-reference"></a>Referência de registo de aplicação

Este documento fornece contexto e descrições de várias [funcionalidades encontradas](https://aka.ms/appregistrations) na experiência de registos da App no portal Azure.

## <a name="my-applications-or-converged-applications"></a>As minhas aplicações ou aplicações convergentes

Esta lista contém todas as suas aplicações registadas para utilização com a plataforma de identidade da Microsoft (v2.0). Estas aplicações têm a capacidade de assinar em utilizadores com contas pessoais da Microsoft e contas de trabalho/escola do Azure Ative Directory. Para saber mais sobre o ponto final da plataforma de identidade, consulte a [visão geral v2.0](./v2-overview.md). Estas aplicações também podem ser utilizadas para integrar-se com o ponto final de autenticação da conta Microsoft, `https://login.live.com` .

## <a name="azure-ad-only-applications"></a>Azure AD apenas aplicações

Esta lista contém todas as suas aplicações registadas para uso com o ponto final Azure AD v1.0. Estas aplicações têm apenas a capacidade de assinar em utilizadores com contas de trabalho/escola da Azure Ative Directory. Esta lista inclui aplicações que foram registadas através da experiência de **registos** da App no [portal Azure.](https://portal.azure.com)

## <a name="live-sdk-applications"></a>Aplicações SDK ao vivo

Esta lista contém todas as suas aplicações registadas para uso exclusivamente na conta da Microsoft. Não estão habilitados a ser utilizados com o Azure Ative Directory. É aqui que encontra quaisquer aplicações que foram previamente registadas no portal de desenvolvimento da MSA em `https://account.live.com/developers/applications` . Todas as funções que já desempenhou `https://account.live.com/developers/applications` podem agora ser executadas nas [inscrições da App.](https://aka.ms/appregistrations)

## <a name="application-secrets"></a>Segredos de Aplicação

Os segredos da aplicação são credenciais que permitem que a sua aplicação realize [uma autenticação](https://tools.ietf.org/html/rfc6749#section-2.3) confiável do cliente com a plataforma de identidade da Microsoft. Em OAuth & OpenID Connect, um segredo de aplicação é comumente referido como `client_secret` um . No protocolo v2.0, qualquer aplicação que receba um token de segurança num local web endereçado (usando um `https` esquema) deve usar um segredo de aplicação para se identificar na plataforma de identidade da Microsoft após o resgate desse sinal de segurança. Além disso, qualquer cliente nativo que receba fichas num dispositivo será proibido de usar um segredo de aplicação para realizar a autenticação do cliente. Isto desencoraja o armazenamento de segredos em ambientes inseguros.

Cada aplicação pode conter dois segredos de aplicação válidos a qualquer momento. Ao manter dois segredos, você tem a capacidade de realizar a rododagem periódica de chave em todo o ambiente da sua aplicação. Uma vez migrado toda a sua candidatura para um novo segredo, pode apagar o velho segredo e providenciar um novo.

Neste momento, apenas dois tipos de segredos de aplicação são permitidos no portal de registo de aplicações. A escolha **de Gerar Nova Palavra-Passe** gera e armazena um segredo partilhado na respetiva loja de dados, que pode utilizar na sua aplicação. A escolha **do Novo Par de Chaves** cria um novo par de chaves público/privado que pode ser descarregado e usado para autenticação de clientes na plataforma de identidade da Microsoft. Escolher **a Chave Pública de Upload** permite-lhe usar o seu próprio par de chaves público/privado.
É-lhe exigido que faça o upload de um certificado que contenha uma chave pública.

## <a name="profile"></a>Perfil

A secção de perfil do portal de registo de aplicações pode ser usada para personalizar a página de inscrição para a sua aplicação. Neste momento pode alterar o logótipo de aplicação da página de início de s nota, termos de URL de serviço e URL de declaração de privacidade. O logótipo deve ser transparente 48 x 48 ou 50 x 50 pixels num ficheiro GIF, PNG ou JPEG que seja de 15 KB ou menor. Tente alterar os valores e ver a página de inscrição resultante!

## <a name="live-sdk-support"></a>Suporte SDK ao vivo

Quando ativar o "Live SDK Support", quaisquer segredos de aplicação que criar serão adcedidos tanto nas lojas de dados AZure AD como na Conta da Microsoft. Isto permite que a sua aplicação se integre diretamente com o serviço de Conta Microsoft (login.live.com). Se desejar construir uma aplicação utilizando a Conta Microsoft diretamente (em vez de utilizar o ponto final v2.0), deve certificar-se de que o Live SDK Support está ativado.

O suporte SDK ao vivo desativar garante que o segredo da aplicação está apenas escrito na loja de dados AZure AD. A loja de dados Azure AD incorpora regulamentos de qualidade empresarial que lhe permitem cumprir determinados padrões, tais como o cumprimento do FISMA. Se ativar o suporte live SDK, a sua aplicação pode não conseguir o cumprimento de algumas destas normas.

Se pretender utilizar o ponto final v2.0, pode desativar com segurança o suporte live SDK.