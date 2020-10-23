---
title: Referência do guia de operações do Azure Ative Directory
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
ms.openlocfilehash: 2de3f78b58e10a4fbf65bb00d516448a089f85b6
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370955"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Referência do guia de operações do Azure Ative Directory

Esta secção do guia de referência de [operações Azure AD](active-directory-ops-guide-intro.md) descreve os controlos e ações que deve tomar para otimizar as operações gerais do Azure Ative Directory (Azure AD).

> [!NOTE]
> Estas recomendações estão em vigor a partir da data da publicação, mas podem mudar ao longo do tempo. As organizações devem avaliar continuamente as suas práticas operacionais à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir os proprietários a tarefas-chave

A Gestão do Diretório Ativo Azure requer a execução contínua de tarefas e processos operacionais fundamentais, que podem não fazer parte de um projeto de implantação. Ainda é importante que crie estas tarefas para otimizar o seu ambiente. As tarefas-chave e os seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Impulsionar melhorias na pontuação de segurança de identidade | Equipa de Operações InfoSec |
| Manter servidores de ligação AD Azure | Equipa de Operações do IAM |
| Execute e triagem regularmente Relatórios IdFix | Equipa de Operações do IAM |
| Triage Azure AD Connect Alertas de Saúde para Sync e AD FS | Equipa de Operações do IAM |
| Se não utilizar o Azure AD Connect Health, então o cliente tem processo e ferramentas equivalentes para monitorizar a infraestrutura personalizada | Equipa de Operações do IAM |
| Se não utilizar O FS AD, então o cliente tem processo e ferramentas equivalentes para monitorizar a infraestrutura personalizada | Equipa de Operações do IAM |
| Monitor Hybrid Logs: Azure AD App Proxy Connectors | Equipa de Operações do IAM |
| Monitor Hybrid Logs: Passthrough Authentication Agents | Equipa de Operações do IAM |
| Monitor De Registos Híbridos: Serviço de Writeback de Palavras-Passe | Equipa de Operações do IAM |
| Monitor Hybrid Logs: Gateway de proteção de senhas no local | Equipa de Operações do IAM |
| Monitor Hybrid Logs: Extensão Azure MFA NPS (se aplicável) | Equipa de Operações do IAM |

Ao rever a sua lista, poderá descobrir que precisa de atribuir um proprietário para tarefas que faltam a um proprietário ou ajustar a propriedade para tarefas com proprietários que não estejam alinhados com as recomendações acima.

#### <a name="owners-recommended-reading"></a>Proprietários recomendaram leitura

- [Atribuir funções de administrador no Azure Active Directory](../roles/permissions-reference.md)
- [Governação no Azure](../../governance/index.yml)

## <a name="hybrid-management"></a>Gestão híbrida

### <a name="recent-versions-of-on-premises-components"></a>Versões recentes de componentes no local

Ter as versões mais atualizadas de componentes no local fornece ao cliente todas as atualizações de segurança mais recentes, melhorias de desempenho, bem como funcionalidades que podem ajudar a simplificar ainda mais o ambiente. A maioria dos componentes tem uma configuração de atualização automática, que automatizará o processo de atualização.

Estes componentes incluem:

- Azure AD Connect
- Conectores de procuração de aplicação Azure AD
- Agentes de autenticação pass-through Azure AD
- Azure Ad Connect Agentes de Saúde

A menos que um tenha sido estabelecido, deverá definir um processo para atualizar estes componentes e confiar na funcionalidade de atualização automática sempre que possível. Se encontrar componentes com seis ou mais meses de atraso, deverá atualizar o mais rapidamente possível.

#### <a name="hybrid-management-recommended-reading"></a>Leitura recomendada de gestão híbrida

- [Azure AD Connect: atualização automática](../hybrid/how-to-connect-install-automatic-upgrade.md)
- [Compreenda os conectores Proxy da aplicação AD Azure / Atualizações automáticas](../manage-apps/application-proxy-connectors.md#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Linha de base de alerta de ligação de saúde Azure AD

As organizações devem implementar [a Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) para monitorização e reporte de Azure AD Connect e AD FS. Azure AD Connect e AD FS são componentes críticos que podem quebrar a gestão e autenticação do ciclo de vida e, portanto, levar a interrupções. O Azure AD Connect Health ajuda a monitorizar e obter informações sobre a sua infraestrutura de identidade no local, garantindo assim a fiabilidade do seu ambiente.

![Azure AD Connect Heath arquitetura](./media/active-directory-ops-guide/active-directory-ops-img16.png)

Ao monitorizar a saúde do seu ambiente, deve abordar imediatamente quaisquer alertas de alta gravidade, seguidos de alertas de menor gravidade.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Leitura recomendada para Azure AD Connect Health

- [Instalação do Agente do Azure AD Connect Health](../hybrid/how-to-connect-health-agent-install.md)

### <a name="on-premises-agents-logs"></a>Registos de agentes no local

Alguns serviços de gestão de identidade e acesso requerem agentes no local para permitir cenários híbridos. Exemplos incluem reset de palavra-passe, autenticação pass-through (PTA), Azure AD Application Proxy e extensão Azure MFA NPS. É fundamental que a equipa de operações faça a linha de base e monitorize a saúde destes componentes arquivando e analisando os registos dos componentes utilizando soluções como o System Center Operations Manager ou o SIEM. É igualmente importante que a sua equipa de Operações Infosec ou o balcão de ajuda compreendam como resolver padrões de erros.

#### <a name="on-premises-agents-logs-recommended-reading"></a>Registos de agentes no local recomendados de leitura

- [Resolver problemas do Proxy de Aplicações](../manage-apps/application-proxy-troubleshoot.md)
- [Autosserviço de senha reinicia resolução de problemas- Diretório Ativo Azure](../authentication/troubleshoot-sspr.md)
- [Compreenda os conectores Proxy de aplicação AD Azure](../manage-apps/application-proxy-connectors.md)
- [Azure AD Connect: Troubleshoot Pass-through Authentication](../hybrid/tshoot-connect-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs)
- [Códigos de erro de resolução de problemas para a extensão NPS do Azure MFA](../authentication/howto-mfa-nps-extension-errors.md)

### <a name="on-premises-agents-management"></a>Gestão de agentes no local

A adoção de boas práticas pode ajudar ao funcionamento ideal dos agentes no local. Considere as seguintes boas práticas:

- Recomenda-se que os conectores de procuração de aplicação AD multiple Azure por grupo de conector forneçam um equilíbrio de carga sem emenda e uma elevada disponibilidade, evitando pontos únicos de falha ao aceder às aplicações proxy. Se atualmente tem apenas um conector num grupo de conector que lida com aplicações em produção, deverá utilizar pelo menos dois conectores para redundância.
- A criação e utilização de um grupo de conector de aplicativos para fins de depuração pode ser útil para cenários de resolução de problemas e ao embarcar em novas aplicações no local. Recomendamos também a instalação de ferramentas de rede, tais como o Analisador de Mensagens e o Violinista nas máquinas de conector.
- Recomenda-se que vários agentes de autenticação pass-through ofereçam um equilíbrio de carga sem emenda e uma elevada disponibilidade, evitando um único ponto de falha durante o fluxo de autenticação. Certifique-se de que vai enviar pelo menos dois agentes de autenticação de passagem para despedimento.

#### <a name="on-premises-agents-management-recommended-reading"></a>Gestão recomendada de agentes no local

- [Compreenda os conectores Proxy de aplicação AD Azure](../manage-apps/application-proxy-connectors.md)
- [Autenticação pass-through Azure AD - arranque rápido](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Gestão em escala

### <a name="identity-secure-score"></a>Pontuação de segurança de identidade

A [pontuação de segurança de identidade](./identity-secure-score.md) fornece uma medida quantificável da postura de segurança da sua organização. É fundamental rever e abordar constantemente os resultados relatados e esforçar-se por ter a pontuação mais alta possível. A classificação ajuda a:

- Medir objetivamente a sua postura de segurança de identidade
- Planear melhorias de segurança de identidade
- Analisar o sucesso das suas melhorias

![Classificação de segurança](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Se a sua organização não tiver atualmente um programa em vigor para monitorizar as alterações na Pontuação De Segurança de Identidade, recomenda-se que implemente um plano e atribua aos proprietários para monitorizar e impulsionar ações de melhoria. As organizações devem remediar as ações de melhoria com um impacto de pontuação superior a 30 o mais rapidamente possível.

### <a name="notifications"></a>Notificações

A Microsoft envia comunicações por e-mail aos administradores para notificar várias alterações no serviço, atualizações de configuração que são necessárias e erros que requerem intervenção de administração. É importante que os clientes estabeleçam os endereços de e-mail de notificação para que as notificações sejam enviadas aos membros da equipa adequados que possam reconhecer e agir em todas as notificações. Recomendamos que adicione vários destinatários ao [Centro de Mensagens](/office365/admin/manage/message-center) e solicite que as notificações (incluindo notificações Azure AD Connect Health) sejam enviadas para uma lista de distribuição ou caixa de correio partilhada. Se tiver apenas uma conta de administração global com um endereço de e-mail, certifique-se de configurar pelo menos duas contas capazes de e-mail.

Existem dois endereços "From" utilizados pela Azure AD: <o365mc@email2.microsoft.com> , que envia notificações do Centro de Mensagens; e <azure-noreply@microsoft.com> , que envia notificações relacionadas com:

- [Comentários de acesso a Ad Azure](../governance/access-reviews-overview.md)
- [Azure AD Connect Health](../hybrid/how-to-connect-health-operations.md#enable-email-notifications)
- [Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-notifications.md)
- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-email-notifications.md)
- [Notificações de certificado de expiração da aplicação da empresa](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)
- Notificações de serviço de fornecimento de aplicativos da empresa

Consulte a tabela seguinte para saber o tipo de notificações enviadas e onde verificar:

| Fonte de notificação | O que é enviado | Onde verificar |
|:-|:-|:-|
| Contacto técnico | Erros de sincronização | Portal Azure - lâmina de propriedades |
| Centro de Mensagens | Avisos de incidentes e degradação dos Serviços de Identidade e serviços de backend da Microsoft 365 | Portal do Escritório |
| Proteção de Identidade Semanal Digest | Proteção de Identidade Digest | Lâmina de proteção de identidade Azure AD |
| Azure AD Connect Health | Notificações de alerta | Portal Azure - Azure AD Connect Health blade |
| Notificações de Aplicações empresariais | Notificações quando os certificados estão prestes a expirar e erros de provisionamento | Portal Azure - Lâmina de Aplicação Empresarial (cada aplicação tem a sua própria definição de endereço de e-mail) |

#### <a name="notifications-recommended-reading"></a>Notificações recomendadas de leitura

- [Altere o endereço da sua organização, contacto técnico e muito mais](/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Área de superfície operacional

### <a name="ad-fs-lockdown"></a>Bloqueio AD FS

As organizações, que configuram aplicações para autenticar diretamente para a Azure AD beneficiam do [bloqueio inteligente AZURE AD](../authentication/concept-sspr-howitworks.md). Se utilizar O FS AD no Windows Server 2012 R2, implemente [a proteção de bloqueio de extranet](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)AD FS . Se utilizar O FS AD no Windows Server 2016 ou mais tarde, implemente [o bloqueio inteligente da extranet](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). No mínimo, recomendamos que permita que o bloqueio da extranet contenha o risco de ataques de força bruta contra o Ative Directory no local. No entanto, se tiver AD FS no Windows 2016 ou superior, também deverá ativar o bloqueio inteligente da extranet que ajudará a mitigar os ataques [de spray de palavra-passe.](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

Se o AD FS for utilizado apenas para a federação Azure AD, existem alguns pontos finais que podem ser desligados para minimizar a área da superfície de ataque. Por exemplo, se o FS AD for utilizado apenas para o Azure AD, deve desativar WS-Trust pontos finais que não sejam os pontos finais ativados para **o utilizador** e **o transporte de janelas.**

### <a name="access-to-machines-with-on-premises-identity-components"></a>Acesso a máquinas com componentes de identidade no local

As organizações devem bloquear o acesso às máquinas com componentes híbridos no local, da mesma forma que o seu domínio no local. Por exemplo, um operador de backup ou administrador Hiper-V não deve ser capaz de iniciar sessão no Azure AD Connect Server para alterar as regras.

O modelo de nível administrativo ative directory foi concebido para proteger os sistemas de identidade utilizando um conjunto de zonas tampão entre o controlo total do Ambiente (Nível 0) e os ativos de estação de trabalho de alto risco que os atacantes frequentemente comprometem. ![Diagrama que mostra as três camadas do modelo de Camada](./media/active-directory-ops-guide/active-directory-ops-img18.png)

O [modelo de nível](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) é composto por três níveis e inclui apenas contas administrativas, não contas padrão de utilizador.

- **Camada 0** – Controlo direto de identidades da empresa no ambiente. A Camada 0 inclui contas, grupos e outros recursos que têm controlo administrativo direto ou indireto da floresta, dos domínios ou dos controladores de domínio do Active Directory e todos os elementos no mesmo. A sensibilidade de segurança de todos os ativos da Camada 0 é equivalente, uma vez que têm controlo uns sobre os outros.
- **Camada 1** – Controlo de aplicações e servidores da empresa. Os ativos da Camada 1 incluem sistemas operativos de servidor, serviços em nuvem e aplicações empresariais. As contas de administrador da Camada 1 têm controlo administrativo de uma quantidade significativa de valor de negócios alojado nestes ativos. Uma função de exemplo comum são os administradores do servidor que realizam a manutenção destes sistemas operativos e são capazes de afetar todos os serviços da empresa.
- **Camada 2** – Controlo dos dispositivos e das estações de trabalho do utilizador. As contas de administrador da Camada 2 têm controlo administrativo de uma quantidade significativa de valor de negócios alojado em dispositivos e estações de trabalho do utilizador. Os exemplos incluem administradores de Suporte Técnico e suporte de computador pois podem afetar a integridade dos dados de praticamente qualquer utilizador.

Bloqueie o acesso a componentes de identidade no local, tais como Azure AD Connect, AD FS e serviços SQL da mesma forma que faz para controladores de domínio.

## <a name="summary"></a>Resumo

Há sete aspetos para uma infraestrutura de identidade segura. Esta lista irá ajudá-lo a encontrar as ações que deve tomar para otimizar as operações para o Azure Ative Directory (Azure AD).

- Atribua os proprietários a tarefas-chave.
- Automatizar o processo de atualização para componentes híbridos no local.
- Implementar Azure AD Connect Health para monitorização e reporte de Azure AD Connect e AD FS.
- Monitorize a saúde dos componentes híbridos no local, arquivando e analisando os registos de componentes utilizando o System Center Operations Manager ou uma solução SIEM.
- Implemente melhorias de segurança medindo a sua postura de segurança com a Identity Secure Score.
- Bloqueie o AD FS.
- Bloqueie o acesso a máquinas com componentes de identidade no local.

## <a name="next-steps"></a>Passos seguintes

Consulte os [planos de implementação do AD Azure](active-directory-deployment-plans.md) para obter detalhes de implementação sobre quaisquer capacidades que não tenha implementado.