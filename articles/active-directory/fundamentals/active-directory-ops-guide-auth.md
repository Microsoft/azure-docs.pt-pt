---
title: Guia de referência de operações de gerenciamento de autenticação Azure Active Directory
description: Este guia de referência de operações descreve as verificações e ações que você deve seguir para proteger o gerenciamento de autenticação
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
ms.openlocfilehash: bc5824fcb62477d4e6dc6c2b7390b1bfa916094f
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368051"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Guia de referência de operações de gerenciamento de autenticação Azure Active Directory

Esta secção do guia de referência de operações da [AD Azure](active-directory-ops-guide-intro.md) descreve as verificações e ações que deve tomar para garantir e gerir credenciais, definir experiência de autenticação, atribuição de delegados, medir o uso e definir políticas de acesso baseadas na postura de segurança da empresa.

> [!NOTE]
> Estas recomendações estão em vigor a partir da data da publicação, mas podem mudar ao longo do tempo. As organizações devem avaliar continuamente suas práticas de identidade à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir proprietários a tarefas-chave

A gestão do Diretório Ativo azure requer a execução contínua de tarefas e processos operacionais fundamentais, que podem não fazer parte de um projeto de lançamento. Ainda é importante que você configuraestas tarefas para otimizar o seu ambiente. As tarefas-chave e os seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Gerenciar o ciclo de vida da configuração de SSO (logon único) no Azure AD | Equipa de Operações do IAM |
| Criar políticas de acesso condicional para aplicativos do Azure AD | Equipe de arquitetura do batalha |
| Arquivar atividade de entrada em um sistema SIEM | Equipa de Operações infosec |
| Arquivar eventos de risco em um sistema SIEM | Equipa de Operações infosec |
| Fazer triagem e investigar relatórios de segurança | Equipa de Operações infosec |
| Fazer triagem e investigar eventos de risco | Equipa de Operações infosec |
| Fazer triagem e investigar os usuários sinalizados para os relatórios de risco e vulnerabilidade de Azure AD Identity Protection | Equipa de Operações infosec |

> [!NOTE]
> Azure AD Identity Protection requer uma licença do Azure AD Premium P2. Para encontrar a licença certa para os seus requisitos, consulte [Comparar as funcionalidades geralmente disponíveis das edições Azure AD Free e Azure AD Premium.](https://azure.microsoft.com/pricing/details/active-directory/)

Ao rever a sua lista, poderá descobrir que precisa de atribuir um proprietário a tarefas que faltam a um proprietário ou ajustar a propriedade para tarefas com proprietários que não estejam alinhados com as recomendações acima.

#### <a name="owner-recommended-reading"></a>Leitura recomendada pelo proprietário

- [Atribuir funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governação no Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>Gestão de credenciais

### <a name="password-policies"></a>Políticas de palavra-passe

O gerenciamento de senhas com segurança é uma das partes mais importantes do gerenciamento de identidade e acesso e, muitas vezes, da maior meta de ataques. O Azure AD dá suporte a vários recursos que podem ajudar a impedir que um ataque seja bem-sucedido.

Use a tabela a seguir para encontrar a solução recomendada para atenuar o problema que precisa ser resolvido:

| Problema | Recomendação |
| :- | :- |
| Nenhum mecanismo para proteção contra senhas fracas | Ativar o reset de [palavra-passe autosserviço](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) Azure AD (SSPR) e [a proteção de passwords](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) |
| Nenhum mecanismo para detectar senhas vazadas | Ativar a sincronização de [hash](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS) de senha para obter insights |
| Usando AD FS e não é possível mover para a autenticação gerenciada | Ativar [o Bloqueio Inteligente Smart Lockout](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) e /ou [Azure AD Smart Lockout](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) |
| A política de senha usa regras baseadas em complexidade, como comprimento, vários conjuntos de caracteres ou expiração | Reconsidere a favor das [Práticas Recomendadas](https://aka.ms/passwordguidance) da Microsoft e mude a sua abordagem para a gestão de passwords e implemente a proteção de [passwords Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad). |
| Os usuários não estão registrados para usar a MFA (autenticação multifator) | [Registe todas as informações de segurança do utilizador](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy) para que possa ser utilizada como um mecanismo para verificar a identidade do utilizador juntamente com a sua palavra-passe |
| Não há revogação de senhas com base no risco do usuário | Implementar políticas de risco de utilizador de [proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy) Azure AD para forçar alterações de senha em credenciais vazadas usando SSPR |
| Não há nenhum mecanismo de bloqueio inteligente para proteger a autenticação mal-intencionada de atores ruins provenientes de endereços IP identificados | Implementar autenticação gerida pela nuvem com sincronização de hash de palavra-passe ou [autenticação pass-through](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Leitura recomendada de políticas de senha

- [As melhores práticas da Azure AD e da AD FS: Defender contra ataques de spray de senha - Mobilidade Empresarial + Segurança](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Habilitar redefinição de senha de autoatendimento e proteção por senha

Os usuários que precisam alterar ou redefinir suas senhas são uma das maiores fontes de volume e custo de chamadas de assistência técnica. Além do custo, alterar a senha como uma ferramenta para mitigar um risco de usuário é uma etapa fundamental para melhorar a postura de segurança de sua organização.

No mínimo, recomenda-se que implemente o reset de senha de [autosserviço](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) Azure AD (SSPR) e a proteção de [senhas](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) no local para realizar:

- Chamadas de suporte técnico de deflexão.
- Substitua o uso de senhas temporárias.
- Substitua qualquer solução de gerenciamento de senha de autoatendimento existente que dependa de uma solução local.
- [Elimine senhas fracas](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) na sua organização.

> [!NOTE]
> Para organizações com uma subscrição Azure AD Premium P2, recomenda-se a implementação de SSPR e usá-lo como parte de uma Política de Risco de Utilizador de [Proteção de Identidade.](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)

### <a name="strong-credential-management"></a>Gerenciamento de credenciais forte

As senhas por si só não são seguras o suficiente para evitar que atores ruins obtenham acesso ao seu ambiente. No mínimo, qualquer usuário com uma conta privilegiada deve ser habilitado para a autenticação multifator (MFA). Idealmente, deve ativar o [registo combinado](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) e exigir que todos os utilizadores se registem para MFA e SSPR utilizando a experiência de [registo combinado.](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview) Eventualmente, recomendamos que adote uma estratégia para [fornecer resiliência](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) para reduzir o risco de bloqueio devido a circunstâncias imprevistas.

![Fluxo de experiência do usuário combinado](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Resiliência local de autenticação de falha

Além dos benefícios da simplicidade e da deteção de credenciais vazadas, o Azure AD Password Hash Sync (PHS) e o Azure MFA permitem aos utilizadores aceder às aplicações SaaS e ao Office 365, apesar das interrupções no local devido a ciberataques como o [NotPetya.](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/) Também é possível habilitar o PHS enquanto estiver em conjunto com a Federação. Habilitar o PHS permite o fallback de autenticação quando os serviços de Federação não estão disponíveis.

Se sua organização local não estiver em uma estratégia de resiliência de interrupção ou se tiver uma que não esteja integrada ao Azure AD, você deverá implantar o Azure AD PHS e definir um plano de recuperação de desastres que inclua o PHS. Habilitar o Azure AD PHS permitirá que os usuários se autentiquem no Azure AD se seu local Active Directory estiver indisponível.

![fluxo de sincronização de hash de senha](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Para melhor compreender as suas opções de autenticação, consulte [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).

### <a name="programmatic-usage-of-credentials"></a>Uso programático de credenciais

Os scripts Azure AD utilizando o PowerShell ou aplicações que utilizam a Microsoft Graph API requerem autenticação segura. O gerenciamento de credenciais deficiente executando esses scripts e ferramentas aumenta o risco de roubo de credenciais. Se estiver a utilizar scripts ou aplicações que dependam de senhas ou instruções de senha sinuosas codificadas, deve primeiro rever as palavras-passe em ficheiros config ou código fonte, em seguida, substituir essas dependências e utilizar identidades geridas pelo Azure, autenticação integrada do Windows ou [certificados](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-access-api-with-certificates) sempre que possível. Para aplicações onde as soluções anteriores não são possíveis, considere utilizar o [Cofre de Chaves Azure](https://azure.microsoft.com/services/key-vault/).

Se você determinar que há entidades de serviço com credenciais de senha e não tiver certeza de como essas credenciais de senha são protegidas por scripts ou aplicativos, entre em contato com o proprietário do aplicativo para entender melhor os padrões de uso.

A Microsoft também recomenda que você entre em contato com os proprietários do aplicativo para entender os padrões de uso se houver entidades de serviço com credenciais de senha.

## <a name="authentication-experience"></a>Experiência de autenticação

### <a name="on-premises-authentication"></a>Autenticação local

A autenticação federada com autenticação integrada do Windows (IWA) ou autenticação gerenciada de SSO (logon único) contínuo com sincronização de hash de senha ou autenticação de passagem é a melhor experiência de usuário quando dentro da rede corporativa com linha de visão para controladores de domínio locais. Ele minimiza a solicitação de credencial fadiga e reduz o risco de os usuários se depararem com ataques de phishing. Se já estiver a utilizar a autenticação gerida pela nuvem com PHS ou PTA, mas os utilizadores ainda precisam de escrever a sua palavra-passe ao autenticar no local, então deve implementar imediatamente [o SSO SSO sem emenda](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso). Por outro lado, se você estiver atualmente federado com planos para eventualmente migrar para a autenticação gerenciada pela nuvem, deverá implementar o SSO contínuo como parte do projeto de migração.

### <a name="device-trust-access-policies"></a>Políticas de acesso de confiança do dispositivo

Tal como um utilizador na sua organização, um dispositivo é uma identidade central que pretende proteger. Pode usar a identidade de um dispositivo para proteger os seus recursos a qualquer momento e de qualquer local. A autenticação do dispositivo e a contabilização de seu tipo de confiança aumenta sua postura e usabilidade de segurança:

- Evitando o conflito, por exemplo, com a MFA, quando o dispositivo é confiável
- Bloqueando o acesso de dispositivos não confiáveis
- Para dispositivos Windows 10, forneça [um único sinal aos recursos no local sem problemas.](https://docs.microsoft.com/azure/active-directory/devices/azuread-join-sso)

Você pode realizar essa meta colocando as identidades do dispositivo e gerenciando-as no Azure AD usando um dos seguintes métodos:

- As organizações podem usar o [Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune) para gerir o dispositivo e impor as políticas de conformidade, atestar a saúde do dispositivo e definir políticas de acesso condicional com base no cumprimento do dispositivo. Microsoft Intune pode gerenciar dispositivos iOS, desktops Mac (via integração do JAMF), áreas de trabalho do Windows (usando nativamente o gerenciamento de dispositivos móveis para Windows 10 e o cogerenciamento com o Microsoft Endpoint Configuration Manager) e dispositivos móveis Android.
- [A adesão da Hybrid Azure AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-managed-domains) fornece gestão com políticas de grupo ou Microsoft Endpoint Configuration Manager num ambiente com dispositivos de computadores ligados ao domínio Ative Directory. As organizações podem implantar um ambiente gerenciado por meio de PHS ou PTA com SSO contínuo. Trazer os seus dispositivos para o Azure AD maximiza a produtividade dos utilizadores através do SSO através da sua nuvem e recursos no local, permitindo-lhe garantir o acesso à sua nuvem e recursos no local com de [Acesso Condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) ao mesmo tempo.

Se tiver dispositivos Windows unidos por domínio sem registo na nuvem, ou dispositivos Windows unidos por domínio que estejam registados na nuvem mas sem políticas de acesso condicional, então deve registar os dispositivos não registados e, em qualquer dos casos, utilizar o [Hybrid Azure AD como controlo](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) nas suas políticas de acesso condicional.

![Uma captura de tela de concessão na política de acesso condicional que requer dispositivo híbrido](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Se estiver a gerir dispositivos com MDM ou Microsoft Intune, mas não utilizar controlos de dispositivos nas suas políticas de acesso condicional, recomendamos que a utilização do [dispositivo Requires seja marcada como conforme](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices#require-device-to-be-marked-as-compliant) como um controlo nessas políticas.

![Uma captura de tela de concessão na política de acesso condicional que requer conformidade do dispositivo](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Leitura recomendada de políticas de acesso de confiança de dispositivo

- [Como: Planeie a sua direção ativa híbrida Azure aderir à implementação](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
- [Configurações de acesso a identidade e dispositivo](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello para empresas

No Windows 10, [o Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) substitui senhas por autenticação forte de dois fatores em PCs. O Windows Hello para empresas permite uma experiência de MFA mais simplificada para os usuários e reduz sua dependência de senhas. Se ainda não começou a lançar dispositivos Windows 10 ou apenas os implementou parcialmente, recomendamos que atualize para o Windows 10 e ative o [Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) em todos os dispositivos.

Se quiser saber mais sobre autenticação sem palavras-passe, consulte [Um mundo sem senhas com Diretório Ativo Azure](https://aka.ms/passwordlessdoc).

## <a name="application-authentication-and-assignment"></a>Autenticação e atribuição de aplicativos

### <a name="single-sign-on-for-apps"></a>Logon único para aplicativos

Fornecer um mecanismo de logon único padronizado para toda a empresa é crucial para melhor experiência do usuário, redução de risco, capacidade de relatório e governança. Se você estiver usando aplicativos que dão suporte ao SSO com o Azure AD, mas estiverem atualmente configurados para usar contas locais, você deverá reconfigurar esses aplicativos para usar o SSO com o Azure AD. Da mesma forma, se você estiver usando qualquer aplicativo que ofereça suporte a SSO com o Azure AD, mas estiver usando outro provedor de identidade, deverá reconfigurar esses aplicativos para usar o SSO com o Azure AD também. Para aplicações que não suportam protocolos da federação mas que suportem a autenticação baseada em formulários, recomendamos que configure a aplicação para usar [abóbada](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting) de senha com procuração de aplicação ad.

![Logon baseado em senha do AppProxy](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Se não tiver um mecanismo para descobrir aplicações não geridas na sua organização, recomendamos implementar um processo de descoberta utilizando uma solução de corretor de segurança de acesso à nuvem (CASB) como o [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Finalmente, se tiver uma galeria de aplicações Azure AD e utilizar aplicações que suportem sSO com AD Azure, recomendamos a [listagem da aplicação na galeria de aplicações.](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)

#### <a name="single-sign-on-recommended-reading"></a>Leitura recomendada de logon único

- [O que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migração de aplicativos de AD FS para o Azure AD

[As aplicações migratórias de AD FS para Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) permitem capacidades adicionais em segurança, gestão mais consistente e uma melhor experiência de colaboração. Se você tiver aplicativos configurados em AD FS que dão suporte a SSO com o Azure AD, você deverá reconfigurar esses aplicativos para usar o SSO com o Azure AD. Se você tiver aplicativos configurados em AD FS com configurações incomuns sem suporte pelo Azure AD, entre em contato com os proprietários do aplicativo para entender se a configuração especial é um requisito absoluto do aplicativo. Se não for necessário, você deverá reconfigurar o aplicativo para usar o SSO com o Azure AD.

![Azure AD como o provedor de identidade principal](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> A [Azure AD Connect Health for ADFS](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs) pode ser usada para recolher detalhes de configuração sobre cada aplicação que pode potencialmente ser migrada para AD Azure.

### <a name="assign-users-to-applications"></a>Atribuir usuários a aplicativos

[Atribuir utilizadores a aplicações](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) é melhor mapeado utilizando grupos porque permitem uma maior flexibilidade e capacidade de gestão em escala. Os benefícios da utilização de grupos incluem [a adesão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) de grupodinâmico baseado em atributos e delegação aos proprietários de [aplicações.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-accessmanagement-managing-group-owners) Portanto, se você já estiver usando e gerenciando grupos, recomendamos que execute as seguintes ações para melhorar o gerenciamento em escala:

- Delegue gerenciamento e governança de grupo para proprietários de aplicativos.
- Permitir acesso de autoatendimento ao aplicativo.
- Defina grupos dinâmicos se os atributos de usuário puderem determinar o acesso aos aplicativos de forma consistente.
- Implementar o atestado a grupos utilizados para o acesso à aplicação utilizando avaliações de [acesso a AD Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

Por outro lado, se encontrar aplicações que tenham atribuição a utilizadores individuais, certifique-se de implementar a [governação](https://docs.microsoft.com/azure/active-directory/governance/index) em torno dessas aplicações.

#### <a name="assign-users-to-applications-recommended-reading"></a>Atribuir usuários a aplicativos de leitura recomendada

- [Atribuir utilizadores e grupos a uma aplicação no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [Delegar permissões de registo de apps no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Regras dinâmicas de adesão para grupos no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>Políticas de acesso

### <a name="named-locations"></a>Locais nomeados

Com [localizações nomeadas](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) em Azure AD, pode rotular gamas de endereços IP fidedignos na sua organização. O Microsoft Azure AD utiliza localizações com nome para:

- Evite falsos positivos em eventos de risco. Entrar de um local de rede confiável reduz o risco de entrada de um usuário.
- Configure [o acesso condicional baseado na localização.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

![Localização com nome](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Com base na prioridade, use a tabela abaixo para encontrar a solução recomendada que melhor atenda às necessidades da sua organização:

| **Prioridade** | **Cenário** | **Recomendação** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Se você usar PHS ou PTA e locais nomeados não tiverem sido definidos | Definir locais nomeados para melhorar a detecção de eventos de risco |
| 2 | Se você for federado e não usar a declaração "insideCorporateNetwork" e os locais nomeados não tiverem sido definidos | Definir locais nomeados para melhorar a detecção de eventos de risco |
| 3 | Se você não usar locais nomeados em políticas de acesso condicional e não houver nenhum controle de risco ou de dispositivo nas políticas de acesso condicional | Configurar a política de acesso condicional para incluir locais nomeados |
| 4 | Se você for federado e usar a declaração "insideCorporateNetwork" e os locais nomeados não tiverem sido definidos | Definir locais nomeados para melhorar a detecção de eventos de risco |
| 5 | Se você estiver usando endereços IP confiáveis com MFA em vez de locais nomeados e marcá-los como confiáveis | Definir locais nomeados e marcá-los como confiáveis para melhorar a detecção de eventos de risco |

### <a name="risk-based-access-policies"></a>Políticas de acesso com base em risco

O Azure AD pode calcular o risco para cada entrada e todos os usuários. O uso de riscos como um critério nas políticas de acesso pode proporcionar uma melhor experiência do usuário, por exemplo, menos prompts de autenticação e maior segurança, por exemplo, apenas solicitações de usuários quando forem necessárias e automatizar a resposta e a correção.

![Política de risco de inscrição](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Se você já tem Azure AD Premium licenças P2 que dão suporte ao uso de risco em políticas de acesso, mas elas não estão sendo usadas, é altamente recomendável adicionar risco à sua postura de segurança.

#### <a name="risk-based-access-policies-recommended-reading"></a>Leitura recomendada de políticas de acesso baseado em risco

- [Como: Configurar a política de risco de entrada](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)
- [Como: Configurar a política de risco do utilizador](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)

### <a name="client-application-access-policies"></a>Políticas de acesso do aplicativo cliente

O gerenciamento de aplicativo Microsoft Intune (MAM) fornece a capacidade de enviar por push controles de proteção de dados, como criptografia de armazenamento, PIN, limpeza de armazenamento remoto, etc., para aplicativos móveis de cliente compatíveis, como o Outlook Mobile. Além disso, podem ser criadas políticas de acesso condicional para restringir o [acesso](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) a serviços na nuvem, como o Exchange Online, a partir de aplicações aprovadas ou compatíveis.

Se seus funcionários instalarem aplicativos compatíveis com MAM, como aplicativos móveis do Office, para acessar recursos corporativos, como o Exchange Online ou o SharePoint Online, e você também oferecer suporte a BYOD (Traga seu próprio dispositivo), recomendamos que você implante as políticas de MAM do aplicativo para gerenciar a configuração de aplicativo em dispositivos de propriedade pessoal sem registro de MDM e, em seguida, atualiza suas políticas de acesso condicional para permitir o acesso somente de clientes compatíveis com MAM.

![Controle de concessão de acesso condicional](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Se os funcionários instalarem aplicativos compatíveis com MAM em relação aos recursos corporativos e o acesso for restrito em dispositivos gerenciados pelo Intune, você deverá considerar a implantação de políticas de MAM do aplicativo para gerenciar a configuração do aplicativo para dispositivos pessoais e Atualize as políticas de acesso condicional para permitir o acesso somente de clientes compatíveis com MAM.

### <a name="conditional-access-implementation"></a>Implementação de acesso condicional

O acesso condicional é uma ferramenta essencial para melhorar a postura de segurança de sua organização. Portanto, é importante seguir estas práticas recomendadas:

- Verifique se todos os aplicativos SaaS têm pelo menos uma política aplicada
- Evite combinar o filtro **Todas as aplicações** com o controlo do **bloco** para evitar o risco de bloqueio
- Evite utilizar os **utilizadores como** filtro e adicionar inadvertidamente **os Hóspedes**
- **Migrar todas as políticas de "legado" para o portal Azure**
- Capturar todos os critérios para usuários, dispositivos e aplicativos
- Utilizar políticas de acesso condicional para [implementar MFA](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access), em vez de utilizar um **MFA por utilizador**
- Ter um pequeno conjunto de políticas de núcleo que podem ser aplicadas a vários aplicativos
- Definir grupos de exceções vazios e adicioná-los às políticas para ter uma estratégia de exceção
- Plano para quebrar contas [de vidro](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency) sem controlos de MFA
- Garanta uma experiência consistente nos aplicativos cliente do Office 365, por exemplo, equipes, OneDrive for Business, Outlook, etc.) Implementando o mesmo conjunto de controles para serviços como o Exchange Online e o SharePoint Online
- A atribuição às políticas deve ser implementada por meio de grupos, não por indivíduos
- Faça revisões regulares dos grupos de exceções usados em políticas para limitar o tempo que os usuários estão fora da postura de segurança. Se você tiver o Azure AD P2, poderá usar as revisões de acesso para automatizar o processo

#### <a name="conditional-access-recommended-reading"></a>Leitura recomendada de acesso condicional

- [Boas práticas para acesso condicional em Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [Configurações de acesso a identidade e dispositivo](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Referência de definições de acesso condicional do Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [Políticas comuns de acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>Área de superfície de acesso

### <a name="legacy-authentication"></a>Autenticação herdada

Credenciais fortes, como a MFA, não podem proteger aplicativos usando protocolos de autenticação herdados, que o tornam o vetor de ataque preferencial por atores mal-intencionados. O bloqueio da autenticação herdada é crucial para melhorar a postura de segurança de acesso.

A autenticação herdada é um termo que se refere aos protocolos de autenticação usados por aplicativos como:

- Clientes mais antigos do Office que não usam autenticação moderna (por exemplo, cliente do Office 2010)
- Clientes que usam protocolos de email, como IMAP/SMTP/POP

Os atacantes preferem fortemente estes protocolos - na verdade, quase [100% dos ataques com spray de senha](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) usam protocolos de autenticação legado! Os hackers usam protocolos de autenticação herdados, pois não dão suporte a entrada interativa, o que é necessário para desafios de segurança adicionais, como autenticação multifator e autenticação de dispositivo.

Se a autenticação do legado for amplamente utilizada no seu ambiente, deverá planear migrar clientes legados para clientes que suportem a [autenticação moderna](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) o mais rapidamente possível. No mesmo token, se você tiver alguns usuários que já usam autenticação moderna, mas outros que ainda usam autenticação herdada, execute as seguintes etapas para bloquear clientes de autenticação herdados:

1. Utilize [relatórios de atividade](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) de iniciar sessão para identificar utilizadores que ainda utilizam autenticação e reparação de planos:

   a. Atualize para clientes compatíveis com a autenticação moderna para usuários afetados.
   
   b. Planeje um período de transição para bloquear por etapas abaixo.
   
   c. Identifique quais aplicativos herdados têm uma dependência rígida na autenticação herdada. Consulte a etapa 3 abaixo.

2. Desabilite os protocolos herdados na origem (por exemplo, caixa de correio do Exchange) para usuários que não estão usando autenticação herdada para evitar mais exposição.
3. Para as restantes contas (idealmente identidades não humanas, como contas de serviço), utilize o acesso condicional para restringir os [protocolos legados](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) após a autenticação.

#### <a name="legacy-authentication-recommended-reading"></a>Leitura recomendada de autenticação herdada

- [Ativar ou desativar o acesso pop3 ou IMAP4 às caixas de correio no Exchange Server](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>Concessões de consentimento

Em um ataque de concessão de consentimento ilícito, o invasor cria um aplicativo registrado no Azure AD que solicita acesso a dados, como informações de contato, email ou documentos. Os usuários podem estar concedendo consentimento a aplicativos mal-intencionados por meio de ataques de phishing durante a aterrissagem em sites mal-intencionados.

Veja abaixo uma lista de aplicativos com permissões que você pode querer analisar para os serviços de nuvem da Microsoft:

- Aplicativos com app ou \*delegados. ReadWrite Permissions
- Aplicativos com permissões delegadas podem ler, enviar ou gerenciar email em nome do usuário
- Aplicativos que recebem o usando as seguintes permissões:

| Recurso | Permissão |
| :- | :- |
| Escritório 365 Exchange Online | Estendi. AccessAsUser. All |
| | EWS. AccessAsUser. All |
| | Mail. Read |
| Microsoft Graph API | Mail. Read |
| | Mail. Read. Shared |
| | Mail. ReadWrite |

- Os aplicativos receberam a representação completa do usuário do usuário conectado. Por exemplo:

|Recurso | Permissão |
| :- | :- |
| Microsoft Graph API| Directory. AccessAsUser. All |
| API de DESCANSO Azul | user_impersonation |

Para evitar este cenário, deve [consultar-se para detetar e remediar as subvenções](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) ilícitas de consentimento no Office 365 para identificar e corrigir quaisquer pedidos com subvenções ilícitas ou pedidos que tenham mais subvenções do que as necessárias. Em seguida, remova completamente o [autosserviço](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent) e estabeleça procedimentos de [governação.](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow) Por fim, agende revisões regulares de permissões de aplicativo e remova-as quando elas não forem necessárias.

#### <a name="consent-grants-recommended-reading"></a>O consentimento concede a leitura recomendada

- [Permissões da Microsoft Graph API](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Configurações de usuário e grupo

Abaixo estão as configurações de usuário e grupo que podem ser bloqueadas se não houver uma necessidade comercial explícita:

#### <a name="user-settings"></a>Definições do utilizador

- **Utilizadores Externos** - a colaboração externa pode acontecer organicamente na empresa com serviços como Teams, Power BI, Sharepoint Online e Azure Information Protection. Se tiver constrangimentos explícitos para controlar a colaboração externa iniciada pelo utilizador, recomenda-se que ative utilizadores externos utilizando a gestão do Direito do Direito do [Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) ou uma operação controlada, como por exemplo através do seu balcão de ajuda. Se não quiser permitir uma colaboração externa orgânica para serviços, pode impedir os [membros de convidar completamente utilizadores externos](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations). Em alternativa, também pode [permitir ou bloquear domínios específicos](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list) em convites externos ao utilizador.
- **Registos de aplicações** - quando os registos de aplicações estão ativados, os utilizadores finais podem embarcar em si próprios e conceder acesso aos seus dados. Um exemplo típico de registro de aplicativo são os usuários que habilitam plug-ins do Outlook, ou assistentes de voz, como Alexa e Siri para ler seu email e calendário ou enviar emails em seu nome. Se o cliente decidir desativar o registro do aplicativo, as equipes batalha e IAM deverão estar envolvidas no gerenciamento de exceções (registros de aplicativo que são necessários com base nos requisitos de negócios), pois eles precisariam registrar os aplicativos com uma conta de administrador, e, provavelmente, exigem a criação de um processo para colocar o processo em operação.
- **Portal da Administração** - as organizações podem bloquear a lâmina Azure AD no portal Azure para que os não administradores possam aceder à gestão da AD Azure no portal Azure e confundir-se. Vá para as configurações de usuário no portal de gerenciamento do Azure AD para restringir o acesso:

![Acesso restrito ao portal de administração](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Os não administradores ainda podem acessar as interfaces de gerenciamento do Azure AD por meio de linha de comando e outras interfaces programáticas.

#### <a name="group-settings"></a>Definições de grupo

**Gestão de Grupos de Self-Service / Utilizadores podem criar grupos de segurança / grupos O365.** Se não houver uma iniciativa de autoatendimento atual para grupos na nuvem, os clientes poderão decidir desativá-lo até que estejam prontos para usar esse recurso.

#### <a name="groups-recommended-reading"></a>Leitura recomendada de grupos

- [O que é a colaboração Azure Ative Directory B2B?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Integração de Aplicações com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Apps, permissões e consentimento no Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [Utilizar grupos para gerir o acesso aos recursos no Diretório Ativo Do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [Criação de gestão de acesso a aplicações de autosserviço no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>Tráfego de locais inesperados

Os invasores se originam de várias partes do mundo. Gerencie esse risco usando políticas de acesso condicional com o local como a condição. O estado de [localização](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) de uma política de Acesso Condicional permite bloquear o acesso a locais de onde não há motivo sinuoso para iniciar sessão.

![Criar um novo local nomeado](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Se disponível, use uma solução SIEM (gerenciamento de informações e eventos de segurança) para analisar e localizar padrões de acesso entre regiões. Se não utilizar um produto SIEM, ou se não estiver a ingerir informações de autenticação da Azure AD, recomendamos que utilize o [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para identificar padrões de acesso entre regiões.

## <a name="access-usage"></a>Uso de acesso

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Logs do Azure AD arquivados e integrados com planos de resposta a incidentes

Ter acesso a atividades de entrada, auditorias e eventos de risco do Azure AD é crucial para solução de problemas, análise de uso e investigações forenses. O Azure AD fornece acesso a essas fontes por meio de APIs REST que têm um período de retenção limitado. Um sistema SIEM (gerenciamento de informações e eventos de segurança) ou uma tecnologia de arquivamento equivalente é a chave para o armazenamento de longo prazo de auditorias e suporte. Para permitir o armazenamento a longo prazo dos registos AD Azure, deve adicioná-los à solução SIEM existente ou utilizar [o Monitor Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor). Arquive logs que podem ser usados como parte de seus planos de resposta a incidentes e investigações.

#### <a name="logs-recommended-reading"></a>Logs de leitura recomendados

- [Referência da Auditoria De Diretório Ativo Azure API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [Relatório de atividade sinuoso do Diretório Ativo Azure referência a API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [Obtenha dados utilizando a AD Reporting API azure com certificados](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Microsoft Graph para Azure Ative Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [Referência a API de Atividade de Gestão 365](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [Como utilizar o Pacote de Conteúdo power bi do diretório ativo Azure](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Resumo

Há 12 aspectos para uma infraestrutura de identidade segura. Esta lista ajudará você a proteger e gerenciar credenciais, definir a experiência de autenticação, delegar a atribuição, usar medidas e definir políticas de acesso com base na postura de segurança corporativa.

- Atribuir os proprietários a tarefas-chave.
- Implemente soluções para detectar senhas fracas ou vazadas, melhorar o gerenciamento e a proteção de senhas e proteger ainda mais o acesso dos usuários aos recursos.
- Gerencie a identidade dos dispositivos para proteger seus recursos a qualquer momento e de qualquer local.
- Implemente a autenticação com senha.
- Forneça um mecanismo de logon único padronizado em toda a organização.
- Migre aplicativos do AD FS para o Azure AD para permitir maior segurança e capacidade de gerenciamento mais consistente.
- Atribua usuários a aplicativos usando grupos para permitir maior flexibilidade e capacidade de gerenciar em escala.
- Configure políticas de acesso com base em risco.
- Bloquear protocolos de autenticação herdados.
- Detectar e corrigir concessões de consentimento ilícitos.
- Bloquear configurações de usuário e de grupo.
- Habilite o armazenamento de longo prazo de logs do Azure AD para solução de problemas, análise de uso e investigações forenses.

## <a name="next-steps"></a>Passos seguintes

Começar com os [controlos operacionais e ações operacionais da governação da identidade.](active-directory-ops-guide-govern.md)
