---
title: Entender a integridade de suas máquinas virtuais do Azure | Microsoft Docs
description: Este artigo descreve como entender a integridade das máquinas virtuais e dos sistemas operacionais subjacentes com Azure Monitor para VMs.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2019
ms.author: magoedte
ms.openlocfilehash: 18297410842b432af0093a71406df71f7e03db9d
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602047"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Entender a integridade de suas máquinas virtuais do Azure

O Azure inclui serviços para funções ou tarefas específicas no espaço de monitoramento, mas não fornece perspectivas de integridade detalhadas de sistemas operacionais (OSs) hospedados em VMs (máquinas virtuais) do Azure. Embora você possa usar Azure Monitor para diferentes condições, ele não foi projetado para modelar e representar a integridade dos componentes principais, ou a integridade geral das VMs.

Usando o Azure Monitor para VMs Health, você pode monitorar ativamente a disponibilidade e o desempenho de um SO convidado Windows ou Linux. O recurso de integridade usa um modelo que representa os principais componentes e suas relações, fornece critérios que especificam como medir a integridade do componente e envia um alerta quando ele detecta uma condição não íntegra.

A exibição do estado de integridade geral de uma VM do Azure e do sistema operacional subjacente pode ser observada em duas perspectivas: diretamente de uma VM ou em todas as VMs em um grupo de recursos de Azure Monitor.

Este artigo mostra como avaliar, investigar e resolver rapidamente problemas de integridade quando eles são detectados pelo recurso de integridade de Azure Monitor para VMs.

Para obter informações sobre como configurar Azure Monitor para VMs, consulte [habilitar Azure monitor para VMs](vminsights-enable-overview.md).

## <a name="monitoring-configuration-details"></a>Detalhes da configuração de monitoramento

Esta seção descreve os critérios de integridade padrão para monitorar as VMs do Windows e Linux do Azure. Todos os critérios de integridade são pré-configurados para enviar um alerta quando detectam uma condição não íntegra.

### <a name="windows-vms"></a>VMs do Windows

- Megabytes disponíveis de memória
- Média de segundos de disco por gravação (disco lógico)
- Média de segundos de disco por gravação (disco)
- Média de segundos de disco lógico por leitura
- Média de segundos de disco lógico por transferência
- Média de segundos de disco por leitura
- Média de segundos de disco por transferência
- Comprimento da fila de disco atual (disco lógico)
- Comprimento da fila de disco atual (disco)
- Porcentagem de tempo ocioso do disco
- Erro ou corrupção do sistema de arquivos
- Espaço livre em disco lógico (%) Pequena
- Espaço livre em disco lógico (MB) baixo
- Porcentagem de tempo ocioso do disco lógico
- Páginas de memória por segundo
- Porcentagem de largura de banda usada lida
- Porcentagem total de largura de banda usada
- Porcentagem de largura de banda usada
- Porcentagem de memória confirmada em uso
- Porcentagem de tempo ocioso do disco
- Integridade do serviço de cliente DHCP
- Integridade do serviço cliente DNS
- Integridade do serviço RPC
- Integridade do serviço do servidor
- Porcentagem de utilização total da CPU
- Integridade do serviço log de eventos do Windows
- Integridade do serviço de firewall do Windows
- Integridade do serviço de Gerenciamento Remoto do Windows

### <a name="linux-vms"></a>VMs do Linux

- Média de disco Disco seg/transferência
- Média de disco Disco seg/leitura
- Média de disco Disco seg/escritas
- Integridade do disco
- Espaço livre em disco lógico
- % De espaço livre no disco lógico
- % De inodes livres no disco lógico
- Integridade do adaptador de rede
- Porcentagem total de tempo do processador
- Megabytes disponíveis do sistema operacional de memória

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Para entrar, vá para a [portal do Azure](https://portal.azure.com).

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Introdução à Azure Monitor para VMs Health

Antes de usar o recurso de integridade para uma única VM ou grupo de VMs, é importante entender como as informações são apresentadas e o que as visualizações representam.

### <a name="view-health-directly-from-a-vm"></a>Exibir a integridade diretamente de uma VM

Para exibir a integridade de uma VM do Azure, selecione insights **(versão prévia)** no painel esquerdo da VM. Na página de informações da VM, a guia **integridade** é aberta por padrão e mostra a exibição de integridade da VM.

![Visão geral do Azure Monitor para VMs Health de uma máquina virtual do Azure selecionada](./media/vminsights-health/vminsights-directvm-health-01.png)

Na seção **integridade da VM** convidada, a tabela mostra o acúmulo de integridade dos componentes de desempenho monitorados pelos critérios de integridade para a VM e o número total de alertas de integridade da VM gerados por componentes não íntegros. Esses componentes incluem **CPU**, **memória**, **disco**e **rede**. Expanda a divisa ao lado de integridade da VM convidada para exibir a integridade de seus componentes.

![Azure Monitor para VMs o status de integridade do componente de uma máquina virtual do Azure selecionada](./media/vminsights-health/vminsights-directvm-health-02.png)

Selecionar o estado ao lado do componente abrirá a experiência de diagnóstico de integridade no contexto do componente selecionado. Ele mostra a composição do estado do componente, descrevendo quais critérios de integridade são usados para computar sua integridade. Para obter mais informações, consulte [diagnóstico de integridade e trabalhando com critérios de integridade](#health-diagnostics). Para obter mais informações sobre alertas, consulte [alertas](#alerts).

Os Estados de integridade definidos para uma VM são descritos na tabela a seguir:

|Ícone |Estado de funcionamento |Significado |
|-----|-------------|---------------|
| |Bom estado de funcionamento |A VM está dentro das condições de integridade definidas. Esse estado indica que não há nenhum problema detectado e a VM está funcionando normalmente. Com um monitor de rollup pai, a integridade acumula e reflete o estado de melhor caso ou o pior caso do filho.|
| |Crítico |O estado não está dentro da condição de integridade definida, indicando que um ou mais problemas críticos foram detectados. Esses problemas devem ser resolvidos para restaurar a funcionalidade normal. Com um monitor de rollup pai, o estado de integridade acumula e reflete o estado de melhor caso ou pior caso do filho.|
| |Aviso |O estado está entre dois limites para a condição de integridade definida, em que um indica um estado de aviso e o outro indica um estado crítico (três limites de estado de integridade podem ser configurados) ou quando um problema não crítico pode causar problemas críticos se não resolvidos. Com um monitor de rollup pai, se um ou mais filhos estiverem em um estado de aviso, o pai refletirá um estado de aviso. Se um filho estiver em um estado crítico e outro filho em um estado de aviso, o rollup pai mostrará o estado de integridade como crítico.|
| |Desconhecido |O estado não pode ser computado por vários motivos. A seção a seguir fornece detalhes adicionais e possíveis soluções. |

Um estado de integridade desconhecido pode ser causado pelos seguintes problemas:

- O agente foi reconfigurado e não fornece mais relatórios para o espaço de trabalho especificado quando Azure Monitor para VMs foi habilitado. Para configurar o agente para relatar ao espaço de trabalho, consulte [adicionando ou removendo um espaço de trabalho](../platform/agent-manage.md#adding-or-removing-a-workspace).
- A VM foi excluída.
- O espaço de trabalho associado a Azure Monitor para VMs foi excluído. Você pode recuperar o espaço de trabalho se tiver benefícios de suporte Premier. Vá para [Premier](https://premier.microsoft.com/) e abra uma solicitação de suporte.
- As dependências da solução foram excluídas. Para reabilitar as soluções ServiceMap e InfrastructureInsights em seu espaço de trabalho Log Analytics, reinstale essas soluções usando o [modelo Azure Resource Manager](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions). Ou use a opção Configurar espaço de trabalho encontrada na guia introdução.
- A VM foi desligada.
- O serviço de VM do Azure está indisponível ou a manutenção está sendo executada.
- O [limite de retenção ou dados diário](../platform/manage-cost-storage.md) do espaço de trabalho foi atingido.

Selecione **Exibir diagnóstico de integridade** para abrir uma página mostrando todos os componentes de uma VM, critérios de integridade associados, alterações de estado e outros problemas detectados por componentes de monitoramento relacionados à VM.

Para obter mais informações, consulte [diagnóstico de integridade](#health-diagnostics).

Na seção **integridade** , uma tabela mostra o acúmulo de integridade dos componentes de desempenho monitorados pelos critérios de integridade. Esses componentes incluem **CPU**, **memória**, **disco**e **rede**. A seleção de um componente abre uma página que lista todos os critérios de monitoramento e o estado de integridade desse componente.

Quando você acessa a integridade de uma VM do Azure que executa o Windows, o estado de integridade dos cinco principais serviços do Windows é mostrado em **integridade dos serviços principais**. A seleção de qualquer um dos serviços abre uma página que lista o monitoramento de critérios de integridade para esse componente junto com seu estado de integridade.

A seleção do nome dos critérios de integridade abre o painel de propriedades. Nesse painel, você pode examinar os detalhes de configuração, incluindo se os critérios de integridade têm um alerta de Azure Monitor correspondente.

Para obter mais informações, consulte [diagnóstico de integridade e trabalhando com critérios de integridade](#health-diagnostics).

### <a name="aggregate-vm-perspective"></a>Perspectiva da VM agregada

Para exibir a coleção de integridade de todas as suas VMs em um grupo de recursos, selecione **Azure monitor** na lista de navegação no portal e, em seguida, selecione **máquinas virtuais (versão prévia)** .

![Exibição de monitoramento do Gerenciador de informações da VM de Azure Monitor](./media/vminsights-health/vminsights-aggregate-health.png)

Nas listas suspensas **assinatura** e **grupo de recursos** , selecione o grupo de recursos apropriado que inclui as VMs relacionadas ao grupo para exibir o estado de integridade relatado. Sua seleção só se aplica ao recurso de integridade e não é transferida para guias de **desempenho** ou **mapa** .

A guia **integridade** fornece as seguintes informações:

* Quantas VMs estão em um estado crítico ou não íntegro, em comparação com quantas estão íntegras ou não estão enviando dados (chamados de estado desconhecido).
* Quais e quantas VMs por sistema operacional estão relatando um estado não íntegro.
* Quantas VMs não estão íntegras devido a um problema detectado com um processador, disco, memória ou adaptador de rede, Categorizado por estado de integridade.
* Quantas VMs não estão íntegras devido a um problema detectado com um serviço de sistema operacional principal, Categorizado por estado de integridade.

Na guia **integridade** , você pode identificar os problemas críticos detectados pelos critérios de integridade que monitoram a VM e examinar os detalhes do alerta e os artigos de conhecimento associados. Esses artigos podem ajudar no diagnóstico e na correção de problemas. Selecione qualquer uma das severidades para abrir a página [todos os alertas](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) filtrada por essa gravidade.

A lista **distribuição de VM por sistema operacional** mostra as VMs listadas pela distribuição do Windows Edition ou do Linux, juntamente com sua versão. Em cada categoria de sistema operacional, as VMs são divididas ainda mais com base na integridade da VM.

![Perspectiva de distribuição de máquina virtual do insights de VM](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Selecione qualquer coluna, **incluindo contagem de VM**, **crítica**, **aviso**, **íntegro**ou **desconhecido**. Exiba a lista de resultados filtrados na página **máquinas virtuais** que correspondem à coluna selecionada.

Por exemplo, para examinar todas as VMs que executam Red Hat Enterprise Linux versão 7,5, selecione o valor de **contagem de VM** para esse so e ele listará as VMs que correspondem a esse filtro e seu estado de integridade atual.

![ROLLUP de exemplo de VMs Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

Você clica em **Mostrar integridade** caixa de seleção e o estado de integridade é retornado para os resultados filtrados na tabela.  

![Exemplo de estado de integridade de VMs Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-02.png)

Para qualquer um dos itens na lista, você pode clicar no estado de integridade correspondente para iniciar o diagnóstico de integridade, que mostra como a integridade é avaliada para a VM selecionada. 

Na página **máquinas virtuais** , se você selecionar o nome de uma VM sob a coluna **nome da VM**, você será direcionado para a página **instância de VM** . Esta página fornece mais detalhes sobre os alertas e problemas de critérios de integridade que estão afetando a VM selecionada. Filtre os detalhes do estado de integridade selecionando o ícone **estado de integridade** no canto superior esquerdo da página para ver quais componentes não estão íntegros. Você também pode exibir os alertas de integridade da VM gerados por um componente não íntegro categorizado pela severidade do alerta.

Na exibição de **lista de VMs** , selecione o nome de uma VM para abrir a página de **integridade** dessa VM, da mesma forma como se você selecionou insights **(visualização)** diretamente da VM.

![Informações de VM de uma máquina virtual do Azure selecionada](./media/vminsights-health/vminsights-directvm-health.png)

As **máquinas virtuais (visualização) na página Azure monitor** mostra um status de integridade de rollup para a VM e os alertas. Esse status de integridade é categorizado por severidade, que representa os alertas de integridade da VM gerados quando o estado de integridade mudou de íntegro para não íntegro, com base em critérios. A seleção de **VMs em condição crítica** abre uma página com uma lista de uma ou mais VMs em um estado de integridade crítico.

Selecionar o status de integridade de uma das VMs mostra a exibição de **diagnóstico de integridade** da VM. Nessa exibição, você pode determinar quais critérios de integridade estão refletindo um problema de estado de integridade. Quando a página **diagnóstico de integridade** é aberta, mostra todos os componentes da VM e seus critérios de integridade associados com o estado de integridade atual.

Para obter mais informações, consulte [diagnóstico de integridade](#health-diagnostics).

Selecionar **Exibir todos os critérios de integridade** abre uma página mostrando uma lista de todos os critérios de integridade disponíveis com esse recurso. As informações podem ser filtradas ainda mais com base nas seguintes opções:

* **Digite**. Há três tipos de critérios de integridade para avaliar condições e acumular o estado de integridade geral de uma VM monitorada:
    - **Unidade**. Mede algum aspecto de uma VM. Esse tipo de critério de integridade pode estar verificando um contador de desempenho para determinar o desempenho do componente, executando um script para executar uma transação sintética ou observando um evento que indica um erro. O filtro é definido como unidade por padrão.
    - **Dependência**. Fornece um acúmulo de integridade entre entidades diferentes. Esse critério de integridade permite que a integridade de uma entidade dependa da integridade de outro tipo de entidade da qual ela depende para uma operação bem-sucedida.
    - **Agregação**. Fornece um estado de integridade combinado de critérios de integridade semelhantes. Os critérios de integridade de unidade e de dependência normalmente são configurados sob um critério de integridade agregado. Além de fornecer uma melhor organização geral dos vários critérios de integridade diferentes direcionados a uma entidade, o critério de integridade agregado fornece um estado de integridade exclusivo para categorias distintas das entidades.

* **Categoria**. O tipo de critérios de integridade usados para agrupar critérios semelhantes para fins de relatório. Essas categorias são **disponibilidade** e **desempenho**.

Para ver quais instâncias não estão íntegras, selecione um valor na coluna **componente não íntegro** . Nesta página, uma tabela lista os componentes que estão em um estado de integridade crítico.

## <a name="health-diagnostics"></a>Diagnóstico de integridade

A página **diagnóstico de integridade** permite que você visualize o modelo de integridade de uma VM. Esta página lista todos os componentes da VM, os critérios de integridade associados, as alterações de estado e outros problemas significativos identificados pelos componentes monitorados relacionados à VM.

![Exemplo de página de diagnóstico de integridade para uma VM](./media/vminsights-health/health-diagnostics-page-01.png)

Inicie o diagnóstico de integridade usando os seguintes métodos:

* Por estado de integridade de rollup para todas as VMs da perspectiva da VM agregada no Azure Monitor:

    1. Na página **integridade** , selecione o ícone do estado de integridade **crítico**, de **aviso**, **ÍNTEGRO**ou **desconhecido** na seção integridade da **VM**convidada.
    2. Vá para a página que lista todas as VMs que correspondem a essa categoria filtrada.
    3. Selecione o valor na coluna **estado de integridade** para abrir o diagnóstico de integridade com escopo para essa VM.

* Pelo sistema operacional da perspectiva da VM agregada no Azure Monitor. Em **distribuição de VM**, a seleção de qualquer um dos valores de coluna abrirá a página **máquinas virtuais** e retornará uma lista na tabela correspondente à categoria filtrada. Selecionar o valor na coluna **estado de integridade** abre o diagnóstico de integridade para a VM selecionada.
 
* Na VM convidada na guia Azure Monitor para VMs **integridade** , selecionando **Exibir diagnóstico de integridade**.

O diagnóstico de integridade organiza as informações de integridade em duas categorias: disponibilidade e desempenho.
 
Todos os critérios de integridade definidos para um componente, como disco lógico, CPU e assim por diante, podem ser exibidos sem filtragem nas duas categorias. Essas exibições podem estar em uma exibição de todos os critérios ou por meio da filtragem dos resultados por qualquer categoria quando você seleciona **disponibilidade** ou **desempenho**.

Além disso, a categoria de critérios pode ser vista ao lado da coluna **critérios de integridade** . Se os critérios não corresponderem à categoria selecionada, uma mensagem informando **que nenhum critério de integridade disponível para a categoria selecionada** aparecerá na coluna **critérios de integridade** .

O estado de um critério de integridade é definido por um dos quatro tipos: **Crítico**, de **aviso**, **íntegro**e **desconhecido**. Os três primeiros são configuráveis, o que significa que você pode modificar os valores de limite dos monitores diretamente no painel de configuração de **critérios de integridade** . Isso também é possível usando a Azure Monitor [operação do monitor de atualização](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)da API REST. **Desconhecido** não é configurável e está reservado para cenários específicos.

A página **diagnóstico de integridade** tem três seções principais:

* Modelo do Componente
* Critérios de Estado de Funcionamento
* Alterações de Estado

![Seções da página diagnóstico de integridade](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Modelo de componente

A coluna mais à esquerda na página **diagnóstico de integridade** é **modelo de componente**. Todos os componentes, que estão associados à VM, são exibidos nesta coluna junto com seu estado de integridade atual.

No exemplo a seguir, os componentes descobertos são **disco**, **disco lógico**, **processador**, **memória**e **sistema operacional**. Várias instâncias desses componentes são descobertas e exibidas nesta coluna.

Por exemplo, a figura a seguir mostra que a VM tem duas instâncias de discos lógicos, **C:** e **D:** , que estão em um estado íntegro:

![Modelo de componente de exemplo apresentado no diagnóstico de integridade](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Critérios de integridade

A coluna central na página diagnóstico de integridade é **critérios de integridade**. O modelo de integridade definido para a VM é exibido em uma árvore hierárquica. O modelo de integridade para uma VM consiste em critérios de integridade de unidade e agregação.

![Critérios de integridade de exemplo apresentados no diagnóstico de integridade](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Um critério de integridade mede a integridade de uma instância monitorada, que pode ser um valor limite, estado de uma entidade e assim por diante. Um critério de integridade tem dois ou três limites de estado de integridade configuráveis, conforme descrito anteriormente. A qualquer momento, o critério de integridade pode estar em apenas um de seus Estados potenciais.

O modelo de integridade define critérios que determinam a integridade do destino geral e dos componentes do destino. A hierarquia de critérios é mostrada na seção **critérios de integridade** na página diagnóstico de **integridade** .

A política de acumulação de integridade faz parte da configuração dos critérios de integridade agregada (o padrão é definido como **pior**). Você pode encontrar um conjunto padrão de critérios de integridade em execução como parte desse recurso na seção [detalhes de configuração de monitoramento](#monitoring-configuration-details) deste artigo.

Você também pode usar a lista de instâncias do monitor de API REST Azure Monitor [por recurso](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) para obter uma lista de todos os critérios de integridade. Esses critérios incluem detalhes de configuração em execução no recurso de VM do Azure.

O tipo de critério de integridade da **unidade** pode ter sua configuração modificada selecionando o link de reticências ao lado direito. Selecione **Mostrar detalhes** para abrir o painel de configuração.

![Configurando um exemplo de critérios de integridade](./media/vminsights-health/health-diagnostics-vm-example-02.png)

No painel de configuração dos critérios de integridade selecionados, se você usar o exemplo **média de segundos de disco por gravação**, o limite poderá ser configurado com um valor numérico diferente. É um monitor de dois Estados, o que significa que ele pode mudar apenas de **íntegro** para **aviso**.

Outros critérios de integridade às vezes usam três Estados, onde você pode configurar o valor para limites de estado de integridade críticos e de aviso. Você também pode modificar um limite usando Azure Monitor [configuração do monitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)da API REST.

>[!NOTE]
>A aplicação de alterações de configuração de critérios de integridade a uma instância aplica-as a todas as instâncias monitoradas. Por exemplo, se você selecionar **disco-1 D:** e, em seguida, modificar o limite **médio de segundos de disco por gravação** , a alteração se aplicará a todas as instâncias descobertas e monitoradas na VM.


![Configurando um critério de integridade de um exemplo de monitor de unidade](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Se você quiser saber mais sobre os critérios de integridade, incluímos artigos de conhecimento para ajudá-lo a identificar problemas, causas e resoluções. Selecione **Exibir informações** na página para ver o artigo de conhecimento relacionado.

Para examinar todos os artigos de conhecimento incluídos no Azure Monitor para VMs Health, consulte a [documentação de conhecimento do Azure monitor Health](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).

### <a name="state-changes"></a>Alterações de estado

A coluna da extrema direita da página **diagnóstico de integridade** são **alterações de estado**. Esta coluna lista todas as alterações de estado associadas aos critérios de integridade selecionados na seção **critérios de integridade** ou a alteração de estado da VM se uma VM foi selecionada na coluna **modelo de componente** ou critérios de **integridade** da tabela.

![Alterações de estado de exemplo apresentadas no diagnóstico de integridade](./media/vminsights-health/health-diagnostics-page-statechanges.png)

A seção a seguir mostra o estado de critérios de integridade e a hora associada. Essas informações mostram o estado mais recente na parte superior da coluna.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Associação das colunas modelo de componente, critérios de integridade e alterações de estado

As três colunas são interligadas entre si. Quando você seleciona uma instância na coluna **modelo de componente** , a coluna **critérios de integridade** é filtrada para essa exibição de componente. De modo correspondente, a coluna de **alterações de estado** é atualizada com base nos critérios de integridade selecionados.

![Exemplo de seleção de instância e resultados monitorados](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Por exemplo, se você selecionar *disco-1 D:* na lista em **modelo de componente**, **critérios de integridade** filtros para *disco-1D:* e **alterações de estado** mostrarão a alteração de estado com base na disponibilidade do *disco-1 D:* .

Para ver um estado de integridade atualizado, você pode atualizar a página de diagnóstico de integridade selecionando o link **Atualizar** . Se houver uma atualização para o estado de integridade do critério de integridade com base no intervalo de sondagem predefinido, essa tarefa permitirá que você evite aguardar e reflita o estado de integridade mais recente. O **estado dos critérios de integridade** é um filtro que permite o escopo dos resultados com base no estado de integridade selecionado: Íntegro, aviso, crítico, desconhecido e tudo. A hora da **última atualização** no canto superior direito representa a última vez em que a página de diagnóstico de integridade foi atualizada.

## <a name="alerts"></a>Alertas

O Azure Monitor para VMs Health se integra aos [alertas do Azure](../../azure-monitor/platform/alerts-overview.md). Ele gera um alerta quando critérios predefinidos, quando detectados, mudam de um estado íntegro para um estado não íntegro. Os alertas são categorizados por severidade, de Sev 0 a Sev 4, com Sev 0 como o nível mais alto.

Os alertas não estão associados a um grupo de ações para notificá-lo quando o alerta foi disparado. O proprietário da assinatura deve configurar notificações seguindo as etapas na seção [configurar alertas](#configure-alerts) .

O número total de alertas de integridade da VM categorizados por severidade está disponível no painel de **integridade** na seção **alertas** . Quando você seleciona o número total de alertas ou o número correspondente a um nível de severidade, a página **alertas** é aberta e lista todos os alertas que correspondem à sua seleção.

Por exemplo, se você selecionar a linha correspondente ao **Sev nível 1**, verá o seguinte modo de exibição:

![Exemplo de todos os alertas de nível de severidade 1](./media/vminsights-health/vminsights-sev1-alerts-01.png)

A página **todos os alertas** não tem o escopo definido para mostrar somente os alertas que correspondem à sua seleção. Ele também é filtrado pelo **tipo de recurso** para mostrar apenas os alertas de integridade gerados por um recurso da VM. Esse formato é refletido na lista de alertas, sob o **recurso de destino**da coluna, em que mostra a VM do Azure o alerta gerado quando uma condição não íntegra foi atendida.

Os alertas de outros tipos de recursos ou serviços não devem ser incluídos nessa exibição. Esses alertas incluem alertas de log, que se baseiam em consultas de log ou alertas de métrica que você normalmente veria na página padrão Azure Monitor [todos os alertas](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) .

Você pode filtrar essa exibição selecionando valores nos menus suspensos na parte superior da página.

|Coluna |Descrição |
|-------|------------|
|Subscription |Selecione uma subscrição do Azure. Somente os alertas na assinatura selecionada são incluídos na exibição. |
|Grupo de Recursos |Selecione um único grupo de recursos. Somente os alertas com destinos no grupo de recursos selecionado são incluídos na exibição. |
|Tipo de recurso |Selecione um ou mais tipos de recurso. Por padrão, somente alertas de **máquinas virtuais** de destino são selecionados e incluídos nessa exibição. Esta coluna só estará disponível depois que um grupo de recursos tiver sido especificado. |
|Resource |Selecione um recurso. Somente os alertas com esse recurso como um destino são incluídos na exibição. Esta coluna só estará disponível depois que um tipo de recurso tiver sido especificado. |
|Severity |Selecione uma severidade de alerta ou selecione **tudo** para incluir alertas de todas as severidades. |
|Condição do Monitor |Selecione uma condição de monitor para filtrar alertas se eles tiverem sido acionados ou resolvidos pelo sistema se a condição não estiver mais ativa. Ou selecione **todos** para incluir alertas de todas as condições. |
|Estado de alerta |Selecione um estado de alerta, **novo**, **reconhecer**, **fechado**ou **todos** para incluir alertas de todos os Estados. |
|Monitorizar serviço |Selecione um serviço ou selecione **todos** para incluir todos os serviços. Somente os alertas do VM insights têm suporte para esse recurso.|
|Intervalo de tempo| Somente os alertas acionados na janela de tempo selecionada são incluídos na exibição. Os valores com suporte são a última hora, as últimas 24 horas, os últimos 7 dias e os últimos 30 dias. |

Quando você seleciona um alerta, a página de **detalhes do alerta** é exibida. Esta página fornece detalhes do alerta e permite que você altere seu estado.

Para saber mais sobre como gerenciar alertas, confira [criar, exibir e gerenciar alertas usando o Azure monitor](../../azure-monitor/platform/alerts-metric.md).

>[!NOTE]
>Atualmente, não há suporte para a criação de novos alertas com base nos critérios de integridade ou para modificar as regras de alerta de integridade existentes no Azure Monitor do Portal.


![Painel detalhes do alerta para um alerta selecionado](./media/vminsights-health/alert-details-pane-01.png)

Você pode alterar um estado de alerta para um ou vários alertas selecionando-os e, em seguida, selecionando **alterar estado** na página **todos os alertas** no canto superior esquerdo. Selecione um dos Estados no painel **alterar estado do alerta** , adicione uma descrição da alteração no campo **Comentário** e, em seguida, selecione **OK** para confirmar suas alterações. Quando as informações são verificadas e as alterações são aplicadas, acompanhe o progresso em **notificações** no menu.

### <a name="configure-alerts"></a>Configurar alertas
Você não pode gerenciar determinadas tarefas de gerenciamento de alertas do portal do Azure. Essas tarefas devem ser executadas usando a [API REST do Azure monitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Especificamente:

- Habilitando ou desabilitando um alerta para critérios de integridade
- Configurando notificações para alertas de critérios de integridade

Cada exemplo usa [ARMClient](https://github.com/projectkudu/armclient) em seu computador Windows. Se você não estiver familiarizado com esse método, consulte [usando ARMClient](../platform/rest-api-walkthrough.md#use-armclient).

#### <a name="enable-or-disable-an-alert-rule"></a>Habilitar ou desabilitar uma regra de alerta

Para habilitar ou desabilitar um alerta para critérios de integridade específicos, a propriedade **alertGeneration** deve ser modificada com um valor de **desabilitado** ou **habilitado**.

Para identificar o *monitorid* para critérios de integridade específicos, o exemplo a seguir mostra como consultar esse valor para os critérios **lógico \ média disco segundos por transferência**:

1. Em uma janela de terminal, digite **armclient. exe login**. Isso solicitará que você entre no Azure.

2. Digite o seguinte comando para recuperar todo o critério de integridade ativo em uma VM específica e identificar o valor da propriedade monitorid:

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    O exemplo a seguir mostra a saída do comando *ARMCLIENT Get* . Anote o valor de Monitorid. Esse valor é necessário para a próxima etapa, onde devemos especificar a ID dos critérios de integridade e modificar sua propriedade para criar um alerta.

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. Digite o seguinte comando para modificar a propriedade *alertGeneration* :

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Insira o comando GET usado na etapa 2 para verificar se o valor da propriedade está definidocomo desabilitado.

#### <a name="associate-an-action-group-with-health-criteria"></a>Associar um grupo de ação a critérios de integridade

O Azure Monitor para VMs Health dá suporte a notificações de SMS e email quando os alertas são gerados a partir de critérios de integridade não íntegros. Para configurar notificações, observe o nome do grupo de ações configurado para enviar notificações por email ou SMS.

>[!NOTE]
>Essa ação deve ser executada em cada VM monitorada para a qual você deseja receber uma notificação. Ele não se aplica a todas as VMs em um grupo de recursos.

1. Em uma janela de terminal, insira o *logon do armclient. exe*. Isso solicitará que você entre no Azure.

2. Digite o seguinte comando para associar um grupo de ação a regras de alerta:
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Para verificar se o valor da propriedade **actionGroupResourceIds** foi atualizado com êxito, insira o seguinte comando:

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    A saída deve ser semelhante aos seguintes critérios:
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>Passos Seguintes

- Para identificar as limitações e o desempenho geral da VM, consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md).
- Para saber mais sobre dependências de aplicativo descobertas, consulte [exibir mapa de Azure monitor para VMs](vminsights-maps.md).
