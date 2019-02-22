---
title: Perguntas mais frequentes (FAQ) do sobre o Centro de segurança do Azure | Documentos da Microsoft
description: Encontre respostas para perguntas sobre o Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: monhaber
ms.openlocfilehash: 6696935cf23fa7286f23ae9b7d0e8a278e31403d
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652667"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure
Encontre respostas para perguntas sobre o Centro de segurança do Azure, um serviço que o ajuda a prevenir, detetar e responder a ameaças com maior visibilidade e controlo da segurança dos seus recursos do Microsoft Azure.

> [!NOTE]
> A partir do início de junho de 2017, o Centro de Segurança irá utilizar o Microsoft Monitoring Agent para recolher e armazenar dados. Para obter mais informações, consulte [migração de plataforma do Centro de segurança do Azure](security-center-platform-migration.md). As informações neste artigo representam a funcionalidade do Centro de Segurança após a transição para o Agente de Monitorização da Microsoft.
>
>

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-security-center"></a>O que é o Centro de Segurança do Azure?
O Centro de Segurança do Azure ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

### <a name="how-do-i-get-azure-security-center"></a>Como obtenho o Centro de segurança do Azure?
Centro de segurança do Azure é ativado com a sua subscrição do Microsoft Azure e acessado a partir da [portal do Azure](https://azure.microsoft.com/features/azure-portal/). ([Iniciar sessão portal](https://portal.azure.com), selecione **procurar**e desloque-se para **Centro de segurança**).  

## <a name="billing"></a>Faturação
### <a name="how-does-billing-work-for-azure-security-center"></a>Como funciona a faturação do Centro de segurança do Azure?
Centro de segurança é disponibilizado em dois escalões:

O **escalão gratuito** fornece visibilidade sobre o estado de segurança dos seus recursos do Azure, política de segurança básica, recomendações de segurança e integração com produtos de segurança e serviços de parceiros.

O **escalão Standard** adiciona ameaças avançadas capacidades de deteção, incluindo ameaças inteligência, análise comportamental, deteção de anomalias, incidentes de segurança e relatórios de atribuição de ameaças. Pode iniciar uma versão de avaliação do tierfree padrão. Para atualizar, selecione [escalão de preço](https://docs.microsoft.com/azure/security-center/security-center-pricing) na política de segurança. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="permissions"></a>Permissões
O Centro de Segurança do Azure utiliza o [Controlo de Acesso Baseado em Funções (RBAC)](../role-based-access-control/role-assignments-portal.md), que fornece [funções incorporadas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços no Azure.

Centro de segurança avalia a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. No Centro de segurança, apenas verá informações relacionadas com a um recurso quando for atribuída a função de proprietário, contribuinte ou leitor para a subscrição ou grupo de recursos que um recurso pertence.

Ver [permissões no Centro de segurança do Azure](security-center-permissions.md) para saber mais sobre as funções e ações permitidas no Centro de segurança.

## <a name="data-collection-agents-and-workspaces"></a>Recolha de dados, agentes e áreas de trabalho
Centro de segurança recolhe dados a partir das suas máquinas virtuais do Azure (VMs) e computadores não Azure para monitorizar ameaças e vulnerabilidades de segurança. Os dados são recolhidos com o Microsoft Monitoring Agent, que lê várias configurações relacionadas com segurança e registos de eventos a partir da máquina e copia os dados para a sua área de trabalho para análise.

### <a name="am-i-billed-for-log-analytics-on-the-workspaces-created-by-security-center"></a>Estou me são faturadas para o Log Analytics nas áreas de trabalho criadas pelo centro de segurança?
Não. Áreas de trabalho criadas pelo centro de segurança, enquanto configurado para o Log Analytics por nó de faturação, não implicam custos de Log Analytics. A faturação do Centro de segurança baseia-se sempre em sua política de segurança do Centro de segurança e as soluções instaladas numa área de trabalho:

- **Escalão gratuito** – Centro de segurança ativa a solução de "SecurityCenterFree" na área de trabalho predefinida. Não são cobradas no escalão gratuito.
- **Escalão Standard** – Centro de segurança ativa a solução de "Segurança" na área de trabalho predefinida.

Para obter mais informações sobre os preços, consulte [preços do Centro de segurança](https://azure.microsoft.com/pricing/details/security-center/). A página de preços aborda as alterações ao armazenamento de dados de segurança e faturação rateada entram partir Junho de 2017.

> [!NOTE]
> O escalão de áreas de trabalho criadas pelo centro de segurança de preços do Log Analytics não afeta a faturação do Centro de segurança.
>
>

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>O que qualifica uma VM para aprovisionamento automático da instalação do Microsoft Monitoring Agent?
VMs de IaaS de Linux ou Windows qualificam se:

- A extensão do Microsoft Monitoring Agent não está atualmente instalada na VM.
- A VM está no estado de execução.
- O Windows ou o agente de VM do Linux está instalado.
- A VM não é utilizada como uma aplicação, como o firewall de aplicações web ou de firewall da próxima geração.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Pode eliminar as áreas de trabalho predefinida criadas pelo centro de segurança?
**A eliminar a área de trabalho predefinida não é recomendada.** Centro de segurança utiliza as áreas de trabalho predefinida para armazenar dados de segurança de suas VMs.  Se eliminar uma área de trabalho, o Centro de segurança não consegue recolher estes dados e algumas recomendações de segurança e alertas não estão disponíveis.

Para recuperar, remova o Microsoft Monitoring Agent nas VMs ligadas à área de trabalho foi eliminada. Centro de segurança reinstala o agente e cria novas áreas de trabalho predefinida.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Como posso usar minha área de trabalho do Log Analytics existente?

Pode selecionar uma área de trabalho do Log Analytics existente para armazenar os dados recolhidos pelo centro de segurança. Para utilizar a sua área de trabalho do Log Analytics existente:

- A área de trabalho tem de ser associada a sua subscrição do Azure selecionada.
- No mínimo, tem de ter o permissões para aceder à área de trabalho de leitura.

Para selecionar uma área de trabalho do Log Analytics existente:

1. Sob **política de segurança – recolha de dados**, selecione **utilizar outra área de trabalho**.

   ![Utilize outra área de trabalho][5]

2. No menu pendente, selecione uma área de trabalho para armazenar os dados recolhidos.

   > [!NOTE]
   > No pull menu pendente, são apresentadas apenas as áreas de trabalho que tenha acesso ao e na sua subscrição do Azure.
   >
   >

3. Selecione **Guardar**.
4. Depois de selecionar **guardar**, será solicitado se pretende reconfigurar VMs monitorizadas.

   - Selecione **não** se pretender que as novas definições de área de trabalho para **aplicam-se em novas VMs apenas**. As novas definições de área de trabalho aplicam-se apenas a novas instalações de agentes; VMs detetadas recentemente que não têm o Microsoft Monitoring Agent instalado.
   - Selecione **Sim** se pretender que as novas definições de área de trabalho para **aplicam-se em todas as VMs**. Além disso, todas as VMS ligadas a um centro de segurança criada a área de trabalho for reconectada para a nova área de trabalho de destino.

   > [!NOTE]
   > Se selecionar Sim, não tem de eliminar as áreas de trabalho criadas pelo centro de segurança até que todas as VMs tenham sido reconectadas à nova área de trabalho de destino. Esta operação falha se uma área de trabalho é eliminada demasiado antigo.
   >
   >

   - Selecione **Cancelar** para cancelar a operação.

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>E se o Microsoft Monitoring Agent já foi instalado como uma extensão na VM?
Centro de segurança não substitui as ligações existentes a áreas de trabalho do utilizador. Centro de segurança armazena os dados de segurança da VM na área de trabalho já ligada. Centro de segurança atualiza a versão de extensão para incluir o ID de recurso do Azure da VM para suportar a utilização do Centro de segurança.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>E se eu tinha um Microsoft Monitoring Agent instalado na máquina, mas não como uma extensão?
Se o Microsoft Monitoring Agent estiver instalado diretamente na VM (não como uma extensão do Azure), o Centro de segurança não instala o Microsoft Monitoring Agent e monitorização de segurança é limitada.

Para obter mais informações, consulte a secção seguinte [o que acontece se um SCOM ou OMS direcionar o agente já está instalado na minha VM?](#scomomsinstalled)

### O que acontece se um SCOM ou OMS direcionar o agente já está instalado na minha VM?<a name="scomomsinstalled"></a>
Centro de segurança não consegue identificar com antecedência que um agente está instalado.  Centro de segurança tenta instalar a extensão do Microsoft Monitoring Agent e falhar devido ao agente instalado existente.  Esta falha impede a substituir as definições de ligação do agente para o seu espaço de trabalho e evita a criação de multi homing.

> [!NOTE]
> A versão do agente é atualizada para a versão mais recente do agente OMS.  Isto aplica-se aos utilizadores do SCOM também.
>
>

### <a name="what-is-the-impact-of-removing-these-extensions"></a>O que é o impacto da remoção destas extensões?
Se remover a extensão de monitorização da Microsoft, o Centro de segurança não é possível recolher dados de segurança da VM e algumas recomendações de segurança e alertas não estão disponíveis. Dentro de 24 horas, o Centro de segurança determina que a VM está em falta a extensão e reinstala a extensão.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Como faço para interromper a criação automática do agente de instalação e a área de trabalho?
Pode desativar aprovisionamento automático das suas subscrições na política de segurança, mas isso não é recomendável. A desativação automáticas limites aprovisionamento recomendações do Centro de segurança e alertas. Para desativar o aprovisionamento automático:

1. Se a sua subscrição está configurada para o escalão Standard, abra a política de segurança para essa subscrição e selecione o **gratuito** escalão.

   ![Escalão de preço][1]

2. Em seguida, desativar aprovisionamento automático, selecionando **Off** no **política de segurança – recolha de dados** painel.
   ![Recolha de dados][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Deve posso recusar a instalação automática do agente e a criação de área de trabalho?

> [!NOTE]
> Certifique-se de que reveja secções [quais são as implicações de desativá-las?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) e [passos recomendados ao desativá-las](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) se escolher a opção de desativar aprovisionamento automático.
>
>

Pode querer desativar aprovisionamento automático, se o seguinte se aplica a:

- Instalação automática do agente pelo centro de segurança aplica-se para a subscrição completa.  Não é possível aplicar a instalação automática para um subconjunto das VMs. Se existirem VMs críticas que não podem ser instaladas com o Microsoft Monitoring Agent, em seguida, deve optar pelo aprovisionamento automático fora.
- Versão do agente de atualizações de instalação da extensão Microsoft Monitoring Agent. Isto aplica-se para um agente direto e de um agente do SCOM. Se o agente do SCOM instalado é o versão 2012 e é atualizado, capacidades de capacidade de gerenciamento podem ser perdidas quando o servidor do SCOM também é a versão 2012. Considere desativar aprovisionamento automático, se o agente do SCOM instalado é a versão 2012.
- Se tiver uma área de trabalho personalizada externa para a subscrição (uma área de trabalho centralizada), em seguida, deve desativar aprovisionamento automático. Pode instalar a extensão do Microsoft Monitoring Agent e ligá-lo a área de trabalho sem Centro de segurança substituindo a ligação manualmente.
- Se quiser evitar a criação de várias áreas de trabalho por subscrição e tiver a área de trabalho personalizada dentro da subscrição, em seguida, tem duas opções:

   1. Pode desativar aprovisionamento automático. Após a migração, predefinir as definições de área de trabalho, tal como descrito no [como posso usar minha área de trabalho do Log Analytics existente?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Em alternativa, pode permitir que a migração concluir, o Microsoft Monitoring Agent para ser instalado nas VMs, e as VMs ligadas à área de trabalho criada. Em seguida, selecione a área de trabalho personalizada ao definir a configuração da área de trabalho de padrão com aceitar para reconfigurar os agentes já instalados. Para obter mais informações, consulte [como posso usar minha área de trabalho do Log Analytics existente?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Quais são as implicações de desativar aprovisionamento automático?
Depois de concluída a migração, o Centro de segurança não é possível recolher dados de segurança da VM e algumas recomendações de segurança e alertas não estão disponíveis. Se optar por sair, deve instalar o Microsoft Monitoring Agent manualmente. Ver [passos recomendados ao desativá-las](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Quais são os passos recomendados quando desativar aprovisionamento automático?

Deve instalar manualmente a extensão do Microsoft Monitoring Agent para que o Centro de segurança possa recolher dados de segurança das suas VMs e fornecer recomendações e alertas. Ver [instalação do agente de VM do Windows](../virtual-machines/extensions/oms-windows.md) ou [instalação do agente de VM do Linux](../virtual-machines/extensions/oms-linux.md) para obter orientações sobre a instalação.

Pode ligar o agente para qualquer área de trabalho personalizada existente ou centro de segurança criada a área de trabalho. Se a uma área de trabalho personalizada não tiver as soluções de "Segurança" ou "SecurityCenterFree" ativadas, terá de aplicar uma solução. Para aplicar, selecione a área de trabalho personalizada ou a subscrição e aplicam-se de um escalão de preço através da **política de segurança – escalão de preço** painel.

   ![Escalão de preço][1]

Centro de segurança permitirá que a solução correta no espaço de trabalho com base no escalão de preço selecionado.

### Como posso remover extensões OMS instaladas pelo centro de segurança?<a name="remove-oms"></a>
Pode remover manualmente o Microsoft Monitoring Agent. Não é recomendado que limita as recomendações do Centro de segurança e alertas.

> [!NOTE]
> Se a recolha de dados estiver ativada, o Centro de segurança irá reinstalar o agente depois é removê-lo.  Terá de desativar a recolha de dados antes de remover manualmente o agente. Ver como impedir a criação automática do agente de instalação e a área de trabalho? Para obter instruções sobre como desativar a recolha de dados.
>
>

Para remover manualmente o agente:

1.  No portal, abra **do Log Analytics**.
2.  No painel do Log Analytics, selecione uma área de trabalho:
3.  Selecione cada VM que não pretende monitorizar e selecione **desligar**.

   ![Remover o agente][3]

> [!NOTE]
> Se uma VM do Linux já tiver um agente do OMS não extensão, remover a extensão remove também o agente e o cliente tem de reinstalá-lo.
>
>
### <a name="how-do-i-disable-data-collection"></a>Como desativar a recolha de dados?
Aprovisionamento automático está desativada por predefinição. Pode desativar aprovisionamento automático de recursos em qualquer altura ao desativar esta definição na política de segurança. Aprovisionamento Automático é altamente recomendado para obter alertas de segurança e recomendações sobre as atualizações do sistema, vulnerabilidades do SO e o endpoint protection.

Para desativar a recolha de dados [inicie sessão no portal do Azure](https://portal.azure.com), selecione **procurar**, selecione **Centro de segurança**e selecione **selecione política**. Selecione a subscrição para a qual pretende desativar o aprovisionamento automático. Quando seleciona uma assinatura **política de segurança – recolha de dados** abre. Sob **aprovisionamento automático**, selecione **desativar**.

### <a name="how-do-i-enable-data-collection"></a>Como ativar a recolha de dados?
Pode ativar a recolha de dados para a sua subscrição do Azure na política de segurança. Para ativar a recolha de dados. [Inicie sessão no portal do Azure](https://portal.azure.com), selecione **procure**, selecione **Centro de segurança**e selecione **política de segurança**. Selecione a subscrição que pretende ativar o aprovisionamento automático. Quando seleciona uma assinatura **política de segurança – recolha de dados** abre. Sob **aprovisionamento automático**, selecione **no**.

### <a name="what-happens-when-data-collection-is-enabled"></a>O que acontece quando a recolha de dados está ativada?
Quando o aprovisionamento automático está ativado, o Centro de segurança Aprovisiona o Microsoft Monitoring Agent em todos os suportadas VMs do Azure e novas que são criadas. Aprovisionamento Automático é vivamente recomendado mas instalação manual de agente também está disponível. [Saiba como instalar a extensão do Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

O agente permite que o evento de criação de processo 4688 e o *CommandLine* campo dentro 4688 de evento. Novos processos criados na VM são registados pelo registo de eventos e monitorizados pelos serviços de deteção do Centro de segurança. Para obter informações sobre os detalhes registados para cada novo processo, consulte [campos de descrição no 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Além disso, o agente recolhe os 4688 eventos criados na VM e armazena-os na pesquisa.

O agente também permite a recolha de dados para [controlos de aplicações adaptativos](security-center-adaptive-application.md), Centro de segurança configura uma política de AppLocker local no modo de auditoria para permitir que todos os aplicativos. Isso fará com que o AppLocker gerar eventos que, em seguida, são recolhidos e utilizados pelo centro de segurança. É importante observar que esta política não irá ser configurada em quaisquer máquinas em que já existe uma política de AppLocker configurada. 

Quando o Centro de segurança detetar atividade suspeita na VM, o cliente é notificado por e-mail, se [informações de contacto de segurança](security-center-provide-security-contact-details.md) foi fornecido. Um alerta é também visível no painel de alertas de segurança do Centro de segurança.



### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>O agente de monitorização impacto sobre o desempenho dos meus servidores?
O agente consome uma quantia nominal de recursos do sistema e deve ter pouco impacto sobre o desempenho. Para obter mais informações sobre o impacto no desempenho e o agente e a extensão, consulte a [guia de operações e planeamento](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Onde são armazenados os meus dados?
Dados recolhidos por este agente são armazenados numa área de trabalho do Log Analytics existente associados à subscrição ou uma nova área de trabalho. Para obter mais informações, consulte [segurança de dados](security-center-data-security.md).

## Clientes existentes do Log Analytics<a name="existingloganalyticscust"></a>

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>O Centro de segurança substituir quaisquer ligações existentes entre as VMs e áreas de trabalho?
Se uma VM já tiver o Microsoft Monitoring Agent instalado como uma extensão do Azure, o Centro de segurança não substitui a ligação de área de trabalho existente. Em vez disso, o Centro de segurança utiliza a área de trabalho existente.

Uma solução de centro de segurança está instalado na área de trabalho se não estiver presente já, e a solução é aplicada apenas às VMs relevantes. Quando adiciona uma solução, ele será automaticamente implantado por predefinição para todos os agentes Windows e Linux ligados à sua área de trabalho do Log Analytics. [Filtragem de solução](../operations-management-suite/operations-management-suite-solution-targeting.md) permite-lhe aplicar um âmbito às suas soluções.

Se o Microsoft Monitoring Agent estiver instalado diretamente na VM (não como uma extensão do Azure), o Centro de segurança não instala o Microsoft Monitoring Agent e monitorização de segurança é limitada.

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Centro de segurança instalar soluções no meu áreas de trabalho do Log Analytics existentes? Quais são as implicações de faturas?
Quando o Centro de segurança identifica que uma VM já está ligada a uma área de trabalho que criou, o Centro de segurança permite soluções nesta área de trabalho, de acordo com o escalão de preço. As soluções são aplicadas apenas para as VMs do Azure relevantes, via [filtragem da solução](../operations-management-suite/operations-management-suite-solution-targeting.md), por isso, a faturação permanece o mesmo.

- **Escalão gratuito** – Centro de segurança instala a solução de "SecurityCenterFree" no espaço de trabalho. Não são cobradas no escalão gratuito.
- **Escalão Standard** – Centro de segurança instala a solução de "Segurança" no espaço de trabalho.

   ![Soluções em área de trabalho predefinida][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Já tem áreas de trabalho no meu ambiente, posso usá-los para recolher dados de segurança?
Se uma VM já tiver o Microsoft Monitoring Agent instalado como uma extensão do Azure, o Centro de segurança utiliza a área de trabalho ligada existente. Uma solução de centro de segurança está instalado na área de trabalho se não estiver presente já, e a solução é aplicada apenas às VMs relevantes via [filtragem da solução](../operations-management-suite/operations-management-suite-solution-targeting.md).

Quando o Centro de segurança instala o Microsoft Monitoring Agent nas VMs, ele usa as áreas de trabalho predefinida criadas pelo centro de segurança.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Já tenho solução de segurança na minha área de trabalho. Quais são as implicações de faturas?
A solução de segurança e auditoria é utilizada para ativar funcionalidades do escalão Standard do Centro de segurança para VMs do Azure. Se a solução de segurança e auditoria já estiver instalada numa área de trabalho, o Centro de segurança utiliza a solução existente. Não há nenhuma alteração na faturação.

## <a name="using-azure-security-center"></a>O Centro de segurança do Azure
### <a name="what-is-a-security-policy"></a>O que é uma política de segurança?
Uma política de segurança define o conjunto de controlos que são recomendados para recursos dentro da subscrição especificada. No Centro de segurança do Azure, é possível definir políticas para as suas subscrições do Azure, de acordo com requisitos de segurança da sua empresa e o tipo de aplicações ou sensibilidade dos dados em cada subscrição.

As políticas de segurança ativadas no monitorização e recomendações de segurança de unidade de centro de segurança do Azure. Para saber mais sobre as políticas de segurança, veja [monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?
Para modificar uma política de segurança, tem de ser um administrador de segurança ou um proprietário ou contribuinte dessa subscrição.

Para saber como configurar uma política de segurança, veja [definir políticas de segurança no Centro de segurança do Azure](tutorial-security-policy.md).

### <a name="what-is-a-security-recommendation"></a>O que é uma recomendação de segurança?
Centro de segurança do Azure analisa o estado de segurança dos seus recursos do Azure. Quando são identificadas potenciais vulnerabilidades de segurança, recomendações são criadas. As recomendações orientam-no durante o processo de configuração o controle necessário. Os exemplos são:

* Aprovisionamento de antimalware para ajudar a identificar e remover software malicioso
* [Grupos de segurança de rede](../virtual-network/security-overview.md) e regras para controlar o tráfego para as máquinas virtuais
* Aprovisionamento de uma firewall de aplicações web para ajudar na defesa contra ataques que visam as suas aplicações web
* Implementação de atualizações do sistema em falta
* Abordagem de configurações de SO que não correspondam às linhas de base recomendadas

Apenas as recomendações que estão ativadas nas políticas de segurança são mostradas aqui.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Como posso ver o estado de segurança atual dos meus recursos do Azure?
O **descrição geral do Centro de segurança** painel mostra a postura de segurança geral do seu ambiente dividida por computação, rede, armazenamento e dados e aplicativos. Cada tipo de recurso tem uma apresentação do indicador se quaisquer potenciais vulnerabilidades de segurança que tenham sido identificadas. Clicar em cada mosaico apresenta uma lista de problemas de segurança identificadas pelo centro de segurança, juntamente com um inventário dos recursos na sua subscrição.

### <a name="what-triggers-a-security-alert"></a>O que aciona um alerta de segurança?
Centro de segurança do Azure automaticamente recolhe, analisa e funde dados de registo de recursos do Azure, rede e soluções de parceiros como antimalware e firewalls. Quando são detetadas ameaças, é criado um alerta de segurança. Os exemplos incluem a deteção de:

* Virtual Machines comprometidas a comunicar com endereços IP maliciosos conhecidos
* Software maligno avançado detetado com o relatório de erros do Windows
* Ataques de força bruta contra máquinas virtuais
* Alertas de segurança de soluções de segurança de parceiros integradas, como o anti-malware ou Firewalls de aplicações Web

### <a name="why-did-secure-scores-values-change"></a>Por que proteger a alteração de valores de pontuações?
A partir de Fevereiro de 2019, o Centro de segurança ajustado a pontuação de algumas recomendações, para se ajustar melhor aos seus gravidade. Como resultado esse ajuste, podem existir valores de pontuação de proteger as alterações no geral.  Para obter mais informações sobre a pontuação segura, consulte [proteger o cálculo de pontuação](security-center-secure-score.md).

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>O que é a diferença entre ameaças detetados e alertados pelo Microsoft Security Response Center em comparação com o Centro de segurança do Azure?
O Microsoft Security Response Center (MSRC) executa a segurança selecione monitorização da rede do Azure e infraestrutura e recebe a reclamações de abuso e inteligência de ameaças de terceiros. Quando o MSRC fica sabendo que os dados do cliente foram acedidos por uma entidade não autorizada ou ilícita ou que utilização do cliente do Azure não estão em conformidade com os termos para utilizar aceitável, um Gestor de incidentes de segurança notifica o cliente. Normalmente, a notificação ocorre ao enviar um e-mail para os contactos de segurança especificados no Centro de segurança do Azure ou o proprietário da subscrição do Azure, se não for especificado um contacto de segurança.

Centro de segurança é um serviço do Azure que monitoriza o ambiente do Azure do cliente e aplica-se a análise para detetar automaticamente uma vasta gama de atividades maliciosas potencialmente continuamente. Estas deteções são apresentadas como alertas de segurança no dashboard do Centro de segurança.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Quais recursos do Azure são monitorizados pelo centro de segurança do Azure?
Centro de segurança do Azure monitoriza os seguintes recursos do Azure:

* Máquinas virtuais (VMs) (incluindo [serviços Cloud](../cloud-services/cloud-services-choose-me.md))
* Redes Virtuais do Azure
* Serviço SQL do Azure
* Conta de armazenamento do Azure
* Aplicações Web do Azure (no [ambiente do serviço de aplicações](../app-service/environment/intro.md))
* Soluções de parceiro integradas com a sua subscrição do Azure, como uma firewall de aplicações web em VMs e no ambiente de serviço de aplicações

## <a name="virtual-machines"></a>Virtual Machines
### <a name="what-types-of-virtual-machines-are-supported"></a>Que tipos de máquinas virtuais são suportados?
Monitorização e recomendações estão disponíveis para máquinas virtuais (VMs) criadas com ambos os [clássico e modelos de implementação do Resource Manager](../azure-classic-rm.md).

Ver [plataformas suportadas no Centro de segurança do Azure](security-center-os-coverage.md) para obter uma lista das plataformas suportadas.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Por que o Centro de segurança do Azure não reconhece a solução de antimalware em execução na minha VM do Azure?
Centro de segurança do Azure tem visibilidade para antimalware instalado através de extensões do Azure. Por exemplo, o Centro de segurança não é capaz de detetar antimalware que foi pré-instalado numa imagem fornecida ou se tiver instalado antimalware nas suas máquinas virtuais através de seus próprios processos (por exemplo, sistemas de gestão de configuração).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Por que motivo recebo a mensagem "Dados de análise em falta" para a minha VM?
Esta mensagem é apresentada quando não existem dados de análise para uma VM não existe. Pode demorar algum tempo (menos de uma hora) para dados de análise preencher após a recolha de dados está ativada no Centro de segurança do Azure. Após o preenchimento inicial de dados de análise, poderá receber esta mensagem porque não existe nenhum dado de análise de todo ou não existe nenhum dado de análise recentes. Verificações não preencher para uma VM no estado parado. Esta mensagem também pode aparecer se os dados de análise não tiverem sido povoadas recentemente (em conformidade com a política de retenção para o agente do Windows, que tem um valor predefinido de 30 dias).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>A frequência com que o Centro de segurança analisar para vulnerabilidades do sistema operativo, atualizações do sistema e problemas do endpoint protection?
A latência no Centro de segurança verifica a existência de vulnerabilidades, atualizações, e é de problemas:

- Configurações de segurança do sistema operacional – dados é atualizado no prazo de 48 horas
- Atualizações do sistema – dados é atualizada dentro de 24 horas
- Problemas de proteção de ponto de extremidade – dados serão atualizados dentro de 8 horas

Normalmente, o Centro de segurança verifica a existência de novos dados a cada hora e atualiza as recomendações em conformidade. 

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Por que motivo recebo a mensagem "O agente da VM está em falta?"
O agente da VM tem de ser instalado em VMs para ativar a recolha de dados. O Agente de VM é instalado por predefinição em VMs que são implementadas a partir do Azure Marketplace. Para obter informações sobre como instalar o agente da VM em outras VMs, consulte a mensagem de blogue [agente da VM e extensões](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
