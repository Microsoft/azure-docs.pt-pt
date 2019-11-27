---
title: Referência geral do guia de operações do Azure Active Directory
description: Este guia de referência de operações descreve as verificações e ações que você deve executar para proteger as operações gerais
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
ms.openlocfilehash: 46e5af9d54cf818366bd2730de0da85dcbe6cade
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535304"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Referência geral do guia de operações do Azure Active Directory

Esta seção do [Guia de referência de operações do Azure ad](active-directory-ops-guide-intro.md) descreve as verificações e as ações que você deve seguir para otimizar as operações gerais do Azure Active Directory (AD do Azure).

> [!NOTE]
> Essas recomendações são atuais a partir da data de publicação, mas podem mudar ao longo do tempo. As organizações devem avaliar continuamente suas práticas operacionais à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir proprietários a tarefas principais

O gerenciamento de Azure Active Directory requer a execução contínua das principais tarefas e processos operacionais, que podem não fazer parte de um projeto de distribuição. Ainda é importante que você configure essas tarefas para otimizar seu ambiente. As principais tarefas e seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Aprimoramentos de unidade na pontuação de segurança de identidade | Equipe de operações do batalha |
| Manter servidores Azure AD Connect | Equipe de operações IAM |
| Executar e fazer triagem regularmente dos relatórios do IdFix | Equipe de operações IAM |
| Alertas do Azure AD Connect Health de triagem para sincronização e AD FS | Equipe de operações IAM |
| Se não estiver usando Azure AD Connect Health, o cliente terá o processo equivalente e as ferramentas para monitorar a infraestrutura personalizada | Equipe de operações IAM |
| Se não estiver usando AD FS, o cliente terá o processo equivalente e as ferramentas para monitorar a infraestrutura personalizada | Equipe de operações IAM |
| Monitorar logs híbridos: conectores de proxy de Aplicativo Azure AD | Equipe de operações IAM |
| Monitorar logs híbridos: agentes de autenticação de passagem | Equipe de operações IAM |
| Monitorar logs híbridos: serviço de write-back de senha | Equipe de operações IAM |
| Monitorar logs híbridos: gateway de proteção de senha local | Equipe de operações IAM |
| Monitorar logs híbridos: extensão NPS do Azure MFA (se aplicável) | Equipe de operações IAM |

Ao examinar sua lista, você pode achar necessário atribuir um proprietário para tarefas que não têm um proprietário ou ajustar a propriedade para tarefas com proprietários que não estão alinhadas com as recomendações acima.

#### <a name="owners-recommended-reading"></a>Leitura recomendada de proprietários

- [Atribuir funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governação no Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="hybrid-management"></a>Gerenciamento híbrido

### <a name="recent-versions-of-on-premises-components"></a>Versões recentes de componentes locais

Ter as versões mais atualizadas dos componentes locais fornece ao cliente todas as atualizações de segurança mais recentes, melhorias de desempenho, bem como funcionalidades que podem ajudar a simplificar ainda mais o ambiente. A maioria dos componentes tem uma configuração de atualização automática, que automatizará o processo de atualização.

Esses componentes incluem:

- Azure AD Connect
- Conectores de Proxy de Aplicativo do AD do Azure
- Agentes de autenticação de passagem do Azure AD
- Agentes de Azure AD Connect Health

A menos que um tenha sido estabelecido, você deve definir um processo para atualizar esses componentes e contar com o recurso de atualização automática sempre que possível. Se você encontrar componentes que tenham seis ou mais meses atrás, você deverá atualizar o mais rápido possível.

#### <a name="hybrid-management-recommended-reading"></a>Leitura recomendada de gerenciamento híbrido

- [Azure AD Connect: atualização automática](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)
- [Entender os conectores de Proxy de Aplicativo do AD do Azure | Atualizações automáticas](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Linha de base de alerta Azure AD Connect Health

As organizações devem implantar [Azure ad Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#what-is-azure-ad-connect-health) para monitoramento e emissão de relatórios de Azure AD Connect e AD FS. Azure AD Connect e AD FS são componentes críticos que podem interromper o gerenciamento e a autenticação do ciclo de vida e, portanto, levam a interrupções. Azure AD Connect Health ajuda a monitorar e obter informações sobre sua infraestrutura de identidade local, garantindo assim a confiabilidade do seu ambiente.

![Arquitetura integridade de Azure AD Connect](./media/active-directory-ops-guide/active-directory-ops-img16.png)

Ao monitorar a integridade do seu ambiente, você deve resolver imediatamente todos os alertas de severidade alta, seguidos de alertas de severidade mais baixos.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health leitura recomendada

- [Instalação do Agente do Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

### <a name="on-premises-agents-logs"></a>Logs de agentes locais

Alguns serviços de gerenciamento de identidade e acesso exigem agentes locais para habilitar cenários híbridos. Os exemplos incluem redefinição de senha, PTA (autenticação de passagem), Azure Proxy de Aplicativo do AD e extensão NPS do Azure MFA. É fundamental que a linha de base da equipe de operações e monitore a integridade desses componentes, arquivando e analisando os logs do agente de componentes usando soluções como o System Center Operations Manager ou o SIEM. É igualmente importante que sua equipe de operações do batalha ou suporte técnico entenda como solucionar problemas de padrões de erros.

#### <a name="on-premises-agents-logs-recommended-reading"></a>Leitura recomendada de logs de agentes locais

- [Solucionar problemas de proxy de aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)
- [Solução de problemas de redefinição de senha por autoatendimento-Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
- [Entender os conectores de Proxy de Aplicativo do AD do Azure](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
- [Azure AD Connect: solucionar problemas de autenticação de passagem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication#collecting-pass-through-authentication-agent-logs)
- [Solucionar problemas de códigos de erro para a extensão NPS do Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-errors)

### <a name="on-premises-agents-management"></a>Gerenciamento de agentes locais

A adoção de práticas recomendadas pode ajudar na operação ideal de agentes locais. Considere as seguintes práticas recomendadas:

- Vários conectores de proxy de aplicativo do Azure AD por grupo de conectores são recomendados para fornecer balanceamento de carga e alta disponibilidade contínuos, evitando pontos únicos de falha ao acessar os aplicativos de proxy. Se você tiver atualmente apenas um conector em um grupo de conectores que lide com aplicativos em produção, você deve implantar pelo menos dois conectores para redundância.
- Criar e usar um grupo de conectores de proxy de aplicativo para fins de depuração pode ser útil para cenários de solução de problemas e ao integrar novos aplicativos locais. Também recomendamos a instalação de ferramentas de rede, como o analisador de mensagens e o Fiddler nos computadores do conector.
- Vários agentes de autenticação de passagem são recomendados para fornecer balanceamento de carga e alta disponibilidade contínuos, evitando o ponto único de falha durante o fluxo de autenticação. Certifique-se de implantar pelo menos dois agentes de autenticação de passagem para redundância.

#### <a name="on-premises-agents-management-recommended-reading"></a>Leitura recomendada do gerenciamento de agentes locais

- [Entender os conectores de Proxy de Aplicativo do AD do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors)
- [Autenticação de passagem do Azure AD – início rápido](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start#step-5-ensure-high-availability)

## <a name="management-at-scale"></a>Gerenciamento em escala

### <a name="identity-secure-score"></a>Pontuação de segurança de identidade

A [Pontuação](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score) de segurança de identidade fornece uma medida quantificável da postura de segurança da sua organização. É importante revisar constantemente e endereçar as descobertas relatadas e se esforçar para ter a pontuação mais alta possível. A classificação ajuda a:

- Medir objetivamente a sua postura de segurança de identidade
- Planear melhorias de segurança de identidade
- Analisar o sucesso das suas melhorias

![Classificação de segurança](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Se sua organização atualmente não tem nenhum programa em vigor para monitorar as alterações na pontuação de segurança de identidade, é recomendável implementar um plano e atribuir proprietários para monitorar e impulsionar ações de aperfeiçoamento. As organizações devem corrigir as ações de aperfeiçoamento com um impacto de pontuação maior que 30 assim que possível.

### <a name="notifications"></a>Notificações

A Microsoft envia comunicações por email aos administradores para notificar várias alterações no serviço, as atualizações de configuração necessárias e os erros que exigem a intervenção do administrador. É importante que os clientes definam os endereços de email de notificação para que as notificações sejam enviadas aos membros apropriados da equipe que podem reconhecer e agir sobre todas as notificações. Recomendamos que você adicione vários destinatários ao [centro de mensagens do Office 365](https://docs.microsoft.com/office365/admin/manage/message-center) e solicite que as notificações (incluindo notificações de Azure ad Connect Health) sejam enviadas para uma lista de distribuição ou caixa de correio compartilhada. Se você tiver apenas uma conta de administrador global com um endereço de email, certifique-se de configurar pelo menos duas contas compatíveis com email.

Há dois endereços "de" usados pelo Azure AD: <o365mc@email2.microsoft.com>, que envia notificações do centro de mensagens do Office 365; e <azure-noreply@microsoft.com>, que envia notificações relacionadas a:

- [Revisões de acesso do Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)
- [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations#enable-email-notifications)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/notifications)
- [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)
- [Notificações de certificado de expiração do aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on#add-email-notification-addresses-for-certificate-expiration)
- Notificações serviço de aprovisionamento da aplicação do Enterprise

Consulte a tabela a seguir para saber o tipo de notificações que são enviadas e onde verificá-las:

| Origem da notificação | O que é enviado | Onde verificar |
|:-|:-|:-|
| Contato técnico | Erros de sincronização | Folha portal do Azure-Propriedades |
| Centro de mensagens do Office 365 | Avisos de degradação e incidentes de serviços de identidade e serviços de back-end do O365 | Portal do Office |
| Resumo semanal da proteção de identidade | Resumo da proteção de identidade | Folha Azure AD Identity Protection |
| Azure AD Connect Health | Notificações de alerta | Folha portal do Azure Azure AD Connect Health |
| Notificações de aplicativos empresariais | Notificações quando os certificados estão prestes a expirar e erros de provisionamento | Folha de aplicativos portal do Azure-Enterprise (cada aplicativo tem sua própria configuração de endereço de email) |

#### <a name="notifications-recommended-reading"></a>Leitura recomendada para notificações

- [Altere o endereço da sua organização, o contato técnico e muito mais-Office 365](https://docs.microsoft.com/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Área de superfície operacional

### <a name="ad-fs-lockdown"></a>Bloqueio de AD FS

Organizações, que configuram aplicativos para autenticar diretamente no Azure AD se beneficiam do [bloqueio inteligente do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Se você usar AD FS no Windows Server 2012 R2, implemente AD FS [proteção contra bloqueio de extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Se você usar AD FS no Windows Server 2016 ou posterior, implemente o [bloqueio inteligente da extranet](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). No mínimo, recomendamos que você habilite o bloqueio de extranet para conter o risco de ataques de força bruta contra Active Directory locais. No entanto, se você tiver AD FS no Windows 2016 ou superior, habilite também o bloqueio inteligente de extranet que ajudará a reduzir os ataques de [irrigação de senha](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/) .

Se AD FS for usado somente para a Federação do Azure AD, há alguns pontos de extremidade que podem ser desativados para minimizar a área da superfície de ataque. Por exemplo, se AD FS for usado somente para o Azure AD, você deverá desabilitar pontos de extremidade WS-Trust diferentes dos pontos de extremidade habilitados para **usernamemixed** e **windowstransport**.

### <a name="access-to-machines-with-on-premises-identity-components"></a>Acesso a computadores com componentes de identidade local

As organizações devem bloquear o acesso aos computadores com componentes híbridos locais da mesma maneira que o seu domínio local. Por exemplo, um operador de backup ou administrador do Hyper-V não deve ser capaz de fazer logon no servidor de Azure AD Connect para alterar as regras.

O modelo de camada administrativa Active Directory foi projetado para proteger sistemas de identidade usando um conjunto de zonas de buffer entre controle total do ambiente (camada 0) e os ativos de estação de trabalho de alto risco que os invasores freqüentemente comprometem. ![Diagrama mostrando as três camadas do modelo de camada](./media/active-directory-ops-guide/active-directory-ops-img18.png)

O [modelo de camada](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) é composto de três níveis e inclui apenas contas administrativas, não contas de usuário padrão.

- **Camada 0** -controle direto de identidades corporativas no ambiente. A camada 0 inclui contas, grupos e outros ativos que têm controle administrativo direto ou indireto do Active Directory floresta, domínios ou controladores de domínio e todos os ativos. A sensibilidade de segurança de todos os ativos da camada 0 é equivalente, pois eles são todos efetivamente no controle.
- **Camada 1** -controle de aplicativos e servidores corporativos. Os ativos da camada 1 incluem sistemas operacionais de servidor, serviços de nuvem e aplicativos empresariais. As contas de administrador da camada 1 têm controle administrativo de uma quantidade significativa de valor comercial que é hospedada nesses ativos. Uma função de exemplo comum são os administradores de servidor que mantêm esses sistemas operacionais com a capacidade de afetar todos os serviços corporativos.
- **Camada 2** -controle de estações de trabalho e dispositivos do usuário. As contas de administrador da camada 2 têm controle administrativo de uma quantidade significativa de valor comercial que é hospedada em estações de trabalho e dispositivos do usuário. Os exemplos incluem administradores de suporte técnico e de computador, pois eles podem afetar a integridade de praticamente qualquer dado de usuário.

Bloqueie o acesso a componentes de identidade locais, como Azure AD Connect, AD FS e serviços SQL da mesma maneira como você faz para controladores de domínio.

## <a name="summary"></a>Resumo

Há sete aspectos para uma infraestrutura de identidade segura. Essa lista o ajudará a encontrar as ações que você deve executar para otimizar as operações do Azure Active Directory (AD do Azure).

- Atribuir proprietários a tarefas principais.
- Automatize o processo de atualização para componentes híbridos locais.
- Implante Azure AD Connect Health para monitoramento e relatório de Azure AD Connect e AD FS.
- Monitore a integridade de componentes híbridos locais arquivando e analisando os logs do agente de componentes usando System Center Operations Manager ou uma solução SIEM.
- Implemente melhorias de segurança medindo sua postura de segurança com a pontuação segura de identidade.
- Bloquear AD FS.
- Bloquear o acesso a computadores com componentes de identidade local.

## <a name="next-steps"></a>Passos Seguintes

Consulte os [planos de implantação do Azure ad](active-directory-deployment-plans.md) para obter detalhes de implementação sobre qualquer recurso que você não tenha implantado.
