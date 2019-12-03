---
title: Preparar para alteração de endereço IP de entrada
description: Se o endereço IP de entrada for alterado, saiba o que fazer para que seu aplicativo continue a funcionar após a alteração.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 999511c4759ee761f028a61fbfaa0310ac93d9b4
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672423"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Como preparar para uma alteração de endereço IP de entrada

Se você recebeu uma notificação de que o endereço IP de entrada do seu aplicativo de serviço de Azure App está sendo alterado, siga as instruções neste artigo.

## <a name="determine-if-you-have-to-do-anything"></a>Determine se você precisa fazer algo

* Opção 1: se o aplicativo do serviço de aplicativo não tiver um domínio personalizado, nenhuma ação será necessária.

* Opção 2: se apenas um registro CNAME (registro DNS apontando para um URI) estiver configurado no portal de registro de domínio (provedor de DNS de terceiros ou DNS do Azure), nenhuma ação será necessária.

* Opção 3: se um registro A (registro DNS apontando diretamente para seu endereço IP) estiver configurado no portal de registro de domínio (provedor de DNS de terceiros ou DNS do Azure), substitua o endereço IP existente pelo novo. Você pode encontrar o novo endereço IP seguindo as instruções na próxima seção.

* Opção 4: se seu aplicativo estiver atrás de um balanceador de carga, filtro IP ou qualquer outro mecanismo de IP que exija o endereço IP do seu aplicativo, substitua o endereço IP existente pelo novo. Você pode encontrar o novo endereço IP seguindo as instruções na próxima seção.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Localizar o novo endereço IP de entrada no portal do Azure

O novo endereço IP de entrada que está sendo dado ao seu aplicativo está no portal no campo **endereço IP virtual** . Esse novo endereço IP e o antigo estão conectados ao seu aplicativo agora e, mais tarde, o antigo será desconectado.

1.  Abra o [Portal do Azure](https://portal.azure.com).

2.  No menu de navegação à esquerda, selecione **serviços de aplicativos**.

3.  Selecione o aplicativo do serviço de aplicativo na lista.

1.  Se o aplicativo for um aplicativo de funções, consulte [endereço IP de entrada do aplicativo de funções](../azure-functions/ip-addresses.md#function-app-inbound-ip-address).

4.  No cabeçalho **configurações** , clique em **Propriedades** no painel de navegação esquerdo e localize a seção chamada **endereço IP virtual**.

5. Copie o endereço IP e reconfigure o registro de domínio ou o mecanismo de IP.

## <a name="next-steps"></a>Passos seguintes

Este artigo explicou como se preparar para uma alteração de endereço IP que foi iniciada pelo Azure. Para obter mais informações sobre endereços IP no serviço Azure App, consulte [endereços IP de entrada e saída no serviço Azure app](overview-inbound-outbound-ips.md).
