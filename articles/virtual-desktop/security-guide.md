---
title: Práticas de segurança do Windows Virtual Desktop - Azure
description: As melhores práticas para manter o ambiente de ambiente de trabalho virtual do Windows seguro.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5330c427088734b049b2cb4f7735ac0099a52b47
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82966678"
---
# <a name="security-best-practices"></a>Melhores práticas de segurança

O Windows Virtual Desktop é um serviço de desktop virtual gerido que inclui muitas capacidades de segurança para manter a sua organização segura. Numa implementação do Windows Virtual Desktop, a Microsoft gere partes dos serviços em nome do cliente. O serviço tem muitas funcionalidades de segurança avançadas incorporadas, como o Reverse Connect, que reduzem o risco envolvido em ter desktops remotos acessíveis a partir de qualquer lugar.

Este artigo descreve passos adicionais que pode tomar como administrador para manter as implementações do Windows Virtual Desktop dos seus clientes seguras.

## <a name="security-responsibilities"></a>Responsabilidades de segurança

O que torna os serviços em nuvem diferentes das tradicionais infraestruturas de ambiente de trabalho virtuais (VDIs) é a forma como lidam com as responsabilidades de segurança. Por exemplo, num VDI tradicional no local, o cliente seria responsável por todos os aspetos da segurança. No entanto, na maioria dos serviços na nuvem, estas responsabilidades são partilhadas entre o cliente e a empresa.

Quando utiliza o Windows Virtual Desktop, é importante entender que, embora alguns componentes estejam já protegidos para o seu ambiente, terá de configurar outras áreas para se adaptar às necessidades de segurança da sua organização.

Aqui estão as necessidades de segurança pelas quais é responsável na implementação do Windows Virtual Desktop:

| Necessidade de segurança | O cliente é responsável por isto? |
|---------------|:-------------------------:|
|Identidade|Sim|
|Dispositivos de utilizador (mobile e PC)|Sim|
|Segurança da aplicação|Sim|
|Sessão anfitrião OS|Sim|
|Configuração de implementação|Sim|
|Controlos de rede|Sim|
|Plano de controlo de virtualização|Não|
|Anfitriões físicos|Não|
|Rede física|Não|
|Datacenter físico|Não|

As necessidades de segurança pelas quais o cliente não é responsável são tratadas pela Microsoft.

## <a name="azure-security-best-practices"></a>Boas práticas de segurança azure

O Windows Virtual Desktop é um serviço no Azure. Para maximizar a segurança da sua implementação no Windows Virtual Desktop, deve certificar-se de que também protege a infraestrutura e o plano de gestão Azure circundantes. Para garantir a sua infraestrutura, considere como o Windows Virtual Desktop se encaixa no seu ecossistema Azure maior. Para saber mais sobre o ecossistema Azure, consulte as [melhores práticas e padrões de segurança azure.](../security/fundamentals/best-practices-and-patterns.md)

Esta secção descreve as melhores práticas para assegurar o seu ecossistema Azure.

### <a name="enable-azure-security-center"></a>Ativar o Centro de Segurança Azure

Recomendamos que ative o Azure Security Center Standard para subscrições, máquinas virtuais, cofres-chave e contas de armazenamento.

Com o Azure Security Center Standard, pode:

* Gerir vulnerabilidades.
* Avaliar o cumprimento de quadros comuns como o PCI.
* Fortaleça a segurança geral do seu ambiente.

Para saber mais, consulte [a sua assinatura Azure para o Security Center Standard](../security-center/security-center-get-started.md).

### <a name="improve-your-secure-score"></a>Melhore a sua pontuação segura

Secure Score fornece recomendações e conselhos de boas práticas para melhorar a sua segurança geral. Estas recomendações são prioritárias para ajudá-lo a escolher quais são as mais importantes, e as opções Quick Fix ajudam-no a resolver vulnerabilidades potenciais rapidamente. Estas recomendações também atualizam ao longo do tempo, mantendo-o atualizado sobre as melhores formas de manter a segurança do seu ambiente. Para saber mais, consulte [Melhorar a sua pontuação segura no Centro de Segurança Azure](../security-center/security-center-secure-score.md).

## <a name="windows-virtual-desktop-security-best-practices"></a>As melhores práticas de segurança do Windows Virtual Desktop

O Windows Virtual Desktop tem muitos controlos de segurança incorporados. Nesta secção, você vai aprender sobre os controlos de segurança que você pode usar para manter os seus utilizadores e dados seguros.

### <a name="require-multi-factor-authentication"></a>Exigir autenticação de vários fatores

Exigir a autenticação de vários fatores para todos os utilizadores e administradores no Windows Virtual Desktop melhora a segurança de toda a sua implementação. Para saber mais, consulte [A autenticação multi-factor Enable Azure para o Ambiente de Trabalho Virtual do Windows](set-up-mfa.md).

### <a name="enable-conditional-access"></a>Ativar o acesso condicional

Ativar o [Acesso Condicional](../active-directory/conditional-access/best-practices.md) permite-lhe gerir riscos antes de conceder aos utilizadores acesso ao seu ambiente de ambiente de trabalho virtual windows. Ao decidir a que utilizadores conceder acesso, recomendamos que considere também quem é o utilizador, como se inscreve e qual o dispositivo que está a usar.

### <a name="collect-audit-logs"></a>Recolher registos de auditoria

Ativar a recolha de registos de auditoria permite-lhe visualizar a atividade de utilizador e administração relacionada com o Windows Virtual Desktop. Alguns exemplos de registos de auditoria chave são:

-   [Registo de Atividades do Azure](../azure-monitor/platform/activity-log-collect.md)
-   [Registo de Atividade ativa do Diretório Azure](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [Anfitriões da sessão](../azure-monitor/platform/agent-windows.md)
-   [Windows Virtual Desktop Diagnostic Log](../virtual-desktop/diagnostics-log-analytics.md)
-   [Registos do cofre de chaves](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>Utilizar Aplicações Remotas

Ao escolher um modelo de implementação, pode fornecer aos utilizadores remotos acesso a computadores de secretária virtuais inteiros ou apenas selecionar aplicações. Aplicações remotas, ou RemoteApps, proporcionam uma experiência perfeita à medida que o utilizador trabalha com aplicações no seu ambiente de trabalho virtual. As Aplicações Remotas reduzem o risco deixando o utilizador trabalhar com um subconjunto da máquina remota exposto pela aplicação.

### <a name="monitor-usage-with-azure-monitor"></a>Monitorizar a utilização com o Monitor Azure

Monitorize a utilização e disponibilidade do seu serviço de desktop virtual windows com [o Monitor Azure](https://azure.microsoft.com/services/monitor/). Considere criar alertas de [saúde](../service-health/alerts-activity-log-service-notifications.md) de serviço para o serviço de ambiente de trabalho virtual do Windows para receber notificações sempre que houver um evento de impacto de serviço.

## <a name="session-host-security-best-practices"></a>Sessão anfitrião boas práticas

Os anfitriões da sessão são máquinas virtuais que funcionam dentro de uma subscrição Azure e rede virtual. A segurança global da implementação do Windows Virtual Desktop depende dos controlos de segurança que coloca nos anfitriões da sessão. Esta secção descreve as melhores práticas para manter os anfitriões da sessão seguros.

### <a name="enable-endpoint-protection"></a>Ativar a proteção do ponto final

Para proteger a sua implementação de software malicioso conhecido, recomendamos que permita a proteção de pontos finais em todos os anfitriões da sessão. Pode utilizar o Antivírus Do Windows Defender ou um programa de terceiros. Para saber mais, consulte o [guia de implementação do Antivírus Do Windows Defender num ambiente VDI](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus). 

Para soluções de perfil como o FSLogix ou outras soluções que montam ficheiros VHD, recomendamos excluir extensões de ficheiros VHD.

### <a name="install-an-endpoint-detection-and-response-product"></a>Instale um produto de deteção e resposta de ponto final

Recomendamos que instale um produto de deteção e resposta de ponto final (EDR) para fornecer capacidades avançadas de deteção e resposta. Para sistemas operativos de servidorcom o [Azure Security Center](../security-center/security-center-services.md) ativado, a instalação de um produto EDR irá implementar o Defender ATP. Para os sistemas operativos do cliente, pode implantar o [Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) ou um produto de terceiros nesses pontos finais.

### <a name="enable-threat-and-vulnerability-management-assessments"></a>Permitir avaliações de gestão de ameaças e vulnerabilidades

Identificar vulnerabilidades de software que existem em sistemas operativos e aplicações é fundamental para manter o ambiente seguro. O Azure Security Center pode ajudá-lo a identificar pontos problemáticos através de avaliações de vulnerabilidade para sistemas operativos do servidor. Também pode utilizar o Defender ATP, que fornece uma gestão de ameaças e vulnerabilidades para sistemas operativos de ambiente de trabalho. Também pode utilizar produtos de terceiros se estiver tão inclinado, embora recomendamos a utilização do Azure Security Center e do Defender ATP.

### <a name="patch-software-vulnerabilities-in-your-environment"></a>Patch vulnerabilidades de software no seu ambiente

Uma vez que identifique uma vulnerabilidade, deve corrigi-la. Isto aplica-se também a ambientes virtuais, que incluem os sistemas operativos em funcionamento, as aplicações que são implantadas no seu interior e as imagens de que cria novas máquinas. Siga as comunicações de notificação do fornecedor e aplique patches atempadamente. Recomendamos remendar as suas imagens base mensalmente para garantir que as máquinas recém-implantadas são o mais seguras possível.

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>Estabelecer políticas máximas de tempo inativo e de desconexão

A contratação de utilizadores quando estão inativos preserva recursos e impede o acesso por utilizadores não autorizados. Recomendamos que os intervalos equilibrem a produtividade dos utilizadores, bem como a utilização de recursos. Para os utilizadores que interagem com aplicações apátridas, considere políticas mais agressivas que desligam as máquinas e preservem recursos. Desligar aplicações de longo prazo que continuem a funcionar se um utilizador estiver inativo, como uma simulação ou renderização CAD, pode interromper o trabalho do utilizador e pode mesmo exigir o reinício do computador.

### <a name="set-up-screen-locks-for-idle-sessions"></a>Configurar bloqueios de ecrã para sessões ociosas

Pode impedir o acesso indesejado do sistema configurando o Windows Virtual Desktop para bloquear o ecrã de uma máquina durante o tempo de inatividade e exigindo a autenticação para o desbloquear.

### <a name="establish-tiered-admin-access"></a>Estabelecer acesso administrativo hierárquico

Recomendamos que não conceda aos seus utilizadores acesso a ambientes de trabalho virtuais. Se precisar de pacotes de software, recomendamos que os disponibilize através de utilitários de gestão de configuração como o Microsoft Endpoint Manager. Num ambiente multi-sessão, recomendamos que não deixe que os utilizadores instalem software diretamente.

### <a name="consider-which-users-should-access-which-resources"></a>Considere quais os utilizadores a aceder aos recursos

Considere os anfitriões da sessão como uma extensão da sua implementação de ambiente de trabalho existente. Recomendamos que controle o acesso aos recursos de rede da mesma forma que faria para outros ambientes de trabalho no seu ambiente, como utilizar a segmentação da rede e a filtragem. Por padrão, os anfitriões da sessão podem ligar-se a qualquer recurso na internet. Existem várias formas de limitar o tráfego, incluindo a utilização de Firewall Azure, Eletrodomésticos Virtuais de Rede ou proxies. Se precisar de limitar o tráfego, certifique-se de que adiciona as regras adequadas para que o Windows Virtual Desktop possa funcionar corretamente.

### <a name="manage-office-pro-plus-security"></a>Gerir a segurança do Office Pro Plus

Além de garantir os anfitriões da sua sessão, é importante também garantir as aplicações que estão a funcionar dentro deles. O Office Pro Plus é uma das aplicações mais comuns implementadas nos anfitriões da sessão. Para melhorar a segurança de implementação do Office, recomendamos que utilize o Conselheiro de Política de [Segurança](/DeployOffice/overview-of-security-policy-advisor) para aplicações microsoft 365 para empresa. Esta ferramenta identifica políticas que podem aplicar à sua implementação para obter mais segurança. O Conselheiro de Política de Segurança também recomenda políticas baseadas no seu impacto na sua segurança e produtividade.

### <a name="other-security-tips-for-session-hosts"></a>Outras dicas de segurança para anfitriões de sessão

Ao restringir as capacidades do sistema operativo, pode reforçar a segurança dos anfitriões da sua sessão. Aqui estão algumas coisas que pode fazer:

- Controle a reorientação do dispositivo redirecionando unidades, impressoras e dispositivos USB para o dispositivo local de um utilizador numa sessão remota de ambiente de trabalho. Recomendamos que avalie os seus requisitos de segurança e verifique se estas funcionalidades devem ser desativadas ou não.

- Restringir o acesso do Windows Explorer escondendo mapeamentos de unidade local e remota. Isto impede que os utilizadores descubram informações indesejadas sobre a configuração do sistema e os utilizadores.

- Evite o acesso direto ao RDP aos anfitriões de sessões no seu ambiente. Se necessitar de acesso direto ao PDR para administração ou resolução de problemas, ative o acesso [a tempo just-in](../security-center/security-center-just-in-time.md) para limitar a superfície de ataque potencial num anfitrião de sessão.

- Conceda aos utilizadores permissões limitadas quando acedem a sistemas de ficheiros locais e remotos. Pode restringir as permissões certificando-se de que os seus sistemas de ficheiros locais e remotos utilizam listas de controlo de acesso com menos privilégios. Desta forma, os utilizadores só podem aceder ao que precisam e não podem alterar ou eliminar recursos críticos.

- Evite que o software indesejado esteja a funcionar nos anfitriões da sessão. Pode ativar o App Locker para obter segurança adicional nos anfitriões da sessão, garantindo que apenas as aplicações que permite podem ser executadas no anfitrião.

## <a name="next-steps"></a>Passos seguintes

Para aprender a permitir a autenticação de vários fatores, consulte Configurar a [autenticação de vários fatores](set-up-mfa.md).