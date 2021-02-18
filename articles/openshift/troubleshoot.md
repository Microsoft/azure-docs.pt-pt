---
title: Resolução de problemas Azure Red Hat OpenShift
description: Resolução de problemas e resolver problemas comuns com Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: af66abff3507279dd1954fd83627900578229866
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100632956"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Resolução de problemas para Azure Red Hat OpenShift

Este artigo detalha algumas questões comuns encontradas ao criar ou gerir os clusters Microsoft Azure Red Hat OpenShift.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Recandidruptar a criação de um aglomerado falhado

Se a criação de um cluster Azure Red Hat OpenShift utilizando o `az` comando CLI falhar, a nova tentativa da criação continuará a falhar.
Utilize `az openshift delete` para eliminar o cluster falhado e, em seguida, crie um cluster inteiramente novo.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Grupo de recursos de cluster OpenShift de chapéu vermelho escondido

Atualmente, o `Microsoft.ContainerService/openShiftManagedClusters` recurso que é automaticamente criado pelo Azure CLI `az openshift create` (comando) está escondido no portal Azure. Na vista do **grupo De recurso,** verifique **os tipos ocultos do Show** para visualizar o grupo de recursos.

![Screenshot da caixa de verificação do tipo oculto no portal](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>A criação de um cluster resulta num erro que nenhum fornecedor de recursos registado encontrou

Se a criação de um cluster resultar num erro `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview` que, então, fez parte da pré-visualização e agora precisa de [adquirir a máquina virtual Azure para](https://aka.ms/openshift/buy) utilizar o produto geralmente disponível. Uma reserva reduz os seus gastos pré-pagando por serviços Azure totalmente geridos. Consulte [*o Que são Reservas Azure*](../cost-management-billing/reservations/save-compute-costs-reservations.md) para saber mais sobre reservas e como lhe poupam dinheiro.

## <a name="next-steps"></a>Passos seguintes

- Experimente o [Centro de Ajuda OpenShift do Chapéu Vermelho](https://help.openshift.com/) para obter mais informações sobre a resolução de problemas do OpenShift.

- Encontre respostas para [perguntas frequentes sobre O Azure Red Hat OpenShift](openshift-faq.md).