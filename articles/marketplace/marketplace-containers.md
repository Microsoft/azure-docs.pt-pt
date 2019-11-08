---
title: Os contêineres oferecem o guia de publicação para o Azure Marketplace
description: Este artigo descreve os requisitos para publicar contêineres no Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: ffdc6b22209c1d334ac7b75c7079e755a7405154
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822999"
---
# <a name="containers-offer-publishing-guide"></a>Guia de publicação de contêineres oferece

O contêiner oferece ajuda para publicar sua imagem de contêiner no Azure Marketplace. Use este guia para entender os requisitos para esta oferta. 

Essas são ofertas de transação que são implantadas e cobradas pelo Marketplace. O plano de ação que um usuário vê é "obter agora".

Use o tipo de oferta de contêiner quando sua solução for uma imagem de contêiner do Docker provisionado como um serviço de contêiner do Azure baseado em kubernetes.

>[!NOTE]
>Por exemplo, um serviço de contêiner do Azure baseado em kubernetes como o serviço kubernetes do Azure ou instâncias de contêiner do Azure, a escolha de clientes do Azure para um tempo de execução de contêiner baseado em kubernetes.  

A Microsoft atualmente dá suporte a modelos de licenciamento gratuitos e BYOL (traga sua própria licença).

## <a name="containers-offer"></a>Oferta de contêineres

| Requisito | Detalhes |  
|:--- |:--- |  
| Cobrança e medição | Suporte ao modelo de cobrança gratuito ou BYOL. |  
| Imagem criada a partir de Dockerfile | As imagens de contêiner devem ser baseadas na especificação de imagem do Docker e devem ser criadas a partir de um Dockerfile.<ul> <li>Para obter mais informações sobre a criação de imagens do Docker, visite a seção uso localizada em [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hospedagem no ACR | As imagens de contêiner devem ser hospedadas em um repositório de ACR (registro de contêiner do Azure).<ul> <li>Para obter mais informações sobre como trabalhar com o ACR, visite o guia de início rápido: criar um registro de contêiner usando a página portal do Azure localizada em [docs.Microsoft.com/Azure/container-Registry/container-Registry-Get-Started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Marcação de imagem | As imagens de contêiner devem conter pelo menos uma marca (máximo de marcas: 16).<ul> <li>Para obter mais informações sobre como marcar uma imagem, visite a página de marcas do Docker localizada em [docs.Docker.com/Engine/Reference/CommandLine/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Passos seguintes

Se você ainda não fez isso, 

- [Registre-se](https://azuremarketplace.microsoft.com/sell) no Marketplace.

Se você estiver registrado e estiver criando uma nova oferta ou trabalhando em uma existente,

- [Faça logon no portal do Cloud Partner](https://cloudpartner.azure.com) para criar ou concluir sua oferta.
- Consulte [contêineres](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) para obter mais informações.
