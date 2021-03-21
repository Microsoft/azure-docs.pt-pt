---
title: Atualize um cluster Azure Red Hat OpenShift
description: Saiba como atualizar um cluster Azure Red Hat OpenShift com openshift 4
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 1/10/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro, openshift, az aro, chapéu vermelho, cli
ms.openlocfilehash: 742da12bd3a10cd1f541e9c43f654cfe7df04340
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720890"
---
# <a name="upgrade-an-azure-red-hat-openshift-aro-cluster"></a>Atualize um cluster Azure Red Hat OpenShift (ARO)

Parte do ciclo de vida do cluster ARO envolve a realização de upgrades periódicos para a versão mais recente do OpenShift. É importante que aplique as mais recentes versões de segurança ou atualização para obter as funcionalidades mais recentes. Este artigo mostra-lhe como atualizar todos os componentes num cluster OpenShift utilizando a Consola Web OpenShift.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que esteja a executar a versão 2.0.65 do Azure CLI. Execute `az --version` para encontrar a versão atual. Se precisar de instalar ou atualizar, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli)

Este artigo pressupõe que você tem acesso a um cluster Azure Red Hat OpenShift como um utilizador com `admin` privilégios.

## <a name="check-for-available-aro-cluster-upgrades"></a>Verifique se estão disponíveis atualizações de clusterS ARO

A partir da consola web OpenShift, selecione Definições de Cluster de **Administração**  >   e abra o separador **Detalhes.**

Se o **Estado de Atualização do** seu cluster refletir **atualizações disponíveis,** pode atualizar o seu cluster.

## <a name="upgrade-your-aro-cluster"></a>Atualize o seu cluster ARO

A partir da consola web no passo anterior, coloque o **Canal** no canal correto para a versão a que pretende atualizar, tal como `stable-4.5` .

Selecione uma versão para atualizar e selecione **Update**. Verá a alteração do estado da atualização para: `Update to <product-version> in progress` . Pode rever o progresso da atualização do cluster observando as barras de progresso para os Operadores e nós.

## <a name="next-steps"></a>Passos seguintes
- [Aprenda a atualizar um cluster ARO usando o OC CLI](https://docs.openshift.com/container-platform/4.6/updating/updating-cluster-between-minor.html)
- Pode encontrar informações sobre os avisos e atualizações disponíveis da Plataforma de Contentores OpenShift na [secção errata](https://access.redhat.com/downloads/content/290/ver=4.6/rhel---8/4.6.0/x86_64/product-errata) do Portal do Cliente.
