---
title: Azure Ative Directory B2C políticas personalizadas | Microsoft Docs
description: Saiba mais sobre as políticas personalizadas do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2019
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 043cf83d804803e1b5b47d3ac51bbccaa06e4e87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87116417"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Políticas personalizadas no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

As políticas personalizadas são ficheiros de configuração que definem o comportamento do inquilino do Azure Active Directory B2C (AAD B2C). Os fluxos de utilizador estão predefinidos no portal do Azure AD B2C para as tarefas de identidade mais comuns. As políticas personalizadas podem ser totalmente editadas por um programador de identidades para concluir várias tarefas diferentes.

## <a name="comparing-user-flows-and-custom-policies"></a>Comparar fluxos de utilizadores e políticas personalizadas

| Contexto | Fluxos do utilizador | Políticas personalizadas |
|-|-------------------|-----------------|
| Utilizadores-alvo | Todos os desenvolvedores de aplicações com ou sem conhecimento de identidade. | Profissionais de identidade, integradores de sistemas, consultores e equipas de identidade interna. São confortáveis com os fluxos OpenID Connect e compreendem os fornecedores de identidade e a autenticação baseada em sinistros. |
| Método de configuração | Portal Azure com uma interface de utilizador fácil de utilizar (UI). | Editar diretamente ficheiros XML e, em seguida, fazer o upload para o portal Azure. |
| Personalização de UI | Personalização completa da UI, incluindo HTML, CSS e JavaScript.<br><br>Suporte multilanguage com cordas personalizadas. | Mesma |
| Personalização de atributos | Atributos padrão e personalizados. | Mesma |
| Gestão de token e sessão | Token personalizado e várias opções de sessão. | Mesma |
| Fornecedores de Identidade | Predefinido provedor local ou social e a maioria dos fornecedores de identidade OIDC, como federação com inquilinos do Azure Ative Directory. | OIDC, OAUTH e SAML baseados em padrões.  A autenticação também é possível utilizando a integração com AS REST. |
| Tarefas identitárias | Inscreva-se ou inscreva-se com contas locais ou muitas contas sociais.<br><br>Redefinição da palavra-passe de autosserviço.<br><br>Edição de perfil.<br><br>Autenticação multi-factor.<br><br>Personalize fichas e sessões.<br><br>O acesso aos fluxos de fichas. | Complete as mesmas tarefas que os fluxos do utilizador utilizando fornecedores de identidade personalizados ou utilize âmbitos personalizados.<br><br>Disponibilização de uma conta de utilizador noutro sistema no momento do registo.<br><br>Envie um e-mail de boas-vindas usando o seu próprio fornecedor de serviços de e-mail.<br><br>Utilize uma loja de utilizadores fora do Azure AD B2C.<br><br>Validar o utilizador forneceu informações com um sistema fidedigno utilizando uma API. |

## <a name="policy-files"></a>Ficheiros de política

Estes três tipos de ficheiros de política são utilizados:

- **Ficheiro base** - contém a maioria das definições. Recomenda-se que faça um número mínimo de alterações neste ficheiro para ajudar na resolução de problemas e na manutenção a longo prazo das suas políticas.
- **Extensions file** - detém as alterações de configuração únicas para o seu inquilino.
- **Ficheiro Do Partido De Gestão (RP)** - O único ficheiro focado em tarefas que é invocado diretamente pela aplicação ou serviço (também, conhecido como Partido De Gestão). Cada tarefa única requer o seu próprio RP e, dependendo dos requisitos de marca, o número pode ser "total de aplicações x número total de casos de uso".

Os fluxos do utilizador em Azure AD B2C seguem o padrão de ficheiro acima descrito, mas o desenvolvedor apenas vê o ficheiro RP, enquanto o portal Azure faz alterações no fundo do ficheiro de extensões.

Embora existam três tipos de ficheiros de política, não está restrito a apenas três ficheiros. Pode ter vários ficheiros de cada tipo de ficheiro. Por exemplo, se não quiser fazer alterações no seu ficheiro Extensões, pode criar um ficheiro Extensions2 para prolongar ainda mais o ficheiro Extensões.

## <a name="custom-policy-core-concepts"></a>Conceitos de núcleo de política personalizada

O serviço de gestão de identidade e acesso ao cliente (CIAM) em Azure inclui:

- Um diretório de utilizadores que é acessível através da utilização do Microsoft Graph e que contém dados de utilizadores tanto para contas locais como para contas federadas.
- Acesso ao **Quadro de Experiência de Identidade** que orquestra a confiança entre utilizadores e entidades e passa reclamações entre eles para completar uma tarefa de gestão de identidade ou acesso.
- Um serviço de símbolos de segurança (STS) que emite fichas de identificação, tokens de atualização e tokens de acesso (e afirmações SAML equivalentes) e os valida para proteger recursos.

O Azure AD B2C interage com fornecedores de identidade, utilizadores, outros sistemas e com o diretório de utilizadores local em sequência para alcançar uma tarefa de identidade. Por exemplo, iniciar sposição num utilizador, registar um novo utilizador ou redefinir uma palavra-passe. O Quadro de Experiência de Identidade e uma política (também chamada de viagem de utilizador ou política de enquadramento de confiança) estabelece a confiança multipartidária e define explicitamente os atores, as ações, os protocolos e a sequência de passos a concluir.

O Quadro de Experiência de Identidade é uma plataforma Azure totalmente configurável, orientada por políticas e baseada em nuvem que orquestra a confiança entre entidades em formatos de protocolo padrão como OpenID Connect, OAuth, SAML e algumas não-standard, por exemplo, trocas de sistema-sistema baseadas em REST API. A estrutura cria experiências fáceis de utilizar e etiquetadas com rótulo branco que suportam HTML e CSS.

Uma política personalizada é representada como um ou vários ficheiros com formatação XML que fazem referência entre si numa cadeia hierárquica. Os elementos XML definem o esquema de reclamações, as transformações de reclamações, as definições de conteúdo, os fornecedores de sinistros, os perfis técnicos e as etapas de orquestração de jornadas de utilizador, entre outros elementos. Uma política personalizada é acessível como um ou vários ficheiros XML que são executados pelo Quadro de Experiência de Identidade quando invocados por uma parte dependente. Os desenvolvedores que configuram políticas personalizadas devem definir as relações fidedignas com cuidado para incluir pontos finais de metadados, definições exatas de troca de alegações e configurar segredos, chaves e certificados, conforme necessário por cada fornecedor de identidade.

### <a name="inheritance-model"></a>Modelo de herança

Quando uma aplicação chama o ficheiro de política de RP, o Quadro de Experiência de Identidade em Azure AD B2C adiciona todos os elementos do ficheiro base, do ficheiro de extensões e, em seguida, do ficheiro de política rp para montar a política atual em vigor.  Elementos do mesmo tipo e nome no ficheiro RP sobrepõem-se aos que estão nas extensões, e as extensões sobrepõem-se à base.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Introdução às políticas personalizadas](custom-policy-get-started.md)
