---
title: Fio de conexão de imagem Azure Service Fabric
description: Conheça a cadeia de ligação da loja de imagens, incluindo as suas utilizações e aplicações a um cluster de Tecido de Serviço.
author: alexwun
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: c3395248188c2a16736cfc8cea262fe163a6944b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645672"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Compreender a definição ImageStoreConnectionString

Em algumas das nossas documentações, mencionamos brevemente a existência de um parâmetro "ImageStoreConnectionString" sem descrever o que realmente significa. E depois de passar por um artigo como [Implementar e remover aplicações usando powerShell,][10]parece que tudo o que faz é copiar/colar o valor como mostrado no manifesto de cluster do cluster target. Portanto, a definição deve ser configurável por cluster, mas quando se cria um cluster através do [portal Azure,][11]não há opção de configurar esta definição e é sempre "fabric:ImageStore". Qual é o propósito deste cenário?

![Manifesto de Cluster][img_cm]

O Service Fabric começou como uma plataforma para o consumo interno da Microsoft por muitas equipas diversas, pelo que alguns aspetos são altamente personalizáveis - a "Image Store" é um desses aspetos. Essencialmente, a Loja de Imagens é um repositório pluggable para armazenar pacotes de aplicações. Quando a sua aplicação é implantada para um nó no cluster, esse nó descarrega o conteúdo do seu pacote de aplicação a partir da Loja de Imagens. O ImageStoreConnectionString é uma definição que inclui todas as informações necessárias tanto para clientes como para nós para encontrar a Loja de Imagem correta para um determinado cluster.

Existem atualmente três tipos possíveis de fornecedores de Lojas de Imagem e as suas cordas de ligação correspondentes são as seguintes:

1. Serviço de Loja de Imagens: "fabric:ImageStore"

2. Sistema de ficheiros: "ficheiro:[caminho do sistema de ficheiros]"

3. Armazenamento Azure: "xstore:DefaultEndpointsProtocol=https; Nome de conta=[...]; Chave de conta=[...]; Contentor=[...]"

O tipo de fornecedor utilizado na produção é o Serviço de Loja de Imagem, que é um serviço de sistema persponia e imponente que pode ver do Service Fabric Explorer. 

![Serviço de Loja de Imagem][img_is]

Hospedar a Loja de Imagem num serviço de sistema dentro do próprio cluster elimina dependências externas para o repositório de pacotes e dá-nos mais controlo sobre a localidade de armazenamento. As futuras melhorias em torno da Loja de Imagens são suscetíveis de visar o fornecedor da Loja de Imagem primeiro, se não exclusivamente. A cadeia de ligação para o fornecedor de Serviços de Loja de Imagem não possui nenhuma informação única, uma vez que o cliente já está ligado ao cluster alvo. O cliente só precisa de saber que os protocolos que visam o serviço do sistema devem ser utilizados.

O fornecedor do Sistema de Ficheiros é utilizado em vez do Serviço de Loja de Imagem para clusters de uma caixa local durante o desenvolvimento para arrancar o cluster um pouco mais rápido. A diferença é tipicamente pequena, mas é uma otimização útil para a maioria das pessoas durante o desenvolvimento. É possível implementar um cluster de uma caixa local com os outros tipos de fornecedores de armazenamento também, mas geralmente não há razão para fazê-lo, uma vez que o fluxo de trabalho de desenvolvimento/teste permanece o mesmo independentemente do fornecedor. O fornecedor de armazenamento Azure só existe para apoio legado de antigos clusters implantados antes da introdução do fornecedor de Serviços de Loja de Imagem.

Além disso, não deve ser utilizado o fornecedor do Sistema de Ficheiros ou o fornecedor de armazenamento Azure como método de partilha de uma Loja de Imagens entre vários clusters - isto resultará na corrupção de dados de configuração de cluster, uma vez que cada cluster pode escrever dados contraditórios para a Imagem A loja. Para partilhar pacotes de aplicações provisionados entre vários clusters, utilize ficheiros [sfpkg,][12] que podem ser enviados para qualquer loja externa com um URI de download.

Assim, enquanto o ImageStoreConnectionString é configurável, basta utilizar a definição predefinida. Ao publicar para o Azure através do Visual Studio, o parâmetro é automaticamente definido para si em conformidade. Para a implementação programática para clusters hospedados em Azure, a cadeia de ligação é sempre "fabric:ImageStore". Embora, em caso de dúvida, o seu valor possa sempre ser verificado recuperando o manifesto de cluster por [PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx), ou [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Tanto os clusters de teste no local como os clusters de produção devem ser sempre configurados para utilizar o fornecedor do Serviço de Loja de Imagem também.

### <a name="next-steps"></a>Passos seguintes
[Implementar e remover aplicações usando powerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
