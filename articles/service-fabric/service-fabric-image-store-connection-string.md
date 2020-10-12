---
title: Cadeia de conexão de loja de imagem Azure Service Fabric
description: Conheça a cadeia de conexão da loja de imagens, incluindo as suas utilizações e aplicações para um cluster de Tecido de Serviço.
author: alexwun
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 8fc0239dd18fc7071823a129a7dbc4f102023d66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86246202"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Compreender a definição ImageStoreConnectionString

Em algumas das nossas documentação, mencionamos brevemente a existência de um parâmetro "ImageStoreConnectionString" sem descrever o que realmente significa. E depois de passar por um artigo como [Implementar e remover aplicações usando PowerShell,][10]parece que tudo o que faz é copiar/colar o valor como mostrado no manifesto de cluster do cluster alvo. Portanto, a configuração deve ser configurável por cluster, mas quando se cria um cluster através do [portal Azure,][11]não há opção para configurar esta definição e é sempre "fabric:ImageStore". Qual é o propósito deste cenário, então?

![Manifesto de Cluster][img_cm]

O Service Fabric começou por ser uma plataforma para o consumo interno da Microsoft por muitas equipas diversas, pelo que alguns aspetos do mesmo são altamente personalizáveis - a "Image Store" é um desses aspetos. Essencialmente, a Image Store é um repositório pluggable para armazenar pacotes de aplicações. Quando a sua aplicação é implantada num nó no cluster, esse nó descarrega o conteúdo do seu pacote de aplicações a partir da Image Store. O ImageStoreConnectionString é uma configuração que inclui todas as informações necessárias para que tanto os clientes como os nós encontrem a Loja de Imagem correta para um determinado cluster.

Existem atualmente três tipos possíveis de fornecedores de Lojas de Imagens e as respetivas cadeias de ligação são as seguintes:

1. Serviço de Loja de Imagens: "fabric:ImageStore"

2. Sistema de ficheiros: "ficheiro:[caminho do sistema de ficheiros]"

3. Armazenamento Azure: "xstore:DefaultEndpointsProtocol=https; ContaName=[...]; ContaKey=[...]; Contentor=[...]"

O tipo de fornecedor utilizado na produção é o Image Store Service, que é um serviço de sistema insistido estatal que pode ver do Service Fabric Explorer. 

![Serviço de Loja de Imagem][img_is]

Hospedar a Image Store num serviço de sistema dentro do próprio cluster elimina dependências externas para o repositório de pacotes e dá-nos mais controlo sobre a localidade do armazenamento. As futuras melhorias em torno da Image Store são suscetíveis de visar primeiro o fornecedor da Image Store, se não exclusivamente. A cadeia de ligação para o fornecedor de Serviço de Loja de Imagens não tem nenhuma informação única, uma vez que o cliente já está ligado ao cluster alvo. O cliente só precisa de saber que os protocolos direcionados para o serviço do sistema devem ser utilizados.

O fornecedor do Sistema de Ficheiros é utilizado em vez do Serviço de Loja de Imagens para clusters locais de uma caixa durante o desenvolvimento para arrancar o cluster um pouco mais rápido. A diferença é tipicamente pequena, mas é uma otimização útil para a maioria das pessoas durante o desenvolvimento. É possível implantar um cluster local de uma caixa com os outros tipos de fornecedores de armazenamento também, mas geralmente não há nenhuma razão para fazê-lo, uma vez que o fluxo de trabalho de desenvolvimento/teste permanece o mesmo independentemente do fornecedor. O fornecedor de armazenamento Azure só existe para suporte legado de clusters antigos implantados antes da introdução do fornecedor de Serviços image Store.

Além disso, não o fornecedor do Sistema de Ficheiros ou o fornecedor de armazenamento Azure devem ser usados como um método de partilha de uma Loja de Imagens entre vários clusters - isto resultará em corrupção de dados de configuração de cluster, uma vez que cada cluster pode escrever dados contraditórios para a Image Store. Para partilhar pacotes de aplicações aparidas entre vários clusters, utilize ficheiros [SFPKG,][12] que podem ser enviados para qualquer loja externa com um download URI.

Assim, enquanto o ImageStoreConnectionString é configurável, basta utilizar a definição predefinidora. Ao publicar para a Azure através do Visual Studio, o parâmetro é automaticamente definido para si em conformidade. Para a implantação programática para agrupamentos alojados em Azure, a cadeia de ligação é sempre "fabric:ImageStore". Embora em caso de dúvida, o seu valor pode sempre ser verificado recuperando o manifesto de cluster por [PowerShell](/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](/previous-versions/azure/reference/mt161375(v=azure.100))ou [REST](/rest/api/servicefabric/get-a-cluster-manifest). Tanto os agrupamentos de teste no local como os agrupamentos de produção devem ser sempre configurados para utilizar o fornecedor de Serviços image Store.

### <a name="next-steps"></a>Passos seguintes
[Implementar e remover aplicações usando PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
