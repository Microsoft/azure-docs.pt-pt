---
title: Preparar a alteração de endereço IP de entrada - serviço de aplicações do Azure
description: Se o endereço IP de entrada vai ser alterado, saiba o que fazer para que a sua aplicação continua a funcionar após a alteração.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: aaa89b5a3bb1af6878ed21e0160a534a1c989228
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270069"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Como se preparar para uma alteração de endereço IP entrada

Se tiver recebido uma notificação de que está a alterar o endereço IP de entrada da sua aplicação de serviço de aplicações do Azure, siga as instruções neste artigo.

## <a name="determine-if-you-have-to-do-anything"></a>Determinar se tem de fazer nada

* Opção 1: Se a sua aplicação de serviço de aplicações não tiver um domínio personalizado, é necessária nenhuma ação.

* Opção 2: Se apenas um registo CNAME (registo DNS que aponte para um URI) é configurado no seu Portal de registo de domínio (terceiros fornecedor DNS ou o DNS do Azure), é necessária nenhuma ação.

* Opção 3: Se um registo (registo DNS que aponte diretamente para o seu endereço IP) estiver configurado no seu Portal de registo de domínio (terceiros fornecedor DNS ou o DNS do Azure), substitua o endereço IP existente pelo novo. Pode encontrar o novo endereço IP ao seguir as instruções na secção seguinte.

* Opção 4: Se a sua aplicação estiver atrás de um balanceador de carga, o filtro de IP, ou qualquer outro mecanismo IP que requer o endereço IP da sua aplicação, substitua o endereço IP existente pelo novo. Pode encontrar o novo endereço IP ao seguir as instruções na secção seguinte.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Encontrar o endereço de IP de entrada novo no portal do Azure

O endereço IP de entrada novo que está a ser dado à sua aplicação estiver no portal no **endereço Virtual IP** campo. Este endereço IP novo e antigo estão ligados à sua aplicação agora e, mais tarde antigo será desligado.

1.  Abra o [Portal do Azure](https://portal.azure.com).

2.  No menu de navegação esquerdo, selecione **dos serviços de aplicações**.

3.  Selecione a sua aplicação de serviço de aplicações na lista.

1.  Se a aplicação for uma aplicação de funções, veja [aplicação de funções endereço IP de entrada](../azure-functions/ip-addresses.md#function-app-inbound-ip-address).

4.  Sob o **configurações** cabeçalho, clique em **propriedades** na navegação à esquerda e localize a seção rotulada **endereço Virtual IP**.

5. Copie o endereço IP e reconfigurar o seu registo de domínio ou o mecanismo IP.

## <a name="next-steps"></a>Passos Seguintes

Este artigo explicou como se preparar para uma alteração de endereço IP que foi iniciada pelo Azure. Para obter mais informações sobre os endereços IP no serviço de aplicações do Azure, consulte [de entrada e endereços IP de saída no serviço de aplicações do Azure](overview-inbound-outbound-ips.md).
