---
title: Implementar plataforma de contentores openshift 4.x em Azure
description: Implementar a Plataforma de Recipientes OpenShift 4.x em Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 14af110b5cf50f167d0c4961e26454bc33c6ed7d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81759486"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Implementar plataforma de contentores openshift 4.x em Azure

A implantação da Plataforma de Contentores OpenShift (OCP) 4.2 é agora suportada em Azure através do modelo de Infraestrutura De provisionada (IPI) instalador.  A página de aterragem para experimentar o OpenShift 4 é [try.openshift.com](https://try.openshift.com/). Para instalar o OCP 4.2 em Azure, visite a página [Red Hat OpenShift Cluster Manager.](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)  As credenciais da Red Hat são necessárias para aceder a este site.


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




## <a name="next-steps"></a>Próximos passos

- [Começando com plataforma de recipientes openshift](https://docs.openshift.com)
