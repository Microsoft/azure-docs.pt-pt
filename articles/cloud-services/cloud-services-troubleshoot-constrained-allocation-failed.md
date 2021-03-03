---
title: Resolução de problemas ConstrainedAllocationFailed ao implementar um serviço Cloud (clássico) para Azure | Microsoft Docs
description: Este artigo mostra como resolver uma exceção CondicionadaallocationFailed ao implementar um serviço Cloud (clássico) para Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 346e7eb77039ab80e6f9dffb8ea8360198040504
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738293"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Resolução de problemas ConstrainedAllocationFailed ao implementar um serviço cloud (clássico) para Azure

Neste artigo, você vai resolver falhas de alocação onde os serviços Azure Cloud (clássico) não podem implementar devido a restrições de alocação.

Quando implementa casos num serviço Cloud (clássico) ou adiciona novas instâncias de funções web ou de trabalhador, o Microsoft Azure atribui recursos de computação.

Pode ocasionalmente receber erros durante estas operações mesmo antes de atingir o limite de subscrição do Azure.

> [!TIP]
> A informação também pode ser útil quando planeia a implantação dos seus serviços.

## <a name="symptom"></a>Sintoma

No portal Azure, navegue para o seu serviço Cloud (clássico) e na barra lateral selecione *registo de operação (clássico)* para ver os registos.

![A imagem mostra a lâmina de registo de operação (clássica).](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

Ao inspecionar os registos do seu serviço Cloud (clássico), verá a seguinte exceção:

|Tipo de Exceção  |Mensagem de Erro  |
|---------|---------|
|ConstrangimentoAllocationFailed     |A operação Azure `{Operation ID}` ' '' falhou com código Compute.ConstrainedAllocationFailed. Detalhes: A atribuição falhou; incapaz de satisfazer constrangimentos a pedido. A implementação do novo serviço solicitada está vinculada a um Grupo de Afinidade, tem como destino uma Rede Virtual ou há uma implementação existente neste serviço alojado. Qualquer uma destas condições limita a nova implantação a recursos específicos da Azure. Reforce mais tarde ou tente reduzir o tamanho de VM ou o número de instâncias de função. Em alternativa, se possível, remova as restrições mencionadas anteriormente ou tente implementar numa região diferente.|

## <a name="cause"></a>Causa

Quando a primeira instância é implantada num serviço Cloud (em encenação ou produção), esse serviço Cloud é fixado a um cluster.

Com o tempo, os recursos neste cluster podem tornar-se totalmente utilizados. Se um serviço Cloud (clássico) fizer um pedido de atribuição de mais recursos quando não há recursos suficientes no cluster fixado, o pedido resultará numa falha de atribuição. Para obter mais informações, consulte as [questões comuns](cloud-services-allocation-failures.md#common-issues)de falha de atribuição.

## <a name="solution"></a>Solução

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