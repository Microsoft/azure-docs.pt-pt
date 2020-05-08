---
title: Novidades Notas de lançamento - Diretório Ativo Azure / Microsoft Docs
description: Saiba o que há de novo com o Azure Ative Directory, como as mais recentes notas de lançamento, questões conhecidas, correções de bugs, funcionalidade depreciada e próximas alterações.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c58c99faa83d6f04ac37d03789202e111f1b06d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611165"
---
# <a name="whats-new-in-azure-active-directory"></a>Quais as novidades no Azure Ative Directory?

>Seja notificado sobre quando revisitar esta página para atualizações copiando ![e colando](./media/whats-new/feed-icon-16x16.png) este URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` no leitor de feed do leitor de feed RSS.

A Azure AD recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

- Os últimos lançamentos
- Problemas conhecidos
- Correções de erros
- Funcionalidade preterida
- Planos para alterações

Esta página é atualizada mensalmente, por isso revisite-a regularmente. Se procura itens com mais de seis meses, pode encontrá-los no [Arquivo para o que há de novo no Diretório Ativo Azure.](whats-new-archive.md)

---

## <a name="april-2020"></a>Abril de 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>A experiência combinada de registo de informações de segurança está agora geralmente disponível

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Autenticações (Logins)

**Capacidade do produto:** Proteção de & de Segurança de Identidade

A experiência de registo combinado para a autenticação multi-factor (MFA) e reset de palavra-passe self-service (SSPR) está agora geralmente disponível. Esta nova experiência de registo permite que os utilizadores se registem para MFA e SSPR num processo único, passo a passo. Quando implementa a nova experiência para a sua organização, os utilizadores podem registar-se em menos tempo e com menos aborrecimentos. Confira aqui o [post](https://bit.ly/3etiRyQ)do blog.

---

### <a name="continuous-access-evaluation"></a>Avaliação contínua de acesso

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Autenticações (Logins)

**Capacidade do produto:** Proteção de & de Segurança de Identidade

A Avaliação contínua de Acesso é uma nova funcionalidade de segurança que permite a aplicação quase em tempo real das políticas em que os partidos que dependem que consomem Fichas de Acesso Azure Quando os eventos acontecem em Azure AD (como a eliminação da conta de utilizador). Estamos a lançar esta funcionalidade primeiro para os clientes Teams e Outlook. Para mais detalhes, leia o nosso [blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) e [documentação.](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation)

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS Sign-in: Os trabalhadores da Firstline podem inscrever-se em aplicações apoiadas pela Azure AD com o seu número de telefone e sem senha

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Autenticações (Logins)

**Capacidade do produto:** Autenticação do Utilizador

O Office está a lançar uma série de aplicações de negócio seletivas móveis que atendem a organizações não tradicionais, e a colaboradores de grandes organizações que não usam o e-mail como o seu método de comunicação principal. Estas aplicações visam funcionários da linha da frente, trabalhadores sem secretária, agentes de campo ou empregados de retalho que podem não obter um endereço de e-mail do seu empregador, têm acesso a um computador, ou a TI. Este projeto permitirá que estes colaboradores inscrevam-se em aplicações empresariais, inserindo um número de telefone e arredondando um código. Para mais detalhes, consulte a nossa [documentação de administração](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin) e [a documentação final do utilizador.](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer)

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Convidar utilizadores internos a utilizar a colaboração B2B

**Tipo:** Nova funcionalidade

**Categoria de serviço:** B2B

**Capacidade do produto:**

Estamos a expandir a capacidade de convite B2B para permitir que as contas internas existentes sejam convidadas a usar credenciais de colaboração B2B. Isto é feito passando o objeto do utilizador para a API convidar para além de parâmetros típicos como o endereço de e-mail convidado. O ID do objeto do utilizador, UPN, membro do grupo, atribuição de aplicativos, etc. permanecem intactos, mas daqui para a frente usarão B2B para autenticar com as suas credenciais de inquilino supérbio em vez das credenciais internas que usaram antes do convite. Para mais detalhes, consulte a [documentação](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>O modo apenas de relatório para acesso condicional está agora geralmente disponível

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Acesso Condicional

**Capacidade do produto:** Proteção de & de Segurança de Identidade

[O modo apenas de relatório para o Acesso Condicional Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) permite avaliar o resultado de uma política sem impor controlos de acesso. Você pode testar políticas apenas de relatório em toda a sua organização e entender o seu impacto antes de permitir-lhes, tornando a implementação mais segura e fácil. Nos últimos meses, temos visto uma forte adoção do modo apenas de relatório, com mais de 26M utilizadores já no âmbito de uma política apenas de relatório. Com este anúncio, as novas políticas de Acesso Condicional Azure AD serão criadas em modo apenas de relatório por padrão. Isto significa que pode monitorizar o impacto das suas políticas a partir do momento em que são criadas. E para aqueles que usam as APIs do Gráfico de MS, também podem gerir as políticas apenas de [relatório sem conta.](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Informações de acesso condicional e livro de reporte está geralmente disponível

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Acesso Condicional

**Capacidade do produto:** Proteção de & de Segurança de Identidade

Os insights de Acesso Condicional e o livro de [reporte](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) dão aos administradores uma visão sumária do Acesso Condicional Azure AD no seu inquilino. Com a capacidade de selecionar uma política individual, os administradores podem entender melhor o que cada política faz e monitorizar quaisquer mudanças em tempo real. O livro transmite dados armazenados no Monitor Azure, que pode configurar em poucos minutos [seguindo estas instruções](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). Para tornar o dashboard mais detetável, mudámo-lo para os novos insights e guia de reporte dentro do menu De Acesso Condicional Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Detalhes da política para acesso condicional está em pré-visualização pública

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Acesso Condicional

**Capacidade do produto:** Proteção de & de Segurança de Identidade

Os novos [detalhes da política](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) mostram quais as atribuições, condições e controlos que foram satisfeitos durante a avaliação da política de acesso condicional. Pode aceder à lâmina selecionando uma linha no **Acesso Condicional** ou **apenas** no relatório dos detalhes do Registo.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - abril 2020

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Aplicativos empresariais

**Capacidade do produto:** 3ª Integração do Partido

Em abril de 2020, adicionámos estas 31 novas aplicações com apoio da Federação à galeria de aplicações: 

[Aplicativos SincroPool](https://www.sincropool.com/), [SmartDB,](https://hibiki.dreamarts.co.jp/smartdb/trial/) [Float,](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial) [LMS365,](https://lms.365.systems/) [IWT Procurement Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial), [Lunni,](https://lunni.fi/) [EasySSO for Jira](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard,](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial) [Office 365 Mover](https://app.mover.io/login), Speaker [Engage,](https://speakerengage.com/login.php) [Honestly,](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial) [Ally,](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial) [DutyFlow,](https://app.dutyflow.nl/) [AlertMedia,](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial) [gr8 People,](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial) [Pen dodo](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial), [HighGround](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial), [Harmony,](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial) [Timetabling Solutions](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial), [SynchroNet CLICK](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial), [capacita](https://www.made-in-office.com/en/), [Fortes Change Cloud,](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial) [Litmus,](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial) [GroupTalk,](https://recorder.grouptalk.com/) [Frontify,](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial) [MongoDB Cloud,](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial) [TickitLMS Learn](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial), [COCO,](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/) [Nitro Productivity Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial) , Trend Micro Web Security [(TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Suporte de consulta delta do Microsoft Graph para oAuth2PermissionGrant disponível para pré-visualização pública

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Gráfico MS

**Capacidade do produto:** Experiência de Desenvolvimento

A consulta delta para oAuth2PermissionGrant está disponível para pré-visualização pública! Agora pode rastrear as mudanças sem ter de sondar continuamente o Microsoft Graph. [Saiba mais.](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Suporte de consulta delta do Microsoft Graph para contacto organizacional geralmente disponível

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Gráfico MS

**Capacidade do produto:** Experiência de Desenvolvimento

A consulta delta para contactos organizacionais está geralmente disponível! Agora pode rastrear as mudanças nas aplicações de produção sem ter de sondar continuamente o Microsoft Graph. Substitua qualquer código existente que resque continuamente os dados orgContact por consulta delta para melhorar significativamente o desempenho. [Saiba mais.](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Suporte de consulta delta do Microsoft Graph para aplicação geralmente disponível

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Gráfico MS

**Capacidade do produto:** Experiência de Desenvolvimento

A consulta delta para aplicações está geralmente disponível! Agora pode rastrear as mudanças nas aplicações de produção sem ter de sondar continuamente o Microsoft Graph. Substitua qualquer código existente que ressone continuamente os dados de aplicação por consulta delta para melhorar significativamente o desempenho. [Saiba mais.](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Suporte de consulta delta do Microsoft Graph para unidades administrativas disponíveis para pré-visualização pública

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Gráfico MS

**Capacidade do produto:** A consulta da Developer Experience Delta para unidades administrativas está disponível para pré-visualização pública! Agora pode rastrear as mudanças sem ter de sondar continuamente o Microsoft Graph. [Saiba mais.](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Gerir números de telefone de autenticação e muito mais em novas APIs beta do Microsoft Graph

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Gráfico MS

**Capacidade do produto:** Experiência de Desenvolvimento

Estas APIs são uma ferramenta chave para gerir os métodos de autenticação dos seus utilizadores. Agora pode pré-registar-se programáticamente e gerir os autenticadores utilizados para o reset de palavra-passe mFA e autosserviço (SSPR). Esta tem sido uma das funcionalidades mais requisitadas nos espaços Azure MFA, SSPR e Microsoft Graph. As novas APIs que lançámos nesta onda dão-te a capacidade de:

- Leia, adicione, atualize e remova os telefones de autenticação de um utilizador
- Redefinir a palavra-passe de um utilizador
- Ligue e desligue sms-sign-in

Para mais informações, consulte a visão geral dos métodos de [autenticação API da Azure API](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

---

### <a name="administrative-units-public-preview"></a>Pré-visualização pública das Unidades Administrativas

**Tipo:** Nova funcionalidade

**Categoria de serviço:** RBAC

**Capacidade do produto:** Controlo de Acesso

As unidades administrativas permitem-lhe conceder permissões de administração restritas a um departamento, região ou outro segmento da sua organização que define. Pode utilizar unidades administrativas para delegar permissões a administradores regionais ou definir a política a um nível granular. Por exemplo, um administrador de conta utilizador poderia atualizar informações de perfil, redefinir senhas e atribuir licenças para utilizadores apenas na sua unidade administrativa.

Utilizando unidades administrativas, um administrador central poderia:

- Criar uma unidade administrativa para a gestão descentralizada dos recursos
- Atribuir um papel com permissões administrativas apenas sobre utilizadores de AD Azure numa unidade administrativa
- Povoar as unidades administrativas com utilizadores e grupos conforme necessário

Para mais informações, consulte [a gestão de unidades administrativas em Diretório Ativo Azure (pré-visualização)](https://aka.ms/AdminUnitsDocs).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Papel integrado do Administrador de Impressora e técnico de impressora

**Tipo:** Nova funcionalidade

**Categoria de serviço:** RBAC

**Capacidade do produto:** Controlo de Acesso

**Administrador da impressora**: Os utilizadores com esta função podem registar impressoras e gerir todos os aspetos de todas as configurações da impressora na solução Microsoft Universal Print, incluindo as definições do Conector De Impressão Universal. Podem consentir em todos os pedidos de autorização de impressão delegados. Os Administradores da Impressora também têm acesso a relatórios de impressão. 

**Técnico de Impressora**: Os utilizadores com esta função podem registar impressoras e gerir o estado da impressora na solução Microsoft Universal Print. Também podem ler todas as informações sobre conector. As tarefas-chave que um Técnico de Impressora não pode fazer são definir permissões de utilizador em impressoras e impressoras de partilha. [Saiba mais.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Identidade Híbrida Admin papel incorporado

**Tipo:** Nova funcionalidade

**Categoria de serviço:** RBAC

**Capacidade do produto:** Controlo de Acesso

Os utilizadores desta função podem ativar, configurar e gerir serviços e configurações relacionadas com a ativação da identidade híbrida em Azure AD. Esta função confere a capacidade de configurar a AD Azure a um dos três métodos de autenticação suportados&#8212;sincronização de hash password (PHS), autenticação pass-through (PTA) ou Federação (AD FS ou fornecedor de 3.ª federação)&#8212;e implementar infraestruturas relacionadas no local para os permitir. A infraestrutura no local inclui agentes de provisionamento e PTA. Esta função permite ativar o Single Sign-On (S-SSO) sem emenda para permitir a autenticação perfeita em dispositivos não Windows 10 ou computadores não Windows Server 2016. Além disso, este papel confere a capacidade de ver registos de login e de aceder à saúde e à análise para fins de monitorização e resolução de problemas. [Saiba mais.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Função incorporada do Administrador de Rede

**Tipo:** Nova funcionalidade

**Categoria de serviço:** RBAC

**Capacidade do produto:** Controlo de Acesso

Os utilizadores com esta função podem rever recomendações de arquitetura de perímetro de rede da Microsoft que são baseadas na telemetria da rede a partir das suas localizações de utilizador. O desempenho da rede para o Office 365 baseia-se na arquitetura cuidadosa do perímetro da rede de clientes empresariais, que é geralmente específica para a localização do utilizador. Esta função permite a edição de locais de utilizador descobertos e a configuração de parâmetros de rede para esses locais para facilitar a melhoria das medições de telemetria e recomendações de design. [Saiba mais.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Atividade a granel e downloads na experiência do portal de administração da AD Azure

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Gestão de Utilizadores

**Capacidade do produto:** Diretório

Agora pode realizar atividades a granel em utilizadores e grupos em Azure AD, carregando um ficheiro CSV na experiência do portal de administração Azure AD. Pode criar utilizadores, apagar utilizadores e convidar utilizadores convidados. E pode adicionar e remover membros de um grupo.

Também pode descarregar listas de recursos da AD Azure a partir da experiência do portal de administração Azure AD. Pode descarregar a lista de utilizadores no diretório, a lista de grupos no diretório e os membros de um determinado grupo.

Para mais informações, consulte o seguinte:

- [Criar utilizadores](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add) ou [convidar utilizadores convidados](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)
- [Eliminar utilizadores](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete) ou [restaurar utilizadores eliminados](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)
- [Lista de descarregamento de utilizadores](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download) ou [lista de descarregamento de grupos](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)
- [Adicione (importar) membros](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members) ou [remova membros](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members) ou [descarregue a lista de membros](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members) para um grupo

---

### <a name="my-staff-delegated-user-management"></a>A minha equipa delegou a gestão do utilizador

**Tipo:** Nova funcionalidade

**Categoria de serviço:** Gestão de Utilizadores

**Capacidade do produto:**

A Minha Equipa permite que os Gestores firstline, como um gerente de loja, garantam que os seus funcionários possam aceder às suas contas Azure AD. Em vez de confiar em um helpdesk central, as organizações podem delegar tarefas comuns, tais como repor palavras-passe ou alterar números de telefone, para um Firstline Manager. Com o My Staff, um utilizador que não consegue aceder à sua conta pode voltar a ter acesso em apenas alguns cliques, sem necessidade de helpdesk ou pessoal de TI. Para mais informações, consulte a Gestão de Utilizadores do 'Gerir os [utilizadores' com o Meu Pessoal (pré-visualização)](https://aka.ms/MyStaffAdminDocs) e [delegar a gestão de utilizadores com o My Staff (pré-visualização)](https://aka.ms/MyStaffUserDocs).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Uma experiência atualizada do utilizador final em avaliações de acesso

**Tipo:** Mudança de funcionalidade

**Categoria de serviço:** Avaliações de Acesso

**Capacidade do produto:** Governança de Identidade

Atualizámos a experiência do revisor para as avaliações de acesso a Anúncio sintetizá-lo no portal My Apps. No final de abril, os seus revisores que estão ligados à experiência de avaliação de acesso a AD Azure verão um banner que lhes permitirá experimentar a experiência atualizada no My Access. Por favor, note que a experiência de comentários de Acesso atualizado oferece a mesma funcionalidade que a experiência atual, mas com uma melhor interface de utilizador em cima de novas capacidades para permitir que os seus utilizadores sejam produtivos. [Pode saber mais sobre a experiência atualizada aqui.](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review) Esta pré-estreia pública vai durar até ao final de julho de 2020. No final de julho, os revisores que não tenham optado pela experiência de pré-visualização serão automaticamente direcionados para o My Access para realizar avaliações de acesso. Se deseja que os seus revisores sejam permanentemente transferidos para a experiência de pré-visualização no My Access, [por favor faça um pedido aqui](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>As aplicações de fornecimento e reprodução de utilizadores de serviços de entrada no dia de trabalho suportam agora as versões mais recentes da Workday Web Services API

**Tipo:** Mudança de funcionalidade

**Categoria de serviço:** Provisionamento de Aplicativos

**Capacidade do produto:** 

Com base no feedback dos clientes, atualizámos agora as aplicações de fornecimento e reprodução de utilizadores do Workday na galeria de aplicações da empresa para suportar as versões mais recentes da API workday Web Services (WWS). Com esta alteração, os clientes podem especificar a versão WWS API que gostariam de usar na cadeia de ligação. Isto dá aos clientes a capacidade de recuperar mais atributos de RH disponíveis nos lançamentos do Workday. A aplicação Workday Writeback utiliza agora o serviço web Change_Work_Contact_Info recomendado para superar as limitações de Maintain_Contact_Info.

Se nenhuma versão for especificada na cadeia de ligação, por padrão, as aplicações de fornecimento de entrada workday continuarão a utilizar WWS v21.1 Para mudar para as mais recentes APIs do Dia de Trabalho para o fornecimento de utilizadores de entrada, os clientes precisam atualizar a cadeia de ligação conforme documentado [no tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) e também atualizar os XPATHs utilizados para o Workday como atributos documentados no guia de referência do atributo do Dia de [Trabalho.](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30) 

Para utilizar a nova API para repreensão, não são necessárias alterações na aplicação de provisionamento Workday Writeback. Do lado do Dia de Trabalho, certifique-se de que a conta do Utilizador do Sistema de Integração do Dia de Trabalho (ISU) tem permissões para invocar o processo de negócio Change_Work_Contact, conforme documentado na secção tutorial, [Configurar permissões](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions)de política de segurança do processo de negócio. 

Atualizámos o nosso [guia tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) para refletir o novo suporte à versão API.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Os utilizadores com função de acesso predefinido estão agora no âmbito do fornecimento

**Tipo:** Mudança de funcionalidade

**Categoria de serviço:** Provisionamento de Aplicativos

**Capacidade do produto:** Gestão do ciclo de vida de identidade

Historicamente, os utilizadores com a função de acesso predefinido têm estado fora de âmbito para o provisionamento. Ouvimos comentários de que os clientes querem que os utilizadores com este papel estejam no âmbito do provisionamento. A partir de 16 de abril de 2020, todas as novas configurações de fornecimento permitem aos utilizadores a função de acesso padrão a ser aprovisionado. Gradualmente, mudaremos o comportamento das configurações de provisionamento existentes para apoiar os utilizadores de fornecimento com esta função. [Saiba mais.](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>UI de provisionamento atualizado

**Tipo:** Mudança de funcionalidade

**Categoria de serviço:** Provisionamento de Aplicativos

**Capacidade do produto:** Gestão do ciclo de vida de identidade

Refrescamos a nossa experiência de provisionamento para criar uma visão de gestão mais focada. Ao navegar para a lâmina de provisionamento para uma aplicação empresarial que já foi configurada, poderá monitorizar facilmente o progresso do fornecimento e gestão de ações como iniciar, parar e reiniciar o fornecimento. [Saiba mais.](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>A validação da regra do Grupo Dinâmico já está disponível para pré-visualização pública

**Tipo:** Mudança de funcionalidade

**Categoria de serviço:** Gestão de Grupos

**Capacidade do produto:** Colaboração

O Azure Ative Directory (Azure AD) fornece agora os meios para validar as regras dinâmicas do grupo. No separador de **regras Validar,** pode validar a sua regra dinâmica contra membros do grupo de amostras para confirmar que a regra está a funcionar como esperado. Ao criar ou atualizar regras dinâmicas do grupo, os administradores querem saber se um utilizador ou um dispositivo será membro do grupo. Isto ajuda a avaliar se um utilizador ou dispositivo cumpre os critérios de regra e ajuda na resolução de problemas quando não é esperado a adesão.

Para mais informações, consulte [Validar uma regra dinâmica de membro do grupo (pré-visualização)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Pontuação de Garantia de Identidade - Falhas de Segurança e atualizações de ação de melhoria do MFA

**Tipo:** Mudança de funcionalidade

**Categoria de serviço:** N/A

**Capacidade do produto:** Proteção de & de Segurança de Identidade

**Apoiar os incumprimentos de segurança para as ações de melhoria da AD Azure:** O Microsoft Secure Score irá atualizar ações de melhoria para suportar falhas de [segurança no Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults), que facilitam a proteção da sua organização com configurações de segurança pré-configuradas para ataques comuns. Isto afetará as seguintes ações de melhoria:

- Garantir que todos os utilizadores podem completar a autenticação de vários fatores para acesso seguro
- Exigir MFA para funções administrativas
- Ativar a política para bloquear a autenticação do legado
 
Atualizações de ação de melhoria do **MFA:** Para refletir a necessidade de as empresas garantirem a segurança mais alta ao mesmo tempo que aplicam políticas que funcionam com o seu negócio, o Microsoft Secure Score removeu três ações de melhoria centradas na autenticação de vários fatores e acrescentou duas.

Ações de melhoria removidas:

- Registe todos os utilizadores para autenticação multifactor
- Pedir MFA para todos os utilizadores
- Exigir MFA para funções privilegiadas da Azure AD

Ações de melhoria adicionais:

- Garantir que todos os utilizadores podem completar a autenticação de vários fatores para acesso seguro
- Exigir MFA para funções administrativas

Estas novas ações de melhoria requerem o registo dos seus utilizadores ou administradores para autenticação de vários fatores (MFA) em todo o seu diretório e estabelecendo o conjunto certo de políticas que se adequam às suas necessidades organizacionais. O principal objetivo é ter flexibilidade, garantindo que todos os seus utilizadores e administradores podem autenticar com múltiplos fatores ou solicitações de verificação de identidade baseadas no risco. Isto pode assumir a forma de ter múltiplas políticas que aplicam decisões com âmbito, ou definir falhas de segurança (a partir de 16 de março) que permitem à Microsoft decidir quando desafiar os utilizadores para o MFA. [Leia mais sobre as novidades no Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---

## <a name="march-2020"></a>Março de 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Contas de Diretório Ativo Azure não geridas em atualização B2B para março de 2021

**Tipo:** Plano de mudança  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
A partir de 31 de março de **2021,** a Microsoft deixará de apoiar o resgate de convites através da criação de contas não geridas do Azure Ative Directory (Azure AD) e dos inquilinos para cenários de colaboração B2B. Em preparação para isso, encorajamo-lo a optar por [e-mail de autenticação de senha única.](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Os utilizadores com a função de acesso predefinido estarão no âmbito do provisionamento

**Tipo:** Plano de mudança  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida de identidade
 
Historicamente, os utilizadores com a função de acesso predefinido têm estado fora de âmbito para o provisionamento. Ouvimos comentários de que os clientes querem que os utilizadores com este papel estejam no âmbito do provisionamento. Estamos a trabalhar na implementação de uma alteração para que todas as novas configurações de fornecimento permitam aos utilizadores com a função de acesso padrão ser provisionado. Gradualmente, vamos alterar o comportamento das configurações de provisionamento existentes para apoiar o fornecimento de utilizadores com esta função. Não é necessária nenhuma ação do cliente. Publicaremos uma atualização da nossa [documentação](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) assim que esta mudança estiver em vigor.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>A colaboração Azure AD B2B estará disponível no Microsoft Azure operado por inquilinos da 21Vianet (Azure China 21Vianet)

**Tipo:** Plano de mudança  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
As capacidades de colaboração Azure AD B2B serão disponibilizadas na Microsoft Azure operada por inquilinos da 21Vianet (Azure China 21Vianet), permitindo aos utilizadores de um inquilino Azure China 21Vianet colaborar em perfeitas colaborações com utilizadores de outros inquilinos Azure China 21Vianet. Saiba mais sobre a [colaboração Azure AD B2B.](https://docs.microsoft.com/azure/active-directory/b2b/)

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B Colaboração convite redesenho de e-mail

**Tipo:** Plano de mudança  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
Os [e-mails enviados](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) pelo serviço de convite de colaboração Azure AD B2B para convidar os utilizadores para o diretório serão redesenhados para tornar a informação do convite e os próximos passos do utilizador mais claros.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>As mudanças de política homeRealmDiscovery aparecerão nos registos de auditoria

**Tipo:** Fixo  
**Categoria de serviço:** Auditoria  
**Capacidade do produto:** Relatório de & de monitorização
 
Corrigimos um bug onde as alterações à [política HomeRealmDiscovery](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) não foram incluídas nos registos de auditoria. Agora poderão ver quando e como a política foi alterada, e por quem. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - março de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Em março de 2020, adicionámos estas 51 novas aplicações com apoio da Federação à galeria de aplicações: 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Zoho One China](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co App SAML,](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial) [iPoint Service Provider](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial), contexxt.ai [SPHERE,](https://contexxt-sphere.com/login) [Wisdom By Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial), Flare Digital [Signage,](https://spark-dev.pixelnebula.com/login) [Logz.io - Cloud Observability for Engineers,](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial) [SpectrumU,](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial) [BizzContact,](https://bizzcontact.app/) [Elqano SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial), [MarketSignShare](http://www.signshare.com/), [CrossKnowledge Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial), [Netvision Compas,](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial) [FCM HUB,](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial) [RIB A/S Byggeweb Mobile,](https://apps.apple.com/us/app/docia/id529058757) [GoLinks,](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial) [Datadog,](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial) [Zscaler B2B User Portal,](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial) [LIFT,](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial) [Planview Enterprise One,](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial) [WatchTeams,](https://www.devfinition.com/) [Aster,](https://demo.asterapp.io/login) [Skills Workflow](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial), [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx), IP [Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial), [InVision](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial), [Pipedrive,](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial) [Showcase Workshop](https://app.showcaseworkshop.com/), Greenlight Integration Platform , [Greenlight](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial) [Compliant Access Management](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial), [Grok Learning,](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial) [Miradore Online,](https://login.online.miradore.com/) [Khoros Care,](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial) [AskYourTeam,](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial) [TruNarrative,](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial) [Smartwaiver,](https://www.smartwaiver.com/m/user/sw_login.php?wms_login) [Biza Estúdio gi para Automação de Processos Digitais,](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial) [insuiteX,](https://www.insuite.jp/) [sybo,](https://www.systexsoftware.com.tw/) [Britive](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial), [WhosOffice](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial), [E-days,](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial) [Kollective SDN,](https://portal.kollective.app/login) [Witivio,](https://app.witivio.com/) [Playvox,](https://my.playvox.com/login) [Korn Ferry 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial), [Campus Café,](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial) [Catchpoint,](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial) [Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Colaboração Azure AD B2B disponível em inquilinos do Governo azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** B2B/B2C
 
As funcionalidades de colaboração Azure AD B2B estão agora disponíveis entre alguns inquilinos do Governo Azure.  Para saber se o seu inquilino é capaz de usar estas capacidades, siga as instruções em Como posso dizer se a [colaboração B2B está disponível no meu inquilino do Governo dos EUA?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Integração do Azure Monitor para A Tetos Azure já está disponível no Governo do Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Relatório de & de monitorização
 
A integração do Azure Monitor com os registos da AD Azure já está disponível no Governo Azure. Pode encaminhar os Registos De AD Azure (Registos de Auditoria e Login) para uma conta de armazenamento, Hub de Eventos e Log Analytics. Consulte a [documentação detalhada,](https://aka.ms/aadlogsinamd) bem como os planos de [implementação para reporte e monitorização](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) para cenários de AD Azure.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Atualização da Proteção de Identidade no Governo de Azure

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Proteção de & de Segurança de Identidade

Estamos entusiasmados por partilhar que já lançámos a renovada experiência de [Proteção](https://aka.ms/IdentityProtectionDocs) de Identidade azure no [portal do Governo microsoft Azure.](https://portal.azure.us/) Para mais informações, consulte o nosso post de blog de [anúncios.](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Recuperação de desastres: Descarregue e guarde a sua configuração de provisionamento

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida de identidade
 
O serviço de provisionamento de Anúncios Azure fornece um conjunto rico de capacidades de configuração. Os clientes precisam de ser capazes de guardar a sua configuração para que possam remeter-se mais tarde ou reverter para uma versão conhecida e boa. Adicionámos a capacidade de descarregar a sua configuração de provisionamento como um ficheiro JSON e carregá-la quando precisar. [Saiba mais](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (reset de senha de autosserviço) requer agora dois portões para administradores no Microsoft Azure operados pela 21Vianet (Azure China 21Vianet) 

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Reset de palavra-passe self-service  
**Capacidade do produto:** Proteção de & de Segurança de Identidade
 
Anteriormente no Microsoft Azure operado pela 21Vianet (Azure China 21Vianet), administra-se a utilizar o reset de palavra-passe self-service (SSPR) para redefinir as suas próprias palavras-passe necessárias apenas um "gate" (desafio) para provar a sua identidade. Nas nuvens públicas e outras nacionais, os administradores geralmente devem usar dois portões para provar a sua identidade ao usar sSPR. Mas como não apoiámos SMS ou telefonemas no Azure China 21Vianet, permitimos a redefinição de uma palavra-passe de um portão por administradores.

Estamos a criar paridade de recursos SSPR entre o Azure China 21Vianet e a nuvem pública. Para a frente, os administradores devem usar dois portões quando utilizarem SSPR. Serão suportadas notificações e códigos de aplicações SMS, chamadas telefónicas e autenticadores. [Saiba mais](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>O comprimento da palavra-passe é limitado a 256 caracteres

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
Para garantir a fiabilidade do serviço Azure AD, as palavras-passe dos utilizadores estão agora limitadas em comprimento a 256 caracteres. Os utilizadores com senhas mais longas do que esta serão solicitadas a alterar a sua palavra-passe no login subsequente, quer contactando a sua administração ou utilizando a função de reset de palavra-passe de autosserviço.

Esta alteração foi ativada a 13 de março de 2020, às 10:00 PST (18:00 UTC), e o erro é AADSTS 50052, InvalidPasswordExceedsMaxLength. Consulte o aviso de alteração de [rutura](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) para mais detalhes.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Os registos de entrada da Azure AD já estão disponíveis para todos os inquilinos gratuitos através do portal Azure

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Reportagem  
**Capacidade do produto:** Relatório de & de monitorização
 
A partir de agora, os clientes que tenham inquilinos gratuitos podem aceder aos registos de entrada da [Azure AD a partir do portal Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) por um máximo de 7 dias. Anteriormente, os registos de login estavam disponíveis apenas para clientes com licenças Azure Ative Directory Premium. Com esta mudança, todos os inquilinos podem aceder a estes registos através do portal.

> [!NOTE]
> Os clientes ainda precisam de uma licença premium (Azure Ative Directory Premium P1 ou P2) para aceder aos registos de entrada através do Microsoft Graph API e do Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Deprecation da opção de grupos de diretório saem de Configurações Gerais de Grupos no portal Azure

**Tipo:** Depreciado  
**Categoria de serviço:** Gestão de Grupos  
**Capacidade do produto:** Colaboração

Para proporcionar uma forma mais flexível para os clientes criarem grupos de diretórios que melhor satisfaçam as suas necessidades, substituímos a opção grupos de **diretórios** das configurações**gerais** dos **grupos** > no portal Azure com uma ligação à [documentação dinâmica do grupo.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) Melhorámos a nossa documentação para incluir mais instruções para que os administradores possam criar grupos de todos os utilizadores que incluam ou excluam os utilizadores convidados.

---

## <a name="february-2020"></a>Fevereiro de 2020

### <a name="upcoming-changes-to-custom-controls"></a>Próximas alterações aos controlos personalizados

**Tipo:** Plano de mudança  
**Categoria de serviço:** MFA  
**Capacidade do produto:** Proteção de & de Segurança de Identidade
 
Estamos a planear substituir a pré-visualização de controlos personalizados atuais por uma abordagem que permite que as capacidades de autenticação fornecidas pelo parceiro funcionem perfeitamente com o administrador do Diretório Ativo Azure e experiências de utilizador final. Hoje, as soluções parceiras do MFA enfrentam as seguintes limitações: funcionam apenas depois de ter sido introduzida uma senha; não servem de MFA para autenticação de aumento em outros cenários-chave; e não se integram com funções de gestão de credenciais de utilizador final ou de credencial administrativa. A nova implementação permitirá que os fatores de autenticação fornecidos pelo parceiro funcionem em conjunto com fatores incorporados para cenários-chave, incluindo registo, utilização, reclamações de MFA, autenticação de aumento, reporte e exploração madeireira. 

Os controlos personalizados continuarão a ser suportados na pré-visualização ao lado do novo design até atingir a disponibilidade geral. Nessa altura, daremos tempo aos clientes para migrarem para o novo design. Devido às limitações da abordagem atual, não embarcaremos nos novos fornecedores até que o novo design esteja disponível. Estamos a trabalhar em estreita colaboração com clientes e fornecedores e comunicaremos a linha temporal à medida que nos aproximamos. [Saiba mais](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identity Secure Score - Atualizações de ação de melhoria do MFA

**Tipo:** Plano de mudança  
**Categoria de serviço:** MFA  
**Capacidade do produto:** Proteção de & de Segurança de Identidade
 
Para refletir a necessidade de as empresas garantirem a segurança mais alta ao mesmo tempo que aplicam políticas que trabalham com o seu negócio, o Microsoft Secure Score está a remover três ações de melhoria centradas na autenticação de vários fatores (MFA), e adicionando duas.

As seguintes ações de melhoria serão removidas:

- Registe todos os utilizadores para MFA
- Pedir MFA para todos os utilizadores
- Exigir MFA para funções privilegiadas da Azure AD

Serão adicionadas as seguintes ações de melhoria:

- Garantir que todos os utilizadores podem completar o MFA para acesso seguro
- Exigir MFA para funções administrativas

Estas novas ações de melhoria exigirão registar os seus utilizadores ou administradores para mfa em todo o seu diretório e estabelecer o conjunto certo de políticas que se adequam às suas necessidades organizacionais. O principal objetivo é ter flexibilidade, garantindo que todos os seus utilizadores e administradores podem autenticar com múltiplos fatores ou solicitações de verificação de identidade baseadas no risco. Isto pode assumir a forma de definir falhas de segurança que permitem à Microsoft decidir quando desafiar os utilizadores para mfa, ou ter múltiplas políticas que aplicam decisões de âmbito. Como parte destas atualizações de ação de melhoria, as políticas de proteção de base deixarão de ser incluídas nos cálculos de pontuação. [Leia mais sobre o que está por vir no Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Sku serviços de domínio Azure AD

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Serviços de Domínio Azure AD  
**Capacidade do produto:** Serviços de Domínio Azure AD
 
Ouvimos comentários de que os clientes da Azure AD Domain Services querem mais flexibilidade na seleção dos níveis de desempenho para os seus casos. A partir de 1 de fevereiro de 2020, passamos de um modelo dinâmico (onde a Azure AD determina o nível de desempenho e preços baseado na contagem de objetos) para um modelo de autosseleção. Agora os clientes podem escolher um nível de desempenho que corresponda ao seu ambiente. Esta mudança também nos permite permitir novos cenários como Florestas de Recursos, e funcionalidades Premium como backups diários. A contagem de objetos é agora ilimitada para todas as SKUs, mas continuaremos a oferecer sugestões de contagem de objetos para cada nível.

**Não é necessária nenhuma ação imediata do cliente.** Para os clientes existentes, o nível dinâmico que estava em uso a 1 de fevereiro de 2020, determina o novo nível padrão. Não existe qualquer preço ou impacto de desempenho como resultado desta mudança. Daqui para a frente, os clientes da Azure AD DS terão de avaliar os requisitos de desempenho à medida que o seu tamanho de diretório e características de carga de trabalho mudarem. A troca entre os níveis de serviço continuará a ser uma operação sem tempo de inatividade, e deixaremos de transferir automaticamente os clientes para novos níveis com base no crescimento do seu diretório. Além disso, não haverá aumentos de preços, e novos preços irão alinhar-se com o nosso atual modelo de faturação. Para mais informações, consulte a [documentação Azure AD DS SKUs](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) e a página de preços dos Serviços de [Domínio Azure AD](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - fevereiro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Em fevereiro de 2020, adicionámos estas 31 novas aplicações com apoio da Federação à galeria de aplicações: 

Plataforma de [Patentes IamIP](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [NS1 SSO For Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), Em caso de crise - Portal [Online](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), Bic [Cloud Design,](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial) [Apicultor Azure AD Data Connector,](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial) [Korn Ferry Assessments,](https://www.kornferry.com/solutions/kf-digital/kf-assess) [Comando Verkada,](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial) [Splashtop,](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial) [Syxsense,](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial) [EAB Navigate,](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial) [New Relíquia (Lançamento Limitado)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium](https://admin.thulium.com/login/instance), Gestor de [Bilhetes](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [Modelo de Escolha para Equipas](https://links.officeatwork.com/templatechooser-download-teams), [Beesy,](https://www.beesy.me/index.php/site/login)Sistema de [Suporte à Saúde,](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial) [MURAL,](https://app.mural.co/signup) [Colmeia,](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial) [Lavado,](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview) [Wakelet,](https://wakelet.com/login) [Firmex VDR,](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial) [ThingLink para Professores e Escolas,](https://www.thinglink.com/) [Coda,](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial) [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO,](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial) [Convidadores,](https://invitepeople.com/login) [Reimpressões Desk - Artigo Galaxy,](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial) [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Novos conectores de provisionamento na Galeria de Aplicação da AD Azure - fevereiro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Agora pode automatizar a criação, atualização e a eliminar as contas dos utilizadores destas aplicações recentemente integradas:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud da Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte o fornecimento de [utilizadores automate para aplicações SaaS com AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Suporte da AD Azure para chaves de segurança FIDO2 em ambientes híbridos

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
Estamos anunciando a pré-visualização pública do suporte da AD Azure para chaves de segurança FIDO2 em ambientes híbridos. Os utilizadores podem agora utilizar chaves de segurança FIDO2 para iniciar sessão nos seus dispositivos Hybrid Azure AD e obter um sinal perfeito para os seus recursos no local e na nuvem. O suporte para ambientes Híbridos tem sido a funcionalidade mais requisitada dos nossos clientes sem palavras-passe desde que inicialmente lançámos a pré-visualização pública para suporte fido2 em dispositivos aderidos ao Azure AD. A autenticação sem palavras-passe utilizando tecnologias avançadas como biometria e criptografia de chaves públicas/privadas proporcionam conveniência e facilidade de utilização enquanto estão seguras. Com esta pré-visualização pública, pode agora usar a autenticação moderna como as chaves de segurança FIDO2 para aceder aos recursos tradicionais do Diretório Ativo. Para mais informações, vá à [SSO para os recursos no local.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises) 

Para começar, visite as chaves de [segurança FIDO2 para o seu inquilino](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) para instruções passo a passo. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>A nova experiência My Account está agora geralmente disponível

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** O meu perfil/conta  
**Capacidade do produto:** Experiências de utilizador final
 
A Minha Conta, a única loja para todas as necessidades de gestão de conta de utilizador final, está agora geralmente disponível! Os utilizadores finais podem aceder a este novo site via URL, ou no cabeçalho da nova experiência My Apps. Saiba mais sobre todas as capacidades de self-service que a nova experiência oferece na [Visão Geral do Portal da Minha Conta.](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Atualização do URL do site da minha conta para myaccount.microsoft.com

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** O meu perfil/conta  
**Capacidade do produto:** Experiências de utilizador final
 
A nova experiência de utilizador final da `https://myaccount.microsoft.com` My Account irá atualizar o seu URL para o próximo mês. Encontre mais informações sobre a experiência e todas as capacidades de self-service da conta que oferece aos utilizadores finais no [portal My Account.](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)

---
 
## <a name="january-2020"></a>Janeiro de 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>O novo portal My Apps já está geralmente disponível

**Tipo:** Plano de mudança  
**Categoria de serviço:** Minhas Apps  
**Capacidade do produto:** Experiências de utilizador final
 
Atualize a sua organização para o novo portal My Apps que já está geralmente disponível! Encontre mais informações sobre o novo portal e coleções [na Create collections no portal My Apps.](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Espaços de trabalho em Azure AD foram renomeados para coleções

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Minhas Apps   
**Capacidade do produto:** Experiências de utilizador final
 
Os espaços de trabalho, os administradores de filtros podem configurar para organizar as aplicações dos seus utilizadores, serão agora referidos como coleções. Encontre mais informações sobre como configurá-las na [Create collections no portal My Apps](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Inscrição e inscrição do telefone Azure AD B2C e inscrição utilizando a política personalizada (Visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C
 
Com o registo do número de telefone e o registo, os desenvolvedores e empresas podem permitir que os seus clientes se inscrevam e inscrevam-se usando uma senha única enviada para o número de telefone do utilizador através de SMS. Esta funcionalidade também permite ao cliente alterar o seu número de telefone se perder o acesso ao seu telemóvel. Com o poder das políticas personalizadas, o registo telefónico e o registo permitem que os desenvolvedores e empresas comuniquem a sua marca através da personalização da página. Descubra como [configurar o registo telefónico e iniciar sessão com políticas personalizadas em Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Novos conectores de provisionamento na Galeria de Aplicação da AD Azure - janeiro 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Agora pode automatizar a criação, atualização e a eliminar as contas dos utilizadores destas aplicações recentemente integradas:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte o fornecimento de [utilizadores automate para aplicações SaaS com AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - janeiro de 2020

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido
 
Em janeiro de 2020, adicionámos estas 33 novas aplicações com apoio da Federação à galeria de aplicações: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial) [Abibot Netlogistik,](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik) [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LeaveBot,](https://leavebot.io/#home) [DataCamp,](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial) [TripActions,](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial) [SmartWork,](https://www.intumit.com/english/SmartWork.html) [Dotcom-Monitor,](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial) [SSOGEN - Azure AD SSO Gateway para oracle E-Business Suite - EBS, PeopleSoft, e JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [Hospedado MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB for Microsoft Teams,](http://teams.smartdb.jp/login/) [PressPage,](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial) [ContractSafe Saml2 SSO,](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial) [Maxient Conduct Manager Software,](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial) [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), [Squelch Cloud Office365 Connector,](https://laxmi.squelch.io/login) [PingFlow Authentication](https://app-staging.pingview.io/), [PrinterLogic SaaS,](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial) [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai,](https://app.sandwai.com/) [EZRentOut,](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial) [AssetSonar,](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial) [Assistente Virtual Akari](https://akari.io/akari-virtual-assistant/)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Duas novas deteções de Proteção de Identidade

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Proteção de Identidade  
**Capacidade do produto:** Proteção de & de Segurança de Identidade
 
Adicionámos dois novos tipos de deteção ligados à Proteção de Identidade: regras suspeitas de manipulação de caixas de entrada e viagens impossíveis. Estas deteções offline são descobertas pelo Microsoft Cloud App Security (MCAS) e influenciam o utilizador e o risco de iniciar sessão na Proteção de Identidade. Para obter mais informações sobre estas deteções, consulte os nossos [tipos de risco de inscrição](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Breaking Change: Os fragmentos de URI não serão transportados através do redirecionamento de login

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador
 
A partir de 8 de fevereiro de 2020, quando um pedido é enviado para login.microsoftonline.com para assinar em um utilizador, o serviço anexará um fragmento vazio ao pedido.  Isto impede uma classe de ataques redirecionados, garantindo que o navegador elimina qualquer fragmento existente no pedido. Nenhuma aplicação deve ter uma dependência deste comportamento. Para mais informações, consulte [as alterações](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) de Rutura na documentação da plataforma de identidade da Microsoft.

---

## <a name="december-2019"></a>Dezembro de 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrar o fornecimento de Fatores de Sucesso SAP em AD Azure e no local AD (Visualização Pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Provisionamento de Aplicativos  
**Capacidade do produto:** Gestão do ciclo de vida de identidade

Agora pode integrar o SAP SuccessFactors como uma fonte de identidade autoritária em Azure AD. Esta integração ajuda-o a automatizar o ciclo de vida de identidade de ponta a ponta, incluindo a utilização de eventos baseados em RH, como novas contratações ou rescisões, para controlar o fornecimento de contas Azure AD.

Para obter mais informações sobre como configurar o fornecimento de entrada sap SuccessFactors para a Azure AD, consulte o tutorial [de provisionamento automático Configure SAP SuccessFactors.](https://aka.ms/SAPSuccessFactorsInboundTutorial)

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Suporte para e-mails personalizados em Azure AD B2C (Pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2C - Gestão de Identidade do Consumidor  
**Capacidade do produto:** B2B/B2C

Agora pode utilizar o Azure AD B2C para criar e-mails personalizados quando os seus utilizadores se inscreverem para utilizar as suas apps. Ao utilizar o DisplayControls (atualmente em pré-visualização) e um fornecedor de e-mail de terceiros (como, [SendGrid,](https://sendgrid.com/) [SparkPost](https://sparkpost.com/), ou um API REST personalizado), pode utilizar o seu próprio modelo de e-mail, **a partir do** endereço e do texto sujeito, bem como apoiar as definições de localização e senha personalizada (OTP).

Para mais informações, consulte [a verificação de email personalizada no Diretório Ativo Azure B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Substituição de políticas de base por incumprimentos de segurança

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Segurança e Proteção de Identidade

Como parte de um modelo seguro por padrão para a autenticação, estamos a remover as políticas de proteção de base existentes de todos os inquilinos. Esta remoção está prevista para ser concluída no final de fevereiro. A substituição destas políticas de proteção de base são incumprimentos de segurança. Se tem usado políticas de proteção de base, deve planear avançar para a nova política de incumprimentos de segurança ou para o Acesso Condicional. Se não usaste estas políticas, não há ação para tomares.

Para obter mais informações sobre os novos incumprimentos de segurança, consulte [o que são falhas de segurança?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Para obter mais informações sobre as políticas de Acesso Condicional, consulte [as políticas de Acesso Condicional Comum.](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

---

## <a name="november-2019"></a>Novembro de 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Suporte para o atributo SameSite e Chrome 80

**Tipo:** Plano de mudança  
**Categoria de serviço:** Autenticações (Logins)  
**Capacidade do produto:** Autenticação do Utilizador

Como parte de um modelo seguro por padrão para cookies, o navegador Chrome 80 está a mudar a forma como trata os cookies sem o `SameSite` atributo. Qualquer cookie que não `SameSite` especifique o atributo será `SameSite=Lax`tratado como se estivesse definido , o que resultará em Chrome bloqueando certos cenários de partilha de cookies de domínio transversal de que a sua aplicação pode depender. Para manter o comportamento chrome mais `SameSite=None` antigo, pode `Secure` utilizar o atributo e adicionar um atributo adicional, pelo que os cookies de cross-site só podem ser acedidos sobre ligações HTTPS. O Chrome deverá concluir esta alteração até 4 de fevereiro de 2020.

Recomendamos que todos os nossos desenvolvedores testem as suas aplicações usando esta orientação:

- Defina o valor predefinido para a definição de **Cookie seguro de utilização** para **Sim**.

- Detete o valor predefinido para o atributo **do SameSite** a **None**.

- Adicione um `SameSite` atributo adicional de **Secure**.

Para mais informações, consulte as [próximas Alterações de Cookies do SameSite em ASP.NET e ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) e Potencial perturbação nos websites dos clientes e produtos e [serviços da Microsoft na versão 79 do Chrome e posteriormente.](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Novo hotfix para Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Tipo:** Fixo  
**Categoria de serviço:** Gestor de Identidade da Microsoft  
**Capacidade do produto:** Gestão do ciclo de vida de identidade

Um pacote de rollup hotfix (build 4.6.34.0) está disponível para microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Este pacote de rollup resolve problemas e adiciona melhorias que são descritas na secção "Questões fixas e melhorias adicionadas nesta atualização".

Para mais informações e para descarregar o pacote hotfix, consulte [o Microsoft Identity Manager 2016 Service Pack 2 (build 4.6.34.0) Update Rollup](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Novo relatório de atividade de aplicações da AD FS para ajudar a migrar aplicações para a AD Azure (Visualização Pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO

Utilize o novo relatório de atividade da app Ative Directory Federation Services (AD FS), no portal Azure, para identificar quais das suas aplicações são capazes de ser migradas para a AD Azure. O relatório avalia todas as aplicações aD FS para compatibilidade com a Azure AD, verifica quaisquer problemas e dá orientações sobre a preparação de aplicações individuais para a migração.

Para mais informações, consulte Utilize o relatório de atividade de [aplicação da AD FS para migrar aplicações para a AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Novo fluxo de trabalho para os utilizadores solicitarem o consentimento do administrador (Pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** Controlo de Acesso

O novo fluxo de trabalho de consentimento da administração dá aos administradores uma forma de conceder acesso a apps que requerem aprovação administrativa. Se um utilizador tentar aceder a uma aplicação, mas não conseguir dar consentimento, pode agora enviar um pedido de aprovação de administradores. O pedido é enviado por e-mail, e colocado numa fila acessível a partir do portal Azure, a todos os administradores que foram designados como revisores. Depois de um revisor tomar medidas sobre um pedido pendente, os utilizadores solicitados são notificados da ação.

Para mais informações, consulte Configure o fluxo de trabalho de [consentimento do administrador (pré-visualização)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nova app Azure AD Inscrições Token experiência de configuração para gestão de reclamações opcionais (Pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Experiência de Desenvolvimento

A nova lâmina de **configuração de registos de aplicações da AD Azure** no portal Azure mostra agora aos programadores de aplicações uma lista dinâmica de reclamações opcionais para as suas apps. Esta nova experiência ajuda a agilizar as migrações de aplicações da AD Azure e a minimizar as configurações de sinistros opcionais.

Para mais informações, consulte [Fornecer reclamações opcionais na sua aplicação Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Novo fluxo de trabalho de aprovação em duas fases na gestão de direitos da AD Azure (Visualização Pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Gestão de Direitos

Introduzimos um novo fluxo de trabalho de aprovação em duas fases que lhe permite exigir dois aprovadores para aprovar o pedido de um utilizador para um pacote de acesso. Por exemplo, pode defini-lo para que o gestor do utilizador que solicita aprove primeiro, e depois também pode exigir que um proprietário de recursos aprove. Se um dos aprovadores não aprovar, o acesso não é concedido.

Para mais informações, consulte as definições de pedido de alteração e aprovação para um pacote de [acesso na gestão de direitos da AD Azure](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Atualizações para a página My Apps juntamente com novos espaços de trabalho (Pré-visualização pública)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Minhas Apps  
**Capacidade do produto:** 3ª Integração do Partido

Agora pode personalizar a forma como os utilizadores da sua organização vêem e acedem à experiência renovada das Minhas Apps. Esta nova experiência inclui também a funcionalidade de novos espaços de trabalho, o que facilita a descoberta e organização de apps pelos utilizadores.

Para obter mais informações sobre a experiência das novas Minhas Apps e criar espaços de trabalho, consulte Criar espaços de [trabalho no portal My Apps](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Suporte de ID social da Google para colaboração Azure AD B2B (Disponibilidade Geral)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** B2B  
**Capacidade do produto:** Autenticação do Utilizador

Novo suporte para a utilização de IDs sociais do Google (contas Gmail) em Anúncio sacana do Azure ajuda a tornar a colaboração mais simples para os seus utilizadores e parceiros. Já não é necessário que os seus parceiros criem e gerem uma nova conta específica da Microsoft. O Microsoft Teams agora suporta totalmente os utilizadores do Google em todos os clientes e através dos pontos finais de autenticação comuns e relacionados com o arrendatário.

Para mais informações, consulte adicionar o Google como um fornecedor de [identidade para utilizadores convidados B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Suporte móvel microsoft edge para acesso condicional e entrada única (Disponibilidade Geral)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Acesso Condicional  
**Capacidade do produto:** Proteção de & de Segurança de Identidade

O Azure AD para o Microsoft Edge no iOS e Android suporta agora o Acesso Único e Único Do AD do Azure AD:

- **Microsoft Edge single sign-on (SSO):** O único sign-on está agora disponível em clientes nativos (como o Microsoft Outlook e o Microsoft Edge) para todas as aplicações ligadas ao Azure AD.

- **Acesso condicional do Microsoft Edge:** Através de políticas de acesso condicional baseadas em aplicações, os utilizadores devem utilizar navegadores protegidos pelo Microsoft Intune, como o Microsoft Edge.

Para obter mais informações sobre acesso condicional e SSO com o Microsoft Edge, consulte o Suporte Móvel microsoft Edge para acesso condicional e post de blog [single-on agora disponível.](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) Para obter mais informações sobre como configurar as suas aplicações de clientes utilizando [acesso condicional baseado em aplicativos](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) ou acesso condicional baseado em [dispositivos,](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)consulte Gerir o acesso à Web utilizando um navegador protegido por políticas [microsoft Intune](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Gestão de direitos da AD Azure (Disponibilidade Geral)

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Outros  
**Capacidade do produto:** Gestão de Direitos

A gestão de direitos da Azure AD é um novo recurso de governação de identidade, que ajuda as organizações a gerir a identidade e a aceder ao ciclo de vida em escala. Esta nova funcionalidade ajuda automatizando fluxos de trabalho de pedidos de acesso, atribuições de acesso, avaliações e expiração entre grupos, apps e sites SharePoint Online.

Com a gestão de direitos da Azure AD, pode gerir de forma mais eficiente o acesso tanto para os colaboradores como para utilizadores fora da sua organização que precisam de acesso a esses recursos.

Para mais informações, consulte o que é a gestão de [direitos da Azure AD?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizar o fornecimento de conta de utilizador para estas aplicações SaaS recentemente suportadas

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido  

Agora pode automatizar a criação, atualização e a eliminar as contas dos utilizadores destas aplicações recentemente integradas:

Serviço de [Autenticação de Identidade](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial)da Plataforma SAP Cloud , [RingCentral,](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial) [SpaceIQ,](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial) [Miro,](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial) [Cloudgate,](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial) [Infor CloudSuite,](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial) [OfficeSpace Software,](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial) [Priority Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Para obter mais informações sobre como proteger melhor a sua organização utilizando o fornecimento automatizado de conta de utilizador, consulte o fornecimento de [utilizadores automate para aplicações SaaS com AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Novas Aplicações Federadas disponíveis na galeria Azure AD App - novembro de 2019

**Tipo:** Nova funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** 3ª Integração do Partido

Em novembro de 2019, adicionámos estas 21 novas aplicações com apoio da Federação à galeria de aplicações:

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access for Members (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly,](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial) [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal,](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=) [NegometrixPortal Single Sign On (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp,](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279) [Motus,](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial) [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail,](https://loginself1.bluemail.me/) [Beedle,](https://teams-web.beedle.co/#/) [Visma,](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial) [OneDesk,](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial) [Foko Retail,](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial) [Qmarkets Idea & Innovation Management](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope User Authentication,](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial) [uniFLOW Online,](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial) [Claromentis,](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) [Jisc Student Voter Registration,](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial) [e4enable](https://portal.e4enable.com/)

Para obter mais informações sobre as aplicações, consulte [a integração da aplicação SaaS com o Azure Ative Directory](https://aka.ms/appstutorial). Para mais informações sobre a listagem da sua aplicação na galeria de aplicações Azure AD, consulte Lista da sua aplicação na galeria de [aplicações do Diretório Ativo Azure](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Galeria de aplicações da AD Azure nova e melhorada

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO

Atualizámos a galeria de aplicações da Azure AD para facilitar a sua descoberta de aplicações pré-integradas que suportam o provisionamento, OpenID Connect e SAML no seu inquilino azure Ative Directory.

Para mais informações, consulte Adicionar uma aplicação ao seu inquilino do [Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Limite de comprimento de definição de função de aplicação aumentado de 120 para 240 caracteres

**Tipo:** Mudança de funcionalidade  
**Categoria de serviço:** Aplicativos empresariais  
**Capacidade do produto:** SSO

Ouvimos dos clientes que o limite de comprimento para o valor de definição de função da aplicação em algumas apps e serviços é muito curto em 120 caracteres. Em resposta, aumentámos o comprimento máximo da definição de valor de papel para 240 caracteres.

Para obter mais informações sobre a utilização de definições de funções específicas para aplicações, consulte adicionar funções de [aplicação na sua aplicação e receba-as no token](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---
