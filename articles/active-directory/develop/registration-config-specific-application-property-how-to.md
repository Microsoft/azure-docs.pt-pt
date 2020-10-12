---
title: Campos de registo no portal do Azure para aplicações personalizadas
description: Orientação para registar uma aplicação personalizada desenvolvida com Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: 1469fcfa68b10353b78d31ccc758c61bf7746692
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88120699"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>Campos de registo no portal do Azure para aplicações personalizadas

Este artigo dá-lhe uma breve descrição de todos os campos disponíveis no formulário de inscrição no [portal Azure.](https://portal.azure.com)

## <a name="register-a-new-application"></a>Registar uma nova aplicação

-   Para registar uma nova aplicação, navegue para o [portal Azure](https://portal.azure.com).

-   A partir do painel de navegação esquerdo, clique no **Diretório Ativo Azure.**

-   Escolha **as inscrições da App** e clique em **Adicionar.**

-   Isto abre o formulário de inscrição.

## <a name="fields-in-the-application-registration-form"></a>Campos no formulário de inscrição

| Campo            | Descrição                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Nome             | O nome da aplicação. Deve ter um mínimo de quatro caracteres.                |
| Tipos de conta suportados| Selecione quais as contas que gostaria que a sua candidatura suportasse: contas apenas neste diretório organizacional, contas em qualquer diretório organizacional ou contas em qualquer diretório organizacional e contas pessoais da Microsoft.  |
| Redirecionamento URI (opcional) | Selecione o tipo de aplicação que está a construir, **Web** ou **cliente público (mobile & desktop)** e, em seguida, insira o URI de redirecionamento (ou URL de resposta) para a sua aplicação. Para aplicações Web, indique o URL base da sua aplicação. Por exemplo, http://localhost:31544 pode ser o URL de uma aplicação Web em execução no seu computador local. Os utilizadores utilizariam este URL para iniciar sessão numa aplicação de cliente Web. Para aplicações cliente públicas, indique o URI utilizado pelo Azure AD para devolver respostas de token. Introduza um valor específico da sua aplicação, como myapp://auth. Para ver exemplos específicos para aplicações web ou aplicações nativas, confira os [nossos quickstarts](./index.yml).|

Depois de ter preenchido os campos acima, a aplicação está registada no portal Azure e é redirecionada para a página geral da aplicação. As páginas de definições no painel esquerdo em **Manage** têm mais campos para você personalizar a sua aplicação. As tabelas abaixo descrevem todos os campos. Você só veria um subconjunto destes campos, dependendo se você criou uma aplicação web ou uma aplicação de cliente público.

### <a name="overview"></a>Descrição geral

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID da Aplicação  | Quando regista uma candidatura, a Azure AD atribui à sua aplicação um ID de aplicação. O ID da aplicação pode ser usado para identificar exclusivamente a sua aplicação em pedidos de autenticação para AZure AD, bem como para aceder a recursos como a API do Gráfico.                                                          |
| URI do ID da Aplicação      | Este deve ser um URI único, geralmente do formulário **https:// nome de nome do &lt; \_ &gt; / &lt; \_ &gt; inquilino.** Isto é utilizado durante o fluxo de concessão de autorização, como um identificador único para especificar o recurso para o qual o símbolo deve ser emitido. Torna-se também a alegação "aud" no token de acesso emitido. |

### <a name="branding"></a>Imagem corporativa

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Carregar novo logotipo | Pode usar isto para carregar um logotipo para a sua aplicação. O logótipo deve estar em formato .bmp, .jpg ou .png, e o tamanho do ficheiro deve ser inferior a 100 KB. As dimensões da imagem devem ser de 215x215 pixels, com dimensões centrais de imagem de 94x94 pixels.|
| URL de página inicial   | Este é o URL de inscrição especificado durante o registo de inscrição.|

### <a name="authentication"></a>Autenticação

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Logout URL      | Este é o URL de logout de assinatura única. A Azure AD envia um pedido de logout a este URL quando o utilizador esvazia a sessão com a Azure AD utilizando qualquer outra aplicação registada.|
| Tipos de conta suportados  | Este interruptor especifica se a aplicação pode ser usada por vários inquilinos. Normalmente, isto significa que as organizações externas podem usar a sua aplicação registando-a no seu inquilino e concedendo acesso aos dados da sua organização.|
| URLs de Redirecionamento      | O redirecionamento, ou resposta, URLs são os pontos finais onde a Azure AD devolve quaisquer fichas que a sua candidatura solicite. Para aplicações nativas, é aqui que o utilizador é enviado após uma autorização bem sucedida. A Azure AD verifica se o redirecionamento uri que a sua aplicação fornece no pedido OAuth 2.0 corresponde a um dos valores registados no portal.|

### <a name="certificates-and-secrets"></a>Certificados e segredos

| Campo           | Descrição        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Segredos de cliente            | Pode criar segredos de clientes, ou chaves, para aceder programáticamente às APIs da web protegidas pela Azure AD sem qualquer interação do utilizador. A partir da página secreta do **novo cliente,** insira uma descrição chave e a data de validade e guarde para gerar a chave. Certifique-se de guardá-lo em algum lugar seguro, já que não poderá acessá-lo mais tarde.             |

## <a name="next-steps"></a>Passos seguintes

[Gestão de Aplicações com Diretório Ativo Azure](../manage-apps/what-is-application-management.md)