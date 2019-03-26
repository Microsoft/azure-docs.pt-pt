---
title: Políticas personalizadas do Active Directory B2C do Azure | Documentos da Microsoft
description: Saiba mais sobre as políticas personalizadas do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7921454cc9269278db58fcc50bc63ca49b41b1e0
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417938"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

As políticas personalizadas são ficheiros de configuração que definem o comportamento do seu inquilino do Azure Active Directory (Azure AD) B2C. Fluxos de utilizador estão predefinidos no portal do Azure AD B2C para as tarefas mais comuns de identidade. As políticas personalizadas podem ser totalmente editadas por um programador de identidades para concluir a muitas tarefas diferentes.

## <a name="comparing-user-flows-and-custom-policies"></a>Comparação entre fluxos de utilizador e as políticas personalizadas

| | Fluxos do utilizador | Políticas personalizadas |
|-|-------------------|-----------------|
| Utilizadores de destino | Todos os desenvolvedores de aplicativos com ou sem conhecimentos de identidade. | Os profissionais de identidade, integradores de sistemas, consultores e equipes internas de identidade. Eles se sente confortáveis OpenIDConnect fluxos e compreender os fornecedores de identidade e autenticação baseada em afirmações. |
| Método de configuração | Portal do Azure com uma simples interface do usuário (IU). | Editar diretamente os arquivos XML e, em seguida, carregar para o portal do Azure. |
| Personalização da interface do Usuário | Personalização da interface do Usuário total incluindo HTML, CSS e JavaScript.<br><br>Suporte a vários idiomas com cadeias de caracteres personalizado. | Mesmo |
| Personalização de atributo | Atributos padrão e personalizados. | Mesmo |
| Gestão de token e de sessão | Token personalizado e várias opções de sessão. | Mesmo |
| Fornecedores de Identidade | Fornecedor predefinido de local ou redes sociais e a maioria dos fornecedores de identidade OIDC, como o federação com inquilinos do Azure Active Directory. | OIDC baseada em normas, OAUTH e SAML.  Autenticação também é possível ao utilizar a integração com as APIs REST. |
| Tarefas de identidade | Inscrição ou início de sessão com o local ou muitas contas de redes sociais.<br><br>Reposição de palavra-passe self-service.<br><br>Edição de perfil.<br><br>Multi-factor Authentication.<br><br>Personalize tokens e sessões.<br><br>Fluxos de token de acesso. | Conclua as mesmas tarefas, como fluxos de utilizador com fornecedores de identidade personalizado ou utilize âmbitos personalizados.<br><br>Aprovisione uma conta de utilizador em outro sistema no momento do registo.<br><br>Envie um e-mail de boas-vindos através de seu próprio fornecedor de serviços de e-mail.<br><br>Utilize um arquivo de utilizador fora do Azure AD B2C.<br><br>Valide o utilizador fornecido informações com um sistema confiável com uma API. |

## <a name="policy-files"></a>Ficheiros de política

Esses três tipos de ficheiros de política são utilizados:

- **Ficheiro de base** -contém a maioria das definições de. Recomenda-se que faça um número mínimo de alterações a este ficheiro para o ajudar com a manutenção de resolução de problemas e a longo prazo das suas políticas.
- **Arquivo de extensões** -contém as alterações de configuração exclusiva para o seu inquilino.
- **Ficheiro de terceiros (RP) da entidade Confiadora** -o ficheiro centrados na tarefa único que é invocado diretamente pela aplicação ou serviço (também conhecido como uma entidade Confiadora). Cada tarefa exclusiva requer seu próprio RP e consoante os requisitos de imagem corporativa, o número pode ser "total de aplicativos x o número total de casos de utilização."

Fluxos de utilizador no Azure AD B2C seguem o padrão de arquivo de três descrito acima, mas o desenvolvedor só vê o ficheiro RP, enquanto o portal do Azure faz alterações em segundo plano para o arquivo de extensões.

## <a name="custom-policy-core-concepts"></a>Conceitos-chave política personalizada

O serviço de gestão (CIAM) de cliente identidades e acessos, no Azure inclui:

- Um diretório de utilizador que está acessível ao utilizar o Microsoft Graph e que contém dados de utilizador para contas locais e contas federadas.
- Acesso para o **arquitetura de experiências de identidade** que orquestra a confiança entre utilizadores e entidades e transmite declarações entre eles para concluir uma tarefa de gestão de identidade ou de acesso. 
- Um token serviço de segurança (STS) que emite ID tokens, atualização de tokens e acesso tokens (e declarações de SAML equivalentes) e valida para proteger os recursos.

O Azure AD B2C interage com fornecedores de identidade, os utilizadores, outros sistemas e com o diretório de utilizador local em seqüência para alcançar uma tarefa de identidade. Por exemplo, iniciar sessão de um utilizador, registe-se de um novo utilizador ou repor uma palavra-passe. O Framework de experiência de identidade e uma política (também denominada um percurso do utilizador ou uma política de estrutura de confiança) estabelece a fidedignidade de múltiplos intervenientes e define explicitamente os atores, as ações, os protocolos e a sequência de passos para concluir.

O Framework de experiência de identidade é uma plataforma do Azure totalmente configurável, orientada por diretivas, com base na cloud que orquestra a confiança entre entidades em formatos de protocolo padrão como OpenIDConnect, OAuth, SAML, WSFed e algumas que não padrão, por exemplo o REST API de sistema baseado em sistema-para trocas de afirmações. O framework cria amigáveis com etiqueta white experiências que oferecem suporte a HTML e CSS.

Uma política personalizada é representada como um ou vários ficheiros com formatação XML que fazem referência entre si numa cadeia hierárquica. Os elementos XML definem o esquema de afirmações, transformações de afirmações, definições de conteúdo, fornecedores de afirmações, perfis técnicos e passos de orquestração de percurso do utilizador, entre outros elementos. Uma política personalizada é acessível como um ou vários arquivos XML que são executados pela estrutura de experiência de identidade quando invocado por uma entidade confiadora. Os desenvolvedores configurar as políticas personalizadas tem de definir as relações fidedignas em detalhe cuidado para incluir pontos de extremidade de metadados, afirmações exatas definições do exchange e configurar certificados, chaves e segredos conforme necessário por cada provedor de identidade.

### <a name="inheritance-model"></a>Modelo de herança

Quando um aplicativo chama o arquivo de diretiva da RP, o Framework de experiência de identidade no Azure AD B2C adiciona todos os elementos de ficheiro de base, do arquivo de extensões e, em seguida, do arquivo de diretiva da RP para montar a política atual em vigor.  Elementos do mesmo tipo e nome no ficheiro de RP serão substituem as a extensões e a base de substituições de extensões.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md)
