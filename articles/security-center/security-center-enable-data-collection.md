---
title: Agentes de implantação automática para o Centro de Segurança Azure | Microsoft Docs
description: Este artigo descreve como configurar o fornecimento automático do agente Log Analytics e outros agentes e extensões utilizados pelo Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: d9d0739704a9f5f16bdbde80661192b2f1ca9bb1
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099425"
---
# <a name="configure-auto-provisioning-for-agents-and-extensions-from-azure-security-center"></a>Configurar provisão automática para agentes e extensões do Centro de Segurança Azure

O Security Center recolhe dados dos seus recursos utilizando o agente ou extensões relevantes para esse recurso e o tipo de recolha de dados que ativou. Utilize as precedências abaixo para garantir que o seu recurso tem o necessário Este artigo descreve como configurar o fornecimento automático do agente Log Analytics e outros agentes e extensões utilizados pelo Azure Security Center

## <a name="prerequisites"></a>Pré-requisitos
Para começar a utilizar o Centro de Segurança, tem de possuir uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="availability"></a>Disponibilidade

| Aspeto                  | Detalhes                                                                                                                                                                                                                      |
|-------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Estado de libertação:          | **Característica**: O provisionamento automático está geralmente disponível (GA)<br>**Agente e extensões**: O agente Log Analytics para VMs Azure é GA, o agente da Microsoft Dependency está em pré-visualização, o Policy Add-on para Kubernetes é GA                |
| Preços:                | Gratuito                                                                                                                                                                                                                         |
| Destinos apoiados: | ![Sim](./media/icons/yes-icon.png) Máquinas virtuais do Azure<br>![Não](./media/icons/no-icon.png) Máquinas Azure Arc<br>![Não](./media/icons/no-icon.png) Os acenos de Kubernetes<br>![Não](./media/icons/no-icon.png) Conjuntos de Dimensionamento de Máquinas Virtuais |
| Nuvens:                 | ![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) EUA Gov, China Gov, Outros Gov                                                                                                      |
|                         |                                                                                                                                                                                                                              |

## <a name="how-does-security-center-collect-data"></a>Como é que o Security Center recolhe dados?

O Security Center recolhe dados das suas máquinas virtuais Azure (VMs), conjuntos de escala de máquinas virtuais, contentores IaaS e máquinas não-Azure (incluindo no local) para monitorizar vulnerabilidades e ameaças de segurança. 

A recolha de dados é necessária para fornecer visibilidade em atualizações em falta, definições de segurança de SO mal configuradas, estado de proteção de ponto final e proteção para a saúde e ameaças. A recolha de dados só é necessária para recursos de computação (VMs, conjuntos de escala de máquinas virtuais, recipientes IaaS e computadores não-Azure). Pode beneficiar do Azure Security Center mesmo que não provisa agentes; no entanto, terá uma segurança limitada e as capacidades acima enumeradas não são suportadas.  

Os dados são recolhidos utilizando:

- O **agente Log Analytics,** que lê várias configurações relacionadas com a segurança e registos de eventos da máquina e copia os dados para o seu espaço de trabalho para análise. Exemplos desses dados são: tipo e versão do sistema operativo, registos do sistema operativo (registos de eventos windows), processos de execução, nome da máquina, endereços IP e registados no utilizador.
- **Extensões de** segurança , como o [Azure Policy Add-on for Kubernetes,](../governance/policy/concepts/policy-for-kubernetes.md)que também pode fornecer dados ao Security Center sobre tipos de recursos especializados.

> [!TIP]
> À medida que o Centro de Segurança cresceu, os tipos de recursos que podem ser monitorizados também cresceram. O número de prorrogações também aumentou. O fornecimento automático expandiu-se para suportar tipos adicionais de recursos, aproveitando as capacidades da Azure Policy.

## <a name="why-use-auto-provisioning"></a>Porquê usar o provisionamento automático?
Qualquer um dos agentes e extensões descritos nesta página *pode* ser instalado manualmente (ver [instalação manual do agente Log Analytics).](#manual-agent) No entanto, **o fornecimento automático** reduz a sobrecarga de gestão instalando todos os agentes e extensões necessários em máquinas existentes - e novas - para garantir uma cobertura de segurança mais rápida para todos os recursos suportados. 

Recomendamos permitir o provisionamento automático, mas é desativado por padrão.

## <a name="how-does-auto-provisioning-work"></a>Como funciona o fornecimento de automóveis?
As definições de provisionamento automático do Security Center têm um alternador para cada tipo de extensão suportada. Quando ativa o fornecimento automático de uma extensão, atribui a política adequada **de Implementação, caso não exista,** para garantir que a extensão é prevista em todos os recursos existentes e futuros desse tipo.

> [!TIP]
> Saiba mais sobre os efeitos da Política Azure, incluindo o implementação, se não existir nos [efeitos da Política de Azure](../governance/policy/concepts/effects.md).


## <a name="enable-auto-provisioning-of-the-log-analytics-agent-and-extensions"></a>Permitir o provisionamento automático do agente e extensões do Log Analytics <a name="auto-provision-mma"></a>

Quando o fornecimento automático está ligado para o agente Log Analytics, o Security Center implementa o agente em todos os VMs Azure suportados e quaisquer novos criados. Para a lista de plataformas apoiadas, consulte [plataformas apoiadas no Centro de Segurança Azure.](security-center-os-coverage.md)

Para permitir o provisionamento automático do agente Log Analytics:

1. A partir do menu do Centro de Segurança, **selecione definições de preços &**.
1. Selecione a subscrição relevante.
1. Na página **de provisionamento automático,** desa estale o estado do agente Log Analytics para **On**.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Habilitação automática do agente Log Analytics":::

1. A partir do painel de opções de configuração, defina o espaço de trabalho para utilizar.

    :::image type="content" source="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png" alt-text="Opções de configuração para provisão automática de agentes do Log Analytics para VMs" lightbox="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png":::

    - **Ligue os VMs do Azure ao espaço de trabalho predefinido criado pelo Security Center** - Security Center cria um novo grupo de recursos e espaço de trabalho predefinido na mesma geolocalização, e liga o agente a esse espaço de trabalho. Se uma subscrição contiver VMs de múltiplas geolocalizaçãos, o Security Center cria vários espaços de trabalho para garantir o cumprimento dos requisitos de privacidade dos dados.

        A convenção de nomeação para o espaço de trabalho e grupo de recursos é: 
        - Área de trabalho: DefaultWorkspace-[subscription-ID]-[geo] 
        - Grupo de Recursos: DefaultResourceGroup-[geo] 

        O Security Center permite automaticamente uma solução do Centro de Segurança no espaço de trabalho por nível de preços definido para a subscrição. 

        > [!TIP]
        > Para questões relacionadas com espaços de trabalho predefinidos, consulte:
        >
        > - [Estou cobrado para registos do Monitor Azure nos espaços de trabalho criados pelo Security Center?](faq-data-collection-agents.md#am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center)
        > - [Onde é criado o espaço de trabalho padrão do Log Analytics?](faq-data-collection-agents.md#where-is-the-default-log-analytics-workspace-created)
        > - [Posso eliminar os espaços de trabalho predefinidos criados pelo Security Center?](faq-data-collection-agents.md#can-i-delete-the-default-workspaces-created-by-security-center)

    - **Ligue os VMs do Azure a um espaço de trabalho diferente** - A partir da lista de dropdown, selecione o espaço de trabalho para armazenar dados recolhidos. A lista de dropdown inclui todos os espaços de trabalho em todas as suas subscrições. Pode utilizar esta opção para recolher dados de máquinas virtuais que executam diferentes subscrições e armazená-lo no seu espaço de trabalho selecionado.  

        Se já tiver um espaço de trabalho log analytics existente, talvez queira utilizar o mesmo espaço de trabalho (requer permissões de leitura e de escrita no espaço de trabalho). Esta opção é útil se estiver a utilizar um espaço de trabalho centralizado na sua organização e quiser usá-lo para recolha de dados de segurança. Saiba mais em [Gerir o acesso a dados de registo e espaços de trabalho no Azure Monitor.](../azure-monitor/logs/manage-access.md)

        Se o seu espaço de trabalho selecionado já tiver uma solução "SecurityCenterFree" ativada, o preço será definido automaticamente. Caso contrário, instale uma solução do Centro de Segurança no espaço de trabalho:

        1. A partir do menu do Security Center, abra **as definições de preços &**.
        1. Selecione o espaço de trabalho ao qual estará a ligar os agentes.
        1. Selecione **Azure Defender on** ou **Azure Defender off**.

1. A partir da configuração de **eventos de segurança** do Windows, selecione a quantidade de dados brutos do evento para armazenar:
    - **Nenhum** – Desativar o armazenamento do evento de segurança. Esta é a predefinição.
    - **Mínimo** – Um pequeno conjunto de eventos para quando pretende minimizar o volume do evento.
    - **Comum** – Um conjunto de eventos que satisfaz a maioria dos clientes e fornece um rasto de auditoria completo.
    - **Todos os eventos** – Para clientes que queiram certificar-se de que todos os eventos estão armazenados.

    > [!TIP]
    > Para definir estas opções ao nível do espaço de trabalho, consulte [definir a opção de evento de segurança ao nível do espaço de trabalho.](#setting-the-security-event-option-at-the-workspace-level)
    > 
    > Para obter mais informações sobre estas opções, consulte as opções de [eventos de segurança do Windows para o agente Log Analytics](#data-collection-tier).

1. **Selecione Aplicar** no painel de configuração.

1. Para permitir o provisionamento automático de uma extensão diferente do agente Log Analytics: 

    1. Se estiver a permitir o provisionamento automático para o agente da Microsoft Dependency, certifique-se de que o agente Log Analytics está programado para ser implantado automaticamente.
    1. Alterne o estado para **On** para a extensão relevante.

        :::image type="content" source="./media/security-center-enable-data-collection/toggle-kubernetes-add-on.png" alt-text="Alternar para permitir o fornecimento automático para o addon de política K8s":::

    1. Selecione **Guardar**. A política Azure é atribuída e é criada uma tarefa de reparação.

        |Extensão  |Política  |
        |---------|---------|
        |Complemento de política para Kubernetes|[Implementar add-on de política Azure para clusters de serviço Azure Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fa8eff44f-8c92-45c3-a3fb-9880802d67a7)|
        |Agente de dependência da Microsoft (pré-visualização) (VMs do Windows)|[Implementar agente de dependência para máquinas virtuais do Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f1c210e94-a481-4beb-95fa-1571b434fb04)         |
        |Agente de dependência da Microsoft (pré-visualização) (Linux VMs)|[Implementar agente de dependência para máquinas virtuais Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da21710-ce6f-4e06-8cdb-5cc4c93ffbee)|
        |||

1. Selecione **Guardar**. Se um espaço de trabalho precisar de ser a provisionado, a instalação do agente pode demorar até 25 minutos.

1. Perguntar-lhe-á se pretende reconfigurar VMs monitorizados que estavam previamente ligados a um espaço de trabalho predefinido:

    :::image type="content" source="./media/security-center-enable-data-collection/reconfigure-monitored-vm.png" alt-text="Opções de revisão para reconfigurar VMs monitorizados":::

    - **Não** - as suas novas definições de espaço de trabalho só serão aplicadas a VMs recém-descobertos que não tenham o agente Log Analytics instalado.
    - **Sim** - as suas novas definições de espaço de trabalho aplicar-se-ão a todos os VMs e todos os VM atualmente ligados a um espaço de trabalho criado pelo Security Center serão reconectados ao novo espaço de trabalho alvo.

   > [!NOTE]
   > Se selecionar **Sim,** não elimine os espaços de trabalho criados pelo Security Center até que todos os VMs estejam novamente ligados ao novo espaço de trabalho alvo. Esta operação falha se um espaço de trabalho for apagado demasiado cedo.


## <a name="windows-security-event-options-for-the-log-analytics-agent"></a>Opções de evento de segurança do Windows para o agente Log Analytics <a name="data-collection-tier"></a> 

A seleção de um nível de recolha de dados no Azure Security Center apenas afeta o *armazenamento* de eventos de segurança no seu espaço de trabalho Log Analytics. O agente Log Analytics ainda irá recolher e analisar os eventos de segurança necessários para a proteção de ameaças do Security Center, independentemente do nível de eventos de segurança que escolha armazenar no seu espaço de trabalho. Optar por armazenar eventos de segurança permite a investigação, pesquisa e auditoria desses eventos no seu espaço de trabalho.

### <a name="requirements"></a>Requisitos 
O Azure Defender é necessário para armazenar dados de eventos de segurança do Windows. [Saiba mais sobre o Azure Defender](azure-defender.md).

Armazenar dados no Log Analytics pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

### <a name="information-for-azure-sentinel-users"></a>Informações para utilizadores do Azure Sentinel 
Utilizadores de Azure Sentinel: note que a recolha de eventos de segurança no contexto de um único espaço de trabalho pode ser configurada a partir do Azure Security Center ou Azure Sentinel, mas não ambos. Se está a planear adicionar o Azure Sentinel a um espaço de trabalho que já está a receber alertas do Azure Security Center, e está pronto para recolher Eventos de Segurança, tem duas opções:
- Deixe a coleção de Eventos de Segurança no Centro de Segurança Azure como está. Poderá consultar e analisar estes eventos em Azure Sentinel, bem como no Azure Defender. No entanto, não será capaz de monitorizar o estado de conectividade do conector ou alterar a sua configuração no Azure Sentinel. Se isto é importante para si, considere a segunda opção.
- Desativar a recolha de Eventos de Segurança no Azure Security Center (definindo **eventos** de segurança do Windows a **Nenhum** na configuração do seu agente Log Analytics). Em seguida, adicione o conector Eventos de Segurança em Azure Sentinel. Tal como na primeira opção, poderá consultar e analisar eventos tanto no Azure Sentinel como no Azure Defender/ASC, mas agora poderá monitorizar o estado de conectividade do conector ou alterar a sua configuração em - e apenas em - Azure Sentinel.

### <a name="what-event-types-are-stored-for-common-and-minimal"></a>Que tipos de eventos são armazenados para "Comum" e "Mínimo"?
Estes conjuntos foram concebidos para abordar cenários típicos. Certifique-se de avaliar quais as suas necessidades antes de implementá-la.

Para determinar os eventos para as opções **Comuns** e **Mínimas,** trabalhámos com clientes e padrões da indústria para aprender sobre a frequência não filtrada de cada evento e a sua utilização. Utilizamos as seguintes diretrizes neste processo:

- **Mínimo** - Certifique-se de que este conjunto cobre apenas eventos que possam indicar uma violação bem sucedida e eventos importantes que tenham um volume muito baixo. Por exemplo, este conjunto contém login bem sucedido e falhado do utilizador (IDs de evento 4624, 4625), mas não contém sinalização que é importante para a auditoria, mas não tem significado para deteção e tem um volume relativamente elevado. A maior parte do volume de dados deste conjunto é o evento de login e o evento de criação de processos (iD 4688 do evento).
- **Common** - Forneça um rasto completo de auditoria ao utilizador neste conjunto. Por exemplo, este conjunto contém logins de utilizador e saídas de registos do utilizador (ID 4634 do evento). Incluímos ações de auditoria como alterações de grupos de segurança, operações de controlador de domínio chave Kerberos, e outros eventos que são recomendados pelas organizações do setor.

Eventos com volume muito baixo foram incluídos no conjunto Comum como a principal motivação para escolhê-lo em todos os eventos é reduzir o volume e não filtrar eventos específicos.

Aqui está uma desagregação completa dos IDs de eventos de Segurança e App Locker para cada conjunto:

| Camada de dados | Indicadores de eventos recolhidos |
| --- | --- |
| Mínimo | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Common | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Se estiver a utilizar o Objeto de Política de Grupo (GPO), recomenda-se que ative as políticas de auditoria Process Creation Event 4688 e o campo *CommandLine* dentro do evento 4688. Para mais informações sobre o Evento de Criação de Processos 4688, consulte [as FAQ](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)do Security Center. Para obter mais informações sobre estas políticas de auditoria, consulte [as Recomendações de Política de Auditoria.](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)
> -  Para permitir a recolha de dados para [controlos de aplicações adaptativos,](security-center-adaptive-application.md)o Security Center configura uma política local do AppLocker em modo de auditoria para permitir todas as aplicações. Isto fará com que o AppLocker gere eventos que são depois recolhidos e alavancados pelo Security Center. É importante notar que esta política não será configurada em nenhuma máquina em que já exista uma política configurada do AppLocker. 
> - Para recolher o [ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)da plataforma de filtragem do Windows, é necessário ativar a [ligação da plataforma de filtragem de auditoria](/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol /set /subcategoria:"Ligação da plataforma de filtragem" /Sucesso:Ativar)
>

### <a name="setting-the-security-event-option-at-the-workspace-level"></a>Definir a opção de evento de segurança ao nível do espaço de trabalho

Pode definir o nível de dados do evento de segurança para armazenar ao nível do espaço de trabalho.

1. A partir do menu do Security Center no portal Azure, **selecione definições de preços &**.
1. Selecione o espaço de trabalho relevante. Os únicos eventos de recolha de dados para um espaço de trabalho são os eventos de segurança do Windows descritos nesta página.

    :::image type="content" source="media/security-center-enable-data-collection/event-collection-workspace.png" alt-text="Definir os dados do evento de segurança para armazenar num espaço de trabalho":::

1. Selecione a quantidade de dados brutos do evento para armazenar e **selecione Guardar**.

## <a name="manual-agent-provisioning"></a>Provisionamento de agentes manuais <a name="manual-agent"></a>
 
Para instalar manualmente o agente Log Analytics:

1. Desative o provisionamento automático.

1. Opcionalmente, criar um espaço de trabalho.

1. Ativar o Azure Defender no espaço de trabalho no qual está a instalar o agente Log Analytics:

    1. A partir do menu do Centro de Segurança, **selecione definições de preços &**.

    1. Ajuste o espaço de trabalho no qual está a instalar o agente. Certifique-se de que o espaço de trabalho está na mesma subscrição que utiliza no Centro de Segurança e que tem permissões de leitura/escrita para o espaço de trabalho.

    1. Selecione **Azure Defender em**, e **Guarde**.

       >[!NOTE]
       >Se o espaço de trabalho já tiver uma solução **SecurityCenterFree** ativada, o preço será definido automaticamente.  

1. Para implementar agentes em novos VMs utilizando um modelo de Gestor de Recursos, instale o agente Log Analytics:

   - [Instale o agente Log Analytics para windows](../virtual-machines/extensions/oms-windows.md)
   - [Instale o agente Log Analytics para Linux](../virtual-machines/extensions/oms-linux.md)

1. Para implantar agentes nos seus VMs existentes, siga as instruções na [Recolha de dados sobre máquinas virtuais Azure](../azure-monitor/vm/quick-collect-azurevm.md) (a secção **Recolher evento e dados de desempenho** é opcional).

1. Para utilizar o PowerShell para implantar os agentes, utilize as instruções da documentação das máquinas virtuais:

    - [Para máquinas Windows](../virtual-machines/extensions/oms-windows.md?toc=%2fazure%2fazure-monitor%2ftoc.json#powershell-deployment)
    - [Para máquinas Linux](../virtual-machines/extensions/oms-linux.md?toc=%2fazure%2fazure-monitor%2ftoc.json#azure-cli-deployment)

> [!TIP]
> Para obter instruções sobre como embarcar no Security Center utilizando o PowerShell, consulte [a automatização do Azure Security Center utilizando o PowerShell](security-center-powershell-onboarding.md).


## <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Provisionamento automático em caso de instalação de agente pré-existente <a name="preexisting"></a> 

Os seguintes casos de utilização especificam como funciona a provisão automática nos casos em que já existe um agente ou extensão instalado. 

- **O agente Log Analytics está instalado na máquina, mas não como uma extensão (Agente direto)** - Se o agente Log Analytics for instalado diretamente no VM (não como uma extensão Azure), o Security Center instalará a extensão do agente Log Analytics e poderá atualizar o agente Log Analytics para a versão mais recente.
O agente instalado continuará a reportar ao seu espaço de trabalho já configurado, e adicionalmente reportar-se-á ao espaço de trabalho configurado no Security Center (o multi-homing é suportado em máquinas Windows).
Se o espaço de trabalho configurado for um espaço de trabalho do utilizador (não o espaço de trabalho predefinido do Security Center), então terá de instalar a solução "Security" ou "SecurityCenterFree" no mesmo para que o Security Center comece a processar eventos de VMs e computadores que reportem para esse espaço de trabalho.

    No caso das máquinas Linux, o Agente multi-homing ainda não está suportado - portanto, se for detetada uma instalação de agente existente, não o provisionamento automático não ocorrerá e a configuração da máquina não será alterada.

    Para as máquinas existentes em subscrições a bordo do Security Center antes de 17 de março de 2019, quando um agente existente será detetado, a extensão do agente Log Analytics não será instalada e a máquina não será afetada. Para estas máquinas, consulte a recomendação "Resolver problemas de saúde do agente de monitorização nas suas máquinas" para resolver os problemas de instalação do agente nestas máquinas.
  
- **O agente do Gestor de Operações do System Center está instalado na máquina** - o centro de segurança instalará a extensão do agente Log Analytics lado a lado com o gestor de operações existente. O agente gestor de operações existente continuará a reportar-se normalmente ao servidor do Gestor de Operações. O agente do Gestor de Operações e o agente Do Log Analytics partilham bibliotecas comuns de tempo de execução, que serão atualizadas para a versão mais recente durante este processo. Se o agente do Gestor de Operações for instalado na versão 2012, **não** permita o provisionamento automático.

- **Está presente uma extensão VM pré-existente:**
    - Quando o Monitor é instalado como uma extensão, a configuração da extensão permite reportar apenas a um único espaço de trabalho. O Centro de Segurança não substitui as ligações existentes aos espaços de trabalho do utilizador. O Security Center irá armazenar dados de segurança a partir do VM no espaço de trabalho já ligado, desde que a solução "Security" ou "SecurityCenterFree" tenha sido instalada no mesmo. O Security Center pode atualizar a versão de extensão para a versão mais recente deste processo.
    - Para ver para que espaço de trabalho a extensão existente está a enviar dados, executar o teste para validar a [conectividade com o Azure Security Center](/archive/blogs/yuridiogenes/validating-connectivity-with-azure-security-center). Em alternativa, pode abrir espaços de trabalho do Log Analytics, selecionar um espaço de trabalho, selecionar o VM e ver a ligação do agente Log Analytics.
    - Se tiver um ambiente onde o agente Log Analytics está instalado em postos de trabalho dos clientes e reporte a um espaço de trabalho log analytics existente, reveja a lista de [sistemas operativos suportados pelo Azure Security Center](security-center-os-coverage.md) para se certificar de que o seu sistema operativo é suportado. Para obter mais informações, consulte [os clientes de analítica de registo existentes.](./faq-azure-monitor-logs.md)
 

## <a name="disable-auto-provisioning"></a>Desativar o provisionamento automático <a name="offprovisioning"></a>

Quando desativar o fornecimento automático, os agentes não serão ateados em novos VMs.

Para desligar o provisionamento automático de um agente:

1. A partir do menu do Security Center no portal, **selecione definições de preços &**.
1. Selecione a subscrição relevante.
1. Selecione **provisão automática**.
1. Alterne o estado para **Off** para o agente relevante.

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Alterna para desativar o provisionamento automático por tipo de agente":::

1. Selecione **Guardar**. Quando o provisionamento automático é desativado, a secção de configuração do espaço de trabalho predefinido não é apresentada:

    :::image type="content" source="./media/security-center-enable-data-collection/empty-configuration-column.png" alt-text="Quando o provisionamento automático é desativado, a célula de configuração está vazia":::


> [!NOTE]
>  A desativação do fornecimento automático não remove o agente Log Analytics da Azure VMs onde o agente foi a provisionado. Para obter informações sobre a remoção da extensão OMS, consulte [como remover as extensões OMS instaladas pelo Security Center](faq-data-collection-agents.md#remove-oms).
>


## <a name="troubleshooting"></a>Resolução de problemas

-   Para identificar problemas de instalação de provisão automática, consulte [problemas de saúde do agente de monitorização](security-center-troubleshooting-guide.md#mon-agent).
-  Para identificar os requisitos da rede de agentes de monitorização, consulte os [requisitos da rede de monitorização do agente de monitorização de resolução de problemas](security-center-troubleshooting-guide.md#mon-network-req).
-   Para identificar problemas manuais de embarque, consulte [como resolver problemas com a Suite de Gestão de Operações .](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)



## <a name="next-steps"></a>Passos seguintes
Esta página explicou como permitir o provisionamento automático para o agente Log Analytics e outras extensões do Centro de Segurança. Também descreveu como definir um espaço de trabalho Log Analytics no qual armazenar os dados recolhidos. Ambas as operações são necessárias para permitir a recolha de dados. Armazenar dados no Log Analytics, quer utilize um espaço de trabalho novo ou existente, pode incorrer em custos adicionais para armazenamento de dados. Para obter detalhes sobre preços na sua moeda de eleição e de acordo com a sua região, consulte [os preços do Security Center](https://azure.microsoft.com/pricing/details/security-center/).