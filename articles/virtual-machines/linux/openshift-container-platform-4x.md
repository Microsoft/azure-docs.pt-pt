---
title: Implementar plataforma de contentores openshift 4.x em Azure
description: Implementar a Plataforma de Recipientes OpenShift 4.x em Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: e8650802b4add9b33664205367bb3242b32b9754
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670386"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Implementar plataforma de contentores openshift 4.x em Azure

A implantação da Plataforma de Contentores OpenShift (OCP) 4.2 é agora suportada em Azure através do modelo Installer-Provisioned Infrastructure (IPI).  A página de aterragem para experimentar o OpenShift 4 é [try.openshift.com](https://try.openshift.com/). Para instalar o OCP 4.2 em Azure, visite a página [Red Hat OpenShift Cluster Manager.](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)  As credenciais da Red Hat são necessárias para aceder a este site.


## <a name="notes"></a>Notas 

 - Um Diretor de Serviço Ativo Azure (AAD) é necessário para instalar e executar OCP 4.x em Azure
     - O SP deve ter a permissão da API de **Aplicação.ReadWrite.OwnedBy** para Azure Ative Directory Graph
     - Um administrador inquilino da AAD deve conceder consentimento administrativo para esta permissão da API para entrar em vigor
     - O SP deve ter funções **de Administrador de Contribuinte** e De Acesso ao **Utilizador** para a subscrição
 - O modelo de instalação para OCP 4.x é diferente de 3.x e não existem modelos de Gestor de Recursos Azure disponíveis para a implementação do OCP 4.x em Azure
 - Se os problemas forem encontrados durante o processo de instalação, contacte a empresa adequada (Microsoft ou Red Hat)

| Descrição do Problema | Ponto de contacto |
|-------------------|---------------|
| Questões específicas do Azure (AAD, SP, Azure Subscription, etc.)                              | Microsoft |
| Questões específicas do OpenShift (Falhas/erros de instalação, subscrição do Chapéu Vermelho, etc.) |  Red Hat  |




## <a name="next-steps"></a>Passos seguintes

- [Começando com plataforma de recipientes openshift](https://docs.openshift.com)
