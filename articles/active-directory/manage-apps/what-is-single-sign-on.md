---
title: O que é Azure single sign-on (SSO)?
description: Saiba como funciona o único sign-on (SSO) com o Azure Ative Directory. Utilize SSO para que os utilizadores não precisem de se lembrar de senhas para cada aplicação. Utilize também o SSO para simplificar a gestão de contas.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: iangithinji
ms.reviewer: arvindh, japere
ms.custom: contperf-fy21q1
ms.openlocfilehash: 65e4b32c7c41eea6a885a09dd7ad772980595a9e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379507"
---
# <a name="what-is-single-sign-on-sso"></a>O que é o Início de Sessão Único (SSO)?

Uma única sindução significa que um utilizador não tem de iniciar sôms em todas as aplicações que utiliza. O utilizador inicia sessão uma vez e essa credencial também é utilizada para outras aplicações.

Se é um utilizador final, provavelmente não se importa muito com detalhes do SSO. Você só quer usar as aplicações que o tornam produtivo sem ter que escrever tanto a sua senha. Pode encontrar as suas aplicações em: https://myapps.microsoft.com .
 
Se você é um administrador, ou profissional de TI, então leia para saber mais sobre SSO e como é implementado em Azure.

## <a name="single-sign-on-basics"></a>Básicos de inscrição única
O sign-on único dá um grande salto em frente na forma como os utilizadores insinuam e usam aplicações. Os sistemas de autenticação baseados em sinais únicos são muitas vezes chamados de "autenticação moderna". A autenticação moderna e o único sign-on enquadram-se numa categoria de computação chamada Gestão de Identidade e Acesso (IAM). Para entender o que torna possível um único sinal, confira este vídeo.

Fundamentos da autenticação: O básico | Diretório Ativo Azure

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>Único s-on com aplicações web
As aplicações web são incrivelmente populares. As aplicações web são hospedadas por várias empresas e disponibilizadas como um serviço. Alguns exemplos populares de aplicações web incluem Microsoft 365, GitHub e Salesforce, e há milhares de outros. As pessoas acedem a aplicações web usando um navegador web no seu computador. Um único sinal permite que as pessoas naveguem entre as várias aplicações web sem terem de assinar várias vezes.

Para saber como funciona o único s-on com aplicações web, confira estes dois vídeos.

Fundamentos de autenticação: Aplicações web | Diretório Ativo Azure

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

Fundamentos de autenticação: Web single sign-on | Diretório Ativo Azure

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>Cloud versus no local hospedado apps
A forma como implementa um único sign-on depende do local onde a aplicação está hospedada. O alojamento é importante devido à forma como o tráfego de rede é encaminhado para aceder à app. Se uma aplicação for hospedada e acedida através da sua rede local, chamada app no local, então não há necessidade de os utilizadores acederem à Internet para usar a app. Se a aplicação estiver hospedada em outro lugar, chamada app hospedada em nuvem, então os utilizadores terão de aceder à Internet para poderem utilizar a app.

> [!TIP]
> As aplicações hospedadas na nuvem também são chamadas de Software como aplicações de serviço (SaaS). 

O único sinal de inscrição para aplicações hospedadas na nuvem é simples. Informe o fornecedor de identidade que está a ser usado para a aplicação. E depois configura a app para confiar no fornecedor de identidade. Para aprender a usar o Azure AD como fornecedor de identidade para uma aplicação, consulte a [Série Quickstart em Gestão de Aplicações.](add-application-portal.md)

> [!TIP]
> Os termos cloud e Internet são frequentemente usados intercambiáveis. A razão para isto tem a ver com os diagramas de rede. É comum denotar grandes redes de computador com uma forma de nuvem num diagrama porque não é viável desenhar todos os componentes. A Internet é a rede mais conhecida e, portanto, é fácil usar os termos intercambiavelmente. No entanto, qualquer rede de computador pode ser cunhada uma nuvem.

Também pode utilizar um único sign-on para aplicações baseadas no local. A tecnologia para fazer com que o SSO aconteça no local chama-se Application Proxy. Para saber mais sobre isso, consulte [as opções de inscrição única](sso-options.md).

## <a name="multiple-identity-providers"></a>Vários fornecedores de identidade
Quando se faz um único contrato para trabalhar entre vários fornecedores de identidade, chama-se federação. Para saber como funciona a federação, confira este vídeo.

Fundamentos da autenticação: Federação | Diretório Ativo Azure

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>Passos seguintes
* [Quickstart Series em Gestão de Aplicações](view-applications-portal.md)
* [Opções de início de sessão único](sso-options.md)
