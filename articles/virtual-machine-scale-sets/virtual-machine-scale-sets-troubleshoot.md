---
title: Resolver problemas de dimensionamento automático com conjuntos de dimensionamento de máquinas virtuais | Documentos da Microsoft
description: Resolver problemas de dimensionamento automático com conjuntos de dimensionamento de Máquina Virtual. Compreenda os problemas comuns encontrados e como resolvê-los.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: windows
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 3308b22606e87853aad7e3d3a3995aab8d1b5401
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005310"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Resolução de problemas de dimensionamento automático com conjuntos de dimensionamento de máquinas virtuais
**Problema** – criar uma infraestrutura de dimensionamento automático no Azure Resource Manager com conjuntos de dimensionamento de máquinas virtuais – por exemplo, ao implementar um modelo como este: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – tiver suas regras de dimensionamento definidas e ele funciona muito bem, exceto não é importante que coloca nas VMs de carga, ela não o dimensionamento automático.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
Algumas coisas a serem considerados incluem:

* O número de vCPUs cada VM tem e está a carregar cada vCPU?
  O modelo de início rápido do Azure de exemplo anterior tem um script de do_work.php, que carrega um único vCPU. Se estiver a utilizar uma VM maior do que um tamanho de VM única vCPU como Standard_A1 ou D1, terá de executar esta carga de várias vezes. Verifique o número de vCPUs para as VMs ao rever [máquinas de virtuais de tamanhos para Windows no Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* O número de VMs no conjunto de dimensionamento de máquina virtual, estamos a trabalhar em cada um deles?
  
    Um evento de escalamento horizontal só ocorre quando a CPU média entre **todos os** as VMs num conjunto de dimensionamento excede o valor do limiar, ao longo do tempo interno definido nas regras de dimensionamento automático.
* Perdeu-se de quaisquer eventos de dimensionamento?
  
    Verifique os registos de auditoria no portal do Azure para eventos de dimensionamento. Talvez tenha havido um dimensionamento de cópia de segurança e dimensionamento para baixo do que se perdeu. Pode filtrar por "Dimensionamento".
  
    ![Registos de Auditoria][audit]
* Seus limites de redução horizontal e Escalamento horizontal são diferentes o suficiente?
  
    Suponha que define uma regra para aumentar horizontalmente quando a CPU média é superior a 50% mais de cinco minutos bem como para reduzir horizontalmente quando a CPU média é inferior a 50%. Esta definição, faria com que um problema de "flapping" quando a utilização da CPU ao limiar, com ações de dimensionamento, aumentar e diminuir o tamanho do conjunto de constantemente. Devido a esta definição, o serviço de dimensionamento automático tenta evitar "em oscilação", que podem se manifestar como no aumento não. Por conseguinte, certifique-se de que seus limites de expansão e redução horizontal são diferentes o suficiente para permitir que algum espaço entre o dimensionamento.
* Escrever seu próprio modelo JSON?
  
    É fácil cometer erros, portanto, comece com um modelo como um acima, que seja comprovadamente trabalhar e fazer pequenas alterações incrementais. 
* Pode manualmente aumentar e reduzir?
  
    Tente voltar a implementar que o dimensionamento de máquinas virtuais conjunto de recursos com uma diferente "capacidade" na definição de alterar manualmente o número de VMs. Um modelo de exemplo está aqui: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – poderá ter de editar o modelo para se certificar de que possui o mesmo tamanho de máquina, conforme o seu conjunto de dimensionamento utiliza. Se com êxito pode alterar o número de VMs manualmente, sabe, em seguida, que o problema está isolado do dimensionamento automático.
* Verifique sua Microsoft.Compute/virtualMachineScaleSet e os recursos de Microsoft. insights o [Explorador de recursos do Azure](https://resources.azure.com/)
  
    O Explorador de recursos do Azure é uma ferramenta de resolução de problemas indispensável que mostra o estado dos seus recursos do Azure Resource Manager. Clique na sua subscrição e ver o grupo de recursos que esteja a resolver. Sob o fornecedor de recursos de computação, ver o conjunto de dimensionamento de máquina virtual que criou e verificar a exibição de instância, que mostra o estado de uma implementação. Além disso, verifique a vista de instância de VMs no conjunto de dimensionamento de máquina virtual. Em seguida, vá até o fornecedor de recursos Microsoft. insights e verificar que as regras de dimensionamento automático parecem corretas.
* A extensão de diagnóstico é trabalhar e emissão de dados de desempenho?
  
    **Update:** Dimensionamento automático do Azure foi aprimorado para utilizar um pipeline de métricas baseadas no anfitrião, o que já não necessita de uma extensão de diagnóstico para ser instalado. Próximos parágrafos já não se aplicam se criar uma aplicação de dimensionamento automático com o novo pipeline. Um exemplo de modelos do Azure que foram convertidos para utilizar o pipeline de anfitrião está disponível aqui: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    Utilizar as métricas baseadas no anfitrião do dimensionamento automático é o melhor pelos seguintes motivos:
  
  * Menos partes móveis como nenhuma extensão de diagnóstico tem de ser instalado.
  * Modelos mais simples. Basta adicione regras de dimensionamento automático de informações a um modelo de conjunto de dimensionamento existente.
  * Relatórios mais confiável e inicialização mais rápido de novas VMs.
    
    Os motivos pelos quais só poderá continuar a utilizar uma extensão de diagnóstico é se tem memória diagnóstico reporting/dimensionamento. As métricas baseadas no anfitrião não comuniquem a memória.
    
    Com isso em mente, apenas siga o resto deste artigo se estiver a utilizar extensões de diagnóstico para o seu dimensionamento automático.
    
    Dimensionamento automático no Azure Resource Manager podem trabalhar (mas não precisa mais) por meio de uma VM extensão chamado a extensão de diagnóstico. Emite dados de desempenho para uma conta de armazenamento, que define no modelo. Estes dados, em seguida, são agregados pelo serviço do Azure Monitor.
    
    Se o serviço de informações não é possível ler os dados das VMs, que deveria para enviar-lhe uma mensagem de e-mail. Por exemplo, receberá um e-mail se as VMs estão indisponíveis. Certifique-se de que verificar o seu e-mail, para o endereço de e-mail que especificou quando criou a conta do Azure.
    
    Também pode ver os dados por conta própria. Ver a conta de armazenamento do Azure com o Explorador de cloud. Por exemplo, utilizando o [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), inicie sessão e escolha a subscrição do Azure que está a utilizar. Em seguida, ver o nome de conta de armazenamento de diagnóstico referenciado na definição da extensão de diagnóstico no seu modelo de implementação.
    
    ![Cloud Explorer][explorer]
    
    Verá uma série de tabelas em que os dados a partir de cada VM estão a ser armazenados. Levando o Linux e a métrica de CPU, por exemplo, ver o que é mais recentes linhas. O cloud explorer do Visual Studio suporta uma linguagem de consulta, pode executar uma consulta. Por exemplo, pode executar uma consulta para "Timestamp gt datetime'2016-02-02T21:20:00Z'" para se certificar de que obtém os eventos mais recentes. O fuso horário corresponde a UTC. O dados que pode ver na lá correspondem às regras de dimensionamento tudo? No exemplo seguinte, a CPU para a máquina 20 ao aumento de 100% durante os últimos cinco minutos.
    
    ![Tabelas de armazenamento][tables]
    
    Se os dados não estiverem lá, isso implica que o problema é com a extensão de diagnóstico em execução nas VMs. Se os dados estão lá, implica que existe um problema com as regras de dimensionamento ou com o serviço de informações. Verifique [estado do Azure](https://azure.microsoft.com/status/).
    
    Assim que estiver estes passos, se ainda estiver a ter problemas de dimensionamento automático, pode tentar os seguintes recursos: 
    * Continue a ler os fóruns [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows), ou [estouro de pilha](https://stackoverflow.com/questions/tagged/azure) 
    * Inicie a sessão de uma chamada de suporte. Esteja preparado para compartilhar o modelo e uma vista dos seus dados de desempenho.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
