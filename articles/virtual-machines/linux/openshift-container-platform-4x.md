---
title: Implementar plataforma de contentores openshift 4.x em Azure
description: Implementar a Plataforma de Recipientes OpenShift 4.x em Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f2fb1657c26fce3e1fdd67f36b0c6511b78dd216
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87373543"
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
