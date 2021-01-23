---
title: Saúde de Recursos para Serviços em Nuvem (Clássico)
description: Este artigo fala sobre o Suporte ao Controlo de Saúde de Recursos (RHC) para os Serviços de Nuvem do Microsoft Azure (Clássico)
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 37294e681066eb27ace69bcacee3a813b750b8eb
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743513"
---
# <a name="resource-health-check-rhc-support-for-azure-cloud-services-classic"></a>Suporte para serviços de cloud Azure (Clássico)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Este artigo fala sobre o Suporte ao Controlo de Saúde de Recursos (RHC) para [os Serviços de Nuvem do Microsoft Azure (Clássico)](https://azure.microsoft.com/services/cloud-services)

[A Azure Resource Health](../service-health/resource-health-overview.md) para serviços na nuvem ajuda-o a diagnosticar e a obter suporte para problemas de serviço que afetam a sua implementação do Serviço cloud, funções & Exemplos de Função. Relata a saúde atual e passada dos seus serviços em nuvem no nível de Implementação, Role & Role Instance.

Relatórios de estado do Azure sobre problemas que afetam um vasto conjunto de clientes da Azure. A Resource Health dá-lhe um dashboard personalizado da saúde dos seus recursos. A Resource Health mostra todas as vezes que os seus recursos não estiveram disponíveis devido a problemas de serviço da Azure. Estes dados facilitam-lhe a mente ver se um SLA foi violado.

:::image type="content" source="media/cloud-services-allocation-failure/rhc-blade-cloud-services.png" alt-text="A imagem mostra a lâmina de verificação de saúde de recursos no portal Azure.":::

## <a name="how-health-is-checked-and-reported"></a>Como a saúde é verificada e reportada?
A saúde dos recursos é reportada a um nível de implantação ou de função. O exame de saúde acontece ao nível da instância de função, agregamos o estatuto e reportamos-no ao nível da função. Por exemplo, Se todas as instâncias de papel estiverem disponíveis, então o estatuto de papel está disponível. Do mesmo modo, agregamos o estado de saúde de todas as funções e reportamos-no a nível de implantação. Por exemplo, Se todas as funções estiverem disponíveis, o estado de implantação fica disponível. 

## <a name="why-i-cannot-see-health-status-for-my-staging-slot-deployment"></a>Por que não vejo o estado de saúde para a minha colocação de slot?
Os controlos de saúde dos recursos apenas funcionam para a implantação de faixas horárias de produção. A colocação de slot slot ainda não está suportada. 

## <a name="does-resource-health-check-also-check-the-health-of-the-application"></a>O Controlo de Saúde dos Recursos também verifica a saúde da aplicação?
Não, o exame de saúde só acontece em casos de função e não monitoriza a saúde da aplicação. Por exemplo, Mesmo que 1 em 3 instâncias de função não sejam saudáveis, a aplicação ainda pode estar disponível. O RHC não utiliza [sondas de balançadores](../load-balancer/load-balancer-custom-probe-overview.md) de carga ou sonda de agente convidado. Por isso, os Clientes devem continuar a utilizar sondas de balançadores de carga para monitorizar a saúde da sua aplicação. 

## <a name="what-are-the-annotations-for-cloud-services"></a>Quais são as anotações para os Serviços cloud?
As anotações são o estado de saúde da implantação ou funções. Existem anotações diferentes baseadas no estado de saúde, razão para a mudança de estado, etc. 

## <a name="what-does-it-mean-by-role-instance-being-unavailable"></a>O que significa que a Role Instance está "indisponível"?
Isto significa que a instância de papel não está a emitir um sinal saudável para a plataforma. Verifique o estado da instância de função para obter uma explicação detalhada do motivo pelo qual o sinal saudável não está a ser emitido.

## <a name="what-does-it-mean-by-deployment-being-unknown"></a>O que significa ser "desconhecido"?
Desconhecido significa que a saúde agregada da implantação do Serviço de Nuvem não pode ser determinada. Normalmente isto indica que ou não existe uma implementação de produção criada para o Cloud Service, a implementação foi recentemente criada (e que o Azure está a começar a recolher eventos de saúde), ou a plataforma está a ter problemas em recolher eventos de saúde para esta implementação.

## <a name="why-does-role-instance-annotations-mentions-vms-instead-of-role-instances"></a>Porque é que as anotações de instância de papel mencionam VMs em vez de Instâncias de Papel?
Uma vez que as instâncias de função são basicamente VMs e o exame de saúde para VMs é reutilizado para instâncias de função, o termo VM é usado para representar Instâncias de Papel. 

## <a name="cloud-services-deployment-level-annotations--their-meanings"></a>Notações de serviços em nuvem (nível de implantação) & os seus significados
| Anotação | Descrição | 
| --- | --- | 
| Disponível| Não existem problemas conhecidos da plataforma Azure que afetem esta implementação do Cloud Service |
| Desconhecido | Atualmente somos incapazes de determinar a saúde desta implementação do Serviço cloud | 
| Criação de Saúde de Recursos | Criação de saúde de recursos para este recurso. A saúde dos recursos observa os seus recursos Azure para fornecer detalhes sobre eventos em curso e passados que os impactaram|
| Degradado | A sua implementação do Serviço Cloud está degradada. Estamos a trabalhar para recuperar automaticamente a sua implementação do Serviço Cloud e para determinar a origem do problema. Não é necessária nenhuma ação adicional de si neste momento. |
| Mau estado de funcionamento | A sua implementação no Cloud Service não é saudável porque {0} as instâncias de {1} função não estão disponíveis |
| Degradado | A sua implementação do Cloud Service está degradada porque {0} fora das situações de {1} papel não estão disponíveis | 
| Disponível e talvez impactado | A sua implementação do Cloud Service está a decorrer, no entanto uma falha de serviço Azure em curso pode impedir-lhe de se ligar ao mesmo. A conectividade será restaurada assim que a paralisação for resolvida |
| Indisponível e talvez impactado | A saúde desta implementação do Cloud Service pode ser afetada por uma falha de serviço da Azure. A sua implementação do Serviço cloud recuperará automaticamente quando a paralisação for resolvida |
| Desconhecido e talvez impactado | Neste momento, não conseguimos determinar a saúde desta implantação do Serviço cloud. Isto pode ser causado por uma paragem de serviço Azure em curso que pode estar a afetar esta máquina virtual, que irá automaticamente recuperar quando a paralisação for resolvida |

## <a name="cloud-services-role-instance-level-annotations--their-meanings"></a>Serviços em Nuvem (Nível de Exemplo de Função) Anotações & os seus significados
| Anotação | Descrição | 
| --- | --- | 
| Disponível | Não existem problemas conhecidos da plataforma Azure que afetem esta máquina virtual | 
| Desconhecido | Atualmente somos incapazes de determinar a saúde desta máquina virtual |
| Parado e negociando | Esta máquina virtual está a ser parada e desalocada conforme pedido por um processo ou utilizador autorizado |
| Criação de Saúde de Recursos | Criação de saúde de recursos para este recurso. A saúde dos recursos observa os seus recursos Azure para fornecer detalhes sobre eventos em curso e passados que os impactaram |
| Indisponível | A máquina virtual está indisponível. Estamos a trabalhar para recuperar automaticamente a máquina virtual e para determinar a origem do problema. Não é necessária nenhuma ação adicional de si neste momento. |
| Degradado | A máquina virtual está degradada. Estamos a trabalhar para recuperar automaticamente a máquina virtual e para determinar a origem do problema. Não é necessária nenhuma ação adicional de si neste momento. |
| Falha no hardware do servidor de anfitrião | Esta máquina virtual é impactada por uma falha fatal {HardwareCategory} no servidor anfitrião. O Azure irá recolocar a sua máquina virtual num servidor de anfitrião saudável |
| Migração programada devido a hardware degradado | O Azure identificou que o servidor anfitrião tem um {0} degradado, que falhará em breve. Se for viável, iremos Migrar em Direto a máquina virtual assim que possível. Caso contrário, será reimplementada depois das {1}, hora UTC. Para minimizar o risco ao seu serviço, e no caso de o hardware falhar antes da migração iniciada pelo sistema, recomendamos que se auto-reimplante a sua máquina virtual o mais rapidamente possível. |
| Disponível e talvez impactado | A sua máquina virtual está em funcionamento, no entanto uma falha de serviço Azure em curso pode impedir-lhe de se ligar a ela. A conectividade será restaurada assim que a paralisação for resolvida |
| Indisponível e talvez impactado | A saúde desta máquina virtual pode ser afetada por uma falha de serviço da Azure. A sua máquina virtual recuperará automaticamente quando a paralisação for resolvida |
| Desconhecido e talvez impactado | Atualmente, não conseguimos determinar a saúde desta máquina virtual. Isto pode ser causado por uma paragem de serviço Azure em curso que pode estar a afetar esta máquina virtual, que irá automaticamente recuperar quando a paralisação for resolvida |
| Recursos de hardware atribuídos | Os recursos de hardware foram atribuídos à máquina virtual e ficarão online em breve |
| Parar e negociar | Esta máquina virtual está a ser parada e desalocada conforme pedido por um processo ou utilizador autorizado |
| Configuração a ser atualizada | A configuração desta máquina virtual está a ser atualizada conforme pedido por um processo ou utilizador autorizado |
| Reiniciado pelo utilizador | Esta máquina virtual está a ser reiniciada conforme pedido por um processo ou utilizador autorizado. Estará de volta online depois do reboot completar |
| Reimplantação para anfitrião diferente | Esta máquina virtual está a ser reimplementada num anfitrião diferente, conforme pedido por um processo ou utilizador autorizado. Estará de volta on-line após a redistribuição concluída |
| Parado pelo utilizador | Esta máquina virtual está a ser parada conforme pedido por um processo ou utilizador autorizado |
| Parado pelo utilizador ou processo | Esta máquina virtual está a ser parada conforme pedido por um utilizador autorizado ou por um processo em execução numa máquina virtual |
| Iniciado pelo utilizador | Esta máquina virtual está a ser iniciada conforme pedido por um processo ou utilizador autorizado. Estará online em breve. |
| Recolocação de manutenção para hospedeiro diferente | Esta máquina virtual está a ser reimplementada num servidor anfitrião diferente como parte de uma atividade de manutenção planeada. Estará de volta on-line após a redistribuição concluída |
| Reinicialização iniciada a partir de dentro da máquina | Foi acionado um reinício a partir da máquina virtual. Esta operação pode ter sido causada por uma falha do sistema operativo da máquina virtual ou por um pedido de um processo ou utilizador autorizado. A máquina virtual estará novamente on-line depois do reboot completar |
| Redimensionado pelo utilizador | Esta máquina virtual está a ser redimensionada conforme pedido por um processo ou utilizador autorizado. Estará de volta on-line depois que o redimensionamento completar |
| Máquina despenhou-se | Foi acionado um reinício a partir da máquina virtual. Esta operação pode ter sido causada por uma falha do sistema operativo da máquina virtual ou por um pedido de um processo ou utilizador autorizado. A máquina virtual estará novamente on-line depois do reboot completar |
| Reinicialização da manutenção para atualização do anfitrião | As atualizações de manutenção estão a ser aplicadas ao servidor anfitrião que executa esta máquina virtual. Não é necessária nenhuma ação adicional da sua parte neste momento. Estará de volta on-line depois que a manutenção terminar |
| Recolocação de manutenção para novo hardware | Esta máquina virtual não está disponível porque está a ser reimplementada em hardware mais recente no âmbito de um evento de manutenção planeada. Não é necessária nenhuma ação adicional da sua parte neste momento. Estará de volta on-line depois que a manutenção planeada terminar |
| Máquina de baixa prioridade antecipada | Esta máquina virtual foi obtida por preempção. Esta máquina virtual de baixa prioridade está a ser parada e negociada |
| Reinicialização do servidor anfitrião | A máquina virtual não está disponível devido a um reinício inesperado do servidor anfitrião. O servidor anfitrião está atualmente a ser reiniciado. A máquina virtual ficará novamente online após a conclusão do reinício. Não é necessária nenhuma ação adicional de si neste momento. |
| Recolocação devido a falha no anfitrião | A máquina virtual não está disponível e está a ser reimplementada devido a uma falha inesperada no servidor anfitrião. O Azure iniciou o processo de recuperação automática e está atualmente a iniciar a máquina virtual num anfitrião diferente. Não é necessária nenhuma ação adicional de si neste momento. |
| Falha inesperada do hospedeiro | A máquina virtual não está disponível devido a uma falha inesperada no servidor anfitrião. O Azure iniciou o processo de recuperação automática e está atualmente a reiniciar o servidor anfitrião. Não é necessária nenhuma ação adicional da sua parte neste momento. A máquina virtual estará novamente on-line depois do reboot completar |
| Reimplantação devido à manutenção não planeada do hospedeiro | A máquina virtual não está disponível e está a ser reimplementada devido a uma falha inesperada no servidor anfitrião. O Azure iniciou o processo de recuperação automática e está atualmente a iniciar a máquina virtual num servidor anfitrião diferente. Não é necessária nenhuma ação adicional de si neste momento. |
| Falha no provisionamento | A máquina virtual não está disponível devido a problemas de aprovisionamento inesperados. O provisionamento da sua máquina virtual falhou devido a um erro inesperado |
| Migração em Direto | Esta máquina virtual está em pausa devido a uma operação de Migração em Direto de preservação da memória. Por norma, a máquina virtual demora cerca de dez segundos a retomar a execução. Não é necessária nenhuma ação adicional de si neste momento. |
| Migração em Direto | Esta máquina virtual está em pausa devido a uma operação de Migração em Direto de preservação da memória. Por norma, a máquina virtual demora cerca de dez segundos a retomar a execução. Não é necessária nenhuma ação adicional de si neste momento. | 
| Disco remoto desligado | A máquina virtual não está disponível devido à perda de conectividade com o disco remoto. Estamos a trabalhar para restabelecer a conectividade do disco. Não é necessária nenhuma ação adicional de si neste momento. |
| Emissão de serviço Azure | A sua máquina virtual é impactada por problema de serviço da Azure |
| Problema de rede | Esta máquina virtual é impactada por um dispositivo de rede topo de série |
| Indisponível | A máquina virtual está indisponível. Neste momento, não conseguimos determinar a razão deste tempo de inatividade. |
| Reinicialização do servidor anfitrião | A máquina virtual não está disponível devido a um reinício inesperado do servidor anfitrião. Um problema inesperado com o servidor anfitrião está a impedir-nos de recuperar automaticamente a sua máquina virtual |
| Recolocação devido a falha no anfitrião | A máquina virtual não está disponível devido a uma falha inesperada no servidor anfitrião. Um problema inesperado com o hospedeiro está a impedir-nos de recuperar automaticamente a sua máquina virtual. |
| Falha inesperada do hospedeiro | A máquina virtual não está disponível devido a uma falha inesperada no servidor anfitrião. Um problema inesperado com o hospedeiro está a impedir-nos de recuperar automaticamente a sua máquina virtual. |
| Reimplantação devido à manutenção não planeada do hospedeiro | A máquina virtual não está disponível devido a uma falha inesperada no servidor anfitrião. Um problema inesperado com o hospedeiro está a impedir-nos de recuperar automaticamente a sua máquina virtual. |
| Falha no provisionamento | A máquina virtual não está disponível devido a problemas de aprovisionamento inesperados. O provisionamento da sua máquina virtual falhou devido a um erro inesperado |
| Disco remoto desligado | A máquina virtual não está disponível devido à perda de conectividade com o disco remoto. Um problema inesperado está a impedir-nos de recuperar automaticamente a sua máquina virtual |
| Reboot devido à atualização do Guest OS | Foi iniciado um reboot pela plataforma Azure para aplicar uma nova atualização do Guest OS. A máquina virtual estará novamente on-line depois do reboot completar |