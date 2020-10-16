---
title: Planeie uma implantação única de sinalização do Azure Ative Directory
description: Guie para ajudá-lo a planear, implementar e gerir o SSO na sua organização.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 06/10/2020
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 87d455a77096a2ae9339c578f3405c629d79fa76
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90603344"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planear uma implementação de início de sessão único

Um único sinal de sso significa aceder a todas as aplicações e recursos de que um utilizador necessita, assinando apenas uma vez utilizando uma única conta de utilizador. Com o SSO, os utilizadores podem aceder a todas as aplicações necessárias sem serem obrigados a autenticar uma segunda vez.

## <a name="benefits-of-sso"></a>Benefícios do SSO

O sign-on único (SSO) adiciona segurança e comodidade quando os utilizadores assinam as aplicações no Azure Ative Directory (Azure AD). 

Muitas organizações confiam no software como aplicações de serviço (SaaS), como a Microsoft 365, Box e Salesforce, para a produtividade do utilizador final. Historicamente, o pessoal de TI precisava de criar e atualizar individualmente as contas de utilizador em cada aplicação SaaS, e os utilizadores precisavam de se lembrar de uma palavra-passe para cada um.

O Azure Marketplace tem mais de 3000 aplicações com ligações SSO pré-integradas, facilitando a sua integração no seu inquilino.

## <a name="licensing"></a>Licenciamento

- **O licenciamento Azure AD** - SSO para aplicações SaaS pré-integradas é gratuito. No entanto, o número de objetos no seu diretório e as funcionalidades que pretende implementar podem necessitar de licenças adicionais. Para obter uma lista completa dos requisitos de licença, consulte [o Azure Ative Directory Pricing](https://azure.microsoft.com/pricing/details/active-directory/).
- **Licenciamento de pedidos** - Você precisará das licenças apropriadas para seus pedidos SaaS para atender às suas necessidades de negócio. Trabalhe com o titular da aplicação para determinar se os utilizadores afetados à aplicação têm as licenças adequadas para as suas funções dentro da aplicação. Se a Azure AD gerir o provisionamento automático com base em funções, as funções atribuídas no Azure AD devem alinhar-se com o número de licenças detidas dentro da aplicação. O número indevido de licenças detidas no pedido pode resultar em erros durante o provisionamento/atualização de um utilizador.

## <a name="plan-your-sso-team"></a>Planeie a sua equipa SSO

- **Envolver as partes interessadas certas** - Quando os projetos tecnológicos falham, é normalmente devido a expectativas desajustadas sobre impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se de que está a envolver as partes interessadas certas](https://aka.ms/deploymentplans) e que as partes interessadas compreendam as suas funções.
- **Comunicações** de planos - A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunicar proativamente aos seus utilizadores sobre como a sua experiência vai mudar, quando vai mudar, e como ganhar apoio se eles experimentarem problemas. Reveja as opções de [como os utilizadores finais acederão às suas aplicações ativadas pelo SSO](end-user-experiences.md)e crie as suas comunicações para corresponder à sua seleção. 

## <a name="plan-your-sso-protocol"></a>Planeie o seu protocolo SSO

Uma implementação SSO baseada em protocolos da federação melhora as experiências de segurança, fiabilidade e utilizador final e é mais fácil de implementar. Muitas aplicações estão pré-integradas no AZure AD com [guias passo a passo disponíveis.](../saas-apps/tutorial-list.md) Pode encontrá-los no nosso [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/) Informações detalhadas sobre cada método SSO podem ser encontradas no artigo [Único sinal de inscrição para aplicações no Azure Ative Directory](what-is-single-sign-on.md).

Existem duas formas primárias de permitir que os seus utilizadores se inscrevam nas suas apps:

- **Com um único sinal federado** O Azure AD autentica o utilizador na aplicação utilizando a sua conta Azure AD. Este método é suportado para aplicações que suportam protocolos como SAML 2.0, WS-Federation ou OpenID Connect, e é o modo mais rico de um único sign-on. Recomendamos a utilização de SSO Federado com Azure AD quando uma aplicação o suporta, em vez de SSO e ADFS baseados em palavra-passe.

- **Com o único sinal de inscrição baseado na palavra-passe,** os utilizadores acedem à aplicação com um nome de utilizador e senha da primeira vez, acedem-na. Após o primeiro início de sação, o Azure AD fornece o nome de utilizador e a palavra-passe à aplicação. O sign-on único baseado em palavra-passe permite o armazenamento de senha de aplicação segura e a repetição usando uma extensão do navegador web ou uma aplicação móvel. Esta opção aproveita o processo de inscrição existente fornecido pela aplicação, permite que um administrador gere as palavras-passe e não exija que o utilizador saiba a palavra-passe.

### <a name="considerations-for-federation-based-sso"></a>Considerações para o SSO baseado na federação

- **Utilizando o OpenID Connect e o OAuth** - Se a aplicação que está a ligar para a suportar, utilize o método OIDC/OAuth 2.0 para ativar o seu SSO nessa aplicação. Este método requer menos configuração e permite uma experiência de utilizador mais rica. Para mais informações, consulte [o OAuth 2.0,](../develop/v2-oauth2-auth-code-flow.md) [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)e [o guia do programador do Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).
- **Configurações de ponto final para SSO baseado em SAML** - Se utilizar o SAML, os seus desenvolvedores precisarão de informações específicas antes de configurar a aplicação. Para obter mais informações, consulte o [sign-on único baseado em Configure SAML.](configure-saml-single-sign-on.md)
- **Gestão de certificados para SSO baseado em SAML** - Quando ativa sSO federado para a sua aplicação, a Azure AD cria um certificado que é por defeito válido por três anos. Pode personalizar a data de validade desse certificado, se necessário. Certifique-se de que tem processos em vigor para renovar certificados antes da sua expiração. Para saber mais, consulte os [Certificados de Gestão da AD Azure.](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)

### <a name="considerations-for-password-based-sso"></a>Considerações para SSO baseado em palavra-passe

A utilização do Azure AD para SSO baseado em palavras-passe requer a implementação de uma extensão do navegador que irá recuperar as credenciais de forma segura e preencher os formulários de login. Defina um mecanismo para implantar a extensão em escala com [navegadores suportados.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) As opções incluem:

- [Política de Grupo para Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Gestor de configuração para Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Descarregamento e configuração conduzidos pelo utilizador para Chrome, Firefox, Microsoft Edge ou IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Para saber mais, consulte [como configurar a palavra-passe de um único sinal .](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Capturar metadados de login para aplicações que não estão na galeria

A Microsoft suporta a captura de metadados numa aplicação web para abotoagem de palavras-passe (capturando o nome de utilizador e os campos de palavra-passe). Navegue para o URL de login durante o processo de configuração da aplicação para capturar os metadados dos formulários. Peça ao proprietário da aplicação o URL de login exato. Estas informações são utilizadas durante o processo de inscrição, mapeando credenciais Azure AD para a aplicação durante a entrada.

Para saber mais, veja [o que é o acesso à aplicação e SSO com Azure AD?](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Indicações de que os metadados em formulários precisam de ser recapturados

Quando as aplicações alterarem o seu layout HTML, poderá ser necessário recapturar os metadados para se ajustarem às alterações. Os sintomas comuns que indicam que o layout HTML tem mudança incluem:

- Utilizadores que relatam que clicar na aplicação fica "preso" na página de login
- Utilizadores que relatam que o nome de utilizador ou palavra-passe não é povoado

#### <a name="shared-accounts"></a>Contas partilhadas

Do ponto de vista do 'iniciar s-in', as aplicações com contas partilhadas não são diferentes de uma aplicação de galeria que utiliza sSO de palavra-passe para utilizadores individuais. No entanto, são necessários alguns passos adicionais para planear e configurar uma aplicação destinada a utilizar contas partilhadas:

1. Trabalhar com os utilizadores empresariais de aplicações para documentar o seguinte:
   1. Conjunto de utilizadores na organização que vai usar a aplicação
   1. Conjunto de credenciais existente na aplicação associada ao conjunto de utilizadores 
1. Para cada combinação de conjunto de utilizadores e credenciais, crie um grupo de segurança na nuvem ou no local com base nas suas necessidades.
1. Redefinir as credenciais partilhadas. Uma vez que a aplicação é implementada em Azure AD, os indivíduos não precisam da senha da conta partilhada. Uma vez que a Azure AD irá armazenar a senha, considere defini-la muito longa e complexa. 
1. Configure a capotagem automática da palavra-passe se a aplicação a suportar. Desta forma, nem mesmo o administrador que fez a configuração inicial saberá a palavra-passe da conta partilhada. 

## <a name="plan-your-authentication-method"></a>Planeie o seu método de autenticação

Escolher o método de autenticação correto é uma primeira decisão crucial na criação de uma solução de identidade híbrida AZure AD. Implemente o método de autenticação configurado utilizando o Azure AD Connect, que também fornece utilizadores na nuvem.

Para escolher um método de autenticação, é necessário considerar o tempo, infraestrutura existente, complexidade e custo de implementação da sua escolha. Estes fatores são diferentes para cada organização e podem mudar com o tempo. Deve escolher o que mais se aproxima do seu cenário específico. Para mais informações, consulte [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory.](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)

## <a name="plan-your-security-and-governance"></a>Planeie a sua segurança e governação 

A identidade é o novo eixo primário para a atenção e investimentos de segurança porque os perímetros de rede tornaram-se cada vez mais porosos e menos eficazes com a explosão de dispositivos BYOD e aplicações em nuvem. 

### <a name="plan-access-reviews"></a>Planeie revisões de acesso

[Os Comentários de Acesso](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) permitem às organizações gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. Deve planear rever o acesso ao utilizador regularmente para garantir que apenas as pessoas certas têm acesso continuado.

Alguns dos tópicos-chave a planear durante a configuração de avaliações de acesso incluem:

1. Identificar uma cadência para comentários de acesso que se adequam à sua necessidade de negócio. Isto pode ser tão frequente como uma vez por semana, mensalmente, anualmente, ou como um exercício a pedido.

1. Criar grupos que representem os revisores dos relatórios de acesso à aplicação. Você precisará garantir que as partes interessadas mais familiarizadas com a app e seus utilizadores-alvo e casos de uso são participantes nas suas avaliações de acesso

1. O preenchimento de uma análise de acesso inclui a retirada de permissões de acesso a apps para utilizadores que já não precisam de acesso. Plano para lidar com potenciais pedidos de apoio de utilizadores negados. Um utilizador eliminado permanecerá eliminado em Azure AD durante 30 dias durante o qual pode ser restaurado por um administrador, se necessário. Após 30 dias, esse utilizador é eliminado permanentemente. Utilizando o portal Azure Ative Directory, um Administrador Global pode eliminar explicitamente um utilizador recentemente eliminado antes desse período de tempo ser atingido.

### <a name="plan-auditing"></a>Auditoria do plano

A Azure AD fornece [relatórios que contenham informações técnicas e empresariais.](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/) 

Tanto os relatórios de segurança como de atividade estão disponíveis. Relatórios de segurança mostram que os utilizadores sinalizam riscos e insustões de risco. Os relatórios de atividade ajudam-no a compreender o comportamento dos utilizadores na sua organização, detalhando a atividade de login e fornecendo pistas de auditoria de todos os logins. Pode utilizar relatórios para gerir o risco, aumentar a produtividade e monitorizar a conformidade.

| Tipo de relatório | Revisão de acesso | Relatórios de segurança | Relatório de inscrição |
|-------------|---------------|------------------|----------------|
| Usar para rever | Permissões de aplicação e utilização. | Contas potencialmente comprometidas | Quem está a aceder às aplicações |
| Ações potenciais | Acesso à auditoria; revogar permissões | Revogar o acesso; forçar reset de segurança | Revogar o acesso |

O Azure AD retém a maioria dos dados de auditoria durante 30 dias e disponibiliza os dados através do portal de administração Azure ou através de uma API para que possa descarregar nos seus sistemas de análise.

### <a name="consider-using-microsoft-cloud-application-security"></a>Considere usar a segurança da aplicação da Microsoft Cloud

Microsoft Cloud App Security (MCAS) é uma solução de Corretor de Segurança de Acesso à Nuvem (CASB). Dá-lhe visibilidade nas suas aplicações e serviços na nuvem, fornece análises sofisticadas para identificar e combater ameaças cibernéticas, e permite-lhe controlar como os seus dados viajam.

A implementação do MCAS permite-lhe:

- Use o Cloud Discovery para mapear e identificar o seu ambiente em nuvem e as aplicações na nuvem que a sua organização está a usar.
- Aplicações sancionadoras e des sanções na sua nuvem
- Utilize conectores de aplicativos fáceis de implementar que tirem partido das APIs do fornecedor, para visibilidade e governação de aplicações a que se conecta
- Use proteção de Controlo de Aplicativos de Acesso Condicional para obter visibilidade e controlo em tempo real sobre o acesso e atividades dentro das suas apps cloud
- Ajuda-o a ter controlo contínuo definindo e, em seguida, continuamente afinando as políticas.

O controlo da sessão microsoft Cloud Application Security (MCAS) está disponível para qualquer navegador em qualquer plataforma principal em qualquer sistema operativo. Aplicações móveis e aplicações de desktop também podem ser bloqueadas ou permitidas. Ao integrar-se de forma nativa com a Azure AD, quaisquer aplicações configuradas com saml, ou aplicações Open ID Connect com um único sign-on em AZure AD podem ser suportadas, incluindo [várias aplicações em destaque.](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

Para obter informações sobre o MCAS, consulte a [visão geral](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)da Microsoft Cloud App Security . O MCAS é um serviço de subscrição baseado no utilizador. Pode rever os detalhes do licenciamento na folha de dados de licenciamento da [MCAS.](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)

### <a name="use-conditional-access"></a>Utilizar o Acesso Condicional

Com o Acesso Condicional, pode automatizar decisões de controlo de acesso baseadas em critérios para as suas aplicações na nuvem.

As políticas de acesso condicional são aplicadas após a autenticação do primeiro fator ter sido concluída. Portanto, o Acesso Condicional não se destina a ser uma defesa de primeira linha para cenários como ataques de negação de serviço (DoS), mas pode usar sinais destes eventos para determinar o acesso. Por exemplo, o nível de risco de inscrição, a localização do pedido, e assim por diante podem ser utilizados. Para obter mais informações sobre o Acesso Condicional, consulte [a visão geral](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) e o [plano de implantação.](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)

## <a name="azure-sso-technical-requirements"></a>Requisitos técnicos do Azure SSO

A secção seguinte detalha os requisitos para configurar a sua aplicação específica, incluindo o ambiente(s) necessário, pontos finais, mapeamento de reclamações, atributos necessários, certificados e protocolos utilizados. Você precisará desta informação para configurar sSO no [portal AD Azure](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Detalhes do mecanismo de autenticação

Para todas as aplicações SaaS pré-integradas, a Microsoft fornece um tutorial e não precisará desta informação. Se a aplicação não estiver no nosso mercado de aplicações/galeria, poderá ter de recolher os seguintes dados:

- **Fornecedor de identidade atual que a aplicação utiliza para SSO se aplicável** - Por exemplo: AD FS, PingFederate, Okta
- **Protocolos suportados pela aplicação-alvo** - Por exemplo, SAML 2.0, OpenID Connect, OAuth, Forms-Based Auth, WS-Fed, WS-Trust
- **Protocolo configurado com Azure AD** - Por exemplo, SAML 2.0 ou 1.1, OpenID Connect, OAuth, Forms-Based, WS-Fed

### <a name="attribute-requirements"></a>Requisitos de atributo

Há um conjunto pré-configurado de atributos e mapeamentos de atributos entre objetos de utilizador AD Azure e objetos de utilizador de cada aplicação SaaS. Algumas aplicações gerem outros tipos de objetos, como grupos. Planeie o mapeamento dos atributos do utilizador do Azure AD para a sua aplicação e [personalize os mapeamentos de atributos padrão de](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) acordo com as necessidades do seu negócio.

### <a name="certificate-requirements"></a>Requisitos de certificados

O certificado para o pedido deve estar atualizado, ou existe o risco de os utilizadores não poderem aceder à aplicação. A maioria dos certificados de candidatura da SaaS são bons para 36 meses. Altere a duração do certificado na lâmina da aplicação. Certifique-se de documentar a expiração e saber como vai gerir a renovação do certificado. 

Há duas maneiras de gerir os seus certificados. 

- **Capotamento automático do certificado** - A Microsoft suporta [a assinatura da chave de capotamento em Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Embora este seja o nosso método preferido para gerir certificados, nem todos os isv suportam este cenário.

- **Atualização manual** - Cada aplicação tem o seu próprio certificado que expira com base na forma como é definido. Antes que o certificado do pedido expire, crie um novo certificado e envie-o para o ISV. Esta informação pode ser retirada dos metadados da federação. [Leia mais sobre os metadados da federação aqui.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Implementar SSO

Utilize as seguintes fases para planear e implementar a sua solução na sua organização:

### <a name="user-configuration-for-sso"></a>Configuração do utilizador para SSO

- **Identifique os seus utilizadores de teste**

   Contacte o proprietário da aplicação e peça-lhes que criem um mínimo de três utilizadores de teste dentro da aplicação. Certifique-se de que a informação que utilizará como identificador principal é povoada corretamente e corresponde a um atributo disponível no Azure AD. Na maioria dos casos, este mapa será o "NameID" para aplicações baseadas em SAML. Para os tokens JWT, é o "preferred_username".
   
   Crie o utilizador em Azure AD manualmente como utilizador baseado na nuvem ou sincronize o utilizador a partir das instalações utilizando o motor de sincronização Azure AD Connect. Certifique-se de que as informações correspondem às reclamações enviadas para o pedido.

- **Configurar SSO**

   A partir [da lista de aplicações,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)localize e abra o tutorial SSO para a sua aplicação, em seguida, siga os passos do tutorial para configurar com sucesso a sua aplicação SaaS.

   Se não conseguir localizar a sua aplicação, consulte a [documentação da Aplicação Personalizada.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery) Isto irá falar sobre como adicionar uma aplicação que não está localizada na galeria AZure AD.

   Opcionalmente, pode utilizar reclamações emitidas no token SAML para a aplicação da empresa utilizando a [documentação de orientação da Microsoft.](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Certifique-se de que este mapa é o que espera receber na resposta SAML para a sua aplicação. Se encontrar problemas durante a configuração, utilize as nossas orientações sobre [como depurar a integração SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

### <a name="provide-sso-change-communications-to-end-users"></a>Fornecer comunicações de alteração SSO aos utilizadores finais

Implemente o seu plano de comunicação. Certifique-se de que está a informar os seus utilizadores finais de que está a chegar uma mudança, quando chegar, o que fazer agora e como procurar ajuda.

### <a name="verify-end-user-scenarios-for-sso"></a>Verifique os cenários finais do utilizador para SSO

Pode utilizar os seguintes casos de teste para realizar testes em dispositivos pessoais e corporativos para garantir que as suas configurações SSO estão a funcionar como esperado. Os cenários abaixo assumem que um utilizador está a navegar para um URL de aplicação e a passar por um fluxo de autenticação iniciado pelo prestador de serviços (fluxo de auth iniciado pela SP).

| Cenário | Resultado esperado no fluxo de auth iniciado pelo SP pelo utilizador |
|----------|---------------------------------------------------|
| Faça login para aplicação com IE enquanto em corpnet. | A autenticação integrada do Windows (IWA) ocorre sem solicitações adicionais. |
| Faça login para aplicação com IE enquanto desligado da corpnet com nova tentativa de login. | Solicitação baseada em formulários no AD FS Server. O utilizador inicia sessão com sucesso e solicita o mFA. |
| Faça login para aplicação com IE enquanto estiver fora da corpnet com uma sessão atual e nunca realizou MFA. | O utilizador não recebe a solicitação para o primeiro fator. O utilizador recebe o pedido de MFA. |
| Faça login para aplicação com IE enquanto estiver fora da corpnet com uma sessão atual e já realizou MFA nesta sessão. | O utilizador não recebe a solicitação para o primeiro fator. O utilizador não recebe MFA. SSOs do utilizador em aplicação. |
| Faça login para aplicação com Chrome/Firefox/Safari enquanto fora da corpnet com uma sessão atual e já realizou MFA nesta sessão. | O utilizador não recebe a solicitação para o primeiro fator. O utilizador não recebe MFA. SSO do utilizador está na aplicação. |
| Inicie sessão em aplicação com o Chrome/Firefox/Safari enquanto está fora da corpnet com nova tentativa de login. | Solicitação baseada em formulários no AD FS Server. O utilizador inicia sessão com sucesso e solicita o mFA. |
| Faça login para aplicação com Chrome/Firefox enquanto está na rede corporativa com uma sessão atual. | O utilizador não recebe a solicitação para o primeiro fator. O utilizador não recebe MFA. SSO do utilizador está na aplicação. |
| Faça login para aplicação com aplicação móvel com uma nova tentativa de login. | Solicitação baseada em formulários no AD FS Server. O utilizador inicia sessão com sucesso e solicita o cliente ADAL para mFA. |
| O utilizador não autorizado tenta iniciar sessão de aplicação com URL de login. | Solicitação baseada em formulários no AD FS Server. O utilizador não consegue iniciar sessão com o primeiro fator. |
| O utilizador autorizado tenta fazer login, mas introduz uma palavra-passe incorreta. | O utilizador navega para o URL da aplicação e recebe mau nome de utilizador/erro de senha. |
| O utilizador autorizado clica no link num e-mail e já está autenticado. | O utilizador clica em URL e é assinado na aplicação sem solicitações adicionais. |
| O utilizador autorizado clica no link num e-mail e ainda não está autenticado. | O utilizador clica em URL e é solicitado a autenticar com o primeiro fator. |
| O Utilizador autorizado entra em aplicação com aplicação móvel de aplicação (iniciado pela SP) com uma nova tentativa de login. | Solicitação baseada em formulários no AD FS Server. O utilizador inicia sessão com sucesso e solicita o cliente ADAL para mFA. |
| O Utilizador não autorizado tenta iniciar sessão de aplicação com URL de login (iniciado pelo SP). | Solicitação baseada em formulários no AD FS Server. O utilizador não consegue iniciar sessão com o primeiro fator. |
| O utilizador autorizado tenta fazer login, mas introduz uma palavra-passe incorreta.| O utilizador navega para o URL da aplicação e recebe mau nome de utilizador/erro de senha. |
| O utilizador autorizado inicia sessão e, em seguida, inicia sessão novamente. | Se o URL de sessão estiver configurado, o utilizador é registado fora de todos os serviços e solicita-se a autenticação. |
| O utilizador autorizado inicia sessão e, em seguida, inicia sessão novamente. | Se o URL de saída não estiver configurado, o utilizador voltará a iniciar sessão automaticamente utilizando o token existente a partir da sessão de navegador Azure AD existente. |
| O utilizador autorizado clica no link num e-mail e já está autenticado. | O utilizador clica em URL e é assinado na aplicação sem solicitações adicionais. |
| O utilizador autorizado clica no link num e-mail e ainda não está autenticado. | O utilizador clica em URL e é solicitado a autenticar com o primeiro fator. |

## <a name="manage-sso"></a>Gerir sso

Esta secção descreve os requisitos e recomendações para gerir com sucesso o SSO.

### <a name="required-administrative-roles"></a>Funções administrativas necessárias

Utilize sempre a função com o menor número de permissões disponíveis para realizar a tarefa necessária no Diretório Ativo Azure. A Microsoft recomenda [rever as diferentes funções disponíveis](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e escolher a certa para resolver as suas necessidades para cada persona para esta aplicação. Algumas funções podem ter de ser aplicadas temporariamente e removidas após a colocação ter sido concluída.

| Persona| Funções | Papel de AD AZure (se necessário) |
|--------|-------|-----------------------------|
| Administração de mesa de ajuda | Suporte de nível 1 | Nenhum |
| Administrador de identidade | Configurar e depurar quando as questões impactam Azure AD | Administrador global |
| Administrador de candidatura | Atestado de utilizador na aplicação, configuração em utilizadores com permissões | Nenhum |
| Administradores de infraestruturas | Cert proprietário de capotamento | Administrador global |
| Empresário/stakeholder | Atestado de utilizador na aplicação, configuração em utilizadores com permissões | Nenhum |

Recomendamos a [utilização de Gestão de Identidade Privilegiada](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) para gerir as suas funções para fornecer auditoria, controlo e revisão de acesso adicionais para utilizadores com permissões de diretório.

### <a name="sso-certificate-lifecycle-management"></a>Gestão do ciclo de vida do certificado SSO

É importante identificar as funções certas e listas de distribuição de e-mails incumbidas de gerir o ciclo de vida do certificado de assinatura entre a Azure AD e a aplicação que está a ser configurada com um único sign-on. Eis algumas das funções-chave que recomendamos ter no lugar:

- Proprietário para atualizar propriedades do utilizador na aplicação
- Suporte de interrupção/correção de pedidos do proprietário
- Lista de distribuição de email monitorizada de perto para notificações de alteração relacionadas com certificados

O prazo máximo de vida de um certificado é de três anos. Recomendamos estabelecer um processo sobre como lidar com uma mudança de certificado entre a Azure AD e a sua aplicação. Isto pode ajudar a prevenir ou minimizar uma paragem devido a um certificado expirando ou forçar a capotagem do certificado.

### <a name="rollback-process"></a>Processo de reversão

Depois de completar os testes com base nos seus casos de teste, é hora de entrar em produção com a sua aplicação. Mudar-se para a produção significa que irá implementar as suas configurações planeadas e testadas no seu inquilino de produção e lançá-la para os seus utilizadores. No entanto, é importante planear o que fazer caso a sua implantação não corra como planeado. Se a configuração SSO falhar durante a implementação, deve entender como atenuar qualquer falha e reduzir o impacto para os seus utilizadores.

A disponibilidade de métodos de autenticação dentro da aplicação determinará a sua melhor estratégia. Certifique-se sempre de que tem documentação detalhada para os proprietários de aplicações sobre como voltar ao estado original de configuração de login, caso a sua implementação se desateça.

- **Se a sua aplicação suportar vários fornecedores de identidade**, por exemplo LDAP e AD FS e Ping, não elimine a configuração SSO existente durante o lançamento. Em vez disso, desative-o durante a migração, caso precise de o trocar mais tarde. 

- **Se a sua aplicação não suportar vários IDPs,** mas permitir que os utilizadores iniciem sessão utilizando a autenticação baseada em formulários (nome de utilizador/palavra-passe), certifique-se de que os utilizadores podem voltar a esta abordagem caso o novo lançamento da configuração SSO falhe.

### <a name="access-management"></a>Gestão de acesso

Recomendamos a escolha de uma abordagem em escala na gestão do acesso aos recursos. As abordagens comuns incluem a utilização de grupos no local sincronizando através do Azure AD Connect, [criando Grupos Dinâmicos em AZure AD com base nos atributos do utilizador,](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)ou criando [grupos de self-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) em Azure AD geridos por um proprietário de recursos.

### <a name="monitor-security"></a>Monitorizar a segurança

Recomendamos a criação de uma cadência regular na qual reveja os diferentes aspetos da segurança da aplicação SaaS e realize quaisquer ações corretivas que sejam necessárias.

### <a name="troubleshooting"></a>Resolução de problemas

Os seguintes links apresentam cenários de resolução de problemas. Pode querer criar um guia específico para a sua equipa de apoio que incorpore estes cenários e os passos para os corrigir.

#### <a name="consent-issues"></a>Questões de consentimento

- [Erro de consentimento inesperado](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Erro de consentimento do utilizador](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Problemas de início de sessão

- [Problemas de sessão a partir de um portal personalizado](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Problemas ao iniciar sessão em As Minhas Aplicações](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Erro na página de início de sessão da aplicação](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Problema de inscrição numa aplicação da Microsoft](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Emissões de SSO para candidaturas listadas na Galeria de Aplicações Azure

- [Problema com a palavra-passe SSO para aplicações listadas na Galeria de Aplicações Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Problema com SSO federado para candidaturas listadas na Galeria de Aplicações Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Emissões de SSO para candidaturas NÃO listadas na Galeria de Aplicações Azure

- [Problema com a palavra-passe SSO para aplicações NÃO listadas na Galeria de Aplicações Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Problema com SSO federado para candidaturas NÃO listados na Galeria de Aplicações Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Passos seguintes

[Depuração SSO baseada em SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Reclamação de mapeamento para Apps via PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Personalização de reclamações emitidas em ficha SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Protocolo SAML de assinatura única](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Protocolo SAML de Sign-Out único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (para utilizadores externos, como parceiros e fornecedores)

[Acesso Condicional do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Proteção de Identidade Azure](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Acesso SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[SSO Tutorial de Aplicação](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

[Transferência whitepaper - Plano único de implantação de sinal-on](https://aka.ms/SSODeploymentPlan)