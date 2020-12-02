---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 354811b1db31731913f2a99287b0e4a71b2aff61
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468855"
---
Antes de começar, certifique-se de que:

* A subscrição do Microsoft Azure tem de estar ativada para um recurso do Azure Stack Edge. Certifique-se de que utilizou uma subscrição suportada, como [o Microsoft Enterprise Agreement (EA),](https://azure.microsoft.com/overview/sales-number/)o Cloud Solution Provider [(CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)ou [o Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Tem acesso ao proprietário ou colaborador a nível de grupo de recursos para os recursos Azure Stack Edge/Azure Storage Gateway, IoT Hub e Azure Storage.

  * Para criar qualquer recurso Azure Stack Edge, deverá ter permissões como contribuinte (ou superior) a nível de grupo de recursos. Também tem de se certificar de que o `Microsoft.DataBoxEdge` fornecedor está registado. Para obter informações sobre como realizar o registo, aceda a [Registar fornecedor de recursos](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Para criar qualquer recurso IoT Hub, certifique-se de que o fornecedor microsoft.devices está registado. Para obter informações sobre como realizar o registo, aceda a [Registar fornecedor de recursos](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Para criar um recurso de conta de Armazenamento, mais uma vez precisa de um contribuinte ou de um acesso mais elevado ao nível do grupo de recursos. O Azure Storage é, por defeito, um fornecedor de recursos registado.
* Tem acesso administrativo ou utilizador à Azure Ative Directory Graph API. Para mais informações, consulte [a Azure Ative Directory Graph API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Tem a conta do Storage do Microsoft Azure com credenciais de acesso.

