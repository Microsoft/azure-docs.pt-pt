---
title: Resolução de problemas OverconstrainedAllocationRequest ao implementar um serviço Cloud (clássico) para Azure | Microsoft Docs
description: Este artigo mostra como resolver uma exceção overconstrainedAllocationRequest ao implementar um serviço Cloud (clássico) para Azure.
services: cloud-services
documentationcenter: ''
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 1b50ded166b3f62b38830b4c2d18da7c4c4f0d35
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746648"
---
# <a name="troubleshoot-overconstrainedallocationrequest-when-deploying-cloud-services-classic-to-azure"></a>Resolução de problemas OverconstrainedAllocationRequest ao implementar serviços cloud (clássico) para Azure

Neste artigo, irá resolver problemas sobre falhas de atribuição restritas que impedem a implantação de Azure Cloud Services (clássico).

Quando implementa casos num Cloud Service ou adiciona novas instâncias de funções web ou de trabalhador, o Microsoft Azure atribui recursos de computação.

Pode ocasionalmente receber erros durante estas operações mesmo antes de atingir o limite de subscrição do Azure.

> [!TIP]
> A informação também pode ser útil quando planeia a implantação dos seus serviços.

## <a name="symptom"></a>Sintoma

![A imagem mostra a lâmina de registo de operação (clássica).](./media/cloud-services-troubleshoot-overconstrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

|Tipo de Exceção  |Mensagem de Erro  |
|---------|---------|
|OverconstrainedAllocationRequest |O tamanho VM (ou combinação de tamanhos VM) exigido por esta implantação não pode ser a provisionado devido a restrições de pedido de implantação. Se possível, tente relaxar constrangimentos, tais como encadernações de rede virtuais, implantar-se num serviço hospedado sem qualquer outra implantação no mesmo e para um grupo de afinidades diferente ou sem grupo de afinidade, ou tente implementar para uma região diferente.|

## <a name="cause"></a>Causa

A causa da raiz varia se o serviço de nuvem estiver **preso** ou **não fixado**.

- [**Não preso:** Falhas de uma primeira implantação de um novo serviço Cloud (clássico)](#not-pinned-to-a-cluster)
- [**Fixado:** Falhas de um serviço Cloud existente (clássico)](#pinned-to-a-cluster)

> [!NOTE]
> Quando a primeira instância é implantada num serviço de nuvem (em encenação ou produção), esse serviço de nuvem fica preso a um cluster.
>
> Com o tempo, os recursos no cluster podem tornar-se totalmente utilizados. Se um serviço Cloud (clássico) fizer um pedido de atribuição de recursos adicionais quando não há recursos suficientes no cluster fixado, o pedido resultará numa falha de [atribuição](cloud-services-allocation-failures.md).

## <a name="solution"></a>Solução

Siga as orientações para falhas de atribuição nos seguintes cenários.

### <a name="not-pinned-to-a-cluster"></a>Não preso a um aglomerado

A primeira vez que implementa um serviço Cloud (clássico), o cluster ainda não foi selecionado, pelo que o serviço de nuvem não está *preso.* O Azure pode ter uma falha de implantação porque:

- Selecionou um tamanho específico que não está disponível na região.
- A combinação de tamanhos que são necessários em diferentes funções não está disponível na região.

Quando se experimenta um erro de atribuição neste cenário, o curso de ação recomendado é verificar os tamanhos disponíveis na região e alterar o tamanho especificado anteriormente.

1. Pode verificar os tamanhos disponíveis numa região na página de produtos do [serviço Cloud (clássico).](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services)

    > [!NOTE]
    > A página *Produtos* não mostrará a capacidade disponível. Para qualquer nova dotação, a Azure deve ser capaz de escolher o cluster ideal na sua região nessa altura.

1. Atualize o ficheiro de definição de serviço para o seu serviço Cloud (clássico) para especificar um tamanho de [produto](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) diferente da sua região.

### <a name="pinned-to-a-cluster"></a>Preso a um aglomerado

Os serviços de nuvem existentes estão *presos* a um aglomerado. Quaisquer outras implementações para o serviço Cloud (clássico) irão acontecer no mesmo cluster.

Quando se experimenta um erro de atribuição neste cenário, o curso de ação recomendado é recolocar um novo serviço Cloud (clássico) (e atualizar o *CNAME).*

> [!TIP]
> Esta solução provavelmente vai ser mais bem-sucedida, pois permite que a plataforma escolha entre todos os clusters nessa região.

> [!NOTE]
> Esta solução deve incorrer em tempo de inatividade zero.

1. Desloque a carga de trabalho para um novo serviço Cloud (clássico).
    - Consulte o guia como criar e implementar um guia [de serviço Cloud (clássico)](cloud-services-how-to-create-deploy-portal.md) para obter mais instruções.

    > [!WARNING]
    > Se não quiser perder o endereço IP associado a esta ranhura de implementação, pode utilizar [a Solução 3 - Mantenha o endereço IP](cloud-services-allocation-failures.md#solutions).

1. Atualize o *CNAME* ou *Um* registo para apontar o tráfego para o novo serviço Cloud (clássico).
    - Consulte o nome de domínio personalizado para um guia de [serviço Azure Cloud (clássico)](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) para obter mais instruções.

1. Uma vez que o tráfego zero vai para o site antigo, você pode apagar o antigo serviço Cloud (clássico).
    - Consulte as implementações de Eliminar e um guia [de serviço cloud (clássico)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) para obter mais instruções.
    - Para ver o tráfego de rede no seu serviço Cloud (clássico), consulte o [serviço Introdução à Nuvem (clássico).](cloud-services-how-to-monitor.md)

Ver [falhas de atribuição de serviços de resolução de problemas (clássicos) | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) para mais medidas de reparação.

## <a name="next-steps"></a>Passos seguintes

Para mais soluções de falha de atribuição e informações de fundo:

> [!div class="nextstepaction"]
> [Falhas de atribuição - Serviço em nuvem (clássico)](cloud-services-allocation-failures.md)

Se a sua edição de Azure não for abordada neste artigo, visite os fóruns Azure na [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Pode publicar o seu problema nestes fóruns ou publicar [ @AzureSupport no Twitter](https://twitter.com/AzureSupport). Também pode submeter um pedido de apoio ao Azure. Para submeter um pedido de apoio, na página de suporte do [Azure,](https://azure.microsoft.com/support/options/) selecione *Obter suporte*.
