---
title: Resolver erros de implementação comuns
titleSuffix: Azure Load Balancer
description: Descreve como resolver erros comuns ao implementar os Equilíbrios de Carga Azure
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: 26b4232832589827a09774eb0106c25e5c446eb1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791090"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>Problemas de resolução de erros comuns de implantação do Azure com o Equilíbrio de Carga Azure

Este artigo descreve alguns erros comuns de implementação do Azure Load Balancer e fornece informações para resolver os erros. Se procura informações sobre um código de erro e essa informação não é fornecida neste artigo, avise-nos. Na parte inferior desta página, pode deixar feedback. O feedback é rastreado com problemas gitHub.

## <a name="error-codes"></a>Códigos de erro

| Código de erro | Detalhes e mitigação |
| ------- | ---------- |
|DifferentSkuLoadBalancers AndPublicIPAddressNotAllowed| Tanto o IP Público SKU como o Balancer De carga SKU devem corresponder. Certifique-se de que o Equilíbrio de Carga Azure e as Ip SKUs públicas correspondem. O SKU padrão é recomendado para cargas de trabalho de produção. Saiba mais sobre as [diferenças nas SKUs](./skus.md)  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | A escala de máquina virtual define o padrão dos equilíbrios básicos de carga quando o SKU não é especificado ou implantado sem IPs públicos padrão. Reutilizar a balança de máquinas virtuais com IPs públicos standard nas instâncias individuais para garantir que o Standard Load Balancer é selecionado ou simplesmente selecionar um LB padrão ao implementar uma balança de máquinavirtual definida a partir do portal Azure. |
|MaxAvailabilitySetsInLoadBalancerReached | O pool de backend de um Balancer de carga pode conter um máximo de 150 conjuntos de disponibilidade. Se não tiver conjuntos de disponibilidade explicitamente definidos para os seus VMs na piscina de backend, cada VM entra no seu próprio Conjunto de Disponibilidade. Assim, a implantação de 150 VMs autónomos implicaria que teria 150 conjuntos de disponibilidade, atingindo assim o limite. Pode implementar um conjunto de disponibilidade e adicionar-lhe VMs adicionais como sutiã. |
|RegrasOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| Não é possível ter mais do que uma regra sobre um determinado tipo de equilíbrio de carga (interno, público) com a mesma porta de backend e protocolo referenciado pelo mesmo conjunto de escala de máquina virtual. Atualize a sua regra para alterar esta criação de regras duplicadas. |
|RegrasOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| Não é possível ter mais do que uma regra sobre um determinado tipo de equilíbrio de carga (interno, público) com a mesma porta de backend e protocolo referenciado pelo mesmo conjunto de escala de máquina virtual. Atualize os parâmetros da sua regra para alterar esta criação de regras duplicadas. |
|OutroInternalLoadBalancerExiste| Pode ter apenas um Balancer de carga de referência interna tipo o mesmo conjunto de Interfaces VMs/rede no final do Balancer de carga. Atualize a sua implementação para garantir que está a criar apenas um Balancer de Carga do mesmo tipo. |
|Não Pode UsarInactiveHealthProbe| Não pode ter uma sonda que não seja usada por nenhuma regra configurada para a saúde da escala de máquinavirtual. Certifique-se de que a sonda que está montada está a ser ativamente utilizada. |
|VMScaleSetCancanuseMultipleLoadBalancersOfSameType| Não é possível ter vários Balanceadores de Carga do mesmo tipo (interno, público). Pode ter um máximo de um equilibrador de carga interno e público. |
|VMScaleSetSetCanReferenceLoadbalancer WhenLargeScaleOrCrossAZ | O Equilíbrio de Carga Básico não é suportado para conjuntos de escala virtual de máquinas de grupo de colocação múltipla ou conjunto de escala virtual de área de disponibilidade cruzada. Utilize o Balancer de carga padrão. |
|Falha na implantação de recursos| Se o seu equilibrista de carga estiver em estado falhado, siga estes passos para trazê-lo de volta do estado falhado:<ol><li>Vá https://resources.azure.coma e inscreva-se com as suas credenciais do portal Azure.</li><li>Selecione **Ler/Escrever**.</li><li>À esquerda, expanda **as Assinaturas**e, em seguida, expanda a Subscrição com o Balancer de Carga para atualizar.</li><li>Expandir **Os Recursos Groups**, e depois expandir o grupo de recursos com o Balancer de Carga para atualizar.</li><li>Selecione **Microsoft.Network** > **LoadBalancers**, e, em seguida, selecione o Balancer de carga para atualizar, **LoadBalancer_1**.</li><li>Na página de exibição para **LoadBalancer_1,** selecione **GET** > **Edit**.</li><li>Atualizar o valor do Estado de **Provisionamento** de **Failed** to **Succeed .**</li><li>Selecione **PUT**.</li></ol>|
|  |  |

## <a name="next-steps"></a>Passos seguintes

* Veja através da tabela de [comparação Azure](./skus.md) Load Balancer SKU
* Conheça [os limites](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) do Equilíbrio de Carga Azure
