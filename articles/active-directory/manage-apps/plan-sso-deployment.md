---
title: Planeie uma implantação de um único sign-on do Azure Ative Directory
description: Guia para ajudá-lo a planear, implementar e gerir o SSO na sua organização.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92496fa572c5c1cae4588f82ac61c18de3024045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512832"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planear uma implementação de início de sessão único

Um único sinal (SSO) significa aceder a todas as aplicações e recursos que um utilizador necessita, assinando apenas uma vez utilizando uma única conta de utilizador. Com o SSO, os utilizadores podem aceder a todas as aplicações necessárias sem serem obrigados a autenticar uma segunda vez.

## <a name="benefits-of-sso"></a>Benefícios do SSO

O único sinal de inscrição (SSO) adiciona segurança e comodidade quando os utilizadores assinam aplicações no Azure Ative Directory (Azure AD). 

Muitas organizações dependem de software como aplicações de serviço (SaaS), como Office 365, Box e Salesforce, para a produtividade do utilizador final. Historicamente, o pessoal de TI precisava de criar e atualizar individualmente as contas dos utilizadores em cada aplicação SaaS, e os utilizadores precisavam de se lembrar de uma palavra-passe para cada um.

O Azure Marketplace conta com mais de 3000 aplicações com ligações SSO pré-integradas, facilitando a sua integração no seu inquilino.

## <a name="licensing"></a>Licenciamento

- **Licenciamento Azure AD** - SSO para aplicações SaaS pré-integradas é gratuito. No entanto, o número de objetos no seu diretório e as funcionalidades que pretende implementar podem requerer licenças adicionais. Para obter uma lista completa dos requisitos de licença, consulte [o Preço do Diretório Ativo do Azure](https://azure.microsoft.com/pricing/details/active-directory/).
- **Licenciamento** de candidatura - Você precisará das licenças apropriadas para as suas aplicações SaaS para atender às suas necessidades de negócio. Trabalhar com o proprietário da aplicação para determinar se os utilizadores designados para a aplicação têm as licenças adequadas para as suas funções dentro da aplicação. Se a Azure AD gerir o fornecimento automático com base em funções, as funções atribuídas em Azure AD devem alinhar-se com o número de licenças detidas no âmbito do pedido. O número indevido de licenças detidas na aplicação pode levar a erros durante o fornecimento/atualização de um utilizador.

## <a name="plan-your-sso-team"></a>Planeie a sua equipa SSO

- **Envolver as partes interessadas certas** - Quando os projetos tecnológicos falham, é tipicamente devido a expectativas desajustadas sobre impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se](https://aka.ms/deploymentplans) de que está a envolver as partes interessadas certas e que as partes interessadas compreendam os seus papéis.
- **Plan communications** - A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique-os de forma proativa aos seus utilizadores sobre como a sua experiência irá mudar, quando irá mudar e como obter suporte se experimentarem problemas. Reveja as opções de [como os utilizadores finais acederão às suas aplicações ativadas por SSO](end-user-experiences.md)e ecraft as suas comunicações correspondam à sua seleção. 

## <a name="plan-your-sso-protocol"></a>Planeie o seu protocolo SSO

Uma implementação SSO baseada em protocolos da federação melhora a segurança, a fiabilidade e as experiências finais dos utilizadores e é mais fácil de implementar. Muitas aplicações são pré-integradas em Azure AD com [guias passo-a-passo disponíveis](../saas-apps/tutorial-list.md). Pode encontrá-los no nosso [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/) Informações detalhadas sobre cada método SSO podem ser encontradas no artigo [Inscrição única para aplicações no Diretório Ativo Azure](what-is-single-sign-on.md).

Existem duas formas primárias de permitir que os seus utilizadores possam iniciar um único sessão nas suas apps:

- **Com inscrição única federada** A Azure AD autentica o utilizador na aplicação utilizando a sua conta Azure AD. Este método é suportado para aplicações que suportam protocolos como SAML 2.0, WS-Federation ou OpenID Connect, e é o modo mais rico de inscrição única. Recomendamos a utilização de SSO Federado com AD Azure quando uma aplicação o suporta, em vez de SSO e ADFS baseados em palavra-passe.

- Com os utilizadores **de entrada única baseados em palavra-passe** a iniciarem o início com um nome de utilizador e senha da primeira vez, acedem-lhe. Após o primeiro início de inscrição, a Azure AD fornece o nome de utilizador e a palavra-passe para a aplicação. O único sign-on baseado em palavra-passe permite o armazenamento e repetição de palavras-passe de aplicação segura usando uma extensão do navegador web ou uma aplicação móvel. Esta opção aproveita o processo de entrada existente fornecido pela aplicação, permite a um administrador gerir as palavras-passe e não requer que o utilizador conheça a palavra-passe.

### <a name="considerations-for-federation-based-sso"></a>Considerações para sSO baseado na federação

- **Utilizando o OpenID Connect e o OAuth** - Se a aplicação que está a ligar a suporte, utilize o método OIDC/OAuth 2.0 para ativar o seu SSO a essa aplicação. Este método requer menos configuração e permite uma experiência de utilizador mais rica. Para mais informações, consulte [OAuth 2.0,](../develop/v2-oauth2-auth-code-flow.md) [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)e [guia de programador do Azure Ative Directory.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)
- **Configurações de ponto final para SSO baseado em SAML** - Se utilizar o SAML, os seus desenvolvedores precisarão de informações específicas antes de configurar a aplicação. Para mais informações, consulte [Editar a Configuração Básica sAML](configure-single-sign-on-non-gallery-applications.md).
- **Gestão de certificados para SSO baseado em SAML** - Quando ativa o SSO Federado para a sua aplicação, a Azure AD cria um certificado que é por padrão válido por três anos. Pode personalizar a data de validade desse certificado, se necessário. Certifique-se de que tem processos em vigor para renovar os certificados antes da sua expiração. Para saber mais, consulte os Certificados de [Gestão da Azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)

### <a name="considerations-for-password-based-sso"></a>Considerações para SSO baseado em palavra-passe

A utilização de AD Azure para o SSO baseado em palavras-passe requer a implementação de uma extensão de navegador que recupere as credenciais de forma segura e preencha os formulários de login. Defina um mecanismo para implementar a extensão em escala com [navegadores suportados.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) As opções incluem:

- [Política de grupo para o Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Gestor de configuração para Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Download e configuração orientados pelo utilizador para Chrome, Firefox, Microsoft Edge ou IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Para saber mais, consulte Como configurar o sinal único da [palavra-passe .](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Capturar login forma metadados para aplicações que não estão na galeria

A Microsoft suporta a captura de metadados numa aplicação web para abóbada de palavras-passe (capturando o nome de utilizador e os campos de palavra-passe). Navegue para o URL de login durante o processo de configuração da aplicação para capturar os formulários metadados. Peça ao proprietário da aplicação o URL de login exato. Estas informações são utilizadas durante o processo de início de sessão, mapeando credenciais azure ad para a aplicação durante o início de sessão.

Para saber mais, veja [O que é o acesso à aplicação e SSO com AD Azure?](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Indicações de que os metadados em formulários precisam de ser recapturados

Quando as aplicações alteram o seu layout HTML, poderá ser necessário recapturar os metadados para se ajustar às alterações. Os sintomas comuns que indicam que o layout HTML tem alterações incluem:

- Os utilizadores que relatam que clicar na aplicação fica "preso" na página de login
- Utilizadores que relatam que o nome de utilizador ou palavra-passe não é povoado

#### <a name="shared-accounts"></a>Contas partilhadas

Do ponto de vista do sign-in, as aplicações com contas partilhadas não são diferentes de uma aplicação de galeria que utiliza sSO de palavra-passe para utilizadores individuais. No entanto, existem alguns passos adicionais necessários para planear e configurar uma aplicação destinada a utilizar contas partilhadas:

1. Trabalhar com utilizadores de empresas de aplicações para documentar o seguinte:
   1. Conjunto de utilizadores na organização que usarão a aplicação
   1. Conjunto de credenciais existente na aplicação associada ao conjunto de utilizadores 
1. Para cada combinação de conjunto de utilizadores e credenciais, crie um grupo de segurança na nuvem ou no local com base nos seus requisitos.
1. Redefinir as credenciais partilhadas. Uma vez que a aplicação é implementada em Azure AD, os indivíduos não precisam da palavra-passe da conta partilhada. Uma vez que a Azure AD irá armazenar a senha, considere defini-la muito longa e complexa. 
1. Configure o capotamento automático da palavra-passe se a aplicação a suportar. Assim, nem mesmo o administrador que fez a configuração inicial saberá a palavra-passe da conta partilhada. 

## <a name="plan-your-authentication-method"></a>Planeie o seu método de autenticação

Escolher o método de autenticação correto é uma primeira decisão crucial na criação de uma solução de identidade híbrida Azure AD. Implementar o método de autenticação configurado utilizando o Azure AD Connect, que também disponibiliza utilizadores na nuvem.

Para escolher um método de autenticação, é necessário considerar o tempo, a infraestrutura existente, a complexidade e o custo de implementação da sua escolha. Estes fatores são diferentes para cada organização e podem mudar com o tempo. Deve escolher o que mais se compara ao seu cenário específico. Para mais informações, consulte [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn).

## <a name="plan-your-security-and-governance"></a>Planeie a sua segurança e governação 

A identidade é o novo pivô principal para a atenção e investimentos de segurança, uma vez que os perímetros de rede se tornaram cada vez mais porosos e menos eficazes com a explosão de dispositivos BYOD e aplicações na nuvem. 

### <a name="plan-access-reviews"></a>Revisões de acesso ao plano

[As Análises](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) de Acesso permitem às organizações gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de papéis. Deve planear rever regularmente o acesso dos utilizadores para garantir que apenas as pessoas certas tenham acesso continuado.

Alguns dos tópicos-chave a planear durante a criação de avaliações de acesso incluem:

1. Identificar uma cadência para avaliações de acesso que se adequaàs necessidades do seu negócio. Isto pode ser tão frequente como uma vez por semana, mensalmente, anualmente, ou como um exercício a pedido.

1. Crie grupos que representem os revisores dos relatórios de acesso à aplicação. Você precisa garantir que as partes interessadas mais familiarizadas com a app e seus utilizadores-alvo e casos de uso são participantes nas suas avaliações de acesso

1. A conclusão de uma revisão de acesso inclui a retirada de permissões de acesso a apps a utilizadores que já não precisam de acesso. Plano para lidar com potenciais pedidos de suporte de utilizadores negados. Um utilizador eliminado permanecerá eliminado em Azure AD durante 30 dias durante os quais pode ser restaurado por um administrador, se necessário. Após 30 dias, esse utilizador é eliminado permanentemente. Utilizando o portal Azure Ative Directory, um Administrador Global pode eliminar explicitamente um utilizador recentemente eliminado antes de esse período de tempo ser atingido.

### <a name="plan-auditing"></a>Auditoria de planos

A Azure AD fornece [relatórios que contêm informações técnicas e empresariais.](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/) 

Os relatórios de segurança e de atividade estão disponíveis. Os relatórios de segurança mostram que os utilizadores sinalizados para o risco, e os relatórios de atividade arriscados. Pode usar relatórios para gerir o risco, aumentar a produtividade e monitorizar a conformidade.

| Tipo de relatório | Revisão de acesso | Relatórios de segurança | Relatório de inscrição |
|-------------|---------------|------------------|----------------|
| Usar para rever | Permissões de aplicação e utilização. | Contas potencialmente comprometidas | Quem está a aceder às aplicações |
| Ações potenciais | Acesso à auditoria; revogar permissões | Revogar o acesso; reset de segurança de força | Revogar o acesso |

A Azure AD retém a maioria dos dados de auditoria durante 30 dias e disponibiliza os dados através do portal de administração Azure ou através de uma API para que possa descarregar nos seus sistemas de análise.

### <a name="consider-using-microsoft-cloud-application-security"></a>Considere usar a Segurança da Aplicação Microsoft Cloud

Microsoft Cloud App Security (MCAS) é uma solução de Corretor de Segurança de Acesso à Nuvem (CASB). Dá-lhe visibilidade nas suas aplicações e serviços na nuvem, fornece análises sofisticadas para identificar e combater ameaças cibernéticas, e permite controlar como os seus dados viajam.

A implementação do MCAS permite:

- Use o Cloud Discovery para mapear e identificar o seu ambiente em nuvem e as aplicações em nuvem que a sua organização está a usar.
- Aplicações sancionadas e sancionadas na sua nuvem
- Utilize conectores de aplicações fáceis de implementar que tirem partido das APIs do fornecedor, para visibilidade e governação de apps a que se conecta
- Utilize a proteção de controlo de aplicações de acesso condicional para obter visibilidade e controlo em tempo real sobre acesso e atividades dentro das suas aplicações na nuvem
- Ajuda-o a ter um controlo contínuo, definindo e, em seguida, continuamente afinando as políticas.

O controlo de sessão de segurança de aplicações da Microsoft Cloud (MCAS) está disponível para qualquer navegador em qualquer plataforma principal em qualquer sistema operativo. Aplicações móveis e aplicações para desktop também podem ser bloqueadas ou permitidas. Ao integrar-se de forma nativa com o Azure AD, podem ser suportadas quaisquer aplicações que estejam configuradas com aplicações SAML ou Open ID Connect com um único sign-on no Azure AD, incluindo [várias aplicações em destaque.](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

Para obter informações sobre o MCAS, consulte a visão geral da [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). O MCAS é um serviço de subscrição baseado no utilizador. Pode rever os detalhes de licenciamento na ficha de dados de licenciamento do [MCAS](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Utilizar o Acesso Condicional

Com acesso condicional, pode automatizar decisões de controlo de acesso baseadas em critérios para as suas aplicações na nuvem.

As políticas de Acesso Condicional são aplicadas após a autenticação do primeiro fator ter sido concluída. Portanto, o Acesso Condicional não se destina a uma defesa de primeira linha para cenários como ataques de negação de serviço (DoS), mas pode usar sinais destes eventos para determinar o acesso. Por exemplo, o nível de risco de inscrição, a localização do pedido, e assim por diante podem ser utilizados. Para mais informações sobre o Acesso Condicional, consulte [a visão geral](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) e o plano de [implementação.](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)

## <a name="azure-sso-technical-requirements"></a>Requisitos técnicos Azure SSO

A secção seguinte detalha os requisitos para configurar a sua aplicação específica, incluindo o ambiente(s) necessário, pontos finais, mapeamento de reclamações, atributos, certificados e protocolos necessários utilizados. Você precisará desta informação para configurar SSO no [portal Azure AD](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Detalhes do mecanismo de autenticação

Para todas as aplicações SaaS pré-integradas, a Microsoft fornece um tutorial e não precisará desta informação. Se a aplicação não estiver no nosso mercado/galeria de aplicações, poderá ter de recolher as seguintes peças de dados:

- **Fornecedor de identidade atual a aplicação utiliza para SSO se aplicável** - Por exemplo: AD FS, PingFederate, Okta
- **Protocolos suportados pela aplicação-alvo** - Por exemplo, SAML 2.0, OpenID Connect, OAuth, Forms-Based Auth, WS-Fed, WS-Trust
- **Protocolo configurado com Azure AD** - Por exemplo, SAML 2.0 ou 1.1, OpenID Connect, OAuth, Forms-Based, WS-Fed

### <a name="attribute-requirements"></a>Requisitos de atribuição

Há um conjunto pré-configurado de atributos e mapeamentos de atributos entre objetos de utilizador da AD Azure e os objetos de utilizador de cada aplicação SaaS. Algumas aplicações gerem outros tipos de objetos, como grupos. Planeie o mapeamento dos atributos do utilizador a partir do Azure AD para a sua aplicação e [personalize os mapeamentos de atributos padrão](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) de acordo com as suas necessidades de negócio.

### <a name="certificate-requirements"></a>Requisitos de certificados

O certificado para a aplicação deve estar atualizado, ou existe o risco de os utilizadores não poderem aceder à aplicação. A maioria dos certificados de candidatura saaS são bons por 36 meses. Altera a duração do certificado na lâmina de inscrição. Certifique-se de documentar a expiração e saber como irá gerir a renovação do seu certificado. 

Há duas maneiras de gerir os seus certificados. 

- **Rollover automático de certificados** - Microsoft suporta a assinatura de capotamento de [chave em Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Embora este seja o nosso método preferido para gerir certificados, nem todos os suportes do ISV apoiam este cenário.

- **Atualização manual** - Cada aplicação tem o seu próprio certificado que expira com base na forma como é definido. Antes de expirar o certificado do pedido, crie um novo certificado e envie-o para o ISV. Esta informação pode ser retirada dos metadados da federação. [Leia mais sobre metadados da federação aqui.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Implementar SSO

Utilize as seguintes fases para planear e implementar a sua solução na sua organização:

### <a name="user-configuration-for-sso"></a>Configuração do utilizador para SSO

- **Identifique os seus utilizadores de teste**

   Contacte o proprietário da aplicação e peça-lhes que criem um mínimo de três utilizadores de teste dentro da aplicação. Certifique-se de que a informação que utilizará como identificador principal é povoada corretamente e corresponde a um atributo que está disponível em Azure AD. Na maioria dos casos, isto irá mapear para o "NameID" para aplicações baseadas em SAML. Para os tokens jWT, é o "preferred_username".
   
   Crie o utilizador em AD Azure manualmente como um utilizador baseado na nuvem ou sincronize o utilizador a partir das instalações utilizando o motor de sincronização Azure AD Connect. Certifique-se de que as informações correspondem às reclamações enviadas para o pedido.

- **Configure SSO**

   A partir da [lista de aplicações,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)localize e abra o tutorial SSO para a sua aplicação, siga os passos do tutorial para configurar com sucesso a sua aplicação SaaS.

   Se não conseguir localizar a sua candidatura, consulte a documentação da [Aplicação Personalizada.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery) Isto irá acompanhá-lo sobre como adicionar uma aplicação que não está localizada na galeria Azure AD.

   Opcionalmente, pode utilizar reclamações emitidas no token SAML para a aplicação da empresa utilizando a [documentação de orientação da Microsoft](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping). Certifique-se de que este mapa é o que espera receber na resposta SAML para a sua aplicação. Se encontrar problemas durante a configuração, use a nossa orientação sobre [como depurar a integração do SSO.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

Aplicação personalizada no embarque é uma funcionalidade de licenças Azure AD Premium P1 ou P2.

### <a name="provide-sso-change-communications-to-end-users"></a>Fornecer comunicações de alteração SSO aos utilizadores finais

Implemente o seu plano de comunicação. Certifique-se de que está a informar os seus utilizadores finais de que está a chegar uma mudança, quando chegou, o que fazer agora e como procurar assistência.

### <a name="verify-end-user-scenarios-for-sso"></a>Verifique os cenários finais do utilizador para SSO

Pode utilizar os seguintes casos de teste para realizar testes em dispositivos corporativos e pessoais para garantir que as suas configurações SSO estão a funcionar como esperado. Os cenários abaixo assumem que um utilizador está a navegar para um URL de aplicação e a passar por um fluxo de autenticação iniciado pelo prestador de serviços (fluxo auth iniciado por SP).

| Cenário | Resultado esperado no fluxo auth iniciado por SP pelo utilizador |
|----------|---------------------------------------------------|
| Login para se aplicação com IE enquanto está no corpnet. | A autenticação integrada do Windows (IWA) ocorre sem solicitações adicionais. |
| Faça login para se aplicar com IE enquanto está fora do corpnet com nova tentativa de login. | Solicitação baseada em formulários no Servidor AD FS. O utilizador inicia sessão com sucesso e solicitações de navegador para MFA. |
| Login para se apresentar com IE enquanto está fora de corpnet com uma sessão atual e nunca realizou MFA. | O utilizador não recebe o pedido para o primeiro fator. O utilizador recebe o pedido de alerta para o MFA. |
| Login para se apresentar com IE enquanto está fora de corpnet com uma sessão atual e já realizou MFA nesta sessão. | O utilizador não recebe o pedido para o primeiro fator. O utilizador não recebe MFA. SSOs do utilizador na aplicação. |
| Login para aplicação com Chrome/Firefox/Safari enquanto está fora do corpnet com uma sessão atual e já realizou MFA nesta sessão. | O utilizador não recebe o pedido para o primeiro fator. O utilizador não recebe MFA. User SSO's na aplicação. |
| Inicie sessão na aplicação com o Chrome/Firefox/Safari enquanto está fora da corpnet com nova tentativa de login. | Solicitação baseada em formulários no Servidor AD FS. O utilizador inicia sessão com sucesso e solicitações de navegador para MFA. |
| Faça login para aplicação com o Chrome/Firefox enquanto está na rede corporativa com uma sessão atual. | O utilizador não recebe o pedido para o primeiro fator. O utilizador não recebe MFA. User SSO's na aplicação. |
| Inicie sessão para aplicação com aplicação móvel com uma nova tentativa de login. | Solicitação baseada em formulários no Servidor AD FS. O utilizador inicia sessão com sucesso e solicitações de clientes ADAL para MFA. |
| O utilizador não autorizado tenta iniciar sessão na aplicação com URL de login. | Solicitação baseada em formulários no Servidor AD FS. O utilizador não inicia o login com o primeiro fator. |
| O utilizador autorizado tenta iniciar sessão, mas introduz uma senha incorreta. | O utilizador navega para o URL de aplicação e recebe um erro de mau nome de utilizador/palavra-passe. |
| O utilizador autorizado clica no link num e-mail e já está autenticado. | O utilizador clica no URL e é assinado na aplicação sem solicitações adicionais. |
| O utilizador autorizado clica no link num e-mail e ainda não foi autenticado. | O utilizador clica no URL e é solicitado a autenticar com o primeiro fator. |
| O Utilizador Autorizado inicia sessão na aplicação com aplicação móvel (iniciada por SP) com uma nova tentativa de login. | Solicitação baseada em formulários no Servidor AD FS. O utilizador inicia sessão com sucesso e solicitações de clientes ADAL para MFA. |
| O Utilizador não autorizado tenta iniciar sessão na aplicação com URL de login (iniciado por SP). | Solicitação baseada em formulários no Servidor AD FS. O utilizador não inicia o login com o primeiro fator. |
| O utilizador autorizado tenta iniciar sessão, mas introduz uma senha incorreta.| O utilizador navega para o URL de aplicação e recebe um erro de mau nome de utilizador/palavra-passe. |
| O utilizador autorizado inicia o login e inicia o login novamente. | Se o URL de inscrição estiver configurado, o utilizador é registado fora de todos os serviços e solicita-se que se autentime. |
| O utilizador autorizado inicia o login e inicia o login novamente. | Se o URL de saída não estiver configurado, o utilizador será automaticamente registado na utilização do token existente a partir da sessão de navegador AD Azure existente. |
| O utilizador autorizado clica no link num e-mail e já está autenticado. | O utilizador clica no URL e é assinado na aplicação sem solicitações adicionais. |
| O utilizador autorizado clica no link num e-mail e ainda não foi autenticado. | O utilizador clica no URL e é solicitado a autenticar com o primeiro fator. |

## <a name="manage-sso"></a>Gerir SSO

Esta secção descreve os requisitos e recomendações para gerir com sucesso o SSO.

### <a name="required-administrative-roles"></a>Funções administrativas necessárias

Utilize sempre o papel com o menor número de permissões disponíveis para realizar a tarefa necessária dentro do Diretório Ativo Azure. A Microsoft recomenda [rever as diferentes funções disponíveis](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e escolher a certa para resolver as suas necessidades para cada persona para esta aplicação. Algumas funções podem ter de ser aplicadas temporariamente e removidas após a conclusão da implantação.

| Persona| Funções | Função Azure AD (se necessário) |
|--------|-------|-----------------------------|
| Administração de secretária de ajuda | Suporte de nível 1 | Nenhuma |
| Administrador de identidade | Configure e depuraquando problemas impactam Azure AD | Administrador global |
| Administrador de candidatura | Atestado de utilizador na aplicação, configuração em utilizadores com permissões | Nenhuma |
| Administradores de infraestruturas | Cert dono de capotamento | Administrador global |
| Empresário/stakeholder | Atestado de utilizador na aplicação, configuração em utilizadores com permissões | Nenhuma |

Recomendamos a utilização de [Gestão de Identidade Privilegiada](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) para gerir as suas funções para fornecer auditoria, controlo e revisão de acesso adicionais aos utilizadores com permissões de diretório.

### <a name="sso-certificate-lifecycle-management"></a>Gestão do ciclo de vida do certificado SSO

É importante identificar as funções certas e listas de distribuição de e-mail encarregadas de gerir o ciclo de vida do certificado de assinatura entre a Azure AD e a aplicação que está a ser configurada com um único sinal. Estas são algumas das funções-chave que recomendamos ter no lugar:

- Proprietário para atualizar propriedades de utilizador na aplicação
- Proprietário On-Call para suporte de pausa/correção de aplicação
- Lista de distribuição de e-mail monitorizada de perto para notificações de alteração relacionadas com certificados

O tempo máximo de vida de um certificado é de três anos. Recomendamos que estabeleça um processo sobre como lidará com uma alteração de certificado entre a Azure AD e a sua aplicação. Isto pode ajudar a prevenir ou minimizar uma paragem devido à caducidade de um certificado ou à caducidade do certificado de força.

### <a name="rollback-process"></a>Processo de retrocesso

Depois de completar os testes com base nos seus casos de teste, é hora de entrar em produção com a sua aplicação. Mudar-se para a produção significa que implementará as suas configurações planeadas e testadas no seu inquilino de produção e irá lançá-la para os seus utilizadores. No entanto, é importante planear o que fazer no caso da sua implantação não correr como planeado. Se a configuração SSO falhar durante a implementação, deve compreender como mitigar qualquer falha e reduzir o impacto para os seus utilizadores.

A disponibilidade de métodos de autenticação dentro da aplicação determinará a sua melhor estratégia. Certifique-se sempre de que tem documentação detalhada para os proprietários de aplicações sobre como voltar ao estado de configuração de login original, caso a sua implementação se detete tede problemas.

- **Se a sua aplicação suportar vários fornecedores**de identidade , por exemplo LDAP e AD FS e Ping, não elimine a configuração SSO existente durante o lançamento. Em vez disso, desative-o durante a migração, caso precise de o trocar mais tarde. 

- **Se a sua aplicação não suportar vários IDPs,** mas permitir que os utilizadores iniciem sessão utilizando a autenticação baseada em formulários (nome de utilizador/palavra-passe), certifique-se de que os utilizadores podem recorrer a esta abordagem caso a nova configuração SSO falhe.

### <a name="access-management"></a>Gestão de acesso

Recomendamos a escolha de uma abordagem à escala na gestão do acesso aos recursos. As abordagens comuns incluem a utilização de grupos no local, sincronizando através do Azure AD Connect, [criando Grupos Dinâmicos em AD Azure com base nos atributos](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)dos utilizadores, ou criando [grupos de self-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) em Azure AD geridos por um proprietário de recursos.

### <a name="monitor-security"></a>Monitorizar a segurança

Recomendamos a criação de uma cadência regular na qual reveja os diferentes aspetos da segurança da aplicação SaaS e realize quaisquer ações corretivas que sejam necessárias.

### <a name="troubleshooting"></a>Resolução de problemas

Os seguintes links apresentam cenários de resolução de problemas. Pode querer criar um guia específico para a sua equipa de apoio que incorpore estes cenários e os passos para os corrigir.

#### <a name="consent-issues"></a>Problemas de consentimento

- [Erro de consentimento inesperado](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Erro de consentimento do utilizador](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Problemas de início de sessão

- [Problemas de sessão a partir de um portal personalizado](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Problemas ao iniciar sessão no painel de acesso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Erro na página de início de sessão da aplicação](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Sessão de problemas numa aplicação da Microsoft](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Emissões de SSO para candidaturas listadas na Galeria de Aplicações do Azure

- [Problema com senha SSO para aplicações listadas na Galeria de Aplicações Do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Problema com SSO federado para candidaturas listadas na Galeria de Aplicações do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Emissões de SSO para candidaturas NÃO listadas na Galeria de Aplicações do Azure

- [Problema com senha SSO para aplicações NÃO listadas na Galeria de Aplicações do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Problema com SSO federado para candidaturas NÃO listadas na Galeria de Aplicações do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Passos seguintes

[Depuração SSO baseada em SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Mapeamento de reivindicações para Apps via PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Personalizando as reclamações emitidas em token SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Protocolo SAML de início único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Protocolo SAML de inscrição única](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (para utilizadores externos, como parceiros e fornecedores)

[Acesso Condicional Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Proteção de Identidade Azure](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Acesso SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Candidatura SSO Tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
