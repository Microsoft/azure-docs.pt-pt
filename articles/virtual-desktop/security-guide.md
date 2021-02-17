---
title: Windows Virtual Desktop security best practices - Azure
description: As melhores práticas para manter o ambiente de ambiente de trabalho virtual do Windows seguro.
author: heidilohr
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cfc980fdabdb9c6e7085088db12754243f133d89
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581397"
---
# <a name="security-best-practices"></a>Melhores práticas de segurança

O Windows Virtual Desktop é um serviço de desktop virtual gerido que inclui muitas capacidades de segurança para manter a sua organização segura. Numa implementação virtual do Windows Desktop, a Microsoft gere partes dos serviços em nome do cliente. O serviço tem muitas funcionalidades de segurança avançadas incorporadas, como o Reverse Connect, que reduz o risco envolvido em ter ambientes de trabalho remotos acessíveis a partir de qualquer lugar.

Este artigo descreve os passos adicionais que pode tomar como administrador para manter as implementações virtuais do Windows Desktop dos seus clientes seguras.

## <a name="security-responsibilities"></a>Responsabilidades de segurança

O que torna os serviços em nuvem diferentes das infraestruturas virtuais tradicionais de desktop (VDIs) é a forma como lidam com as responsabilidades de segurança. Por exemplo, num VDI tradicional no local, o cliente seria responsável por todos os aspetos da segurança. No entanto, na maioria dos serviços na nuvem, estas responsabilidades são partilhadas entre o cliente e a empresa.

Quando utiliza o Windows Virtual Desktop, é importante entender que, embora alguns componentes já venham protegidos para o seu ambiente, terá de configurar outras áreas para se adaptar às necessidades de segurança da sua organização.

Aqui estão as necessidades de segurança pelas quais é responsável na sua implementação virtual do Windows Desktop:

| Necessidade de segurança | O cliente é responsável por isto? |
|---------------|:-------------------------:|
|Identidade|Yes|
|Dispositivos de utilizador (mobile e PC)|Yes|
|Segurança de aplicativos|Yes|
|Sessão anfitrião OS|Yes|
|Configuração de implementação|Yes|
|Controlos de rede|Yes|
|Plano de controlo de virtualização|No|
|Anfitriões físicos|No|
|Rede física|No|
|Datacenter físico|No|

As necessidades de segurança que o cliente não é responsável são tratadas pela Microsoft.

## <a name="azure-security-best-practices"></a>Melhores práticas de segurança do Azure

Windows Virtual Desktop é um serviço sob Azure. Para maximizar a segurança da sua implementação virtual do Windows Desktop, deve certificar-se de que também protege a infraestrutura e o plano de gestão Azure circundantes. Para garantir a sua infraestrutura, considere como o Windows Virtual Desktop se encaixa no seu ecossistema Azure maior. Para saber mais sobre o ecossistema Azure, consulte as [melhores práticas e padrões](../security/fundamentals/best-practices-and-patterns.md)de segurança da Azure.

Esta secção descreve as melhores práticas para garantir o seu ecossistema Azure.

### <a name="enable-azure-security-center"></a>Ativar o Centro de Segurança Azure

Recomendamos que ative o Azure Security Center Standard para subscrições, máquinas virtuais, cofres-chave e contas de armazenamento.

Com o Azure Security Center Standard, pode:

* Gerir vulnerabilidades.
* Avaliar o cumprimento de quadros comuns como o PCI.
* Fortaleça a segurança geral do seu ambiente.

Para saber mais, consulte [a bordo da sua assinatura Azure para o Security Center Standard](../security-center/security-center-get-started.md).

### <a name="improve-your-secure-score"></a>Melhorar a Classificação de Segurança

A Secure Score fornece recomendações e conselhos de boas práticas para melhorar a sua segurança global. Estas recomendações são priorizadas para ajudá-lo a escolher quais são mais importantes, e as opções Quick Fix ajudam-no a lidar rapidamente com potenciais vulnerabilidades. Estas recomendações também atualizam ao longo do tempo, mantendo-o atualizado sobre as melhores formas de manter a segurança do seu ambiente. Para saber mais, consulte [Melhorar a sua pontuação segura no Centro de Segurança Azure](../security-center/secure-score-security-controls.md).

## <a name="windows-virtual-desktop-security-best-practices"></a>As melhores práticas de segurança do Windows Virtual Desktop

O Windows Virtual Desktop tem muitos controlos de segurança incorporados. Nesta secção, você vai aprender sobre os controlos de segurança que você pode usar para manter os seus utilizadores e dados seguros.

### <a name="require-multi-factor-authentication"></a>Requerem autenticação de vários fatores

Exigir a autenticação de vários fatores para todos os utilizadores e administradores no Windows Virtual Desktop melhora a segurança de toda a sua implementação. Para saber mais, consulte [ativar a autenticação multi-factor Azure AD para o Windows Virtual Desktop](set-up-mfa.md).

### <a name="enable-conditional-access"></a>Ativar o acesso condicional

Ativar [o Acesso Condicional](../active-directory/conditional-access/overview.md) permite-lhe gerir riscos antes de conceder aos utilizadores acesso ao seu ambiente de trabalho virtual do Windows. Ao decidir a que utilizadores devem conceder acesso, recomendamos que considere também quem é o utilizador, como se insinuem e que dispositivo estão a utilizar.

### <a name="collect-audit-logs"></a>Recolher registos de auditoria

Ativar a recolha de registos de auditoria permite-lhe visualizar a atividade de utilizador e administração relacionada com o Windows Virtual Desktop. Alguns exemplos de registos de auditoria chave são:

-   [Registo de Atividades do Azure](../azure-monitor/essentials/activity-log.md)
-   [Registo de atividade do diretório ativo Azure](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [Anfitriões de sessão](../azure-monitor/agents/agent-windows.md)
-   [Registo de diagnóstico virtual do Windows Desktop](../virtual-desktop/diagnostics-log-analytics.md)
-   [Troncos de cofre de chaves](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>Utilizar Remotas

Ao escolher um modelo de implementação, pode fornecer aos utilizadores remotos acesso a todos os ambientes de trabalho virtuais ou apenas selecionar aplicações. As aplicações remotas, ou RemoteApps, proporcionam uma experiência perfeita à medida que o utilizador trabalha com aplicações no seu ambiente de trabalho virtual. Os RemoteApps reduzem o risco apenas permitindo que o utilizador trabalhe com um subconjunto da máquina remota exposto pela aplicação.

### <a name="monitor-usage-with-azure-monitor"></a>Monitorize a utilização com o Azure Monitor

Monitorize a utilização e disponibilidade do seu serviço virtual de ambiente de trabalho do Windows com [o Azure Monitor.](https://azure.microsoft.com/services/monitor/) Considere criar alertas de [saúde](../service-health/alerts-activity-log-service-notifications-portal.md) de serviço para o serviço de desktop virtual do Windows para receber notificações sempre que houver um evento com impacto de serviço.

## <a name="session-host-security-best-practices"></a>Sessão anfitrião de boas práticas de segurança

Os anfitriões de sessão são máquinas virtuais que funcionam dentro de uma subscrição do Azure e de uma rede virtual. A segurança geral da sua implementação virtual do Windows Desktop depende dos controlos de segurança que colocou nos anfitriões da sessão. Esta secção descreve as melhores práticas para manter os anfitriões da sessão seguros.

### <a name="enable-screen-capture-protection-preview"></a>Ativar a proteção contra a captura de ecrã (pré-visualização)

A funcionalidade de proteção para captura de ecrã impede que informações sensíveis sejam capturadas nos pontos finais do cliente. Quando ativar esta funcionalidade, o conteúdo remoto será automaticamente bloqueado ou escondido em imagens e partilhas de ecrã. Também será ocultado de software malicioso que poderá estar continuamente a capturar o conteúdo do seu ecrã. Recomendamos que desative a reorientação da área de transferência para evitar a cópia de conteúdo remoto para pontos finais durante a utilização desta função.

Esta política é aplicada ao nível do hospedeiro configurando uma chave de registo. Para ativar esta política, abra o PowerShell e desabroque a tecla de registo **fEnableScreenCaptureProtection** executando este cmdlet:

```powershell
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableScreenCaptureProtection /t REG_DWORD /d 1
```

Para testar esta nova funcionalidade:

- Certifique-se de que as suas piscinas de anfitrião estão aprovisionadas no ambiente de validação.
- Certifique-se de que descarregou e instalou o cliente do Windows Desktop, versão 1.2.1526 ou posterior.

>[!NOTE]
>Durante a pré-visualização, apenas as ligações completas do ambiente de trabalho a partir dos pontos finais do Windows 10 suportam esta funcionalidade.


### <a name="enable-endpoint-protection"></a>Permitir a proteção do ponto final

Para proteger a sua implementação de software malicioso conhecido, recomendamos que se permita a proteção do ponto final em todos os anfitriões da sessão. Pode utilizar o Antivírus do Windows Defender ou um programa de terceiros. Para saber mais, consulte [o guia de implementação do Antivírus do Windows Defender num ambiente VDI](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus).

Para soluções de perfil como fSLogix ou outras soluções que suportem ficheiros VHD, recomendamos a exclusão de extensões de ficheiros VHD.

### <a name="install-an-endpoint-detection-and-response-product"></a>Instale um produto de deteção e resposta de ponto final

Recomendamos que instale um produto de deteção e resposta de ponto final (EDR) para fornecer capacidades avançadas de deteção e resposta. Para sistemas operativos de servidor com [O Azure Security Center](../security-center/security-center-services.md) ativados, a instalação de um produto EDR irá implantar o Defender ATP. Para sistemas operativos clientes, pode implementar [o Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) ou um produto de terceiros nesses pontos finais.

### <a name="enable-threat-and-vulnerability-management-assessments"></a>Permitir avaliações de gestão de ameaças e vulnerabilidades

Identificar vulnerabilidades de software que existem em sistemas operativos e aplicações é fundamental para manter o seu ambiente seguro. O Azure Security Center pode ajudá-lo a identificar pontos problemáticos através de avaliações de vulnerabilidade para sistemas operativos de servidores. Também pode utilizar o Defender ATP, que fornece gestão de ameaças e vulnerabilidades para sistemas operativos de desktop. Também pode utilizar produtos de terceiros se estiver tão inclinado, embora recomendemos a utilização do Azure Security Center e do Defender ATP.

### <a name="patch-software-vulnerabilities-in-your-environment"></a>Corrigir vulnerabilidades de software no seu ambiente

Uma vez que identifique uma vulnerabilidade, deve remendá-la. Isto aplica-se também a ambientes virtuais, que incluem os sistemas operativos em funcionamento, as aplicações que são implementadas no seu interior e as imagens a partir das quais se criam novas máquinas. Siga as comunicações de notificação de patches do seu fornecedor e aplique patches em tempo útil. Recomendamos remendar as suas imagens base mensalmente para garantir que as máquinas recém-implantadas estão o mais seguras possível.

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>Estabelecer políticas máximas inativas de tempo e desconexão

A assinatura dos utilizadores quando estão inativos preserva recursos e impede o acesso de utilizadores não autorizados. Recomendamos que os intervalos de tempo equilibram a produtividade do utilizador, bem como a utilização de recursos. Para os utilizadores que interagem com aplicações apátridas, considere políticas mais agressivas que desliguem máquinas e preservem recursos. Desligar aplicações de longa duração que continuam a funcionar se um utilizador estiver inativo, como uma simulação ou renderização CAD, pode interromper o trabalho do utilizador e pode até exigir o reinício do computador.

### <a name="set-up-screen-locks-for-idle-sessions"></a>Configurar fechaduras de ecrã para sessões ociosas

Pode impedir o acesso indesejado do sistema configurando o Windows Virtual Desktop para bloquear o ecrã de uma máquina durante o tempo de marcha lenta e exigir a autenticação para desbloqueá-lo.

### <a name="establish-tiered-admin-access"></a>Estabelecer acesso a administradores hierarquizado

Recomendamos que não conceda aos seus utilizadores acesso a ambientes de trabalho virtuais. Se precisar de pacotes de software, recomendamos que os disponibilize através de utilitários de gestão de configuração como o Microsoft Endpoint Manager. Num ambiente de várias sessões, recomendamos que não deixe que os utilizadores instalem software diretamente.

### <a name="consider-which-users-should-access-which-resources"></a>Considere quais os utilizadores que devem aceder aos recursos

Considere os anfitriões da sessão como uma extensão da sua implementação de ambiente de trabalho existente. Recomendamos que controle o acesso aos recursos da rede da mesma forma que faria para outros ambientes de trabalho no seu ambiente, como a utilização da segmentação de rede e filtragem. Por predefinição, os anfitriões da sessão podem ligar-se a qualquer recurso na internet. Existem várias formas de limitar o tráfego, incluindo a utilização de Firewall Azure, Aparelhos Virtuais de Rede ou proxies. Se precisar de limitar o tráfego, certifique-se de que adiciona as regras adequadas para que o Windows Virtual Desktop possa funcionar corretamente.

### <a name="manage-office-pro-plus-security"></a>Gerir a segurança do Office Pro Plus

Além de garantir os anfitriões da sessão, é importante também garantir as aplicações que estão dentro delas. Office Pro Plus é uma das aplicações mais comuns implementadas em anfitriões de sessão. Para melhorar a segurança da implementação do Office, recomendamos que utilize o [Conselheiro de Política de Segurança](/DeployOffice/overview-of-security-policy-advisor) para aplicações microsoft 365 para empresas. Esta ferramenta identifica políticas que podem aplicar-se à sua implementação para obter mais segurança. O Conselheiro de Política de Segurança também recomenda políticas baseadas no seu impacto na sua segurança e produtividade.

### <a name="other-security-tips-for-session-hosts"></a>Outras dicas de segurança para os anfitriões da sessão

Ao restringir as capacidades do sistema operativo, pode reforçar a segurança dos anfitriões da sessão. Aqui estão algumas coisas que pode fazer:

- Redirecionar o dispositivo redirecionando unidades, impressoras e dispositivos USB para o dispositivo local de um utilizador numa sessão de ambiente de trabalho remoto. Recomendamos que avalie os seus requisitos de segurança e verifique se estas funcionalidades devem ser desativadas ou não.

- Restringir o acesso do Windows Explorer escondendo mapeamentos de unidade local e remota. Isto impede que os utilizadores descubram informações indesejadas sobre a configuração do sistema e os utilizadores.

- Evite o acesso direto do PDR aos anfitriões de sessão no seu ambiente. Se precisar de acesso RDP direto para administração ou resolução de problemas, permita o acesso [just-in-time](../security-center/security-center-just-in-time.md) para limitar a superfície de ataque potencial num anfitrião de sessão.

- Conceder aos utilizadores permissões limitadas quando acedem a sistemas de ficheiros locais e remotos. Pode restringir as permissões certificando-se de que os seus sistemas de ficheiros locais e remotos utilizam listas de controlo de acesso com o menor privilégio. Desta forma, os utilizadores só podem aceder ao que precisam e não podem alterar ou apagar recursos críticos.

- Evite que o software indesejado esteja a funcionar em anfitriões de sessão. Pode ativar o App Locker para obter segurança adicional nos anfitriões da sessão, garantindo que apenas as aplicações que permite podem ser executadas no anfitrião.

## <a name="next-steps"></a>Passos seguintes

Para aprender a ativar a autenticação de vários fatores, consulte [Configurar a autenticação de vários fatores.](set-up-mfa.md)