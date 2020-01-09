---
title: Cadeia de conexão do repositório de imagens do Azure Service Fabric
description: Saiba mais sobre a cadeia de conexão do repositório de imagens, incluindo seus usos e aplicativos para um Cluster Service Fabric.
author: alexwun
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: c3395248188c2a16736cfc8cea262fe163a6944b
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645672"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Entender a configuração de ImageStoreConnectionString

Em algumas de nossas documentações, mencionamos rapidamente a existência de um parâmetro "ImageStoreConnectionString" sem descrever o que realmente significa. E, depois de passar por um artigo como [implantar e remover aplicativos usando o PowerShell][10], parece que tudo o que você faz é copiar/colar o valor, conforme mostrado no manifesto do cluster do cluster de destino. Portanto, a configuração deve ser configurável por cluster, mas quando você cria um cluster por meio do [portal do Azure][11], não há nenhuma opção para definir essa configuração e é sempre "Fabric: ImageStore". Qual é a finalidade dessa configuração?

![Manifesto do cluster][img_cm]

Service Fabric iniciado como uma plataforma para o consumo interno da Microsoft por várias equipes diversas, de modo que alguns aspectos dele sejam altamente personalizáveis – o "Repositório de Imagens" é um aspecto desse tipo. Essencialmente, o Repositório de Imagens é um repositório conectável para armazenar pacotes de aplicativos. Quando seu aplicativo é implantado em um nó no cluster, esse nó baixa o conteúdo do seu pacote de aplicativos do Repositório de Imagens. O ImageStoreConnectionString é uma configuração que inclui todas as informações necessárias para que os clientes e os nós encontrem o Repositório de Imagens correto para um determinado cluster.

Atualmente, há três tipos possíveis de provedores de Repositório de Imagens e suas cadeias de conexão correspondentes são as seguintes:

1. Serviço de Repositório de Imagens: "Fabric: ImageStore"

2. Sistema de arquivos: "arquivo: [caminho do sistema de arquivos]"

3. Azure Storage: "xstore:DefaultEndpointsProtocol=https;AccountName=[...];AccountKey=[...];Container=[...]"

O tipo de provedor usado na produção é o serviço de Repositório de Imagens, que é um serviço de sistema persistente com monitoração de estado que você pode ver na Service Fabric Explorer. 

![Serviço de Repositório de Imagens][img_is]

Hospedar o Repositório de Imagens em um serviço do sistema dentro do próprio cluster elimina dependências externas para o repositório de pacotes e nos dá mais controle sobre a localidade do armazenamento. Melhorias futuras em relação à Repositório de Imagens provavelmente serão direcionadas primeiro ao provedor de Repositório de Imagens, se não exclusivamente. A cadeia de conexão para o provedor de serviços Repositório de Imagens não tem nenhuma informação exclusiva, pois o cliente já está conectado ao cluster de destino. O cliente só precisa saber que os protocolos que visam o serviço do sistema devem ser usados.

O provedor do sistema de arquivos é usado em vez do serviço de Repositório de Imagens para clusters de uma caixa local durante o desenvolvimento para inicializar o cluster ligeiramente mais rápido. A diferença é normalmente pequena, mas é uma otimização útil para a maioria das pessoas durante o desenvolvimento. É possível implantar um cluster One-Box local com outros tipos de provedor de armazenamento, mas geralmente não há motivo para isso, pois o fluxo de trabalho de desenvolvimento/teste permanece o mesmo, independentemente do provedor. O provedor de armazenamento do Azure só existe para suporte herdado de clusters antigos implantados antes da introdução do provedor de serviços de Repositório de Imagens.

Além disso, não o provedor do sistema de arquivos ou o provedor de armazenamento do Azure deve ser usado como um método de compartilhamento de um Repositório de Imagens entre vários clusters-isso resultará na corrupção dos dados de configuração do cluster, pois cada cluster pode gravar dados conflitantes na imagem Armazenadas. Para compartilhar pacotes de aplicativos provisionados entre vários clusters, use os arquivos [sfpkg][12] , que podem ser carregados em qualquer repositório externo com um URI de download.

Assim, embora o ImageStoreConnectionString seja configurável, basta usar a configuração padrão. Ao publicar no Azure por meio do Visual Studio, o parâmetro é definido automaticamente para você de acordo. Para a implantação programática em clusters hospedados no Azure, a cadeia de conexão é sempre "Fabric: ImageStore". No entanto, quando em dúvida, seu valor sempre pode ser verificado recuperando o manifesto do cluster pelo [PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclustermanifest), [.net](https://msdn.microsoft.com/library/azure/mt161375.aspx)ou [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Os clusters de teste e de produção locais sempre devem ser configurados para usar o provedor de serviços de Repositório de Imagens também.

### <a name="next-steps"></a>Passos seguintes
[Implantar e remover aplicativos usando o PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
