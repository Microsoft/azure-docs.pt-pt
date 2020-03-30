---
title: Implementar plataforma de contentores OpenShift 4.x em Azure
description: Implemente a Plataforma de Contentores OpenShift 4.x em Azure.
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
ms.openlocfilehash: 213c02b76f822d134729ebc4c0e6bff40f62089f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035443"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Implementar plataforma de contentores OpenShift 4.x em Azure

A implantação da Plataforma de Contentores OpenShift (OCP) 4.2 é agora suportada em Azure através do modelo de Infraestrutura (IPI) de Instalação.  A página de aterragem para experimentar o OpenShift 4 é [try.openshift.com](https://try.openshift.com/). Para instalar o OCP 4.2 em Azure, visite a página Red [Hat OpenShift Cluster Manager.](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)  As credenciais do Chapéu Vermelho são necessárias para aceder a este site.


## <a name="notes"></a>Notas 

 - Um Diretor Ativo Azure (SP) é obrigado a instalar e executar OCP 4.x em Azure
     - O SP deve ser concedido a autorização da API de **Application.ReadWrite.OwnedBy** for Azure Ative Directory Graph
     - Um administrador de inquilino da AAD deve conceder consentimento ao Administrador para esta permissão da API para fazer efeito
     - O SP deve ser concedido **ao Contribuinte** e ao Administrador de Acesso **ao Utilizador** para a subscrição
 - O modelo de instalação para OCP 4.x é diferente de 3.x e não existem modelos de Gestor de Recursos Azure disponíveis para a implementação de OCP 4.x em Azure
 - Se forem encontrados problemas durante o processo de instalação, contacte a empresa adequada (Microsoft ou Red Hat)

| Descrição do Problema | Ponto de contacto |
|-------------------|---------------|
| Questões específicas do Azure (AAD, SP, Assinatura Azure, etc.)                              | Microsoft |
| Questões específicas do OpenShift (Falhas de instalação/erros, subscrição do Chapéu Vermelho, etc.) |  Red Hat  |




## <a name="next-steps"></a>Passos seguintes

- [Começar com plataforma de contentores OpenShift](https://docs.openshift.com)
