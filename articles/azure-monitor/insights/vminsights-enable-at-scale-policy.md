---
title: Ativar o Monitor Azure para VMs utilizando a Política Azure
description: Este artigo descreve como permite o Monitor Azure para VMs para várias máquinas virtuais Azure ou conjuntos de escala de máquinas virtuais utilizando a Política Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 7069f2cc96b8876f5514acfa4ba49274b61be46f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282941"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>Ativar o Monitor Azure para VMs utilizando a Política Azure

Este artigo explica como ativar o Monitor Azure para VMs para máquinas virtuais Azure ou conjuntos de escala de máquinas virtuais utilizando a Política Azure. No final deste processo, terá configurado com sucesso os agentes de Log Analytics e Dependency e identificado máquinas virtuais que não estão em conformidade.

Para descobrir, gerir e ativar o Monitor Azure para VMs para todas as suas máquinas virtuais Azure ou conjuntos de escala de máquinas virtuais, pode utilizar a Política Azure ou a Azure PowerShell. A Política Azure é o método que recomendamos porque pode gerir definições de políticas para governar eficazmente as suas subscrições para garantir uma conformidade consistente e uma habilitação automática de VMs recém-provisionados. Estas definições políticas:

* Implante o agente Log Analytics e o agente dependency.
* Relatório sobre os resultados da conformidade.
* Correctificação para VMs não conformes.

Se estiver interessado em realizar estas tarefas com o Azure PowerShell ou um modelo de Gestor de Recursos Azure, consulte [o Enable Azure Monitor para VMs utilizando modelos Azure PowerShell ou Azure Resource Manager](vminsights-enable-at-scale-powershell.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de utilizar a Política para embarcar nos seus VMs Azure e conjuntos de escala de máquinas virtuais para a Monitorização Azure para VMs, deve ativar a solução VMInsights no espaço de trabalho que utilizará para armazenar os seus dados de monitorização. Esta tarefa pode ser completada a partir da página **Get Started** no Monitor Azure no separador **outras opções** de embarque.  **Selecione Configurar um espaço**de trabalho , o que o levará a selecionar o espaço de trabalho a configurar.

![Configurar a área de trabalho](media/vminsights-enable-at-scale-policy/configure-workspace.png)

Também pode configurar o seu espaço de trabalho escolhendo **ativar a apólice** e, em seguida, selecionar o botão de barra de ferramentas configurar o espaço de **trabalho.**  Isto irá instalar a solução VMInsights no espaço de trabalho selecionado que permitirá ao espaço de trabalho armazenar os dados de monitorização enviados pelos VMs e conjuntos de escala de máquinavirtual que ativa utilizando a Política. 

![Ativar a utilização da política](media/vminsights-enable-at-scale-policy/enable-using-policy.png)

## <a name="manage-policy-coverage-feature-overview"></a>Gerir a visão geral da funcionalidade de cobertura de políticas

O Azure Monitor para a Cobertura Política de VMs simplifica a descoberta, gestão e capacitação em escala da iniciativa **Enable Azure Monitor for VMs,** que inclui as definições políticas mencionadas anteriormente. Para aceder a esta funcionalidade, selecione **Outras opções** de embarque a partir do separador **Get Started** no Monitor Azure para VMs. Selecione Gerir a **Cobertura política** para abrir o Monitor Azure para a página de Cobertura política de **VMs.**

![Monitor Azure de VMs Get Started tab](./media/vminsights-enable-at-scale-policy/get-started-page.png)

A partir daqui, pode verificar e gerir a cobertura da iniciativa em todos os seus grupos de gestão e subscrições. Pode compreender quantos VMs existem em cada um dos grupos de gestão e subscrições e seu estado de conformidade.

![Monitor Azure para vMs gerir página política](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Esta informação é útil para ajudá-lo a planear e executar o seu cenário de governação para O Monitor Azure para VMs a partir de uma localização central. Embora a Política Azure forneça uma visão de conformidade quando uma política ou iniciativa é atribuída a um âmbito, com esta nova página pode descobrir onde a política ou iniciativa não é atribuída e atribuí-la no lugar. Todas as ações como atribuir, visualizar e editar redirecionamento para a Política Azure diretamente. A página **Azure Monitor for VMs Policy Coverage** é uma experiência alargada e integrada apenas para a iniciativa **Enable Azure Monitor para VMs.**

A partir desta página, também pode configurar o seu espaço de trabalho Log Analytics para O Monitor Azure para VMs, que:

- Instala a solução Do Mapa de Serviço.
- Permite os contadores de desempenho do sistema operativo utilizados pelos gráficos de desempenho, livros de trabalho e as suas consultas e alertas personalizados.

![Monitor Azure para VMs configurar espaço de trabalho](media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Esta opção não está relacionada com nenhuma ação política. Está disponível para fornecer uma maneira fácil de satisfazer os [pré-requisitos necessários](vminsights-enable-overview.md) para ativar o Monitor Azure para VMs.  

### <a name="what-information-is-available-on-this-page"></a>Que informação está disponível nesta página?

A tabela seguinte fornece uma desagregação da informação que é apresentada na página de cobertura de políticas e como interpretá-la.

| Função | Descrição | 
|----------|-------------| 
| **Âmbito** | Grupo de gestão e subscrições a que herdou ou herdou acesso com capacidade de aprofundar através da hierarquia do grupo de gestão.|
| **Função** | O seu papel no âmbito, que pode ser leitor, proprietário ou colaborador. Em alguns casos, pode parecer em branco indicar que pode ter acesso à subscrição, mas não ao grupo de gestão a que pertence. A informação noutras colunas varia consoante o seu papel. O papel é fundamental para determinar que dados pode ver e ações que pode executar em termos de atribuição de políticas ou iniciativas (proprietário), editá-las ou visualizar o cumprimento. |
| **Total VMs** | Número de VMs sob esse âmbito. Para um grupo de gestão, é uma soma de VMs aninhados sob as assinaturas ou grupo de gestão de crianças. |
| **Cobertura de Atribuição** | Por cento dos VMs que estão abrangidos pela política ou iniciativa. |
| **Estatuto de Atribuição** | Informações sobre o estado da sua política ou atribuição de iniciativa. |
| **VMs conformes** | Número de VMs que estão em conformidade com a política ou iniciativa. Para a iniciativa **Enable Azure Monitor para VMs,** este é o número de VMs que têm tanto o agente Log Analytics como o agente dependency. Em alguns casos, pode parecer em branco por falta de atribuição, sem VMs, ou sem permissões suficientes. A informação é fornecida no Estado de **Conformidade.** |
| **Conformidade** | O número global de conformidade é a soma de recursos distintos que são compatíveis divididos pela soma de todos os recursos distintos. |
| **Estado de Compatibilidade** | Informações sobre o estado de conformidade para a sua política ou atribuição de iniciativa.|

Ao atribuir a política ou iniciativa, o âmbito selecionado na atribuição pode ser o âmbito listado ou um subconjunto da mesmas. Por exemplo, pode ter criado uma atribuição para uma subscrição (âmbito de política) e não um grupo de gestão (âmbito de cobertura). Neste caso, o valor da Cobertura de **Atribuição** indica os VMs no âmbito de política ou iniciativa dividido pelos VMs no âmbito de cobertura. Noutro caso, pode ter excluído alguns VMs, grupos de recursos ou uma subscrição do âmbito de aplicação da política. Se o valor estiver em branco, indica que ou a política ou iniciativa não existe ou não tem permissão. A informação é fornecida no estado de **atribuição.**

## <a name="enable-by-using-azure-policy"></a>Ativar utilizando a Política Azure

Para ativar o Monitor Azure para VMs utilizando a Política Azure no seu inquilino:

- Atribuir a iniciativa a um âmbito: grupo de gestão, subscrição ou grupo de recursos.
- Reveja e remediar os resultados da conformidade.

Para obter mais informações sobre a atribuição de Política Azure, consulte a visão geral da [Política do Azure](../../governance/policy/overview.md#policy-assignment) e reveja a [visão geral dos grupos de gestão](../../governance/management-groups/overview.md) antes de continuar.

### <a name="policies-for-azure-vms"></a>Políticas para VMs Azure

As definições políticas para um VM Azure estão listadas na tabela seguinte.

|Nome |Descrição |Tipo |
|-----|------------|-----|
|Ativar o Monitor Azure para VMs |Ativar o Monitor Azure para as máquinas virtuais no âmbito especificado (grupo de gestão, subscrição ou grupo de recursos). Toma o espaço de trabalho log Analytics como parâmetro. |Iniciativa |
|Implementação do agente de dependência da auditoria – Imagem VM (OS) não cotada |Os VMs reportam como não conformes se a imagem VM (OS) não estiver definida na lista e o agente não estiver instalado. |Política |
|Implementação do agente Desembolsação de Registos Analytics – Imagem VM (OS) não cotada |Os VMs reportam como não conformes se a imagem VM (OS) não estiver definida na lista e o agente não estiver instalado. |Política |
|Agente de dependência de implantação para VMs Linux |Desloque o agente de dependência para Os VMs Linux se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |
|Implementar agente de dependência para VMs windows |Implementar o agente dependency para VMs windows se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |
|Implementar o agente Log Analytics para VMs Linux |Implementar o agente Log Analytics para VMs Linux se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |
|Implementar o agente Log Analytics para VMs do Windows |Implementar o agente Log Analytics para VMs do Windows se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Políticas para conjuntos de escala de máquinas virtuais Azure

As definições políticas para um conjunto de escala de máquinas virtuais Azure estão listadas na tabela seguinte.

|Nome |Descrição |Tipo |
|-----|------------|-----|
|Ativar o Monitor Azure para conjuntos de escala de máquinas virtuais |Ativar o Monitor Azure para os conjuntos de escala de máquinavirtual no âmbito especificado (grupo de gestão, subscrição ou grupo de recursos). Toma o espaço de trabalho log Analytics como parâmetro. Nota: Se a sua política de atualização definida em escala estiver definida para Manual, aplique a extensão a todos os VMs definidos, ligando para a atualização dos mesmos. No CLI, isto `az vmss update-instances`é. |Iniciativa |
|Implementação de agente de dependência de auditoria em conjuntos de escala de máquinas virtuais – imagem VM (OS) não cotada |Os relatórios de escala de máquina virtual definidos como não conformes se a imagem VM (OS) não estiver definida na lista e o agente não estiver instalado. |Política |
|Implementação do agente Desembolsante log analytics em conjuntos de escala de máquinas virtuais – imagem VM (OS) não listada |Os relatórios de escala de máquina virtual definidos como não conformes se a imagem VM (OS) não estiver definida na lista e o agente não estiver instalado. |Política |
|Implementar agente de dependência para conjuntos de escala de máquinas virtuais Linux |Desloque o agente de dependência para conjuntos de escala de máquinas virtuais Linux se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |
|Implementar agente de dependência para conjuntos de escala de máquinas virtuais windows |Implementar o agente dependency para conjuntos de escala de máquinas virtuais do Windows se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |
|Implementar o agente Log Analytics para conjuntos de escala de máquinas virtuais Linux |Implementar o agente Log Analytics para conjuntos de escala de máquinas virtuais Linux se a Imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |
|Implementar o agente Log Analytics para conjuntos de escala de máquinas virtuais do Windows |Implemente o agente Log Analytics para conjuntos de escala de máquinas virtuais do Windows se a imagem VM (OS) estiver definida na lista e o agente não estiver instalado. |Política |

A política autónoma (não incluída na iniciativa) é descrita aqui:

|Nome |Descrição |Tipo |
|-----|------------|-----|
|Audit Log Analytics espaço de trabalho para VM – Incompatibilidade de relatório |Informe os VMs como não conformes se não estiverem a aceder ao espaço de trabalho do Log Analytics especificado na atribuição de políticas ou iniciativas. |Política |

### <a name="assign-the-azure-monitor-initiative"></a>Atribuir a iniciativa Azure Monitor

Para criar a atribuição de políticas a partir da página de Cobertura política do **Monitor Azure para VMs,** siga estes passos. Para entender como completar estes passos, consulte [Criar uma atribuição política do portal Azure.](../../governance/policy/assign-policy-portal.md)

Ao atribuir a política ou iniciativa, o âmbito selecionado na atribuição pode ser o âmbito listado aqui ou um subconjunto da mesmas. Por exemplo, pode ter criado uma atribuição para a subscrição (âmbito de política) e não para o grupo de gestão (âmbito de cobertura). Neste caso, a percentagem de cobertura indicaria os VMs no âmbito da política ou iniciativa dividido pelos VM no âmbito da cobertura. Noutro caso, pode ter excluído alguns VMs, ou grupos de recursos, ou uma subscrição do âmbito de aplicação da política. Se estiver em branco, indica que ou a política ou iniciativa não existe ou não tem permissões. A informação é fornecida no estado de **atribuição.**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. No portal Azure, selecione **Monitor**. 

3. Escolha **Máquinas Virtuais** na secção **Insights.**
 
4. Selecione o separador **Get Started.** Na página, selecione **Gerir cobertura**de política .

5. Selecione um grupo de gestão ou uma subscrição da tabela. Selecione **Scope** selecionando a elipse (...). No exemplo, um âmbito de aplicação limita a atribuição de políticas a um agrupamento de máquinas virtuais para aplicação da lei.

6. Na página de atribuição de **Políticas Azure,** é pré-povoada com a iniciativa **Enable Azure Monitor para VMs**. 
    A caixa de **nome supor** a atribuição é automaticamente povoada com o nome da iniciativa, mas pode mudá-la. Também pode adicionar uma descrição opcional. O **Atribuído por** caixa é automaticamente povoado com base em quem está registado. Este valor é opcional.

7. (Opcional) Para remover um ou mais recursos do âmbito, selecione **Exclusões**.

8. Na lista de drop-down do espaço de **trabalho Log Analytics** para a região suportada, selecione um espaço de trabalho.

   > [!NOTE]
   > Se o espaço de trabalho estiver fora do âmbito da atribuição, conceda permissões ao *Log Analytics Contributor* para o ID principal da atribuição de políticas. Se não o fizer, poderá ver uma `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` falha de implementação como a concessão de acesso, reveja [como configurar manualmente a identidade gerida](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  A caixa de verificação de **identidade gerida** é selecionada porque a iniciativa que está a ser atribuída inclui uma política com o efeito *implementaçãoIfNotExists.*
    
9. Na lista de desacato de **localização Manage Identity,** selecione a região apropriada.

10. Selecione **Atribuir**.

Depois de criar a atribuição, o Monitor Azure para cobertura **de políticas vMs** atualiza cobertura de **atribuição,** **Status de atribuição,** **VMs conformes**e **Compliance State** para refletir as alterações. 

A matriz seguinte mapeia cada estado de conformidade possível para a iniciativa.  

| Estado de conformidade | Descrição | 
|------------------|-------------|
| **Compatível** | Todos os VMs no âmbito têm os agentes de Log Analytics e Dependency destacados para eles.|
| **Não conforme** | Nem todos os VMs no âmbito têm os agentes de Log Analytics e Dependency destacados para eles e podem exigir remediação.|
| **Não começou** | Uma nova missão foi adicionada. |
| **Bloquear** | Não tem privilégios suficientes para o grupo de gestão. <sup>1</sup> | 
| **Em branco** | Nenhuma apólice é atribuída. | 

<sup>1</sup> Se não tiver acesso ao grupo de gestão, peça a um proprietário que forneça acesso. Ou, ver a conformidade e gerir as atribuições através dos grupos de gestão de crianças ou subscrições. 

A tabela seguinte mapeia cada um possível estatuto de atribuição para a iniciativa.

| Estatuto de atribuição | Descrição | 
|------------------|-------------|
| **Sucesso** | Todos os VMs no âmbito têm os agentes de Log Analytics e Dependency destacados para eles.|
| **Aviso** | A subscrição não está sob um grupo de gestão.|
| **Não começou** | Uma nova missão foi adicionada. |
| **Bloquear** | Não tem privilégios suficientes para o grupo de gestão. <sup>1</sup> | 
| **Em branco** | Não existem VMs ou uma apólice não é atribuída. | 
| **Ação** | Atribuir uma apólice ou editar uma atribuição. | 

<sup>1</sup> Se não tiver acesso ao grupo de gestão, peça a um proprietário que forneça acesso. Ou, ver a conformidade e gerir as atribuições através dos grupos de gestão de crianças ou subscrições.

## <a name="review-and-remediate-the-compliance-results"></a>Rever e remediar os resultados da conformidade

O exemplo que se segue é para um VM Azure, mas também se aplica a conjuntos de escala de máquinas virtuais. Para saber como rever os resultados da conformidade, consulte [Identificar resultados de incumprimento](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Na página Azure Monitor para Cobertura Política de **VMs,** selecione um grupo de gestão ou uma subscrição da tabela. Selecione **Ver Conformidade** selecionando a elipse (...).   

![Conformidade política para VMs Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

Com base nos resultados das políticas incluídas na iniciativa, os VMs são reportados como incompatíveis nos seguintes cenários:

* O agente de Log Analytics ou o agente de dependência não estão implantados.  
    Este cenário é típico de um âmbito com VMs existentes. Para mitigar, implemente os agentes necessários criando tarefas de [reparação](../../governance/policy/how-to/remediate-resources.md) numa política não conforme.  
    - Agente de dependência de implantação para VMs Linux
    - Implementar agente de dependência para VMs windows
    - Implementar o agente Log Analytics para VMs Linux
    - Implementar o agente Log Analytics para VMs do Windows

* A imagem vM (OS) não está identificada na definição de política.  
    Os critérios da política de implantação incluem apenas VMs que são implantados a partir de imagens bem conhecidas do Azure VM. Verifique a documentação para ver se o VM OS é suportado. Se não for suportado, duplique a política de implementação e atualize-a ou modifique-a para tornar a imagem conforme.  
    - Implementação do agente de dependência da auditoria – Imagem VM (OS) não cotada
    - Implementação do agente Desembolsação de Registos Analytics – Imagem VM (OS) não cotada

* Os VMs não estão a iniciar sessão no espaço de trabalho especificado do Log Analytics.  
    É possível que alguns VMs no âmbito da iniciativa estejam a iniciar sessão num espaço de trabalho de Log Analytics que não o que está especificado na atribuição de políticas. Esta política é uma ferramenta para identificar quais os VMs que estão a reportar a um espaço de trabalho não conforme.  
    - Audit Log Analytics espaço de trabalho para VM – Incompatibilidade de relatório

## <a name="edit-an-initiative-assignment"></a>Editar uma atribuição de iniciativa

A qualquer momento após a atribuição de uma iniciativa a um grupo de gestão ou subscrição, pode editá-la para modificar as seguintes propriedades:

- Nome de atribuição
- Descrição
- Atribuído por
- Área de trabalho do Log Analytics
- Exceções

## <a name="next-steps"></a>Passos seguintes

Agora que a monitorização está ativada para as suas máquinas virtuais, esta informação está disponível para análise com o Monitor Azure para VMs. 

- Para visualizar as dependências de aplicações descobertas, consulte o View Azure Monitor para o Mapa de [VMs](vminsights-maps.md). 

- Para identificar estrangulamentos e utilização geral com o desempenho do seu VM, consulte o desempenho do [VM view Azure](vminsights-performance.md). 
