---
title: O Azure Container Instances - perguntas mais frequentes
description: Respostas para perguntas mais frequentes sobre relacionados com o serviço do Azure Container Instances
services: container-instances
author: dkkapur
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 4/25/2019
ms.author: dekapur
ms.openlocfilehash: 5657ac9f10c42c2201641c9af447b7ad2e6a3507
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079019"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Perguntas mais frequentes sobre o Azure Container Instances

Este artigo aborda perguntas mais frequentes sobre o Azure Container Instances.

## <a name="deployment"></a>Implementação

### <a name="how-large-can-my-container-image-be"></a>Que tamanho pode minha imagem de contentor ser?

O tamanho máximo para uma imagem de contentor implementáveis no Azure Container Instances é 15 GB. Poderá ser capaz de implantar imagens maiores dependendo da disponibilidade exata no momento de implementar, mas isso não é garantido.

O tamanho da sua imagem de contentor tem impacto sobre o tempo que demora para implementar, geralmente que quer manter as imagens de contentor o menor possível.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Como posso acelerar a implantação do meu contêiner?

Como um dos determinants principais de tempos de implantação é o tamanho da imagem, procurem por maneiras de reduzir o tamanho. Remova camadas não precisa ou reduzir o tamanho de camadas na imagem (ao escolher uma imagem de SO base mais leve). Por exemplo, se estiver a executar contentores do Linux, considere utilizar Alpine como a imagem base, em vez de um servidor completo do Ubuntu. Da mesma forma, para contentores do Windows, utilize se for possível uma imagem de base do servidor Nano. 

Também deve verificar a lista de imagens previamente em cache em imagens de contentor do Azure, disponível através do [listar imagens colocadas em cache](/rest/api/container-instances/listcachedimages) API. Poderá conseguir mudar uma camada de imagem para uma das imagens previamente em cache. 

Ver mais [orientações detalhadas](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) na redução de tempo de inicialização do contentor.

### <a name="what-windows-base-os-images-are-supported"></a>As imagens de SO base do Windows são suportadas?

#### <a name="windows-server-2016-base-images"></a>Imagens de base do Windows Server 2016

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `10.0.14393.x`, `sac2016`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`,  `10.0.14393.x`

> [!NOTE]
> Imagens do Windows com base na versão do canal Semianual 1709 ou versão 1803 não são suportadas.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>2019 do Windows Server e cliente imagens base (pré-visualização)

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809`, `10.0.17763.x`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`, `1809`, `10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809`, `10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Que camada de imagem do .NET ou .NET Core deve utilizar no meu contêiner? 

Utilize a imagem mais pequena que atende as suas necessidades. Para o Linux, poderia usar uma *tempo de execução alpine* imagem de .NET Core, que tem sido suportada desde o lançamento do .NET Core 2.1. Para Windows, se estiver a utilizar o .NET Framework completo, em seguida, terá de utilizar uma imagem do Windows Server Core (imagem só de tempo de execução, como *4.7.2-windowsservercore-ltsc2016*). Imagens de só de tempo de execução são mais pequenas, mas não suportam as cargas de trabalho que exigem o SDK do .NET.

## <a name="availability-and-quotas"></a>Disponibilidade e quotas

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Número de núcleos e memória devo alocar para meu contentores ou o grupo de contentores?

Isso realmente depende de sua carga de trabalho. Comece por algo pequeno e testar o desempenho para ver como fazer seus contentores. [Monitorizar a utilização de recursos de CPU e memória](container-instances-monitor.md)e, em seguida, adicione os núcleos ou memória com base no tipo de processos que implementar no contentor. 

Certifique-se também verificar a [de disponibilidade de recursos](container-instances-region-availability.md#availability---general) para a região que esteja implantando para os limites superiores núcleos de CPU e memória disponível por grupo de contentores. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Infraestrutura subjacente ACI é executado no?

Tem como objetivo ser um serviço de contentores a pedido sem servidor, para que possamos Queremos que ser voltada para o desenvolvimento de seus contentores e não se preocupar sobre a infraestrutura do Azure Container Instances! Para aqueles que estão curiosos ou wanting fazer comparações de desempenho, ACI executa em conjuntos de VMs do Azure de várias SKUs, principalmente do F e a série D. Esperamos que esta opção para mudar no futuro, à medida que continuarmos a desenvolver e otimizar o serviço. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Quero implementar milhares de núcleos no ACI - posso obter minha aumentar a quota?
 
Sim (às vezes). Consulte a [quotas e limites](container-instances-quotas.md) artigo para as quotas atuais e os limites podem ser aumentados ao pedido.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Pode implementar com mais de 4 núcleos e 16 GB de RAM?

Ainda não. Atualmente, estes são os valores máximos para um grupo de contentores. Contacte o suporte do Azure com requisitos específicos ou pedidos. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Quando ficará ACI numa região específica?

Disponibilidade de região atual é publicada [aqui](container-instances-region-availability.md#availability---general), e as informações atualizadas estejam disponíveis através do [capacidades de lista](/rest/api/container-instances/listcapabilities) API. Se tiver um requisito para uma região específica, contacte o suporte do Azure.

## <a name="features-and-scenarios"></a>Recursos e cenários

### <a name="how-do-i-scale-a-container-group"></a>Como posso dimensionar a um grupo de contentores?

Atualmente, dimensionamento não está disponível para contentores ou grupos de contentores. Se precisar executar mais instâncias, utilize a nossa API para automatizar e mais pedidos para a criação do grupo de contentor para o serviço de criação. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Que funcionalidades estão disponíveis para instâncias em execução numa VNet personalizado?

Pode implementar grupos de contentor numa rede virtual do Azure à sua escolha e delegar IPs privados para os grupos de contentor para encaminhar o tráfego dentro da VNet todos os seus recursos do Azure. Implementação de um grupo de contentor numa rede virtual está atualmente em pré-visualização e alguns aspetos desta funcionalidade podem ser alterados antes da disponibilidade geral (GA). Ver [limitações de pré-visualização](container-instances-vnet.md#preview-limitations) para obter informações atualizadas.

## <a name="pricing"></a>Preços

### <a name="when-does-the-meter-start-running"></a>Quando o medidor de iniciar a execução?

Duração do grupo de contentor é calculada a partir do momento em que podemos começar a extrair a imagem do seu primeiro contentor (para uma nova implementação) ou o seu grupo de contentores é reiniciado (se já implementada), até que o grupo de contentores está parado. Consulte os detalhes em [os preços das instâncias de contentor](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Posso parar a ser cobrado quando a minha contentores são parados?

Medidores de parar a execução depois que o seu grupo de contentor inteiro está parado. Enquanto estiver a executar um contentor no seu grupo de contentores, promovemos os recursos no caso de que pretende que sejam reiniciados os contentores. 

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais](container-instances-overview.md) sobre o Azure Container Instances.
* [Resolver problemas comuns de](container-instances-troubleshooting.md) no Azure Container Instances.