---
title: Implantar a plataforma de contêiner OpenShift 4. x no Azure | Microsoft Docs
description: Implante a plataforma de contêiner OpenShift 4. x no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 9f807823b1d0d8079c43b2ba0e074a1c8a91b458
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392686"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Implantar a plataforma de contêiner OpenShift 4. x no Azure

Agora há suporte para a implantação da OpenShift (plataforma de contêiner) 4,2 no Azure por meio do modelo de IPI (infraestrutura provisionada) do instalador.  A página de aterrissagem para experimentar o OpenShift 4 é [try.openshift.com](https://try.openshift.com/). Para instalar o OCP 4,2 no Azure, visite a página do [Gerenciador de cluster do Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) .  As credenciais do Red Hat são necessárias para acessar este site.


## <a name="notes"></a>Notas 

 - Um SP (entidade de serviço) Azure Active Directory (AAD) é necessário para instalar e executar o OCP 4. x no Azure
     - O SP deve receber a permissão de API de **Application. ReadWrite. OwnedBy** para o grafo Azure Active Directory
     - Um Administrador de Locatários do AAD deve conceder consentimento de administrador para que a permissão da API entre em vigor
     - O SP deve receber as funções **colaborador** e **administrador de acesso do usuário** para a assinatura
 - O modelo de instalação para OCP 4. x é diferente de 3. x e não há modelos de Azure Resource Manager disponíveis para implantar o OCP 4. x no Azure
 - Se forem encontrados problemas durante o processo de instalação, entre em contato com a empresa apropriada (Microsoft ou Red Hat)

| Descrição do problema | Ponto de contato |
|-------------------|---------------|
| Problemas específicos do Azure (AAD, SP, assinatura do Azure, etc.)                              | Microsoft |
| Problemas específicos do OpenShift (falhas de instalação/erros, assinatura do Red Hat, etc.) |  Red Hat  |




## <a name="next-steps"></a>Passos seguintes

- [Introdução à plataforma de contêiner do OpenShift](https://docs.openshift.com)
