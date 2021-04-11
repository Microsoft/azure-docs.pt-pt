---
title: Remover Start/Stop VMs v2 (pré-visualização)
description: Este artigo descreve como remover a função Iniciar/Parar VMs v2 (pré-visualização).
services: azure-functions
ms.subservice: ''
ms.date: 03/30/2021
ms.topic: conceptual
ms.openlocfilehash: b4308be8f7494c1cb6f6b4839fc5a2e9717668e3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111801"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>Como remover VMs de início/paragem v2 (pré-visualização)

Depois de ativar a função Iniciar/Parar VMs v2 (pré-visualização) para gerir o estado de funcionamento dos seus VMs Azure, poderá decidir parar de o utilizar. A remoção desta funcionalidade pode ser feita eliminando o grupo de recursos dedicado a armazenar os seguintes recursos de apoio aos VMs de início/paragem v2 (pré-visualização):

- As aplicações Azure Functions
- Horários em Azure Logic Apps
- A instância De Insights de Aplicação
- Conta de armazenamento do Azure

## <a name="delete-the-dedicated-resource-group"></a>Eliminar o grupo de recursos dedicado

Para eliminar o grupo de recursos, siga os passos descritos no grupo de [recursos Azure Resource Manager e](../../azure-resource-manager/management/delete-resource-group.md) no artigo de eliminação de recursos.

## <a name="next-steps"></a>Passos seguintes

Para relançar esta função, consulte [Iniciar/Parar v2](deploy.md) (pré-visualização).