---
title: Recolha de Dados no Centro de Segurança Azure Microsoft Docs
description: Este artigo descreve como instalar um agente Do Log Analytics e definir um espaço de trabalho Log Analytics no qual armazenar os dados recolhidos.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 10/08/2020
ms.author: memildin
ms.openlocfilehash: e5c9540bed34de3cad5c74c7041c8d7e06aef9ca
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946064"
---
# <a name="data-collection-in-azure-security-center"></a>Data collection in Azure Security Center (Recolha de dados no Centro de Segurança do Azure)
O Security Center recolhe dados das suas máquinas virtuais Azure (VMs), conjuntos de escala de máquinas virtuais, contentores IaaS e computadores não-Azure (incluindo no local) para monitorizar vulnerabilidades e ameaças de segurança. Os dados são recolhidos utilizando o agente Log Analytics, que lê várias configurações relacionadas com a segurança e registos de eventos da máquina e copia os dados para o seu espaço de trabalho para análise. Exemplos desses dados são: tipo e versão do sistema operativo, registos do sistema operativo (registos de eventos windows), processos de execução, nome da máquina, endereços IP e registados no utilizador.

A recolha de dados é necessária para fornecer visibilidade em atualizações em falta, definições de segurança de SO mal configuradas, estado de proteção de ponto final e proteção para a saúde e ameaças. A recolha de dados só é necessária para recursos de computação (VMs, conjuntos de escala de máquinas virtuais, recipientes IaaS e computadores não-Azure). Pode beneficiar do Azure Security Center mesmo que não provisa agentes; no entanto, terá uma segurança limitada e as capacidades acima enumeradas não são suportadas.  

Este artigo descreve como instalar um agente Do Log Analytics e definir um espaço de trabalho Log Analytics no qual armazenar os dados recolhidos. Ambas as operações são necessárias para permitir a recolha de dados. Armazenar dados no Log Analytics, quer utilize um espaço de trabalho novo ou existente, pode incorrer em custos adicionais para armazenamento de dados. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

> [!TIP]
> Para a lista de plataformas apoiadas, consulte [plataformas apoiadas no Centro de Segurança Azure.](security-center-os-coverage.md)

## <a name="enable-automatic-provisioning-of-the-log-analytics-agent"></a>Permitir o provisionamento automático do agente Log Analytics <a name="auto-provision-mma"></a>

> [!NOTE]
> Utilizadores de Azure Sentinel: note que a recolha de eventos de segurança no contexto de um único espaço de trabalho pode ser configurada a partir do Azure Security Center ou Azure Sentinel, mas não ambos. Se está a planear adicionar o Azure Sentinel a um espaço de trabalho que já está a receber alertas do Azure Defender do Azure Security Center, e está preparado para recolher Eventos de Segurança, tem duas opções:
> - Deixe a coleção de Eventos de Segurança no Centro de Segurança Azure como está. Poderá consultar e analisar estes eventos em Azure Sentinel, bem como no Azure Defender. No entanto, não será capaz de monitorizar o estado de conectividade do conector ou alterar a sua configuração no Azure Sentinel. Se isto é importante para si, considere a segunda opção.
>
> - [Desativar a coleção de Eventos](#data-collection-tier) de Segurança no Azure Security Center e só depois adicionar o conector eventos de segurança em Azure Sentinel. Tal como na primeira opção, poderá consultar e analisar eventos tanto no Azure Sentinel como no Azure Defender/ASC, mas agora poderá monitorizar o estado de conectividade do conector ou alterar a sua configuração em - e apenas em - Azure Sentinel.


Para recolher os dados das máquinas, deverá ter o agente Log Analytics instalado. A instalação do agente pode ser feita automaticamente (recomendada) ou pode instalar o agente manualmente. Por predefinição, o provisionamento automático está desligado.

Quando o provisionamento automático está ligado, o Security Center implementa o agente Log Analytics em todos os VMs Azure suportados e quaisquer novos que sejam criados. Recomenda-se o provisionamento automático, mas pode instalar o agente manualmente se necessário (ver [instalação manual do agente Log Analytics).](#manual-agent)

Com o agente implantado nas suas máquinas, o Security Center pode fornecer recomendações adicionais relacionadas com o estado de atualização do sistema, configurações de segurança do SISTEMA, proteção de ponto final, bem como gerar alertas de segurança adicionais.

Para permitir o provisionamento automático do agente Log Analytics:

1. A partir do menu do Centro de Segurança, **selecione definições de preços &**.
1. Selecione a subscrição relevante.
1. Na página de **recolha de dados,** desa **essa adir** automático ao **On**.
1. Selecione **Guardar**.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Habilitação automática do agente Log Analytics":::

    >[!TIP]
    > Se um espaço de trabalho precisar de ser a provisionado, a instalação do agente pode demorar até 25 minutos.


## <a name="workspace-configuration"></a>Configuração do espaço de trabalho
Os dados recolhidos pelo Centro de Segurança são armazenados nas áreas de trabalho do Log Analytics. Os seus dados podem ser recolhidos a partir de VMs Azure armazenados em espaços de trabalho criados pelo Security Center ou num espaço de trabalho existente que criou. 

A configuração do espaço de trabalho é definida por subscrição, e muitas subscrições podem usar o mesmo espaço de trabalho.

### <a name="using-a-workspace-created-by-security-center"></a>Usando um espaço de trabalho criado pelo Security Center

O centro de segurança pode automaticamente criar um espaço de trabalho predefinido para armazenar os dados. 

Para selecionar um espaço de trabalho criado pelo Security Center:

1. Na **configuração do espaço de trabalho predefinido,** selecione Use workspace(s) criado pelo Centro de Segurança.
    :::image type="content" source="./media/security-center-enable-data-collection/workspace-selection.png" alt-text="Habilitação automática do agente Log Analytics"::: 

1. Clique em **Guardar**.<br>
    O Security Center cria um novo grupo de recursos e espaço de trabalho predefinido nessa geolocalização, e liga o agente a esse espaço de trabalho. A convenção de nomeação para o espaço de trabalho e grupo de recursos é:<br>
   **Espaço de trabalho: DefaultWorkspace-[subscrição-ID]-[geo] <br> Grupo de Recursos: DefaultResourceGroup-[geo]**

   Se uma subscrição contiver VMs de múltiplas geolocalações, então o Centro de Segurança cria vários espaços de trabalho. Vários espaços de trabalho são criados para manter as regras de privacidade de dados.
1. O Security Center permitirá automaticamente uma solução do Centro de Segurança no espaço de trabalho por nível de preços definido para a subscrição. 

> [!NOTE]
> O nível de preços do Log Analytics dos espaços de trabalho criados pelo Security Center não afeta a faturação do Security Center. A faturação do Centro de Segurança baseia-se sempre na sua política de segurança do Centro de Segurança e nas soluções instaladas numa área de trabalho. Para subscrições sem Azure Defender, o Security Center permite a solução *SecurityCenterFree* no espaço de trabalho predefinido. Para subscrições com o Azure Defender, o Security Center permite a solução *de Segurança* no espaço de trabalho predefinido.
> Armazenar dados no Log Analytics pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Para obter mais informações sobre as contas de análise de registo existentes, consulte [os clientes de análise de registo existentes](./faq-azure-monitor-logs.md).

### <a name="using-an-existing-workspace"></a>Usando um espaço de trabalho existente

Se já tem um espaço de trabalho log analytics existente, talvez queira utilizar o mesmo espaço de trabalho.

Para utilizar o seu espaço de trabalho log analytics existente, deve ter lido e escrito permissões no espaço de trabalho.

> [!NOTE]
> As soluções ativadas no espaço de trabalho existente serão aplicadas aos VMs Azure que estão ligados ao mesmo. Para soluções pagas, isto poderia resultar em encargos adicionais. Para questões de privacidade de dados, certifique-se de que o seu espaço de trabalho selecionado está na região geográfica certa.
> Armazenar dados na análise de registos pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Para selecionar um espaço de trabalho existente do Log Analytics:

1. Na **configuração do espaço de trabalho predefinido**, selecione **Utilize outro espaço de trabalho**.
    :::image type="content" source="./media/security-center-enable-data-collection/use-another-workspace.png" alt-text="Habilitação automática do agente Log Analytics"::: 

2. A partir do menu pull-down, selecione um espaço de trabalho para armazenar dados recolhidos.

   > [!NOTE]
   > No menu pull down, todos os espaços de trabalho em todas as suas subscrições estão disponíveis. Consulte [a seleção do espaço de trabalho de subscrição cruzada](security-center-enable-data-collection.md#cross-subscription-workspace-selection) para obter mais informações. Deve ter permissão para aceder ao espaço de trabalho.
   >
   >

3. Selecione **Guardar**.
4. Depois de selecionar **Save,** perguntar-lhe-á se pretende reconfigurar VMs monitorizados que estavam previamente ligados a um espaço de trabalho predefinido.

   - Selecione **Não** se quiser que as novas definições do espaço de trabalho se apliquem apenas em novos VMs. As novas definições de espaço de trabalho aplicam-se apenas às novas instalações de agentes; VMs recém-descobertos que não têm o agente Log Analytics instalado.
   - Selecione **Sim** se quiser que as novas definições do espaço de trabalho se apliquem em todos os VMs. Além disso, todos os VM ligados a um espaço de trabalho criado pelo Security Center estão reconectados com o novo espaço de trabalho alvo.

   > [!NOTE]
   > Se selecionar Sim, não deve eliminar os espaços de trabalho criados pelo Security Center até que todos os VMs tenham sido reconectados ao novo espaço de trabalho alvo. Esta operação falha se um espaço de trabalho for apagado demasiado cedo.
   >
   >

   - Para cancelar a operação, **selecione Cancelar**.

     ![Opções de revisão para reconfigurar VMs monitorizados][3]

5. Selecione se o espaço de trabalho terá ou não O Azure Defender ativado.

    Para utilizar um espaço de trabalho existente, desa um nível de preços para o espaço de trabalho. Isto instalará uma solução de Centro de Segurança no espaço de trabalho se ainda não estiver presente.

    1. No menu principal do Centro de Segurança, selecione **definições de preços &**.
     
    1. Selecione o espaço de trabalho ao qual estará a ligar o agente.

    1. Selecione **Azure Defender on** ou **Azure Defender off**.

   
   >[!NOTE]
   >Se o espaço de trabalho já tiver uma solução **SecurityCenterFree** ativada, o preço será definido automaticamente. **Security** 


## <a name="cross-subscription-workspace-selection"></a>Seleção de espaço de trabalho de subscrição cruzada
Quando seleciona um espaço de trabalho para armazenar os seus dados, todos os espaços de trabalho em todas as suas subscrições estão disponíveis. A seleção da área de trabalho de subscrições cruzadas permite-lhe recolher dados de máquinas virtuais em execução em diferentes subscrições e armazenar os mesmos na área de trabalho da sua preferência. Esta seleção será útil se estiver a utilizar uma área de trabalho centralizada na sua organização e pretender utilizá-la para a recolha de dados de segurança. Para obter mais informações sobre como gerir espaços de trabalho, consulte [Gerir o acesso ao espaço de trabalho.](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access)



## <a name="data-collection-tier"></a>Nível de recolha de dados
A seleção de um escalão de recolha de dados no Centro de Segurança do Azure só vai afetar o armazenamento dos eventos de segurança na área de trabalho do Log Analytics. O agente Log Analytics ainda recolherá e analisará os eventos de segurança necessários para a proteção de ameaças do Azure Security Center, independentemente do nível de eventos de segurança que escolher armazenar no seu espaço de trabalho Log Analytics (se houver). Optar por armazenar os eventos de segurança na sua área de trabalho permitirá a investigação, a pesquisa e a auditoria desses eventos na área de trabalho. 
> [!NOTE]
> Armazenar dados na análise de registos pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Pode escolher a política de filtragem certa para as suas subscrições e espaços de trabalho a partir de quatro conjuntos de eventos a serem armazenados no seu espaço de trabalho: 
- **Nenhum** – Desativar o armazenamento do evento de segurança. Esta é a predefinição.
- **Mínimo** – Um conjunto menor de eventos para clientes que querem minimizar o volume do evento.
- **Common** – Este é um conjunto de eventos que satisfaz a maioria dos clientes e lhes permite um rasto completo de auditoria.
- **Todos os eventos** – Para clientes que queiram certificar-se de que todos os eventos estão armazenados.

Estes conjuntos de eventos de segurança estão disponíveis apenas com o Azure Defender. Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança.

Estes conjuntos foram concebidos para abordar cenários típicos. Certifique-se de avaliar quais as suas necessidades antes de implementá-la.

Para determinar os eventos que pertencerão aos conjuntos de eventos **Comuns** e **Minimal,** trabalhámos com clientes e padrões da indústria para aprender sobre a frequência não filtrada de cada evento e a sua utilização. Utilizamos as seguintes diretrizes neste processo:

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
> - Se estiver a utilizar o Objeto de Política de Grupo (GPO), recomenda-se que ative as políticas de auditoria Process Creation Event 4688 e o campo *CommandLine* dentro do evento 4688. Para mais informações sobre o Evento de Criação de Processos 4688, consulte [as FAQ](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)do Security Center. Para obter mais informações sobre estas políticas de auditoria, consulte [as Recomendações de Política de Auditoria.](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)
> -  Para permitir a recolha de dados para [controlos de aplicações adaptativos,](security-center-adaptive-application.md)o Security Center configura uma política local do AppLocker em modo de auditoria para permitir todas as aplicações. Isto fará com que o AppLocker gere eventos que são depois recolhidos e alavancados pelo Security Center. É importante notar que esta política não será configurada em nenhuma máquina em que já exista uma política configurada do AppLocker. 
> - Para recolher o [ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)da plataforma de filtragem do Windows, é necessário ativar a [ligação da plataforma de filtragem de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol /set /subcategoria:"Ligação da plataforma de filtragem" /Sucesso:Ativar)
>

Para escolher a sua política de filtragem:
1. Na página **de Recolha de Dados,** selecione a sua política de filtragem sob **a Loja dados brutos adicionais - eventos de segurança do Windows**.
 
1. Selecione **Guardar**.
    :::image type="content" source="./media/security-center-enable-data-collection/data-collection-tiers.png" alt-text="Habilitação automática do agente Log Analytics":::

### <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Provisionamento automático em caso de instalação de agente pré-existente <a name="preexisting"></a> 

Os seguintes casos de utilização especificam como funciona a provisão automática nos casos em que já existe um agente ou extensão instalado. 

- O agente Log Analytics está instalado na máquina, mas não como uma extensão (Agente direto)<br>
Se o agente Log Analytics for instalado diretamente no VM (não como uma extensão Azure), o Security Center instalará a extensão do agente Log Analytics e poderá atualizar o agente Log Analytics para a versão mais recente.
O agente instalado continuará a reportar ao seu espaço de trabalho já configurado, e adicionalmente reportar-se-á ao espaço de trabalho configurado no Security Center (o multi-homing é suportado em máquinas Windows).
Se o espaço de trabalho configurado for um espaço de trabalho do utilizador (não o espaço de trabalho predefinido do Security Center), então terá de instalar a solução "security/securityFree" nele para o Security Center começar a processar eventos de VMs e computadores reportando para esse espaço de trabalho.<br>
<br>
No caso das máquinas Linux, o Agente multi-homing ainda não está suportado - portanto, se for detetada uma instalação de agente existente, não o provisionamento automático não ocorrerá e a configuração da máquina não será alterada.
<br>
Para as máquinas existentes em subscrições a bordo do Security Center antes de 17 de março de 2019, quando um agente existente será detetado, a extensão do agente Log Analytics não será instalada e a máquina não será afetada. Para estas máquinas, consulte a recomendação "Resolver problemas de saúde do agente de monitorização nas suas máquinas" para resolver os problemas de instalação do agente nestas máquinas.

  
- O agente gestor de operações do System Center está instalado na máquina<br>
O centro de segurança instalará a extensão do agente Log Analytics lado a lado com o gestor de operações existente. O agente gestor de operações existente continuará a reportar-se normalmente ao servidor do Gestor de Operações. O agente do Gestor de Operações e o agente Do Log Analytics partilham bibliotecas comuns de tempo de execução, que serão atualizadas para a versão mais recente durante este processo. Se o agente do Gestor de Operações for instalado na versão 2012, **não** permita o provisionamento automático.<br>

- Uma extensão VM pré-existente está presente<br>
    - Quando o Monitor é instalado como uma extensão, a configuração da extensão permite reportar apenas a um único espaço de trabalho. O Centro de Segurança não substitui as ligações existentes aos espaços de trabalho do utilizador. O Security Center irá armazenar dados de segurança a partir do VM no espaço de trabalho já ligado, desde que a solução "security" ou "securityFree" tenha sido instalada no mesmo. O Security Center pode atualizar a versão de extensão para a versão mais recente deste processo.  
    - Para ver para que espaço de trabalho a extensão existente está a enviar dados, executar o teste para validar a [conectividade com o Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Em alternativa, pode abrir espaços de trabalho do Log Analytics, selecionar um espaço de trabalho, selecionar o VM e ver a ligação do agente Log Analytics. 
    - Se tiver um ambiente onde o agente Log Analytics está instalado em postos de trabalho dos clientes e reporte a um espaço de trabalho log analytics existente, reveja a lista de [sistemas operativos suportados pelo Azure Security Center](security-center-os-coverage.md) para se certificar de que o seu sistema operativo é suportado. Para obter mais informações, consulte [os clientes de analítica de registo existentes.](./faq-azure-monitor-logs.md)
 
### <a name="turn-off-automatic-provisioning"></a>Desligue o provisionamento automático <a name="offprovisioning"></a>
Para desligar o provisionamento automático do agente Log Analytics:

1. A partir do menu do Security Center no portal, **selecione definições de preços &**.
2. Selecione a subscrição relevante.

    :::image type="content" source="./media/security-center-enable-data-collection/select-subscription.png" alt-text="Habilitação automática do agente Log Analytics":::

3. Selecione **a recolha de dados.**
4. Em **Previsão automática**, selecione **Off** para desativar o provisionamento automático.
5. Selecione **Guardar**. 


Quando o provisionamento automático é desativado (desligado), a secção de configuração do espaço de trabalho predefinido não é apresentada.

Se desligar a provisão automática depois de ter sido anteriormente ligada, os agentes não serão a provisionados em novos VMs.

 
> [!NOTE]
>  A desativação do fornecimento automático não remove o agente Log Analytics da Azure VMs onde o agente foi a provisionado. Para obter informações sobre a remoção da extensão OMS, consulte [como remover as extensões OMS instaladas pelo Security Center](faq-data-collection-agents.md#remove-oms).
>
    
## <a name="manual-agent-provisioning"></a>Provisionamento de agentes manuais <a name="manual-agent"></a>
 
Existem várias formas de instalar manualmente o agente Log Analytics. Ao instalar manualmente, certifique-se de que desativa o provisionamento automático.

### <a name="operations-management-suite-vm-extension-deployment"></a>Implementação de extensão do Suite VM de Gestão de Operações 

Pode instalar manualmente o agente Log Analytics, para que o Security Center possa recolher dados de segurança dos seus VMs e fornecer recomendações e alertas.

1. Desative o provisionamento automático.

1. Opcionalmente, criar um espaço de trabalho.

1. Ativar o Azure Defender no espaço de trabalho no qual está a instalar o agente Log Analytics:

    1. A partir do menu do Centro de Segurança, **selecione definições de preços &**.

    1. Ajuste o espaço de trabalho no qual está a instalar o agente. Certifique-se de que o espaço de trabalho está na mesma subscrição que utiliza no Centro de Segurança e que tem permissões de leitura/escrita no espaço de trabalho.

    1. Coloque o Azure Defender ligado e selecione **Save**.

       >[!NOTE]
       >Se o espaço de trabalho já tiver uma solução **SecurityCenterFree** ativada, o preço será definido automaticamente. **Security** 

1. Se pretender implantar os agentes em novos VMs utilizando um modelo de Gestor de Recursos, instale o agente Log Analytics:

   - [Instale o agente Log Analytics para windows](../virtual-machines/extensions/oms-windows.md)
   - [Instale o agente Log Analytics para Linux](../virtual-machines/extensions/oms-linux.md)

1. Para implementar as extensões em VMs existentes, siga as instruções na [Recolha de dados sobre máquinas virtuais Azure](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > A secção **Recolher dados de eventos e desempenho** é opcional.
   >

1. Para utilizar o PowerShell para implantar a extensão, utilize as instruções da documentação das máquinas virtuais:

    - [Para máquinas Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#powershell-deployment)
    - [Para máquinas Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-linux?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#azure-cli-deployment)



> [!NOTE]
> Para obter instruções sobre como embarcar no Security Center utilizando o PowerShell, consulte [a automatização do Azure Security Center utilizando o PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Resolução de problemas

-   Para identificar problemas automáticos de instalação de fornecimento, consulte [problemas de saúde do agente de monitorização](security-center-troubleshooting-guide.md#mon-agent).

-  Para identificar os requisitos da rede de agentes de monitorização, consulte os [requisitos da rede de monitorização do agente de monitorização de resolução de problemas](security-center-troubleshooting-guide.md#mon-network-req).
-   Para identificar problemas manuais de embarque, consulte [como resolver problemas com a Suite de Gestão de Operações .](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)

- Para identificar vms não monitorizados e problemas de computadores:

    Um VM ou computador não é monitorizado pelo Security Center se a máquina não estiver a executar a extensão do agente Log Analytics. Uma máquina pode ter um agente local já instalado, por exemplo, o agente direto OMS ou o agente Gestor de Operações do System Center. As máquinas com estes agentes são identificadas como não monitorizadas porque estes agentes não são totalmente apoiados no Centro de Segurança. Para beneficiar plenamente de todas as capacidades do Security Center, é necessária a extensão do agente Log Analytics.

    Para obter mais informações sobre as razões pelas quais o Security Center não consegue monitorizar com sucesso VMs e computadores inicializados para provisão automático, consulte [problemas de saúde do agente de monitorização](security-center-troubleshooting-guide.md#mon-agent).


## <a name="next-steps"></a>Próximos passos
Este artigo mostrou-lhe como funciona a recolha de dados e o provisionamento automático no Centro de Segurança. Para saber mais sobre o Centro de Segurança, consulte as seguintes páginas:

- [FAQ do Centro de Segurança do Azure](faq-general.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
- [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.



<!--Image references-->
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
