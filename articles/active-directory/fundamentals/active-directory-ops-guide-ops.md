---
title: Referência do guia de operações do Diretório Ativo Azure
description: Este guia de referência de operações descreve os controlos e ações que deve tomar para garantir operações gerais
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
ms.openlocfilehash: d039373d3e70076149da2b970a234b59d7aa661a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422951"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Referência do guia de operações do Diretório Ativo Azure

Esta secção do guia de referência de operações da [AD Azure](active-directory-ops-guide-intro.md) descreve as verificações e ações que deve tomar para otimizar as operações gerais do Azure Ative Directory (Azure AD).

> [!NOTE]
> Estas recomendações estão em vigor a partir da data da publicação, mas podem mudar ao longo do tempo. As organizações devem avaliar continuamente as suas práticas operacionais à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir proprietários a tarefas-chave

A gestão do Diretório Ativo azure requer a execução contínua de tarefas e processos operacionais fundamentais, que podem não fazer parte de um projeto de lançamento. Ainda é importante que você configuraestas tarefas para otimizar o seu ambiente. As tarefas-chave e os seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Melhorias de unidade na pontuação segura de identidade | Equipa de Operações infosec |
| Mantenha os servidores de ligação ad azure | Equipa de Operações do IAM |
| Executar e triagem regularmente IdFix Reports | Equipa de Operações do IAM |
| Triage Azure AD Liga alertas de saúde para Sync e AD FS | Equipa de Operações do IAM |
| Se não utilizar a Azure AD Connect Health, então o cliente tem processo e ferramentas equivalentes para monitorizar infraestruturas personalizadas | Equipa de Operações do IAM |
| Se não usar AD FS, então o cliente tem processo e ferramentas equivalentes para monitorizar infraestruturas personalizadas | Equipa de Operações do IAM |
| Monitor Registos Híbridos: Ligadores proxy de aplicação ad azure | Equipa de Operações do IAM |
| Monitor Registos Híbridos: Agentes de Autenticação Passthrough | Equipa de Operações do IAM |
| Monitor Registos Híbridos: Serviço de Reversão de Palavra-Passe | Equipa de Operações do IAM |
| Monitor Registos Híbridos: Gateway de proteção de senhano no local | Equipa de Operações do IAM |
| Monitor Registos Híbridos: Extensão De NPS Azure MFA (se aplicável) | Equipa de Operações do IAM |

Ao rever a sua lista, poderá descobrir que precisa de atribuir um proprietário a tarefas que faltam a um proprietário ou ajustar a propriedade para tarefas com proprietários que não estejam alinhados com as recomendações acima.

#### <a name="owners-recommended-reading"></a>Proprietários recomendado leitura

- [Atribuir funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governação no Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="hybrid-management"></a>Gestão híbrida

### <a name="recent-versions-of-on-premises-components"></a>Versões recentes de componentes no local

Ter as versões mais atualizadas dos componentes no local fornece ao cliente todas as mais recentes atualizações de segurança, melhorias de desempenho e funcionalidade que poderia ajudar a simplificar ainda mais o ambiente. A maioria dos componentes tem uma definição de atualização automática, que irá automatizar o processo de atualização.

Estes componentes incluem:

- Azure AD Connect
- Conectores de proxy de aplicação da AD Azure
- Agentes de autenticação pass-through da Azure AD
- Agentes de saúde Azure AD Connect

A menos que tenha sido estabelecido, deve definir um processo para atualizar estes componentes e confiar na funcionalidade de atualização automática sempre que possível. Se encontrar componentes com seis ou mais meses de atraso, deverá fazer o upgrade o mais rapidamente possível.

#### <a name="hybrid-management-recommended-reading"></a>Leitura recomendada pela gestão híbrida

- [Azure AD Connect: atualização automática](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)
- [Compreender os conectores de procuração de aplicação da AD Azure [ Atualizações automáticas](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Linha de base de alerta azure AD Connect Health

As organizações devem implementar a [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#what-is-azure-ad-connect-health) para monitorização e reporte de Azure AD Connect e AD FS. Azure AD Connect e AD FS são componentes críticos que podem quebrar a gestão e autenticação do ciclo de vida e, portanto, levar a interrupções. A Azure AD Connect Health ajuda a monitorizar e a obter informações sobre a sua infraestrutura de identidade no local, garantindo assim a fiabilidade do seu ambiente.

![Azure AD Connect Heath arquitetura](./media/active-directory-ops-guide/active-directory-ops-img16.png)

Ao monitorizar a saúde do seu ambiente, deve abordar imediatamente quaisquer alertas de alta gravidade, seguidos de alertas de menor gravidade.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Leitura recomendada pela Azure AD Connect Health

- [Instalação do Agente do Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

### <a name="on-premises-agents-logs"></a>Registos de agentes no local

Alguns serviços de gestão de identidade e acesso requerem agentes no local para permitir cenários híbridos. Exemplos incluem reset de palavra-passe, autenticação pass-through (PTA), Procuração de Aplicação AD Azure e extensão de NPS Azure MFA. É fundamental que a equipa de operações acompanhe e monitorize a saúde destes componentes arquivando e analisando os registos do agente componente utilizando soluções como o System Center Operations Manager ou o SIEM. É igualmente importante que a sua equipa de Operações infosec ou o seu balcão de ajuda compreendam como resolver os padrões de erros.

#### <a name="on-premises-agents-logs-recommended-reading"></a>Registos de agentes no local recomendados de leitura

- [Resolver problemas do Proxy de Aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)
- [Auto-service password reset troubleshooting - Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
- [Compreender os conectores de procuração de aplicação da AD Azure](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
- [Azure AD Connect: Aautenticação pass-through de sessão de problemas](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication#collecting-pass-through-authentication-agent-logs)
- [Códigos de erro de resolução de problemas para a extensão do NPS Do MFA Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-errors)

### <a name="on-premises-agents-management"></a>Gestão de agentes no local

A adoção de boas práticas pode ajudar ao ótimo funcionamento dos agentes no local. Considere as seguintes boas práticas:

- Recomenda-se que os conectores proxy de aplicação de aplicação AD de múltiplos azure por grupo de conectores forneçam um equilíbrio de carga sem emenda e uma elevada disponibilidade, evitando pontos únicos de falha ao aceder às aplicações de procuração. Se tem atualmente apenas um conector num grupo de conectores que trata as aplicações em produção, deverá utilizar pelo menos dois conectores para despedimento.
- Criar e utilizar um grupo de conector proxy de aplicações para fins de depuração pode ser útil para cenários de resolução de problemas e ao embarcar em novas aplicações no local. Recomendamos também a instalação de ferramentas de rede como o Analisador de Mensagens e o Violinista nas máquinas de conector.
- Recomenda-se que vários agentes de autenticação pass-through forneçam um equilíbrio de carga sem emenda e uma elevada disponibilidade, evitando um único ponto de falha durante o fluxo de autenticação. Certifique-se de que vai colocar pelo menos dois agentes de autenticação pass-through para despedimento.

#### <a name="on-premises-agents-management-recommended-reading"></a>Leitura recomendada por agentes no local

- [Compreender os conectores de procuração de aplicação da AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors)
- [Autenticação pass-through Azure AD - quickstart](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Gestão à escala

### <a name="identity-secure-score"></a>Pontuação de segurança de identidade

A [pontuação segura](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score) de identidade fornece uma medida quantificável da postura de segurança da sua organização. É fundamental rever e abordar constantemente as conclusões reportadas e esforçar-se por ter a pontuação mais alta possível. A classificação ajuda a:

- Medir objetivamente a sua postura de segurança de identidade
- Planear melhorias de segurança de identidade
- Analisar o sucesso das suas melhorias

![Classificação de segurança](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Se a sua organização não tem atualmente nenhum programa em vigor para monitorizar alterações na Pontuação Segura de Identidade, recomenda-se que implemente um plano e atribua aos proprietários para monitorizar e impulsionar ações de melhoria. As organizações devem remediar as ações de melhoria com um impacto de pontuação superior a 30 o mais rapidamente possível.

### <a name="notifications"></a>Notificações

A Microsoft envia comunicações de e-mail aos administradores para notificar várias alterações no serviço, atualizações de configuração que são necessárias e erros que requerem intervenção administrativa. É importante que os clientes estabeleçam os endereços de e-mail de notificação para que as notificações sejam enviadas aos membros da equipa adequados que possam reconhecer e agir sobre todas as notificações. Recomendamos que adicione vários destinatários ao Centro de [Mensagens do Office 365](https://docs.microsoft.com/office365/admin/manage/message-center) e solicite que as notificações (incluindo notificações azure AD Connect Health) sejam enviadas para uma lista de distribuição ou caixa de correio partilhada. Se tiver apenas uma conta de administração global com um endereço de e-mail, certifique-se de configurar pelo menos duas contas com capacidade para o e-mail.

Existem dois endereços "From" utilizados <o365mc@email2.microsoft.com>pela Azure AD: , que envia notificações do Office 365 Message Center; e, <azure-noreply@microsoft.com>que envia notificações relacionadas com:

- [Avaliações de acesso a Anúncio saqueadas](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)
- [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations#enable-email-notifications)
- [Azure AD Identity Protection](/azure/active-directory/identity-protection/howto-identity-protection-configure-notifications)
- [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)
- [Notificações de certificados de caducidade da aplicação da empresa](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on#add-email-notification-addresses-for-certificate-expiration)
- Notificações de serviços de provisionamento de aplicações empresariais

Consulte a tabela seguinte para saber o tipo de notificações que são enviadas e onde verificar:

| Fonte de notificação | O que é enviado | Onde verificar |
|:-|:-|:-|
| Contacto técnico | Erros de sincronização | Portal Azure - lâmina de propriedades |
| Centro de Mensagens do Office 365 | Avisos de incidentes e degradação dos Serviços de Identidade e serviços de apoio O365 | Portal do Escritório |
| Digestão semanal de proteção de identidade | Proteção de Identidade Digest | Lâmina de proteção de identidade Azure AD |
| Azure AD Connect Health | Notificações de alerta | Portal Azure - Azure AD Connect Health blade |
| Notificações de Aplicações Empresariais | Notificações quando os certificados estão prestes a expirar e erros de provisionamento | Portal Azure - Lâmina de aplicação empresarial (cada aplicação tem a sua própria definição de endereço de e-mail) |

#### <a name="notifications-recommended-reading"></a>Leitura recomendada para notificações

- [Altere o endereço da sua organização, contacto técnico e muito mais - Office 365](https://docs.microsoft.com/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Área de superfície operacional

### <a name="ad-fs-lockdown"></a>Bloqueio aD FS

As organizações, que configuram aplicações para autenticar diretamente o Azure AD beneficiam do [bloqueio inteligente da Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Se utilizar AD FS no Windows Server 2012 R2, implemente a proteção de [bloqueio extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)AD FS . Se utilizar AD FS no Windows Server 2016 ou mais tarde, implemente o [bloqueio inteligente extranet](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). No mínimo, recomendamos que permita que o bloqueio extranet contenha o risco de ataques de força bruta contra o Diretório Ativo no local. No entanto, se tiver AD FS no Windows 2016 ou superior, também deverá ativar o bloqueio inteligente extranet que ajudará a mitigar os ataques de spray de [senha.](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

Se a AD FS for apenas usada para a federação de Anúncios Azure, existem alguns pontos finais que podem ser desligados para minimizar a área da superfície de ataque. Por exemplo, se o AD FS for utilizado apenas para a AD Azure, deve desativar os pontos finais da WS-Trust para além dos pontos finais ativados para o **username e** **o windowstransport**.

### <a name="access-to-machines-with-on-premises-identity-components"></a>Acesso a máquinas com componentes de identidade no local

As organizações devem bloquear o acesso às máquinas com componentes híbridos no local da mesma forma que o seu domínio no local. Por exemplo, um operador de reserva ou um administrador Hyper-V não devem poder iniciar sessão no Azure AD Connect Server para alterar as regras.

O modelo de nível administrativo ative Diretório foi concebido para proteger os sistemas de identidade utilizando um conjunto de zonas tampão entre o controlo total do Ambiente (Nível 0) e os ativos de estação de trabalho de alto risco que os atacantes frequentemente comprometem. ![Diagrama que mostra as três camadas do modelo de Camada](./media/active-directory-ops-guide/active-directory-ops-img18.png)

O [modelo de nível](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) é composto por três níveis e inclui apenas contas administrativas, não contas de utilizador padrão.

- **Nível 0** - Controlo Direto das identidades empresariais no ambiente. A Camada 0 inclui contas, grupos e outros recursos que têm controlo administrativo direto ou indireto da floresta, dos domínios ou dos controladores de domínio do Active Directory e todos os elementos no mesmo. A sensibilidade de segurança de todos os ativos da Camada 0 é equivalente, uma vez que têm controlo uns sobre os outros.
- **Nível 1** - Controlo de servidores e aplicações empresariais. Os ativos da Camada 1 incluem sistemas operativos de servidor, serviços em nuvem e aplicações empresariais. As contas de administrador da Camada 1 têm controlo administrativo de uma quantidade significativa de valor de negócios alojado nestes ativos. Uma função de exemplo comum são os administradores do servidor que realizam a manutenção destes sistemas operativos e são capazes de afetar todos os serviços da empresa.
- **Nível 2** - Controlo dos postos de trabalho e dispositivos do utilizador. As contas de administrador da Camada 2 têm controlo administrativo de uma quantidade significativa de valor de negócios alojado em dispositivos e estações de trabalho do utilizador. Os exemplos incluem administradores de Suporte Técnico e suporte de computador pois podem afetar a integridade dos dados de praticamente qualquer utilizador.

Bloqueie o acesso a componentes de identidade no local, tais como serviços Azure AD Connect, AD FS e SQL da mesma forma que para os controladores de domínio.

## <a name="summary"></a>Resumo

Há sete aspetos para uma infraestrutura de identidade segura. Esta lista irá ajudá-lo a encontrar as ações que deve tomar para otimizar as operações para o Azure Ative Directory (Azure AD).

- Atribuir os proprietários a tarefas-chave.
- Automatizar o processo de atualização para componentes híbridos no local.
- Implemente a Azure AD Connect Health para monitorização e reporte de Azure AD Connect e AD FS.
- Monitorize a saúde dos componentes híbridos no local arquivando e analisando os registos do agente componente utilizando o System Center Operations Manager ou uma solução SIEM.
- Implemente melhorias de segurança medindo a sua postura de segurança com Pontuação Segura de Identidade.
- Bloqueie a AD FS.
- Bloqueie o acesso a máquinas com componentes de identidade no local.

## <a name="next-steps"></a>Passos seguintes

Consulte os planos de implementação da [AD Azure](active-directory-deployment-plans.md) para detalhes de implementação sobre quaisquer capacidades que não tenha implementado.
