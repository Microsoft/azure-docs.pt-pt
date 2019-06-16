---
title: Resolver problemas do Azure de Red Hat OpenShift | Documentos da Microsoft
description: Resolver problemas comuns com a Azure Red Hat OpenShift
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 7f2bdf643f12671bec3d0c087d8775844099fe9a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66306253"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Resolução de problemas do Azure de Red Hat OpenShift

Este artigo fornece detalhes sobre alguns problemas comuns encontrados ao criar ou gerir clusters do Microsoft Azure Red Hat OpenShift.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Repetir a criação de um cluster com falhas

Se criar um Azure Red Hat OpenShift cluster usando o `az` falha do comando da CLI, repetir o criar irá continuar a falhar.
Utilize `az openshift delete` para eliminar o cluster com falhas, em seguida, crie um cluster totalmente novo.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Grupo de recursos de cluster do Azure Red Hat OpenShift oculto

Atualmente, o `Microsoft.ContainerService/openShiftManagedClusters` recurso que é criado automaticamente pela CLI do Azure (`az openshift create` comandos) está oculta no portal do Azure. Na **grupo de recursos** modo de exibição, verificação **mostrar tipos ocultos** para ver o grupo de recursos.

![Captura de ecrã da caixa de seleção de tipo ocultos no portal](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Criar um cluster de resultados no erro não encontrado nenhum fornecedor de recursos registado

Se criar um resultados de cluster num erro, que `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, foram a parte da pré-visualização e agora precisa [instâncias de reservada a compra de máquina virtual do Azure](https://aka.ms/openshift/buy) para usar o produto em disponibilidade geral. Uma reserva reduz seu gastar ao pagar previamente para serviços do Azure completamente geridos. Consulte a [ *quais são as reservas do Azure* ](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) para saber mais sobre as reservas e como poupar dinheiro.

## <a name="next-steps"></a>Passos Seguintes

- Experimente o [Centro de ajuda do Red Hat OpenShift](https://help.openshift.com/) para obter mais on OpenShift resolução de problemas.

- Encontre respostas para [perguntas frequentes sobre a Azure Red Hat OpenShift](openshift-faq.md).
