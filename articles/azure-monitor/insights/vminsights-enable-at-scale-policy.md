---
title: Ativar o Monitor Azure para VMs utilizando a política do Azure
description: Este artigo descreve como permite o Azure Monitor para VMs para várias máquinas virtuais Azure ou conjuntos de balanças de máquinas virtuais utilizando a Política Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 7d3c4e0f4bd34f996bb39426af39a692a6f79c5c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85507182"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>Ativar o Monitor Azure para VMs utilizando a política do Azure

Este artigo explica como permitir o Azure Monitor para VMs para máquinas virtuais Azure, conjuntos de balanças de máquinas virtuais Azure e máquinas Azure Arc utilizando a Política Azure. No final deste processo, terá configurado com sucesso, permitindo aos agentes de Log Analytics e Dependency e máquinas virtuais identificadas que não estão em conformidade.

Para descobrir, gerir e ativar o Azure Monitor para VMs para todas as suas máquinas virtuais Azure ou conjuntos de balanças de máquinas virtuais, pode utilizar a Azure Policy ou a Azure PowerShell. A Política Azure é o método que recomendamos porque pode gerir definições políticas para governar eficazmente as suas subscrições para garantir o cumprimento consistente e a autorização automática de VMs recentemente a provisionados. Estas definições políticas:

* Implemente o agente Log Analytics e o agente De dependência.
* Relatório sobre os resultados da conformidade.
* Remediar para VMs não conformes.

Se estiver interessado em realizar estas tarefas com o Azure PowerShell ou um modelo de Gestor de Recursos Azure, consulte [Enable Azure Monitor para VMs utilizando modelos Azure PowerShell ou Azure Resource Manager](vminsights-enable-at-scale-powershell.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de utilizar a Política para embarcar nos seus VMs Azure e conjuntos de balanças de máquinas virtuais para Azure Monitoring para VMs, deve ativar a solução VMInsights no espaço de trabalho que utilizará para armazenar os seus dados de monitorização. Esta tarefa pode ser concluída a partir da página **Get Started** no Azure Monitor no **separador Outras opções de embarque.**  Selecione **Configurar um espaço de trabalho,** o que lhe levará a selecionar o espaço de trabalho a configurar.

![Configurar a área de trabalho](media/vminsights-enable-at-scale-policy/configure-workspace.png)

Também pode configurar o seu espaço de trabalho escolhendo **ativar a política** e, em seguida, selecione o botão **de ferramentas Configure workspace.**  Isto instalará a solução VMInsights no espaço de trabalho selecionado que permitirá ao espaço de trabalho armazenar os dados de monitorização enviados pelos VMs e conjuntos de escala de máquina virtual que permite utilizar a Política. 

![Ativar a política de utilização](media/vminsights-enable-at-scale-policy/enable-using-policy.png)

## <a name="manage-policy-coverage-feature-overview"></a>Gerir a visão geral do recurso de cobertura de política

O Azure Monitor for VMs Policy Coverage simplifica a descoberta, gestão e capacitação em escala da iniciativa **Enable Azure Monitor for VMs,** que inclui as definições de política mencionadas anteriormente. Para aceder a esta funcionalidade, selecione **Outras opções** de embarque a partir do **separador Iniciar em** Azure Monitor para VMs. **Selecione Gerir** a Cobertura de Política para abrir o Monitor Azure para a página de cobertura de política de **VMs.**

![Monitor Azure da VMs Get Started tab](./media/vminsights-enable-at-scale-policy/get-started-page.png)

A partir daqui, você pode verificar e gerir a cobertura para a iniciativa através dos seus grupos de gestão e subscrições. Você pode entender quantos VMs existem em cada um dos grupos de gestão e subscrições e seu estado de conformidade.

![Monitor Azure para página de Política de Gestão de VMs](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Estas informações são úteis para ajudá-lo a planear e executar o seu cenário de governação para O Azure Monitor para VMs a partir de uma localização central. Enquanto a Azure Policy fornece uma visão de conformidade quando uma política ou iniciativa é atribuída a um âmbito, com esta nova página você pode descobrir onde a política ou iniciativa não é atribuída e atribuí-la no lugar. Todas as ações como atribuir, ver e editar redirecionamento diretamente para a Política Azure. A página **Azure Monitor for VMs Policy Coverage** é uma experiência alargada e integrada apenas para a iniciativa **Enable Azure Monitor for VMs**.

A partir desta página, também pode configurar o seu espaço de trabalho Log Analytics para O Azure Monitor para VMs, que instala a solução *VMInsights.*

![Monitor Azure para VMs configura espaço de trabalho](media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Esta opção não está relacionada com quaisquer ações políticas. Está disponível para fornecer uma maneira fácil de satisfazer os [pré-requisitos necessários](vminsights-enable-overview.md) para permitir o Azure Monitor para VMs.  

### <a name="what-information-is-available-on-this-page"></a>Que informação está disponível nesta página?

A tabela seguinte fornece uma desagregação da informação que é apresentada na página de cobertura da apólice e como interpretá-la.

| Função | Descrição | 
|----------|-------------| 
| **Âmbito** | Grupo de gestão e subscrições a que tem ou herdou acesso com capacidade de perfurar através da hierarquia do grupo de gestão.|
| **Role** | O seu papel no âmbito, que pode ser leitor, proprietário ou contribuinte. Em alguns casos, pode parecer em branco indicar que pode ter acesso à subscrição, mas não ao grupo de gestão a que pertence. A informação noutras colunas varia consoante o seu papel. O papel é fundamental para determinar que dados pode ver e ações que pode realizar em termos de atribuição de políticas ou iniciativas (proprietário), edição ou visualização do cumprimento. |
| **Total VMs** | Número de VMs sob este âmbito. Para um grupo de gestão, é uma soma de VMs aninhados sob as assinaturas ou grupo de gestão de crianças. |
| **Cobertura de Atribuição** | Por cento dos VMs que são abrangidos pela política ou iniciativa. |
| **Estado da atribuição** | Informação sobre o estado da sua política ou atribuição de iniciativa. |
| **VMs conformes** | Número de VMs que estão em conformidade com a política ou iniciativa. Para a iniciativa **Enable Azure Monitor para VMs,** este é o número de VMs que têm tanto o agente Log Analytics como o agente De dependência. Em alguns casos, pode parecer em branco por nenhuma atribuição, sem VMs, ou com permissões insuficientes. A informação é fornecida ao abrigo **do Estado de Conformidade.** |
| **Conformidade** | O número global de conformidade é a soma de recursos distintos que são compatíveis divididos pela soma de todos os recursos distintos. |
| **Estado de Compatibilidade** | Informação sobre o estado de conformidade para a sua atribuição de política ou iniciativa.|

Quando atribuir a política ou iniciativa, o âmbito selecionado na atribuição pode ser o âmbito listado ou um subconjunto da sua. Por exemplo, pode ter criado uma atribuição para uma subscrição (âmbito de política) e não um grupo de gestão (âmbito de cobertura). Neste caso, o valor da Cobertura de **Atribuição** indica os VMs no âmbito de política ou iniciativa dividido pelos VMs no âmbito de cobertura. Noutro caso, pode ter excluído alguns VMs, grupos de recursos ou uma subscrição do âmbito de aplicação da política. Se o valor estiver em branco, indica que a apólice ou a iniciativa não existem ou não têm permissão. A informação é fornecida no âmbito **do Estado de Atribuição**.

## <a name="enable-by-using-azure-policy"></a>Ativar utilizando a Política Azure

Para ativar o Azure Monitor para VMs utilizando a Política Azure no seu inquilino:

- Atribuir a iniciativa a um âmbito: grupo de gestão, subscrição ou grupo de recursos.
- Reveja e remedia os resultados de conformidade.

Para obter mais informações sobre a atribuição da Política Azure, consulte [a visão geral da Política Azure](../../governance/policy/overview.md#assignments) e reveja a [visão geral dos grupos de gestão](../../governance/management-groups/overview.md) antes de continuar.

### <a name="policies-for-azure-vms"></a>Políticas para VMs Azure

As definições políticas para um Azure VM estão listadas no quadro seguinte.

|Name |Descrição |Tipo |
|-----|------------|-----|
|Ativar monitor Azure para VMs |Ativar o Azure Monitor para as máquinas virtuais no âmbito especificado (grupo de gestão, subscrição ou grupo de recursos). Toma o espaço de trabalho log Analytics como parâmetro. |Iniciativa |
|Implementação de agente de dependência de auditoria – imagem VM (OS) não cotada |Reporta vMs como incompatível se a imagem VM (OS) não estiver definida na lista e o agente não estiver instalado. |Política |
|Implementação de agente de Audit Log Analytics – imagem VM (OS) não cotada |Reporta vMs como incompatível se a imagem VM (OS) não estiver definida na lista e o agente não estiver instalado. |Política |
|Implementar agente de dependência para Os VMs Linux |Implementar agente de dependência para Os VMs Linux se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |
|Implementar agente de dependência para VMs do Windows |Implementar o agente de dependência para vMs do Windows se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |
|Implementar agente de Analítica de Registo para Os VMs linux |Implementar o agente Desafinado de Registos para VMs Linux se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |
|Implementar agente de Analítica de Registo para VMs do Windows |Implementar o agente Desajustado de Registo para VMs do Windows se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |


### <a name="policies-for-hybrid-azure-arc-machines"></a>Políticas para máquinas híbridas Azure Arc

As definições políticas para máquinas híbridas Azure Arc estão listadas na tabela seguinte.

|Name |Descrição |Tipo |
|-----|------------|-----|
| [Pré-visualização]: O agente Log Analytics deve ser instalado nas suas máquinas Linux Azure Arc |Relata as máquinas híbridas Azure Arc como incompatíveis para os VMs Linux se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |
| [Pré-visualização]: O agente Log Analytics deve ser instalado nas suas máquinas Windows Azure Arc |Relata as máquinas híbridas Azure Arc como incompatível com VMs do Windows se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |
| [Pré-visualização]: Implementar agente de dependência para máquinas híbridas Linux Azure Arc |Implementar agente de dependência para máquinas Azure Arc híbridas Linux se a imagem VM (OS) for definida na lista e o agente não estiver instalado. |Política |
| [Pré-visualização]: Implementar agente de dependência para máquinas híbridas do Windows Azure Arc |Implementar o agente de dependência das máquinas Azure Arc híbridas do Windows se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |
| [Pré-visualização]: Implementar o agente Desacutivamento de Registos para máquinas Linux Azure Arc |Implementar o agente Log Analytics para máquinas Azure Arc híbridas Linux se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |
| [Pré-visualização]: Implementar o agente Desacutivamento de Registos para máquinas Windows Azure Arc |Implementar o agente Log Analytics para máquinas Azure Arc híbridas do Windows se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |


### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Políticas para conjuntos de escala de máquina virtual Azure

As definições de política para um conjunto de escala de máquina virtual Azure estão listadas na tabela seguinte.

|Name |Descrição |Tipo |
|-----|------------|-----|
|Ativar o Azure Monitor para conjuntos de escala de máquinas virtuais |Ativar o Azure Monitor para os conjuntos de escala de máquina virtual no âmbito especificado (grupo de gestão, subscrição ou grupo de recursos). Toma o espaço de trabalho log Analytics como parâmetro. Nota: Se a sua política de atualização definida em escala estiver definida como Manual, aplique a extensão a todos os VMs do conjunto, chamando-os de atualização. No CLI, isto `az vmss update-instances` é. |Iniciativa |
|Implementação de agente de dependência de auditoria em conjuntos de escala de máquina virtual – imagem VM (OS) não cotada |Relata a escala de máquina virtual definida como incompatível se a imagem VM (OS) não estiver definida na lista e o agente não estiver instalado. |Política |
|Implementação de agente de Audit Log Analytics em conjuntos de escala de máquina virtual – imagem VM (OS) não cotada |Relata a escala de máquina virtual definida como incompatível se a imagem VM (OS) não estiver definida na lista e o agente não estiver instalado. |Política |
|Implementar agente de dependência para conjuntos de escala de máquina virtual Linux |Implementar o agente de dependência para conjuntos de escala de máquina virtual Linux se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |
|Implementar agente de dependência para conjuntos de escala de máquina virtual do Windows |Implementar o agente de dependência para conjuntos de escala de máquina virtual do Windows se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |
|Implementar o agente Desagregalhador para conjuntos de escala de máquina virtual Linux |Implementar o agente Log Analytics para conjuntos de escala de máquina virtual Linux se a Imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |
|Implementar o agente Desagregalhador para conjuntos de escala de máquina virtual do Windows |Implementar o agente Desajustado de Analítico para conjuntos de escala de máquina virtual do Windows se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |

A política autónoma (não incluída na iniciativa) é descrita aqui:

|Name |Descrição |Tipo |
|-----|------------|-----|
|Audit Log Analytics espaço de trabalho para VM – Relatório incompatibilidade |Reporte os VMs como incompatíveis se não estiverem a registar-se no espaço de trabalho do Log Analytics especificado na atribuição de políticas ou iniciativas. |Política |

### <a name="assign-the-azure-monitor-initiative"></a>Atribuir a iniciativa Azure Monitor

Para criar a atribuição de políticas a partir da página **Azure Monitor para cobertura de política de VMs,** siga estes passos. Para entender como completar estes passos, consulte [Criar uma atribuição de política a partir do portal Azure.](../../governance/policy/assign-policy-portal.md)

Quando atribuir a política ou iniciativa, o âmbito selecionado na atribuição pode ser o âmbito listado aqui ou um subconjunto da sua. Por exemplo, pode ter criado uma atribuição para a subscrição (âmbito de política) e não para o grupo de gestão (âmbito de cobertura). Neste caso, a percentagem de cobertura indicaria os VM no âmbito de política ou iniciativa dividido pelos VM no âmbito de cobertura. Noutro caso, pode ter excluído alguns VMs, ou grupos de recursos, ou uma subscrição do âmbito da política. Se estiver em branco, indica que a apólice ou a iniciativa não existem ou não têm permissões. A informação é fornecida no âmbito **do Estado de Atribuição**.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No portal Azure, selecione **Monitor**. 

3. Escolha **máquinas virtuais** na secção **Insights.**
 
4. Selecione o **separador Iniciar.** Na página, **selecione Manage Policy Coverage**.

5. Selecione um grupo de gestão ou uma subscrição da tabela. Selecione **Scope** selecionando a elipse (...). No exemplo, um âmbito limita a atribuição de políticas a um agrupamento de máquinas virtuais para aplicação da lei.

6. Na página de atribuição da **Política Azure,** é pré-povoada com a iniciativa **Enable Azure Monitor for VMs**. 
    A **caixa de nomes de atribuição** é automaticamente preenchida com o nome da iniciativa, mas pode alterá-la. Também pode adicionar uma descrição opcional. O **Atribuído por** caixa é automaticamente povoado com base em quem está registado. Este valor é opcional.

7. (Opcional) Para remover um ou mais recursos do âmbito, selecione **Exclusions**.

8. Na lista de drop-down **do log Analytics** para a região suportada, selecione um espaço de trabalho.

   > [!NOTE]
   > Se o espaço de trabalho estiver fora do âmbito da atribuição, conceda permissões *ao Log Analytics Contributor* para o ID principal da atribuição de políticas. Se não o fizer, poderá ver uma falha de implantação como `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` conceder acesso, rever [como configurar manualmente a identidade gerida](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  A caixa de verificação **de identidade gerida** é selecionada porque a iniciativa que está a ser atribuída inclui uma política com o efeito *deployIfNotExists.*
    
9. Na lista de **localização de identidade gerencial,** selecione a região apropriada.

10. Selecione **Atribuir**.

Depois de criar a atribuição, o Monitor Azure para a página **de cobertura de política de VMs** atualiza a cobertura de **atribuição,** **estado de atribuição,** **VMs compatível**e **Estado de Conformidade** para refletir as alterações. 

A matriz a seguir mapeia cada possível estado de conformidade para a iniciativa.  

| Estado de conformidade | Descrição | 
|------------------|-------------|
| **Compatível** | Todos os VMs no âmbito têm os agentes de Log Analytics e Dependência destacados para eles.|
| **Não conforme** | Nem todos os VMs no âmbito têm os agentes de Log Analytics e Dependency destacados para eles e podem exigir reparação.|
| **Não começou** | Uma nova missão foi adicionada. |
| **Bloquear** | Não tens privilégios suficientes para o grupo de gestão. <sup>1</sup> | 
| **Em branco** | Nenhuma apólice é atribuída. | 

<sup>1</sup> Se não tiver acesso ao grupo de gestão, peça ao proprietário para fornecer acesso. Ou, ver conformidade e gerir atribuições através dos grupos de gestão de crianças ou subscrições. 

A tabela seguinte mapeia cada possível estatuto de atribuição para a iniciativa.

| Estado de atribuição | Descrição | 
|------------------|-------------|
| **Sucesso** | Todos os VMs no âmbito têm os agentes de Log Analytics e Dependência destacados para eles.|
| **Aviso** | A assinatura não está sob um grupo de gestão.|
| **Não começou** | Uma nova missão foi adicionada. |
| **Bloquear** | Não tens privilégios suficientes para o grupo de gestão. <sup>1</sup> | 
| **Em branco** | Não existem VMs ou uma apólice não é atribuída. | 
| **Ação** | Atribua uma apólice ou edite uma atribuição. | 

<sup>1</sup> Se não tiver acesso ao grupo de gestão, peça ao proprietário para fornecer acesso. Ou, ver conformidade e gerir atribuições através dos grupos de gestão de crianças ou subscrições.

## <a name="review-and-remediate-the-compliance-results"></a>Rever e remediar os resultados da conformidade

O exemplo a seguir é para um Azure VM, mas também se aplica a conjuntos de escala de máquina virtual. Para aprender a rever os resultados da conformidade, consulte [identificar os resultados do incumprimento.](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources) Na página **Azure Monitor for VMs Policy Coverage,** selecione um grupo de gestão ou uma subscrição da tabela. Selecione **Ver Conformidade** selecionando a elipse (...).   

![Conformidade da política para os VMs Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

Com base nos resultados das políticas incluídas na iniciativa, os VM são reportados como incompatíveis nos seguintes cenários:

* O agente de log Analytics ou agente de dependência não está implantado.  
    Este cenário é típico de um âmbito com VMs existentes. Para atenuá-lo, implemente os agentes [necessários criando tarefas de reparação](../../governance/policy/how-to/remediate-resources.md) numa política não conforme.  
    - Implementar agente de dependência para Os VMs Linux
    - Implementar agente de dependência para VMs do Windows
    - Implementar agente de Analítica de Registo para Os VMs linux
    - Implementar agente de Analítica de Registo para VMs do Windows

* A imagem VM (OS) não está identificada na definição de política.  
    Os critérios da política de implantação incluem apenas VMs que são implantados a partir de imagens VM bem conhecidas do Azure. Verifique a documentação para ver se o VM OS está suportado. Se não for suportada, duplique a política de implementação e atualize-a ou modifique-a para tornar a imagem conforme.  
    - Implementação de agente de dependência de auditoria – imagem VM (OS) não cotada
    - Implementação de agente de Audit Log Analytics – imagem VM (OS) não cotada

* Os VM não estão a iniciar sessão no espaço de trabalho especificado do Log Analytics.  
    É possível que alguns VMs no âmbito da iniciativa estejam a iniciar sessão num espaço de trabalho do Log Analytics que não seja o especificado na atribuição de políticas. Esta política é uma ferramenta para identificar quais os VMs que estão reportando a um espaço de trabalho não conforme.  
    - Audit Log Analytics espaço de trabalho para VM – Relatório incompatibilidade

## <a name="edit-an-initiative-assignment"></a>Editar uma atribuição de iniciativa

Em qualquer momento após a atribuição de uma iniciativa a um grupo de gestão ou subscrição, pode editá-la para modificar as seguintes propriedades:

- Nome da atribuição
- Descrição
- Atribuído por
- Área de trabalho do Log Analytics
- Exceções

## <a name="next-steps"></a>Próximos passos

Agora que a monitorização está ativada para as suas máquinas virtuais, esta informação está disponível para análise com o Azure Monitor para VMs. 

- Para ver as dependências de aplicações descobertas, consulte [o Monitor do Azure para o Mapa de VMs](vminsights-maps.md). 

- Para identificar estrangulamentos e utilização global com o desempenho do seu VM, consulte o desempenho do [See Azure VM](vminsights-performance.md). 
