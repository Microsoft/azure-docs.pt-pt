---
title: Resiliência através das melhores práticas do desenvolvedor utilizando o Azure AD B2C | Microsoft Docs
description: Resiliência através das melhores práticas do desenvolvedor na Gestão de Identidade e Acesso do Cliente utilizando o Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff7505e7c47b93f32efd9de60463873026247329
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724930"
---
# <a name="resilience-through-developer-best-practices"></a>Resiliência através das melhores práticas do desenvolvedor

Neste artigo, partilhamos algumas aprendizagens que se baseiam na nossa experiência de trabalhar com grandes clientes. Pode considerar estas recomendações na conceção e implementação dos seus serviços.

![Imagem mostra componentes de experiência de desenvolvedor](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>Utilize a Biblioteca de Autenticação microsoft (MSAL)

A [Microsoft Authentication Library (MSAL)](../develop/msal-overview.md) e a [microsoft identity web authentication library for ASP.NET](../develop/reference-v2-libraries.md) simplificar a aquisição, gestão, caching e refrescamento dos tokens que uma aplicação requer. Estas bibliotecas são otimizadas especificamente para suportar a Identidade do Microsoft, incluindo funcionalidades que melhoram a resiliência da aplicação.

Os desenvolvedores devem adotar as mais recentes versões do MSAL e manter-se atualizados. Veja [como aumentar a resiliência da autenticação e autorização](resilience-app-development-overview.md) nas suas aplicações. Sempre que possível, evite implementar a sua própria pilha de autenticação e utilize bibliotecas bem estabelecidas.

## <a name="optimize-directory-reads-and-writes"></a>Otimizar leituras e escritos de diretório

O serviço de diretório Ad B2C da Microsoft Azure suporta milhares de milhões de autenticações por dia. É projetado para uma alta taxa de leituras por segundo. Otimize as suas escritas para minimizar as dependências e aumentar a resiliência.

### <a name="how-to-optimize-directory-reads-and-writes"></a>Como otimizar leituras e escritos de diretórios

- **Evite escrever funções no diretório no momento da inscrição**: Nunca execute uma escrita no s pré-in sem pré-condição (se cláusula) nas suas políticas personalizadas. Um caso de utilização que requer uma escrita num sing-in é [a migração a tempo das palavras-passe do utilizador](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration). Evite qualquer cenário que exija uma escrita em cada snus.

  - [As condições prévias](../../active-directory-b2c/userjourneys.md) numa viagem de utilizador serão assim:

  ``
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  < Precondition/>
  ``
  - Crie resistência a inscrições com impulso [bot, integrando-se com um sistema CAPTCHA](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration).

  - Utilize uma [amostra de teste](../../active-directory-b2c/best-practices.md#testing) de carga para simular a inscrição e o insusição. 

- **Compreender estrangulamento :** O diretório implementa as regras de estrangulamento de nível de aplicação e inquilino. Existem outros limites de tarifas para as operações de Read/GET, Write/POST, Update/PUT e Delete/DELETE e cada operação tem limites diferentes.

  - Uma escrita no momento da sing-in será apresentada por um POST para novos utilizadores ou PUT para os utilizadores existentes.

  - Uma política personalizada que cria ou atualiza um utilizador em cada sôr-in, pode potencialmente atingir um limite de taxa DE COLOCA ou POST de nível de aplicação. Os mesmos limites aplicam-se ao atualizar objetos de diretório através do Azure AD ou do Microsoft Graph. Da mesma forma, examine as leituras para manter o número de leituras em cada sposição ao mínimo.

  - Estimar a carga máxima para prever a taxa de escrita do diretório e evitar estrangulamentos. As estimativas máximas de tráfego devem incluir estimativas para ações como inscrição, inscrição e autenticação de vários fatores (MFA). Certifique-se de testar tanto o sistema Azure AD B2C como a sua aplicação para o tráfego máximo. É possível que o Azure AD B2C consiga lidar com a carga sem estrangulamento, quando as suas aplicações ou serviços a jusante não o fazem.

  - Compreenda e planeie a sua linha temporal de migração. Ao planear migrar os utilizadores para Azure AD B2C usando o Microsoft Graph, considere os limites da aplicação e do inquilino para calcular o tempo necessário para completar a migração dos utilizadores. Se dividir o seu trabalho de criação de utilizador ou script usando duas aplicações, pode utilizar o limite de aplicação por aplicação. Continuaria a ser necessário manter-se abaixo do limiar por inquilino.

  - Compreenda os efeitos do seu trabalho de migração noutras aplicações. Considere o tráfego ao vivo servido por outras aplicações de dependência para se certificar de que não causa estrangulamento ao nível do inquilino e fome de recursos para a sua aplicação ao vivo. Para obter mais informações, consulte a orientação de estrangulamento do [Microsoft Graph](/graph/throttling).
  
## <a name="extend-token-lifetimes"></a>Prolongar as vidas simbólicas

Num caso improvável, quando o serviço de autenticação Azure AD B2C não conseguir completar novas inscrições e inscrições, ainda pode fornecer mitigação aos utilizadores que se inscrevam. Com [a configuração](../../active-directory-b2c/configure-tokens.md), pode permitir que os utilizadores que já se inscreveram continuem a utilizar a aplicação sem qualquer perturbação percebida até que o utilizador assine a saída da aplicação ou os tempos de [sessão](../../active-directory-b2c/session-behavior.md) fora devido à inatividade.

Os seus requisitos de negócio e a experiência desejada pelo utilizador final ditarão a sua frequência de atualização simbólica tanto para aplicações web como de página única (SPAs).

### <a name="how-to-extend-token-lifetimes"></a>Como prolongar as vidas simbólicas

- **Aplicações web**: Para aplicações web em que o token de autenticação é validado no início da sessão, a aplicação depende do cookie da sessão para continuar a alargar a validade da sessão.

  - Permitir que os utilizadores se mantenham inscritos implementando tempos de sessão rolantes que continuarão a renovar as sessões com base na atividade do utilizador. Se houver uma interrupção de emissão de fichas a longo prazo, estes tempos de sessão podem ser aumentados como uma configuração única na aplicação. Mantenha o tempo de vida útil da sessão ao máximo permitido.

- **SPAs**: Um SPA pode depender de fichas de acesso para fazer chamadas para as APIs. Um SPA usa tradicionalmente o fluxo implícito que não resulta num token de atualização. O SPA pode usar um iframe oculto para realizar novos pedidos simbólicos contra o ponto final de autorização se o navegador ainda tiver uma sessão ativa com o Azure AD B2C. Para as SPAs, existem algumas opções disponíveis para permitir que o utilizador continue a utilizar a aplicação.

  - Aumente a duração de validade do token de acesso para satisfazer os requisitos do seu negócio.

  - Construa a sua aplicação para utilizar um gateway API como representante de autenticação. Nesta configuração, o SPA carrega sem qualquer autenticação e as chamadas API são feitas para o gateway da API. O gateway da API envia o utilizador através de um processo de inscrição utilizando uma bolsa de código de [autorização](https://oauth.net/2/grant-types/authorization-code/) baseada numa apólice e autentica o utilizador. Posteriormente, a sessão de autenticação entre o gateway da API e o cliente é mantida utilizando um cookie de autenticação. As APIs são servidas a partir do gateway da API utilizando o token que é obtido pelo gateway da API ou qualquer outro método de autenticação direta, como certificados, credenciais de cliente ou chaves API.

  - [Migrar o seu SPA de concessão implícita](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/) para [o fluxo de concessão](../../active-directory-b2c/implicit-flow-single-page-application.md) de código de autorização com chave de troca de códigos (PKCE) e suporte de partilha de recursos de origem cruzada (CORS). Migrar a sua aplicação de MSAL.js 1.x para MSAL.js 2.x para perceber a resiliência das aplicações web.

  - Para aplicações móveis, é recomendado prolongar tanto a atualização como o acesso a vidas simbólicas.

- **Aplicações de backend ou microserviços**: Como as aplicações backend (daemon) não são interativas e não estão num contexto de utilizador, a perspetiva de roubo de token é muito reduzida. A recomendação é encontrar um equilíbrio entre segurança e vida e definir uma longa vida útil.

## <a name="configure-single-sign-on"></a>Configurar um único sinal

Com [o single sign-on (SSO)](../manage-apps/what-is-single-sign-on.md), os utilizadores insinuam-se uma vez com uma única conta e têm acesso a várias aplicações. A aplicação pode ser uma aplicação web, móvel ou uma aplicação de página única (SPA), independentemente do nome de plataforma ou domínio. Quando o utilizador inicialmente assina uma aplicação, o Azure AD B2C persiste numa [sessão baseada em cookies](../../active-directory-b2c/session-behavior.md).

Após pedidos de autenticação subsequentes, o Azure AD B2C lê e valida a sessão baseada em cookies e emite um token de acesso sem levar o utilizador a iniciar novamente a sessão. Se o SSO estiver configurado com um âmbito limitado numa apólice ou numa aplicação, o acesso posterior a outras políticas e aplicações exigirá uma autenticação nova.

### <a name="how-to-configure-sso"></a>Como configurar o SSO

[Configure o SSO](../hybrid/how-to-connect-sso-quick-start.md) para ser inquilino (padrão) para permitir que várias aplicações e fluxos de utilizador no seu inquilino partilhem a mesma sessão de utilizador. A configuração em todo o inquilino proporciona a maior resiliência à autenticação fresca.  

## <a name="safe-deployment-practices"></a>Práticas de implementação segura

Os desreguladores de serviço mais comuns são as alterações de código e configuração. A adoção de processos e ferramentas de Integração Contínua e Entrega Contínua (CICD) ajudam na rápida implantação em larga escala e reduz os erros humanos durante os testes e a implantação na produção. Adotar o CICD para redução de erros, eficiência e consistência. [Azure Pipelines](/azure/devops/pipelines/apps/cd/azure/cicd-data-overview) é um exemplo de CICD.

## <a name="web-application-firewall"></a>Firewall de aplicações Web

Proteja as suas aplicações contra vulnerabilidades conhecidas, tais como ataques de Negação de Serviço Distribuído (DDoS), injeções DE SQL, scripts de cross-site, execução remota de código, e muitos outros como documentado no [OWASP Top 10](https://owasp.org/www-project-top-ten/). A implementação de uma Firewall de Aplicação Web (WAF) pode defender-se contra explorações e vulnerabilidades comuns.

- Utilize a Azure [WAF,](../../web-application-firewall/overview.md)que fornece proteção centralizada contra ataques.

- Utilize WAF com Proteção de Identidade AD [Azure e Acesso Condicional para fornecer proteção em várias camadas](../../active-directory-b2c/conditional-access-identity-protection-overview.md) quando utilizar O Azure AD B2C.  

## <a name="secrets-rotation"></a>Rotação de segredos

Azure AD B2C usa segredos para aplicações, APIs, políticas e encriptação. Os segredos asseguram a autenticação, as interações externas e o armazenamento. O Instituto Nacional de Normalização e Tecnologia (NIST) chama ao período de tempo durante o qual uma chave específica é autorizada a ser usada por entidades legítimas como criptoperídio. Escolha o comprimento certo do [criptoperídio](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final) para atender às necessidades do seu negócio. Os desenvolvedores precisam definir manualmente a expiração e girar segredos bem antes da sua expiração.

### <a name="how-to-implement-secret-rotation"></a>Como implementar a rotação secreta

- Utilize [identidades geridas](../managed-identities-azure-resources/overview.md) para recursos suportados para autenticar qualquer serviço que suporte a autenticação AD Azure. Quando utiliza identidades geridas, pode gerir automaticamente os recursos, incluindo a rotação de credenciais.

- Faça um inventário de todas as [chaves e certificados configurados](../../active-directory-b2c/policy-keys-overview.md) em Azure AD B2C. Esta lista é suscetível de incluir chaves usadas em políticas personalizadas, [APIs,](../../active-directory-b2c/secure-rest-api.md)sinal de ID e certificados para SAML.

- Utilizando o CICD, rode segredos que estão prestes a expirar dentro de dois meses da temporada de pico prevista. O criptoperídio máximo recomendado de chaves privadas associadas a um certificado é de um ano.

- Monitorize e rode proativamente as credenciais de acesso da API, tais como palavras-passe e certificados.

## <a name="test-rest-apis"></a>APIs de repouso de teste

No contexto da resiliência, os testes de APIs REST precisam de incluir a verificação de – códigos HTTP, carga útil de resposta, cabeçalhos e desempenho. Os testes não devem incluir apenas testes de percurso feliz, mas também verificar se a API lida graciosamente com cenários de problemas.

### <a name="how-to-test-apis"></a>Como testar APIs

Recomendamos que o seu plano de teste inclua [testes completos de API.](../../active-directory-b2c/best-practices.md#testing) Se está a planear um próximo aumento devido à promoção ou tráfego de férias, tem de rever os seus testes de carga com as novas estimativas. Realize testes de carga das suas APIs e da Rede de Entrega de Conteúdos (CDN) num ambiente de desenvolvimento e não em produção.

## <a name="next-steps"></a>Passos seguintes

- [Recursos de resiliência para desenvolvedores Azure AD B2C](resilience-b2c.md)
  - [Experiência resiliente do utilizador final](resilient-end-user-experience.md)
  - [Interfaces resilientes com processos externos](resilient-external-processes.md)
  - [Resiliência através da monitorização e análise](resilience-with-monitoring-alerting.md)
- [Construa resiliência na sua infraestrutura de autenticação](resilience-in-infrastructure.md)
- [Aumentar a resiliência da autenticação e autorização nos seus pedidos](resilience-app-development-overview.md)