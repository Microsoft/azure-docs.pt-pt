---
title: Compreender o estado de funcionamento das suas máquinas virtuais do Azure | Documentos da Microsoft
description: Este artigo descreve como compreender o estado de funcionamento de máquinas virtuais e sistemas de operativos subjacentes com o Azure Monitor para as VMs.
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
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 2bf891f8cfecbb9e78e511dcee7ed1c61c170016
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340147"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Compreender o estado de funcionamento das suas máquinas virtuais do Azure

Azure inclui serviços para funções específicas ou tarefas no espaço de monitorização, mas ele não fornece perspectivas de estado de funcionamento detalhado dos sistemas operacionais (OSs) alojadas em máquinas virtuais do Azure (VMs). Apesar de poder utilizar o Azure Monitor para condições diferentes, não foi concebido para modelar e representam o estado de funcionamento dos componentes principais, ou o estado de funcionamento global de VMs.

Ao utilizar o Azure Monitor de estado de funcionamento de VMs, pode monitorizar ativamente a disponibilidade e desempenho de um convidado SO Windows ou Linux. A funcionalidade de estado de funcionamento utiliza um modelo que representa os principais componentes e suas relações, fornece critérios que especifica como medir o estado de funcionamento do componente e envia um alerta quando Deteta uma condição de mau estado de funcionamento.

Visualizar o estado de funcionamento geral de uma VM do Azure e o sistema operacional subjacente pode ser observado de duas perspetivas: diretamente a partir de uma VM, ou em todas as VMs num grupo de recursos do Azure Monitor.

Este artigo mostra como avaliar rapidamente, investigar e resolver problemas de estado de funcionamento quando eles são detetados pelo Monitor do Azure para a funcionalidade de estado de funcionamento de VMs.

Para obter informações sobre como configurar o Azure Monitor para VMs, veja [ativar o Azure Monitor para VMs](vminsights-enable-overview.md).

## <a name="monitoring-configuration-details"></a>Detalhes de configuração de monitorização

Esta seção descreve os critérios de estado de funcionamento padrão para monitorizar o Windows Azure e VMs do Linux. Todos os critérios de estado de funcionamento são previamente configurados para enviar um alerta quando detetam uma condição de mau estado de funcionamento.

### <a name="windows-vms"></a>VMs do Windows

- Disponível em Megabytes de memória
- Segundos de média de disco por gravação (disco lógico)
- Segundos de média de disco por gravação (disco)
- Segundos de disco lógico médio por leitura
- Segundos de disco lógico médio por transferência
- Segundos de média de disco por leitura
- Segundos de disco médio por transferência
- Comprimento de fila de disco atual (disco lógico)
- Comprimento de fila de disco atual (disco)
- Tempo de inatividade de percentagem de disco
- Erro de sistema de ficheiros ou corrupção
- Espaço livre de disco lógico (%) Baixa
- Disco lógico (MB) de espaço livre reduzido
- Tempo de inatividade de percentagem de disco lógico
- Páginas de memória por segundo
- Largura de banda de percentagem utilizada de leitura
- Largura de banda percentagem utilizada Total
- Largura de banda de percentagem utilizada de escrita
- Percentagem de memória consolidada em utilização
- Tempo de inatividade de percentagem de disco
- Estado de funcionamento de serviço de cliente DHCP
- Estado de funcionamento de serviço de cliente DNS
- Estado de funcionamento de serviço RPC
- Estado de funcionamento de serviço de servidor
- Percentagem de utilização da total CPU
- Estado de funcionamento de serviço de registo de eventos de Windows
- Estado de funcionamento do serviço do Windows Firewall
- Estado de funcionamento do serviço do Windows gestão remota

### <a name="linux-vms"></a>VMs do Linux

- Média de disco Disco seg/transferência
- Média de disco Disco seg/leitura
- Média de disco Disco seg/escritas
- Estado de funcionamento do disco
- Espaço livre em disco lógico
- Disco lógico % de espaço livre
- Disco lógico % de Inodes livres
- Estado de funcionamento de placa de rede
- Tempo de processador de percentagem total
- Sistema operativo de Megabytes de memória disponíveis

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Para iniciar sessão, vá para o [portal do Azure](https://portal.azure.com).

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Introdução ao Azure Monitor para o estado de funcionamento de VMs

Antes de utilizar a funcionalidade de estado de funcionamento para uma única VM ou grupo de VMs, é importante compreender como as informações são apresentadas e o que representam as visualizações.

### <a name="view-health-directly-from-a-vm"></a>Ver estado de funcionamento diretamente a partir de uma VM

Para ver o estado de funcionamento de uma VM do Azure, selecione **Insights (pré-visualização)** no painel esquerdo da VM. Na página de informações VM, o **estado de funcionamento** separador está aberto por predefinição e mostra a vista de estado de funcionamento da VM.

![O Azure Monitor para descrição geral do Estado de funcionamento de VMs de uma máquina virtual do Azure selecionada](./media/vminsights-health/vminsights-directvm-health.png)

Na **estado de funcionamento** separador, em **estado de funcionamento da VM do convidado**, a tabela mostra o estado de funcionamento da VM e o número total de alertas de estado de funcionamento da VM gerados por um componente em mau estado de funcionamento.

Para obter mais informações, consulte [alertas](#alerts).

Os Estados de funcionamento definidos para uma VM são descritos na tabela a seguir:

|Ícone |Estado de funcionamento |Significado |
|-----|-------------|---------------|
| |Bom estado de funcionamento |A VM estiver nas condições de estado de funcionamento definidos. Este estado indica lá são sem problemas detetados e a VM está a funcionar normalmente. Com um monitor de rollup principal, o estado de funcionamento é agregado e reflete o estado mais favorável ou pior do filho.|
| |Crítico |O estado não estiver na condição de estado de funcionamento definidos, que indica que uma ou foram detetados problemas mais críticos. Esses problemas devem ser resolvidos para restaurar o funcionamento normal. Com um monitor de rollup principal, o estado de funcionamento é agregado e reflete o estado mais favorável ou pior do filho.|
| |Aviso |O estado é entre dois limiares para a condição de estado de funcionamento definidos, onde um indica um Estado de aviso e a outra indica um estado crítico (três limiares de estado de funcionamento podem ser configurados), ou quando um problema de não-críticas pode causar problemas críticos se não resolvido. Com um monitor de rollup principal, se um ou mais filhos está num Estado de aviso, o elemento principal irá refletir o estado de aviso. Se for um filho num estado crítico e subordinado de outro num Estado de aviso, o rollup principal irá mostrar o estado de funcionamento como crítico.|
| |Desconhecido |Não é possível calcular o estado por vários motivos. A secção seguinte fornece detalhes adicionais e possíveis soluções. |

Um Estado de funcionamento desconhecido pode ser causado pelos seguintes problemas:

- O agente foi reconfigurado e já não relatórios para a área de trabalho especificado quando o Azure Monitor para VMs foi ativado. Para configurar o agente para reportar para a área de trabalho, consulte [adição ou remoção de uma área de trabalho](../platform/agent-manage.md#adding-or-removing-a-workspace).
- A VM foi eliminada.
- A área de trabalho associada com o Azure Monitor para VMs foi eliminada. Pode recuperar a área de trabalho, se tiver os benefícios de suporte Premier. Aceda a [Premier](https://premier.microsoft.com/) e abra um pedido de suporte.
- As dependências de solução foram eliminadas. Para voltar a activar as soluções ServiceMap e InfrastructureInsights na sua área de trabalho do Log Analytics, reinstalar estas soluções utilizando o [modelo Azure Resource Manager](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions). Em alternativa, utilize a opção de configurar a área de trabalho foi encontrada no separador de começar a utilizar.
- A VM foi encerrada.
- O serviço de VM do Azure está disponível ou está a ser executada manutenção.
- A área de trabalho [dados diária ou limite de retenção](../platform/manage-cost-storage.md) foi cumprida.

Selecione **ver o diagnóstico de estado de funcionamento** para abrir uma página que mostra todos os componentes de uma VM, associados critérios de estado de funcionamento, as alterações de estado e outros problemas detetados pela monitorização de componentes relacionados com a VM.

Para obter mais informações, consulte [diagnóstico de estado de funcionamento](#health-diagnostics).

Na **estado de funcionamento** seção, uma tabela mostra o rollup de estado de funcionamento dos componentes de desempenho monitorizados pelos critérios de estado de funcionamento. Estes componentes incluem **CPU**, **memória**, **disco**, e **rede**. Selecionar um componente é aberta uma página que lista todos os do critério de monitorização e o estado de funcionamento desse componente.

Quando acessa o estado de funcionamento a partir de uma VM do Azure que executa o Windows, o estado de funcionamento dos serviços de Windows core de cinco principais é apresentado em **Core dos serviços de estado de funcionamento**. Selecionar qualquer um dos serviços abre uma página que lista os critérios de estado de funcionamento de monitorização para esse componente, juntamente com o seu estado de funcionamento.

Selecionar o nome dos critérios de estado de funcionamento é aberto o painel de propriedades. Neste painel, pode rever os detalhes de configuração, incluindo se os critérios de estado de funcionamento tem um alerta correspondente do Azure Monitor.

Para obter mais informações, consulte [diagnóstico de estado de funcionamento e trabalhar com os critérios de estado de funcionamento](#health-diagnostics).

### <a name="aggregate-vm-perspective"></a>Ponto de vista VM agregado

Para ver a coleção de estado de funcionamento para todas as suas VMs num grupo de recursos, selecione **do Azure Monitor** na lista de navegação no portal e, em seguida, selecione **máquinas virtuais (pré-visualização)** .

![Vista do Azure Monitor de monitorização de informações de VM](./media/vminsights-health/vminsights-aggregate-health.png)

Na **subscrição** e **grupo de recursos** listas pendentes, selecione o grupo de recursos adequados que inclui as VMs relacionados ao grupo, para ver o respetivo estado de funcionamento comunicadas. A seleção de apenas aplica-se para a funcionalidade de estado de funcionamento e não passa para **desempenho** ou **mapa** separadores.

O **estado de funcionamento** separador fornece as seguintes informações:

* O número de VMs está num estado crítico ou mau estado de funcionamento, versus quantas estão em bom estadas ou não estiver a enviar dados (referidos como um Estado desconhecido).
* Quais e quantas VMs pelo sistema operacional estiver está a comunicar um mau estado de funcionamento.
* O número de VMs é mau estado de funcionamento devido a um problema detetado com um processador, disco, memória ou um adaptador de rede, categorizados por Estado de funcionamento.
* O número de VMs é mau estado de funcionamento devido a um problema detetado com um serviço de sistema operacional principal, categorizado por Estado de funcionamento.

Sobre o **estado de funcionamento** separador, pode identificar os problemas críticos detetados pelos critérios de estado de funcionamento monitorize a VM e a revisão de detalhes do alerta e a artigos de conhecimento associado. Estes artigos podem ajudar no diagnóstico e correção de problemas. Selecione qualquer uma das gravidades para abrir o [todos os alertas](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) página filtrada por esse gravidade.

O **distribuição VM pelo sistema operativo** lista mostra as VMs listadas por edição do Windows ou a distribuição de Linux, juntamente com suas versões. Em cada categoria de sistema operacional, as VMs são divididas de forma ainda mais com base no estado de funcionamento da VM.

![Ponto de vista de distribuição de máquina virtual de VM Insights](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Selecione qualquer coluna, incluindo **contagem VM**, **crítico**, **aviso**, **bom estado de funcionamento**, ou **desconhecido**. Ver a lista de resultados filtrados no **máquinas virtuais** página que correspondem a coluna selecionada.

Por exemplo, para rever todas as VMs que executam o Red Hat Enterprise Linux versão 7.5, selecione o **contagem VM** valor para o sistema operacional e ele irá listar as VMs que correspondem esse filtro e o respetivo estado atual do Estado de funcionamento.

![Exemplo de rollup de VMs de Linux do Red Hat](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

Na **máquinas virtuais** página, se selecionar o nome de uma VM sob a coluna **nome da VM**, está direcionado para o **instância de VM** página. Esta página fornece mais detalhes sobre os alertas e problemas de critérios de estado de funcionamento que estão afetando a VM selecionada. Filtrar os detalhes de estado de funcionamento, selecionando **estado de funcionamento** ícone no canto superior esquerdo da página para ver quais componentes estão em mau estado de funcionamento. Também pode ver os alertas de estado de funcionamento da VM gerados por um componente de mau estado de funcionamento categorizado por gravidade do alerta.

Do **lista de VMS** ver, selecione o nome de uma VM para abrir o **estado de funcionamento** página para essa VM, da mesma forma como se tiver selecionado **Insights (pré-visualização)** da VM diretamente.

![Informações VM de uma máquina virtual do Azure selecionada](./media/vminsights-health/vminsights-directvm-health.png)

O **Insights (pré-visualização)** página mostra um rollup de estado de funcionamento da VM e alertas. Este estado de funcionamento é categorizado por gravidade, que representa os alertas do Estado de funcionamento VM gerados quando o estado de funcionamento alterado de bom estado de funcionamento para mau estado de funcionamento, com base nos critérios. Selecionando **VMs em condição crítica** abre uma página com uma lista de uma ou mais VMs num Estado de funcionamento crítico.

Selecionar o estado de funcionamento para um dos mostra a VMs a **diagnóstico de estado de funcionamento** vista da VM. Nesta vista, pode determinar quais critérios de estado de funcionamento está refletindo um problema de estado de funcionamento. Quando o **diagnóstico de estado de funcionamento** é aberta a página, ele mostra todos os componentes VM e os respetivos critérios de estado de funcionamento associado com o estado de funcionamento atual.

Para obter mais informações, consulte [diagnóstico de estado de funcionamento](#health-diagnostics).

Selecionando **ver todos os critérios de estado de funcionamento** abre uma página que mostra uma lista de todos os critérios de estado de funcionamento disponíveis com esta funcionalidade. As informações podem ser mais filtradas com base nas seguintes opções:

* **tipo de**. Existem três tipos de critérios de estado de funcionamento para avaliar condições e agregar o estado de funcionamento geral de uma VM monitorizada:
    - **Unit**. Medem algum aspeto de uma VM. Este tipo de critérios de estado de funcionamento pode ser verificar um contador de desempenho para determinar o desempenho do componente, executar um script para executar uma transação sintética, ou ao ver um evento que indica um erro. O filtro é definido como unidade, por padrão.
    - **Dependência**. Fornece um rollup de estado de funcionamento entre diferentes entidades. Estes critérios de estado de funcionamento permite que o estado de funcionamento de uma entidade a depender do Estado de funcionamento de outro tipo de entidade que ele depende para operação concluída com êxito.
    - **Agregação**. Fornece um Estado de funcionamento combinado de critérios de estado de funcionamento semelhante. Critério de estado de funcionamento de unidades e de dependência são normalmente configurados sob um critério de agregação do Estado de funcionamento. Além de fornecer uma melhor organização geral dos muitos critérios de estado de funcionamento de diferentes destinados a uma entidade, o critério de agregação do Estado de funcionamento fornece um Estado de funcionamento exclusivo para as categorias distintas das entidades.

* **Categoria**. O tipo de critérios de estado de funcionamento utilizados para agrupar critérios semelhante para fins de relatórios. Essas categorias são **disponibilidade** e **desempenho**.

Para ver quais as instâncias estão em mau estado de funcionamento, selecione um valor sob a **mau estado de funcionamento do componente** coluna. Nesta página, uma tabela lista os componentes que estão num Estado de funcionamento crítico.

## <a name="health-diagnostics"></a>Diagnóstico de estado de funcionamento

O **diagnóstico de estado de funcionamento** página permite-lhe visualizar o modelo de estado de funcionamento de uma VM. Esta página apresenta uma lista de todos os componentes VM, critérios de estado de funcionamento associado, alterações de estado e outros problemas importantes identificados por componentes monitorizados relacionados à VM.

![Exemplo de página de diagnóstico de estado de funcionamento para uma VM](./media/vminsights-health/health-diagnostics-page-01.png)

Inicie o diagnóstico de estado de funcionamento através dos seguintes métodos:

* Por Estado de funcionamento de rollup para todas as VMs a partir da perspetiva VM de agregação no Azure Monitor:

    1. Sobre o **estado de funcionamento** página, selecione o ícone de **crítico**, **aviso**, **bom estado de funcionamento**, ou **desconhecido** Estado de funcionamento na seção **estado de funcionamento da VM do convidado**.
    2. Vá para a página que apresenta uma lista de todas as VMs correspondentes essa categoria filtrada.
    3. Selecione o valor na **estado de funcionamento** coluna para abrir o diagnóstico de estado de funcionamento de âmbito para essa VM.

* Por SO a partir da perspetiva VM de agregação no Azure Monitor. Sob **distribuição de VM**, selecionar qualquer um dos valores da coluna irá abrir o **máquinas virtuais** página e retornar uma lista na tabela que correspondem a categoria filtrada. Selecionar o valor sob **estado de funcionamento** coluna abre o diagnóstico de estado de funcionamento para a VM selecionada.
 
* Da VM no Monitor do Azure para VMs do convidado **estado de funcionamento** separador, ao selecionar **ver diagnóstico de estado de funcionamento**.

Diagnóstico de estado de funcionamento organiza informações de estado de funcionamento em duas categorias: disponibilidade e desempenho.
 
É possível ver todos os critérios de estado de funcionamento definidos para um componente, como o disco lógico, CPU e assim por diante, sem filtragem em duas categorias. Estas vistas podem ser numa vista completo de critérios, ou por meio de filtrar os resultados por qualquer um dos categoria Quando seleciona **disponibilidade** ou **desempenho**.

Além disso, a categoria de critérios pode ser vista junto a **critérios de estado de funcionamento** coluna. Se os critérios não corresponde à categoria selecionada, uma mensagem a indicar **sem critérios de estado de funcionamento disponíveis para a categoria selecionada** é apresentado na **critérios de estado de funcionamento** coluna.

O estado de um critério de estado de funcionamento é definido por um dos quatro tipos: **Crítico**, **aviso**, **bom estado de funcionamento**, e **desconhecido**. Os três primeiros são configuráveis, o que significa que pode modificar os valores dos limiares de monitores diretamente no **critérios de estado de funcionamento** painel de configuração. Isso também é possível ao utilizar a API de REST do Azure Monitor [monitor a operação de atualização](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update). **Desconhecido** não é configurável e está reservado para cenários específicos.

O **diagnóstico de estado de funcionamento** página tem três seções principais:

* Modelo de componente
* Critérios de Estado de Funcionamento
* Alterações de estado

![Secções da página de diagnóstico de estado de funcionamento](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Modelo de componente

A coluna mais à esquerda na **diagnóstico de estado de funcionamento** página é **modelo de componente**. Todos os componentes que estão associados a VM, são apresentados nesta coluna, juntamente com o respetivo estado de funcionamento atual.

No exemplo a seguir, são os componentes detetados **disco**, **disco lógico**, **processador**, **memória**, e  **Sistema operativo**. Várias instâncias desses componentes são detetadas e apresentadas nesta coluna.

Por exemplo, a figura seguinte mostra que a VM tem duas instâncias de discos lógicos, **c:** e **D:** , que estão em bom estado de funcionamento:

![Modelo de componente de exemplo apresentado no diagnóstico de estado de funcionamento](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Critérios de estado de funcionamento

A coluna centro na página de diagnóstico de estado de funcionamento é **critérios de estado de funcionamento**. O modelo de estado de funcionamento definido para a VM é apresentado numa árvore hierárquica. O modelo de estado de funcionamento para uma VM é composta por unidade e os critérios de estado de funcionamento agregado.

![Critérios de estado de funcionamento de exemplo apresentados no diagnóstico de estado de funcionamento](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Um critério de estado de funcionamento mede o estado de funcionamento de uma instância monitorizado, o que poderia ser um valor de limite, estado de uma entidade e assim por diante. Um critério de estado de funcionamento tem duas ou três limiares de estado de funcionamento configuráveis, conforme descrito anteriormente. Em qualquer momento, o critério de estado de funcionamento pode estar em apenas um dos seus Estados potenciais.

O modelo de estado de funcionamento define os critérios que determinam o estado de funcionamento do destino geral e componentes do destino. A hierarquia de critérios é apresentada na **critérios de estado de funcionamento** secção sobre o **diagnóstico de estado de funcionamento** página.

A política de rollup de estado de funcionamento faz parte da configuração dos critérios de estado de funcionamento agregado (a predefinição está definida como **pior de**). Pode encontrar um conjunto predefinido de critérios de estado de funcionamento em execução como parte desse recurso na [detalhes de configuração da monitorização](#monitoring-configuration-details) seção deste artigo.

Também pode utilizar a API de REST do Azure Monitor [lista de instâncias do monitor pelo recurso](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) para obter uma lista de todos os critérios de estado de funcionamento. Estes critérios inclui detalhes de configuração em execução contra o recurso de VM do Azure.

O **unidade** tipo de critérios de estado de funcionamento pode ter a respetiva configuração modificada ao selecionar a ligação do botão de reticências para o lado direito. Selecione **Mostrar detalhes** para abrir o painel de configuração.

![Configurar um exemplo de critérios de estado de funcionamento](./media/vminsights-health/health-diagnostics-vm-example-02.png)

No painel de configuração para os critérios de estado de funcionamento selecionado, se utilizar o exemplo **média disco segundos por escrever**, o limite pode ser configurado com um valor numérico diferente. É um monitor de dois Estados, que significa que ele pode alterar apenas a partir **bom estado de funcionamento** ao **aviso**.

Outros critérios de estado de funcionamento às vezes usam os três Estados, onde pode configurar o valor de limiares de estado de funcionamento de aviso e críticos. Também pode modificar um limiar com API de REST do Azure Monitor [configuração do monitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update).

>[!NOTE]
>Aplicar alterações de configuração de critérios de estado de funcionamento para uma instância aplica-as para todas as instâncias monitorizadas. Por exemplo, se selecionar **d: de-1 do disco** e, em seguida, modifique o **média disco segundos por escrever** limiar, a alteração aplica-se a todas as instâncias detetadas e monitorizadas na VM.


![Configurar um critério de estado de funcionamento de um exemplo de monitor de unidade](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Se quiser obter mais informações sobre critérios de estado de funcionamento, incluímos artigos de conhecimento para ajudar a identificar problemas, causas e resoluções. Selecione **ver informações** na página para ver o artigo de conhecimento relacionados.

Para rever todos os artigos de conhecimento incluídos com o Azure Monitor para o estado de funcionamento de VMs, veja [documentação de dados de conhecimento do Estado de funcionamento do Azure Monitor](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).

### <a name="state-changes"></a>Alterações de estado

A coluna da direita do **diagnóstico de estado de funcionamento** página é **alterações de estado**. Esta coluna apresenta uma lista de todas as alterações de estado associadas com os critérios de estado de funcionamento selecionados no **critérios de estado de funcionamento** secção ou a alteração do Estado da VM se uma VM tiver sido selecionada a partir do **modelo de componente** ou  **Critérios de estado de funcionamento** coluna da tabela.

![Alterações de estado de exemplo apresentadas no diagnóstico de estado de funcionamento](./media/vminsights-health/health-diagnostics-page-statechanges.png)

A seção a seguir mostra o estado de critérios de estado de funcionamento e o tempo associado. Estas informações mostram o estado mais recente na parte superior da coluna.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Associação de colunas do modelo de componente, critérios de estado de funcionamento e as alterações de estado

As três colunas são interligadas entre si. Quando seleciona uma instância na **modelo de componente** coluna, o **critérios de estado de funcionamento** coluna é filtrada para essa exibição de componente. Do mesmo modo, o **as alterações de estado** coluna é atualizada com base nos critérios de estado de funcionamento selecionado.

![Exemplo de seleção de instância monitorizada e os resultados](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Por exemplo, se selecionar *disco - d 1:* na lista em **modelo de componente**, **critérios de estado de funcionamento** filtra *disco - 1 D:* e o  **Alterações de estado** mostra a alteração de estado com base na disponibilidade dos *disco - d 1:* .

Para ver um Estado de funcionamento atualizado, pode atualizar a página de diagnóstico de estado de funcionamento, selecionando o **atualizar** ligação. Se existir uma atualização do Estado de funcionamento do critério de estado de funcionamento com base no intervalo de consulta predefinido, essa tarefa permite-lhe evitar espera e reflete o estado de funcionamento mais recente. O **estado de funcionamento de critérios** é um filtro que permite-lhe analisar os resultados com base no estado do Estado de funcionamento selecionado: Bom estado de funcionamento, aviso, crítico, desconhecido e todos os. O **última atualização** tempo no canto superior direito representa a última vez que a página de diagnóstico de estado de funcionamento foi atualizada.

## <a name="alerts"></a>Alertas

Monitor do Azure para VMs do Estado de funcionamento integra [alertas do Azure](../../azure-monitor/platform/alerts-overview.md). Gera um alerta quando critérios predefinidos, quando detectado, é alterado de bom estado de funcionamento para um mau estado de funcionamento. Alertas são categorizados por gravidade, de gravidade 0 a 4 de gravidade, com gravidade 0 como o nível mais elevado.

Alertas não estão associados a um grupo de ação para ser notificado quando o alerta foi acionado. O proprietário da subscrição tem de configurar notificações ao seguir os passos a [configurar alertas](#configure-alerts) secção.

O número total de alertas de estado de funcionamento da VM categorizados por gravidade está disponível na **estado de funcionamento** dashboard na **alertas** secção. Ao selecionar o número total de alertas ou o número correspondente a um nível de gravidade, o **alertas** página abre e apresenta uma lista de todos os alertas que correspondam a sua seleção.

Por exemplo, se selecionar a linha correspondente **nível de gravidade 1**, verá a vista seguinte:

![Exemplo de todos os alertas de nível de gravidade 1](./media/vminsights-health/vminsights-sev1-alerts-01.png)

O **todos os alertas** página não está no âmbito para mostrar apenas os alertas que correspondam a sua seleção. Também é filtrado por **tipo de recurso** para mostrar apenas os alertas de estado de funcionamento gerados por um recurso VM. Este formato é refletido na lista de alerta, abaixo da coluna **recurso de destino**, que indica a VM do Azure o alerta registado quando uma condição de mau estado de funcionamento foi cumprida.

Alertas a partir de outros tipos de recursos ou serviços não se destinam a serem incluídos nesta vista. Estes alertas incluem alertas de registo, que se baseiam em consultas de registo ou alertas de métricas que normalmente veria do padrão do Azure Monitor [todos os alertas](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) página.

Pode filtrar esta vista ao selecionar os valores nos menus de lista pendente na parte superior da página.

|Coluna |Descrição |
|-------|------------|
|Subscrição |Selecione uma subscrição do Azure. Apenas os alertas na subscrição selecionada são incluídos na vista. |
|Grupo de Recursos |Selecione um grupo de recursos. Apenas os alertas com destinos no grupo de recursos selecionado estão incluídos na vista. |
|Tipo de recurso |Selecione um ou mais tipos de recursos. Por predefinição, apenas os alertas de destino **máquinas virtuais** é selecionado e incluído nesta vista. Esta coluna só está disponível depois de um grupo de recursos foi especificado. |
|Resource |Selecione um recurso. Apenas alertas com esse recurso como um destino estão incluídas na vista. Esta coluna está disponível somente depois que foi especificado um tipo de recurso. |
|Gravidade |Selecione uma gravidade de alerta ou selecione **todos os** para incluir alertas de todas as gravidades. |
|Condição do monitor |Selecione uma condição de monitor a alertas de filtro, se tiver sido acionados ou resolvidos pelo sistema, se a condição já não estiver ativa. Em alternativa, selecione **todos os** para incluir alertas de todas as condições. |
|Estado de alerta |Selecione um Estado de alerta **New**, **confirmação**, **fechado**, ou **todos os** para incluir alertas de todos os Estados. |
|Monitorizar serviço |Selecione um serviço ou selecione **todos os** para incluir todos os serviços. Apenas os alertas do Insights de VM são suportados para esta funcionalidade.|
|Intervalo de tempo| Apenas os alertas acionados dentro da janela de tempo selecionado estão incluídos na vista. Os valores suportados são a última hora, últimas 24 horas, últimos 7 dias e os últimos 30 dias. |

Quando seleciona um alerta, o **detalhes do alerta** é apresentada a página. Esta página fornece os detalhes do alerta e permite-lhe alterar o seu estado.

Para saber mais sobre como gerir alertas, veja [criar, ver e gerir alertas ao utilizar o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

>[!NOTE]
>Atualmente não é suportada a criação de novos alertas com base nos critérios de estado de funcionamento ou modificação estado de funcionamento regras de alerta existentes no Azure Monitor no portal.


![Painel de detalhes do alerta para um alerta selecionado](./media/vminsights-health/alert-details-pane-01.png)

Pode alterar um Estado de alerta para alertas de um ou vários selecionando-os e, em seguida, selecionando **alterar o estado** partir do **todos os alertas** página no canto superior esquerdo. Selecione um dos Estados no **alterar estado de alerta** painel, adicione uma descrição da alteração no **comentário** campo e, em seguida, selecione **Ok** para consolidar as alterações. Quando as informações são verificadas e as alterações são aplicadas, acompanhar o progresso em **notificações** no menu.

### <a name="configure-alerts"></a>Configurar alertas
Não é possível gerir determinadas tarefas de gestão de alertas do portal do Azure. Estas tarefas devem ser efetuadas utilizando o [API de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Especificamente:

- Ativar ou desativar um alerta para os critérios de estado de funcionamento
- Configurar notificações de alertas de critérios de estado de funcionamento

Cada exemplo utiliza [ARMClient](https://github.com/projectkudu/armclient) no seu computador Windows. Se não estiver familiarizado com esse método, consulte [ARMClient usando](../platform/rest-api-walkthrough.md#use-armclient).

#### <a name="enable-or-disable-an-alert-rule"></a>Ativar ou desativar uma regra de alerta

Para ativar ou desativar um alerta para os critérios de estado de funcionamento específico, a propriedade **alertGeneration** devem ser modificados com o valor **desativada** ou **ativado**.

Para identificar os *monitorId* para os critérios de estado de funcionamento específico, o exemplo seguinte mostra como consultar esse valor para os critérios **LogicalDisk\Avg disco segundos por transferência**:

1. Na janela do terminal, escreva **armclient.exe início de sessão**. Se o fizer, pede-lhe para iniciar sessão no Azure.

2. Introduza o seguinte comando para obter o critério de estado de funcionamento ativo numa VM específica e identificar o valor para *monitorId* propriedade:

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    O exemplo seguinte mostra a saída do *armclient GET* comando. Tome nota do valor de *MonitorId*. Este valor é necessário para a próxima etapa, onde podemos tem de especificar o ID dos critérios de estado de funcionamento e modificar sua propriedade para criar um alerta.

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

3. Introduza o comando seguinte para modificar a *alertGeneration* propriedade:

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Introduza o comando GET utilizado no passo 2 para verificar se o valor da propriedade está definido **desativado**.

#### <a name="associate-an-action-group-with-health-criteria"></a>Associar um grupo de ação de critérios de estado de funcionamento

O Azure Monitor de estado de funcionamento de VMs suporta notificações de e-mail e SMS quando são gerados alertas de critérios de estado de funcionamento mau estado de funcionamento. Para configurar notificações, tome nota do nome do grupo de ação configurada para enviar notificações de e-mail ou SMS.

>[!NOTE]
>Esta ação deve ser executada em relação a cada VM monitorizada que pretende receber uma notificação para. Não se aplica a todas as VMs num grupo de recursos.

1. Na janela do terminal, introduza *armclient.exe início de sessão*. Se o fizer, pede-lhe para iniciar sessão no Azure.

2. Introduza o seguinte comando para associar um grupo de ação com regras de alerta:
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Para verificar se o valor da propriedade **actionGroupResourceIds** com êxito a foi atualizado, introduza o seguinte comando:

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    O resultado deverá ser semelhante os seguintes critérios:
    
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

- Para identificar as limitações e o desempenho geral de VM, consulte [o desempenho da VM do Azure de modo de exibição](vminsights-performance.md).
- Para saber mais sobre as dependências das aplicações detetadas, consulte [vista do Azure Monitor para o mapa de VMs](vminsights-maps.md).
