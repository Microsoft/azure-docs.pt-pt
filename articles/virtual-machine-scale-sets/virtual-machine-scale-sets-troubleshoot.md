---
title: Escala automática de resolução de problemas com conjuntos de escala de máquina virtual
description: Resolução de problemas auto-escala com conjuntos de escala de máquina virtual. Compreender os problemas típicos encontrados e como resolvê-los.
author: avirishuv
ms.author: avverma
ms.topic: troubleshooting
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.reviwer: jushiman
ms.custom: avverma
ms.openlocfilehash: 11302c301bee466f678d544d0c4838c39cec9c8e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91818529"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Resolver problemas de dimensionamento automático em Conjuntos de Dimensionamento de Máquinas Virtuais
**Problema** – criou uma infraestrutura de autoscalização no Azure Resource Manager utilizando conjuntos de escala de máquinas virtuais – por exemplo, ao implementar um modelo como este: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale  – tem as suas regras de escala definidas e funciona muito bem, exceto que por mais carga que coloque nos VMs, não se autoescala.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
Algumas coisas a considerar incluem:

* Quantos vCPUs cada VM tem, e está a carregar cada vCPU?
  A amostra anterior Azure Quickstart modelo tem um script do_work.php, que carrega um único vCPU. Se estiver a utilizar um VM maior do que um tamanho VM de um único vCPU como Standard_A1 ou D1, terá de executar esta carga várias vezes. Verifique quantos vCPUs para os seus VMs, analisando [tamanhos para máquinas virtuais Windows em Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Quantos VMs no conjunto de escala de máquina virtual, está a trabalhar em cada um deles?
  
    Um evento de escala só ocorre quando o CPU médio em **todos os** VMs em uma escala definida excede o valor limiar, ao longo do tempo definido nas regras de autoescala.
* Perdeu algum evento de escala?
  
    Consulte os registos de auditoria no portal Azure para ver se há eventos de escala. Talvez houvesse uma escala para cima e uma escala para baixo que não tinha sido. Pode filtrar por "Escala".
  
    ![Registos de Auditoria][audit]
* Os seus limiares de escala e escala são suficientemente diferentes?
  
    Suponha que estabeleça uma regra para escalar quando o CPU médio é superior a 50% em cinco minutos, e para escalar quando a CPU média é inferior a 50%. Esta definição provocaria um problema de "flaping" quando a utilização do CPU está próxima do limiar, com as ações de escala constantemente a aumentar e a diminuir o tamanho do conjunto. Devido a esta definição, o serviço de autoescala tenta evitar "bater palmas", o que pode manifestar-se como não escalonamento. Por isso, certifique-se de que os seus limiares de escala e de escala são suficientemente diferentes para permitir algum espaço entre a escala.
* Escreveu o seu próprio modelo JSON?
  
    É fácil cometer erros, por isso comece com um modelo como o acima que está provado funcionar, e fazer pequenas mudanças incrementais. 
* Pode entrar ou sair manualmente?
  
    Tente recolocar o recurso de conjunto de escala de máquina virtual com uma definição de "capacidade" diferente para alterar manualmente o número de VMs. Um modelo de exemplo está aqui: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – pode ser necessário editar o modelo para se certificar de que tem o mesmo tamanho da máquina que o seu Conjunto de Escalas utiliza. Se conseguir alterar manualmente o número de VMs, sabe que o problema está isolado para a autoescala.
* Consulte os recursos microsoft.Compute/virtualMachineScaleSet e Microsoft.Insights no [Azure Resource Explorer](https://resources.azure.com/)
  
    O Azure Resource Explorer é uma ferramenta indispensável para a resolução de problemas que lhe mostra o estado dos recursos do seu Gestor de Recursos Azure. Clique na sua subscrição e veja o Grupo de Recursos que está a resolver problemas. No fornecedor de recursos Compute, consulte o conjunto de escala de máquina virtual que criou e verifique a Visão de Instância, que mostra o estado de uma implementação. Verifique também a visão de exemplo dos VMs no conjunto de escala de máquina virtual. Em seguida, vá ao fornecedor de recursos Microsoft.Insights e verifique se as regras de autoescala parecem certas.
* A extensão de Diagnóstico está a funcionar e a emitir dados de desempenho?
  
    **Atualização:** A escala automática Azure foi melhorada para usar um pipeline de métricas baseado em hospedeiro, que já não requer uma extensão de diagnóstico para ser instalado. Os próximos parágrafos já não se aplicam se criar uma aplicação de autoscalagem utilizando o novo oleoduto. Um exemplo de modelos Azure que foram convertidos para usar o gasoduto hospedeiro está disponível aqui: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale . 
  
    A utilização de métricas baseadas em hospedeiros para autoescala é melhor pelas seguintes razões:
  
  * Menos peças móveis, uma vez que não é necessário instalar extensões de diagnóstico.
  * Modelos mais simples. Basta adicionar insights regras de escala automática a um modelo de conjunto de escala existente.
  * Relatórios mais fiáveis e lançamento mais rápido de novos VMs.
    
    A única razão pela qual pode querer continuar a utilizar uma extensão de diagnóstico é se precisar de relatórios/dimensionamento de diagnósticos de memória. As métricas baseadas no hospedeiro não reportam memória.
    
    Com isso em mente, só siga o resto deste artigo se estiver a usar extensões de diagnóstico para a sua autoscalagem.
    
    A autoescalação no Azure Resource Manager pode funcionar (mas já não tem de funcionar) através de uma extensão VM chamada extensão de diagnóstico. Emite dados de desempenho a uma conta de armazenamento que define no modelo. Estes dados são então agregados pelo serviço Azure Monitor.
    
    Se o serviço Insights não conseguir ler dados dos VMs, é suposto enviar-lhe um e-mail. Por exemplo, recebes um e-mail se os VMs estiverem em baixo. Certifique-se de verificar o seu e-mail, no endereço de e-mail especificado quando criou a sua conta Azure.
    
    Também pode olhar para os dados por si mesmo. Veja a conta de armazenamento Azure usando um explorador de nuvem. Por exemplo, utilizando [o Visual Studio Cloud Explorer,](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2)inicie sessão e escolha a subscrição Azure que está a utilizar. Em seguida, consulte o nome da conta de armazenamento de Diagnóstico referenciado na definição de extensão de Diagnóstico no seu modelo de implementação.
    
    ![Cloud Explorer][explorer]
    
    Vê-se um monte de tabelas onde os dados de cada VM estão a ser armazenados. Tomando linux e a métrica do CPU como exemplo, veja as linhas mais recentes. O Visual Studio Cloud Explorer suporta uma linguagem de consulta para que possa executar uma consulta. Por exemplo, pode executar uma consulta para "Timestamp gt datetime'2016-02-02T21:20:00Z'" para se certificar de que obtém os eventos mais recentes. O intervalo corresponde à UTC. Os dados que vê aí correspondem às regras de escala que criou? No exemplo seguinte, o CPU para a máquina 20 começou a aumentar para 100% nos últimos cinco minutos.
    
    ![Mesas de Armazenamento][tables]
    
    Se os dados não estiverem lá, implica que o problema está com a extensão de diagnóstico em execução nos VMs. Se os dados estiverem lá, isso implica que existe ou um problema com as suas regras de escala, ou com o serviço Insights. Verifique [o estado do Azure](https://azure.microsoft.com/status/).
    
    Uma vez que você tenha passado por estes passos, se você ainda estiver com problemas de autoescala, você pode experimentar os seguintes recursos: 
    * Leia os fóruns no [Microsoft Q&Uma página de perguntas,](/answers/topics/azure-virtual-machines.html)ou Stack [transbordar](https://stackoverflow.com/questions/tagged/azure) 
    * Faça um registo de uma chamada de apoio. Esteja preparado para partilhar o modelo e uma visão dos seus dados de desempenho.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
