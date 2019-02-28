---
title: Monitorizar o estado de funcionamento da máquina virtual com o Azure Monitor para VMs (pré-visualização) | Documentos da Microsoft
description: Este artigo descreve como compreender o estado de funcionamento da máquina virtual e do sistema operacional subjacente com o Azure Monitor para as VMs.
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
ms.date: 02/08/2019
ms.author: magoedte
ms.openlocfilehash: 7eecd7f5da2b20cb80d8a04d105460baaf9ed4a2
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984718"
---
# <a name="understand-the-health-of-your-azure-virtual-machines-with-azure-monitor-for-vms-preview"></a>Compreender o estado de funcionamento das suas máquinas virtuais do Azure com o Azure Monitor para VMs (pré-visualização)
Azure inclui vários serviços que executar individualmente uma tarefa ou função específica no espaço de monitorização, mas o fornecimento de uma perspectiva detalhada do Estado de funcionamento do sistema operativo alojada em máquinas virtuais do Azure não estava disponível.  Enquanto poderia monitorar para condições diferentes através do Azure Monitor, ele não foi projetado para modelar e representam o estado de funcionamento dos componentes principais ou de estado de funcionamento geral da máquina virtual.  Com o Azure Monitor para a funcionalidade de estado de funcionamento de VMs, proativamente monitoriza a disponibilidade e desempenho do Windows ou Linux SO convidado com um modelo que representam os principais componentes e suas relações, os critérios que especifica como medir o estado de funcionamento desses componentes, e o alerte quando é detetada uma condição de mau estado de funcionamento.  

Visualizar o estado de funcionamento geral da VM do Azure e subjacentes o sistema operativo podem ser observados de duas perspetivas com o Azure Monitor de estado de funcionamento de VMs, diretamente a partir da máquina virtual ou em todas as VMs num grupo de recursos do Azure Monitor.

Este artigo ajuda-o a compreender como avaliar rapidamente, investigar e resolver problemas de estado de funcionamento detetados.

Para obter informações sobre como configurar o Azure Monitor para VMs, veja [ativar o Azure Monitor para VMs](vminsights-onboard.md).

>[!NOTE]
>A partir de 11 de Fevereiro de 2019 Vamos começar a migrar, do modelo de estado de funcionamento atual no Azure Monitor para funcionalidade de estado de funcionamento de VMs, que é visível quando estiver na experiência de diagnóstico de estado de funcionamento hoje em dia, para uma nova versão do modelo de estado de funcionamento. Esta atualização melhora o desempenho de processamento de rollup do Estado de funcionamento e inclui um modelo de estado de funcionamento refinados apresentado na vista de diagnóstico de estado de funcionamento. 
>
>Com o novo modelo de estado de funcionamento, agregação de critérios de estado de funcionamento de subordinado aos critérios de nível de estado de funcionamento da entidade/principal será mais rápida e, consequentemente, o estado de funcionamento das atualizações de principal para o estado pretendido ou alvo com menor latência. Ainda pode filtrar os critérios de estado de funcionamento no **desempenho** e **disponibilidade** categorias, ao contrário do método com base no separador anterior para selecionar qualquer categoria na vista.
>
>Para obter mais detalhes sobre a experiência de diagnóstico de estado de funcionamento, consulte o diagnóstico de estado de funcionamento [secção](#health-diagnostics) neste artigo. 
>
>Esta atualização irá melhorar o seguinte: 
>
>- Estado de funcionamento rollup processamento com latência reduzida  
>- Alertas mais rápido no estado de funcionamento muda 
>- Atualização mais rápida do Estado de funcionamento na vista de máquina de virtual agregados para todas as VMs 
>
>Não há nenhum regressão de qualquer funcionalidade entregue hoje em dia com a funcionalidade de estado de funcionamento do Azure Monitor para as VMs.

>Como resultado desta alteração, duas experiências no diagnóstico de estado de funcionamento são afetadas - histórico de alterações de estado será reposto e alterações de estado anteriores para os critérios de estado de funcionamento não estarão disponíveis para revisão na coluna de alteração de estado da página de diagnóstico de estado de funcionamento. Se estiver interessado nos dados históricos de qualquer de missão crítica VM, em seguida, pode tirar uma captura de ecrã dos dados de critérios de estado de funcionamento e as alterações de estado correspondentes para sua referência. 

## <a name="monitoring-configuration-details"></a>Detalhes de configuração de monitorização
Esta seção descreve os critérios de estado de funcionamento do padrão definidos para monitorizar o Windows Azure e máquinas virtuais do Linux. Todos os critérios de estado de funcionamento são previamente configurados para o alerta quando for cumprida a condição de mau estado de funcionamento. 

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
- Tempo de inatividade de percentagem de disco físico
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
- Tempo de DPC percentagem do processador
- Processador de percentagem de tempo do processador
- Tempo de processador de percentagem total
- Tempo de DPC de percentagem total
- Sistema operativo de Megabytes de memória disponíveis

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no [portal do Azure](https://portal.azure.com). 

## <a name="introduction-to-health-experience"></a>Introdução à experiência de estado de funcionamento
Antes de explorar a utilizar a funcionalidade de estado de funcionamento de uma única máquina virtual ou o grupo de VMs, é importante, fornecemos uma breve introdução para compreender como as informações são apresentadas e o que representam as visualizações.  

## <a name="view-health-directly-from-a-virtual-machine"></a>Ver estado de funcionamento diretamente a partir de uma máquina virtual 
Para ver o estado de funcionamento de uma VM do Azure, selecione **Insights (pré-visualização)** no painel esquerdo da máquina virtual. Na página de informações VM, **estado de funcionamento** está aberta por predefinição e mostra a vista de estado de funcionamento da VM.  

![O Azure Monitor para descrição geral do Estado de funcionamento de VMs de uma máquina virtual do Azure selecionada](./media/vminsights-health/vminsights-directvm-health.png)

Sobre o **estado de funcionamento** separador, na secção **estado de funcionamento da VM do convidado**, a tabela mostra o estado de funcionamento atual da sua máquina virtual e o número total de alertas de estado de funcionamento da VM gerados por um componente em mau estado de funcionamento. Consulte a secção de alertas para obter mais detalhes sobre a experiência de alertas.  

Os Estados de funcionamento definidos para uma VM são descritos na tabela a seguir: 

|Ícone |Estado de funcionamento |Significado |
|-----|-------------|------------|
| |Bom estado de funcionamento |Estado de funcionamento está em bom estado, se for dentro as condições de estado de funcionamento definidos, indicando que nenhum problema detetado para a VM e está a funcionar conforme necessário. No caso de um monitor de rollup principal, rolls-up do Estado de funcionamento e reflete o estado mais favorável ou pior do filho.|
| |Crítica |Estado de funcionamento é crítico, se não se encontra na condição de estado de funcionamento definidos, que indica que foram detetados um ou mais problemas críticos, que precisam ser abordadas para restaurar o funcionamento normal. No caso de um monitor de rollup principal, rolls-up do Estado de funcionamento e reflete o estado mais favorável ou pior do filho.|
| |Aviso |Estado de funcionamento é aviso se estiver entre dois limiares para a condição de estado de funcionamento definidos, onde um indica uma *aviso* estado e a outra indica um *crítico* Estado (três limiares de estado de funcionamento podem ser configurado), ou quando é detetado um problema de não-críticas que pode causar problemas críticos, se não resolvido. No caso de um rollup principal monitor, se um ou mais dos filhos estão no estado de aviso, em seguida, irá refletir o pai *aviso* estado. Se houver um filho que está numa *crítico* e outro filho num *aviso* Estado, o rollup principal mostrará um Estado de funcionamento *crítico*.|
| |Desconhecidos |Estado de funcionamento está numa *desconhecido* estado quando o estado de funcionamento não é possível calcular por várias razões, tais como não é possível recolher dados, o serviço não inicializados, etc. Este estado de funcionamento não é configurável.| 

Selecionando **ver o diagnóstico de estado de funcionamento** abre uma página que mostra todos os componentes da VM, critérios de estado de funcionamento associado, alterações de estado e outros problemas importantes encontrados por monitorizar componentes relacionados com a VM. Para obter mais informações, consulte [diagnóstico de estado de funcionamento](#health-diagnostics). 

Sob o **estado de funcionamento do componente** secção, a tabela mostra um Estado de rollup de estado de funcionamento das categorias principais do desempenho monitorizados pelos critérios de estado de funcionamento para essas áreas, especificamente **CPU**,  **Memória**, **disco**, e **rede**.  Selecionar qualquer um dos componentes é aberta uma página com todos os critérios de estado de funcionamento individuais monitorização aspectos desse componente e o estado do respetivo estado de funcionamento de cada.  

Ao aceder ao estado de funcionamento a partir de uma VM do Azure com o sistema operativo do Windows, o estado de funcionamento dos serviços de Windows core de 5 principais são apresentados na secção **Core dos serviços de estado de funcionamento**.  Selecionar qualquer um dos serviços é aberta uma página listando os critérios de estado de funcionamento monitorização esse componente e o estado de integridade.  Ao clicar no nome dos critérios de estado de funcionamento irá abrir o painel de propriedades e a partir daqui pode rever os detalhes de configuração, incluindo se os critérios de estado de funcionamento tem um alerta correspondente do Azure Monitor definido. Para obter mais informações, consulte [diagnóstico de estado de funcionamento e trabalhar com os critérios de estado de funcionamento](#health-diagnostics).  

## <a name="aggregate-virtual-machine-perspective"></a>Ponto de vista de máquina de virtual agregado
Para ver a recolha de estado de funcionamento para todas as suas máquinas virtuais num grupo de recursos, na lista de navegação no portal, selecione **do Azure Monitor** e, em seguida, selecione **máquinas virtuais (pré-visualização)**.  

![Vista do Azure Monitor de monitorização de informações de VM](./media/vminsights-health/vminsights-aggregate-health.png)

Partir do **subscrição** e **grupo de recursos** listas pendentes, selecione o grupo de recursos adequados que inclui as VMs relacionados ao grupo, para ver o respetivo estado de funcionamento comunicadas.  A seleção de apenas aplica-se para a funcionalidade de estado de funcionamento e não vão ser transferidos para o desempenho ou de mapa.

Sobre o **estado de funcionamento** separador, é possível saber o seguinte:

* O número de VMs está num estado crítico ou mau estado de funcionamento, versus quantas estão em bom estado ou não a submeter dados (referidos como um Estado desconhecido)?
* Qual as VMs por sistema operativo (SO) estão a enviar relatórios mau estado de funcionamento e quantos?
* O número de VMs é mau estado de funcionamento devido a um problema detetado com um processador, disco, memória ou um adaptador de rede, categorizados por Estado de funcionamento?  
* O número de VMs é mau estado de funcionamento devido a um problema detetado com um serviço de sistema operacional principal, categorizado por Estado de funcionamento?

Aqui pode rapidamente identificar os principais problemas críticos detetados pelos critérios de estado de funcionamento de monitoramento proativo da VM e rever os detalhes de alerta de estado de funcionamento da VM e o artigo de conhecimento associado a intenção de auxiliar no diagnóstico e correção do problema.  Selecione qualquer uma das gravidades para abrir o [todos os alertas](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) página filtrada por esse gravidade.

O **distribuição VM pelo sistema operativo** lista mostra as VMs listadas por edição do Windows ou a distribuição de Linux, juntamente com suas versões. Em cada categoria de sistema operativo, as VMs são divididas de forma ainda mais com base no estado de funcionamento da VM. 

![Ponto de vista de distribuição de máquina virtual de VM Insights](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Pode clicar em qualquer item de coluna - **contagem VM**, **crítico**, **aviso**, **bom estado de funcionamento** ou **desconhecido** para desagregar os **máquinas virtuais** página Ver uma lista de resultados filtrados que correspondem a coluna selecionada. Por exemplo, se quisermos rever todas as VMs em execução **Red Hat Enterprise Linux versão 7.5**, clique nas **contagem VM** valor para o sistema operacional e abrirá a página seguinte, as máquinas virtuais que correspondam a listagem esse filtro e o respetivo estado de funcionamento atualmente conhecidos.  

![Exemplo de rollup de VMs de Linux do Red Hat](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
Sobre o **máquinas virtuais** página, se selecionar o nome de uma VM sob a coluna **nome da VM**, será direcionado para a página de instância VM com mais detalhes sobre os alertas e problemas de critérios de estado de funcionamento identificado que são que afetam a VM selecionada.  A partir daqui, pode filtrar os detalhes de estado de funcionamento ao clicar em **estado de funcionamento** ícone no canto superior esquerdo da página para ver quais componentes estão em mau estado de funcionamento ou pode ver os alertas de estado de funcionamento da VM gerados por um componente em mau estado de funcionamento categorizadas por gravidade do alerta.    

Na vista de lista VM, clicar no nome de uma VM abre o **estado de funcionamento** página, para que selecionou VM, da mesma forma como se selecionou **Insights (pré-visualização)** da VM diretamente.

![Informações VM de uma máquina virtual do Azure selecionada](./media/vminsights-health/vminsights-directvm-health.png)

Aqui mostra um rollup **estado de funcionamento** para a máquina virtual e **alertas**, categorizados por gravidade, que representam os alertas de estado de funcionamento da VM gerados quando o estado de funcionamento muda de estado de funcionamento para mau estado de funcionamento para um critérios de estado de funcionamento.  Selecionando **VMs em condição crítica** abrirá uma página com uma lista de uma ou mais VMs que estão num Estado de funcionamento crítico.  Clicar no estado de funcionamento para uma das VMs na lista mostrará os **diagnóstico de estado de funcionamento** vista da VM.  Aqui pode descobrir quais critérios de estado de funcionamento está refletindo um problema de estado de funcionamento. Quando o **diagnóstico de estado de funcionamento** é aberta a página, mostra todos os componentes da VM e os respetivos critérios de estado de funcionamento associado com o estado de funcionamento atual.  Consulte a [diagnóstico de estado de funcionamento](#health-diagnostics) secção para obter mais detalhes.  

Selecionando **ver todos os critérios de estado de funcionamento** abre uma página que mostra uma lista de todos os critérios de estado de funcionamento disponíveis com esta funcionalidade.  As informações podem ser mais filtradas com base nas seguintes opções:

* **Tipo de** – existem três tipos de estado de funcionamento tipos de critérios para avaliar condições e rollup de estado de funcionamento geral da VM monitorizada.  
    a. **Unidade** – medem algum aspeto da máquina virtual. Este tipo de critérios de estado de funcionamento pode ser verificar um contador de desempenho para determinar o desempenho do componente, executar um script para efetuar uma transação sintética ou monitorizar um evento que indica um erro.  Por predefinição, o filtro está definido para a unidade.  
    b. **Dependência** -fornece o rollup de estado de funcionamento entre diferentes entidades. Estes critérios de estado de funcionamento permite que o estado de funcionamento de uma entidade a depender do Estado de funcionamento de outro tipo de entidade que ele depende para operação concluída com êxito.  
    c. **Agregação** -fornece um Estado de funcionamento combinado de critérios de estado de funcionamento semelhante. Critérios de estado de funcionamento de unidades e de dependência serão normalmente configurados sob um critério de agregação do Estado de funcionamento. Além de fornecer uma melhor organização geral dos muitos critérios de estado de funcionamento de diferentes destinados a uma entidade, o critério de agregação do Estado de funcionamento fornece um Estado de funcionamento exclusivo para as categorias distintas das entidades.

* **Categoria** -tipo de critérios de estado de funcionamento utilizado para os critérios de grupo do tipo semelhante para fins de relatórios.  Eles são **disponibilidade** ou **desempenho**.

Pode explorar ainda mais baixo para ver quais as instâncias estão em mau estado de funcionamento ao clicar num valor baixo a **mau estado de funcionamento do componente** coluna.  Na página, uma tabela lista os componentes, que estão num Estado de funcionamento crítico.    

## <a name="health-diagnostics"></a>Diagnóstico de estado de funcionamento
Thge **diagnóstico de estado de funcionamento** página permite visualizar o modelo de estado de funcionamento de uma VM, todos os componentes da VM, a listagem associado critérios de estado de funcionamento, as alterações de estado, e outros problemas importantes identificados pelo monitorizado componentes relacionados com para a VM.

![Exemplo de página de diagnóstico de estado de funcionamento para uma VM](./media/vminsights-health/health-diagnostics-page-01.png)

Pode iniciar o diagnóstico de estado de funcionamento das seguintes formas.

* Por Estado de funcionamento de rollup para todas as VMs a partir da perspetiva VM de agregação no Azure Monitor.  Sobre o **estado de funcionamento** página, clique no ícone para **crítico**, **aviso**, **bom estado de funcionamento**, ou **desconhecido** Estado de funcionamento na seção **estado de funcionamento da VM do convidado** e Desagregue até a página que apresenta uma lista de todas as VMs correspondentes dessa categoria filtrada.  Ao clicar nos valores a **estado de funcionamento** coluna abrirá o diagnóstico de estado de funcionamento de âmbito para essa VM específica.      

* Pelo sistema de operativo a partir da perspetiva VM de agregação no Azure Monitor. Sob **distribuição de VM**, selecionar qualquer um dos valores da coluna irá abrir o **máquinas virtuais** página e retornar uma lista na tabela que correspondem a categoria filtrada.  Clique no valor sob **estado de funcionamento** coluna abre o diagnóstico de estado de funcionamento para a VM selecionada.    
 
* Da VM no Monitor do Azure para VMs do convidado **estado de funcionamento** separador, selecionando **ver diagnóstico de estado de funcionamento** 

Diagnóstico de estado de funcionamento organiza as informações de estado de funcionamento nas seguintes categorias: 

* Disponibilidade
* Desempenho
 
Todos os critérios de estado de funcionamento definidos para um componente específico, como o disco lógico, CPU, etc. Além disso, a categoria do monitor pode ser vista ao lado no **critérios de estado de funcionamento** coluna.  

Estado de um critério de estado de funcionamento é definido por um dos quatro Estados – *crítico*, *aviso*, *bom estado de funcionamento*, e *desconhecido*. Os três primeiros são configuráveis, o que significa que pode modificar os valores de limiar de monitores com o [API do Monitor de carga de trabalho](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update). *Desconhecido* não é configurável e reservado para cenários específicos.  

Página de diagnóstico de estado de funcionamento tem três seções principais:

* Modelo do Componente 
* Critérios de Estado de Funcionamento
* Alterações de Estado 

![Secções da página de diagnóstico de estado de funcionamento](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Modelo de componente
A coluna mais à esquerda na página de diagnóstico de estado de funcionamento é o modelo de componente. Todos os componentes, que estão associados a VM, são apresentados nesta coluna, juntamente com o respetivo estado de funcionamento atual. 

No exemplo a seguir, os componentes detetados são disco, disco lógico, processador, memória e sistema operativo. Várias instâncias desses componentes são detetadas e apresentadas nesta coluna. Por exemplo, a imagem abaixo mostra que a VM tiver duas instâncias de discos lógicos - c: e d:, que estão em bom estado de funcionamento.  

![Modelo de componente de exemplo apresentado no diagnóstico de estado de funcionamento](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Critérios de estado de funcionamento
A coluna centro na página de diagnóstico de estado de funcionamento é o **critérios de estado de funcionamento** coluna. O modelo de estado de funcionamento definido para a VM é apresentado numa árvore hierárquica. O modelo de estado de funcionamento para uma VM é composta por unidade e os critérios de estado de funcionamento agregado.  

![Critérios de estado de funcionamento de exemplo apresentados no diagnóstico de estado de funcionamento](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Um critério de estado de funcionamento mede o estado de funcionamento da instância monitorizado com alguns critérios, o que poderia ser um valor de limite, estado de uma entidade, etc. Um critério de estado de funcionamento tem duas ou três limiares de estado de funcionamento configuráveis, conforme descrito anteriormente. Em qualquer determinado momento, o critério de estado de funcionamento pode estar em apenas um dos seus Estados potenciais. 

O estado de funcionamento geral de um destino é determinado pelo Estado de funcionamento de cada um dos respetivos critérios de estado de funcionamento definidos no modelo de estado de funcionamento. Esta será uma combinação de critérios de estado de funcionamento direcionados diretamente para o destino, os critérios de estado de funcionamento destinados a acumular no destino com um critério de estado de funcionamento agregado de componentes. Esta hierarquia é ilustrada na **critérios de estado de funcionamento** secção da página Diagnóstico de estado de funcionamento. A política de rollup do Estado de funcionamento é parte da configuração dos critérios de estado de funcionamento agregado (predefinição está definida como *pior de*). Pode encontrar uma lista de conjunto predefinido de critérios de estado de funcionamento em execução como parte desta funcionalidade na seção [detalhes de configuração da monitorização](#monitoring-configuration-details).  

**Unidade** tipo de critérios de estado de funcionamento pode ter a respetiva configuração modificada clicando no link elipse para até o momento certo e selecionar **Mostrar detalhes** para abrir o painel de configuração. 

![Configurar um exemplo de critérios de estado de funcionamento](./media/vminsights-health/health-diagnostics-vm-example-02.png)

No painel de configuração para os critérios de estado de funcionamento selecionado, usando o exemplo **média disco segundos por escrever**, seu limiar pode ser configurado com um valor numérico diferente. É um monitor de dois Estados, significando que alterações apenas de bom estado de funcionamento para aviso. Outro critério de estado de funcionamento pode ser três Estados, onde pode configurar o valor para o limiar de estado de funcionamento de aviso e crítico.  

>[!NOTE]
>Aplicar alterações de configuração de critérios de estado de funcionamento para uma instância é aplicada a todas as instâncias monitorizadas.  Por exemplo, se selecionar **d: de-1 do disco** e modificar a **média disco segundos por escrever** limiar, não se aplica a apenas essa instância, mas todas as outras instâncias do disco detetado e monitorizado na VM.
>

![Configurar um critério de estado de funcionamento de um exemplo de monitor de unidade](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Se quiser saber mais sobre o indicador de estado de funcionamento, artigos de conhecimento são incluídos para ajudar a identificar problemas, causas e resoluções. Clique nas **ver informações** link na página e este será aberto um novo separador no browser que mostra o artigo de conhecimento específico. Em qualquer altura, pode rever todos os artigos de conhecimento de critério de estado de funcionamento incluídos com o Azure Monitor para a funcionalidade de estado de funcionamento de VMs [aqui](https://docs.microsoft.com/azure/monitoring/infrastructure-health/).
  
### <a name="state-changes"></a>Alterações de estado
A coluna mais à direita na página de diagnóstico de estado de funcionamento é **as alterações de estado**. Ele lista todas as alterações de estado associadas com os critérios de estado de funcionamento que está selecionado no **critérios de estado de funcionamento** seção ou a alteração do Estado da VM se uma VM tiver sido selecionada a partir do **modelo de componente** ou **Critérios de estado de funcionamento** coluna da tabela. 

![Alterações de estado de exemplo apresentadas no diagnóstico de estado de funcionamento](./media/vminsights-health/health-diagnostics-page-statechanges.png)

Esta secção compreende o estado de critérios de estado de funcionamento e o tempo associado ordenados pelo Estado mais recente na parte superior.   

### <a name="association-of-component-model-health-criteria-and-state-change-columns"></a>Associação de modelo de componente, critérios de estado de funcionamento e estado alterar colunas 
As três colunas são interligadas entre si. Quando seleciona uma instância detetada no **modelo de componente** secção, o **critérios de estado de funcionamento** secção é filtrada para essa exibição de componente e proporcionalmente o **alteração de estado**secção é atualizada com base nos critérios de estado de funcionamento selecionado. 

![Exemplo de seleção de instância monitorizada e os resultados](./media/vminsights-health/health-diagnostics-vm-example-01.png)

No exemplo acima, quando seleciona **disco - d 1:**, a árvore de critérios de estado de funcionamento é filtrada para **disco - 1 D:**. O **de alteração de estado** coluna mostra a alteração de estado com base na disponibilidade dos **disco - d 1:**. 

Para ver um Estado de funcionamento atualizado, pode atualizar a página de diagnóstico de estado de funcionamento ao clicar o **atualizar** ligação.  Se existir uma atualização do Estado de funcionamento do critério de estado de funcionamento com base no intervalo de consulta predefinido, essa tarefa permite-lhe evitar espera e reflete o estado de funcionamento mais recente.  O **estado de funcionamento de critérios** é um filtro que lhe permite definir o âmbito os resultados com base no estado do Estado de funcionamento selecionado - *bom estado de funcionamento*, *aviso*, *crítico*, *Desconhecido*, e *todos os*.  O **última atualização** tempo no canto superior direito representa a última vez em quando a página de diagnóstico de estado de funcionamento foi atualizada.  

## <a name="alerts"></a>Alertas
Monitor do Azure para a funcionalidade de estado de funcionamento de VMs integra [alertas do Azure](../../azure-monitor/platform/alerts-overview.md) e emite um alerta quando os critérios de estado de funcionamento predefinido alterar de bom estado de funcionamento para um mau estado de funcionamento, quando a condição for detectada. Alertas são categorizados por gravidade - gravidade 0 a 4, com 0 representando o nível de gravidade mais elevado de gravidade.  

Número total de alertas de estado de funcionamento da VM categorizados por gravidade está disponível na **estado de funcionamento** dashboard na secção **alertas**. Ao selecionar o número total de alertas ou o número correspondente a um nível de gravidade, o **alertas** página abre e apresenta uma lista de todos os alertas que correspondam a sua seleção.  Por exemplo, se tiver selecionado a linha correspondente **nível de gravidade 1**, em seguida, verá a vista seguinte:

![Exemplo de todos os alertas de nível de gravidade 1](./media/vminsights-health/vminsights-sev1-alerts-01.png)

Sobre o **alertas** página, ele apenas não tem como escopo para mostrar alertas correspondentes a sua seleção, mas também são filtradas pela **tipo de recurso** para mostrar apenas os alertas de estado de funcionamento gerados pelo recurso de máquina virtual.  Isto é refletido na lista de alertas, abaixo da coluna **recurso de destino**, onde ele mostra que a VM do Azure, o alerta foi gerado para quando a condição de mau estado de funcionamento dos critérios de estado de funcionamento específico foi cumprida.  

Alertas a partir de outros tipos de recursos ou serviços não se destinam a serem incluídos nesta vista, como alertas de registo com base em consultas de registo ou alerta de métrica que normalmente seria ver do padrão do Azure Monitor [todos os alertas](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) página. 

Pode filtrar esta vista ao selecionar os valores nos menus de lista pendente na parte superior da página.

|Coluna |Descrição | 
|-------|------------| 
|Subscrição |Selecione uma subscrição do Azure. Apenas os alertas na subscrição selecionada são incluídos na vista. | 
|Grupo de Recursos |Selecione um grupo de recursos. Apenas os alertas com destinos no grupo de recursos selecionado estão incluídos na vista. | 
|Tipo de recurso |Selecione um ou mais tipos de recursos. Por predefinição, apenas os alertas de destino **máquinas virtuais** é selecionado e incluído nesta vista. Esta coluna só está disponível depois de um grupo de recursos foi especificado. | 
|Recurso |Selecione um recurso. Apenas alertas com esse recurso como um destino estão incluídas na vista. Esta coluna apenas está disponível após foi especificado um tipo de recurso. | 
|Gravidade |Opte por uma gravidade de alerta ou selecione *todos os* para incluir alertas de todas as gravidades. | 
|Condição do Monitor |Selecione uma condição do monitor para filtrar alertas se eles tiverem sido *Fired* pelo sistema ou *resolvido* pelo sistema se a condição já não estiver ativa. Ou selecione *todos os* para incluir alertas de todas as condições. | 
|Estado de alerta |Selecione um Estado de alerta *New*, *confirmação*, *fechado*, ou selecione *todos os* para incluir alertas de todos os Estados. | 
|Monitorizar serviço |Selecione um serviço ou selecione *todos os* para incluir todos os serviços. Apenas alertas a partir da *VM Insights* são suportados para esta funcionalidade.| 
|Intervalo de tempo| Apenas os alertas acionados dentro da janela de tempo selecionado estão incluídos na vista. Os valores suportados são a última hora, últimas 24 horas, últimos 7 dias e os últimos 30 dias. | 

O **detalhes do alerta** página é apresentada quando seleciona um alerta, fornecendo detalhes do alerta e permitindo que altere seu estado. Para saber mais sobre como gerir alertas, veja [criar, ver e gerir alertas ao utilizar o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).  

>[!NOTE]
>Este tempo, criar novos alertas com base nos critérios de estado de funcionamento ou modificar o estado de funcionamento existente não é suportada a regras de alerta no Monitor do Azure no portal.  
>

![Painel de detalhes do alerta para um alerta selecionado](./media/vminsights-health/alert-details-pane-01.png)

Alerta de estado também pode ser alterado para um ou vários alertas, selecionando-os e, em seguida, selecionando **alterar o estado** partir a **todos os alertas** página, no canto superior esquerdo. Sobre o **alterar o estado de alerta** painel selecionar um dos Estados, adicione uma descrição da alteração no **comentário** campo e, em seguida, clique em **Ok** para consolidar as alterações. Enquanto as informações são verificadas e as alterações são aplicadas, pode acompanhar o progresso em **notificações** no menu.  

## <a name="next-steps"></a>Passos Seguintes
Para identificar afunilamentos e a utilização geral com o desempenho de VMs, veja [vista de desempenho da VM do Azure](vminsights-performance.md), ou para ver dependências de aplicações detetadas, consulte [vista de Azure Monitor para o mapa de VMs](vminsights-maps.md). 
