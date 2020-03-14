---
title: Azure Security Center FAQ - recolha de dados e agentes
description: Perguntas frequentes sobre recolha de dados, agentes e espaços de trabalho para o Azure Security Center, um produto que o ajuda a prevenir, detetar e responder a ameaças
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 8317a13b9ef87679836f55627268deefa4500dce
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245476"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>FAQ - Perguntas sobre recolha de dados, agentes e espaços de trabalho

O Security Center recolhe dados das suas máquinas virtuais Azure (VMs), conjuntos de escala de máquinas virtuais, contentores IaaS e computadores não-Azure (incluindo máquinas no local) para monitorizar vulnerabilidades e ameaças de segurança. Os dados são recolhidos com o Microsoft Monitoring Agent, que lê várias configurações relacionadas com segurança e registos de eventos a partir da máquina e copia os dados para a sua área de trabalho para análise.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Estou a cobrar registos do Monitor Azure nos espaços de trabalho criados pelo Centro de Segurança?

Não. Espaços de trabalho criados pelo Security Center, enquanto configurados para registos do Monitor Azure por faturação de nó, não incorrem em encargos de registo do Monitor Azure. A faturação do Centro de segurança baseia-se sempre em sua política de segurança do Centro de segurança e as soluções instaladas numa área de trabalho:

- **Free tier** – O Security Center permite a solução 'SecurityCenterFree' no espaço de trabalho predefinido. Não será cobrado para o free tier.

- **Standard tier** – O Centro de Segurança permite a solução de 'Segurança' no espaço de trabalho predefinido.

Para obter mais informações sobre os preços, consulte os preços do Centro de [Segurança.](https://azure.microsoft.com/pricing/details/security-center/)

> [!NOTE]
> O nível de preços de log analytics dos espaços de trabalho criados pelo Security Center não afeta a faturação do Security Center.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>O que qualifica uma VM para aprovisionamento automático da instalação do Microsoft Monitoring Agent?

VMs de IaaS de Linux ou Windows qualificam se:

- A extensão do Microsoft Monitoring Agent não está atualmente instalada na VM.
- A VM está no estado de execução.
- O Windows ou o [Linux Azure Virtual Machine Agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) estão instalados.
- A VM não é utilizada como uma aplicação, como o firewall de aplicações web ou de firewall da próxima geração.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Pode eliminar as áreas de trabalho predefinida criadas pelo centro de segurança?

**Não é recomendada a aparas ao espaço de trabalho predefinido.** Centro de segurança utiliza as áreas de trabalho predefinida para armazenar dados de segurança de suas VMs. Se eliminar uma área de trabalho, o Centro de segurança não consegue recolher estes dados e algumas recomendações de segurança e alertas não estão disponíveis.

Para recuperar, remova o Microsoft Monitoring Agent nas VMs ligadas à área de trabalho foi eliminada. Centro de segurança reinstala o agente e cria novas áreas de trabalho predefinida.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Como posso usar minha área de trabalho do Log Analytics existente?

Pode selecionar uma área de trabalho do Log Analytics existente para armazenar os dados recolhidos pelo centro de segurança. Para utilizar a sua área de trabalho do Log Analytics existente:

- A área de trabalho tem de ser associada a sua subscrição do Azure selecionada.
- No mínimo, tem de ter o permissões para aceder à área de trabalho de leitura.

Para selecionar uma área de trabalho do Log Analytics existente:

1. No âmbito **da política de Segurança – Recolha de Dados,** selecione Utilize outro espaço de **trabalho.**

    ![Utilize outra área de trabalho][4]

1. No menu pendente, selecione uma área de trabalho para armazenar os dados recolhidos.

    > [!NOTE]
    > No pull menu pendente, são apresentadas apenas as áreas de trabalho que tenha acesso ao e na sua subscrição do Azure.

1. Selecione **Guardar**. Será-lhe perguntado se gostaria de reconfigurar os VMs monitorizados.

    - Selecione **Não** se pretender que as novas definições do espaço de trabalho **se apliquem apenas em novos VMs**. As novas definições de área de trabalho aplicam-se apenas a novas instalações de agentes; VMs detetadas recentemente que não têm o Microsoft Monitoring Agent instalado.
    - Selecione **Sim** se quiser que as novas definições do espaço de trabalho **se apliquem em todos os VMs**. Além disso, todas as VMS ligadas a um centro de segurança criada a área de trabalho for reconectada para a nova área de trabalho de destino.

    > [!NOTE]
    > Se selecionar **Sim,** não elimine quaisquer espaços de trabalho criados pelo Security Center até que todos os VMs tenham sido reconectados ao novo espaço de trabalho alvo. Esta operação falha se uma área de trabalho é eliminada demasiado antigo.

    - Para cancelar a operação, selecione **Cancelar**.

## E se o Agente de Monitorização da Microsoft já estivesse instalado como uma extensão do VM?<a name="mmaextensioninstalled"></a>

Quando o Agente de Monitorização é instalado como uma extensão, a configuração da extensão permite reportar apenas um único espaço de trabalho. Centro de segurança não substitui as ligações existentes a áreas de trabalho do utilizador. O Security Center armazenará dados de segurança de um VM num espaço de trabalho já ligado, desde que a solução "Security" ou "SecurityCenterFree" tenha sido instalada no mesmo. O Security Center pode atualizar a versão de extensão para a versão mais recente neste processo.

Para obter mais informações, consulte o [fornecimento automático em caso de instalação de um agente pré-existente](security-center-enable-data-collection.md#preexisting).



## E se um Agente de Monitorização da Microsoft for instalado diretamente na máquina, mas não como uma extensão (Agente Direto)?<a name="directagentinstalled"></a>

Se o Microsoft Monitoring Agent for instalado diretamente no VM (não como uma extensão Azure), o Security Center instalará a extensão do Microsoft Monitoring Agent e poderá atualizar o agente de monitorização da Microsoft para a versão mais recente.

O agente instalado continuará a reportar ao seu espaço de trabalho já configurado, e além disso reportará o espaço de trabalho configurado no Security Center (Multi-homing é suportado nas máquinas Windows).

Se o espaço de trabalho configurado for um espaço de trabalho do utilizador (não o espaço de trabalho padrão do Security Center), terá de instalar a solução "Security/"SecurityCenterFree" para o Security Center começar a processar eventos a partir de VMs e computadores que reportem a esse espaço de trabalho.

Para as máquinas Linux, o agente multi-homing ainda não é suportado - portanto, se for detetada uma instalação de agente existente, não ocorrerá um fornecimento automático e a configuração da máquina não será alterada.

Para máquinas existentes em assinaturas a bordo do Security Center antes de 17 de março de 2019, quando um agente existente for detetado, a extensão do Microsoft Monitoring Agent não será instalada e a máquina não será afetada. Para estas máquinas, consulte a recomendação "Resolver problemas de saúde do agente de monitorização nas suas máquinas" para resolver os problemas de instalação do agente nestas máquinas

Para mais informações, consulte a secção seguinte O que acontece se um Gestor de [Operações do Centro de Sistema ou um agente direto OMS já estiver instalado no meu VM?](#scomomsinstalled)

## E se um agente do System Center Operations Manager já estiver instalado no meu VM?<a name="scomomsinstalled"></a>

O centro de segurança instalará a extensão do Agente de Monitorização da Microsoft lado a lado com o agente de operações do System Center existente. O agente existente continuará a reportar normalmente ao servidor do Gestor de Operações do Centro de Sistema. Note que o agente do Gestor de Operações e o Agente de Monitorização do Microsoft partilham bibliotecas comuns de tempo de execução, que serão atualizadas para a versão mais recente durante este processo. Nota - Se a versão 2012 do agente do Gestor de Operações estiver instalada, não ligue o fornecimento automático (as capacidades de gestão podem ser perdidas quando o servidor do Gestor de Operações também for versão 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>O que é o impacto da remoção destas extensões?

Se remover a extensão de monitorização da Microsoft, o Centro de segurança não é possível recolher dados de segurança da VM e algumas recomendações de segurança e alertas não estão disponíveis. Dentro de 24 horas, o Centro de segurança determina que a VM está em falta a extensão e reinstala a extensão.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Como faço para interromper a criação automática do agente de instalação e a área de trabalho?

Pode desativar aprovisionamento automático das suas subscrições na política de segurança, mas isso não é recomendável. A desativação automáticas limites aprovisionamento recomendações do Centro de segurança e alertas. Para desativar o aprovisionamento automático:

1. Se a sua subscrição estiver configurada para o nível Standard, abra a política de segurança para essa subscrição e selecione o nível **Livre.**

   ![Escalão de preço][1]

1. Em seguida, desligue o fornecimento automático **selecionando** a página de segurança – Página de recolha de **dados.**
   ![Recolha de dados][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Deve posso recusar a instalação automática do agente e a criação de área de trabalho?

> [!NOTE]
> Não se esqueça de rever as secções Quais [](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) são as implicações de optar por não optar por não oprovisionamento [automático.](#what-are-the-implications-of-opting-out-of-automatic-provisioning)

Pode querer desativar aprovisionamento automático, se o seguinte se aplica a:

- Instalação automática do agente pelo centro de segurança aplica-se para a subscrição completa. Não é possível aplicar a instalação automática para um subconjunto das VMs. Se existirem VMs críticas que não podem ser instaladas com o Microsoft Monitoring Agent, em seguida, deve optar pelo aprovisionamento automático fora.
- A instalação da extensão do Microsoft Monitoring Agent (MMA) atualiza a versão do agente. Isto aplica-se a um agente direto e a um agente gestor de operações do System Center (neste último, o Gestor de Operações e o MMA partilham bibliotecas de prazos de funcionamento comuns - que serão atualizadas no processo). Se o agente do Gestor de Operações instalado for a versão 2012 e for atualizado, as capacidades de gestão podem ser perdidas quando o servidor do Gestor de Operações também for a versão 2012. Considere optar por não fornecer automaticamente se o agente do Gestor de Operações instalado for a versão 2012.
- Se tiver um espaço de trabalho personalizado externo à subscrição (um espaço de trabalho centralizado), então deve optar por não fornecer automaticamente. Pode instalar a extensão do Microsoft Monitoring Agent e ligá-lo a área de trabalho sem Centro de segurança substituindo a ligação manualmente.
- Se quiser evitar a criação de várias áreas de trabalho por subscrição e tiver a área de trabalho personalizada dentro da subscrição, em seguida, tem duas opções:

   1. Pode desativar aprovisionamento automático. Após a migração, detete as definições padrão do espaço de trabalho como descrito em Como posso usar o meu espaço de trabalho existente no [Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Em alternativa, pode permitir que a migração concluir, o Microsoft Monitoring Agent para ser instalado nas VMs, e as VMs ligadas à área de trabalho criada. Em seguida, selecione a área de trabalho personalizada ao definir a configuração da área de trabalho de padrão com aceitar para reconfigurar os agentes já instalados. Para mais informações, consulte como posso usar o meu espaço de trabalho existente no [Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Quais são as implicações de desativar aprovisionamento automático?

Quando a migração está concluída, o Centro de Segurança não pode recolher dados de segurança do VM e algumas recomendações e alertas de segurança não estão disponíveis. Se optar por não optar, instale manualmente o Microsoft Monitoring Agent. Consulte [os passos recomendados ao optar por não o fazer](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Quais são os passos recomendados quando desativar aprovisionamento automático?

Instale manualmente a extensão do Microsoft Monitoring Agent para que o Security Center possa recolher dados de segurança dos seus VMs e fornecer recomendações e alertas. Consulte a [instalação do agente para](../virtual-machines/extensions/oms-windows.md) o Windows VM ou [instalação de agente para o Linux VM](../virtual-machines/extensions/oms-linux.md) para obter orientações sobre a instalação.

Pode ligar o agente para qualquer área de trabalho personalizada existente ou centro de segurança criada a área de trabalho. Se a uma área de trabalho personalizada não tiver as soluções de "Segurança" ou "SecurityCenterFree" ativadas, terá de aplicar uma solução. Para se candidatar, selecione o espaço de trabalho personalizado ou subscrição e aplique um nível de preços através da política de Segurança – Página **de nível** de preços.

   ![Escalão de preço][1]

Centro de segurança permitirá que a solução correta no espaço de trabalho com base no escalão de preço selecionado.


## Como remover as extensões OMS instaladas pelo Security Center?<a name="remove-oms"></a>

Pode remover manualmente o Microsoft Monitoring Agent. Não é recomendado que limita as recomendações do Centro de segurança e alertas.

> [!NOTE]
> Se a recolha de dados estiver ativada, o Centro de segurança irá reinstalar o agente depois é removê-lo.  Terá de desativar a recolha de dados antes de remover manualmente o agente. Vê como paro a instalação automática de agentes e a criação do espaço de trabalho? para instruções sobre a desativação da recolha de dados.

Para remover manualmente o agente:

1.  No portal, abra **o Log Analytics.**

1.  Na página Log Analytics, selecione um espaço de trabalho:

1.  Selecione os VMs que não pretende monitorizar e selecione **Desligar**.

   ![Remover o agente][3]

> [!NOTE]
> Se um VM Linux já tiver um agente OMS não extensão, remover a extensão também remove o agente e terá de o reinstalar.


## <a name="how-do-i-disable-data-collection"></a>Como desativar a recolha de dados?

Aprovisionamento automático está desativada por predefinição. Pode desativar aprovisionamento automático de recursos em qualquer altura ao desativar esta definição na política de segurança. O fornecimento automático é altamente recomendado para obter alertas de segurança e recomendações sobre atualizações do sistema, vulnerabilidades de SO e proteção de pontos finais.

Para desativar a recolha de dados, [inscreva-se no portal Azure,](https://portal.azure.com) **selecione Browse,** selecione **Security Center**e selecione a **política Desseta**. Selecione a subscrição para a qual pretende desativar o aprovisionamento automático. Quando seleciona uma política de segurança de subscrição - A recolha de **dados** abre. Sob **o provisionamento automático,** selecione **Off**.


## <a name="how-do-i-enable-data-collection"></a>Como ativar a recolha de dados?

Pode ativar a recolha de dados para a sua subscrição do Azure na política de segurança. Para ativar a recolha de dados. [Inscreva-se no portal Azure,](https://portal.azure.com) **selecione Browse,** selecione **Security Center**e selecione a política de **segurança.** Selecione a subscrição que pretende ativar o aprovisionamento automático. Quando seleciona uma política de segurança de subscrição - A recolha de **dados** abre. Sob **o provisionamento automático,** selecione **On**.


## <a name="what-happens-when-data-collection-is-enabled"></a>O que acontece quando a recolha de dados está ativada?

Quando o aprovisionamento automático está ativado, o Centro de segurança Aprovisiona o Microsoft Monitoring Agent em todos os suportadas VMs do Azure e novas que são criadas. Recomenda-se o fornecimento automático, mas a instalação do agente manual também está disponível. [Saiba como instalar a extensão do MMA](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

O agente permite o evento de criação de processos 4688 e o campo *CommandLine* dentro do evento 4688. Novos processos criados na VM são registados pelo registo de eventos e monitorizados pelos serviços de deteção do Centro de segurança. Para obter mais informações sobre os detalhes registados para cada novo processo, consulte os campos de [descrição no 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Além disso, o agente recolhe os 4688 eventos criados na VM e armazena-os na pesquisa.

O agente também permite a recolha de dados para controlos de [aplicações adaptáveis,](security-center-adaptive-application.md)o Security Center configura uma política local do AppLocker no modo de Auditoria para permitir todas as aplicações. Esta política fará com que o AppLocker gere eventos, que são depois recolhidos e alavancados pelo Security Center. É importante observar que esta política não irá ser configurada em quaisquer máquinas em que já existe uma política de AppLocker configurada. 

Quando o Security Center detetar atividades suspeitas no VM, o cliente é notificado por e-mail se as [informações](security-center-provide-security-contact-details.md) de contacto de segurança forem fornecidas. Um alerta é também visível no painel de alertas de segurança do Centro de segurança.


## <a name="will-security-center-work-using-an-oms-gateway"></a>O Centro de Segurança funcionará usando um portal OMS?

Sim. O Azure Security Center aproveita o Monitor Azure para recolher dados de VMs e servidores Azure, utilizando o Agente de Monitorização da Microsoft.
Para recolher os dados, cada VM e servidor devem ligar-se à Internet utilizando HTTPS. A ligação pode ser direta, utilizando um proxy, ou através do [Gateway OMS](../azure-monitor/platform/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>O agente de monitorização impacto sobre o desempenho dos meus servidores?

O agente consome uma quantia nominal de recursos do sistema e deve ter pouco impacto sobre o desempenho. Para obter mais informações sobre o impacto do desempenho e sobre o agente e extensão, consulte o guia de [planeamento e operações.](security-center-planning-and-operations-guide.md#data-collection-and-storage)


## <a name="where-is-my-data-stored"></a>Onde são armazenados os meus dados?

Dados recolhidos por este agente são armazenados numa área de trabalho do Log Analytics existente associados à subscrição ou uma nova área de trabalho. Para mais informações, consulte [A Segurança de Dados](security-center-data-security.md).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png