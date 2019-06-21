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
ms.openlocfilehash: 2553aa95d5028c510b4e1a1b7f51a9f410bcea51
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154853"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Registar o fornecedor de recursos de Microsoft.VMwareCloudSimple na sua subscrição do Azure

O serviço de CloudSimple permite-lhe consumir a solução de VMware do Azure por CloudSimple. Pode registrar o serviço de Microsoft.VMwareCloudSimple como o fornecedor de recursos.

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
