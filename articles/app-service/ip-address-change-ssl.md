---
title: Preparar para alterar o endereço IP SSL
description: Se o seu endereço IP SSL for alterado, saiba o que fazer para que seu aplicativo continue a funcionar após a alteração.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 34f35eb67cada6066e35227fcd6a0eaf425ac007
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672394"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Como se preparar para uma alteração de endereço IP SSL

Se você recebeu uma notificação de que o endereço IP SSL do seu aplicativo de serviço de Azure App está sendo alterado, siga as instruções neste artigo para liberar o endereço IP SSL existente e atribuir um novo.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>Liberar endereços IP de SSL e atribuir novos itens

1.  Abra o [Portal do Azure](https://portal.azure.com).

2.  No menu de navegação à esquerda, selecione **serviços de aplicativos**.

3.  Selecione o aplicativo do serviço de aplicativo na lista.

4.  No cabeçalho **configurações** , clique em **configurações de SSL** no painel de navegação esquerdo.

1. Na seção associações SSL, selecione o registro de nome de host. No editor que é aberto, escolha **SSL de SNI** no menu suspenso **tipo de SSL** e clique em **Adicionar Associação**. Quando você vir a mensagem de êxito da operação, o endereço IP existente foi liberado.

6.  Na seção **associações SSL** , selecione novamente o mesmo registro de nome de host com o certificado. No editor que é aberto, desta vez escolha **SSL baseado em IP** no menu suspenso **tipo de SSL** e clique em **Adicionar Associação**. Quando você vir a mensagem de êxito da operação, você adquiriu um novo endereço IP.

7.  Se um registro A (registro DNS que aponta diretamente para seu endereço IP) estiver configurado em seu portal de registro de domínio (provedor de DNS de terceiros ou DNS do Azure), substitua o endereço IP existente pelo novo gerado. Você pode encontrar o novo endereço IP seguindo as instruções na próxima seção.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Localizar o novo endereço IP SSL no portal do Azure

1.  Aguarde alguns minutos e, em seguida, abra o [portal do Azure](https://portal.azure.com).

2.  No menu de navegação à esquerda, selecione **serviços de aplicativos**.

3.  Selecione o aplicativo do serviço de aplicativo na lista.

4.  No cabeçalho **configurações** , clique em **Propriedades** no painel de navegação esquerdo e localize a seção chamada **endereço IP virtual**.

5. Copie o endereço IP e reconfigure o registro de domínio ou o mecanismo de IP.

## <a name="next-steps"></a>Passos seguintes

Este artigo explicou como se preparar para uma alteração de endereço IP que foi iniciada pelo Azure. Para obter mais informações sobre endereços IP no serviço Azure App, consulte [endereços IP SSL e SSL no serviço Azure app](overview-inbound-outbound-ips.md).
