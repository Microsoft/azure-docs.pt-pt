---
title: Tópicos de ajuda de Portal de registo de aplicações | Documentos da Microsoft
description: Uma descrição das várias funcionalidades no portal de registo de aplicação do Microsoft.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77b5185056329113ee1fd17fa3ed3f364380ca2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095879"
---
# <a name="app-registration-reference"></a>Referência de registo de aplicação
Este documento fornece o contexto e descrições de várias funcionalidades encontradas no [Portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/).

> [!NOTE]
> Podemos deixar de suportar a registar e gestão de aplicações de AD convergidas e do Azure no [Portal de registo de aplicação](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/) a partir de Maio de 2019. Recomendamos que gerir as suas aplicações existentes e registar novas aplicações utilizando o [registos de aplicações (pré-visualização)](https://aka.ms/appregistrations) experiência no portal do Azure.

## <a name="my-applications-or-converged-applications"></a>Meus aplicativos ou aplicativos de convergência
Esta lista contém todos os seus aplicativos para uso com o ponto de final de v2.0 do Azure AD. Estas aplicações têm a capacidade de iniciar sessão dos utilizadores com contas Microsoft pessoais e contas escolares/do Azure Active Directory. Para saber mais sobre o ponto de final de v2.0 do Azure AD, consulte a [descrição geral de v2.0](active-directory-appmodel-v2-overview.md). Esses aplicativos também podem ser utilizados para integrar com o endpoint de autenticação de conta Microsoft, `https://login.live.com`.

## <a name="azure-ad-only-applications"></a>Aplicações apenas do Azure AD
Esta lista contém todos os seus aplicativos para uso com o ponto de extremidade de versão 1.0 do Azure AD. Esses aplicativos têm apenas a capacidade de iniciar sessão dos utilizadores com contas escolares/do Azure Active Directory. Esta lista inclui os aplicativos que foram registados com o **registos de aplicações** experiência no [Portal do Azure](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Aplicações do Live SDK
Esta lista contém todos os seus aplicativos para uso exclusivamente com conta Microsoft. Não estão ativadas para utilização com o Azure Active Directory. Isso é onde encontrar todas as aplicações que foram anteriormente registadas com o portal de programador da MSA em `https://account.live.com/developers/applications`. Todas as funções que realizou anteriormente no `https://account.live.com/developers/applications` agora podem ser executadas neste novo portal, `https://apps.dev.microsoft.com`.

## <a name="application-secrets"></a>Segredos da Aplicação
Segredos da aplicação são credenciais que permitem que seu aplicativo execute fiável [autenticação de cliente](https://tools.ietf.org/html/rfc6749#section-2.3) com o Azure AD. OAuth e OpenID Connect, um segredo de aplicação é frequentemente referido como um `client_secret`. No protocolo v2.0, qualquer aplicativo que recebe um token de segurança num local acessível na web (usando um `https` esquema) tem de utilizar um segredo de aplicação para identificar a próprio para o Azure AD ao resgate desse token de segurança. Além disso, qualquer cliente nativo que recebe tokens num dispositivo vai ser proibido da utilização de um segredo de aplicação para efetuar a autenticação de cliente. Isso desencoraja o armazenamento dos segredos em ambientes inseguros.

Cada aplicação pode conter dois segredos da aplicação válido num determinado momento. Ao manter dois segredos, tem a capacidade de efetuar o rollover da chave de periódico em todo o ambiente da sua aplicação. Assim que tiver migrado a totalidade da sua aplicação para um novo segredo, pode eliminar o segredo antigo e aprovisionar um novo.

Neste momento, apenas dois tipos de segredos da aplicação são permitidos no portal de registo de aplicação. Escolher **gerar nova palavra-passe** gera e armazena um segredo partilhado no arquivo de dados correspondentes, que pode utilizar na sua aplicação. Escolher **gerar novo par de chave** cria um novo par de chaves públicas/privadas que pode ser transferido e utilizado para autenticação de cliente para o Azure AD. Escolher **carregar a chave pública** permite-lhe utilizar o seu próprio par de chaves públicas/privadas.
É necessário para carregar um certificado que contém uma chave pública.

## <a name="profile"></a>Perfil
A seção de perfil do portal de registo de aplicação pode ser utilizada para personalizar a página de início de sessão para a sua aplicação. Neste momento é possível alterar o logótipo da aplicação de início de sessão da página, termos de URL do serviço e o URL da declaração de privacidade. O logótipo tem de ser um ficheiro de imagem GIF, PNG ou JPEG transparente com 48 x 48 ou 50 x 50 pixéis. Limite de tamanho: 15 KB. Tente alterar os valores e ver a página de início de sessão resultante!

## <a name="live-sdk-support"></a>Suporte do Live SDK
Quando ativar o "Live SDK suporte", quaisquer segredos da aplicação que cria serão aprovisionados para o Azure AD e armazenamentos de dados de Account Microsoft. Isso permite que seu aplicativo integrar diretamente com o serviço Microsoft Account (login.live.com). Se quiser criar uma aplicação com o Microsoft Account diretamente (em vez de utilizar o ponto de final de v2.0 do Azure AD), certifique-se de que o suporte do SDK do Live está ativado.

Desativar o suporte do Live SDK garante que o segredo de aplicação só é escrito nos dados do Azure AD armazenar. Os dados do Azure AD store incorpora regulamentos de nível empresarial que lhe permitem cumprir determinadas normas, como a conformidade do FISMA. Se ativar o suporte do Live SDK, é possível que seu aplicativo não conseguirem a conformidade com algumas desses padrões.

Se pretender apenas ao utilizar o ponto de final de v2.0 do Azure AD, pode desativar com segurança o suporte do Live SDK.

