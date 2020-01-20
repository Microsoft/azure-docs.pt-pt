---
title: Solucionar problemas de dimensionamento automático com conjuntos de escala de máquina virtual
description: Solucionar problemas de dimensionamento automático com conjuntos de escala de máquina virtual. Entenda os problemas típicos encontrados e como resolvê-los.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: windows
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 923967a902f611ce845fbdc096fd2c02e681bb6e
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76272437"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Solucionando problemas de dimensionamento automático com conjuntos de dimensionamento de máquinas virtuais
**Problema** – você criou uma infraestrutura de dimensionamento automático em Azure Resource Manager usando conjuntos de dimensionamento de máquinas virtuais, por exemplo, implantando um modelo como este: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – você tem suas regras de dimensionamento definidas e funciona muito bem, exceto que a carga que você colocou nas VMs não faz dimensionamento automático.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas
Alguns itens a serem considerados incluem:

* Quantas vCPUs cada VM tem, e você está carregando cada vCPU?
  O modelo de início rápido do Azure de exemplo anterior tem um script do_work. php, que carrega um único vCPU. Se você estiver usando uma VM maior do que um tamanho de VM de vCPU único como Standard_A1 ou D1, precisará executar essa carga várias vezes. Verificar quantas vCPUs para suas VMs revisando [tamanhos das máquinas virtuais do Windows no Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Quantas VMs no conjunto de dimensionamento de máquinas virtuais você está trabalhando em cada uma delas?
  
    Um evento de expansão só ocorre quando a média de CPU em **todas** as VMs em um conjunto de dimensionamento excede o valor de limite, ao longo do tempo interno definido nas regras de dimensionamento automático.
* Você perdeu algum evento de escala?
  
    Verifique os logs de auditoria no portal do Azure para eventos de escala. Talvez houvesse uma escala vertical e uma redução para baixo que foi perdida. Você pode filtrar por "escala".
  
    ![Registos de Auditoria][audit]
* Os limites de redução e expansão são suficientemente diferentes?
  
    Suponha que você defina uma regra para escalar horizontalmente quando a média da CPU for maior que 50% em cinco minutos e para reduzir quando a média da CPU for menor que 50%. Essa configuração causaria um problema de "oscilação" quando o uso da CPU estiver perto do limite, com ações de escala constantemente aumentando e diminuindo o tamanho do conjunto. Devido a essa configuração, o serviço de dimensionamento automático tenta evitar "oscilação", que pode ser manifestado como não dimensionamento. Portanto, certifique-se de que os limites de escala e redução sejam suficientemente diferentes para permitir algum espaço entre o dimensionamento.
* Você escreveu seu próprio modelo JSON?
  
    É fácil cometer erros, portanto, comece com um modelo como aquele acima, que é comprovado para funcionar e faça pequenas alterações incrementais. 
* Você pode reduzir ou reduzir manualmente?
  
    Tente reimplantar o recurso de conjunto de dimensionamento de máquinas virtuais com uma configuração de "capacidade" diferente para alterar o número de VMs manualmente. Um modelo de exemplo está aqui: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – talvez seja necessário editar o modelo para verificar se ele tem o mesmo tamanho de máquina que o conjunto de dimensionamento usa. Se for possível alterar o número de VMs manualmente, você saberá que o problema está isolado no dimensionamento automático.
* Verifique seus recursos Microsoft. Compute/virtualMachineScaleSet e Microsoft. insights no [Azure Resource Explorer](https://resources.azure.com/)
  
    O Azure Resource Explorer é uma ferramenta de solução de problemas indispensável que mostra o estado de seus recursos de Azure Resource Manager. Clique em sua assinatura e examine o grupo de recursos que você está solucionando. No provedor de recursos de computação, examine o conjunto de dimensionamento de máquinas virtuais que você criou e verifique o modo de exibição de instância, que mostra o estado de uma implantação. Além disso, verifique a exibição de instância das VMs no conjunto de dimensionamento de máquinas virtuais. Em seguida, vá para o provedor de recursos Microsoft. insights e verifique se as regras de dimensionamento automático parecem corretas.
* A extensão de diagnóstico está funcionando e emitindo dados de desempenho?
  
    **Atualização:** O dimensionamento automático do Azure foi aprimorado para usar um pipeline de métricas baseado em host, que não requer mais uma extensão de diagnóstico para ser instalada. Os próximos parágrafos não se aplicarão mais se você criar um aplicativo de dimensionamento automático usando o novo pipeline. Um exemplo de modelos do Azure que foram convertidos para usar o pipeline de host está disponível aqui: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    O uso de métricas baseadas em host para dimensionamento automático é melhor pelos seguintes motivos:
  
  * Menos partes móveis como nenhuma extensão de diagnóstico precisam ser instaladas.
  * Modelos mais simples. Basta adicionar Insight regras de dimensionamento automático a um modelo de conjunto de dimensionamento existente.
  * Relatórios mais confiáveis e lançamento mais rápido de novas VMs.
    
    Os únicos motivos que você pode querer manter usando uma extensão de diagnóstico é se você precisar de relatórios/dimensionamento de diagnóstico de memória. As métricas baseadas em host não relatam memória.
    
    Com isso em mente, só siga o restante deste artigo se você estiver usando as extensões de diagnóstico para o dimensionamento automático.
    
    O dimensionamento automático no Azure Resource Manager pode funcionar (mas não precisa mais) por meio de uma extensão de VM chamada de extensão de diagnóstico. Ele emite dados de desempenho para uma conta de armazenamento que você define no modelo. Esses dados são então agregados pelo serviço de Azure Monitor.
    
    Se o serviço de informações não conseguir ler os dados das VMs, ele deverá enviar um email para você. Por exemplo, você receberá um email se as VMs estiverem inativas. Certifique-se de verificar seu email, no endereço de email especificado quando você criou sua conta do Azure.
    
    Você também pode examinar os dados por conta própria. Examine a conta de armazenamento do Azure usando um Cloud Explorer. Por exemplo, usando o [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), faça logon e escolha a assinatura do Azure que você está usando. Em seguida, examine o nome da conta de armazenamento de diagnóstico referenciada na definição de extensão de diagnóstico em seu modelo de implantação.
    
    ![Cloud Explorer][explorer]
    
    Você vê um monte de tabelas em que os dados de cada VM estão sendo armazenados. Usando o Linux e a métrica de CPU como exemplo, examine as linhas mais recentes. O Visual Studio Cloud Explorer dá suporte a uma linguagem de consulta para que você possa executar uma consulta. Por exemplo, você pode executar uma consulta para "timestamp gt DateTime ' 2016-02-02T21:20:00Z '" para certificar-se de obter os eventos mais recentes. O fuso horário corresponde ao UTC. Os dados que você vê ali correspondem às regras de dimensionamento que você configurou? No exemplo a seguir, a CPU para o computador 20 começou a aumentar para 100% nos últimos cinco minutos.
    
    ![Tabelas de Armazenamento][tables]
    
    Se os dados não estiverem lá, isso implicará que o problema está na extensão de diagnóstico em execução nas VMs. Se os dados estiverem lá, isso implica que há um problema com as regras de dimensionamento ou com o serviço insights. Verifique o [status do Azure](https://azure.microsoft.com/status/).
    
    Depois de seguir essas etapas, se você ainda tiver problemas de dimensionamento automático, poderá experimentar os seguintes recursos: 
    * Leia os fóruns no [msdn](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows)ou [Stack Overflow](https://stackoverflow.com/questions/tagged/azure) 
    * Registre uma chamada de suporte. Esteja preparado para compartilhar o modelo e uma exibição de seus dados de desempenho.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
