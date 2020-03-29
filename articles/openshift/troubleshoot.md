---
title: Troubleshoot Azure Red Hat OpenShift
description: Resolução de problemas e resolução de problemas comuns com o Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274933"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Resolução de problemas para O Chapéu Vermelho Azure OpenShift

Este artigo detalha algumas questões comuns encontradas ao criar ou gerir clusters OpenShift do Microsoft Azure Red Hat.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Reexperimentando a criação de um aglomerado falhado

Se a criação de um cluster `az` OpenShift de chapéu vermelho azure utilizando o comando CLI falhar, a tentativa de reprovação da criação continuará a falhar.
Utilize `az openshift delete` para eliminar o cluster falhado e, em seguida, criar um cluster inteiramente novo.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Grupo de recursos de cluster OpenShift do chapéu vermelho oculto

Atualmente, `Microsoft.ContainerService/openShiftManagedClusters` o recurso que é automaticamente criado pelo`az openshift create` Azure CLI (comando) está escondido no portal Azure. Na vista do **grupo Recursos,** verifique **os tipos ocultos do Show** para ver o grupo de recursos.

![Screenshot da caixa de verificação do tipo oculto no portal](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>A criação de um cluster resulta em erro que nenhum fornecedor de recursos registado encontrou

Se a criação de `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`um cluster resultar num erro que, então fez parte da pré-visualização e agora precisa de [adquirir instâncias reservadas](https://aka.ms/openshift/buy) à máquina virtual Azure para utilizar o produto geralmente disponível. Uma reserva reduz os seus gastos ao pré-pagar por serviços Azure totalmente geridos. Consulte o [*What are Azure Reservas*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) para saber mais sobre reservas e como poupam dinheiro.

## <a name="next-steps"></a>Passos seguintes

- Experimente o Centro de [Ajuda OpenShift](https://help.openshift.com/) do Chapéu Vermelho para mais informações sobre a resolução de problemas openShift.

- Encontre respostas para [perguntas frequentes sobre o Azure Red Hat OpenShift](openshift-faq.md).
