---
title: Campos de registo do portal Azure para aplicações desenvolvidas sob medida
description: Orientação para registar uma aplicação desenvolvida sob medida com a Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: 36d74b9926639bb4ec49821a3d73b5d615016394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702679"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>Campos de registo do portal Azure para aplicações desenvolvidas sob medida

Este artigo dá-lhe uma breve descrição de todos os campos disponíveis no formulário de inscrição de candidatura no [portal Azure](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registar uma nova aplicação

-   Para registar uma nova aplicação, navegue para o [portal Azure.](https://portal.azure.com)

-   A partir do painel de navegação esquerdo, clique no **Diretório Ativo Azure.**

-   Escolha **as inscrições da App** e clique em **Adicionar**.

-   Isto abre o formulário de inscrição da candidatura.

## <a name="fields-in-the-application-registration-form"></a>Campos no formulário de inscrição de candidatura

| Campo            | Descrição                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Nome             | O nome da aplicação. Deve ter um mínimo de quatro caracteres.                |
| Tipos de conta suportados| Selecione quais as contas que deseja que a sua aplicação suporte: contas apenas neste diretório organizacional, contas em qualquer diretório organizacional, ou contas em qualquer diretório organizacional e contas pessoais da Microsoft.  |
| Redirecionamento URI (opcional) | Selecione o tipo de aplicação que está a construir, **web** ou cliente público (ambiente de **trabalho & móvel)** e, em seguida, introduza o URI redirecionamento (ou URL de resposta) para a sua aplicação. Para aplicações Web, indique o URL base da sua aplicação. Por exemplo, http://localhost:31544 pode ser o URL de uma aplicação Web em execução no seu computador local. Os utilizadores utilizariam este URL para iniciar sessão numa aplicação de cliente Web. Para aplicações cliente públicas, indique o URI utilizado pelo Azure AD para devolver respostas de token. Insira um valor específico da sua aplicação, como myapp://auth. Para ver exemplos específicos para aplicações web ou aplicações nativas, confira os nossos [quickstarts](https://docs.microsoft.com/azure/active-directory/develop).|

Depois de ter preenchido os campos acima, a aplicação está registada no portal Azure, e é redirecionado para a página de visão geral da aplicação. As páginas de definições no painel esquerdo em **Manage** têm mais campos para personalizar a sua aplicação. As tabelas abaixo descrevem todos os campos. Só veria um subconjunto destes campos, dependendo se criou uma aplicação web ou uma aplicação de cliente público.

### <a name="overview"></a>Descrição geral

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID da aplicação  | Ao registar uma aplicação, a Azure AD atribui à sua aplicação um ID de aplicação. O ID da aplicação pode ser usado para identificar exclusivamente a sua aplicação em pedidos de autenticação à AD Azure, bem como para aceder a recursos como o Graph API.                                                          |
| URI do ID da Aplicação      | Este deve ser um URI único, geralmente do formulário **https://&lt;nome\_&gt;/&lt;\_&gt;** de candidatura de nome de inquilino. Isto é utilizado durante o fluxo de concessão de autorização, como um identificador único para especificar o recurso para o qual o símbolo deve ser emitido. Torna-se também a alegação 'aud' no sinal de acesso emitido. |

### <a name="branding"></a>Imagem corporativa

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Carregar novo logótipo | Pode usá-lo para fazer upload de um logotipo para a sua aplicação. O logótipo deve estar em formato .bmp, .jpg ou .png, e o tamanho do ficheiro deve ser inferior a 100 KB. As dimensões da imagem devem ser 215x215 pixels, com dimensões de imagem central de 94x94 pixels.|
| URL de página inicial   | Este é o URL de entrada especificado durante o registo da candidatura.|

### <a name="authentication"></a>Autenticação

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Logout URL      | Este é o único URL de logout de inscrição. A Azure AD envia um pedido de logout para este URL quando o utilizador limpar a sua sessão com a Azure AD utilizando qualquer outra aplicação registada.|
| Tipos de conta suportados  | Este interruptor especifica se a aplicação pode ser usada por vários inquilinos. Normalmente, isto significa que as organizações externas podem usar a sua aplicação, registando-a no seu inquilino e concedendo acesso aos dados da sua organização.|
| URLs de Redirecionamento      | Os URLs redirecionamento, ou resposta, são os pontos finais onde a Azure AD devolve quaisquer fichas que a sua aplicação solicita. Para aplicações nativas, é aqui que o utilizador é enviado após autorização bem sucedida. A Azure AD verifica se o redirecionamento do URI que a sua aplicação fornece no pedido OAuth 2.0 corresponde a um dos valores registados no portal.|

### <a name="certificates-and-secrets"></a>Certificados e segredos

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Segredos de cliente            | Pode criar segredos de clientes, ou chaves, para aceder programáticamente a APIs web protegidos pela Azure AD sem qualquer interação do utilizador. A partir da página secreta do **novo cliente,** introduza uma descrição chave e a data de validade e poupe para gerar a chave. Certifique-se de guardá-lo em algum lugar seguro, pois não poderá aceder mais tarde.             |

## <a name="next-steps"></a>Passos seguintes

[Gestão de Aplicações com Diretório Ativo Azure](../manage-apps/what-is-application-management.md)
