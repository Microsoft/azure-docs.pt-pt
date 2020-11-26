---
title: Guia de referência de operações de gestão de autenticação ativa do Azure Ative Directory
description: Este guia de referência de operações descreve as verificações e ações que deve tomar para garantir a gestão da autenticação
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
ms.openlocfilehash: 5f529e6148463ae384791985659378cb9d0a5046
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168870"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Guia de referência de operações de gestão de autenticação ativa do Azure Ative Directory

Esta secção do guia de referência de [operações Azure AD](active-directory-ops-guide-intro.md) descreve os controlos e ações que deve tomar para proteger e gerir credenciais, definir experiência de autenticação, delegar, medir o uso e definir políticas de acesso com base na postura de segurança da empresa.

> [!NOTE]
> Estas recomendações estão em vigor a partir da data da publicação, mas podem mudar ao longo do tempo. As organizações devem avaliar continuamente as suas práticas de identidade à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir os proprietários a tarefas-chave

A Gestão do Diretório Ativo Azure requer a execução contínua de tarefas e processos operacionais fundamentais, que podem não fazer parte de um projeto de implantação. Ainda é importante que crie estas tarefas para otimizar o seu ambiente. As tarefas-chave e os seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Gerir o ciclo de vida da configuração de um único sign-on (SSO) em Azure AD | Equipa de Operações do IAM |
| Conceber políticas de acesso condicional para aplicações AD Azure | Equipa de Arquitetura InfoSec |
| Atividade de inscrição de arquivo num sistema SIEM | Equipa de Operações InfoSec |
| Archive eventos de risco num sistema SIEM | Equipa de Operações InfoSec |
| Triagem e investigar relatórios de segurança | Equipa de Operações InfoSec |
| Triagem e investigar eventos de risco | Equipa de Operações InfoSec |
| Triagem e investigam utilizadores sinalizados por relatórios de risco e vulnerabilidade da Azure AD Identity Protection | Equipa de Operações InfoSec |

> [!NOTE]
> A Azure AD Identity Protection requer uma licença Azure AD Premium P2. Para encontrar a licença certa para os seus requisitos, consulte [comparar as funcionalidades geralmente disponíveis das edições Azure AD Free e Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/).

Ao rever a sua lista, poderá descobrir que precisa de atribuir um proprietário para tarefas que faltam a um proprietário ou ajustar a propriedade para tarefas com proprietários que não estejam alinhados com as recomendações acima.

#### <a name="owner-recommended-reading"></a>Leitura recomendada pelo proprietário

- [Atribuir funções de administrador no Azure Active Directory](../roles/permissions-reference.md)
- [Governação no Azure](../../governance/index.yml)

## <a name="credentials-management"></a>Gestão de credenciais

### <a name="password-policies"></a>Políticas de palavra-passe

Gerir as palavras-passe de forma segura é uma das partes mais críticas da gestão de identidade e acesso e muitas vezes o maior alvo de ataques. A Azure AD suporta várias funcionalidades que podem ajudar a evitar que um ataque seja bem sucedido.

Utilize o quadro abaixo para encontrar a solução recomendada para atenuar a questão que precisa de ser abordada:

| Problema | Recomendação |
| :- | :- |
| Nenhum mecanismo para proteger contra senhas fracas | Ativar o [reset da palavra-passe de autosserviço AZure (SSPR)](../authentication/concept-sspr-howitworks.md) e [a proteção da palavra-passe](../authentication/concept-password-ban-bad-on-premises.md) |
| Nenhum mecanismo para detetar senhas vazadas | Permitir a [sincronização de haxixe de palavra-passe](../hybrid/how-to-connect-password-hash-synchronization.md) (PHS) para obter insights |
| Utilização de FS AD e incapaz de mover-se para a autenticação gerida | Ativar [o bloqueio inteligente da extranet AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) e/ou [Azure AD Smart Lockout](../authentication/howto-password-smart-lockout.md) |
| A política de palavra-passe utiliza regras baseadas na complexidade, tais como comprimento, múltiplos conjuntos de caracteres ou expiração | Reconsidere a favor das [práticas recomendadas](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) pela Microsoft e altere a sua abordagem para a gestão de passwords e implemente [a proteção de senha AZure AD](../authentication/concept-password-ban-bad.md). |
| Os utilizadores não estão registados para utilizar a autenticação de vários fatores (MFA) | [Registar todas as informações de segurança do utilizador](../identity-protection/howto-identity-protection-configure-mfa-policy.md) para que possa ser usado como um mecanismo para verificar a identidade do utilizador juntamente com a sua palavra-passe |
| Não há revogação de senhas baseadas no risco do utilizador | Implementar políticas de [risco de proteção](../identity-protection/howto-identity-protection-configure-risk-policies.md) de identidade Azure AD para forçar alterações de senha em credenciais vazadas usando SSPR |
| Não existe nenhum mecanismo de bloqueio inteligente para proteger a autenticação maliciosa de maus atores provenientes de endereços IP identificados | Implementar a autenticação gerida pela nuvem com sincronização de haxixe [de palavra-passe](../hybrid/how-to-connect-pta-quick-start.md) ou autenticação de passagem (PTA) |

#### <a name="password-policies-recommended-reading"></a>Políticas de palavra-passe recomendadas leitura

- [Azure AD e AD FS boas práticas: Defender contra ataques de spray de senha - Mobilidade Empresarial + Segurança](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Ativar o reset da palavra-passe de autosserviço e a proteção da palavra-passe

Os utilizadores que precisam de alterar ou redefinir as suas palavras-passe é uma das maiores fontes de volume e custo das chamadas de help desk. Além do custo, alterar a palavra-passe como uma ferramenta para mitigar o risco do utilizador é um passo fundamental para melhorar a postura de segurança da sua organização.

No mínimo, recomenda-se que implemente [o reset da palavra-passe de autosserviço](../authentication/concept-sspr-howitworks.md) Azure AD (SSPR) e a proteção da [palavra-passe](../authentication/howto-password-ban-bad-on-premises-deploy.md) no local para realizar:

- Desvie as chamadas do balcão de ajuda.
- Substitua a utilização de senhas temporárias.
- Substitua qualquer solução de gestão de senha de autosserviço existente que dependa de uma solução no local.
- [Elimine senhas fracas](../authentication/concept-password-ban-bad.md) na sua organização.

> [!NOTE]
> Para organizações com uma assinatura Azure AD Premium P2, recomenda-se a implementação de SSPR e a sua utilização como parte de uma Política de Risco de Utilização de [Proteção](../identity-protection/howto-identity-protection-configure-risk-policies.md)de Identidade .

### <a name="strong-credential-management"></a>Forte gestão credencial

As palavras-passe por si só não são seguras o suficiente para impedir que os maus atores tenham acesso ao seu ambiente. No mínimo, qualquer utilizador com uma conta privilegiada deve ser ativado para a autenticação de vários fatores (MFA). Idealmente, deve permitir o [registo combinado](../authentication/concept-registration-mfa-sspr-combined.md) e exigir que todos os utilizadores se registem para MFA e SSPR utilizando a experiência de [registo combinado.](../user-help/security-info-setup-signin.md) Eventualmente, recomendamos que adote uma estratégia para [fornecer resiliência](../authentication/concept-resilient-controls.md) para reduzir o risco de bloqueio devido a circunstâncias imprevistas.

![Fluxo combinado de experiência do utilizador](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Resiliência da autenticação em local

Além dos benefícios da simplicidade e permitindo a deteção de credenciais vazadas, a Azure AD Password Hash Sync (PHS) e a Azure AD MFA permitem aos utilizadores aceder às aplicações saaS e à Microsoft 365, apesar de falhas no local devido a ciberataques como [o NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/). Também é possível permitir phs enquanto em conjunto com a federação. Ativar phs permite uma redução da autenticação quando os serviços da federação não estão disponíveis.

Se a sua organização no local não tiver uma estratégia de resiliência de paralisação ou tiver uma que não esteja integrada com a Azure AD, deve implementar phS AD Ad Azure e definir um plano de recuperação de desastres que inclua PHS. Permitir que o Azure AD PHS permita que os utilizadores autentem a autenticação contra a AD Azure caso o seu Ative Directory esteja indisponível.

![fluxo de sincronização de haxixe de palavra-palavra](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Para melhor compreender as suas opções de autenticação, consulte [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory](../hybrid/choose-ad-authn.md).

### <a name="programmatic-usage-of-credentials"></a>Uso programático de credenciais

Os scripts AD do AD que utilizam o PowerShell ou aplicações que utilizam a API do Gráfico microsoft requerem autenticação segura. A má gestão credencial que executa esses scripts e ferramentas aumenta o risco de roubo de credenciais. Se estiver a utilizar scripts ou aplicações que se baseiem em palavras-passe ou pedidos de palavra-passe codificados, deve primeiro rever as palavras-passe em ficheiros config ou código fonte, em seguida, substituir essas dependências e utilizar identidades geridas Integrated-Windows Azure, Integrated-Windows Autenticação ou [certificados](../reports-monitoring/tutorial-access-api-with-certificates.md) sempre que possível. Para aplicações em que as soluções anteriores não são possíveis, considere a utilização [do Cofre da Chave Azure](https://azure.microsoft.com/services/key-vault/).

Se determinar que existem princípios de serviço com credenciais de senha e não tem a certeza de como essas credenciais de senha são protegidas por scripts ou aplicações, contacte o proprietário da aplicação para entender melhor os padrões de utilização.

A Microsoft também recomenda que contacte os proprietários de aplicações para entender os padrões de utilização se existirem princípios de serviço com credenciais de senha.

## <a name="authentication-experience"></a>Experiência de autenticação

### <a name="on-premises-authentication"></a>Autenticação no local

A autenticação federada com autenticação integrada do Windows (IWA) ou a autenticação gerida sem emenda Sign-On (SSO) com sincronização de hash de palavra-passe ou autenticação pass-through é a melhor experiência do utilizador quando dentro da rede corporativa com controladores de domínio de linha de visão para as instalações. Minimiza a fadiga da origem da credencial e reduz o risco de os utilizadores serem vítimas de ataques de phishing. Se já estiver a utilizar a autenticação gerida pela nuvem com PHS ou PTA, mas os utilizadores ainda precisam de escrever a sua palavra-passe ao autenticar no local, então deve implementar imediatamente [o SSO sem emenda](../hybrid/how-to-connect-sso.md). Por outro lado, se atualmente está federado com planos para eventualmente migrar para a autenticação gerida pela nuvem, então deve implementar o SSO sem emenda como parte do projeto de migração.

### <a name="device-trust-access-policies"></a>Políticas de acesso de confiança do dispositivo

Como um utilizador na sua organização, um dispositivo é uma identidade central que quer proteger. Pode utilizar a identidade de um dispositivo para proteger os seus recursos a qualquer momento e a partir de qualquer local. A autenticação do dispositivo e a contabilização do seu tipo de confiança melhora a sua postura de segurança e usabilidade através de:

- Evitando o atrito, por exemplo, com o MFA, quando o dispositivo é confiável
- Bloquear o acesso a dispositivos não fidedqui os falsos
- Para dispositivos Windows 10, forneça [recursos únicos de iniciar s-on no local sem problemas.](../devices/azuread-join-sso.md)

Pode realizar este objetivo trazendo identidades do dispositivo e gerindo-as em Azure AD utilizando um dos seguintes métodos:

- As organizações podem usar o [Microsoft Intune](/intune/what-is-intune) para gerir o dispositivo e impor políticas de conformidade, atestar a saúde do dispositivo e definir políticas de acesso condicional com base no facto de o dispositivo estar em conformidade. O Microsoft Intune pode gerir dispositivos iOS, desktops Mac (via integração JAMF), desktops Windows (utilizando nativamente Mobile Device Management para Windows 10, e cogestão com o Microsoft Endpoint Configuration Manager) e dispositivos móveis Android.
- [A ad azure híbrida fornece](../devices/hybrid-azuread-join-managed-domains.md) gestão com políticas de grupo ou Microsoft Endpoint Configuration Manager em um ambiente com dispositivos de computadores ligados ao domínio do Ative Directory. As organizações podem implementar um ambiente gerido através de PHS ou PTA com SSO sem emenda. Trazer os seus dispositivos para a Azure AD maximiza a produtividade do utilizador através do SSO através da sua nuvem e recursos no local, permitindo-lhe garantir o acesso à sua nuvem e recursos no local com [Acesso Condicional](../conditional-access/overview.md) ao mesmo tempo.

Se tiver dispositivos Windows associados a domínios que não estejam registados na nuvem, ou dispositivos Windows associados a domínios que estejam registados na nuvem mas sem políticas de acesso condicional, então deve registar os dispositivos não registados e, em qualquer dos casos, [utilizar a AD Híbrida Azure como um controlo](../conditional-access/require-managed-devices.md) nas suas políticas de acesso condicional.

![Uma imagem de concessão na política de acesso condicional que requer dispositivo híbrido](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Se estiver a gerir dispositivos com MDM ou Microsoft Intune, mas não utilizar controlos de dispositivos nas suas políticas de acesso condicional, recomendamos que utilize [o dispositivo Require para ser marcado como um](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) controlo nessas políticas.

![Uma imagem de concessão na política de acesso condicional que exige a conformidade do dispositivo](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Políticas de acesso fidedição do dispositivo recomendadas leitura

- [Como: Planeie o seu Azure Ative Directory híbrido junte-se à implementação](../devices/hybrid-azuread-join-plan.md)
- [Configurações de acesso aos dispositivos e identidade](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello para empresas

No Windows 10, [o Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) substitui as palavras-passe por uma forte autenticação de dois fatores nos Computadores. O Windows Hello for Business permite uma experiência de MFA mais simplificada para os utilizadores e reduz a sua dependência de senhas. Se ainda não começou a lançar dispositivos Windows 10, ou apenas os implementou parcialmente, recomendamos que atualize para o Windows 10 e [permita o Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) em todos os dispositivos.

Se quiser saber mais sobre a autenticação sem palavras-passe, consulte [um mundo sem palavras-passe com o Azure Ative Directory](../authentication/concept-authentication-passwordless.md).

## <a name="application-authentication-and-assignment"></a>Autenticação e atribuição de candidaturas

### <a name="single-sign-on-for-apps"></a>Único s-on para apps

Fornecer um mecanismo de inscrição único padronizado a toda a empresa é crucial para a melhor experiência do utilizador, redução de risco, capacidade de reportar e governação. Se estiver a utilizar aplicações que suportam SSO com AZure AD mas que estão atualmente configuradas para utilizar contas locais, deve reconfigurar essas aplicações para utilizar SSO com Azure AD. Da mesma forma, se estiver a utilizar quaisquer aplicações que suportem SSO com Azure AD mas que estejam a utilizar outro Fornecedor de Identidade, deverá reconfigurar essas aplicações para utilizar o SSO com Azure AD também. Para aplicações que não suportam protocolos da federação mas que suportem a autenticação baseada em formulários, recomendamos que configures a aplicação para usar [o cofre de palavras-passe](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) com o Azure AD Application Proxy.

![Sinal de inscrição baseada em passwords do AppProxy](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Se não tiver um mecanismo para descobrir aplicações não geridos na sua organização, recomendamos implementar um processo de descoberta utilizando uma solução de corretor de segurança de acesso à nuvem (CASB), como [a Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Finalmente, se tiver uma galeria de aplicações AD Azure e utilizar aplicações que suportem SSO com AD AZure, recomendamos [a listagem da aplicação na galeria de aplicações.](../develop/v2-howto-app-gallery-listing.md)

#### <a name="single-sign-on-recommended-reading"></a>Leitura recomendada de inscrição única

- [O que é o acesso à aplicação e um único acesso com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migração de aplicações da AD FS para Azure AD

[A migração de aplicações de AD FS para Azure AD](../manage-apps/migrate-adfs-apps-to-azure.md) permite capacidades adicionais em segurança, gestão mais consistente e uma melhor experiência de colaboração. Se tiver aplicações configuradas em FS AD que suportam SSO com Ad AD, então deve reconfigurar essas aplicações para usar SSO com Azure AD. Se tiver aplicações configuradas em FS AD com configurações incomuns não suportadas pelo Azure AD, deve contactar os proprietários da aplicação para perceber se a configuração especial é um requisito absoluto da aplicação. Se não for necessário, então deve reconfigurar a aplicação para usar SSO com Azure AD.

![Azure AD como o fornecedor principal de identidade](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [O Azure AD Connect Health para ADFS](../hybrid/how-to-connect-health-adfs.md) pode ser usado para recolher detalhes de configuração sobre cada aplicação que pode potencialmente ser migrada para Azure AD.

### <a name="assign-users-to-applications"></a>Atribuir utilizadores a aplicações

[Atribuir os utilizadores às aplicações](../manage-apps/assign-user-or-group-access-portal.md) é melhor mapeado através da utilização de grupos, porque permitem uma maior flexibilidade e capacidade de gestão em escala. Os benefícios da utilização de grupos incluem [a adesão dinâmica baseada no atributo](../enterprise-users/groups-dynamic-membership.md) e [delegação aos proprietários de aplicações.](../fundamentals/active-directory-accessmanagement-managing-group-owners.md) Portanto, se já está a utilizar e a gerir grupos, recomendamos que tome as seguintes ações para melhorar a gestão em escala:

- Delegar gestão e governação de grupos aos proprietários de candidaturas.
- Permitir o acesso self-service à aplicação.
- Defina grupos dinâmicos se os atributos do utilizador puderem determinar consistentemente o acesso às aplicações.
- Implementar atestado a grupos utilizados para acesso a aplicações utilizando [revisões de acesso AZure AD](../governance/access-reviews-overview.md).

Por outro lado, se encontrar aplicações que tenham atribuição a utilizadores individuais, não se esqueça de implementar [a governação](../governance/index.yml) em torno dessas aplicações.

#### <a name="assign-users-to-applications-recommended-reading"></a>Atribuir utilizadores a aplicações recomendadas de leitura

- [Atribuir utilizadores e grupos a uma aplicação no Azure Ative Directory](../manage-apps/assign-user-or-group-access-portal.md)
- [Delegar permissões de registo de aplicativos no Azure Ative Directory](../roles/delegate-app-roles.md)
- [Regras dinâmicas de adesão para grupos no Azure Ative Directory](../enterprise-users/groups-dynamic-membership.md)

## <a name="access-policies"></a>Políticas de acesso

### <a name="named-locations"></a>Localizações com nome

Com [localizações nomeadas](../reports-monitoring/quickstart-configure-named-locations.md) em Azure AD, pode rotular gamas de endereços IP fidedignos na sua organização. O Microsoft Azure AD utiliza localizações com nome para:

- Prevenir falsos positivos em eventos de risco. A sessão a partir de uma localização de rede fidedigna reduz o risco de inscrição de um utilizador.
- Configure [o acesso condicional baseado na localização.](../reports-monitoring/quickstart-configure-named-locations.md)

![Localização com nome](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Com base na prioridade, utilize a tabela abaixo para encontrar a solução recomendada que melhor satisfaça as necessidades da sua organização:

| **Priority** | **Cenário** | **Recomendação** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Se você usar PHS ou PTA e locais nomeados não foram definidos | Definir localizações nomeadas para melhorar a deteção de eventos de risco |
| 2 | Se você é federado e não usar a reivindicação "insideCorporateNetwork" e locais nomeados não foram definidos | Definir localizações nomeadas para melhorar a deteção de eventos de risco |
| 3 | Se não utilizar localizações nomeadas em políticas de acesso condicional e não houver riscos ou controlos de dispositivos em políticas de acesso condicional | Configure a política de acesso condicional para incluir localizações nomeadas |
| 4 | Se você é federado e você usa a reivindicação "insideCorporateNetwork" e locais nomeados não foram definidos | Definir localizações nomeadas para melhorar a deteção de eventos de risco |
| 5 | Se estiver a usar endereços IP fidedignos com MFA em vez de localizações nomeadas e marcando-os como confiáveis | Defina localizações nomeadas e marque-as como confiáveis para melhorar a deteção de eventos de risco |

### <a name="risk-based-access-policies"></a>Políticas de acesso baseadas no risco

O Azure AD pode calcular o risco para cada sedura e cada utilizador. A utilização do risco como critério nas políticas de acesso pode proporcionar uma melhor experiência do utilizador, por exemplo, menos pedidos de autenticação e uma melhor segurança, por exemplo, apenas levar os utilizadores quando são necessários, e automatizar a resposta e a remediação.

![Política de risco do início de sessão](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Se já possui licenças Azure AD Premium P2 que suportam o uso de riscos nas políticas de acesso, mas não estão a ser utilizadas, recomendamos vivamente adicionar riscos à sua postura de segurança.

#### <a name="risk-based-access-policies-recommended-reading"></a>Políticas de acesso baseadas no risco recomendadas leitura

- [Como: Configurar a política de risco de inscrição](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Como: Configurar a política de risco do utilizador](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Políticas de acesso a aplicações ao cliente

A Microsoft Intune Application Management (MAM) fornece a capacidade de pressionar controlos de proteção de dados, tais como encriptação de armazenamento, PIN, limpeza remota de armazenamento, etc. para aplicações móveis compatíveis com clientes, como o Outlook Mobile. Além disso, podem ser criadas políticas de acesso condicional para [restringir o acesso](../conditional-access/app-based-conditional-access.md) a serviços na nuvem, como o Exchange Online, a partir de aplicações aprovadas ou compatíveis.

Se os seus colaboradores instalarem aplicações capazes de MAM, como aplicações móveis do Office, acederem a recursos corporativos como o Exchange Online ou SharePoint Online, e também apoiarem o BYOD (tragam o seu próprio dispositivo), recomendamos que implemente políticas de aplicação MAM para gerir a configuração da aplicação em dispositivos de propriedade pessoal sem a inscrição de MDM e, em seguida, atualize as suas políticas de acesso condicional para permitir apenas o acesso a clientes capazes de MAM.

![Controlo de concessão de acesso condicional](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Se os colaboradores instalarem aplicações capazes de MAM contra recursos corporativos e o acesso for restrito em dispositivos Geridos Intune, então deve considerar a implementação de políticas de MAM para gerir a configuração da aplicação para dispositivos pessoais e atualizar políticas de Acesso Condicional apenas para permitir o acesso a clientes capazes de MAM.

### <a name="conditional-access-implementation"></a>Implementação do Acesso Condicional

O Acesso Condicional é uma ferramenta essencial para melhorar a postura de segurança da sua organização. Por isso, é importante que siga estas boas práticas:

- Certifique-se de que todas as aplicações saaS têm pelo menos uma política aplicada
- Evite combinar o filtro **All apps** com o controlo de **blocos** para evitar o risco de bloqueio
- Evite utilizar os **Todos os utilizadores** como filtro e adicionar inadvertidamente **os Hóspedes**
- **Migrar todas as políticas "legados" para o portal Azure**
- Apanhe todos os critérios para utilizadores, dispositivos e aplicações
- Utilizar políticas de acesso condicional para [implementar MFA,](../conditional-access/plan-conditional-access.md)em vez de utilizar um **MFA por utilizador**
- Ter um pequeno conjunto de políticas fundamentais que podem aplicar-se a múltiplas aplicações
- Defina grupos de exceção vazios e adicione-os às políticas para ter uma estratégia de exceção
- Plano para quebrar contas [de vidro](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency) sem controlos de MFA
- Garantir uma experiência consistente em aplicações de clientes Microsoft 365, por exemplo, Equipas, OneDrive, Outlook, etc.) implementando o mesmo conjunto de controlos para serviços como Exchange Online e Sharepoint Online
- A atribuição às políticas deve ser implementada através de grupos, não de indivíduos
- Faça revisões regulares dos grupos de exceção utilizados nas políticas para limitar o tempo que os utilizadores estão fora da postura de segurança. Se possuir Azure AD P2, então pode usar comentários de acesso para automatizar o processo

#### <a name="conditional-access-recommended-reading"></a>Leitura recomendada de acesso condicional

- [Melhores práticas para acesso condicional no Diretório Ativo Azure](../conditional-access/overview.md)
- [Configurações de acesso aos dispositivos e identidade](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Referência de definições de acesso condicional do Diretório Ativo Azure](../conditional-access/concept-conditional-access-conditions.md)
- [Políticas de Acesso Condicional comuns](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>Área de superfície de acesso

### <a name="legacy-authentication"></a>Autenticação do legado

Credenciais fortes como mFA não podem proteger aplicações usando protocolos de autenticação legado, que fazem dele o vetor de ataque preferido por atores mal-intencionados. Bloquear a autenticação do legado é crucial para melhorar a postura de segurança de acesso.

A autenticação do legado é um termo que se refere a protocolos de autenticação utilizados por apps como:

- Clientes do Escritório mais antigos que não usam a autenticação moderna (por exemplo, cliente do Office 2010)
- Clientes que utilizam protocolos de correio como IMAP/SMTP/POP

Os atacantes preferem fortemente estes protocolos - na verdade, quase [100% dos ataques de spray de senha](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) usam protocolos de autenticação legado! Os hackers usam protocolos de autenticação antigas, porque não suportam a inscrição interativa, o que é necessário para desafios adicionais de segurança como a autenticação de vários fatores e a autenticação do dispositivo.

Se a autenticação de legados for amplamente utilizada no seu ambiente, deverá planear migrar clientes legados para clientes que suportem a [autenticação moderna o](/office365/enterprise/modern-auth-for-office-2013-and-2016) mais rapidamente possível. No mesmo tom, se já tem alguns utilizadores que já utilizam a autenticação moderna mas outros que ainda utilizam a autenticação antiga, deverá tomar as seguintes medidas para bloquear clientes de autenticação legado:

1. Utilize [relatórios de Atividade de Inscrição](../reports-monitoring/concept-sign-ins.md) para identificar utilizadores que ainda utilizam a autenticação do legado e planeiam a reparação:

   a. Upgrade para clientes capazes de autenticação moderna para utilizadores afetados.
   
   b. Planeie um prazo de corte para bloquear por degraus abaixo.
   
   c. Identifique quais aplicações antigas têm uma dependência difícil da autenticação do legado. Veja o passo 3 abaixo.

2. Desative os protocolos legados na fonte (por exemplo, Exchange Mailbox) para utilizadores que não estão a usar o legado para evitar mais exposição.
3. Para as restantes contas (idealmente identidades não-humanas, como contas de serviço), utilize [o acesso condicional para restringir os protocolos legados](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) pós-autenticação.

#### <a name="legacy-authentication-recommended-reading"></a>Leitura recomendada de autenticação do legado

- [Ativar ou desativar o acesso POP3 ou IMAP4 a caixas de correio no Servidor de Câmbio](/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>Subsídios de consentimento

Num ataque de concessão de consentimento ilícito, o intruso cria uma aplicação registada em Azure que solicita o acesso a dados como informações de contacto, e-mail ou documentos. Os utilizadores podem estar a conceder consentimento a aplicações maliciosas através de ataques de phishing ao aterrarem em sites fraudulentos.

Abaixo está uma lista de aplicações com permissões que você pode querer examinar para os serviços na nuvem da Microsoft:

- Aplicativos com app ou \* delegados. Permissões ReadWrite
- Aplicações com permissões delegadas podem ler, enviar ou gerir e-mail em nome do utilizador
- Aplicativos a quem é concedida a utilização das seguintes permissões:

| Recurso | Permissão |
| :- | :- |
| Exchange Online | EAS. AccessAsUser.All |
| | EWS. AccessAsUser.All |
| | Mail.Read |
| Microsoft Graph API | Mail.Read |
| | Mail.Read.Shared |
| | E-mail.ReadWrite |

- As aplicações concederam a personificação completa do utilizador do utilizador inscrito. Por exemplo:

|Recurso | Permissão |
| :- | :- |
| Microsoft Graph API| Diretório.AccessAsUser.All |
| API REST do Azure | user_impersonation |

Para evitar este cenário, deverá consultar [e remediar as subvenções de consentimento ilícitas no Office 365](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) para identificar e corrigir quaisquer pedidos com subvenções ilícitas ou pedidos que tenham mais subvenções do que o necessário. Em seguida, [remova completamente o autosserviço](../manage-apps/configure-user-consent.md) e [estabeleça procedimentos de governação.](../manage-apps/configure-admin-consent-workflow.md) Por fim, agende as análises regulares das permissões de apps e remova-as quando não forem necessárias.

#### <a name="consent-grants-recommended-reading"></a>Concessões de consentimento recomendadas leitura

- [Permissões API do Microsoft Graph](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Configurações de utilizador e grupo

Abaixo estão as configurações do utilizador e do grupo que podem ser bloqueadas se não houver uma necessidade explícita do negócio:

#### <a name="user-settings"></a>Definições do utilizador

- **Utilizadores Externos** - a colaboração externa pode acontecer organicamente na empresa com serviços como Equipas, Power BI, Sharepoint Online e Azure Information Protection. Se tiver restrições explícitas para controlar a colaboração externa iniciada pelo utilizador, recomenda-se que ative utilizadores externos utilizando [a gestão do Direito AD Azure](../governance/entitlement-management-overview.md) ou uma operação controlada, como através do seu balcão de ajuda. Se não quiser permitir a colaboração externa orgânica para serviços, pode impedir os [membros de convidarem completamente utilizadores externos.](../external-identities/delegate-invitations.md) Em alternativa, também pode [permitir ou bloquear domínios específicos](../external-identities/allow-deny-list.md) em convites externos do utilizador.
- **Registos de aplicações** - quando os registos da App estiverem ativados, os utilizadores finais podem embarcar aplicações e conceder acesso aos seus dados. Um exemplo típico do registo de Aplicações são os utilizadores que permitem que os plug-ins do Outlook, ou assistentes de voz como a Alexa e a Siri, leiam os seus emails e calendários ou enviem e-mails em seu nome. Se o cliente decidir desligar o registo da App, as equipas InfoSec e IAM devem estar envolvidas na gestão de exceções (registos de aplicações que são necessários com base nos requisitos do negócio), uma vez que teriam de registar as aplicações com uma conta de administração, e muito provavelmente requerem a conceção de um processo para operacionalizar o processo.
- **Portal da Administração** - as organizações podem bloquear a lâmina AZure AD no portal Azure para que os não administradores não possam aceder à gestão AD do Azure no portal Azure e ficar confusos. Aceda às definições do utilizador no portal de gestão Azure AD para restringir o acesso:

![Portal da administração restrito acesso](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Os não administradores ainda podem aceder às interfaces de gestão AZure AD através da linha de comando e de outras interfaces programáticas.

#### <a name="group-settings"></a>Definições de grupo

**Gestão do Grupo self-Service / Os utilizadores podem criar grupos de Segurança / Microsoft 365.** Se não houver uma iniciativa de autosserviço atual para grupos na nuvem, os clientes podem decidir desligá-la até estarem prontos a usar esta capacidade.

#### <a name="groups-recommended-reading"></a>Grupos recomendados de leitura

- [O que é a colaboração B2B do Azure Active Directory?](../external-identities/what-is-b2b.md)
- [Integração de Aplicações com Diretório Ativo Azure](../develop/quickstart-register-app.md)
- [Aplicativos, permissões e consentimento no Azure Ative Directory.](../develop/quickstart-register-app.md)
- [Utilize grupos para gerir o acesso a recursos no Azure Ative Directory](./active-directory-manage-groups.md)
- [Criação de gestão de acesso a aplicações self-service no Azure Ative Directory](../enterprise-users/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>Tráfego de locais inesperados

Os agressores são originários de várias partes do mundo. Gerencie este risco utilizando políticas de acesso condicional com a localização como condição. A condição de [localização](../conditional-access/location-condition.md) de uma política de Acesso Condicional permite-lhe bloquear o acesso a locais de onde não há razão comercial para iniciar sedu.

![Criar um novo local nomeado](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Se disponível, utilize uma solução de informação de segurança e gestão de eventos (SIEM) para analisar e encontrar padrões de acesso entre regiões. Se não utilizar um produto SIEM, ou se não estiver a ingerir informações de autenticação a partir da Azure AD, recomendamos que utilize [o Azure Monitor](../../azure-monitor/overview.md) para identificar padrões de acesso em todas as regiões.

## <a name="access-usage"></a>Utilização de acesso

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Registos AD AD arquivados e integrados com planos de resposta a incidentes

Ter acesso a atividade de inscrição, auditorias e eventos de risco para a Azure AD é crucial para a resolução de problemas, análises de uso e investigações forenses. A Azure AD fornece acesso a estas fontes através de APIs REST que têm um período de retenção limitado. Um sistema de informação de segurança e gestão de eventos (SIEM), ou tecnologia de arquivo equivalente, é fundamental para o armazenamento a longo prazo de auditorias e suporte. Para permitir o armazenamento a longo prazo dos Registos AD Azure, deve adicioná-los à sua solução SIEM existente ou utilizar [o Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md). Registos de arquivo que podem ser usados como parte dos seus planos de resposta a incidentes e investigações.

#### <a name="logs-recommended-reading"></a>Registos recomendados de leitura

- [Referência de Azure Ative Directory API](/graph/api/resources/directoryaudit?view=graph-rest-beta%3fview%3dgraph-rest-beta)
- [Azure Ative Directory relatório de atividades API referência](/graph/api/resources/signin?view=graph-rest-beta%3fview%3dgraph-rest-beta)
- [Utilizar a API de Relatórios do Azure AD com certificados para obter dados](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Microsoft Graph para Azure Ative Directory Identity Protection](../identity-protection/howto-identity-protection-graph-api.md)
- [Referência API de Atividade de Gestão 365](/office/office-365-management-api/office-365-management-activity-api-reference)
- [Como utilizar o Pacote de Conteúdos do Power BI para o Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Resumo

Há 12 aspetos para uma infraestrutura de identidade segura. Esta lista irá ajudá-lo a proteger e gerir ainda mais as credenciais, definir experiência de autenticação, delegar atribuição, medir o uso e definir políticas de acesso baseadas na postura de segurança da empresa.

- Atribua os proprietários a tarefas-chave.
- Implementar soluções para detetar senhas fracas ou vazadas, melhorar a gestão e proteção de passwords e garantir ainda mais o acesso dos utilizadores aos recursos.
- Gerencie a identidade dos dispositivos para proteger os seus recursos a qualquer momento e a partir de qualquer local.
- Implementar a autenticação sem palavras-passe.
- Forneça um mecanismo de inscrição único padronizado em toda a organização.
- Migrar aplicativos de AD FS para Azure AD para permitir uma melhor segurança e uma gestão mais consistente.
- Atribua os utilizadores às aplicações utilizando grupos para permitir uma maior flexibilidade e capacidade de gestão em escala.
- Configure políticas de acesso baseadas no risco.
- Bloqueie os protocolos de autenticação do legado.
- Detetar e remediar subsídios de consentimento ilícitos.
- Bloqueie as definições do utilizador e do grupo.
- Permitir armazenamento a longo prazo de registos AZure AD para resolução de problemas, análises de uso e investigações forenses.

## <a name="next-steps"></a>Passos seguintes

Começar com os [controlos operacionais e ações operacionais da governação da identidade.](active-directory-ops-guide-govern.md)