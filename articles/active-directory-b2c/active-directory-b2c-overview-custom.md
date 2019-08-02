---
title: Azure Active Directory B2C políticas personalizadas | Microsoft Docs
description: Saiba mais sobre Azure Active Directory B2C políticas personalizadas.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: be6d54886f23b0fa219b1e4b8948b4a4c51f5864
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716831"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Políticas personalizadas são arquivos de configuração que definem o comportamento do seu locatário do Azure Active Directory (Azure AD) B2C. Os fluxos de usuário são predefinidos no portal de Azure AD B2C para as tarefas de identidade mais comuns. As políticas personalizadas podem ser totalmente editadas por um desenvolvedor de identidade para concluir várias tarefas diferentes.

## <a name="comparing-user-flows-and-custom-policies"></a>Comparando fluxos de usuário e políticas personalizadas

| | Fluxos do utilizador | Políticas personalizadas |
|-|-------------------|-----------------|
| Usuários de destino | Todos os desenvolvedores de aplicativos com ou sem conhecimento de identidade. | Profissionais de identidade, integradores de sistemas, consultores e equipes de identidade interna. Eles se sentem confortáveis com os fluxos do OpenID Connect e entendem os provedores de identidade e a autenticação baseada em declarações. |
| Método de configuração | Portal do Azure com uma interface de usuário (IU) amigável. | Editar arquivos XML diretamente e, em seguida, carregar para o portal do Azure. |
| Personalização da interface do usuário | Personalização completa da interface do usuário, incluindo HTML, CSS e JavaScript.<br><br>Suporte multilíngüe com cadeias de caracteres personalizadas. | Idêntica |
| Personalização de atributo | Atributos padrão e personalizados. | Idêntica |
| Gerenciamento de token e sessão | Opções de token personalizado e várias sessões. | Idêntica |
| Fornecedores de Identidade | Provedor local ou social predefinido e provedores de identidade mais OIDC, como Federação com locatários Azure Active Directory. | OIDC, OAUTH e SAML baseados em padrões.  A autenticação também é possível usando a integração com APIs REST. |
| Tarefas de identidade | Inscreva-se ou entre com contas sociais locais ou diversas.<br><br>Redefinição de senha de autoatendimento.<br><br>Edição de perfil.<br><br>Autenticação multifator.<br><br>Personalizar tokens e sessões.<br><br>Fluxos de token de acesso. | Conclua as mesmas tarefas que os fluxos de usuário usando provedores de identidade personalizados ou use Escopos personalizados.<br><br>Provisione uma conta de usuário em outro sistema no momento do registro.<br><br>Envie um email de boas-vindas usando seu próprio provedor de serviços de email.<br><br>Use um repositório de usuários fora do Azure AD B2C.<br><br>Validar as informações fornecidas pelo usuário com um sistema confiável usando uma API. |

## <a name="policy-files"></a>Arquivos de política

Esses três tipos de arquivos de política são usados:

- **Arquivo base** -contém a maioria das definições. É recomendável que você faça um número mínimo de alterações nesse arquivo para ajudar na solução de problemas e na manutenção de longo prazo de suas políticas.
- **Arquivo de extensões** – mantém as alterações de configuração exclusivas para seu locatário.
- Arquivo de terceira parte **confiável (RP)** -o único arquivo com foco em tarefa que é invocado diretamente pelo aplicativo ou serviço (também conhecido como uma terceira parte confiável). Cada tarefa exclusiva requer seu próprio RP e, dependendo dos requisitos de identidade visual, o número pode ser "total de aplicativos x número total de casos de uso".

Os fluxos de usuário no Azure AD B2C seguem o padrão de três arquivos descrito acima, mas o desenvolvedor vê apenas o arquivo RP, enquanto o portal do Azure faz alterações em segundo plano no arquivo de extensões.

## <a name="custom-policy-core-concepts"></a>Conceitos básicos da política personalizada

O serviço de gerenciamento de acesso e identidade do cliente (CIAM) no Azure inclui:

- Um diretório de usuário que é acessível usando Microsoft Graph e que contém dados de usuário para contas locais e contas federadas.
- Acesso à **estrutura de experiência de identidade** que orquestra a confiança entre usuários e entidades e passa declarações entre eles para concluir uma tarefa de gerenciamento de identidade ou acesso.
- Um serviço de token de segurança (STS) que emite tokens de ID, tokens de atualização e tokens de acesso (e asserções SAML equivalentes) e os valida para proteger os recursos.

Azure AD B2C interage com provedores de identidade, usuários, outros sistemas e com o diretório de usuário local em sequência para obter uma tarefa de identidade. Por exemplo, conecte-se a um usuário, registre um novo usuário ou redefina uma senha. A estrutura de experiência de identidade e uma política (também chamada de um percurso do usuário ou uma política de estrutura confiável) estabelecem uma relação de confiança de várias partes e definem explicitamente os atores, as ações, os protocolos e a sequência de etapas a serem concluídas.

A estrutura de experiência de identidade é uma plataforma do Azure baseada em nuvem, orientada por políticas e totalmente configurável, que orquestra a confiança entre entidades em formatos de protocolo padrão, como OpenID Connect, OAuth, SAML, WSFed e alguns não padrão, por exemplo, REST As trocas de declarações do sistema para o sistema baseadas em API. A estrutura cria experiências com nome de usuário e em branco que dão suporte a HTML e CSS.

Uma política personalizada é representada como um ou vários ficheiros com formatação XML que fazem referência entre si numa cadeia hierárquica. Os elementos XML definem o esquema de declarações, transformações de declarações, definições de conteúdo, provedores de declarações, perfis técnicos e etapas de orquestração de jornada do usuário, entre outros elementos. Uma política personalizada pode ser acessada como um ou vários arquivos XML que são executados pela estrutura de experiência de identidade quando invocados por uma terceira parte confiável. Os desenvolvedores que configuram políticas personalizadas devem definir as relações confiáveis com detalhes cuidadosos para incluir pontos de extremidade de metadados, definições exatas de troca de declarações e configurar segredos, chaves e certificados conforme a necessidade de cada provedor de identidade.

### <a name="inheritance-model"></a>Modelo de herança

Quando um aplicativo chama o arquivo de política de RP, a estrutura de experiência de identidade no Azure AD B2C adiciona todos os elementos do arquivo base, do arquivo de extensões e, em seguida, do arquivo de política RP para montar a política atual em vigor.  Os elementos do mesmo tipo e nome no arquivo RP substituirão aqueles nas extensões e as extensões serão substituídas pela base.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md)
