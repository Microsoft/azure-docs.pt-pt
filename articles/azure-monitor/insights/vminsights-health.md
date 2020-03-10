---
title: Compreenda a saúde das suas máquinas virtuais Azure  Microsoft Docs
description: Este artigo descreve como compreender a saúde das máquinas virtuais e dos sistemas operativos subjacentes com o Monitor Azure para VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 3cecb04a4f299051860c45425f0fc4e13c3722ea
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395780"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Compreenda a saúde das suas máquinas virtuais Azure

O Azure inclui serviços para funções específicas ou tarefas no espaço de monitorização, mas não fornece perspetivas aprofundadas de saúde dos sistemas operativos (OSs) alojados em máquinas virtuais Azure (VMs). Embora possa utilizar o Monitor Azure para diferentes condições, não foi concebido para modelar e representar a saúde dos componentes principais, ou a saúde geral dos VMs.

Ao utilizar o Monitor Azure para a saúde dos VMs, pode monitorizar ativamente a disponibilidade e desempenho de um Os hóspede do Windows ou do Linux. A função de saúde utiliza um modelo que representa componentes-chave e suas relações, fornece critérios que especificam como medir a saúde dos componentes, e envia um alerta quando deteta uma condição pouco saudável.

Ver o estado de saúde geral de um VM Azure e o SISTEMA subjacente podem ser observados a partir de duas perspetivas: diretamente de um VM, ou em todos os VMs de um grupo de recursos do Azure Monitor.

Este artigo mostra como avaliar, investigar e resolver rapidamente problemas de saúde quando são detetados pelo Monitor Azure para a funcionalidade de saúde dos VMs.

Para obter informações sobre a configuração do Monitor Azure para VMs, consulte [o Monitor Enable Azure para VMs](vminsights-enable-overview.md).

>[!NOTE]
>Anunciámos recentemente [alterações](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) à funcionalidade Saúde com base no feedback que recebemos dos nossos clientes de pré-visualização pública. Dado o número de alterações que vamos fazer, vamos deixar de oferecer a funcionalidade Saúde a novos clientes. Os clientes existentes podem continuar a utilizar a funcionalidade de saúde. Para mais detalhes, consulte as nossas [FAQ](vminsights-ga-release-faq.md)de Disponibilidade Geral . 

## <a name="monitoring-configuration-details"></a>Monitorizar detalhes de configuração

Esta secção descreve os critérios de saúde padrão para monitorizar os VMs Azure Windows e Linux. Todos os critérios de saúde são pré-configurados para enviar um alerta quando detetam uma condição pouco saudável.

| Monitorizar o nome | Frequência (min) | Duração do retrocesso (min) | Operador | Limiar | Alerta sobre o estado | Gravidade | Categoria de carga de trabalho | 
|--------------|-----------|----------|----------|-----------|----------------|----------|-------------------|
| Disco Lógico Online | 5 | 15 | <> | 1 (verdadeiro) | Crítica | Sev1 | Linux | 
| Espaço Livre de Disco Lógico | 5 | 15 | < | 200 MB (aviso)<br> 100 MB (crítico) | Aviso | Sev1<br> Sev2 | Linux | 
| % de Inodes Livres de Disco Lógico | 5 | 15 | < | 5% | Crítica | Sev1 | Linux | 
| % de Espaço Livre de Disco Lógico | 5 | 15 | < | 5% | Crítica | Sev1 | Linux | 
| Estado do adaptador de rede | 5 | 15 | <> | 1 (verdadeiro) | Aviso | Sev2 | Linux | 
| Memória em Megabytes Disponíveis do Sistema Operativo | 5 | 10 | < | 2,5 MB | Crítica | Sev1 | Linux | 
| Disk Avg. Disk sec/Read | 5 | 25 | > | 0,05s | Crítica | Sev1 | Linux | 
| Disk Avg. Disk sec/Transfer | 5 | 25 | > | 0,05s | Crítica | Sev1 | Linux | 
| Disk Avg. Disk sec/Write | 5 | 25 | > | 0,05s | Crítica | Sev1 | Linux | 
| Estado do disco | 5 | 25 | <> | 1 (verdadeiro) | Crítica | Sev1 | Linux | 
| Percentagem Total de Tempo do Processador do Sistema Operativo | 5 | 10 | >= | 95% | Crítica | Sev1 | Linux | 
| Percentagem total de utilização do CPU | 5 | 10 | >= | 95% | Crítica | Sev1 | Windows | 
| Erro do sistema de ficheiros ou corrupção | 60 | 60 | <> | 4 | Crítica | Sev1 | Windows | 
| Segundos de disco lógico médio por leitura | 1 | 15 | > | 0.04s | Aviso | Sev2 | Windows | 
| Segundos lógicos médios do disco por transferência | 1 | 15 | > | 0.04s | Aviso | Sev2 | Windows | 
| Segundos lógicos médios do disco por escrita (disco lógico) | 1 | 15 | > | 0.04s | Aviso | Sev2 | Windows | 
| Comprimento da fila do disco atual (disco lógico) | 5 | 60 | >= | 32 | Aviso | Sev2 | Windows | 
| Espaço Livre de Disco Lógico (MB) | 15 | 60 | > | Aviso de 500 MB<br> 300 MB crítico | Crítica | Sev1<br> Sev2 | Windows | 
| Espaço Sem Disco Lógico (%) | 15 | 60 | > | 10% de aviso<br> 5% crítico | Crítica | Sev1<br> Sev2 | Windows |
| Tempo lógico de disqueia por cento inativo | 15 | 360 | <= | 20% | Aviso | Sev2 | Windows | 
| Por cento de largura de banda leitura usada | 5 | 60 | >= | 60% | Aviso | Sev2 | Windows | 
| Por cento de largura de banda usada total | 5 | 60 | >= | 75% | Aviso | Sev2 | Windows | 
| Por cento de largura de banda escrita usada | 5 | 60 | >= | 60% | Aviso | Sev2 | Windows | 
| DHCP Atendimento ao Cliente Saúde | 5 | 12 | <> | 4 (em execução) | Crítica | Sev1 | Windows | 
| Saúde do Serviço ao Cliente do DNS | 5 | 12 | <> | 4 (em execução) | Crítica | Sev1 | Windows | 
| Saúde do serviço de log de eventos do windows | 5 | 12 | <> | 4 (em execução) | Crítica | Sev1 | Windows | 
| Saúde do serviço de firewall do Windows Firewall | 5 | 12 | <> | 4 (em execução) | Crítica | Sev1 | Windows | 
| Saúde do Serviço RPC | 5 | 12 | <> | 4 (em execução) | Crítica | Sev1 | Windows | 
| Saúde do Serviço de Servidores | 5 | 12 | <> | 4 (em execução) | Crítica | Sev1 | Windows | 
| Saúde do Serviço de Gestão Remota do Windows | 5 | 12 | <> | 4 (em execução) | Crítica | Sev1 | Windows | 
| Megabytes disponíveis de memória | 5 | 10 | < | 100 MB | Crítica | Sev1 | Windows | 
| Entradas de tabela de página de sistema gratuito | 5 | 10 | <= | 5000 | Crítica | Sev1 | Windows | 
| Páginas de memória por segundo | 5 | 10 | >= | 5000/s | Aviso | Sev1 | Windows | 
| Percentagem de Memória Comprometida em Uso | 5 | 10 | > | 80% | Crítica | Sev1 | Windows | 
| Segundos médios do disco por transferência | 1 | 15 | > | 0.04s | Aviso | Sev2 | Windows | 
| Segundos médios do disco por escrita | 1 | 15 | > | 0.04s | Aviso | Sev2 | Windows | 
| Comprimento da fila do disco atual | 5 | 60 | >= | 32 | Aviso | Sev2 | Windows | 
| Tempo de marcha lenta por cento do disco | 5 | 60 | >= | 20% | Aviso | Sev2 | Windows | 

>[!NOTE]
>A duração do retrocesso representa a frequência com que a janela de olhar para trás verifica os valores métricos, como nos últimos cinco minutos.  

>[!NOTE]
>A frequência representa a frequência com que o alerta métrico verifica se as condições são satisfeitas, como a cada minuto.  É a taxa a que o critério de saúde é executado, e o retrocesso é a duração sobre a qual o critério de saúde é avaliado. Por exemplo, o critério de saúde está a avaliar se a utilização do **CPU** da condição é superior a 95 por cento com uma frequência de 5 minutos e permanece superior a 95% durante 15 minutos (3 ciclos de avaliação consecutivos), então o Estado é atualizado para a gravidade crítica se já não fosse.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Para entrar, vá ao [portal Azure.](https://portal.azure.com)

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Introdução ao Monitor Azure para a saúde dos VMs

Antes de utilizar a funcionalidade de saúde para um único VM ou grupo de VMs, é importante entender como a informação é apresentada e o que as visualizações representam.

### <a name="view-health-directly-from-a-vm"></a>Ver saúde diretamente de um VM

Para ver a saúde de um VM Azure, selecione **Insights (pré-visualização)** no painel esquerdo do VM. Na página de insights vM, o separador **Saúde** é aberto por padrão e mostra a visão de saúde do VM.

![Monitor Azure para visão geral da saúde dos VMs de uma máquina virtual Azure selecionada](./media/vminsights-health/vminsights-directvm-health-01.png)

Na secção de **saúde VM convidada,** a tabela mostra o rolo de saúde dos componentes de desempenho monitorizados por critérios de saúde para o VM, e o número total de alertas de saúde VM levantados por componentes pouco saudáveis. Estes componentes incluem **CPU,** **Memória,** **Disco**e **Rede.** Expanda o chevron ao lado da saúde Guest VM para ver a saúde dos seus componentes.

![Monitor Azure para o estado de saúde dos componentes vMs de uma máquina virtual Azure selecionada](./media/vminsights-health/vminsights-directvm-health-02.png)

A seleção do estado junto ao componente abrirá a experiência de Diagnóstico de Saúde no contexto da componente selecionada. Mostra a composição do estado dessa componente, descrevendo quais os critérios de saúde usados para calcular a sua saúde. Para mais informações, consulte diagnósticos de [saúde e trabalhando com critérios de saúde.](#health-diagnostics) Para mais informações sobre alertas, consulte [Alertas](#alerts).

Os estados de saúde definidos para um VM são descritos na tabela seguinte:

|Ícone |Estado de saúde |Significado |
|-----|-------------|---------------|
| |Bom estado de funcionamento |O VM está dentro das condições de saúde definidas. Este estado indica que não há problemas detetados e o VM está a funcionar normalmente. Com um monitor de rollup dos pais, a saúde aparece e reflete o melhor ou pior estado da criança.|
| |Crítica |O Estado não está dentro da condição de saúde definida, indicando que foram detetadas uma ou mais questões críticas. Estas questões devem ser abordadas para restabelecer a funcionalidade normal. Com um monitor de rollup dos pais, o estado de saúde aparece e reflete o melhor ou pior estado da criança.|
| |Aviso |O estado está entre dois limiares para a condição de saúde definida, onde um indica um estado de alerta e o outro indica um estado crítico (três limiares de estado de saúde podem ser configurados), ou quando uma questão não crítica pode causar problemas críticos se não resolvido. Com um monitor de rollup dos pais, se uma ou mais crianças estiverem em estado de alerta, o progenitor refletirá um estado de alerta. Se uma criança estiver em estado crítico e outra criança em estado de alerta, o rolo de pais mostrará o estado de saúde como crítico.|
| |Desconhecido |O Estado não pode ser calculado por várias razões. A secção seguinte fornece detalhes adicionais e possíveis soluções. |

Um estado de saúde desconhecido pode ser causado pelas seguintes questões:

- O agente foi reconfigurado e já não reporta ao espaço de trabalho especificado quando o Monitor Azure para VMs foi ativado. Para configurar o agente para reportar ao espaço de trabalho ver, [adicionar ou remover um espaço](../platform/agent-manage.md#adding-or-removing-a-workspace)de trabalho .
- O VM foi apagado.
- O espaço de trabalho associado ao Monitor Azure para VMs foi eliminado. Pode recuperar o espaço de trabalho se tiver benefícios de suporte premier. Vá ao [Primeiro-Ministro](https://premier.microsoft.com/) e abra um pedido de apoio.
- As dependências da solução foram eliminadas. Para reativar as soluções ServiceMap e InfrastructureInsights no seu espaço de trabalho Log Analytics, reinstale a solução ServiceMap utilizando o modelo do Gestor de [Recursos Azure](vminsights-enable-at-scale-powershell.md#install-the-servicemap-solution). Para reinstalar a solução InfastructureInsights, o email vminsights@microsoft.com. 
- O VM foi desligado.
- O serviço Azure VM não está disponível ou a manutenção está a ser realizada.
- Os [dados diários](../platform/manage-cost-storage.md) do espaço de trabalho ou o limite de retenção foram cumpridos.

Selecione **Ver diagnósticos** de saúde para abrir uma página mostrando todos os componentes de um VM, critérios de saúde associados, alterações do estado e outros problemas detetados pela monitorização de componentes relacionados com o VM.

Para mais informações, consulte diagnósticos de [saúde.](#health-diagnostics)

Na secção **Saúde,** uma tabela mostra o rolo de saúde dos componentes de desempenho monitorizados por critérios de saúde. Estes componentes incluem **CPU,** **Memória,** **Disco**e **Rede.** A seleção de um componente abre uma página que lista todos os critérios de monitorização e o estado de saúde desse componente.

Quando você acede à saúde de um VM Azure que gere o Windows, o estado de saúde dos cinco principais serviços principais do Windows é mostrado sob a saúde dos **serviços Core**. A seleção de qualquer um dos serviços abre uma página que lista os critérios de saúde de monitorização dessa componente juntamente com o seu estado de saúde.

A seleção do nome dos critérios de saúde abre o painel de propriedades. Neste painel, pode rever os detalhes da configuração, incluindo se os critérios de saúde tiverem um alerta de Monitor Azure correspondente.

Para mais informações, consulte diagnósticos de [saúde e trabalhando com critérios de saúde.](#health-diagnostics)

### <a name="aggregate-vm-perspective"></a>Perspetiva VM agregada

Para ver a recolha de saúde para todos os seus VMs num grupo de recursos, selecione **O Monitor Azure** da lista de navegação no portal e, em seguida, selecione **Virtual Machines (pré-visualização)** .

![Visão de monitorização VM Insights do Monitor Azure](./media/vminsights-health/vminsights-aggregate-health.png)

Nas listas de abandono do Grupo **de Subscrição** e **Recursos,** selecione o grupo de recursos adequado que inclui os VMs relacionados com o grupo, para ver o seu estado de saúde relatado. A sua seleção aplica-se apenas à funcionalidade de saúde e não passa para os separadores **Performance** ou **Map.**

O separador **Saúde** fornece as seguintes informações:

* Quantas VMs estão em estado crítico ou pouco saudável, contra quantas são saudáveis ou não estão a enviar dados (referidos como um estado desconhecido).
* Que e quantos VMs da OS estão a relatar um estado pouco saudável.
* Quantos VMs não são saudáveis devido a um problema detetado com um processador, disco, memória ou adaptador de rede, categorizado pelo estado de saúde.
* Quantos VMs não são saudáveis devido a um problema detetado com um serviço de SO central, categorizado pelo estado de saúde.

No separador **Saúde,** pode identificar as questões críticas detetadas pelos critérios de saúde que monitorizam o VM, e rever detalhes de alerta e artigos de conhecimento associados. Estes artigos podem ajudar no diagnóstico e na remediação de questões. Selecione qualquer uma das severidades para abrir a página [All Alerts](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) filtrada por essa gravidade.

A **distribuição vm por** lista de sistemaoperativo mostra VMs listados pela edição do Windows ou distribuição linux, juntamente com a sua versão. Em cada categoria de SM, os VMs são discriminados ainda mais com base na saúde do VM.

![Perspetiva de distribuição virtual de máquinas VM Insights](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Selecione qualquer coluna que **inclua contagem vm**, **Critical**, **Warning**, **Healthy**, ou **Unknown**. Ver a lista de resultados filtrados na página **Máquinas Virtuais** que correspondem à coluna selecionada.

Por exemplo, para rever todos os VMs que executam o lançamento do Red Hat Enterprise Linux 7.5, selecione o valor da contagem de **VM** para esse SISTEMA, e listará os VMs correspondentes a esse filtro e ao seu estado de saúde atual.

![Lançamento de exemplo de VMs de Red Hat Linux](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

Clique na caixa de verificação **Show Health** e o estado de saúde é devolvido para os resultados filtrados na tabela.  

![Exemplo estado de saúde de VMs de Chapéu Vermelho](./media/vminsights-health/vminsights-rollup-vm-rehl-02.png)

Para qualquer um dos itens da lista, pode clicar no estado de saúde correspondente para lançar Diagnósticos de Saúde, o que mostra como a saúde é avaliada para o VM selecionado. 

Na página **Máquinas Virtuais,** se selecionar o nome de um VM sob o **nome VM**da coluna, é direcionado para a página **de instância VM.** Esta página fornece mais detalhes sobre os problemas de alertas e critérios de saúde que estão a afetar o VM selecionado. Filtre os detalhes do estado de saúde selecionando o ícone **do Estado de Saúde** no canto superior esquerdo da página para ver quais os componentes que não são saudáveis. Também pode ver alertas de Saúde VM levantados por um componente pouco saudável categorizado pela gravidade do alerta.

Na vista da **lista VM,** selecione o nome de um VM para abrir a página **Health** para esse VM, da mesma forma que selecionou **Insights (pré-visualização)** diretamente do VM.

![VM insights de uma máquina virtual Azure selecionada](./media/vminsights-health/vminsights-directvm-health.png)

As **Máquinas Virtuais (pré-visualização) na** página Do Monitor Azure mostram um estado de saúde nominal para o VM e alertas. Este estado de saúde é categorizado pela gravidade, que representa alertas de saúde VM levantados quando o estado de saúde passou de saudável para pouco saudável, com base em critérios. A seleção de **VMs em estado crítico** abre uma página com uma lista de um ou mais VMs em estado de saúde crítico.

A seleção do estado de saúde de um dos VMs mostra a visão de **Diagnóstico de Saúde** do VM. Nesta perspetiva, pode determinar quais os critérios de saúde que refletem uma questão de estado de saúde. Quando a página de Diagnósticos de **Saúde** abre, mostra todos os componentes VM e os seus critérios de saúde associados ao estado de saúde atual.

Para mais informações, consulte diagnósticos de [saúde.](#health-diagnostics)

Selecionando **Ver todos os critérios de saúde** abre uma página que mostra uma lista de todos os critérios de saúde disponíveis com esta funcionalidade. A informação pode ser filtrada com base nas seguintes opções:

* **Tipo**. Existem três tipos de critérios de saúde para avaliar as condições e arregaçar o estado geral de saúde de um VM monitorizado:
    - **Unidade**. Mede algum aspeto de um VM. Este tipo de critérios de saúde pode estar a verificar um contador de desempenho para determinar o desempenho do componente, executar um script para realizar uma transação sintética, ou assistir a um evento que indique um erro. O filtro está definido para a unidade por defeito.
    - **Dependência.** Proporciona um rollup de saúde entre diferentes entidades. Este critério de saúde permite que a saúde de uma entidade dependa da saúde de outro tipo de entidade em que se baseia para o funcionamento bem sucedido.
    - **Agregado**. Proporciona um estado de saúde combinado de critérios de saúde semelhantes. O critério de saúde unitário e de dependência é tipicamente configurado sob um critério de saúde agregado. Além de proporcionar uma melhor organização geral dos diferentes critérios de saúde direcionados a uma entidade, o critério de saúde agregado proporciona um estado de saúde único para categorias distintas das entidades.

* **Categoria**. O tipo de critérios de saúde utilizados para agrupar critérios semelhantes para efeitos de reporte. Estas categorias são **Disponibilidade** e **Desempenho.**

Para ver quais as instâncias que não são saudáveis, selecione um valor sob a coluna **Componente Não Saudável.** Nesta página, uma tabela lista os componentes que estão em estado crítico de saúde.

## <a name="health-diagnostics"></a>Diagnósticos de saúde

A página **de Diagnósticos** de Saúde permite visualizar o modelo de saúde de um VM. Esta página lista todos os componentes vM, critérios de saúde associados, alterações estatais e outras questões significativas identificadas por componentes monitorizados relacionados com o VM.

![Exemplo da página de Diagnósticos de Saúde para um VM](./media/vminsights-health/health-diagnostics-page-01.png)

Inicie os diagnósticos de saúde utilizando os seguintes métodos:

* Por estado de saúde rollup para todos os VMs a partir da perspetiva global de VM no Monitor Azure:

    1. Na página **Health,** selecione o ícone para **Estado**crítico , **aviso,** **saudável**ou **desconhecido** sob a secção **Saúde VM convidada**.
    2. Vá à página que lista todos os VMs correspondentes a essa categoria filtrada.
    3. Selecione o valor na coluna do Estado de **Saúde** para abrir os diagnósticos de saúde traçados para esse VM.

* Por OS a partir da perspetiva vm agregada no Monitor Azure. Sob **a distribuição vm,** selecionando qualquer um dos valores da coluna abrirá a página **Máquinas Virtuais** e devolverá uma lista na tabela correspondente à categoria filtrada. A seleção do valor sob a coluna **Do Estado de Saúde** abre diagnósticos de saúde para o VM selecionado.
 
* A partir do VM convidado no separador Azure Monitor para VMs **Health,** selecionando **Ver diagnósticos**de saúde .

O diagnóstico de saúde organiza informação de saúde em duas categorias: disponibilidade e desempenho.
 
Todos os critérios de saúde definidos para um componente, como o disco lógico, o CPU, e assim por diante, podem ser vistos sem filtragem nas duas categorias. Estas vistas podem estar numa visão geral dos critérios, ou através da filtragem dos resultados por qualquer uma das categorias quando selecionar **Disponibilidade** ou **Desempenho**.

Além disso, a categoria de critérios pode ser vista junto à coluna Critérios de **Saúde.** Se os critérios não corresponderem à categoria selecionada, uma mensagem indicando que não há critérios de **saúde disponíveis para a categoria selecionada** na coluna Critérios de **Saúde.**

O estado de um critério de saúde é definido por um de quatro tipos: **Crítico,** **Aviso,** **Saudável**e **Desconhecido.** Os três primeiros são configuráveis, o que significa que pode modificar os valores-limiar dos monitores diretamente no painel de configuração dos Critérios de **Saúde.** Isto também é possível utilizando a operação de monitorização de [atualização](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)API do Monitor Do Monitor Azure . **Desconhecido** não é configurável e está reservado para cenários específicos.

A página **de Diagnósticos de Saúde** tem três secções principais:

* Modelo de Componente
* Critérios de Saúde
* Alterações de Estado

![Página de Secções de Diagnóstico de Saúde](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Modelo de componente

A coluna mais à esquerda na página de **Diagnósticos** de Saúde é **o Modelo de Componentes**. Todos os componentes, que estão associados ao VM, são apresentados nesta coluna juntamente com o seu estado de saúde atual.

No exemplo seguinte, os componentes descobertos são **Disco,** **Disco Lógico,** **Processador,** **Memória**e **Sistema Operativo.** Várias instâncias destes componentes são descobertas e exibidas nesta coluna.

Por exemplo, a figura que se segue mostra que o VM tem dois casos de discos lógicos, **C:** e **D:** , que estão em estado saudável:

![Modelo de componente de exemplo apresentado em diagnósticos de saúde](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Critérios de saúde

A coluna central na página de Diagnósticos de Saúde é **Critérios de Saúde.** O modelo de saúde definido para o VM é exibido numa árvore hierárquica. O modelo de saúde de um VM consiste em critérios de saúde unitários e agregados.

![Critérios de saúde de exemplo apresentados em diagnósticos de saúde](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Um critério de saúde mede a saúde de uma instância monitorizada, que pode ser um valor limiar, estado de uma entidade, e assim por diante. Um critério de saúde tem dois ou três limiares de estado de saúde configuráveis, como descrito anteriormente. Em qualquer momento, o critério de saúde pode estar apenas num dos seus estados potenciais.

O modelo de saúde define critérios que determinam a saúde do objetivo geral e componentes do alvo. A hierarquia de critérios é mostrada na secção critérios de **saúde** na página de Diagnósticos de **Saúde.**

A política de rollup de saúde faz parte da configuração de critérios de saúde agregados (o padrão está definido para **o pior de**). Pode encontrar um conjunto padrão de critérios de saúde em execução como parte desta funcionalidade na secção de detalhes de configuração de [Monitorização](#monitoring-configuration-details) deste artigo.

Também pode utilizar a lista de [instâncias](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) de monitor escotomonitor Do Monitor Azure Rest por recurso para obter uma lista de todos os critérios de saúde. Estes critérios incluem detalhes de configuração em execução contra o recurso Azure VM.

O tipo de critérios de saúde **unitáriopode** ter a sua configuração modificada selecionando a ligação de elipse para o lado direito. Selecione **Detalhes do show** para abrir o painel de configuração.

![Configurar um exemplo de critérios de saúde](./media/vminsights-health/health-diagnostics-vm-example-02.png)

No painel de configuração para os critérios de saúde selecionados, se utilizar o exemplo **Average Disk Seconds Per Write,** o limiar pode ser configurado com um valor numérico diferente. É um monitor de dois estados, o que significa que só pode mudar de **Saudável** para **Aviso.**

Outros critérios de saúde por vezes usam três estados, onde você pode configurar o valor para alerta e limiares críticos de estado de saúde. Também pode modificar um limiar utilizando a [configuração](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)do monitor API do Monitor Do Monitor Azure .

>[!NOTE]
>Aplicar as alterações de configuração dos critérios de saúde numa instância aplica-as a todas as instâncias monitorizadas. Por exemplo, se selecionar **o Disco -1 D:** e depois modificar o limiar **average Seconds Per Write,** a alteração aplica-se a todas as instâncias descobertas e monitorizadas no VM.


![Configurar um critério de saúde de um exemplo de monitor de unidade](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Se quiser saber mais sobre critérios de saúde, incluímos artigos de conhecimento para ajudá-lo a identificar problemas, causas e resoluções. Selecione **Ver informações** na página para ver o artigo de conhecimento relacionado.

Para rever todos os artigos de conhecimento incluídos no Azure Monitor para a saúde dos VMs, consulte a documentação do conhecimento de [saúde do Azure Monitor.](https://docs.microsoft.com/azure/monitoring/infrastructure-health/)

### <a name="state-changes"></a>Alterações de Estado

A coluna de extrema-direita da página de Diagnósticos de **Saúde** é **alterações do Estado.** Esta coluna enumera todas as alterações do Estado associadas aos critérios de saúde selecionados na secção Critérios de **Saúde,** ou a alteração do Estado do VM se um VM foi selecionado a partir do **Modelo de Componente** ou da coluna critérios de **saúde** da tabela.

![Mudanças de estado exemplo apresentadas nos diagnósticos de saúde](./media/vminsights-health/health-diagnostics-page-statechanges.png)

A secção seguinte mostra o estado dos critérios de saúde e o tempo associado. Esta informação mostra o último estado no topo da coluna.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Associação de Modelos componentes, Critérios de Saúde e Alterações do Estado

As três colunas estão interligadas umas com as outras. Quando seleciona uma instância na coluna Modelo **componente,** a coluna Critérios de **Saúde** é filtrada para essa vista do componente. Em conformidade, a coluna **Alterações** do Estado é atualizada com base nos critérios de saúde selecionados.

![Exemplo de seleção de instâncias e resultados monitorizados](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Por exemplo, se selecionar *o Disco - 1 D:* da lista em **Modelo**componente, filtros de critérios de **saúde** para *disco - 1D:* e **alterações** de Estado mostram a mudança de estado com base na disponibilidade do *Disco - 1 D:* .

Para ver um estado de saúde atualizado, pode refrescar a página de Diagnósticos de Saúde selecionando o link **Refresh.** Se houver uma atualização do estado de saúde do critério de saúde com base no intervalo de sondagens pré-definido, esta tarefa permite evitar esperar e refletir o mais recente estado de saúde. O Estado dos Critérios de **Saúde** é um filtro que permite analisar os resultados com base no estado de saúde selecionado: Saudável, Aviso, Crítico, Desconhecido e Tudo. A última vez **atualizada** no canto superior direito representa a última vez que a página de Diagnósticos de Saúde foi refrescada.

## <a name="alerts"></a>Alertas

O Monitor Azure para a saúde dos VMs integra-se com [alertas Azure.](../../azure-monitor/platform/alerts-overview.md) Levanta um alerta quando critérios predefinidos, quando detetados, mudam de um estado saudável para um estado pouco saudável. Os alertas são categorizados pela gravidade, do Sev 0 ao Sev 4, com o Sev 0 como o nível mais alto.

Os alertas não estão associados a um grupo de ação para notificá-lo quando o alerta foi desencadeado. Um utilizador com a função Proprietário no âmbito de subscrição deve configurar notificações seguindo as etapas na secção de [alertas Configure.](#configure-alerts)

O número total de alertas de Saúde VM categorizados pela gravidade está disponível no painel de **saúde** no âmbito da secção **Alertas.** Quando seleciona o número total de alertas ou o número correspondente a um nível de gravidade, a página **Alerts** abre e lista todos os alertas correspondentes à sua seleção.

Por exemplo, se selecionar a linha correspondente ao **nível Sev 1,** verá a seguinte vista:

![Exemplo de todos os alertas de Nível 1 da Severity](./media/vminsights-health/vminsights-sev1-alerts-01.png)

A página **All Alerts** não é vista para mostrar apenas alertas correspondentes à sua seleção. Também é filtrado pelo **tipo Derecurso** para mostrar apenas alertas de saúde levantados por um recurso VM. Este formato reflete-se na lista de alerta, sob a coluna **Target Resource,** onde mostra ao Azure VM o alerta elevado quando uma condição pouco saudável foi satisfeita.

Neste ponto de vista, os alertas de outros tipos ou serviços de recursos não se destinam a ser incluídos. Estes alertas incluem alertas de registo, que são baseados em consultas de registo ou alertas métricos que normalmente visualizaria a partir da página padrão do Monitor Azure [Todos os Alertas.](../../azure-monitor/platform/alerts-overview.md#all-alerts-page)

Pode filtrar esta vista selecionando valores nos menus suspensos na parte superior da página.

|Coluna |Descrição |
|-------|------------|
|Subscrição |Selecione uma subscrição do Azure. Apenas os alertas na subscrição selecionada estão incluídos na vista. |
|Grupo de Recursos |Selecione um único grupo de recursos. Apenas os alertas com alvos no grupo de recursos selecionados estão incluídos na vista. |
|Tipo de recurso |Selecione um ou mais tipos de recursos. Por predefinição, apenas são selecionados e **incluídos,** nesta vista, apenas os alertas das máquinas virtuais-alvo. Esta coluna só está disponível depois de um grupo de recursos ter sido especificado. |
|Recurso |Selecione um recurso. Apenas os alertas com esse recurso como alvo estão incluídos na vista. Esta coluna só está disponível depois de ser especificado um tipo de recurso. |
|Gravidade |Selecione uma gravidade de alerta ou selecione **Tudo** para incluir alertas de todas as gravidades. |
|Condição do Monitor |Selecione uma condição de monitor para filtrar alertas se tiverem sido disparados ou resolvidos pelo sistema se a condição já não estiver ativa. Ou, selecione **Tudo** para incluir alertas de todas as condições. |
|Estado de alerta |Selecione um estado de alerta, **Novo,** **Reconhecimento,** **Fechado,** ou **Tudo** para incluir alertas de todos os Estados. |
|Serviço de monitorização |Selecione um serviço ou selecione **Todos** os serviços. Apenas os alertas da VM Insights são suportados para esta funcionalidade.|
|Intervalo de tempo| Apenas os alertas disparados dentro da janela de tempo selecionada estão incluídos na vista. Os valores suportados são a última hora, as últimas 24 horas, os últimos 7 dias, e os últimos 30 dias. |

Quando selecionar um alerta, a página de detalhes do **Alerta** é apresentada. Esta página fornece detalhes do alerta e permite-lhe alterar o seu estado.

Para saber mais sobre gestão de alertas, consulte [Criar, visualizar e gerir alertas utilizando o Monitor Azure.](../../azure-monitor/platform/alerts-metric.md)

>[!NOTE]
>A criação de novos alertas com base em critérios de saúde ou na modificação das regras de alerta de saúde existentes no Monitor de Saúde do Portal não é atualmente suportada.


![Painel de detalhes de alerta para um alerta selecionado](./media/vminsights-health/alert-details-pane-01.png)

Pode alterar um estado de alerta para um ou vários alertas selecionando-os e, em seguida, selecionando o **estado De alterar** a partir da página All **Alerts** no canto superior esquerdo. Selecione um dos estados no painel de estado de **alerta Change,** adicione uma descrição da alteração no campo **'Comentários'** e, em seguida, selecione **Ok** para cometer as suas alterações. Quando a informação for verificada e as alterações forem aplicadas, acompanhe os progressos no âmbito das **Notificações** no menu.

### <a name="configure-alerts"></a>Configurar alertas
Não se pode gerir certas tarefas de gestão de alerta do portal Azure. Estas tarefas devem ser executadas utilizando a [API REST DO Monitor Azure](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Mais concretamente:

- Habilitar ou desativar um alerta para critérios de saúde
- Criação de notificações para critérios de saúde alerta

Cada exemplo utiliza o [ARMClient](https://github.com/projectkudu/armclient) na sua máquina Windows. Se não estiver familiarizado com este método, consulte [a Utilização do ARMClient](../platform/rest-api-walkthrough.md#use-armclient).

#### <a name="enable-or-disable-an-alert-rule"></a>Ativar ou desativar uma regra de alerta

Para ativar ou desativar um alerta para critérios específicos de saúde, o alerta de propriedade **A geração** deve ser modificada com um valor de **Incapacidade** ou **Habilitação**.

Para identificar o *monitorId* para critérios específicos de saúde, o exemplo seguinte mostra como consultar esse valor para os critérios **LogicalDisk\Avg Disk Seconds Por Transferência:**

1. Numa janela terminal, escreva **login armclient.exe**. Fazê-lo leva-o a assinar no Azure.

2. Insira o seguinte comando para recuperar todos os critérios de saúde ativos num VM específico e identificar o valor para a propriedade *monitorId:*

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview"
    ```

    O exemplo seguinte mostra a saída do comando GET do *armclient.* Tome nota do valor do *MonitorId*. Este valor é necessário para o próximo passo, onde devemos especificar a identificação dos critérios de saúde e modificar a sua propriedade para criar um alerta.

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

3. Insira o seguinte comando para modificar a propriedade *alertGeneration:*

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Introduza o comando GET utilizado no passo 2 para verificar se o valor da propriedade está definido para **desativado**.

#### <a name="associate-an-action-group-with-health-criteria"></a>Associar um grupo de ação com critérios de saúde

O Monitor Azure para a saúde de VMs suporta SMS e notificações de e-mail quando os alertas são gerados a partir de critérios de saúde pouco saudáveis. Para configurar notificações, note o nome do grupo de ação configurado para enviar notificações de SMS ou e-mail.

>[!NOTE]
>Esta ação deve ser executada contra cada VM monitorizado que pretende receber uma notificação. Não se aplica a todos os VMs de um grupo de recursos.

1. Numa janela de terminais, introduza *login armclient.exe*. Fazê-lo leva-o a assinar no Azure.

2. Insira o seguinte comando para associar um grupo de ação às regras de alerta:
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Para verificar se o valor da **ação imobiliáriaGroupResourceIds** foi atualizado com sucesso, insira o seguinte comando:

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    A saída deve parecer os seguintes critérios:
    
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

## <a name="next-steps"></a>Passos seguintes

- Para identificar limitações e desempenho geral de VM, consulte [View Azure VM Performance](vminsights-performance.md).

- Para saber sobre dependências de aplicações descobertas, consulte o View Azure Monitor para o Mapa de [VMs](vminsights-maps.md).
