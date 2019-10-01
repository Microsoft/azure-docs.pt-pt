---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 10/01/2019
ms.author: marsma
ms.openlocfilehash: 67a0832f868fecd47983aa8cddff9cdf139b3f2a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702157"
---
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure Active Directory** (*não* Azure ad B2C). Ou então, selecione **todos os serviços** e, em seguida, pesquise e selecione **Azure Active Directory**.
1. Em **gerenciar**, selecione **registros de aplicativo (Herdado)** .
1. Selecione **Novo registo de aplicação**.
1. Insira um nome para o aplicativo. Por exemplo, *managementapp1*.
1. Para **tipo de aplicativo**, selecione **aplicativo Web/API**.
1. Insira qualquer URL válida na **URL de logon**. Por exemplo, `https://localhost`. O ponto de extremidade não precisa estar acessível, mas deve ser uma URL válida.
1. Selecione **Criar**.
1. Registre a **ID do aplicativo** que aparece na página Visão geral do **aplicativo registrado** . Você usará esse valor em uma etapa posterior.
