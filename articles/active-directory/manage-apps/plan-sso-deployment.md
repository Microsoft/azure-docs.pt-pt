---
title: Planear uma Azure Active Directory implementação única do início de sessão
description: Guia para o ajudar a planejar, implantar e gerenciar o SSO em sua organização.
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
ms.openlocfilehash: e5278d504c43688bf064b869982938db52b1b1bf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164942"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planear uma implementação de início de sessão única

Início de sessão único (SSO) significa que aceder a todas as aplicações e recursos um usuário precisa ao iniciar sessão apenas uma vez através de uma conta de utilizador único. Com o SSO, os utilizadores podem aceder aplicativos necessários tudo sem a ser necessária para autenticar uma segunda vez.

## <a name="benefits-of-sso"></a>Benefícios do SSO

Início de sessão único (SSO) adiciona segurança e conveniência, quando os utilizadores iniciam sessão aplicações no Azure Active Directory (Azure AD). 

Muitas organizações contam com software como um aplicativos de serviço (SaaS), como o Office 365, a caixa e o Salesforce, para produtividade dos usuários finais. Historicamente, a equipe de TI precisava individualmente criar e atualizar as contas de utilizador em cada aplicação SaaS e os utilizadores necessários lembrar-se de uma palavra-passe para cada um.

O Azure Marketplace tem mais de 3000 aplicações com ligações pré-integradas de SSO, facilitando a integrá-los no seu inquilino.

## <a name="licensing"></a>Licenciamento

- **Licenciamento do Azure AD** -SSO para aplicações SaaS previamente integradas é gratuito. No entanto, o número de objetos no seu diretório e as funcionalidades que pretende implementar pode requerer licenças adicionais. Para obter uma lista completa dos requisitos de licença, veja [preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
- **Licenciamento da aplicação** -terá das licenças adequadas para as suas aplicações de SaaS atender às necessidades da sua empresa. Trabalhar com o proprietário da aplicação para determinar se os utilizadores atribuídos à aplicação tem as licenças adequadas para as respetivas funções dentro do aplicativo. Se o Azure AD gere o aprovisionamento automático com base nas funções, as funções atribuídas no Azure AD devem estar alinhadas com o número de licenças de propriedade dentro do aplicativo. Número de licenças de propriedade no aplicativo incorrecto pode levar a erros durante o aprovisionamento/atualização de um utilizador.

## <a name="plan-your-sso-team"></a>Planear a sua equipa SSO

- **Interaja com os participantes certos** - quando falham, de projetos de tecnologia é normalmente devido a expetativas sem correspondência em impacto, resultados e responsabilidades. Para evitar essas armadilhas [Certifique-se de que está a como utilizar os participantes certos](https://aka.ms/deploymentplans) e que os participantes compreendem suas funções.
- **Planear as comunicações** -a comunicação é crítica para o sucesso de qualquer serviço novo. Comunicar de forma pró-ativa para os seus utilizadores sobre como a experiência deles será alterado quando ele será alterado e como obter suporte, se se deparar com problemas. Reveja as opções para [como os utilizadores finais irão aceder aos respetivos SSO ativado aplicativos](end-user-experiences.md)e crie as comunicações de acordo com a sua seleção. 

## <a name="plan-your-sso-protocol"></a>Planear o seu protocolo SSO

Uma implementação do SSO com base em protocolos de Federação melhora a segurança, confiabilidade, e experiências de utilizador final e é mais fácil de implementar. Muitos aplicativos são previamente integrados no Azure AD com [passo a passo orienta disponíveis](../saas-apps/tutorial-list.md). Pode encontrá-los no nosso [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Informações detalhadas sobre cada método SSO podem ser encontradas no artigo [início de sessão único para aplicações no Azure Active Directory](what-is-single-sign-on.md).

Existem duas formas principais em que pode permitir que os utilizadores o início de sessão único às suas aplicações:

- **Com início de sessão único federado** do Azure AD autentica o utilizador para a aplicação ao utilizar a respetiva conta do Azure AD. Este método é suportado para aplicações que suporte protocolos, tais como SAML 2.0, WS-Federation e OpenID Connect e é o modo mais sofisticado de início de sessão único. Recomendamos que utilize o SSO Federado com o Azure AD quando um aplicativo oferece suporte a ele, em vez de SSO de palavra-passe e o AD FS.

- **Com baseado em palavra-passe de início de sessão único** os utilizadores iniciam sessão para a aplicação com um nome de utilizador e palavra-passe na primeira vez, acederem ao mesmo. Após o primeiro início de sessão, o Azure AD fornece o nome de utilizador e palavra-passe para a aplicação. Com base em palavra-passe de início de sessão único permite o armazenamento de palavra-passe de aplicação segura e de repetição com uma extensão de browser ou aplicação móvel. Esta opção utiliza o início de sessão no processo existente fornecido pela aplicação, permite que um administrador gerir as palavras-passe e não exige o utilizador saiba a palavra-passe.

### <a name="considerations-for-federation-based-sso"></a>Considerações para SSO baseado em Federação

- **Usando o OpenID Connect e OAuth** - se a aplicação estiver a ligar a suporta, usar o método OIDC/OAuth 2.0 para ativar o SSO para essa aplicação. Este método requer menos configuração e permite uma experiência de usuário mais sofisticada. Para obter mais informações, consulte [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1.0](../develop/v2-protocols-oidc.md), e [guia para programadores do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).
- **Configurações de ponto final para SSO baseado em SAML** -se utilizar SAML, seus desenvolvedores terá informações específicas antes de configurar a aplicação. Para mais informações, veja [configurar opções básicas de SAML](configure-single-sign-on-portal.md).
- **Certificado de gestão para SSO baseado em SAML** - quando ativar o SSO federado para a sua aplicação, o Azure AD cria um certificado que está por predefinição válida para três anos. Pode personalizar a data de expiração para esse certificado se for necessário. Certifique-se de que tem processos no local para renovar certificados antes da sua expiração. Para obter mais informações, consulte [do Azure AD gestão de certificados](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).

### <a name="considerations-for-password-based-sso"></a>Considerações para SSO baseado em palavra-passe

Utilizar o Azure AD para SSO baseado em palavra-passe requer a implementação de uma extensão de browser que irá obter as credenciais de forma segura e preencher os formulários de início de sessão. Definir um mecanismo para implementar a extensão em escala com [browsers suportados](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). As opções incluem:

- [Política de grupo para o Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [O System Center Configuration Manager (SCCM) para o Internet Explorer](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)
- [Utilizador controlado por transferência e configuração para o Chrome, o Firefox, o Microsoft Edge ou o IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Para obter mais informações, consulte [como para configurar a palavra-passe única iniciar sessão](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Captura de metadados de formulários de início de sessão para aplicações que não estão na Galeria

A Microsoft suporta a captura de metadados num aplicativo web para a palavra-passe vaulting (capturando os campos de nome de utilizador e palavra-passe). Navegue para o URL de início de sessão durante o processo de configuração do aplicativo para capturar os metadados de formulários. Peça ao proprietário do aplicativo para o URL de início de sessão exato. Estas informações são utilizadas durante o processo de logon, o mapeamento de credenciais do Azure AD para o aplicativo durante o início de sessão.

Para obter mais informações, consulte [o que é o acesso a aplicações e SSO com o Azure AD? – o SSO baseado em palavra-passe](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Precisam desses metadados em formulários indicações seja recapturado

Quando aplicativos alterar o layout HTML, poderá ter de recapturar os metadados para ajustar as alterações. Sintomas comuns que indicam que o esquema HTML tem alterações incluem:

- Utilizadores de relatórios que clicar no aplicativo obtém ficou "preso" na página de início de sessão
- Utilizadores de relatórios que o nome de utilizador ou palavra-passe não está preenchido

#### <a name="shared-accounts"></a>Contas partilhadas

Da perspectiva do início de sessão, os aplicativos com as contas partilhadas não diferentes de um aplicativo de galeria que usa o SSO de palavra-passe para utilizadores individuais. No entanto, existem algumas etapas adicionais necessárias ao planear e configurar uma aplicação devem utilizar as contas partilhadas:

1. Trabalhar com usuários de negócios do aplicativo para documentar o seguinte:
   1. Conjunto de utilizadores na organização que irá utilizar a aplicação
   1. Conjunto de credenciais na aplicação associada ao conjunto de utilizadores existente 
1. Para cada combinação de conjunto de usuários e as credenciais, crie um grupo de segurança na cloud ou no local com base nos seus requisitos.
1. Repor as credenciais partilhadas. Assim que a aplicação é implementada no Azure AD, indivíduos não tem da palavra-passe de conta partilhada. Uma vez que o Azure AD irá armazenar a palavra-passe, pondere defini-lo para ser muito longos e complexos. 
1. Configure o rollover automático da senha se o aplicativo oferece suporte a ele. Dessa forma, nem mesmo o administrador que fizeram a configuração inicial saberá a palavra-passe de conta partilhada. 

## <a name="plan-your-authentication-method"></a>Planear o seu método de autenticação

Escolher o método de autenticação correta é uma primeira decisão fundamental na configuração de uma solução de identidade híbrida do Azure AD. Implemente o método de autenticação que é configurado através do Azure AD Connect, que também Aprovisiona utilizadores na cloud.

Para escolher um método de autenticação, que é preciso considerar o tempo, a infraestrutura existente, a complexidade e o custo da implementação à sua escolha. Esses fatores são diferentes para cada organização e podem ser alterados ao longo do tempo. Deve escolher aquele que melhor corresponde aos seu cenário específico. Para obter mais informações, consulte [escolha o método de autenticação correta para sua solução de identidade híbrida do Azure Active Directory](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).

## <a name="plan-your-security-and-governance"></a>Planear a segurança e governação 

A identidade é o novo pivô primário para a atenção de segurança e os investimentos porque perímetros de rede têm se tornado cada vez mais porosos e menos em vigor, com a enxurrada de dispositivos BYOD e aplicações na cloud. 

### <a name="plan-access-reviews"></a>Revisões de acesso do plano

[As revisões de acesso](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) permitem que as organizações com eficiência gerir membros do grupo, acesso a aplicações empresariais e atribuições de funções. Deve planear a rever o acesso de utilizador em intervalos regulares para se certificar de que apenas as pessoas certas tenham acesso contínuo.

Alguns dos principais tópicos para planear ao configurar as revisões de acesso incluem:

1. Identificar uma cadência de revisões de acesso que se adeque às necessidades da sua empresa. Isso pode ser tão frequente que uma vez por semana, mensalmente, anualmente ou como um exercício de sob demanda.

1. Crie grupos que representam os revisores dos relatórios de acesso de aplicação. Precisará garantir que os participantes mais familiarizados com a aplicação e os seus utilizadores de destino e casos de utilização sejam participantes nas suas revisões de acesso

1. A concluir uma revisão de acesso inclui a possibilidade de eliminar as permissões de acesso da aplicação aos utilizadores que já não precisam de acesso. Planejar o gerenciamento de pedidos de suporte de potencial de utilizadores sem permissão. Um utilizador eliminado continuará a ser eliminado no Azure AD durante 30 dias, período durante o qual eles podem ser restaurados por um administrador se necessário. Após 30 dias, esse utilizador é eliminado permanentemente. Utilizar o portal do Azure Active Directory, um Administrador Global pode explicitamente eliminar permanentemente um utilizador eliminado recentemente antes desse período de tempo é alcançado.

### <a name="plan-auditing"></a>Plano de auditoria

O Azure AD proporciona [relatórios que contêm informações técnico e comercial](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). 

Segurança e relatórios de atividade estão disponíveis. Os relatórios de segurança mostram os utilizadores sinalizados para risco e inícios de sessão de risco. Relatórios de atividade de ajudar a compreender o comportamento dos utilizadores na sua organização com detalhes sobre a atividade de início de sessão e fornecendo trilhas de auditoria de todos os inícios de sessão. Pode utilizar relatórios para gerir os riscos, aumentar a produtividade e monitorizar a conformidade.

| Tipo de relatório | Revisão de acesso | Relatórios de segurança | Relatório de início de sessão |
|-------------|---------------|------------------|----------------|
| Utilizar para rever | Permissões de aplicação e a utilização. | Contas potencialmente comprometidas | Quem está a aceder as aplicações |
| Ações possíveis | Auditar o acesso; revogar as permissões | Revogar o acesso; forçar a reposição de segurança | Revogar o acesso |

Azure AD mantém a maior parte dos dados de auditoria durante 30 dias e disponibiliza os dados através do portal de administração do Azure ou através de uma API para transferência em seus sistemas de análise.

### <a name="consider-using-microsoft-cloud-application-security"></a>Considere a utilização de segurança de aplicações do Microsoft Cloud

Segurança de aplicação na Cloud da Microsoft (MCAS) é uma solução de Mediador de segurança de acesso de nuvem (CASB). Permite-lhe ver as aplicações na cloud e serviços, fornece uma análise sofisticada para identificar e a combater ameaças virtuais e permite-lhe controlar a forma como os dados circulam.

Implantar o MCAS permite-lhe:

- Utilize a Cloud Discovery para mapear e identificar o seu ambiente na cloud e as aplicações na cloud a que sua organização está a utilizar.
- Aprovação e anular aprovar aplicações na sua cloud
- Utilizar conectores de aplicações fáceis de implementar que tiram partido das APIs de fornecedores, para visibilidade e governação de aplicações que ligam à
- Utilizar a proteção de controlo de aplicação de acesso condicional para obter visibilidade em tempo real e controlem o acesso e atividades dentro de suas aplicações na cloud
- Ajuda-o a ter controlo contínuo, por definição e, em seguida, ao ajustar continuamente, as políticas.

Controlo de sessão de segurança de aplicação na Cloud da Microsoft (MCAS) está disponível para qualquer browser em qualquer plataforma principais em qualquer sistema operativo. Aplicações móveis e aplicações de ambiente de trabalho também podem ser bloqueadas ou permitidas. Ao integrar nativamente com o Azure AD, quaisquer aplicações que estão configuradas com o SAML ou abrir ID Connect as aplicações com início de sessão único no Azure AD podem ser suportado, incluindo [várias aplicações em destaque](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad).

Para obter informações sobre MCAS, consulte a [descrição geral do Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). MCAS é um serviço de subscrição baseada no utilizador. Pode rever os detalhes de licenciamento no [folha de dados de licenciamento MCAS](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Utilizar o acesso condicional

Com acesso condicional, é possível automatizar as decisões de controlo de acesso baseado em critérios para as suas aplicações na cloud.

Políticas de acesso condicional são aplicadas após o primeiro--factor authentication foi concluído. Por conseguinte, acesso condicional não se destina como uma defesa de linha de primeira para cenários, como ataques do denial-of-service (DoS), mas pode utilizar os sinais desses eventos para determinar o acesso. Por exemplo, o nível de risco de início de sessão, pode ser utilizada a localização do pedido e assim por diante. Para obter mais informações sobre o acesso condicional, consulte [a descrição geral](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) e o [plano de implantação](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access).

## <a name="azure-sso-technical-requirements"></a>Requisitos técnicos do SSO do Azure

A secção seguinte fornece detalhes sobre os requisitos para configurar o aplicativo específico, incluindo ambientes necessários, pontos de extremidade, mapeamento de declaração, atributos necessários, certificados e protocolos utilizados. Precisará estas informações para configurar o SSO no [portal do Azure AD](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Detalhes do mecanismo de autenticação

Para todas as aplicações SaaS previamente integradas, a Microsoft fornece um tutorial e não precisará estas informações. Se a aplicação não está no nosso mercado de aplicações / galeria, poderá ter de recolher as seguintes partes de dados:

- **Fornecedor de identidade atual, o aplicativo usa para SSO, se aplicável** – por exemplo: AD FS, PingFederate, Okta
- **Protocolos suportados pelo aplicativo de destino** – por exemplo, SAML 2.0, OpenID Connect, OAuth, autenticação baseada em formulários, WS-Fed, WS-Trust
- **Protocolo a ser configurado com o Azure AD** – por exemplo, SAML 2.0 ou 1.1, OpenID Connect, OAuth, baseada em formulários, WS-Fed

### <a name="attribute-requirements"></a>Requisitos de atributo

Existe um conjunto pré-configuradas de atributos e mapeamentos de atributos entre objetos de utilizador do Azure AD e os objetos de utilizador de cada aplicação de SaaS. Algumas aplicações, gerir outros tipos de objetos como grupos. Planear o mapeamento de atributos de utilizador do Azure AD à sua aplicação e [personalizar os mapeamentos de atributos padrão](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) , de acordo com seu negócio precisa.

### <a name="certificate-requirements"></a>Requisitos de certificado

O certificado para a aplicação tem de ser atualizado ou há um risco dos utilizadores não possam aceder à aplicação. A maioria dos certificados de aplicações de SaaS são ideais para 36 meses. Alterar essa duração do certificado no painel da aplicação. Lembre-se de que a expiração de documentos e saber como irá gerir a renovação de certificado. 

Existem duas formas de gerir os certificados. 

- **Rollover de automática de certificados** -a Microsoft suporta [rollover da chave de assinatura no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Embora esse seja o nosso método preferencial para gestão de certificados, nem todos os ISV suporta este cenário.

- **Atualização manual** -cada aplicativo tem seu próprio certificado expira com base em como ele é definido. Antes de expira o certificado da aplicação, crie um novo certificado e enviá-lo para o ISV. Esta informação pode ser extraída dos metadados de Federação. [Leia mais em metadados de Federação aqui.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Implementar SSO

Utilize as seguintes fases planejar e implantar sua solução na sua organização:

### <a name="user-configuration-for-sso"></a>Configuração do usuário para SSO

- **Identificar os seus utilizadores de teste**

   Contacte o proprietário da aplicação e peça-lhe para criar um mínimo de utilizadores de teste de três dentro do aplicativo. Certifique-se de que as informações que pretende utilizar como o identificador principal são preenchidas corretamente e corresponde a um atributo que está disponível no Azure AD. Na maioria dos casos isso irá mapear para o "NameID" para aplicações baseadas em SAML. Para os tokens JWT, é "preferred_username."
   
   Criar o utilizador no Azure AD ou manualmente, como um utilizador com base na cloud ou sincronizar o utilizador no local com o motor de sincronização do Azure AD Connect. Certifique-se de que as informações corresponde as afirmações que está a ser enviadas para a aplicação.

- **Configurar o SSO**

   Partir do [lista de aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list), localizar e abrir o tutorial SSO para a sua aplicação, em seguida, siga os passos do tutorial para configurar com êxito a aplicação SaaS.

   Se não conseguir localizar o seu aplicativo, consulte [documentação de aplicativo personalizada](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Isto irá guiá-lo por meio de sobre como adicionar uma aplicação que não se encontra na galeria do Azure AD.

   Opcionalmente, pode utilizar as afirmações emitidas no token SAML para a aplicação de enterprise utilizando [documentação de orientação da Microsoft](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping). Certifique-se de que isso é mapeado para o que esperar receber na resposta SAML para a sua aplicação. Se ocorrerem problemas durante a configuração, utilize o nosso orientações no [como a integração do SSO de depurar](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

Integração de aplicativo personalizada é uma funcionalidade de licenças do Azure AD Premium P1 ou P2.

### <a name="provide-sso-change-communications-to-end-users"></a>Fornecer comunicações de alteração SSO aos utilizadores finais

Implemente o plano de comunicação. Certifique-se de que está permitindo que os utilizadores finais, sabe que uma alteração vem, quando ele está disponível, o que fazer agora e como contactar a assistência.

### <a name="verify-end-user-scenarios-for-sso"></a>Verifique se a cenários de utilizador final para SSO

Pode utilizar os seguintes casos de teste para realizar testes na empresa e dispositivos pessoais para garantir que as configurações de SSO funcionam conforme esperado. Os cenários abaixo partem do princípio de que um utilizador estiver navegando para um URL de aplicação e passar por um fluxo de autenticação iniciado pelo fornecedor de serviços (fluxo de autenticação iniciado por SP).

| Cenário | Resultado esperado no fluxo de autenticação iniciado por SP por utilizador |
|----------|---------------------------------------------------|
| Início de sessão à aplicação com o IE enquanto na rede empresarial. | Autenticação integrada do Windows (IWA) ocorre sem pedidos adicionais. |
| Início de sessão à aplicação com o IE enquanto exteriores com nova tentativa de início de sessão. | Pedido do baseada em formulários no servidor do AD FS. Utilizador inicia sessão com êxito e o browser pede-lhe para a MFA. |
| Início de sessão à aplicação com o IE enquanto exteriores com uma sessão atual e nunca efetuou a MFA. | Utilizador não receber o pedido para o primeiro fator. Utilizador recebe aviso para a MFA. |
| Início de sessão à aplicação com o IE enquanto exteriores com uma sessão atual e já realizou MFA nesta sessão. | Utilizador não receber o pedido para o primeiro fator. Utilizador não recebe a MFA. Utilizador SSOs na aplicação. |
| Início de sessão à aplicação com o Firefox/Chrome/Safari enquanto exteriores com uma sessão atual e já realizou MFA nesta sessão. | Utilizador não receber o pedido para o primeiro fator. Utilizador não recebe a MFA. Utilizador do SSO na aplicação. |
| Inicie sessão na aplicação com o Firefox/Chrome/Safari enquanto exteriores com nova tentativa de início de sessão. | Pedido do baseada em formulários no servidor do AD FS. Utilizador inicia sessão com êxito e o browser pede-lhe para a MFA. |
| Início de sessão à aplicação com o Firefox/Chrome enquanto na rede empresarial com uma sessão atual. | Utilizador não receber o pedido para o primeiro fator. Utilizador não recebe a MFA. Utilizador do SSO na aplicação. |
| Início de sessão à aplicação com a aplicação móvel de aplicação com uma nova tentativa de início de sessão. | Pedido do baseada em formulários no servidor do AD FS. Pedidos de cliente da ADAL para a MFA e utilizador inicia sessão com êxito. |
| Utilizador não autorizado tentar iniciar sessão na aplicação com o URL de início de sessão. | Pedido do baseada em formulários no servidor do AD FS. Utilizador não conseguir iniciar sessão com o primeiro fator. |
| Utilizador autorizado tenta iniciar a sessão, mas insere uma palavra-passe incorreta. | Utilizador navega para o URL da aplicação e recebe o erro de nome de utilizador/palavra-passe incorreta. |
| Utilizador autorizado clica na ligação no e-mail e já está autenticado. | Utilizador clica na URL e está conectado a aplicação sem pedidos adicionais. |
| Utilizador autorizado clica na ligação no e-mail e ainda não está autenticado. | Utilizador clica na URL e é a linha de comandos autenticar com o primeiro fator. |
| Autorizado o utilizador inicia sessão na aplicação com a aplicação móvel de aplicação (iniciado por SP) com uma nova tentativa de início de sessão. | Pedido do baseada em formulários no servidor do AD FS. Pedidos de cliente da ADAL para a MFA e utilizador inicia sessão com êxito. |
| Utilizador não autorizado tentar iniciar sessão na aplicação com o URL de início de sessão (iniciado por SP). | Pedido do baseada em formulários no servidor do AD FS. Utilizador não conseguir iniciar sessão com o primeiro fator. |
| Utilizador autorizado tenta iniciar a sessão, mas insere uma palavra-passe incorreta.| Utilizador navega para o URL da aplicação e recebe o erro de nome de utilizador/palavra-passe incorreta. |
| Utilizador autorizado terminar a sessão e, em seguida, inicia sessão novamente. | Se o URL de fim de sessão é configurado, o utilizador tem sessão iniciada em todos os serviços e linha de comandos para autenticar. |
| Utilizador autorizado terminar a sessão e, em seguida, inicia sessão novamente. | Se o URL de fim de sessão não está configurado, usuário será automaticamente registado novamente no token de existente a partir da sessão de browser do Azure AD existente a utilizar. |
| Utilizador autorizado clica na ligação no e-mail e já está autenticado. | Utilizador clica na URL e está conectado a aplicação sem pedidos adicionais. |
| Utilizador autorizado clica na ligação no e-mail e ainda não está autenticado. | Utilizador clica na URL e é a linha de comandos autenticar com o primeiro fator. |

## <a name="manage-sso"></a>Gerir o SSO

Esta seção descreve os requisitos e recomendações para gerir com êxito o SSO.

### <a name="required-administrative-roles"></a>Funções administrativas necessárias

Utilize sempre a função com as permissões de menor disponíveis para realizar a tarefa necessária no Azure Active Directory. A Microsoft recomenda [rever as diferentes funções que estão disponíveis](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e escolher a ferramenta correta para resolver suas necessidades de cada pessoa para esta aplicação. Algumas funções poderão ter de ser aplicadas temporariamente e removido após a implementação foi concluída.

| Persona| Funções | Função do Azure AD (se necessário) |
|--------|-------|-----------------------------|
| Ajudar o administrador de suporte técnico | Suporte de escalão 1 | Nenhuma |
| Administrador de identidade | Configurar e depurar quando o impacto de problemas do Azure AD | Administrador global |
| Administrador da aplicação | Atestado de usuário no aplicativo, configuração, os utilizadores com permissões | Nenhuma |
| Administradores de infraestrutura | Proprietário de rollover de certificado | Administrador global |
| Proprietário/participante de negócios | Atestado de usuário no aplicativo, configuração, os utilizadores com permissões | Nenhuma |

Recomendamos que utilize [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) para gerir as funções para fornecer adicionais auditoria, controlo e acesso, reveja a utilizadores com permissões de diretório.

### <a name="sso-certificate-lifecycle-management"></a>Gerenciamento de ciclo de vida do certificado SSO

É importante identificar as funções corretas e listas de distribuição, a tarefa de gerenciar o ciclo de vida do certificado de assinatura entre o Azure AD de e-mail e a aplicação que está a ser configurada com o início de sessão único. Aqui estão algumas das principais funções que recomendamos ter no local:

- Proprietário para atualizar as propriedades do utilizador numa aplicação
- Proprietário da chamada para o suporte técnico break/fix da aplicação
- Lista de distribuição de e-mail monitorizado de perto para notificações de alteração relacionado com certificados

A duração máxima de um certificado é três anos. É recomendável estabelecer um processo na forma como irá processar uma alteração de certificado entre o Azure AD e a sua aplicação. Isto pode ajudar a impedir ou minimizar um período de indisponibilidade devido a um certificado prestes a expirar ou forçar o rollover de certificado.

### <a name="rollback-process"></a>Processo de reversão

Depois de concluir o teste com base nos seus casos de teste, está na altura de mover para produção com a sua aplicação. Ir para a produção significa que irá implementar as configurações testadas e não planeado no seu inquilino de produção e distribuí-lo aos seus utilizadores. No entanto, é importante planear o que fazer no caso da implementação não vai conforme planejado. Se a configuração de SSO falhar durante a implantação, deve compreender como a reduzir qualquer interrupção e a reduzir o impacto para os seus utilizadores.

A disponibilidade dos métodos de autenticação na aplicação irá determinar a melhor estratégia. Certifique-se sempre de que ter de documentação para os proprietários da aplicação sobre como obter de volta para o estado de configuração original do início de sessão no caso da implementação é executado em problemas de detalhada.

- **Se a sua aplicação suportar vários fornecedores de identidade**, para o exemplo LDAP e o AD FS e o Ping, não elimine a configuração de SSO existente durante a implementação. Em vez disso, desativá-lo durante a migração caso precise reverter a ele mais tarde. 

- **Se a aplicação não suportar vários IDPs** , mas permite aos utilizadores iniciar sessão com a autenticação baseada em formulários (nome de utilizador/palavra-passe), certifique-se de que os utilizadores podem reverter para essa abordagem em caso de falha de implementação de configuração do novo SSO.

### <a name="access-management"></a>Gestão de acesso

Recomendamos que escolha uma abordagem dimensionada ao gerir o acesso aos recursos. Abordagens comuns incluem a utilização de grupos no local, sincronizando através do Azure AD Connect [criar grupos dinâmicos no Azure AD com base nos atributos de utilizador](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal), ou criando [grupos de self-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) no Azure AD gerido por um proprietário do recurso.

### <a name="monitor-security"></a>Monitorização da segurança

Recomendamos a configuração de uma cadência regular em que reveja os diversos aspectos da segurança de aplicação SaaS e execute quaisquer ações corretivas necessárias.

### <a name="troubleshooting"></a>Resolução de problemas

Os links a seguir apresentam cenários de resolução de problemas. Pode querer criar um guia específico para a equipe de suporte que incorpora estes cenários e as etapas para corrigi-los.

#### <a name="consent-issues"></a>Problemas de consentimento

- [Erro de consentimento inesperado](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Erro de consentimento do utilizador](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Problemas de início de sessão

- [Problemas ao iniciar sessão a partir de um portal personalizado](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Problemas ao iniciar sessão no painel de acesso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Erro na página de início de sessão da aplicação](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Problema ao iniciar sessão num aplicativo da Microsoft](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Problemas SSO para os aplicativos listados na Galeria de aplicações do Azure

- [Problema com a palavra-passe SSO para aplicações listados na Galeria de aplicações do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Problema com o SSO federado para aplicativos listados na Galeria de aplicações do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Problemas SSO para aplicações não listadas na Galeria de aplicações do Azure

- [Problema com a palavra-passe SSO para aplicações não listadas na Galeria de aplicações do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Problema com o SSO federado para aplicações não listadas na Galeria de aplicações do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Passos Seguintes

[Depuração SSO baseadas em SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Mapeamento de declaração para aplicações através do PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Personalizar afirmações emitidas no SAML token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Protocolo SAML de início de sessão único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Protocolo SAML de Fim de Sessão Único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[O Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (para utilizadores externos, como parceiros e fornecedores)

[Acesso condicional do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Proteção de identidade do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Acesso SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Tutorial de SSO de aplicação](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
