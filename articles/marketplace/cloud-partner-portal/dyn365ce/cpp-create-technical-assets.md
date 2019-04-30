---
title: Criar o Dynamics 365 para ativos técnicos do envolvimento do cliente – Azure Marketplace | Documentos da Microsoft
description: Crie os recursos técnicos para um de Dynamics 365 para a oferta de aplicação de envolvimento do cliente.
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 50c4fd512206cdf17ebb555acb88de2a3f74c2bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472673"
---
# <a name="create-technical-assets-for-azure-application-offer"></a>Criar ativos técnicos para a oferta de aplicação do Azure

Normalmente, de desenvolvimento de soluções utilizando o [SDK para o Dynamics 365 para aplicações de Customer Engagement](https://docs.microsoft.com/dynamics365/customer-engagement/developer/get-started-sdk).  Soluções de tirar uma variedade de formas, conforme descrito em [modelos de programação para o Dynamics 365 para aplicações de Customer Engagement](https://docs.microsoft.com/dynamics365/customer-engagement/developer/programming-models).  Escolha a forma que melhor está em conformidade com os requisitos da solução.  Ao desenvolver uma solução, há uma série de problemas, que deve resolver, tais como opções de extensibilidade, componentes da solução e compatibilidade de versões.  Para obter mais informações, consulte [introdução às soluções](https://docs.microsoft.com/dynamics365/customer-engagement/developer/introduction-solutions).

A maioria das soluções do Dynamics 365 publicadas no AppSource são distribuídos como ficheiros de pacote de aplicativos gerenciados.


## <a name="creating-and-storing-the-package"></a>Criando e armazenando o pacote

A documentação paralela a criação de Dynamics 365 para envolvimento do cliente oferece for encontrada na seção [publicar a sua aplicação no AppSource](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource).  O seguinte contidos detalhes de tópicos como criar o ficheiro de pacote de solução e carregue-o para o armazenamento do Azure:

- [Passo 4: Criar um pacote do AppSource para a sua aplicação](https://docs.microsoft.com/dynamics365/customer-engagement/developer/create-package-app-appsource) -explica como criar um ficheiro comprimido (zip) que representa o seu aplicativo gerenciado e contém: pasta de recursos de solução, DLL de código personalizado, o ficheiro de informações do tipo de MIME, o ícone de pacote de AppSource, licença ficheiros de termos (HTML) e o ficheiro de conteúdo (XML).
- [Passo 5: Store seu pacote de AppSource no armazenamento do Azure e gerar um URL com a chave SAS](https://docs.microsoft.com/dynamics365/customer-engagement/developer/store-appsource-package-azure-storage) -explica como armazenar um ficheiro de pacote do AppSource numa conta de armazenamento de Blobs do Microsoft Azure e utilizar uma chave de assinatura de acesso partilhado (SAS) para partilhar o ficheiro de pacote. O ficheiro de pacote é obtido a partir da sua localização de armazenamento do Azure para obter uma certificação e, em seguida, de publicação e de avaliação de AppSource.


## <a name="next-steps"></a>Passos Seguintes

Se ainda não tiver feito, [criar o seu Dynamics 365 para a oferta de Customer Engagement](./cpp-create-offer.md).  Em seguida, estará pronto para [publicar a sua oferta](./cpp-publish-offer.md).
