---
title: Azure cadeia de ligação do arquivo de imagem do Service Fabric | Documentos da Microsoft
description: Compreender a cadeia de ligação do arquivo de imagem
services: service-fabric
documentationcenter: .net
author: alexwun
manager: chackdan
editor: ''
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 4a56b48c0041e963b89312c59335b45cabacc1bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60720200"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Compreender a definição ImageStoreConnectionString

Em alguns da nossa documentação, mencionamos brevemente a existência de um parâmetro de "ImageStoreConnectionString" sem que descreve o que realmente significa. E depois de passar por um artigo como [implementar e remover aplicações com o PowerShell][10], ele se parece com tudo o que fazer é copiar/colar o valor conforme mostrado no manifesto do cluster do cluster de destino. Portanto, a definição deve ser configurável por cluster, mas quando cria um cluster através da [portal do Azure][11], não existe nenhuma opção para configurar esta definição e é sempre "recursos de infraestrutura: ImageStore". O que é a finalidade de, em seguida, esta definição?

![Manifesto do cluster][img_cm]

Service Fabric iniciada como uma plataforma de consumo interno da Microsoft por muitas equipes diferentes, para alguns aspectos do mesmo são altamente personalizáveis - a "imagem Store" é um dos aspectos desse tipo. Essencialmente, a imagem Store é um repositório conectável para armazenar pacotes de aplicações. Quando a aplicação é implementada para um nó do cluster, esse nó transfere o conteúdo do seu pacote de aplicação a partir do Store de imagem. O ImageStoreConnectionString é uma definição que inclui todas as informações necessárias para os clientes e nós para encontrar a correta Store de imagem para um determinado cluster.

Atualmente, existem três tipos possíveis de fornecedores de imagem Store e suas cadeias de caracteres de conexão correspondentes são os seguintes:

1. Serviço de imagem Store: "recursos de infraestrutura: ImageStore"

2. Sistema de ficheiros: "caminho do sistema file:[file]"

3. Azure Storage: "xstore:DefaultEndpointsProtocol=https;AccountName=[...];AccountKey=[...];Container=[...]"

O tipo de fornecedor utilizado na produção é o serviço de Store de imagem, que é um serviço de sistema persistentes com estado que pode ver no Service Fabric Explorer. 

![Serviço de imagem Store][img_is]

Hospedar o Store de imagem num serviço de sistema dentro do cluster em si elimina as dependências externas para o repositório do pacote e dá-nos mais controlo sobre a localização de armazenamento. Melhorias futuras em todo o Store de imagem são provável que o fornecedor de Store de imagem de destino, primeiro, se não exclusivamente. A cadeia de ligação para o fornecedor de serviços de Store de imagem não tem quaisquer informações exclusivas, uma vez que o cliente já está ligado ao cluster de destino. O cliente só precisa de saber que os protocolos visando o serviço de sistema devem ser utilizados.

O fornecedor do sistema de ficheiros é utilizado em vez do serviço de Store de imagem para clusters de caixa de um locais, durante o desenvolvimento para iniciar o cluster de um pouco mais rapidamente. A diferença é normalmente pequena, mas é uma otimização útil para a maioria das pessoas durante o desenvolvimento. É possível implementar um cluster de caixa de um local com os outros fornecedor tipos de armazenamento também, mas geralmente não há motivo para isso, uma vez que o fluxo de trabalho de desenvolver/teste permanece o mesmo, independentemente do fornecedor. O fornecedor de armazenamento do Azure só existe para o suporte legacy antigos clusters implementados antes do fornecedor de serviços de Store de imagem foi introduzido.

Além disso, não o fornecedor do sistema de ficheiros ou o fornecedor de armazenamento do Azure deve ser usado como um método de compartilhamento Store uma imagem entre vários clusters – isso resultará em Corrupção de dados de configuração de cluster como cada cluster pode escrever dados em conflito para a imagem Store. Para partilhar os pacotes de aplicações aprovisionadas entre vários clusters, utilize [sfpkg] [ 12] ficheiros em vez disso, que podem ser carregados para qualquer arquivo externo com um URI de transferência.

Então, enquanto o ImageStoreConnectionString é configurável, simplesmente usar a configuração padrão. Ao publicar no Azure através do Visual Studio, o parâmetro é automaticamente definido para si em conformidade. Para a implementação programática para clusters alojados no Azure, a cadeia de ligação é sempre "recursos de infraestrutura: ImageStore". Embora em caso de dúvida, seu valor sempre pode ser verificado ao obter o manifesto do cluster por [PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx), ou [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Testar no local e clusters de produção sempre devem ser configuradas para utilizar o fornecedor de serviços de Store de imagem também.

### <a name="next-steps"></a>Passos Seguintes
[Implementar e remover aplicações com o PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
