---
title: Perguntas mais frequentes
description: Respostas para perguntas frequentes relacionadas com o serviço de Instâncias de Contentores Azure
author: macolso
ms.author: macolso
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 1cedfb7464fd97249c5cd3fae73c9290365da6cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91818492"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Perguntas frequentes sobre instâncias de contentores Azure

Este artigo aborda perguntas frequentes sobre casos de contentores Azure.

## <a name="deployment"></a>Implementação

### <a name="how-large-can-my-container-image-be"></a>Quão grande pode ser a minha imagem de contentor?

O tamanho máximo para uma imagem de recipiente implantável em Instâncias de Contentores Azure é de 15 GB. Pode ser capaz de implementar imagens maiores dependendo da disponibilidade exata no momento em que implementar, mas isso não está garantido.

O tamanho da sua imagem do recipiente tem impacto no tempo que leva a ser implantado, por isso, em geral, pretende manter as imagens do seu contentor o mais pequenas possível.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Como posso acelerar a colocação do meu contentor?

Porque um dos principais determinantes dos tempos de implantação é o tamanho da imagem, procure formas de reduzir o tamanho. Remova as camadas de que não precisa, ou reduza o tamanho das camadas na imagem (escolhendo uma imagem de SO base mais leve). Por exemplo, se estiver a executar recipientes Linux, considere usar o Alpine como a sua imagem base em vez de um Servidor Ubuntu completo. Da mesma forma, para recipientes Windows, utilize uma imagem base do Nano Server, se possível. 

Deve também consultar a lista de imagens pré-em cache em Imagens de Contentores Azure, disponíveis através da [Lista cached Images](/rest/api/container-instances/location/listcachedimages) API. Pode ser capaz de trocar uma camada de imagem por uma das imagens pré-em cache. 

Consulte [orientações](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) mais detalhadas sobre a redução do tempo de arranque do contentor.

### <a name="what-windows-base-os-images-are-supported"></a>Quais as imagens OS base do Windows suportadas?

> [!NOTE]
> Devido a problemas com retrocompatibilidade após as atualizações do Windows em 2020, as seguintes versões de imagem incluem o número de versão mínima que recomendamos que utilize na sua imagem base. As implementações atuais que utilizam versões de imagem mais antigas não são impactadas, mas as novas implementações devem aderir às seguintes imagens base. 

#### <a name="windows-server-2016-base-images"></a>Imagens base do Windows Server 2016

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `sac2016` , ou mais `10.0.14393.3506` recente
* [Núcleo do Servidor do Windows](https://hub.docker.com/_/microsoft-windows-servercore): , ou mais `ltsc2016`  `10.0.14393.3506` recente

> [!NOTE]
> As imagens do Windows baseadas no Semi-Annual lançamento do Canal 1709 ou 1803 não são suportadas.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 e imagens base do cliente (pré-visualização)

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809` , ou mais `10.0.17763.1040` recente
* [Núcleo do Servidor do Windows:](https://hub.docker.com/_/microsoft-windows-servercore) `ltsc2019` , ou mais `1809` `10.0.17763.1040` recente
* [Janelas](https://hub.docker.com/_/microsoft-windows): `1809` , ou mais `10.0.17763.1040` recentes

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Que camada de imagem .NET ou .NET Core devo usar no meu recipiente? 

Use a imagem mais pequena que satisfaça os seus requisitos. Para o Linux, pode utilizar uma imagem *de runtime-alpine* .NET Core, que foi suportada desde o lançamento de .NET Core 2.1. Para o Windows, se estiver a utilizar o quadro .NET completo, então tem de utilizar uma imagem do Núcleo do Servidor do Windows (imagem apenas em funcionamento, como *4.7.2-windowsservercore-ltsc2016).* As imagens apenas de tempo de execução são menores, mas não suportam cargas de trabalho que requeiram o .NET SDK.

## <a name="availability-and-quotas"></a>Disponibilidade e quotas

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Quantos núcleos e memória devo atribuir para os meus contentores ou para o grupo de contentores?

Isto depende da sua carga de trabalho. Inicie um desempenho pequeno e teste para ver como se saem os seus recipientes. [Monitorize o CPU e o uso do recurso de memória](container-instances-monitor.md)e, em seguida, adicione núcleos ou memória com base no tipo de processos que implementa no recipiente.

Certifique-se também de verificar a disponibilidade de [recursos](container-instances-region-availability.md#availability---general) para a região em que está a implantar para os limites superiores nos núcleos de CPU e memória disponível por grupo de contentores. 

> [!NOTE]
> Uma pequena quantidade de recursos de um grupo de contentores é utilizada pela infraestrutura subjacente do serviço. Os seus contentores poderão aceder à maioria, mas não a todos os recursos atribuídos ao grupo. Por esta razão, planeie um pequeno tampão de recursos ao solicitar recursos para contentores no grupo.

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Que infraestrutura subjacente funciona?

A Azure Container Instances pretende ser um serviço de contentores sem servidor a pedido, por isso queremos que esteja focado no desenvolvimento dos seus contentores, e não se preocupe com a infraestrutura! Para aqueles que são curiosos ou que querem fazer comparações sobre o desempenho, o ACI corre em conjuntos de VMs Azure de vários SKUs, principalmente da série F e D. Esperamos que isso mude no futuro à medida que continuamos a desenvolver e otimizar o serviço. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Quero colocar milhares de núcleos no ACI - posso aumentar a minha quota?
 
Sim (às vezes). Consulte o artigo [de quotas e limites](container-instances-quotas.md) para as quotas correntes e quais os limites que podem ser aumentados por pedido.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Posso implantar com mais de 4 núcleos e 16 GB de RAM?

Ainda não. Atualmente, estes são os máximos para um grupo de contentores. Contacte o Suporte Azure com requisitos ou pedidos específicos. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Quando estará o ACI numa região específica?

A disponibilidade da região atual é publicada [aqui.](container-instances-region-availability.md#availability---general) Se tiver um requisito para uma região específica, contacte o Suporte Azure.

## <a name="features-and-scenarios"></a>Características e cenários

### <a name="how-do-i-scale-a-container-group"></a>Como posso escalar um grupo de contentores?

Atualmente, a escala não está disponível para contentores ou grupos de contentores. Se precisar de executar mais instâncias, use a nossa API para automatizar e criar mais pedidos de criação de grupos de contentores ao serviço. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Quais as funcionalidades disponíveis para as instâncias em execução num VNet personalizado?

Você pode [implantar grupos de contentores numa rede virtual Azure](container-instances-vnet.md) à sua escolha, e delegar IPs privados para os grupos de contentores para encaminhar o tráfego dentro do VNet através dos seus recursos Azure. Para cenários de networking e limitações com instâncias de contentores Azure, consulte [cenários e recursos de rede virtual.](container-instances-virtual-network-concepts.md)

## <a name="pricing"></a>Preços

### <a name="when-does-the-meter-start-running"></a>Quando é que o taxímetro começa a funcionar?

A duração do grupo do contentor é calculada a partir do momento em que começamos a puxar a imagem do seu primeiro recipiente (para uma nova implantação) ou o seu grupo de contentores é reiniciado (se já estiver implantado), até que o grupo de contentores seja parado. Consulte os detalhes do [preço de Container Instances](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Parei de ser cobrado quando os meus contentores estão parados?

Os contadores param de funcionar assim que todo o grupo de contentores estiver parado. Enquanto um contentor do seu grupo de contentores estiver em funcionamento, nós guardamos os recursos no caso de querer voltar a ligar os contentores. 

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais](container-instances-overview.md) sobre as Instâncias do Contentor Azure.
* [Resolver problemas comuns](container-instances-troubleshooting.md) em instâncias de contentores Azure.
