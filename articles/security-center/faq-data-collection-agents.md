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
ms.date: 11/15/2020
ms.author: memildin
ms.openlocfilehash: 4ef01045d1ca16d0101cdd9ccfcd118231cd28de
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456118"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>FAQ - Perguntas sobre recolha de dados, agentes e espaços de trabalho

O Security Center recolhe dados das suas máquinas virtuais Azure (VMs), conjuntos de escala de máquinas virtuais, contentores IaaS e computadores não-Azure (incluindo máquinas no local) para monitorizar as vulnerabilidades e ameaças de segurança. Os dados são recolhidos utilizando o agente Log Analytics, que lê várias configurações relacionadas com a segurança e registos de eventos da máquina e copia os dados para o seu espaço de trabalho para análise.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Estou cobrado para registos do Monitor Azure nos espaços de trabalho criados pelo Security Center?

N.º Os espaços de trabalho criados pelo Security Center, enquanto configurados para registos do Monitor Azure por faturação de nó, não incorrem em cargas de registos do Azure Monitor. A faturação do Security Center baseia-se sempre na sua política de segurança do Security Center e nas soluções instaladas num espaço de trabalho:

- **Azure Defender off** – O Security Center permite a solução "SecurityCenterFree" no espaço de trabalho predefinido. Não será cobrado se o Azure Defender estiver fora.

- **O Azure Defender on** – Security Center permite a solução "Segurança" no espaço de trabalho predefinido.

Para obter detalhes sobre preços na sua moeda de eleição e de acordo com a sua região, consulte [os preços do Security Center](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> O nível de preços de registo de espaços de trabalho criados pelo Security Center não afeta a faturação do Centro de Segurança.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-is-the-log-analytics-agent"></a>O que é o agente log analytics?

Para monitorizar vulnerabilidades e ameaças de segurança, o Azure Security Center depende do [Agente De Análise de Registo](../azure-monitor/agents/log-analytics-agent.md) - este é o mesmo agente utilizado pelo serviço Azure Monitor. 

O agente é por vezes referido como o Agente de Monitorização da Microsoft (ou "MMA"). 

O agente recolhe vários detalhes de configuração relacionados com a segurança e registos de eventos a partir de máquinas conectadas e, em seguida, copia os dados para o seu espaço de trabalho Log Analytics para uma análise mais aprofundada. Exemplos desses dados são: tipo e versão do sistema operativo, registos do sistema operativo (registos de eventos windows), processos de execução, nome da máquina, endereços IP e registados no utilizador.

Certifique-se de que as suas máquinas estão a executar um dos sistemas operativos suportados para o agente, conforme descrito nas páginas seguintes:

* [Log Analytics agente para sistemas operativos suportados pelo Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

* [Log Analytics agente para sistemas operativos suportados pelo Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

Saiba mais sobre os [dados recolhidos pelo agente Log Analytics.](security-center-enable-data-collection.md)




## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>O que qualifica um VM para o provisionamento automático da instalação do agente Log Analytics?

Os Windows ou Linux IaaS VMs se qualificam se:

- A extensão do agente Log Analytics não está atualmente instalada no VM.
- O VM está em estado de funcionamento.
- O Windows ou o Linux [Azure Virtual Machine Agent](../virtual-machines/extensions/agent-windows.md) estão instalados.
- O VM não é utilizado como um aparelho, como firewall de aplicação web ou firewall de próxima geração.


## <a name="where-is-the-default-log-analytics-workspace-created"></a>Onde é criado o espaço de trabalho padrão do Log Analytics?

A localização do espaço de trabalho predefinido depende da sua região de Azure:

- Para VMs nos Estados Unidos e Brasil a localização do espaço de trabalho é os Estados Unidos
- Para VMs no Canadá, a localização do espaço de trabalho é o Canadá
- Para os VMs na Europa, a localização do espaço de trabalho é a Europa
- Para VMs no Reino Unido, a localização do espaço de trabalho é o Reino Unido
- Para os VMs na Ásia Oriental e Sudeste Asiático a localização do espaço de trabalho é a Ásia
- Para os VMs na Coreia, a localização do espaço de trabalho é a Coreia
- Para VMs na Índia, a localização do espaço de trabalho é a Índia
- Para VMs no Japão, a localização do espaço de trabalho é o Japão
- Para VMs na China, a localização do espaço de trabalho é a China
- Para VMs na Austrália, a localização do espaço de trabalho é a Austrália


## <a name="what-security-events-are-collected-by-the-log-analytics-agent"></a>Que eventos de segurança são recolhidos pelo agente Log Analytics?

Para obter uma lista completa dos eventos de segurança recolhidos pelo agente, consulte que tipos de [eventos são armazenados para as definições de eventos de segurança "Comuns" e "Mínimos"?](security-center-enable-data-collection.md#what-event-types-are-stored-for-common-and-minimal)

> [!IMPORTANT]
> Note que para alguns serviços, como o Azure Firewall, se tiver ativado o registo e escolhido um recurso chatty para registar (por exemplo, definir o log para *verbose),* poderá ver impactos significativos nas necessidades de armazenamento do seu espaço de trabalho Log Analytics. 


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Posso eliminar os espaços de trabalho predefinidos criados pelo Security Center?

**Não é aconselhável eliminar o espaço de trabalho predefinido.** O Security Center utiliza os espaços de trabalho predefinidos para armazenar dados de segurança dos seus VMs. Se eliminar um espaço de trabalho, o Security Center não poderá recolher estes dados e algumas recomendações de segurança e alertas não estão disponíveis.

Para recuperar, remova o agente Log Analytics nos VMs ligados ao espaço de trabalho eliminado. O Security Center reinstala o agente e cria novos espaços de trabalho predefinidos.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Como posso usar o meu espaço de trabalho log analytics existente?

Pode selecionar um espaço de trabalho do Log Analytics existente para armazenar dados recolhidos pelo Security Center. Para utilizar o seu espaço de trabalho log analytics existente:

- O espaço de trabalho deve estar associado à subscrição Azure selecionada.
- No mínimo, deve ter lido permissões para aceder ao espaço de trabalho.

Para selecionar um espaço de trabalho existente do Log Analytics:

1. A partir do menu do Centro de Segurança, **selecione definições de preços &**.
1. Selecione a subscrição relevante.
1. Abra a página **de provisionamento automático.**
1. Para o agente 'Registar Analytics', **selecione a configuração de Editar**. 

    :::image type="content" source="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png" alt-text="A configuração do agente Log Analytics para utilizar ao utilizar a implantação automática" lightbox="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png":::

1. Selecione **Connect Azure VMs para um espaço de trabalho diferente** e escolha o seu espaço de trabalho existente.

    :::image type="content" source="./media/security-center-enable-data-collection/choose-workspace.png" alt-text="Selecionando um espaço de trabalho não padrão para o seu agente Log Analytics reportar a" lightbox="./media/security-center-enable-data-collection/choose-workspace.png":::

    > [!TIP]
    > A lista inclui apenas espaços de trabalho aos quais tem acesso e que estão na sua subscrição do Azure.

1. Selecione **Guardar**. Perguntar-lhe-á se pretende reconfigurar os VMs monitorizados.

    - Selecione **Não** se quiser que as novas definições do espaço de trabalho **se apliquem apenas em novos VMs**. As novas definições de espaço de trabalho aplicam-se apenas às novas instalações de agentes; VMs recém-descobertos que não têm o agente Log Analytics instalado.
    - Selecione **Sim** se quiser que as novas definições do espaço de trabalho **se apliquem em todos os VMs**. Além disso, todos os VM ligados a um espaço de trabalho criado pelo Security Center estão reconectados com o novo espaço de trabalho alvo.

    > [!NOTE]
    > Se selecionar **Sim,** não elimine quaisquer espaços de trabalho criados pelo Security Center até que todos os VMs tenham sido reconectados ao novo espaço de trabalho alvo. Esta operação falha se um espaço de trabalho for apagado demasiado cedo.


## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>E se o agente Log Analytics já tivesse sido instalado como uma extensão no VM?<a name="mmaextensioninstalled"></a>

Quando o Monitor é instalado como uma extensão, a configuração da extensão permite reportar apenas a um único espaço de trabalho. O Centro de Segurança não substitui as ligações existentes aos espaços de trabalho do utilizador. O Security Center armazenará dados de segurança a partir de um VM num espaço de trabalho que já está ligado, desde que a solução "SecurityCenterFree" tenha sido instalada no mesmo. O Security Center pode atualizar a versão de extensão para a versão mais recente deste processo.

Para obter mais informações, consulte [o provisionamento automático em caso de instalação de um agente pré-existente](security-center-enable-data-collection.md#preexisting).



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>E se um agente Do Log Analytics for instalado diretamente na máquina, mas não como uma extensão (Agente Direto)?<a name="directagentinstalled"></a>

Se o agente Log Analytics for instalado diretamente no VM (não como uma extensão Azure), o Security Center instalará a extensão do agente Log Analytics e poderá atualizar o agente Log Analytics para a versão mais recente.

O agente instalado continuará a reportar ao seu espaço de trabalho já configurado, e além disso reportará ao espaço de trabalho configurado no Security Center (o multi-homing é suportado em máquinas Windows).

Se o espaço de trabalho configurado for um espaço de trabalho do utilizador (não o espaço de trabalho predefinido do Security Center), terá de instalar a solução "Security" ou "SecurityCenterFree" no mesmo para que o Security Center comece a processar eventos de VMs e computadores que reportem a esse espaço de trabalho.

No caso das máquinas Linux, o Agente multi-homing ainda não está suportado - portanto, se for detetada uma instalação de agente existente, não o provisionamento automático não ocorrerá e a configuração da máquina não será alterada.

Para as máquinas existentes em subscrições a bordo do Security Center antes de 17 de março de 2019, quando um agente existente será detetado, a extensão do agente Log Analytics não será instalada e a máquina não será afetada. Para estas máquinas, consulte a recomendação "Resolver problemas de saúde do agente de monitorização nas suas máquinas" para resolver os problemas de instalação do agente nestas máquinas

Para mais informações, consulte a próxima secção [O que acontece se um Gestor de Operações do System Center ou agente direto da OMS já estiver instalado no meu VM?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>E se um agente do Gestor de Operações do System Center já estiver instalado no meu VM?<a name="scomomsinstalled"></a>

O centro de segurança instalará a extensão do agente Log Analytics lado a lado com o agente gestor de operações do Centro de Sistema existente. O agente existente continuará a reportar-se normalmente ao servidor Gestor de Operações do Centro de Sistema. Note que o agente gestor de operações e o agente Log Analytics partilham bibliotecas comuns de tempo de execução, que serão atualizadas para a versão mais recente durante este processo. Nota - Se a versão 2012 do agente Gestor de Operações for instalada, não ligue o provisionamento automático (as capacidades de gestão podem ser perdidas quando o servidor Do Gestor de Operações também for a versão 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Qual é o impacto da remoção destas extensões?

Se remover a extensão de monitorização da Microsoft, o Security Center não poderá recolher dados de segurança do VM e algumas recomendações e alertas de segurança não estão disponíveis. Dentro de 24 horas, o Centro de Segurança determina que o VM está a perder a extensão e reinstala a extensão.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Como posso impedir a instalação do agente automático e a criação de espaço de trabalho?

Pode desativar o fornecimento automático para as suas subscrições na política de segurança, mas isso não é recomendado. Desligar o fornecimento automático limita as recomendações e alertas do Centro de Segurança. Para desativar o provisionamento automático:

1. A partir do menu do Centro de Segurança, **selecione definições de preços &**.
1. Selecione a subscrição relevante.
1. Se a sua subscrição tiver o Azure Defender ativado, abra **os planos do Azure Defender** e selecione **o Azure Defender fora**.

    :::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Ativar ou desativar o Azure Defender":::

1. A partir da página **de provisionamento automático,** selecione caneta e desativar o provisionamento automático na política de Segurança – página **de recolha de dados.**

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Ativar a implantação automática para o agente Log Analytics":::

1. Selecione **Guardar**.


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Devo optar por não sair da instalação do agente automático e da criação do espaço de trabalho?

> [!NOTE]
> Certifique-se de rever secções Quais são as [](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) [implicações de excluir?](#what-are-the-implications-of-opting-out-of-automatic-provisioning)

É melhor optar por não prestar provisões automáticas se lhe for aplicável:

- A instalação automática de agentes pelo Security Center aplica-se a toda a subscrição. Não é possível aplicar a instalação automática a um subconjunto de VMs. Se existirem VMs críticos que não podem ser instalados com o agente Log Analytics, então deve optar por não fazer o fornecimento automático.
- A instalação da extensão do agente Log Analytics atualiza a versão do agente. Isto aplica-se a um agente direto e a um agente Gestor de Operações do System Center (neste último, o Gestor de Operações e o agente Log Analytics partilham bibliotecas comuns de tempo de execução - que serão atualizadas no processo). Se o agente Gestor de Operações instalado for a versão 2012 e for atualizado, as capacidades de gestão podem ser perdidas quando o servidor do Gestor de Operações também é versão 2012. Considere excluir o fornecimento automático se o agente Gestor de Operações instalado for a versão 2012.
- Se tiver um espaço de trabalho personalizado externo à subscrição (um espaço de trabalho centralizado), então deve optar por não fazer o provisionamento automático. Pode instalar manualmente a extensão do agente Log Analytics e conectá-la ao seu espaço de trabalho sem que o Centro de Segurança sobrevava a ligação.
- Se quiser evitar a criação de múltiplos espaços de trabalho por subscrição e tiver o seu próprio espaço de trabalho personalizado dentro da subscrição, então tem duas opções:

   1. Pode optar por não fazer o fornecimento automático. Após a migração, defina as definições padrão do espaço de trabalho como descrito em [Como posso utilizar o meu espaço de trabalho log analytics existente?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Ou, pode permitir que a migração esteja concluída, o agente Log Analytics deve ser instalado nos VMs e os VMs ligados ao espaço de trabalho criado. Em seguida, selecione o seu próprio espaço de trabalho personalizado definindo a definição padrão do espaço de trabalho optando por reconfigurar os agentes já instalados. Para mais informações, veja [como posso usar o meu espaço de trabalho log analytics existente?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Quais são as implicações de não optar pelo provisionamento automático?

Quando a migração estiver concluída, o Centro de Segurança não pode recolher dados de segurança do VM e algumas recomendações de segurança e alertas não estão disponíveis. Se optar por não optar, instale manualmente o agente Log Analytics. Consulte [os passos recomendados ao optar por sair](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Quais são os passos recomendados ao excluir o provisionamento automático?

Instale manualmente a extensão do agente Log Analytics para que o Security Center possa recolher dados de segurança dos seus VMs e fornecer recomendações e alertas. Consulte [a instalação do agente para](../virtual-machines/extensions/oms-windows.md) a instalação do Windows VM ou agente para o [Linux VM](../virtual-machines/extensions/oms-linux.md) para obter orientação sobre a instalação.

Pode ligar o agente a qualquer espaço de trabalho personalizado ou ao Centro de Segurança criado. Se um espaço de trabalho personalizado não tiver as soluções "Security" ou "SecurityCenterFree" ativadas, então terá de aplicar uma solução. Para se candidatar, selecione o espaço de trabalho personalizado ou subscrição e aplique um nível de preços através da política de Segurança – página **de nível de preços.**

:::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Ativar ou desativar o Azure Defender":::

O Centro de Segurança permitirá a solução correta no espaço de trabalho com base no nível de preços selecionado.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Como remover as extensões OMS instaladas pelo Security Center?<a name="remove-oms"></a>

Pode remover manualmente o agente Log Analytics. Isto não é recomendado, pois limita as recomendações e alertas do Centro de Segurança.

> [!NOTE]
> Se a recolha de dados estiver ativada, o Security Center reinstalará o agente depois de o remover.  É necessário desativar a recolha de dados antes de remover manualmente o agente. Veja como paro a instalação do agente automático e a criação de espaço de trabalho? para instruções sobre a desativação da recolha de dados.

Para remover manualmente o agente:

1.    No portal, abra **o Log Analytics**.

1.    Na página 'Registar Analytics', selecione um espaço de trabalho:

1.    Selecione os VMs que não pretende monitorizar e **selecione Desligar**.

   ![Remova o agente][3]

> [!NOTE]
> Se um Linux VM já tiver um agente OMS não extensão, remover a extensão também remove o agente e terá de o reinstalar.


## <a name="how-do-i-disable-data-collection"></a>Como desativo a recolha de dados?

O fornecimento automático é altamente recomendado para obter alertas de segurança e recomendações sobre atualizações do sistema, vulnerabilidades de SO e proteção de pontos finais. Por predefinição, o fornecimento automático é desativado.

Se o ativou, mas agora quer desativá-lo:

1. A partir [do portal Azure,](https://portal.azure.com)abra o **Centro de Segurança** e selecione Preços e **configurações**.

1. Selecione a subscrição na qual pretende desativar o provisionamento automático.

1. No **fornecimento automático,** desligue o toggle para o agente Log Analytics.


## <a name="how-do-i-enable-data-collection"></a>Como posso permitir a recolha de dados?

Pode ativar a recolha de dados para a sua subscrição Azure na política de Segurança. Para ativar a recolha de dados. [Inscreva-se no portal Azure](https://portal.azure.com), **selecione Browse,** selecione **Security Center** e selecione a política **de Segurança**. Selecione a subscrição que deseja para permitir o provisionamento automático. Quando seleciona uma política de segurança de subscrição - a **recolha de dados** abre. Sob **provisão automática**, selecione **On**.


## <a name="what-happens-when-data-collection-is-enabled"></a>O que acontece quando a recolha de dados está ativada?

Quando o provisionamento automático é ativado, o Centro de Segurança fornece o agente Log Analytics em todos os VMs Azure suportados e quaisquer novos que sejam criados. Recomenda-se o fornecimento automático, mas a instalação do agente manual também está disponível. [Saiba como instalar a extensão do agente Log Analytics](../azure-monitor/vm/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

O agente permite o evento de criação de processo 4688 e o campo *CommandLine* dentro do evento 4688. Os novos processos criados no VM são registados pelo EventLog e monitorizados pelos serviços de deteção do Security Center. Para obter mais informações sobre os detalhes registados para cada novo processo, consulte [os campos de descrição em 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). O agente também recolhe os 4688 eventos criados no VM e armazena-os em busca.

O agente também permite a recolha de dados para [controlos de aplicações adaptativos,](security-center-adaptive-application.md)o Security Center configura uma política local de AppLocker em modo de auditoria para permitir todas as aplicações. Esta política fará com que o AppLocker gere eventos, que são depois recolhidos e alavancados pelo Security Center. É importante notar que esta política não será configurada em nenhuma máquina em que já exista uma política configurada do AppLocker. 

Quando o Centro de Segurança deteta atividade suspeita no VM, o cliente é notificado por e-mail se tiver sido fornecida [informações de contacto de segurança.](security-center-provide-security-contact-details.md) Um alerta também é visível no painel de alertas de segurança do Centro de Segurança.


## <a name="will-security-center-work-using-an-oms-gateway"></a>O Centro de Segurança funcionará com um gateway OMS?

Sim. O Azure Security Center aproveita o Azure Monitor para recolher dados de VMs e servidores Azure, utilizando o agente Log Analytics.
Para recolher os dados, cada VM e servidor devem ligar-se à Internet utilizando HTTPS. A ligação pode ser direta, utilizando um proxy, ou através do [Portal OMS](../azure-monitor/agents/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>O Agente de Monitorização afeta o desempenho dos meus servidores?

O agente consome uma quantidade nominal de recursos do sistema e deve ter pouco impacto no desempenho. Para obter mais informações sobre o impacto do desempenho e sobre o agente e a extensão, consulte o [guia de planeamento e operações.](security-center-planning-and-operations-guide.md#data-collection-and-storage)




<!--Image references-->
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png