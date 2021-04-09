---
title: Resolução de problemas FabricInternalServerError ou ServiceAllocationFailure ao implementar um serviço Cloud (clássico) para Azure | Microsoft Docs
description: Este artigo mostra como resolver uma exceção FabricInternalServerError ou ServiceAllocationFailure ao implementar um serviço Cloud (clássico) para Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 0a4111f569b751ace80a2a886ed2ce7e4968ce16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746758"
---
# <a name="troubleshoot-fabricinternalservererror-or-serviceallocationfailure-when-deploying-a-cloud-service-classic-to-azure"></a>Resolução de problemas FabricInternalServerError ou ServiceAllocationFailure ao implementar um serviço cloud (clássico) para Azure

Neste artigo, irá resolver problemas de falhas de atribuição onde o controlador de tecido não pode alocar ao implementar um serviço Azure Cloud (clássico).

Quando implementa casos num Cloud Service ou adiciona novas instâncias de funções web ou de trabalhador, o Microsoft Azure atribui recursos de computação.

Pode ocasionalmente receber erros durante estas operações mesmo antes de atingir o limite de subscrição do Azure.

> [!TIP]
> A informação também pode ser útil quando planeia a implantação dos seus serviços.

## <a name="symptom"></a>Sintoma

No portal Azure, navegue para o seu serviço Cloud (clássico) e na barra lateral selecione *registo de operação (clássico)* para ver os registos.

![A imagem mostra a lâmina de registo de operação (clássica).](./media/cloud-services-troubleshoot-fabric-internal-server-error/cloud-services-troubleshoot-allocation-logs.png)

Ao inspecionar os registos do seu serviço Cloud (clássico), verá a seguinte exceção:

|Exceção  |Mensagem de Erro  |
|---------|---------|
FabricInternalServerError     |A operação falhou com o código de erro 'InternalError' e o erroMessage 'O servidor encontrou um erro interno. Por favor, re-recandid recorde o pedido.|
|ServiceAllocationFailure     |A operação falhou com o código de erro 'InternalError' e o erroMessage 'O servidor encontrou um erro interno. Por favor, re-recandid recorde o pedido.|

## <a name="cause"></a>Causa

*FabricInternalServerError* e *ServiceAllocationFailure* são exceções que podem ocorrer quando o controlador de tecido não alocar instâncias no cluster. A causa da raiz varia se o serviço de nuvem estiver **preso** ou **não fixado**.

- [**Não preso:** Falhas de uma primeira implantação de um novo serviço na nuvem](#not-pinned-to-a-cluster)
- [**Fixado:** Falhas dos serviços de nuvem existentes](#pinned-to-a-cluster)

> [!NOTE]
> Quando a primeira instância é implantada num serviço de nuvem (em encenação ou produção), esse serviço de nuvem fica preso a um cluster.
>
> Com o tempo, os recursos neste conjunto de recursos podem tornar-se totalmente utilizados. Se um serviço de nuvem fizer um pedido de alocação de recursos adicionais quando não há recursos suficientes disponíveis no conjunto de recursos fixados, o pedido resultará numa [falha de atribuição](cloud-services-allocation-failures.md).

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
