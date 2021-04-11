---
title: O que mudar para volume de quota dura significa para o seu serviço Azure NetApp Files | Microsoft Docs
description: Descreve a mudança para a utilização de quota dura de volume, como planear a mudança e como monitorizar e gerir as capacidades.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: b-juche
ms.openlocfilehash: 52e855cb4ab42ed78a055ecdc31cffa886dc9bf2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731994"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>O que mudar para volume de quota dura significa para o seu serviço Azure NetApp Files

Desde o início do serviço, o Azure NetApp Files tem vindo a utilizar um mecanismo de fornecimento de capacidade e de crescimento automático. Os volumes Azure NetApp Files são pouco abastetados num conjunto de capacidades de subposição, abasteçados pelo cliente de um nível e tamanho selecionados. Os tamanhos de volume (quotas) são utilizados para proporcionar desempenho e capacidade, e as quotas podem ser ajustadas no voo a qualquer momento. Este comportamento significa que, atualmente, a quota de volume é uma alavanca de desempenho usada para controlar a largura de banda ao volume. Atualmente, os pools de capacidade de subposição crescem automaticamente quando a capacidade se enche.   

> [!IMPORTANT] 
> O comportamento do Azure NetApp Files de volume e capacidade de fornecimento de piscinas mudará para um mecanismo *manual* e *controlável.* **A partir de 30 de abril de 2021 (atualizado), os tamanhos de volume (quota) vão gerir o desempenho da largura de banda, bem como a capacidade a provisionada, e as piscinas de capacidade subjacentes deixarão de crescer automaticamente.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>Razões para a alteração da quota dura do volume

Muitos clientes indicaram três principais desafios com o comportamento *inicial:*
* Os clientes VM veriam a capacidade pouco abastecer (100 TiB) de qualquer volume dado quando utilizarem ferramentas de monitorização de espaço ou capacidade de SISTEMA, dando visibilidade de capacidade ao cliente ou à capacidade lateral de aplicação imprecisa.
* Os proprietários de aplicações não teriam qualquer controlo sobre o espaço de piscina de capacidade a provisionada (e o custo associado), devido ao comportamento de crescimento automático da piscina de capacidade. Esta situação é complicada em ambientes onde os "processos de fuga" poderiam rapidamente preencher e aumentar a capacidade e os custos previstos.
* Os clientes querem ver e manter uma correlação direta entre o tamanho do volume (quota) e o desempenho. Com o comportamento atual de (implícito) sobresubscrever um volume (em termos de capacidade) e de crescimento automático da piscina, os clientes não têm uma correlação direta, até que a quota de volume tenha sido ativamente definida ou reposta. 

Muitos clientes solicitaram o controlo direto sobre a capacidade a provisionada. Querem controlar e equilibrar a capacidade de armazenamento e a utilização. Pretendem igualmente controlar os custos, juntamente com a visibilidade do lado da aplicação e do lado do cliente, da capacidade e desempenho avisionados dos seus volumes de aplicação. 

## <a name="what-is-the-volume-hard-quota-change"></a>Qual é a mudança de quota dura de volume   

Com a variação de quota dura de volume, os volumes Azure NetApp Files deixarão de ser finos a (no máximo) 100 TiB. Os volumes serão a provisionados à dimensão configurada real (quota). Além disso, os pools de capacidade de subposição deixarão de crescer automaticamente ao atingirem o consumo de capacidade total. Esta mudança refletirá o comportamento como os discos geridos a Azure, que também são a provisionados como é, sem aumento automático de capacidade.

Por exemplo, considere um volume de ficheiros Azure NetApp configurado em tamanho 1-TiB (quota) num pool de capacidade de serviço 4-TiB Ultra. Uma aplicação está continuamente a escrever dados para o volume.

O comportamento *inicial:*  
* Largura de banda esperada: 128 MiB/s
* Capacidade total utilizável (e visível do cliente): 100 TiB   
    Não será capaz de escrever mais dados sobre o volume além deste tamanho.
* Piscina de capacidade: Cresce automaticamente com 1 incrementos TiB quando está cheio.
* Variação da quota de volume: Só altera o desempenho (largura de banda) do volume. Não altera a capacidade visível ou utilizável do cliente.

O comportamento *alterado:*  
* Largura de banda esperada: 128 MiB/s
* Capacidade total utilizável (e visível do cliente): 1 TiB Não será capaz de escrever mais dados sobre o volume além deste tamanho.
* Piscina de capacidade: Mantém-se 4 TiB em tamanho e não cresce automaticamente. 
* Alteração da quota de volume: Altera o desempenho (largura de banda) e a capacidade visível ou utilizável do volume do cliente.

É necessário monitorizar proativamente a utilização dos volumes e piscinas de capacidade do Azure NetApp. Você precisa alterar propositadamente o volume e a utilização da piscina para um consumo próximo ao máximo. Os Ficheiros Azure NetApp continuarão a permitir [operações de redimensionação de volume e capacidade de volume on-fly](azure-netapp-files-resize-capacity-pools-or-volumes.md).

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>Como operacionalizar a variação de quota dura de volume

Esta secção fornece orientações sobre como operacionalizar a alteração da quota dura de volume para uma transição suave. Fornece igualmente informações sobre o manuseamento de volumes e agrupamentos de capacidade atualmente a provisionados, monitorização contínua e opções de alerta e gestão da capacidade.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>Atualmente a provisionados volumes e piscinas de capacidade

Devido à variação de quota rígida de volume, deve alterar o seu modelo de funcionamento. Os volumes a provisionados e os pools de capacidade exigirão uma gestão contínua da capacidade.  Uma vez que o comportamento alterado irá ocorrer instantaneamente, a equipa do Azure NetApp Files recomenda uma série de medidas corretivas únicas para volumes e piscinas de capacidade existentes, previamente a provisionadas, conforme descrito nesta secção.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>Recomendações de medidas corretivas ou preventivas únicas  

A variação do volume de quotas rígidas resultará em alterações na capacidade a provisionada e disponível para volumes e piscinas previamente avisionados. Como resultado, alguns desafios de atribuição de capacidade podem acontecer. Para evitar situações fora do espaço de curto prazo para os clientes, a equipa da Azure NetApp Files recomenda as seguintes medidas corretivas/preventivas únicas: 

* **Tamanhos de volume previstos:**   
    Redimensione cada volume a provisionado para ter um tampão adequado com base na taxa de variação e alertando ou redimensionando o tempo de reviravolta (por exemplo, 20% com base em considerações típicas de carga de trabalho), com um máximo de 100 TiB (que é o limite de tamanho do [volume).](azure-netapp-files-resource-limits.md#resource-limits) Este novo tamanho do volume, incluindo a capacidade tampão, deve basear-se nos seguintes fatores:
    * Capacidade de volume **a provisionada,** no caso de a capacidade utilizada ser inferior à quota de volume prevista.
    * Capacidade de volume **utilizada,** caso a capacidade utilizada seja superior à quota de volume prevista.  
    Não existe um custo adicional para o aumento da capacidade do volume se o pool de capacidade de subposição não precisar de ser cultivado. Como efeito desta alteração, poderá observar um *aumento* do limite de largura de banda para o volume (no caso de ser utilizado o [tipo de piscina de capacidade automática QoS).](azure-netapp-files-understand-storage-hierarchy.md#qos_types)

* **Tamanhos de piscina de capacidade a provisionada:**   
    Após os ajustes de tamanhos de volume, se a soma dos tamanhos de volume se tornar maior do que o tamanho do pool de capacidade de alojamento, o pool de capacidade terá de ser aumentado para um tamanho igual ou superior à soma dos volumes, com um máximo de 500 TiB (que é o limite de tamanho da piscina de [capacidade).](azure-netapp-files-resource-limits.md#resource-limits) A capacidade adicional do pool estará sujeita a carga ACR normalmente.

Deverá trabalhar com os seus especialistas em Ficheiros Azure NetApp para validar o seu ambiente, caso necessite de ajuda para configurar a monitorização ou alerta, conforme descrito nas secções abaixo.

### <a name="ongoing-capacity-management"></a>Gestão contínua da capacidade  

Após a execução das medidas corretivas únicas, deve reunir processos em curso para monitorizar e gerir a capacidade. As seguintes secções fornecem sugestões e alternativas sobre monitorização e gestão de capacidades.

### <a name="monitor-capacity-utilization"></a>Monitorizar a utilização da capacidade

Pode monitorizar a utilização da capacidade a vários níveis. 

#### <a name="vm-level-monitoring"></a>Monitorização ao nível do VM 

O nível mais elevado de monitorização (mais próximo da aplicação) é de dentro da máquina virtual de aplicação. Observará uma mudança de comportamento no reporte de capacidade a partir do cliente VM OS.

Nos dois cenários seguintes, considere um volume de ficheiros Azure NetApp configurado em tamanho 1-TiB (quota) num pool de capacidade de 4-TiB, nível de serviço Ultra. 

##### <a name="windows"></a>Windows

Os clientes windows podem verificar a capacidade usada e disponível de um volume utilizando as propriedades de unidade mapeadas pela rede. Pode utilizar a opção **Explorer**  ->  **Drive**  ->  **Properties.**  

Os exemplos a seguir mostram o volume de reporte da capacidade no Windows *antes* da alteração do comportamento:

![Imagens que mostram capacidade de armazenamento de exemplo de um volume antes de mudar de comportamento.](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

Também pode utilizar o `dir` comando na pronta de comando, como mostrado abaixo:

![Screenshot que mostra usar um comando para exibir a capacidade de armazenamento para um volume antes de mudar de comportamento.](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

Os exemplos a seguir mostram a capacidade de volume a reportar no Windows *após* a alteração do comportamento:

![Imagens que mostram capacidade de armazenamento de exemplo de um volume após mudança de comportamento.](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

O exemplo a seguir mostra a saída do `dir` comando:  

![Screenshot que mostra usar um comando para exibir a capacidade de armazenamento para um volume após mudança de comportamento.](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Os clientes Linux podem verificar a capacidade utilizada e disponível de um volume utilizando o [ `df` comando](https://linux.die.net/man/1/df). A `-h` opção mostrará o tamanho, o espaço usado e o espaço disponível em formato legível pelo homem, utilizando os tamanhos das unidades M, G e T.

O exemplo a seguir mostra o relato da capacidade de volume no Linux *antes* da mudança de comportamento:  

![Screenshot que mostra usar o Linux para exibir a capacidade de armazenamento para um volume antes de mudar de comportamento.](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

O exemplo a seguir mostra o relato da capacidade de volume no Linux *após* a alteração do comportamento:  

![Screenshot que mostra usar o Linux para exibir a capacidade de armazenamento para um volume após mudança de comportamento.](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>Configure alertas usando ANFCapacityManager

Pode utilizar a ferramenta Logic Apps ANFCapacityManager suportada pela comunidade para monitorizar a capacidade dos Ficheiros Azure NetApp e receber alertas personalizados. A ferramenta ANFCapacityManager está disponível na [página do GitHub da ANFCapacity.](https://github.com/ANFTechTeam/ANFCapacityManager)

ANFCapacityManager é uma App Azure Logic que gere regras de alerta baseadas na capacidade. Aumenta automaticamente os tamanhos de volume para evitar que os volumes dos ficheiros Azure NetApp se escasseem. É fácil de implementar e fornece as seguintes capacidades de Gestão de Alerta:

* Quando um pool ou volume de capacidade do Azure NetApp Files é criado, a ANFCapacityManager cria uma regra de alerta métrica com base no limiar por cento consumido especificado.
* Quando um pool ou volume de capacidade do Azure NetApp Files é redimensionado, a ANFCapacityManager modifica a regra de alerta métrico com base no limiar de capacidade por cento especificado. Se a regra de alerta não existir, será criada.
* Quando um pool ou volume de capacidade do Azure NetApp Files for eliminado, a regra de alerta métrico correspondente será eliminada.

Pode configurar as seguintes definições de alerta de teclas:  

* **Capacidade Pool % Limiar Completo** - Esta definição determina o limiar consumido que desencadeia um alerta para os pools de capacidade. Um valor de 90 faria com que um alerta fosse desencadeado quando a capacidade da piscina atinge os 90% consumidos.
* **Volume % Limiar Total** - Esta definição determina o limiar consumido que desencadeia um alerta para volumes. Um valor de 80 faria com que um alerta fosse desencadeado quando o volume atinge os 80% consumidos.
* **Grupo de Ação existente para notificações de capacidade** - Esta configuração é o grupo de ação que será acionado para alerta baseado na capacidade. Esta definição deve ser pré-criada por si. O grupo de ação pode enviar e-mail, SMS ou outros formatos.

A seguinte ilustração mostra a configuração do alerta:  

![Ilustração que mostra configuração de alerta usando ANFCapacityManager.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

Após a instalação do ANFCapacityManager, pode esperar o seguinte comportamento: Quando um conjunto de capacidade ou volume de capacidade do Azure NetApp Files for criado, modificado ou eliminado, a Aplicação Lógica criará, modificará ou eliminará automaticamente uma regra de alerta métrico baseada na capacidade com o nome `ANF_Pool_poolname` ou `ANF_Volume_poolname_volname` . 

### <a name="manage-capacity"></a>Gerir capacidade

Além de monitorizar e alertar, deve também incorporar uma prática de gestão da capacidade de aplicação para gerir o consumo de capacidade do Azure NetApp Files (aumento) do consumo de capacidade. Quando um volume ou piscina de capacidade do Azure NetApp Files se enche, [a capacidade extra pode ser fornecida no voo sem interrupção da aplicação](azure-netapp-files-resize-capacity-pools-or-volumes.md). Esta secção descreve várias formas manuais e automatizadas de aumentar o volume e a capacidade de pool de espaço, conforme necessário.
 
#### <a name="manual"></a>Manual 

Pode utilizar o portal ou o CLI para aumentar manualmente o tamanho do volume ou da capacidade da piscina. 

##### <a name="portal"></a>Portal 

Pode [alterar o tamanho de um volume](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) se necessário. O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto.

1. A partir da lâmina de conta Manage NetApp, clique em **Volumes**.  
2. Clique com o botão direito no nome do volume que pretende redimensionar ou clicar `…` no ícone no final da linha do volume para visualizar o menu de contexto. 
3. Utilize as opções do menu de contexto para redimensionar ou eliminar o volume.   

   ![Screenshot que mostra opções de menu de contexto para um volume.](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![Screenshot que mostra a janela de quota de volume de atualização.](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

Em alguns casos, o pool de capacidade de alojamento não tem capacidade suficiente para redimensionar os volumes. No entanto, pode [alterar o tamanho da piscina](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) em incrementos ou decré-medidas de 1-TiB. O tamanho da piscina não pode ser inferior a 4 TiB. *A redimensionamento da capacidade muda a capacidade adquirida dos Ficheiros Azure NetApp.*

1. A partir da lâmina 'Gerir a Conta NetApp', clique na capacidade que pretende redimensionar.
2. Clique com o direito no nome do pool de capacidade ou clique `…` no ícone no final da linha da piscina de capacidade para exibir o menu de contexto.
3. Utilize as opções do menu de contexto para redimensionar ou eliminar o pool de capacidade.    

   ![Screenshot que mostra opções de menu de contexto para um pool de capacidade.](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![Screenshot que mostra a janela da Piscina redimensione.](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI ou PowerShell

Pode utilizar as [ferramentas CLI dos Ficheiros Azure NetApp,](azure-netapp-files-sdk-cli.md#cli-tools)incluindo o Azure CLI e o Azure PowerShell, para alterar manualmente o tamanho do volume ou da capacidade da piscina.  Os dois comandos seguintes podem ser utilizados para gerir o volume de volume e os recursos de pool do Azure NetApp:  

* [`az netappfiles pool`](/cli/azure/netappfiles/pool)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume)

Para gerir os recursos do Azure NetApp Files utilizando o Azure CLI, pode abrir o portal Azure e selecionar a ligação Azure **Cloud Shell** no topo da barra de menus: 

[![Screenshot que mostra como aceder à ligação Cloud Shell. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

Esta ação abrirá a Azure Cloud Shell:

[![Screenshot que mostra a janela Cloud Shell. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

Os seguintes exemplos utilizam os comandos para [mostrar](/cli/azure/netappfiles/volume#az-netappfiles-volume-show) e [atualizar](/cli/azure/netappfiles/volume#az-netappfiles-volume-update) o tamanho de um volume:
 
[![Screenshot que mostra usar o PowerShell para mostrar o tamanho do volume. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[![Screenshot que mostra usar o PowerShell para atualizar o tamanho do volume. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

Os seguintes exemplos utilizam os comandos para [mostrar](/cli/azure/netappfiles/pool#az-netappfiles-pool-show) e [atualizar](/cli/azure/netappfiles/pool#az-netappfiles-pool-update) o tamanho de um pool de capacidade:

[![Screenshot que mostra usar o PowerShell para mostrar o tamanho da piscina de capacidade. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[![Screenshot que mostra usar o PowerShell para atualizar o tamanho da piscina de capacidade. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>Automatizada  

Pode construir um processo automatizado para gerir o comportamento alterado.

##### <a name="rest-api"></a>API REST   

A API REST para o serviço Azure NetApp Files define operações HTTP contra recursos como a conta NetApp, o pool de capacidade, os volumes e instantâneos. A especificação REST API para ficheiros Azure NetApp é publicada através da [página GitHub do Gestor de Recursos de Ficheiros Azure NetApp].](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) Pode encontrar [código de exemplo para utilização com APIs REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) no GitHub.

Consulte [os ficheiros Azure NetApp com API REST](azure-netapp-files-develop-with-rest-api.md). 

##### <a name="rest-api-using-powershell"></a>API REST com o PowerShell  

A API REST para o serviço Azure NetApp Files define operações HTTP contra recursos como a conta NetApp, o pool de capacidade, os volumes e instantâneos. A [especificação REST API para Azure NetApp Files](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) é publicada através do GitHub.

Consulte [Os Ficheiros Azure NetApp com API REST utilizando PowerShell](develop-rest-api-powershell.md).

##### <a name="capacity-management-using-anfcapacitymanager"></a>Gestão de capacidades utilizando ANFCapacityManager

ANFCapacityManager é uma App Azure Logic que gere regras de alerta baseadas na capacidade. Aumenta automaticamente os tamanhos de volume para evitar que os volumes dos ficheiros Azure NetApp se escasseem. Além de enviar alertas, pode permitir o aumento automático de tamanhos de volume e capacidade de piscina para evitar que os volumes dos seus Ficheiros Azure NetApp se escasseem fora do espaço: 

* Opcionalmente, quando um Volume de Ficheiros Azure NetApp atinge o limiar por cento especificado consumido, a quota de volume (tamanho) será aumentada pela percentagem especificada entre 10-100.  
* Se o aumento do tamanho do volume exceder a capacidade do pool contendo capacidade, o tamanho da piscina também será aumentado para acomodar o novo tamanho de volume.

Pode configurar a seguinte definição de gestão da capacidade da chave:  

* Aumento por cento do **autogrow** - Percentagem do tamanho do volume existente para crescer automaticamente um volume se atingir o **limiar completo de %** especificado . Um valor de 0 (zero) irá desativar a função AutoGrow. Recomenda-se um valor entre 10 e 100.

    ![Screenshot que mostra definir volume de crescimento por cento.](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>FAQ 

Esta secção responde a algumas perguntas sobre a mudança de quota dura de volume. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>O espaço instantâneo conta para a capacidade utilizável ou a provisionada de um volume?

Sim, a capacidade de instantâneo consumida conta para o espaço a provisionado no volume. Caso o volume esteja cheio, considere duas opções de reparação:

* Redimensione o volume tal como descrito neste artigo.
* Remova as imagens mais antigas para libertar espaço no volume de hospedagem.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>Esta alteração significa que o comportamento de crescimento automático do volume desaparecerá dos Ficheiros Azure NetApp?

Um equívoco comum é que os volumes de *Ficheiros* Azure NetApp cresceriam automaticamente ao encherem-se. Os volumes foram pouco abastecados com um tamanho de 100 TiB, independentemente da quota definida real, enquanto o *pool de capacidade* de subposição cresceria automaticamente com incrementos de 1-TiB. Esta alteração abordará o tamanho do *volume* (visível e utilizável) para a quota definida, e os *pools de capacidade* deixarão de crescer automaticamente. Esta alteração resulta em espaço preciso do lado do cliente e no relato de capacidade. Evita o consumo de capacidade "em fuga".

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>Esta alteração tem algum efeito nos volumes replicados com replicação entre regiões (pré-visualização)? 

A quota de volume rígido não é aplicada nos volumes de destino de replicação.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>Esta alteração tem algum efeito sobre as métricas atualmente disponíveis no Azure Monitor?

As estatísticas do Portal e do Azure Monitor refletirão com precisão o novo modelo de alocação e utilização.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>Esta alteração tem algum efeito nos limites de recursos para ficheiros Azure NetApp?

Não existe qualquer alteração nos limites de recursos para ficheiros Azure NetApp para além das alterações de quota descritas neste artigo.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>Existe um exemplo de fluxo de trabalho da ANFCapacityManager?  

Sim. Consulte a [página Volume AutoGrow Workflow Example GitHub](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md).

### <a name="is-anfcapacitymanager-microsoft-supported"></a>A ANFCapacityManager Microsoft está suportada?  

[A aplicação lógica ANFCapacityManager é fornecida como é e não é suportada pelo NetApp ou microsoft](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer). É encorajado a modificar para se adaptar ao seu ambiente ou requisitos específicos. Deve testar a funcionalidade antes de a implementar em qualquer ambiente crítico ou de produção.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>Como posso reportar um bug ou enviar um pedido de recurso para ANFCapacityManger?
Pode submeter bugs e pedidos de funcionalidade clicando em **Nova Emissão** na [página DO GitHub da AnFCapacityManager](https://github.com/ANFTechTeam/ANFCapacityManager/issues).

## <a name="next-steps"></a>Passos seguintes
* [Redimensionar um conjunto de capacidade ou um volume](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Métricas do Azure NetApp Files](azure-netapp-files-metrics.md)
