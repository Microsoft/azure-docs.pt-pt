---
title: Guia de publicação de ofertas de contentores no Azure Marketplace
description: Este artigo descreve os requisitos para a publicação de ofertas de contentores no Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 83c575aa40b80d9a8e39263e89a5e7860c8f8774
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95741666"
---
# <a name="publishing-guide-for-azure-container-offers"></a>Guia de publicação de ofertas de contentores Azure

O recipiente Azure oferece-o para o ajudar a publicar a sua imagem de contentor no Azure Marketplace. Utilize este guia para compreender os requisitos para este tipo de oferta.

As ofertas de contentores Azure são ofertas de transações que são implementadas e faturadas através do Azure Marketplace. A opção de listagem que um utilizador vê é "Get It Now".

Utilize o tipo de oferta de recipiente Azure quando a sua solução é uma imagem de contentor Docker que é configurada como uma instância de recipiente Azure baseada em Kubernetes.

> [!NOTE]
> Uma instância do Azure Container é uma instância de estivador de tempo de execução que fornece a maneira mais rápida e simples de executar um recipiente em Azure, sem ter que gerir nenhuma máquina virtual e sem ter que adotar um serviço de nível mais alto. As instâncias do contentor podem ser implantadas diretamente para Azure ou orquestradas pela Azure Kubernetes Services ou pelo Motor de Serviço Azure Kubernetes.  

A Microsoft suporta atualmente modelos de licenciamento gratuitos e de trazer a sua própria licença (BYOL).

## <a name="container-offer-requirements"></a>Requisitos de oferta de contentores

| Requisito | Detalhes |  
|:--- |:--- |  
| Faturação e medição | Apoie o modelo de faturação gratuito ou BYOL.<br><br> |  
| Imagem construída a partir de um Dockerfile | As imagens do contentor devem basear-se na especificação de imagem do Docker e construídas a partir de um Dockerfile.<br> <br>Para obter mais informações sobre a construção de imagens Docker, consulte a secção "Utilização" da [referência Dockerfile](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Hospedagem num repositório de registo de contentores Azure | As imagens do contentor devem ser acolhidas num repositório de registo de contentores Azure.<br> <br>Para obter mais informações sobre o trabalho com o Registo do Contentor Azure, consulte [Quickstart: Criar um registo privado de contentores utilizando o portal Azure](../container-registry/container-registry-get-started-portal.md).<br><br> |  
| Etiquetagem de imagens | As imagens do recipiente devem conter pelo menos uma etiqueta (número máximo de etiquetas: 16).<br><br>Para obter mais informações sobre a marcação de uma imagem, consulte a `docker tag` página no site da [Documentação do Docker.](https://docs.docker.com/engine/reference/commandline/tag)<br><br> |  

## <a name="next-steps"></a>Passos seguintes

- Para preparar os ativos técnicos para uma oferta de contentores, consulte [os ativos técnicos do contentor Create Azure](create-azure-container-technical-assets.md).

- Para criar uma oferta de recipiente Azure, consulte criar uma oferta de [recipiente Azure no Azure Marketplace](create-azure-container-offer.md) para obter mais informações.
