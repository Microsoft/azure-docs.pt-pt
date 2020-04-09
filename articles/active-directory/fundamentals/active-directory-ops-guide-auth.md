---
title: Guia de referência de operações de autenticação de diretório ativo Azure
description: Este guia de referência de operações descreve os controlos e ações que deve tomar para garantir a gestão da autenticação
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: f25abb70a95f559cf0cc14efa6cf9f0e81ec9ec0
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876297"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Guia de referência de operações de autenticação de diretório ativo Azure

Esta secção do guia de referência de operações da [AD Azure](active-directory-ops-guide-intro.md) descreve as verificações e ações que deve tomar para garantir e gerir credenciais, definir experiência de autenticação, atribuição de delegados, medir o uso e definir políticas de acesso baseadas na postura de segurança da empresa.

> [!NOTE]
> Estas recomendações estão em vigor a partir da data da publicação, mas podem mudar ao longo do tempo. As organizações devem avaliar continuamente as suas práticas de identidade à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir proprietários a tarefas-chave

A gestão do Diretório Ativo azure requer a execução contínua de tarefas e processos operacionais fundamentais, que podem não fazer parte de um projeto de lançamento. Ainda é importante que você configuraestas tarefas para otimizar o seu ambiente. As tarefas-chave e os seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Gerir o ciclo de vida da configuração de inscrição única (SSO) em Azure AD | Equipa de Operações do IAM |
| Conceber políticas de acesso condicional para aplicações da AD Azure | Equipa de Arquitetura InfoSec |
| Archive atividade de entrada num sistema SIEM | Equipa de Operações infosec |
| Arquivar eventos de risco num sistema SIEM | Equipa de Operações infosec |
| Triagem e investigar relatórios de segurança | Equipa de Operações infosec |
| Triagem e investigar eventos de risco | Equipa de Operações infosec |
| Triagem e investigar utilizadores sinalizados por relatórios de risco e vulnerabilidade da Azure AD Identity Protection | Equipa de Operações infosec |

> [!NOTE]
> A Azure AD Identity Protection requer uma licença Azure AD Premium P2. Para encontrar a licença certa para os seus requisitos, consulte [Comparar as funcionalidades geralmente disponíveis das edições Azure AD Free e Azure AD Premium.](https://azure.microsoft.com/pricing/details/active-directory/)

Ao rever a sua lista, poderá descobrir que precisa de atribuir um proprietário a tarefas que faltam a um proprietário ou ajustar a propriedade para tarefas com proprietários que não estejam alinhados com as recomendações acima.

#### <a name="owner-recommended-reading"></a>Leitura recomendada do proprietário

- [Atribuir funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governação no Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>Gestão de credenciais

### <a name="password-policies"></a>Políticas de palavra-passe

Gerir as palavras-passe de forma segura é uma das partes mais críticas da gestão de identidade e acesso e muitas vezes o maior alvo de ataques. A Azure AD suporta várias funcionalidades que podem ajudar a evitar que um ataque seja bem sucedido.

Utilize o quadro abaixo para encontrar a solução recomendada para atenuar a questão que precisa de ser abordada:

| Problema | Recomendação |
| :- | :- |
| Nenhum mecanismo para proteger contra senhas fracas | Ativar o reset de [palavra-passe autosserviço](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) Azure AD (SSPR) e [a proteção de passwords](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) |
| Nenhum mecanismo para detetar senhas vazadas | Ativar a sincronização de [hash](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS) de senha para obter insights |
| Utilização de AD FS e incapaz de mover-se para a autenticação gerida | Ativar [o Bloqueio Inteligente Smart Lockout](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) e /ou [Azure AD Smart Lockout](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) |
| A política de passwords usa regras baseadas na complexidade, tais como comprimento, conjuntos de caracteres múltiplos ou expiração | Reconsidere a favor das [Práticas Recomendadas](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) da Microsoft e mude a sua abordagem para a gestão de passwords e implemente a proteção de [passwords Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad). |
| Os utilizadores não estão registados para usar a autenticação de vários fatores (MFA) | [Registe todas as informações de segurança do utilizador](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy) para que possa ser utilizada como um mecanismo para verificar a identidade do utilizador juntamente com a sua palavra-passe |
| Não existe revogação de senhas com base no risco do utilizador | Implementar políticas de risco de utilizador de [proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy) Azure AD para forçar alterações de senha em credenciais vazadas usando SSPR |
| Não existe um mecanismo inteligente de bloqueio para proteger a autenticação maliciosa de maus atores provenientes de endereços IP identificados | Implementar autenticação gerida pela nuvem com sincronização de hash de palavra-passe ou [autenticação pass-through](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Leitura recomendada por políticas de senha

- [As melhores práticas da Azure AD e da AD FS: Defender contra ataques de spray de senha - Mobilidade Empresarial + Segurança](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Ativar o reset de palavra-passe autosserviço e a proteção de passwords

Os utilizadores que precisam de alterar ou redefinir as suas palavras-passe é uma das maiores fontes de volume e custo das chamadas de secretária de ajuda. Além do custo, alterar a palavra-passe como uma ferramenta para mitigar o risco do utilizador é um passo fundamental para melhorar a postura de segurança da sua organização.

No mínimo, recomenda-se que implemente o reset de senha de [autosserviço](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) Azure AD (SSPR) e a proteção de [senhas](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) no local para realizar:

- Desvie as chamadas da secretária de ajuda.
- Substitua a utilização de senhas temporárias.
- Substitua qualquer solução de gestão de senhas de autosserviço existente que dependa de uma solução no local.
- [Elimine senhas fracas](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) na sua organização.

> [!NOTE]
> Para organizações com uma subscrição Azure AD Premium P2, recomenda-se a implementação de SSPR e usá-lo como parte de uma Política de Risco de Utilizador de [Proteção de Identidade.](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)

### <a name="strong-credential-management"></a>Forte gestão da credencial

As palavras-passe por si só não são seguras o suficiente para impedir que os maus atores tenham acesso ao seu ambiente. No mínimo, qualquer utilizador com uma conta privilegiada deve ser habilitado para a autenticação de vários fatores (MFA). Idealmente, deve ativar o [registo combinado](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) e exigir que todos os utilizadores se registem para MFA e SSPR utilizando a experiência de [registo combinado.](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview) Eventualmente, recomendamos que adote uma estratégia para [fornecer resiliência](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) para reduzir o risco de bloqueio devido a circunstâncias imprevistas.

![Fluxo combinado de experiência do utilizador](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>No local, a autenticação da autenticação

Além dos benefícios da simplicidade e da deteção de credenciais vazadas, o Azure AD Password Hash Sync (PHS) e o Azure MFA permitem aos utilizadores aceder às aplicações SaaS e ao Office 365, apesar das interrupções no local devido a ciberataques como o [NotPetya.](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/) Também é possível ativar phs em conjunto com a federação. Ativar o PHS permite uma recaída da autenticação quando os serviços da federação não estão disponíveis.

Se a sua organização no local não tiver uma estratégia de resiliência de paragem ou tiver uma que não esteja integrada com o Azure AD, deve implementar o Azure AD PHS e definir um plano de recuperação de desastres que inclua PHS. Ativar o Azure AD PHS permitirá que os utilizadores se autentifiquem contra o Azure AD caso o seu Diretório Ativo no local não esteja disponível.

![fluxo de sincronização de hash palavra-passe](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Para melhor compreender as suas opções de autenticação, consulte [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory](../hybrid/choose-ad-authn.md).

### <a name="programmatic-usage-of-credentials"></a>Utilização programática de credenciais

Os scripts Azure AD utilizando o PowerShell ou aplicações que utilizam a Microsoft Graph API requerem autenticação segura. A má gestão credencial que executa os scripts e ferramentas aumenta o risco de roubo de credenciais. Se estiver a utilizar scripts ou aplicações que dependam de senhas ou instruções de senha sinuosas codificadas, deve primeiro rever as palavras-passe em ficheiros config ou código fonte, em seguida, substituir essas dependências e utilizar identidades geridas pelo Azure, autenticação integrada do Windows ou [certificados](../reports-monitoring/tutorial-access-api-with-certificates.md) sempre que possível. Para aplicações onde as soluções anteriores não são possíveis, considere utilizar o [Cofre de Chaves Azure](https://azure.microsoft.com/services/key-vault/).

Se determinar que existem diretores de serviço com credenciais de senha e não tem a certeza de como essas credenciais de senha são protegidas por scripts ou aplicações, contacte o proprietário da aplicação para entender melhor os padrões de utilização.

A Microsoft também recomenda que contacte os proprietários de aplicações para entender os padrões de utilização se houver diretores de serviço com credenciais de senha.

## <a name="authentication-experience"></a>Experiência de autenticação

### <a name="on-premises-authentication"></a>Autenticação no local

Autenticação Federada com Autenticação Integrada do Windows (IWA) ou Single Sign-On (SSO) gerida por autenticação gerida com sincronização de hash de palavra-passe ou autenticação pass-through é a melhor experiência do utilizador quando dentro da rede corporativa com controladores de domínio de linha de visão no local. Minimiza a fadiga de alerta credencial e reduz o risco de os utilizadores serem vítimas de ataques de phishing. Se já estiver a utilizar a autenticação gerida pela nuvem com PHS ou PTA, mas os utilizadores ainda precisam de escrever a sua palavra-passe ao autenticar no local, então deve implementar imediatamente [o SSO SSO sem emenda](../hybrid/how-to-connect-sso.md). Por outro lado, se você está atualmente federado com planos para eventualmente migrar para a autenticação gerida pela nuvem, então você deve implementar Seamless SSO como parte do projeto de migração.

### <a name="device-trust-access-policies"></a>Políticas de acesso à confiança do dispositivo

Tal como um utilizador na sua organização, um dispositivo é uma identidade central que pretende proteger. Pode usar a identidade de um dispositivo para proteger os seus recursos a qualquer momento e de qualquer local.Autenticar o dispositivo e contabilizar o seu tipo de confiança melhora a sua postura de segurança e usabilidade por:

- Evitando atrito, por exemplo, com MFA, quando o dispositivo é confiável
- Bloquear o acesso a dispositivos não confiáveis
- Para dispositivos Windows 10, forneça [um único sinal aos recursos no local sem problemas.](../devices/azuread-join-sso.md)

Pode realizar este objetivo trazendo identidades do dispositivo e gerindo-as em Azure AD utilizando um dos seguintes métodos:

- As organizações podem usar o [Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune) para gerir o dispositivo e impor as políticas de conformidade, atestar a saúde do dispositivo e definir políticas de acesso condicional com base no cumprimento do dispositivo. O Microsoft Intune pode gerir dispositivos iOS, desktops Mac (através da integração JAMF), desktops windows (utilizando de forma nativa a Gestão de Dispositivos Móveis para windows 10, e cogestão com o Microsoft Endpoint Configuration Manager) e dispositivos móveis Android.
- [A adesão da Hybrid Azure AD](../devices/hybrid-azuread-join-managed-domains.md) fornece gestão com políticas de grupo ou Microsoft Endpoint Configuration Manager num ambiente com dispositivos de computadores ligados ao domínio Ative Directory. As organizações podem implementar um ambiente gerido através de PHS ou PTA com SSO SSO Sso sem emenda. Trazer os seus dispositivos para o Azure AD maximiza a produtividade dos utilizadores através do SSO através da sua nuvem e recursos no local, permitindo-lhe garantir o acesso à sua nuvem e recursos no local com [Acesso](../conditional-access/overview.md) Condicional ao mesmo tempo.

Se tiver dispositivos Windows unidos por domínio sem registo na nuvem, ou dispositivos Windows unidos por domínio que estejam registados na nuvem mas sem políticas de acesso condicional, então deve registar os dispositivos não registados e, em qualquer dos casos, utilizar o [Hybrid Azure AD como controlo](../conditional-access/require-managed-devices.md) nas suas políticas de acesso condicional.

![Uma imagem de concessão na política de acesso condicional que requer dispositivo híbrido](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Se estiver a gerir dispositivos com MDM ou Microsoft Intune, mas não utilizar controlos de dispositivos nas suas políticas de acesso condicional, recomendamos que a utilização do [dispositivo Requires seja marcada como conforme](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) como um controlo nessas políticas.

![Uma imagem de concessão na política de acesso condicional que requer o cumprimento do dispositivo](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Políticas de acesso à confiança do dispositivo recomendadas para a leitura

- [Como: Planeie a sua direção ativa híbrida Azure aderir à implementação](../devices/hybrid-azuread-join-plan.md)
- [Configurações de acesso aos dispositivos e identidade](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello para empresas

No Windows 10, [o Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) substitui senhas por autenticação forte de dois fatores em PCs. O Windows Hello for Business permite uma experiência de MFA mais simplificada para os utilizadores e reduz a sua dependência de senhas. Se ainda não começou a lançar dispositivos Windows 10 ou apenas os implementou parcialmente, recomendamos que atualize para o Windows 10 e ative o [Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) em todos os dispositivos.

Se quiser saber mais sobre autenticação sem palavras-passe, consulte [Um mundo sem senhas com Diretório Ativo Azure](../authentication/concept-authentication-passwordless.md).

## <a name="application-authentication-and-assignment"></a>Autenticação e atribuição de candidaturas

### <a name="single-sign-on-for-apps"></a>Inscrição única para apps

Fornecer um mecanismo de inscrição única padronizado a toda a empresa é crucial para a melhor experiência do utilizador, redução de risco, capacidade de reporte e governação. Se estiver a utilizar aplicações que suportam OSO com AD Azure, mas que está atualmente configurada para utilizar contas locais, deverá reconfigurar essas aplicações para utilizar o SSO com a AD Azure. Da mesma forma, se estiver a utilizar quaisquer aplicações que suportem o SSO com a AD Azure, mas que esteja a utilizar outro Fornecedor de Identidade, deve reconfigurar essas aplicações para utilizar o SSO com a AD Azure também. Para aplicações que não suportam protocolos da federação mas que suportem a autenticação baseada em formulários, recomendamos que configure a aplicação para usar [abóbada](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) de senha com procuração de aplicação ad..

![AppProxy Password-based Sign-on](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Se não tiver um mecanismo para descobrir aplicações não geridas na sua organização, recomendamos implementar um processo de descoberta utilizando uma solução de corretor de segurança de acesso à nuvem (CASB) como o [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Finalmente, se tiver uma galeria de aplicações Azure AD e utilizar aplicações que suportem sSO com AD Azure, recomendamos a [listagem da aplicação na galeria de aplicações.](../azuread-dev/howto-app-gallery-listing.md)

#### <a name="single-sign-on-recommended-reading"></a>Leitura recomendada por inscrição única

- [O que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migração de aplicações da AD FS para a AD Azure

[As aplicações migratórias de AD FS para Azure AD](../manage-apps/migrate-adfs-apps-to-azure.md) permitem capacidades adicionais em segurança, gestão mais consistente e uma melhor experiência de colaboração. Se tiver aplicações configuradas em AD FS que suportam SSO com AD Azure, então deve reconfigurar essas aplicações para utilizar OSO com AD Azure. Se tiver aplicações configuradas em AD FS com configurações incomuns não suportadas pelo Azure AD, deverá contactar os proprietários da aplicação para perceber se a configuração especial é um requisito absoluto da aplicação. Se não for necessário, deverá reconfigurar a aplicação para utilizar o SSO com a AD Azure.

![Azure AD como o principal fornecedor de identidade](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> A [Azure AD Connect Health for ADFS](../hybrid/how-to-connect-health-adfs.md) pode ser usada para recolher detalhes de configuração sobre cada aplicação que pode potencialmente ser migrada para AD Azure.

### <a name="assign-users-to-applications"></a>Atribuir utilizadores a aplicações

[Atribuir utilizadores a aplicações](../manage-apps/assign-user-or-group-access-portal.md) é melhor mapeado utilizando grupos porque permitem uma maior flexibilidade e capacidade de gestão em escala. Os benefícios da utilização de grupos incluem [a adesão](../users-groups-roles/groups-dynamic-membership.md) de grupodinâmico baseado em atributos e delegação aos proprietários de [aplicações.](../fundamentals/active-directory-accessmanagement-managing-group-owners.md) Portanto, se já está a utilizar e gerir grupos, recomendamos que tome as seguintes ações para melhorar a gestão à escala:

- Delegar gestão e governação de grupo seleções aos proprietários de candidaturas.
- Permitir o acesso ao autosserviço à aplicação.
- Defina grupos dinâmicos se os atributos do utilizador puderem determinar consistentemente o acesso às aplicações.
- Implementar o atestado a grupos utilizados para o acesso à aplicação utilizando avaliações de [acesso a AD Azure](../governance/access-reviews-overview.md).

Por outro lado, se encontrar aplicações que tenham atribuição a utilizadores individuais, certifique-se de implementar a [governação](https://docs.microsoft.com/azure/active-directory/governance/index) em torno dessas aplicações.

#### <a name="assign-users-to-applications-recommended-reading"></a>Atribuir utilizadores a aplicações de leitura recomendada

- [Atribuir utilizadores e grupos a uma aplicação no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [Delegar permissões de registo de apps no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Regras dinâmicas de adesão para grupos no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>Políticas de acesso

### <a name="named-locations"></a>Localizações com nome

Com [localizações nomeadas](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) em Azure AD, pode rotular gamas de endereços IP fidedignos na sua organização. O Microsoft Azure AD utiliza localizações com nome para:

- Prevenir falsos positivos em eventos de risco. A sessão a partir de um local de rede fidedigno reduz o risco de inscrição de um utilizador.
- Configure [o acesso condicional baseado na localização.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

![Localização com nome](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Com base na prioridade, use a tabela abaixo para encontrar a solução recomendada que melhor responda às necessidades da sua organização:

| **Prioridade** | **Cenário** | **Recomendação** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Se utilizar PHS ou PTA e locais nomeados não foram definidos | Defina locais nomeados para melhorar a deteção de eventos de risco |
| 2 | Se você é federado e não usar a reivindicação "insideCorporateNetwork" e os locais nomeados não foram definidos | Defina locais nomeados para melhorar a deteção de eventos de risco |
| 3 | Se não utilizar localizações nomeadas em políticas de acesso condicional e não houver controlos de risco ou dispositivo sintetizados em políticas de acesso condicional | Configure a política de acesso condicional para incluir locais nomeados |
| 4 | Se você é federado e usar a reivindicação "insideCorporateNetwork" e os locais nomeados não foram definidos | Defina locais nomeados para melhorar a deteção de eventos de risco |
| 5 | Se estiver a usar endereços IP fidedignos com MFA em vez de locais nomeados e a marcá-los como confiáveis | Defina localizações nomeadas e marque-as como confiáveis para melhorar a deteção de eventos de risco |

### <a name="risk-based-access-policies"></a>Políticas de acesso baseadas no risco

A Azure AD pode calcular o risco para cada entrada e cada utilizador. A utilização do risco como critério nas políticas de acesso pode proporcionar uma melhor experiência do utilizador, por exemplo, menos solicitações de autenticação e uma melhor segurança, por exemplo, apenas solicitar aos utilizadores quando são necessários, e automatizar a resposta e a reparação.

![Política de risco de inscrição](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Se já possui licenças Azure AD Premium P2 que suportam o uso de riscos nas políticas de acesso, mas que não estão a ser utilizadas, recomendamos vivamente adicionar risco à sua postura de segurança.

#### <a name="risk-based-access-policies-recommended-reading"></a>Políticas de acesso baseadas no risco recomendadas para a leitura

- [Como: Configurar a política de risco de entrada](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Como: Configurar a política de risco do utilizador](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Políticas de acesso à aplicação de clientes

A Microsoft Intune Application Management (MAM) fornece a capacidade de pressionar controlos de proteção de dados, tais como encriptação de armazenamento, PIN, limpeza remota de armazenamento, etc. para aplicações móveis compatíveis com clientes como o Outlook Mobile. Além disso, podem ser criadas políticas de acesso condicional para restringir o [acesso](../conditional-access/app-based-conditional-access.md) a serviços na nuvem, como o Exchange Online, a partir de aplicações aprovadas ou compatíveis.

Se os seus colaboradores instalarem aplicações mam-able, como aplicações móveis do Office, para aceder a recursos corporativos como o Exchange Online ou o SharePoint Online, e também apoiar o BYOD (traga o seu próprio dispositivo), recomendamos que implemente políticas de Aplicação MAM para gerir a configuração da aplicação em dispositivos pessoais sem matrícula de MDM e, em seguida, atualizar as suas políticas de acesso condicional para permitir apenas o acesso de clientes capazes de MAM.

![Controlo de subvenção de acesso condicional](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Se os colaboradores instalarem aplicações mam-capables contra recursos corporativos e o acesso for restrito em dispositivos Geridos intune, então deve considerar implementar políticas de MAM de aplicação para gerir a configuração da aplicação para dispositivos pessoais, e atualizar as políticas de Acesso Condicional para apenas permitir o acesso de clientes capazes de MAM.

### <a name="conditional-access-implementation"></a>Implementação do Acesso Condicional

O Acesso Condicional é uma ferramenta essencial para melhorar a postura de segurança da sua organização. Por isso, é importante que siga estas boas práticas:

- Certifique-se de que todas as aplicações SaaS têm pelo menos uma política aplicada
- Evite combinar o filtro **Todas as aplicações** com o controlo do **bloco** para evitar o risco de bloqueio
- Evite utilizar os **utilizadores como** filtro e adicionar inadvertidamente **os Hóspedes**
- **Migrar todas as políticas de "legado" para o portal Azure**
- Apanhe todos os critérios para utilizadores, dispositivos e aplicações
- Utilizar políticas de acesso condicional para [implementar MFA](../conditional-access/plan-conditional-access.md), em vez de utilizar um **MFA por utilizador**
- Ter um pequeno conjunto de políticas fundamentais que podem aplicar-se a múltiplas aplicações
- Defina grupos de exceção vazios e adicione-os às políticas para ter uma estratégia de exceção
- Plano para quebrar contas [de vidro](../users-groups-roles/directory-admin-roles-secure.md#break-glass-what-to-do-in-an-emergency) sem controlos de MFA
- Garantir uma experiência consistente em todas as aplicações de clientes do Office 365, por exemplo, Equipas, OneDrive para Negócios, Outlook, etc.) implementando o mesmo conjunto de controlos para serviços como O Exchange Online e O Sharepoint Online
- A atribuição de políticas deve ser implementada através de grupos, não de indivíduos
- Faça revisões regulares dos grupos de exceção utilizados nas políticas para limitar o tempo que os utilizadores estão fora da postura de segurança. Se possuir O Azure AD P2, então pode usar avaliações de acesso para automatizar o processo

#### <a name="conditional-access-recommended-reading"></a>Acesso Condicional leitura recomendada

- [Boas práticas para acesso condicional em Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [Configurações de acesso aos dispositivos e identidade](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Referência de definições de acesso condicional do Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [Políticas de Acesso Condicional comuns](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>Área de superfície de acesso

### <a name="legacy-authentication"></a>Autenticação legado

Credenciais fortes como o MFA não podem proteger aplicações usando protocolos de autenticação legado, que fazem dele o vetor de ataque preferido por atores mal-intencionados. Bloquear a autenticação do legado é crucial para melhorar a postura de segurança de acesso.

A autenticação do legado é um termo que se refere a protocolos de autenticação utilizados por apps como:

- Clientes mais antigos do Office que não usam autenticação moderna (por exemplo, cliente do Office 2010)
- Clientes que utilizam protocolos de correio como IMAP/SMTP/POP

Os atacantes preferem fortemente estes protocolos - na verdade, quase [100% dos ataques com spray de senha](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) usam protocolos de autenticação legado! Os hackers usam protocolos de autenticação legado, porque não suportam o início de sessão interativa, que é necessário para desafios de segurança adicionais, como a autenticação de vários fatores e a autenticação do dispositivo.

Se a autenticação do legado for amplamente utilizada no seu ambiente, deverá planear migrar clientes legados para clientes que suportem a [autenticação moderna](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) o mais rapidamente possível. No mesmo sinal, se tem alguns utilizadores já a utilizar a autenticação moderna mas outros que ainda utilizam a autenticação do legado, deverá tomar as seguintes medidas para bloquear clientes de autenticação legado:

1. Utilize [relatórios de atividade](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) de iniciar sessão para identificar utilizadores que ainda utilizam autenticação e reparação de planos:

   a. Upgrade para clientes modernos de autenticação capazes de clientes para utilizadores afetados.
   
   b. Planeie um prazo de corte para bloquear por passos abaixo.
   
   c. Identifique quais as aplicações antigas que têm uma forte dependência da autenticação do legado. Consulte o passo 3 abaixo.

2. Desative os protocolos legados na fonte (por exemplo, Exchange Mailbox) para utilizadores que não estão a usar o legado auth para evitar mais exposição.
3. Para as restantes contas (idealmente identidades não humanas, como contas de serviço), utilize o acesso condicional para restringir os [protocolos legados](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) após a autenticação.

#### <a name="legacy-authentication-recommended-reading"></a>Leitura recomendada pela autenticação do legado

- [Ativar ou desativar o acesso pop3 ou IMAP4 às caixas de correio no Exchange Server](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>Bolsas de consentimento

Num ataque de concessão de consentimento ilícito, o intruso cria uma aplicação registada pela AD Azure que solicita acesso a dados como informações de contacto, e-mail ou documentos. Os utilizadores podem estar a conceder consentimento a aplicações maliciosas através de ataques de phishing ao aterrar em sites maliciosos.

Abaixo estão uma lista de aplicações com permissões que você pode querer escrutinar para serviços de nuvem microsoft:

- Aplicativos com app \*ou delegados. ReadWrite Permissions
- As aplicações com permissões delegadas podem ler, enviar ou gerir e-mails em nome do utilizador
- Aplicações que são concedidas utilizando as seguintes permissões:

| Recurso | Permissão |
| :- | :- |
| Escritório 365 Exchange Online | A EAS. AccessAsuser.All |
| | EWS. AccessAsuser.All |
| | Correio.Ler |
| Microsoft Graph API | Correio.Ler |
| | Mail.read.shared |
| | Mail.ReadWrite |

- As aplicações concederam a plena imitação do utilizador do utilizador inscrito. Por exemplo:

|Recurso | Permissão |
| :- | :- |
| Microsoft Graph API| Diretório.AccessAsUser.All |
| API REST do Azure | user_impersonation |

Para evitar este cenário, deve [consultar-se para detetar e remediar as subvenções](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) ilícitas de consentimento no Office 365 para identificar e corrigir quaisquer pedidos com subvenções ilícitas ou pedidos que tenham mais subvenções do que as necessárias. Em seguida, remova completamente o [autosserviço](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent) e estabeleça procedimentos de [governação.](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow) Por fim, marque revisões regulares das permissões das aplicações e remova-as quando não forem necessárias.

#### <a name="consent-grants-recommended-reading"></a>Bolsas de consentimento leitura recomendada

- [Permissões da Microsoft Graph API](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Definições de utilizador e grupo

Abaixo estão as definições de utilizador e grupo que podem ser bloqueadas se não houver uma necessidade explícita de negócio:

#### <a name="user-settings"></a>Definições do utilizador

- **Utilizadores Externos** - a colaboração externa pode acontecer organicamente na empresa com serviços como Teams, Power BI, Sharepoint Online e Azure Information Protection. Se tiver constrangimentos explícitos para controlar a colaboração externa iniciada pelo utilizador, recomenda-se que ative utilizadores externos utilizando a gestão do Direito do Direito do [Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) ou uma operação controlada, como por exemplo através do seu balcão de ajuda. Se não quiser permitir uma colaboração externa orgânica para serviços, pode impedir os [membros de convidar completamente utilizadores externos](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations). Em alternativa, também pode [permitir ou bloquear domínios específicos](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list) em convites externos ao utilizador.
- **Registos de aplicações** - quando os registos de aplicações estão ativados, os utilizadores finais podem embarcar em si próprios e conceder acesso aos seus dados. Um exemplo típico do registo da App são os utilizadores que permitem aos plug-ins do Outlook, ou assistentes de voz como alexa e Siri, ler o seu e-mail e calendário ou enviar e-mails em seu nome. Se o cliente decidir desativar o registo da App, as equipas InfoSec e IAM devem estar envolvidas na gestão de exceções (registos de aplicações que são necessários com base nos requisitos do negócio), pois teriam de registar as aplicações com uma conta de administrador, e muito provavelmente exigirão a conceção de um processo para operacionalizar o processo.
- **Portal da Administração** - as organizações podem bloquear a lâmina Azure AD no portal Azure para que os não administradores possam aceder à gestão da AD Azure no portal Azure e confundir-se. Aceda às definições do utilizador no portal de gestão da AD Azure para restringir o acesso:

![Portal de administração acesso restrito](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Os não-administradores ainda podem aceder às interfaces de gestão da AD Azure através da linha de comando e outras interfaces programáticas.

#### <a name="group-settings"></a>Definições de grupo

**Gestão de Grupos de Self-Service / Utilizadores podem criar grupos de segurança / grupos O365.** Se não houver uma iniciativa de autosserviço atual para grupos na nuvem, os clientes podem decidir desligá-lo até que estejam prontos para usar esta capacidade.

#### <a name="groups-recommended-reading"></a>Leitura recomendada por grupos

- [O que é a colaboração B2B do Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Integração de Aplicações com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Apps, permissões e consentimento no Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [Utilizar grupos para gerir o acesso aos recursos no Diretório Ativo Do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [Criação de gestão de acesso a aplicações de autosserviço no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>Tráfego de locais inesperados

Os atacantes são originários de várias partes do mundo. Gerir este risco utilizando políticas de acesso condicional com localização como condição. O estado de [localização](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) de uma política de Acesso Condicional permite bloquear o acesso a locais de onde não há motivo sinuoso para iniciar sessão.

![Criar uma nova localização nomeada](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Se disponível, utilize uma solução de informação de segurança e gestão de eventos (SIEM) para analisar e encontrar padrões de acesso entre regiões. Se não utilizar um produto SIEM, ou se não estiver a ingerir informações de autenticação da Azure AD, recomendamos que utilize o [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para identificar padrões de acesso entre regiões.

## <a name="access-usage"></a>Uso de acesso

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Registos da AD Azure arquivados e integrados com planos de resposta a incidentes

Ter acesso a atividade de inscrição, auditorias e eventos de risco para a AD Azure é crucial para investigações de resolução de problemas, análise de uso e investigações forenses. A Azure AD fornece acesso a estas fontes através de APIs REST que têm um período de retenção limitado. Um sistema de informação de segurança e gestão de eventos (SIEM), ou tecnologia de arquivo equivalente, é fundamental para o armazenamento a longo prazo de auditorias e suporte. Para permitir o armazenamento a longo prazo dos registos AD Azure, deve adicioná-los à solução SIEM existente ou utilizar [o Monitor Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor). Arquivo de registos que podem ser usados como parte dos seus planos de resposta a incidentes e investigações.

#### <a name="logs-recommended-reading"></a>Leitura recomendada de registos

- [Referência da Auditoria De Diretório Ativo Azure API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [Relatório de atividade sinuoso do Diretório Ativo Azure referência a API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [Utilizar a API de Relatórios do Azure AD com certificados para obter dados](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Microsoft Graph para Azure Ative Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [Referência a API de Atividade de Gestão 365](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [Como utilizar o Pacote de Conteúdos do Power BI para o Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Resumo

Há 12 aspetos para uma infraestrutura de identidade segura. Esta lista irá ajudá-lo a garantir e gerir ainda mais credenciais, definir experiência de autenticação, atribuição de delegados, medir o uso e definir políticas de acesso baseadas na postura de segurança da empresa.

- Atribuir os proprietários a tarefas-chave.
- Implementar soluções para detetar senhas fracas ou vazadas, melhorar a gestão e proteção de passwords e garantir ainda mais o acesso dos utilizadores aos recursos.
- Gerencie a identidade dos dispositivos para proteger os seus recursos a qualquer momento e de qualquer local.
- Implementar a autenticação sem palavras-passe.
- Forneça um mecanismo de inscrição único padronizado em toda a organização.
- Migrar aplicativos de AD FS para Azure AD para permitir uma melhor segurança e uma gestão mais consistente.
- Atribuir os utilizadores às aplicações utilizando grupos para permitir uma maior flexibilidade e capacidade de gestão à escala.
- Configure políticas de acesso baseadas no risco.
- Bloqueie os protocolos de autenticação do legado.
- Detetar e remediar as subvenções ilícitas de consentimento.
- Bloqueie as definições do utilizador e do grupo.
- Permitir o armazenamento a longo prazo de registos da AD Azure para resolução de problemas, análisede utilização e investigações forenses.

## <a name="next-steps"></a>Passos seguintes

Começar com os [controlos operacionais e ações operacionais da governação da identidade.](active-directory-ops-guide-govern.md)
