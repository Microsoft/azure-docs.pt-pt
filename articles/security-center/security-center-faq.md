---
title: Perguntas frequentes sobre a central de segurança do Azure | Microsoft Docs
description: Essas perguntas FREQUENTEs respondem a perguntas sobre a central de segurança do Azure, um produto que ajuda você a prevenir, detectar e responder a ameaças.
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
ms.date: 03/19/2019
ms.author: memildin
ms.openlocfilehash: 896db06204188c4347fbdced0b1bb3f216f56ef9
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558665"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure
Essas perguntas frequentes respondem a perguntas sobre a central de segurança do Azure, um serviço que ajuda você a prevenir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança de seus recursos de Microsoft Azure.

> [!NOTE]
> A central de segurança usa o Microsoft Monitoring Agent para coletar e armazenar dados. Para saber mais, confira [migração da plataforma da central de segurança do Azure](security-center-platform-migration.md).
>
>

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-security-center"></a>O que é o Centro de Segurança do Azure?
O Centro de Segurança do Azure ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

### <a name="how-do-i-get-azure-security-center"></a>Como fazer obter a central de segurança do Azure?
A central de segurança do Azure está habilitada com sua assinatura do Microsoft Azure e acessada no [portal do Azure](https://azure.microsoft.com/features/azure-portal/). ([Entre no portal](https://portal.azure.com), selecione **procurar**e role até a central de **segurança**).  

## <a name="billing"></a>Faturação
### <a name="how-does-billing-work-for-azure-security-center"></a>Como funciona a cobrança para a central de segurança do Azure?
O Centro de Segurança é disponibilizado em dois escalões:

A **camada gratuita** fornece visibilidade do estado de segurança de seus recursos do Azure, política básica de segurança, recomendações de segurança e integração com produtos e serviços de segurança de parceiros.

A **camada Standard** adiciona recursos avançados de detecção de ameaças, incluindo inteligência contra ameaças, análise comportamental, detecção de anomalias, incidentes de segurança e relatórios de atribuição de ameaças. Você pode iniciar uma avaliação gratuita da camada Standard. Para atualizar, selecione [tipo de preço](https://docs.microsoft.com/azure/security-center/security-center-pricing) na política de segurança. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

### <a name="how-can-i-track-who-in-my-organization-performed-pricing-tier-changes-in-azure-security-center"></a>Como posso controlar quem na minha organização realizou alterações de tipo de preço na central de segurança do Azure
As assinaturas do Azure podem ter vários administradores com permissões para alterar o tipo de preço. Para descobrir qual usuário realizou uma alteração no tipo de preço, use o log de atividades do Azure. Para obter mais informações, consulte [aqui](https://techcommunity.microsoft.com/t5/Security-Identity/Tracking-Changes-in-the-Pricing-Tier-for-Azure-Security-Center/td-p/390832).

## <a name="permissions"></a>Permissões
O Centro de Segurança do Azure utiliza o [Controlo de Acesso Baseado em Funções (RBAC)](../role-based-access-control/role-assignments-portal.md), que fornece [funções incorporadas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços no Azure.

A central de segurança avalia a configuração de seus recursos para identificar problemas de segurança e vulnerabilidades. Na central de segurança, você só vê informações relacionadas a um recurso quando recebe a função de proprietário, colaborador ou leitor para a assinatura ou grupo de recursos ao qual um recurso pertence.

Consulte [permissões na central de segurança do Azure](security-center-permissions.md) para saber mais sobre funções e ações permitidas na central de segurança.

## <a name="data-collection-agents-and-workspaces"></a>Coleta de dados, agentes e espaços de trabalho
A central de segurança coleta dados de suas VMs (máquinas virtuais) do Azure, conjuntos de dimensionamento de máquinas virtuais, contêineres de IaaS e computadores não Azure (incluindo locais) para monitorar vulnerabilidades de segurança e ameaças. Os dados são recolhidos com o Microsoft Monitoring Agent, que lê várias configurações relacionadas com segurança e registos de eventos a partir da máquina e copia os dados para a sua área de trabalho para análise.

### <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Sou cobrado pelos logs de Azure Monitor nos espaços de trabalho criados pela central de segurança?
Não. Os espaços de trabalho criados pela central de segurança, enquanto configurados para logs de Azure Monitor por nó, não incorrem em cobranças de Azure Monitor logs. A cobrança da central de segurança sempre é baseada em sua política de segurança da central de segurança e nas soluções instaladas em um espaço de trabalho:

- **Camada gratuita** – a central de segurança habilita a solução ' SecurityCenterFree ' no espaço de trabalho padrão. Você não será cobrado pela camada gratuita.
- **Camada Standard** – a central de segurança habilita a solução ' segurança ' no espaço de trabalho padrão.

Para obter mais informações sobre preços, consulte [preços da central de segurança](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> O tipo de preço do log Analytics de espaços de trabalho criados pela central de segurança não afeta a cobrança da central de segurança.
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>O que qualifica uma VM para o provisionamento automático da instalação do Microsoft Monitoring Agent?
As VMs IaaS Windows ou Linux se qualificam se:

- A extensão Microsoft Monitoring Agent não está instalada atualmente na VM.
- A VM está em estado de execução.
- O [agente de máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) para Windows ou Linux está instalado.
- A VM não é usada como um dispositivo como o Firewall do aplicativo Web ou o firewall da próxima geração.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Posso excluir os espaços de trabalho padrão criados pela central de segurança?
**Não é recomendável excluir o espaço de trabalho padrão.** A central de segurança usa os espaços de trabalho padrão para armazenar dados de segurança de suas VMs.  Se você excluir um espaço de trabalho, a central de segurança não poderá coletar esses dados e algumas recomendações de segurança e alertas não estarão disponíveis.

Para recuperar, remova o Microsoft Monitoring Agent nas VMs conectadas ao espaço de trabalho excluído. A central de segurança reinstala o agente e cria novos espaços de trabalho padrão.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Como posso usar meu espaço de trabalho existente do Log Analytics?

Você pode selecionar um espaço de trabalho Log Analytics existente para armazenar os dados coletados pela central de segurança. Para usar seu espaço de trabalho Log Analytics existente:

- O espaço de trabalho deve ser associado à sua assinatura do Azure selecionada.
- No mínimo, você deve ter permissões de leitura para acessar o espaço de trabalho.

Para selecionar um espaço de trabalho de Log Analytics existente:

1. Em **política de segurança – coleta de dados**, selecione **usar outro espaço de trabalho**.

   ![Usar outro espaço de trabalho][5]

2. No menu suspenso, selecione um espaço de trabalho para armazenar os dados coletados.

   > [!NOTE]
   > No menu suspenso, somente espaços de trabalho aos quais você tem acesso e estão em sua assinatura do Azure são mostrados.
   >
   >

3. Selecione **Guardar**.
4. Depois de selecionar **salvar**, você será perguntado se deseja reconfigurar as VMs monitoradas.

   - Selecione **não** se você quiser que as novas configurações de espaço de trabalho sejam **aplicadas somente a novas VMs**. As novas configurações de espaço de trabalho se aplicam somente a novas instalações de agente; VMs recém-descobertas que não têm o Microsoft Monitoring Agent instalado.
   - Selecione **Sim** se desejar que as novas configurações de espaço de trabalho sejam **aplicadas em todas as VMs**. Além disso, todas as VMs conectadas a um espaço de trabalho criado pela central de segurança são reconectadas ao novo espaço de trabalho de destino.

   > [!NOTE]
   > Se você selecionar Sim, não deverá excluir os espaços de trabalho criados pela central de segurança até que todas as VMs tenham sido reconectadas ao novo espaço de trabalho de destino. Essa operação falhará se um espaço de trabalho for excluído muito cedo.
   >
   >

   - Selecione **Cancelar** para cancelar a operação.

### E se o Microsoft Monitoring Agent já tiver sido instalado como uma extensão na VM?<a name="mmaextensioninstalled"></a>
Quando o agente de monitoramento é instalado como uma extensão, a configuração de extensão permite relatar apenas um único espaço de trabalho. A central de segurança não substitui as conexões existentes aos espaços de trabalho do usuário. A central de segurança armazenará dados de segurança de uma VM em um espaço de trabalho que já está conectado, desde que a solução "segurança" ou "SecurityCenterFree" tenha sido instalada nela. A central de segurança pode atualizar a versão da extensão para a versão mais recente neste processo.

Para obter mais informações, consulte [provisionamento automático em casos de uma instalação de agente pré-existente](security-center-enable-data-collection.md#preexisting).


### E se eu tivesse um Microsoft Monitoring Agent estiver instalado diretamente no computador, mas não como uma extensão (agente direto)?<a name="directagentinstalled"></a>
Se o Microsoft Monitoring Agent for instalado diretamente na VM (não como uma extensão do Azure), a central de segurança instalará a extensão de Microsoft Monitoring Agent e poderá atualizar o Microsoft Monitoring Agent para a versão mais recente.
O agente instalado continuará a relatar seus espaços de trabalho já configurados e, além disso, relatará ao espaço de trabalho configurado na central de segurança (há suporte para hospedagem múltipla em computadores Windows).
Se o espaço de trabalho configurado for um espaço de trabalho do usuário (não o espaço de trabalho padrão da central de segurança), você precisará instalar a solução "Security/" SecurityCenterFree "nela para a central de segurança iniciar o processamento de eventos de VMs e computadores que se reportam a esse espaço de trabalho.

Para computadores Linux, o agente de hospedagem múltipla ainda não tem suporte-portanto, se uma instalação de agente existente for detectada, o provisionamento automático não ocorrerá e a configuração da máquina não será alterada.

Para computadores existentes nas assinaturas integradas à central de segurança antes de 17 2019 de março, quando um agente existente for detectado, a extensão de Microsoft Monitoring Agent não será instalada e o computador não será afetado. Para esses computadores, consulte a recomendação "resolver problemas de integridade do agente de monitoramento em suas máquinas" para resolver os problemas de instalação do agente nesses computadores

 Para obter mais informações, consulte a próxima seção [o que acontecerá se um System Center Operations Manager ou agente direto do OMS já estiver instalado em minha VM?](#scomomsinstalled)

### O que acontece se um agente de System Center Operations Manager já estiver instalado em minha VM?<a name="scomomsinstalled"></a>
A central de segurança instalará a extensão de Microsoft Monitoring Agent lado a lado no agente de System Center Operations Manager existente. O agente existente continuará a relatar para o servidor de System Center Operations Manager normalmente. Observe que o agente de Operations Manager e Microsoft Monitoring Agent compartilham bibliotecas comuns de tempo de execução, que serão atualizadas para a versão mais recente durante esse processo. Observação: se a versão 2012 do agente de Operations Manager estiver instalada, não ative o provisionamento automático (os recursos de gerenciamento podem ser perdidos quando o servidor de Operations Manager também for a versão 2012).

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Qual é o impacto da remoção dessas extensões?
Se você remover a extensão de monitoramento da Microsoft, a central de segurança não poderá coletar dados de segurança da VM e algumas recomendações de segurança e alertas não estarão disponíveis. Dentro de 24 horas, a central de segurança determina que a VM não tem a extensão e reinstala a extensão.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Como fazer interromper a instalação automática do agente e a criação do espaço de trabalho?
Você pode desativar o provisionamento automático para suas assinaturas na política de segurança, mas isso não é recomendado. Desativar o provisionamento automático limita os alertas e recomendações da central de segurança. Para desabilitar o provisionamento automático:

1. Se sua assinatura estiver configurada para a camada Standard, abra a política de segurança para essa assinatura e selecione a camada **gratuita** .

   ![Escalão de preço][1]

2. Em seguida, desative o provisionamento **automático selecionando** na página **política de segurança – coleta de dados** .
   ![Recolha de dados][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Devo recusar a instalação automática do agente e a criação do espaço de trabalho?

> [!NOTE]
> Certifique-se de examinar as seções [quais são as implicações de se recusar?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) e [as etapas recomendadas quando](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) você opta por recusar o provisionamento automático.
>
>

Talvez você queira recusar o provisionamento automático se o seguinte se aplicar a você:

- A instalação automática de agente pela central de segurança se aplica à assinatura inteira. Não é possível aplicar a instalação automática a um subconjunto de VMs. Se houver VMs críticas que não podem ser instaladas com o Microsoft Monitoring Agent, você deverá recusar o provisionamento automático.
- A instalação da extensão Microsoft Monitoring Agent (MMA) atualiza a versão do agente. Isso se aplica a um agente direto e um agente de System Center Operations Manager (no último, o Operations Manager e MMA compartilham bibliotecas de tempo de execução comuns, que serão atualizadas no processo). Se o agente de Operations Manager instalado for a versão 2012 e for atualizado, os recursos de gerenciamento poderão ser perdidos quando o servidor de Operations Manager também for a versão 2012. Considere a possibilidade de recusar o provisionamento automático se o agente de Operations Manager instalado for a versão 2012.
- Se você tiver um espaço de trabalho personalizado externo à assinatura (um espaço de trabalho centralizado), deverá recusar o provisionamento automático. Você pode instalar manualmente a extensão de Microsoft Monitoring Agent e conectá-la ao seu espaço de trabalho sem a central de segurança substituindo a conexão.
- Se você quiser evitar a criação de vários espaços de trabalho por assinatura e tiver seu próprio espaço de trabalho personalizado dentro da assinatura, terá duas opções:

   1. Você pode recusar o provisionamento automático. Após a migração, defina as configurações padrão do espaço de trabalho, conforme descrito em [como posso usar meu espaço de trabalho existente do log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Ou, você pode permitir que a migração seja concluída, o Microsoft Monitoring Agent a ser instalado nas VMs e as VMs conectadas ao espaço de trabalho criado. Em seguida, selecione seu próprio espaço de trabalho personalizado definindo a configuração padrão do espaço de trabalho com a opção de se recusar a reconfigurar os agentes já instalados. Para obter mais informações, consulte [como posso usar meu espaço de trabalho existente do log Analytics?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Quais são as implicações de recusar o provisionamento automático?
Quando a migração for concluída, a central de segurança não poderá coletar dados de segurança da VM e algumas recomendações e alertas de segurança não estarão disponíveis. Se você recusar, instale o Microsoft Monitoring Agent manualmente. Consulte [as etapas recomendadas ao recusar](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Quais são as etapas recomendadas ao recusar o provisionamento automático?

Instale manualmente a extensão de Microsoft Monitoring Agent para que a central de segurança possa coletar dados de segurança de suas VMs e fornecer recomendações e alertas. Consulte [instalação do agente para VM do Windows](../virtual-machines/extensions/oms-windows.md) ou [instalação do agente para VM do Linux](../virtual-machines/extensions/oms-linux.md) para obter orientação sobre a instalação.

Você pode conectar o agente a qualquer espaço de trabalho personalizado existente ou espaço de trabalho criado na central de segurança. Se um espaço de trabalho personalizado não tiver as soluções ' Security ' ou ' SecurityCenterFree ' habilitadas, será necessário aplicar uma solução. Para aplicar, selecione o espaço de trabalho ou a assinatura personalizada e aplique um tipo de preço por meio da página **política de segurança – tipo de preço** .

   ![Escalão de preço][1]

A central de segurança habilitará a solução correta no espaço de trabalho com base no tipo de preço selecionado.

### Como fazer remover extensões do OMS instaladas pela central de segurança?<a name="remove-oms"></a>
Você pode remover manualmente o Microsoft Monitoring Agent. Isso não é recomendado, pois limita as recomendações e alertas da central de segurança.

> [!NOTE]
> Se a coleta de dados estiver habilitada, a central de segurança reinstalará o agente depois de removê-lo.  Você precisa desabilitar a coleta de dados antes de remover manualmente o agente. Consulte Como fazer parar a instalação automática do agente e a criação do espaço de trabalho? para obter instruções sobre como desabilitar a coleta de dados.
>
>

Para remover manualmente o agente:

1.  No portal, abra **log Analytics**.
2.  Na página Log Analytics, selecione um espaço de trabalho:
3.  Selecione as VMs que você não deseja monitorar e selecione **Desconectar**.

   ![Remover o agente][3]

> [!NOTE]
> Se uma VM do Linux já tiver um agente do OMS sem extensão, remover a extensão também removerá o agente e o cliente precisará reinstalá-lo.
>
>
### <a name="how-do-i-disable-data-collection"></a>Como fazer desabilitar a coleta de dados?
O provisionamento automático está desativado por padrão. Você pode desabilitar o provisionamento automático de recursos a qualquer momento desativando essa configuração na política de segurança. O provisionamento automático é altamente recomendado para obter alertas de segurança e recomendações sobre atualizações do sistema, vulnerabilidades do sistema operacional e proteção de ponto de extremidade.

Para desabilitar a coleta de dados, [entre no portal do Azure](https://portal.azure.com), selecione **procurar**, **central de segurança**e selecione **política**. Selecione a subscrição para a qual pretende desativar o aprovisionamento automático. Quando você seleciona uma **política de segurança de assinatura, a coleta de dados** é aberta. Em **provisionamento automático**, selecione **desativado**.

### <a name="how-do-i-enable-data-collection"></a>Como fazer habilitar a coleta de dados?
Você pode habilitar a coleta de dados para sua assinatura do Azure na política de segurança. Para habilitar a coleta de dados. [Entre no portal do Azure](https://portal.azure.com), selecione **procurar**, **central de segurança**e selecione política de **segurança**. Selecione a assinatura para a qual você deseja habilitar o provisionamento automático. Quando você seleciona uma **política de segurança de assinatura, a coleta de dados** é aberta. Em **provisionamento automático**, selecione **ativado**.

### <a name="what-happens-when-data-collection-is-enabled"></a>O que acontece quando a coleta de dados está habilitada?
Quando o provisionamento automático está habilitado, a central de segurança provisiona o Microsoft Monitoring Agent em todas as VMs do Azure com suporte e quaisquer novas que são criadas. O provisionamento automático é recomendado, mas a instalação manual de agente também está disponível. [Saiba como instalar a extensão do MMA](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

O agente habilita o evento 4688 de criação de processo e o campo *CommandLine* dentro do evento 4688. Novos processos criados na VM são registrados pelo log de eventos e monitorados pelos serviços de detecção da central de segurança. Para obter mais informações sobre os detalhes registrados para cada novo processo, consulte os [campos de descrição em 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). O agente também coleta os eventos 4688 criados na VM e os armazena em pesquisa.

O agente também habilita a coleta de dados para [controles de aplicativo adaptáveis](security-center-adaptive-application.md), a central de segurança configura uma política do AppLocker local no modo de auditoria para permitir todos os aplicativos. Essa política fará com que o AppLocker gere eventos, que são coletados e aproveitados pela central de segurança. É importante observar que essa política não será configurada em nenhum computador no qual já exista uma política do AppLocker configurada. 

Quando a central de segurança detectar atividade suspeita na VM, o cliente será notificado por email se [as informações de contato de segurança](security-center-provide-security-contact-details.md) tiverem sido fornecidas. Um alerta também é visível no painel alertas de segurança da central de segurança.

### <a name="will-security-center-work-using-an-oms-gateway"></a>A central de segurança funcionará usando um gateway do OMS?
Sim. A central de segurança do Azure aproveita Azure Monitor para coletar dados de VMs e servidores do Azure, usando o Microsoft Monitoring Agent.
Para coletar os dados, cada VM e servidor devem se conectar à Internet usando HTTPS. A conexão pode ser direta, usando um proxy ou por meio do [gateway do OMS](../azure-monitor/platform/gateway.md).

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>O agente de monitoramento afeta o desempenho dos meus servidores?
O agente consome uma quantidade nominal de recursos do sistema e deve ter pouco impacto sobre o desempenho. Para obter mais informações sobre o impacto no desempenho e o agente e a extensão, consulte o [Guia de planejamento e operações](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Onde são armazenados os meus dados?
Os dados coletados desse agente são armazenados em um espaço de trabalho Log Analytics existente associado à sua assinatura ou a um novo espaço de trabalho. Para obter mais informações, consulte [segurança de dados](security-center-data-security.md).

## Clientes existentes de logs de Azure Monitor<a name="existingloganalyticscust"></a>

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>A central de segurança substitui todas as conexões existentes entre as VMs e os espaços de trabalho?
Se uma VM já tiver o Microsoft Monitoring Agent instalado como uma extensão do Azure, a central de segurança não substituirá a conexão de espaço de trabalho existente. Em vez disso, a central de segurança usa o espaço de trabalho existente. A VM será protegida desde que a solução "segurança" ou "SecurityCenterFree" tenha sido instalada no espaço de trabalho para o qual está se comunicando. 

Uma solução da central de segurança é instalada no espaço de trabalho selecionado na tela de coleta de dados, se ainda não estiver presente, e a solução será aplicada somente às VMs relevantes. Quando você adiciona uma solução, ela é implantada automaticamente por padrão para todos os agentes do Windows e Linux conectados ao seu espaço de trabalho do Log Analytics. O [direcionamento de solução](../operations-management-suite/operations-management-suite-solution-targeting.md) permite que você aplique um escopo às suas soluções.

Se o Microsoft Monitoring Agent for instalado diretamente na VM (não como uma extensão do Azure), a central de segurança não instalará o Microsoft Monitoring Agent e o monitoramento de segurança será limitado.

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>A central de segurança instala soluções em meus espaços de trabalho existentes do Log Analytics? Quais são as implicações de cobrança?
Quando a central de segurança identifica que uma VM já está conectada a um espaço de trabalho que você criou, a central de segurança habilita soluções nesse espaço de trabalho de acordo com seu tipo de preço. As soluções são aplicadas somente às VMs relevantes do Azure, por meio do [direcionamento de solução](../operations-management-suite/operations-management-suite-solution-targeting.md), para que a cobrança permaneça a mesma.

- **Camada gratuita** – a central de segurança instala a solução ' SecurityCenterFree ' no espaço de trabalho. Você não será cobrado pela camada gratuita.
- **Camada Standard** – a central de segurança instala a solução "segurança" no espaço de trabalho.

   ![Soluções no espaço de trabalho padrão][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Eu já tenho espaços de trabalho em meu ambiente, posso usá-los para coletar dados de segurança?
Se uma VM já tiver o Microsoft Monitoring Agent instalado como uma extensão do Azure, a central de segurança usará o espaço de trabalho conectado existente. Uma solução da central de segurança será instalada no espaço de trabalho, se ainda não estiver presente, e a solução será aplicada somente às VMs relevantes por meio do [direcionamento de solução](../operations-management-suite/operations-management-suite-solution-targeting.md).

Quando a central de segurança instala o Microsoft Monitoring Agent em VMs, ela usa os espaços de trabalho padrão criados pela central de segurança.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Já tenho uma solução de segurança em meus espaços de trabalho. Quais são as implicações de cobrança?
A solução de auditoria de & de segurança é usada para habilitar recursos da camada Standard da central de segurança para VMs do Azure. Se a solução de auditoria de segurança & já estiver instalada em um espaço de trabalho, a central de segurança usará a solução existente. Não há nenhuma alteração na cobrança.

## <a name="using-azure-security-center"></a>Usando a central de segurança do Azure
### <a name="what-is-a-security-policy"></a>O que é uma política de segurança?
Uma política de segurança define o conjunto de controles que são recomendados para recursos dentro da assinatura especificada. Na central de segurança do Azure, você define políticas para suas assinaturas do Azure de acordo com os requisitos de segurança da sua empresa e o tipo de aplicativos ou a sensibilidade dos dados em cada assinatura.

As políticas de segurança habilitadas na central de segurança do Azure orientam as recomendações de segurança e monitoramento. Para saber mais sobre as políticas de segurança, consulte [monitoramento de integridade de segurança na central de segurança do Azure](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?
Para modificar uma política de segurança, você deve ser um administrador de segurança ou um proprietário ou colaborador dessa assinatura.

Para saber como configurar uma política de segurança, consulte [definindo políticas de segurança na central de segurança do Azure](tutorial-security-policy.md).

### <a name="what-is-a-security-recommendation"></a>O que é uma recomendação de segurança?
O Centro de Segurança do Azure analisa o estado de segurança dos recursos do Azure. Quando possíveis vulnerabilidades de segurança são identificadas, são criadas recomendações. As recomendações orientam você pelo processo de configuração do controle necessário. Os exemplos são:

* Provisionamento de anti-malware para ajudar a identificar e remover softwares mal-intencionados
* [Grupos de segurança de rede](../virtual-network/security-overview.md) e regras para controlar o tráfego para máquinas virtuais
* Provisionamento de um firewall do aplicativo Web para ajudar a proteger contra ataques direcionados a seus aplicativos Web
* Implementação de atualizações do sistema em falta
* Abordagem de configurações de SO que não correspondam às linhas de base recomendadas

Somente as recomendações habilitadas em políticas de segurança são mostradas aqui.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Como posso ver o estado de segurança atual dos meus recursos do Azure?
A página **visão geral da central de segurança** mostra a postura de segurança geral do seu ambiente dividido por computação, rede, armazenamento & dados e aplicativos. Cada tipo de recurso tem um indicador mostrando se alguma vulnerabilidade de segurança em potencial foi identificada. Clicar em cada bloco exibe uma lista de problemas de segurança identificados pela central de segurança, juntamente com um inventário dos recursos em sua assinatura.

### <a name="what-triggers-a-security-alert"></a>O que dispara um alerta de segurança?
A central de segurança do Azure coleta, analisa e fusível automaticamente dados de log de seus recursos do Azure, da rede e de soluções de parceiros, como antimalware e firewalls. Quando são detetadas ameaças, é criado um alerta de segurança. Os exemplos incluem a deteção de:

* Virtual Machines comprometidas a comunicar com endereços IP maliciosos conhecidos
* Malware avançado detectado usando o relatório de erros do Windows
* Ataques de força bruta contra máquinas virtuais
* Alertas de segurança de soluções de segurança de parceiros integradas, como anti-malware ou firewalls de aplicativos Web

### Por que os valores de Pontuação seguros são alterados? <a name="secure-score-faq"></a>
A partir de fevereiro de 2019, a central de segurança ajustou a pontuação de algumas recomendações, a fim de se adequar melhor à sua gravidade. Como resultado desse ajuste, pode haver alterações nos valores gerais de Pontuação segura.  Para obter mais informações sobre Pontuação segura, consulte [cálculo de Pontuação segura](security-center-secure-score.md).

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Qual é a diferença entre ameaças detectadas e alertadas pelo Microsoft Security Response Center em comparação com a central de segurança do Azure?
O MSRC (Microsoft Security Response Center) executa o monitoramento de segurança selecionado da rede e da infraestrutura do Azure e recebe a inteligência contra ameaças e as reclamações de abuso de terceiros. Quando o MSRC fica ciente de que os dados do cliente foram acessados por uma parte ilegal ou não autorizada ou que o uso do cliente do Azure não está em conformidade com os termos de uso aceitável, um Gerenciador de incidentes de segurança notifica o cliente. A notificação normalmente ocorre enviando um email para os contatos de segurança especificados na central de segurança do Azure ou o proprietário da assinatura do Azure se um contato de segurança não for especificado.

A central de segurança é um serviço do Azure que monitora continuamente o ambiente do Azure do cliente e aplica a análise para detectar automaticamente uma ampla gama de atividades potencialmente mal-intencionadas. Essas detecções são exibidas como alertas de segurança no painel da central de segurança.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Quais recursos do Azure são monitorados pela central de segurança do Azure?
A central de segurança do Azure monitora os seguintes recursos do Azure:

* VMs (máquinas virtuais) (incluindo [serviços de nuvem](../cloud-services/cloud-services-choose-me.md))
* Conjuntos de dimensionamento de máquinas virtuais
* Redes Virtuais do Azure
* Serviço SQL do Azure
* Conta de armazenamento do Azure
* Aplicativos Web do Azure (no [ambiente do serviço de aplicativo](../app-service/environment/intro.md))
* Soluções de parceiros integradas à sua assinatura do Azure, como um firewall de aplicativo Web em VMs e em Ambiente do Serviço de Aplicativo

Além disso, os computadores não Azure (incluindo locais) também podem ser monitorados pela central de segurança do Azure (os computadores com [Windows](./quick-onboard-windows-computer.md) e [Linux](./quick-onboard-linux-computer.md) têm suporte)

## <a name="virtual-machines"></a>Máquinas Virtuais
### <a name="what-types-of-virtual-machines-are-supported"></a>Quais tipos de máquinas virtuais têm suporte?
O monitoramento e as recomendações estão disponíveis para VMs (máquinas virtuais) criadas usando os [modelos de implantação clássico e do Resource Manager](../azure-classic-rm.md).

Consulte [plataformas com suporte na central de segurança do Azure](security-center-os-coverage.md) para obter uma lista de plataformas com suporte.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Por que a central de segurança do Azure não reconhece a solução antimalware em execução na minha VM do Azure?
A central de segurança do Azure tem visibilidade do Antimalware instalado por meio das extensões do Azure. Por exemplo, a central de segurança não é capaz de detectar antimalware que foi pré-instalado em uma imagem fornecida ou se você instalou o antimalware em suas máquinas virtuais usando seus próprios processos (como sistemas de gerenciamento de configuração).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Por que recebo a mensagem "dados de verificação ausentes" para minha VM?
Esta mensagem é apresentada quando não existem dados de análise para uma VM. Pode demorar algum tempo (menos de uma hora) para que os dados de análise sejam preenchidos após a ativação da Recolha de Dados no Centro de Segurança do Azure. Após o preenchimento inicial dos dados de análise, pode receber esta mensagem pois não existem dados de análise ou não existem dados de análise recentes. As análises não preenchem uma VM no estado parado. Essa mensagem também pode aparecer se os dados de verificação não foram preenchidos recentemente (de acordo com a política de retenção para o agente do Windows, que tem um valor padrão de 30 dias).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Com que frequência a central de segurança verifica vulnerabilidades do sistema operacional, atualizações do sistema e problemas do Endpoint Protection?
Abaixo estão os tempos de latência para verificações de vulnerabilidades, atualizações e problemas da central de segurança:

- Configurações de segurança do sistema operacional – os dados são atualizados em até 48 horas
- Atualizações do sistema – os dados são atualizados dentro de 24 horas
- Problemas de Endpoint Protection – os dados são atualizados dentro de 8 horas

A central de segurança geralmente procura novos dados a cada hora e atualiza as recomendações de acordo. 

> [!NOTE]
> A central de segurança usa o Microsoft Monitoring Agent para coletar e armazenar dados. Para saber mais, confira [migração da plataforma da central de segurança do Azure](security-center-platform-migration.md).
>
>

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Por que recebo a mensagem "o agente de VM está ausente?"
O agente de VM deve ser instalado em VMs para habilitar a coleta de dados. O Agente de VM é instalado por predefinição em VMs que são implementadas a partir do Azure Marketplace. Para obter informações sobre como instalar o agente de VM em outras VMs, consulte a postagem de blog [agente e extensões de VM](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
