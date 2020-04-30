---
title: Perguntas mais frequentes
description: Respostas para perguntas frequentes relacionadas com o serviço Decasos de Contentores Azure
author: dkkapur
ms.topic: article
ms.date: 04/10/2020
ms.openlocfilehash: 4fca198356c8db006c4190e0f16b20f78dc1d477
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115232"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Perguntas frequentes sobre instâncias de contentores de Azure

Este artigo aborda frequentemente perguntas sobre instâncias de contentores do Azure.

## <a name="deployment"></a>Implementação

### <a name="how-large-can-my-container-image-be"></a>Quão grande pode ser a minha imagem de contentor?

O tamanho máximo para uma imagem de recipiente implantável em Instâncias de Contentores Azure é de 15 GB. Poderá ser capaz de implementar imagens maiores dependendo da disponibilidade exata no momento em que se implementa, mas isso não está garantido.

O tamanho da imagem do seu recipiente afeta o tempo que demora a ser implantado, pelo que geralmente pretende manter as imagens do recipiente o mais pequenas possível.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Como posso acelerar a colocação do meu contentor?

Como um dos principais determinantes dos tempos de implantação é o tamanho da imagem, procure formas de reduzir o tamanho. Remova camadas de que não precisa, ou reduza o tamanho das camadas na imagem (escolhendo uma imagem de Os base mais leve). Por exemplo, se estiver a executar contentores Linux, considere usar a Alpine como imagem base em vez de um Servidor Ubuntu completo. Da mesma forma, para os recipientes Windows, utilize uma imagem base nana server, se possível. 

Deve também verificar a lista de imagens pré-cached em Imagens de Contentores Azure, disponíveis através da [Lista Imagens Em Cached](/rest/api/container-instances/listcachedimages) API. Pode ser capaz de trocar uma camada de imagem por uma das imagens pré-cached. 

Consulte [uma orientação](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) mais detalhada sobre a redução do tempo de arranque dos contentores.

### <a name="what-windows-base-os-images-are-supported"></a>Que imagens do Sistema operativo Windows base são suportadas?

#### <a name="windows-server-2016-base-images"></a>Imagens base do Windows Server 2016

* [Nano](https://hub.docker.com/_/microsoft-windows-nanoserver)Server `10.0.14393.x`: ,`sac2016`
* [Núcleo do](https://hub.docker.com/_/microsoft-windows-servercore) `ltsc2016`Servidor do Windows : ,`10.0.14393.x`

> [!NOTE]
> As imagens do Windows baseadas no lançamento do Canal 1709 ou 1803 não são suportadas.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 e imagens base de cliente (pré-visualização)

* [Nano](https://hub.docker.com/_/microsoft-windows-nanoserver)Server `1809` `10.0.17763.914` : , ou mais cedo
* [Núcleo](https://hub.docker.com/_/microsoft-windows-servercore)do `ltsc2019`Servidor `1809` `10.0.17763.914` do Windows : , ou mais cedo
* [Janelas](https://hub.docker.com/_/microsoft-windows) `1809` `10.0.17763.914` : , ou mais cedo

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Que camada de imagem .NET ou .NET Core devo usar no meu recipiente? 

Use a imagem mais pequena que satisfaz os seus requisitos. Para o Linux, pode utilizar uma imagem de base .NET Core *invertada,* que tem sido suportada desde o lançamento do .NET Core 2.1. Para windows, se estiver a utilizar a estrutura completa .NET, então precisa de utilizar uma imagem do Windows Server Core (imagem apenas de tempo de execução, como *4.7.2 windowsservercore-ltsc2016*). As imagens apenas com tempo de execução são menores, mas não suportam cargas de trabalho que requerem o SDK .NET.

## <a name="availability-and-quotas"></a>Disponibilidade e quotas

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Quantos núcleos e memória devo atribuir para os meus contentores ou para o grupo de contentores?

Isto depende da sua carga de trabalho. Inicie um pequeno desempenho e teste para ver como se fazem os seus recipientes. [Monitorize](container-instances-monitor.md)o CPU e o uso de recursos de memória e adicione, em seguida, núcleos ou memória com base no tipo de processos que implementa no recipiente.

Certifique-se também de verificar a disponibilidade de [recursos](container-instances-region-availability.md#availability---general) para a região que está a implantar para os limites superiores dos núcleos de CPU e da memória disponível por grupo de contentores. 

> [!NOTE]
> Uma pequena quantidade de recursos de um grupo de contentores é utilizada pela infraestrutura subjacente do serviço. Os seus contentores poderão aceder à maioria, mas não todos os recursos atribuídos ao grupo. Por esta razão, planeie um pequeno tampão de recursos ao solicitar recursos para contentores no grupo.

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Em que infraestruturas subjacentes a ACI funciona?

O Azure Container Instances pretende ser um serviço de contentores sem servidores a pedido, por isso queremos que se concentre no desenvolvimento dos seus contentores e não se preocupe com a infraestrutura! Para aqueles que são curiosos ou que querem fazer comparações sobre o desempenho, o ACI funciona em conjuntos de VMs Azure de várias SKUs, principalmente da série F e D. Esperamos que isto mude no futuro à medida que continuamos a desenvolver e otimizar o serviço. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Quero implantar milhares de núcleos no ACI- posso aumentar a minha quota?
 
Sim (às vezes). Consulte o artigo [quotas e limites](container-instances-quotas.md) para as quotas correntes e quais os limites que podem ser aumentados metodoveis.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Posso implantar com mais de 4 núcleos e 16 GB de RAM?

Ainda não. Atualmente, estes são os máximos para um grupo de contentores. Contacte o Suporte Azure com requisitos ou pedidos específicos. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Quando é que a ACI estará numa região específica?

A disponibilidade atual da região é publicada [aqui.](container-instances-region-availability.md#availability---general) Se tiver um requisito para uma região específica, contacte o Suporte Azure.

## <a name="features-and-scenarios"></a>Características e cenários

### <a name="how-do-i-scale-a-container-group"></a>Como escalo um grupo de contentores?

Atualmente, a escala não está disponível para contentores ou grupos de contentores. Se precisar de executar mais instâncias, use a nossa API para automatizar e criar mais pedidos de criação de grupo de contentores para o serviço. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Quais as funcionalidades disponíveis para casos que correm num VNet personalizado?

Você pode implantar grupos de [contentores numa rede virtual Azure](container-instances-vnet.md) à sua escolha, e delegar iPs privados para os grupos de contentores para direcionar o tráfego dentro do VNet através dos seus recursos Azure. A implantação de um grupo de contentores numa rede virtual está atualmente disponível para cargas de trabalho de produção num subconjunto das regiões de Azure.

## <a name="pricing"></a>Preços

### <a name="when-does-the-meter-start-running"></a>Quando é que o taxímetro começa a funcionar?

A duração do grupo de contentores é calculada a partir do momento em que começamos a puxar a imagem do seu primeiro contentor (para uma nova implantação) ou o seu grupo de contentores é reiniciado (se já estiver implantado), até que o grupo de contentores seja parado. Consulte os detalhes no preço das instâncias de [contentores](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Paro de ser carregado quando os meus contentores estão parados?

Os contadores param de funcionar assim que todo o seu grupo de contentores estiver parado. Enquanto um contentor do seu grupo de contentores estiver em funcionamento, guardamos os recursos no caso de querer relançar os contentores. 

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais](container-instances-overview.md) sobre as instâncias de contentores azure.
* [Problemas de resolução de problemas](container-instances-troubleshooting.md) em casos de contentores de Azure.