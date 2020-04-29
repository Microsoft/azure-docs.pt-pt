---
title: Escala automática de resolução de problemas com conjuntos de escala de máquina virtual
description: Escala automática de sessão de problemas com conjuntos de escala de máquina virtual. Compreender os problemas típicos encontrados e como resolvê-los.
author: mimckitt
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: windows
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: mimckitt
ms.openlocfilehash: 4bc5e66f5b0759bdb5fe34276369161200bd5442
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273380"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Resolver problemas de dimensionamento automático em Conjuntos de Dimensionamento de Máquinas Virtuais
**Problema** – criou uma infraestrutura de autoscalcificação no Azure Resource Manager utilizando conjuntos de escala https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale de máquinas virtuais – por exemplo, ao implementar um modelo como este: – tem as suas regras de escala definidas e funciona muito bem, exceto por mais carga que coloque nos VMs, não faz escala automática.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
Algumas coisas a considerar incluem:

* Quantos vCPUs cada VM tem, e está a carregar cada vCPU?
  A amostra anterior do modelo Azure Quickstart tem um roteiro do_work.php, que carrega um único vCPU. Se estiver a usar um VM maior do que um VM de um único vCPU como Standard_A1 ou D1, terá de executar esta carga várias vezes. Verifique quantos vCPUs para os seus VMs, revendo [Tamanhos para máquinas virtuais Windows em Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Quantos VMs no conjunto de máquinas virtuais, estás a trabalhar em cada um?
  
    Um evento de escala só ocorre quando o CPU médio em **todos os** VMs numa escala definida excede o valor limiar, ao longo do tempo interno definido nas regras de escala automática.
* Perdeu algum evento à escala?
  
    Verifique os registos de auditoria no portal Azure para obter eventos de escala. Talvez tenha havido uma subida e uma descida que não foi perdida. Pode filtrar por "Escala".
  
    ![Registos de Auditoria][audit]
* Os seus limiares de escala e escala são suficientemente diferentes?
  
    Suponha que estabeleça uma regra para escalar quando a CPU média é superior a 50% em cinco minutos, e escalar quando a CPU média é inferior a 50%. Esta definição provocaria um problema de "bater palmas" quando a utilização do CPU está próxima do limiar, com as ações de escala a aumentarem constantemente e a diminuirem o tamanho do conjunto. Devido a esta configuração, o serviço de escala automática tenta evitar o "bater", que pode manifestar-se como não escalar. Por isso, certifique-se de que os seus limiares de escala e escala são suficientemente diferentes para permitir algum espaço entre a escala.
* Escreveu o seu próprio modelo JSON?
  
    É fácil cometer erros, por isso comece com um modelo como o acima que está provado funcionar, e faça pequenas mudanças incrementais. 
* Pode entrar ou sair manualmente?
  
    Tente recolocar o recurso conjunto de conjunto de máquinas virtuais com uma definição de "capacidade" diferente para alterar manualmente o número de VMs. Um modelo de https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing exemplo está aqui: – você pode precisar editar o modelo para se certificar de que tem o mesmo tamanho da máquina que o seu Conjunto de Escala saem. Se conseguir alterar manualmente o número de VMs, então sabe que o problema está isolado à escala automática.
* Verifique os recursos Microsoft.Compute/virtualMachineScaleSet e Microsoft.Insights no [Azure Resource Explorer](https://resources.azure.com/)
  
    O Azure Resource Explorer é uma ferramenta indispensável de resolução de problemas que lhe mostra o estado dos seus recursos do Gestor de Recursos Azure. Clique na sua subscrição e veja o Grupo de Recursos que está a resolver problemas. Sob o fornecedor de recursos Compute, olhe para o conjunto de escala de máquina virtual que criou e verifique a Vista de Instância, que mostra o estado de uma implementação. Além disso, verifique a visão de exemplo dos VMs no conjunto de escala de máquina virtual. Em seguida, entre no fornecedor de recursos Microsoft.Insights e verifique se as regras de escala automática parecem certas.
* A extensão de diagnóstico está a funcionar e a emitir dados de desempenho?
  
    **Atualização:** A escala automática Azure foi melhorada para utilizar um gasoduto de métricas baseado no hospedeiro, que já não requer uma extensão de diagnóstico para ser instalado. Os próximos parágrafos já não se aplicam se criar uma aplicação autoscalcificante utilizando o novo pipeline. Um exemplo de modelos Azure que foram convertidos para https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscaleutilizar o gasoduto hospedeiro está disponível aqui: . 
  
    A utilização de métricas baseadas em hospedeiros para escala automática é melhor para as seguintes razões:
  
  * Menos peças móveis, uma vez que não é necessário instalar extensões de diagnóstico.
  * Modelos mais simples. Basta adicionar insights regras de escala automática a um modelo de conjunto de escala existente.
  * Relatórios mais fiáveis e lançamento mais rápido de novos VMs.
    
    A única razão pela qual pode continuar a utilizar uma extensão de diagnóstico é se precisar de relatórios/escalas de diagnóstico de memória. As métricas baseadas no hospedeiro não reportam a memória.
    
    Com isso em mente, basta seguir o resto deste artigo se estiver a usar extensões de diagnóstico para a sua autoscalcificação.
    
    A escala automática em Azure Resource Manager pode funcionar (mas já não tem de o fazer) através de uma extensão VM chamada Extensão de Diagnósticos. Emite dados de desempenho para uma conta de armazenamento que define no modelo. Estes dados são então agregados pelo serviço Azure Monitor.
    
    Se o serviço Insights não conseguir ler os dados dos VMs, é suposto enviar-lhe um e-mail. Por exemplo, recebe um e-mail se os VMs estiverem em baixo. Certifique-se de verificar o seu e-mail, no endereço de e-mail que especificou quando criou a sua conta Azure.
    
    Também pode olhar para os dados. Veja a conta de armazenamento do Azure usando um explorador de nuvens. Por exemplo, utilizando o [Visual Studio Cloud Explorer,](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2)faça login e escolha a subscrição Azure que está a utilizar. Em seguida, veja o nome da conta de armazenamento de Diagnóstico referenciado na definição de extensão de Diagnóstico no seu modelo de implementação.
    
    ![Cloud Explorer][explorer]
    
    Você vê um monte de tabelas onde os dados de cada VM estão sendo armazenados. Tomando linux e a métrica do CPU como exemplo, olhe para as linhas mais recentes. O explorador de nuvem do Estúdio Visual suporta uma linguagem de consulta para que possa fazer uma consulta. Por exemplo, pode fazer uma consulta para "Timestamp gt datetime'2016-02-02T21:20:00Z'" para garantir que obtém os eventos mais recentes. O fuso horário corresponde à UTC. Os dados que vê lá dentro correspondem às regras de escala que criou? No exemplo seguinte, o CPU da máquina 20 começou a aumentar para 100% nos últimos cinco minutos.
    
    ![Mesas de Armazenamento][tables]
    
    Se os dados não estiverem lá, implica que o problema está com a extensão de diagnóstico em execução nos VMs. Se os dados estiverem lá, implica que há um problema com as suas regras de escala, ou com o serviço Insights. Verifique o [estado do Azure](https://azure.microsoft.com/status/).
    
    Depois de passar por estes degraus, se ainda tiver problemas de escala automática, pode experimentar os seguintes recursos: 
    * Leia os fóruns na [MSDN,](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows)ou [Stack transbordar](https://stackoverflow.com/questions/tagged/azure) 
    * Faça uma chamada de apoio. Esteja preparado para partilhar o modelo e uma visão dos seus dados de desempenho.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
