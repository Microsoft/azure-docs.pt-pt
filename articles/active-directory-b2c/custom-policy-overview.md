---
title: Políticas personalizadas do Diretório Ativo Azure B2C Microsoft Docs
description: Conheça as políticas personalizadas do Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f72aedb010301f9c7b12778432c4f10feb10f7a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246048"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Políticas personalizadas no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

As políticas personalizadas são ficheiros de configuração que definem o comportamento do seu inquilino Azure Ative Directory B2C (Azure AD B2C). Os fluxos de utilizadores são predefinidos no portal Azure AD B2C para as tarefas de identidade mais comuns. As políticas personalizadas podem ser totalmente editadas por um desenvolvedor de identidade para completar muitas tarefas diferentes.

## <a name="comparing-user-flows-and-custom-policies"></a>Comparar fluxos de utilizadores e políticas personalizadas

| | Fluxos do utilizador | Políticas personalizadas |
|-|-------------------|-----------------|
| Utilizadores-alvo | Todos os desenvolvedores de aplicações com ou sem conhecimentos de identidade. | Profissionais de identidade, integradores de sistemas, consultores e equipas de identidade interna. Eles são confortáveis com os fluxos OpenID Connect e compreendem fornecedores de identidade e autenticação baseada em sinistros. |
| Método de configuração | Portal Azure com uma interface de utilizador amigável (UI). | Editar diretamente ficheiros XML e, em seguida, fazer o upload para o portal Azure. |
| Personalização da UI | Personalização completa de UI incluindo HTML, CSS e JavaScript.<br><br>Suporte multilingmístico com cordas personalizadas. | Mesma |
| Personalização de atributos | Atributos padrão e personalizados. | Mesma |
| Token e gestão de sessões | Token personalizado e várias opções de sessão. | Mesma |
| Fornecedores de Identidade | Prestador local ou social predefinido e a maioria dos fornecedores de identidade da OIDC, como a federação com inquilinos do Diretório Ativo Azure. | OIDC, OAUTH e SAML.  A autenticação também é possível através da integração com APIs REST. |
| Tarefas de Identidade | Inscreva-se ou inscreva-se com contas sociais locais ou muitas.<br><br>Reset de senha de autosserviço.<br><br>Edição de perfil.<br><br>Autenticação multi-factor.<br><br>Personalize fichas e sessões.<br><br>Fluxos de fichas de acesso. | Complete as mesmas tarefas que os fluxos do utilizador utilizando fornecedores de identidade personalizados ou utilize âmbitos personalizados.<br><br>Fornecer uma conta de utilizador noutro sistema no momento do registo.<br><br>Envie um e-mail de boas-vindas usando o seu próprio fornecedor de serviços de e-mail.<br><br>Utilize uma loja de utilizadores fora do Azure AD B2C.<br><br>Validar o utilizador forneceu informações com um sistema fidedigno utilizando uma API. |

## <a name="policy-files"></a>Ficheiros de política

Estes três tipos de ficheiros políticos são utilizados:

- **Ficheiro base** - contém a maioria das definições. Recomenda-se que efaça um número mínimo de alterações neste ficheiro para ajudar na resolução de problemas e na manutenção a longo prazo das suas políticas.
- **Ficheiro de extensões** - contém as alterações de configuração únicas para o seu inquilino.
- **Ficheiro do Partido De Fiação (RP)** - O ficheiro focado na tarefa única que é invocado diretamente pela aplicação ou serviço (também, conhecido como Partido De Fiação). Cada tarefa única requer o seu próprio RP e, dependendo dos requisitos de marca, o número pode ser "total de aplicações x número total de casos de uso".

Os fluxos de utilizadores em Azure AD B2C seguem o padrão de três ficheiros acima descrito, mas o desenvolvedor apenas vê o ficheiro RP, enquanto o portal Azure faz alterações no fundo do ficheiro de extensões.

## <a name="custom-policy-core-concepts"></a>Conceitos de núcleo de política personalizada

O serviço de gestão de identidade e acesso ao cliente (CIAM) em Azure inclui:

- Um diretório de utilizador acessível através do Microsoft Graph e que detém dados de utilizadores tanto para contas locais como para contas federadas.
- Acesso ao Quadro de Experiência de **Identidade** que orquestra a confiança entre utilizadores e entidades e passa reclamações entre eles para completar uma tarefa de gestão de identidade ou acesso.
- Um serviço de token de segurança (STS) que emite fichas de identificação, fichas de atualização e fichas de acesso (e afirmações equivalentes de SAML) e os valida para proteger recursos.

O Azure AD B2C interage com fornecedores de identidade, utilizadores, outros sistemas e com o diretório de utilizadores local em sequência para alcançar uma tarefa de identidade. Por exemplo, inscreva-se num utilizador, registe um novo utilizador ou repor uma palavra-passe. O Quadro de Experiência de Identidade e uma política (também chamada de viagem de utilizador ou política-quadro de confiança) estabelece a confiança multipartidária e define explicitamente os atores, as ações, os protocolos e a sequência de passos a concluir.

O Quadro de Experiência de Identidade é uma plataforma Azure totalmente configurável, orientada por políticas e baseada na nuvem que orquestra a confiança entre entidades em formatos de protocolo padrão como OpenID Connect, OAuth, SAML e algumas não padrão, por exemplo REST Trocas de sinistros sistema-sistema baseados em API. O quadro cria experiências fáceis de utilizar e com rótulo branco que suportam HTML e CSS.

Uma política personalizada é representada como um ou vários ficheiros com formatação XML que fazem referência entre si numa cadeia hierárquica. Os elementos XML definem o esquema de sinistros, as transformações de sinistros, as definições de conteúdo, os fornecedores de sinistros, os perfis técnicos e os passos de orquestração de viagens do utilizador, entre outros elementos. Uma política personalizada é acessível como um ou vários ficheiros XML que são executados pelo Quadro de Experiência de Identidade quando invocados por uma parte que confia. Os desenvolvedores que configuram políticas personalizadas devem definir as relações fidedignas em detalhes cuidadosos para incluir pontos finais de metadados, definições exatas de troca de reclamações e configurar segredos, chaves e certificados conforme necessário por cada fornecedor de identidade.

### <a name="inheritance-model"></a>Modelo de herança

Quando uma aplicação chama o ficheiro de política de RP, o Quadro de Experiência de Identidade em Azure AD B2C adiciona todos os elementos do ficheiro base, a partir do ficheiro de extensões, e depois do ficheiro de política rp para montar a política atual em vigor.  Elementos do mesmo tipo e nome no ficheiro RP sobrepor-se-ão aos das extensões e as extensões sobrepõem-se à base.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Introdução às políticas personalizadas](custom-policy-get-started.md)
