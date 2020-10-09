---
title: Preparar para alteração de endereço IP de entrada
description: Se o seu endereço IP de entrada for alterado, saiba o que fazer para que a sua aplicação continue a funcionar após a mudança.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 999511c4759ee761f028a61fbfaa0310ac93d9b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74672423"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Como se preparar para uma alteração de endereço IP de entrada

Se recebeu a notificação de que o endereço IP de entrada da sua aplicação Azure App Service está a ser alterado, siga as instruções deste artigo.

## <a name="determine-if-you-have-to-do-anything"></a>Determine se tem que fazer alguma coisa

* Opção 1: Se a sua aplicação de Serviço de Aplicações não tiver um Domínio Personalizado, não é necessária qualquer ação.

* Opção 2: Se apenas um registo CNAME (registo DNS apontando para um URI) estiver configurado no seu Portal de Registo de Domínio (fornecedor de DNS de terceiros ou Azure DNS), não é necessária qualquer ação.

* Opção 3: Se um registo A (registo DNS que aponta diretamente para o seu endereço IP) estiver configurado no seu Portal de Registo de Domínio (fornecedor de DNS de terceiros ou Azure DNS), substitua o endereço IP existente pelo novo. Pode encontrar o novo endereço IP seguindo as instruções na secção seguinte.

* Opção 4: Se a sua aplicação estiver por trás de um equilibrador de carga, filtro IP ou qualquer outro mecanismo IP que exija o endereço IP da sua aplicação, substitua o endereço IP existente pelo novo. Pode encontrar o novo endereço IP seguindo as instruções na secção seguinte.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Encontre o novo endereço IP de entrada no portal Azure

O novo endereço IP de entrada que está a ser dado à sua aplicação encontra-se no portal no campo **de endereços IP Virtual.** Tanto este novo endereço IP como o antigo estão agora ligados à sua aplicação, e mais tarde o antigo será desligado.

1.  Abra o [portal do Azure](https://portal.azure.com).

2.  No menu de navegação à esquerda, selecione **Serviços de Aplicações**.

3.  Selecione a sua aplicação De Serviço de Aplicações a partir da lista.

1.  Se a aplicação for uma aplicação de função, consulte [o endereço IP de entrada de aplicações da Função.](../azure-functions/ip-addresses.md#function-app-inbound-ip-address)

4.  No cabeçalho **Definições,** clique em **Propriedades** na navegação à esquerda e encontre a secção com a etiqueta **do endereço IP virtual**.

5. Copie o endereço IP e reconfigure o seu registo de domínio ou mecanismo de IP.

## <a name="next-steps"></a>Passos seguintes

Este artigo explicou como se preparar para uma alteração de endereço IP que foi iniciada pela Azure. Para obter mais informações sobre endereços IP no Azure App Service, consulte [endereços IP de entrada e saída no Serviço de Aplicações Azure.](overview-inbound-outbound-ips.md)
