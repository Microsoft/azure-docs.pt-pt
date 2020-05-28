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
ms.openlocfilehash: 022942778b714d5d66ce6eeb2c29351b11c66e40
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996249"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>FAQ - Perguntas sobre recolha de dados, agentes e espaços de trabalho

O Security Center recolhe dados das suas máquinas virtuais Azure (VMs), conjuntos de escala de máquinas virtuais, contentores IaaS e computadores não-Azure (incluindo máquinas no local) para monitorizar vulnerabilidades e ameaças de segurança. Os dados são recolhidos através do agente Log Analytics, que lê várias configurações relacionadas com a segurança e registos de eventos da máquina e copia os dados para o seu espaço de trabalho para análise.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Estou a cobrar registos do Monitor Azure nos espaços de trabalho criados pelo Centro de Segurança?

Não. Espaços de trabalho criados pelo Security Center, enquanto configurados para registos do Monitor Azure por faturação de nó, não incorrem em encargos de registo do Monitor Azure. A faturação do Security Center baseia-se sempre na sua política de segurança do Security Center e nas soluções instaladas num espaço de trabalho:

- **Free tier** – O Security Center permite a solução 'SecurityCenterFree' no espaço de trabalho predefinido. Não será cobrado para o free tier.

- **Standard tier** – O Centro de Segurança permite a solução de 'Segurança' no espaço de trabalho predefinido.

Para obter mais informações sobre os preços, consulte os preços do Centro de [Segurança.](https://azure.microsoft.com/pricing/details/security-center/)

> [!NOTE]
> O nível de preços de log analytics dos espaços de trabalho criados pelo Security Center não afeta a faturação do Security Center.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>O que qualifica um VM para o fornecimento automático da instalação do agente Log Analytics?

Windows ou Linux IaaS VMs qualificam-se se:

- A extensão do agente Log Analytics não está atualmente instalada no VM.
- O VM está em estado de execução.
- O Windows ou o [Linux Azure Virtual Machine Agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) estão instalados.
- O VM não é utilizado como um aparelho como firewall de aplicação web ou firewall de próxima geração.


## <a name="where-is-the-default-log-analytics-workspace-created"></a>Onde é criado o espaço de trabalho padrão log Analytics?

A localização do espaço de trabalho padrão depende da sua região de Azure:

- Para VMs nos Estados Unidos e Brasil o local do espaço de trabalho é os Estados Unidos
- Para VMs no Canadá, a localização do espaço de trabalho é o Canadá
- Para VMs na Europa, a localização do espaço de trabalho é a Europa
- Para VMs no Reino Unido, a localização do espaço de trabalho é o Reino Unido
- Para VMs no Leste asiático e sudeste asiático, a localização do espaço de trabalho é a Ásia
- Para vMs na Coreia, o local do espaço de trabalho é a Coreia
- Para VMs na Índia, a localização do espaço de trabalho é a Índia
- Para VMs no Japão, a localização do espaço de trabalho é o Japão
- Para VMs na China, o local do espaço de trabalho é a China
- Para VMs na Austrália, a localização do espaço de trabalho é a Austrália


## <a name="what-data-is-collected-by-the-log-analytics-agent"></a>Que dados são recolhidos pelo agente Log Analytics?

Para obter uma lista completa das aplicações e serviços monitorizados pelo agente, consulte [o que é monitorizado pelo Azure Monitor?](https://docs.microsoft.com/azure/azure-monitor/monitor-reference#azure-services)

> [!IMPORTANT]
> Note que para alguns serviços, como o Azure Firewall, se tiver ativado a exploração madeireira e escolhido um recurso tagarela para registar (por exemplo, configurar o registo para *verboso), poderá verbosar*impactos significativos nas suas necessidades de armazenamento no espaço de trabalho do Log Analytics. 


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Posso apagar os espaços de trabalho por defeito criados pelo Security Center?

**Não é recomendada a aparas ao espaço de trabalho predefinido.** O Security Center utiliza os espaços de trabalho padrão para armazenar dados de segurança dos seus VMs. Se eliminar um espaço de trabalho, o Security Center não pode recolher estes dados e algumas recomendações e alertas de segurança não estão disponíveis.

Para recuperar, remova o agente Log Analytics nos VMs ligados ao espaço de trabalho apagado. O Security Center reinstala o agente e cria novos espaços de trabalho predefinidos.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Como posso usar o meu espaço de trabalho de Log Analytics existente?

Pode selecionar um espaço de trabalho de Log Analytics existente para armazenar dados recolhidos pelo Security Center. Para utilizar o espaço de trabalho existente no Log Analytics:

- O espaço de trabalho deve ser associado à subscrição do Azure selecionada.
- No mínimo, deve ter lido permissões para aceder ao espaço de trabalho.

Para selecionar um espaço de trabalho existente no Log Analytics:

1. No âmbito **da política de Segurança – Recolha de Dados,** selecione Utilize outro espaço de **trabalho.**

    ![Use outro espaço de trabalho][4]

1. A partir do menu de pull-down, selecione um espaço de trabalho para armazenar dados recolhidos.

    > [!NOTE]
    > No menu pull down, apenas são mostrados espaços de trabalho a que tem acesso e que estão na sua subscrição Azure.

1. Selecione **Guardar**. Será-lhe perguntado se gostaria de reconfigurar os VMs monitorizados.

    - Selecione **Não** se pretender que as novas definições do espaço de trabalho **se apliquem apenas em novos VMs**. As novas definições do espaço de trabalho aplicam-se apenas às instalações de novos agentes; VMs recém-descobertos que não têm o agente Log Analytics instalado.
    - Selecione **Sim** se quiser que as novas definições do espaço de trabalho **se apliquem em todos os VMs**. Além disso, cada VM ligado a um centro de segurança criado espaço de trabalho é reconectado ao novo espaço de trabalho alvo.

    > [!NOTE]
    > Se selecionar **Sim,** não elimine quaisquer espaços de trabalho criados pelo Security Center até que todos os VMs tenham sido reconectados ao novo espaço de trabalho alvo. Esta operação falha se um espaço de trabalho for apagado demasiado cedo.

    - Para cancelar a operação, selecione **Cancelar**.

## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>E se o agente Log Analytics já estivesse instalado como uma extensão no VM?<a name="mmaextensioninstalled"></a>

Quando o Agente de Monitorização é instalado como uma extensão, a configuração da extensão permite reportar apenas um único espaço de trabalho. O Security Center não sobrepor-se às ligações existentes aos espaços de trabalho dos utilizadores. O Security Center armazenará dados de segurança de um VM num espaço de trabalho já ligado, desde que a solução "Security" ou "SecurityCenterFree" tenha sido instalada no mesmo. O Security Center pode atualizar a versão de extensão para a versão mais recente neste processo.

Para obter mais informações, consulte o [fornecimento automático em caso de instalação de um agente pré-existente](security-center-enable-data-collection.md#preexisting).



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>E se um agente Log Analytics for instalado diretamente na máquina, mas não como uma extensão (Agente Direto)?<a name="directagentinstalled"></a>

Se o agente Log Analytics for instalado diretamente no VM (não como uma extensão Azure), o Security Center instalará a extensão do agente Log Analytics e poderá atualizar o agente Log Analytics para a versão mais recente.

O agente instalado continuará a reportar ao seu espaço de trabalho já configurado, e além disso reportará o espaço de trabalho configurado no Security Center (Multi-homing é suportado nas máquinas Windows).

Se o espaço de trabalho configurado for um espaço de trabalho do utilizador (não o espaço de trabalho padrão do Security Center), terá de instalar a solução "Security/"SecurityCenterFree" para o Security Center começar a processar eventos a partir de VMs e computadores que reportem a esse espaço de trabalho.

Para as máquinas Linux, o agente multi-homing ainda não é suportado - portanto, se for detetada uma instalação de agente existente, não ocorrerá um fornecimento automático e a configuração da máquina não será alterada.

Para máquinas existentes em assinaturas a bordo do Centro de Segurança antes de 17 de março de 2019, quando um agente existente for detetado, a extensão do agente Log Analytics não será instalada e a máquina não será afetada. Para estas máquinas, consulte a recomendação "Resolver problemas de saúde do agente de monitorização nas suas máquinas" para resolver os problemas de instalação do agente nestas máquinas

Para mais informações, consulte a secção seguinte O que acontece se um Gestor de [Operações do Centro de Sistema ou um agente direto OMS já estiver instalado no meu VM?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>E se um agente do System Center Operations Manager já estiver instalado no meu VM?<a name="scomomsinstalled"></a>

O centro de segurança instalará a extensão do agente Log Analytics lado a lado com o agente de operações do System Center existente. O agente existente continuará a reportar normalmente ao servidor do Gestor de Operações do Centro de Sistema. Note que o agente do Gestor de Operações e o agente Log Analytics partilham bibliotecas comuns de tempo de execução, que serão atualizadas para a versão mais recente durante este processo. Nota - Se a versão 2012 do agente do Gestor de Operações estiver instalada, não ligue o fornecimento automático (as capacidades de gestão podem ser perdidas quando o servidor do Gestor de Operações também for versão 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Qual é o impacto da remoção destas extensões?

Se remover a extensão de monitorização da Microsoft, o Security Center não poderá recolher dados de segurança do VM e algumas recomendações e alertas de segurança não estão disponíveis. Dentro de 24 horas, o Security Center determina que o VM está a perder a extensão e reinstala a extensão.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Como posso parar a instalação automática de agentes e a criação do espaço de trabalho?

Pode desativar o fornecimento automático para as suas subscrições na política de segurança, mas isso não é recomendado. Desligar o fornecimento automático limita recomendações e alertas do Centro de Segurança. Para desativar o fornecimento automático:

1. Se a sua subscrição estiver configurada para o nível Standard, abra a política de segurança para essa subscrição e selecione o nível **Livre.**

   ![Escalão de preço][1]

1. Em seguida, desligue o fornecimento automático **selecionando** a página de segurança – Página de recolha de **dados.**
   ![Recolha de dados][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Devo optar pela instalação automática de agentes e criação de espaço de trabalho?

> [!NOTE]
> Não se esqueça de rever as secções Quais [recommended steps when opting out](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) são as implicações de optar por não optar por não oprovisionamento [automático.](#what-are-the-implications-of-opting-out-of-automatic-provisioning)

Pode querer optar por não fornecer automaticamente se o seguinte se aplicar a si:

- A instalação automática de agentes pelo Security Center aplica-se a toda a subscrição. Não é possível aplicar a instalação automática a um subconjunto de VMs. Se existem VMs críticos que não podem ser instalados com o agente Log Analytics, então deve optar por não fornecer automaticamente.
- A instalação da extensão do agente Log Analytics atualiza a versão do agente. Isto aplica-se a um agente direto e a um agente gestor de operações do System Center (neste último, o Gestor de Operações e o agente de Log Analytics partilham bibliotecas de prazos comuns - que serão atualizadas no processo). Se o agente do Gestor de Operações instalado for a versão 2012 e for atualizado, as capacidades de gestão podem ser perdidas quando o servidor do Gestor de Operações também for a versão 2012. Considere optar por não fornecer automaticamente se o agente do Gestor de Operações instalado for a versão 2012.
- Se tiver um espaço de trabalho personalizado externo à subscrição (um espaço de trabalho centralizado), então deve optar por não fornecer automaticamente. Pode instalar manualmente a extensão do agente Log Analytics e ligá-lo ao seu espaço de trabalho sem que o Security Center sobreponha à ligação.
- Se pretende evitar a criação de múltiplos espaços de trabalho por subscrição e tem o seu próprio espaço de trabalho personalizado dentro da subscrição, então tem duas opções:

   1. Pode optar por não fornecer o fornecimento automático. Após a migração, detete as definições padrão do espaço de trabalho como descrito em Como posso usar o meu espaço de trabalho existente no [Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Ou, pode permitir que a migração esteja concluída, o agente Log Analytics será instalado nos VMs e os VMs ligados ao espaço de trabalho criado. Em seguida, selecione o seu próprio espaço de trabalho personalizado, definindo a definição padrão do espaço de trabalho com a inpção de reconfigurar os agentes já instalados. Para mais informações, consulte como posso usar o meu espaço de trabalho existente no [Log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Quais são as implicações de não oprovisionamento automático?

Quando a migração está concluída, o Centro de Segurança não pode recolher dados de segurança do VM e algumas recomendações e alertas de segurança não estão disponíveis. Se optar por não optar, instale manualmente o agente Log Analytics. Consulte [os passos recomendados ao optar por não o fazer](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Quais são os passos recomendados ao optar por não fornecer o provisionamento automático?

Instale manualmente a extensão do agente Log Analytics para que o Security Center possa recolher dados de segurança dos seus VMs e fornecer recomendações e alertas. Consulte a [instalação do agente para](../virtual-machines/extensions/oms-windows.md) o Windows VM ou [instalação de agente para o Linux VM](../virtual-machines/extensions/oms-linux.md) para obter orientações sobre a instalação.

Pode ligar o agente a qualquer espaço de trabalho personalizado existente ou o Centro de Segurança criou espaço de trabalho. Se um espaço de trabalho personalizado não tiver as soluções 'Security' ou 'SecurityCenterFree' ativadas, então terá de aplicar uma solução. Para se candidatar, selecione o espaço de trabalho personalizado ou subscrição e aplique um nível de preços através da política de Segurança – Página **de nível** de preços.

   ![Escalão de preço][1]

O Centro de Segurança ativará a solução correta no espaço de trabalho com base no nível de preços selecionado.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Como remover as extensões OMS instaladas pelo Security Center?<a name="remove-oms"></a>

Pode remover manualmente o agente Log Analytics. Isto não é recomendado porque limita recomendações e alertas do Centro de Segurança.

> [!NOTE]
> Se a recolha de dados estiver ativada, o Security Center reinstalará o agente depois de o remover.  Tem de desativar a recolha de dados antes de remover manualmente o agente. Vê como paro a instalação automática de agentes e a criação do espaço de trabalho? para instruções sobre a desativação da recolha de dados.

Para remover manualmente o agente:

1.    No portal, abra **o Log Analytics.**

1.    Na página Log Analytics, selecione um espaço de trabalho:

1.    Selecione os VMs que não pretende monitorizar e selecione **Desligar**.

   ![Remova o agente][3]

> [!NOTE]
> Se um VM Linux já tiver um agente OMS não extensão, remover a extensão também remove o agente e terá de o reinstalar.


## <a name="how-do-i-disable-data-collection"></a>Como desabilito a recolha de dados?

O fornecimento automático é altamente recomendado para obter alertas de segurança e recomendações sobre atualizações do sistema, vulnerabilidades de SO e proteção de pontos finais. Por predefinição, o fornecimento automático é desativado.

Se o habilitaste, mas agora queres desativá-lo:

1. Do [portal Azure,](https://portal.azure.com) **open Security Center** e selecione a política de **segurança.**

1. Selecione a subscrição na qual pretende desativar o fornecimento automático.

    **Política** de segurança - A recolha de dados abre.

1. Sob **o provisionamento automático,** selecione **Off**.


## <a name="how-do-i-enable-data-collection"></a>Como posso permitir a recolha de dados?

Pode ativar a recolha de dados para a sua subscrição Azure na política de Segurança. Para permitir a recolha de dados. [Inscreva-se no portal Azure,](https://portal.azure.com) **selecione Browse,** selecione **Security Center**e selecione a política de **segurança.** Selecione a subscrição que deseja ativar o fornecimento automático. Quando seleciona uma política de segurança de subscrição - A recolha de **dados** abre. Sob **o provisionamento automático,** selecione **On**.


## <a name="what-happens-when-data-collection-is-enabled"></a>O que acontece quando a recolha de dados está ativada?

Quando o fornecimento automático é ativado, o Security Center disponibiliza o agente Log Analytics em todos os VMs Azure suportados e quaisquer novos que sejam criados. Recomenda-se o fornecimento automático, mas a instalação do agente manual também está disponível. [Saiba como instalar a extensão do agente Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

O agente permite o evento de criação de processos 4688 e o campo *CommandLine* dentro do evento 4688. Os novos processos criados no VM são registados pelo EventLog e monitorizados pelos serviços de deteção do Security Center. Para obter mais informações sobre os detalhes registados para cada novo processo, consulte os campos de [descrição no 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). O agente também recolhe os 4688 eventos criados no VM e armazena-os em busca.

O agente também permite a recolha de dados para controlos de [aplicações adaptáveis,](security-center-adaptive-application.md)o Security Center configura uma política local do AppLocker no modo de Auditoria para permitir todas as aplicações. Esta política fará com que o AppLocker gere eventos, que são depois recolhidos e alavancados pelo Security Center. É importante notar que esta política não será configurada em nenhuma máquina em que já exista uma política configurada do AppLocker. 

Quando o Security Center detetar atividades suspeitas no VM, o cliente é notificado por e-mail se as [informações](security-center-provide-security-contact-details.md) de contacto de segurança forem fornecidas. Um alerta também é visível no painel de alertas de segurança do Security Center.


## <a name="will-security-center-work-using-an-oms-gateway"></a>O Centro de Segurança funcionará usando um portal OMS?

Sim. O Azure Security Center aproveita o Azure Monitor para recolher dados de VMs e servidores Azure, utilizando o agente Log Analytics.
Para recolher os dados, cada VM e servidor devem ligar-se à Internet utilizando HTTPS. A ligação pode ser direta, utilizando um proxy, ou através do [Gateway OMS](../azure-monitor/platform/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>O Agente de Monitorização afeta o desempenho dos meus servidores?

O agente consome uma quantidade nominal de recursos do sistema e deve ter pouco impacto no desempenho. Para obter mais informações sobre o impacto do desempenho e sobre o agente e extensão, consulte o guia de [planeamento e operações.](security-center-planning-and-operations-guide.md#data-collection-and-storage)




<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png
