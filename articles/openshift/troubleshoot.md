---
title: Resolver problemas do Azure Red Hat OpenShift
description: Solucionar problemas comuns com o Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274933"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Solução de problemas do Azure Red Hat OpenShift

Este artigo detalha alguns problemas comuns encontrados ao criar ou gerenciar Microsoft Azure clusters Red Hat OpenShift.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Repetindo a criação de um cluster com falha

Se estiver criando um cluster do Azure Red Hat OpenShift usando o comando `az` CLI falhar, tentar novamente a criação continuará a falhar.
Use `az openshift delete` para excluir o cluster com falha e, em seguida, crie um cluster totalmente novo.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Grupo de recursos de cluster do Azure Red Hat OpenShift oculto

Atualmente, o recurso de `Microsoft.ContainerService/openShiftManagedClusters` que é criado automaticamente pelo CLI do Azure (`az openshift create` comando) é ocultado no portal do Azure. Na exibição **grupo de recursos** , marque **Mostrar tipos ocultos** para exibir o grupo de recursos.

![Captura de tela da caixa de seleção de tipo oculto no portal](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>A criação de um cluster resulta em erro de que nenhum provedor de recursos registrado foi encontrado

Se a criação de um cluster resultar em um erro que `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, você fazia parte da versão prévia e agora precisa [comprar as instâncias reservadas da máquina virtual do Azure](https://aka.ms/openshift/buy) para usar o produto geralmente disponível. Uma reserva reduz seus gastos por meio do pagamento antecipado de serviços do Azure totalmente gerenciados. Consulte [*o que são as reservas do Azure*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) para saber mais sobre reservas e como elas economizam dinheiro.

## <a name="next-steps"></a>Passos seguintes

- Experimente o [centro de ajuda do Red Hat OpenShift](https://help.openshift.com/) para saber mais sobre a solução de problemas do OpenShift.

- Encontre respostas para [perguntas frequentes sobre o Azure Red Hat OpenShift](openshift-faq.md).
