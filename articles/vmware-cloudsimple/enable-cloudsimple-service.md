---
title: Ativar a solução de VMware de Azure pelo serviço CloudSimple
description: Descreve como ativar o serviço de CloudSimple numa subscrição do Azure e, em seguida, registar o fornecedor de recursos de CLoudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cacd5510147ce997efec922f4b4656956a098d88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676942"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Registar o fornecedor de recursos de Microsoft.VMwareCloudSimple na sua subscrição do Azure

O serviço de CloudSimple permite-lhe consumir a solução de VMware do Azure por CloudSimple. Para utilizar o serviço de CloudSimple, terá primeiro de ser ativado na sua subscrição do Azure. Em seguida, pode registar o serviço de Microsoft.VMwareCloudSimple como o fornecedor de recursos.

## <a name="enable-the-cloudsimple-service"></a>Ativar o serviço de CloudSimple

Para ativar o serviço de CloudSimple na sua subscrição do Azure, abra um pedido de suporte com [suporte da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Selecione as opções seguintes ao submeter o pedido.

* Tipo de problema: **Technical**
* Subscrição: **O ID de subscrição**
* Tipo de serviço: **Solução de VMware ao CloudSimple**
* Tipo de problema: **Quota de nós dedicado**
* Subtipo de problema: **Aumentar a quota de nós dedicados**
* Assunto: **Ativar o serviço de CloudSimple**

Também pode contactar o seu representante de conta da Microsoft em [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com). Forneça o seu ID de subscrição do Azure no e-mail.  

Depois do serviço de CloudSimple está ativado para a sua subscrição, pode ativar o fornecedor de recursos na subscrição.

## <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **Todos os serviços**.

3. Procure e selecione **subscrições**.

    ![Selecionar subscrições](media/cloudsimple-service-select-subscriptions.png)

4. Selecione a subscrição na qual pretende ativar o serviço CloudSimple.

5. Clique em **fornecedores de recursos** para a subscrição.

6. Uso **Microsoft.VMwareCloudSimple** para filtrar o fornecedor de recursos.

7. Selecione o fornecedor de recursos e clique em **registar**.

    ![Registar o fornecedor de recursos](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [criar um serviço de CloudSimple](create-cloudsimple-service.md)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)