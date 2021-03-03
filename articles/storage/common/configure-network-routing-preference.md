---
title: Configurar preferência de encaminhamento de rede
titleSuffix: Azure Storage
description: Configure a preferência de encaminhamento de rede para a sua conta de armazenamento Azure para especificar como o tráfego de rede é encaminhado para a sua conta a partir de clientes através da Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/21/2020
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: fb427de170764e5cd1fca57f9fb2d1410829e521
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700906"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Configure preferência de encaminhamento de rede para armazenamento Azure

Este artigo descreve como pode configurar a preferência de encaminhamento de rede e pontos finais específicos da rota para a sua conta de armazenamento. 

A preferência de encaminhamento de rede especifica como o tráfego de rede é encaminhado para a sua conta a partir de clientes através da internet. Os pontos finais específicos da rota são novos pontos finais que o Azure Storage cria para a sua conta de armazenamento. Estes pontos finais encaminham o tráfego por um caminho desejado sem alterar a sua preferência de encaminhamento padrão. Para saber mais, consulte [a preferência de encaminhamento de rede para armazenamento Azure](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Configure a preferência de encaminhamento para o ponto final público predefinido

Por padrão, a preferência de encaminhamento para o ponto final público da conta de armazenamento é definida para a rede global da Microsoft. Pode escolher entre a rede global da Microsoft e o encaminhamento da Internet como a preferência por padrão de encaminhamento para o ponto final público da sua conta de armazenamento. Para saber mais sobre a diferença entre estes dois tipos de encaminhamento, consulte [a preferência de encaminhamento de rede para armazenamento Azure](network-routing-preference.md). 

Para alterar a sua preferência de encaminhamento para o encaminhamento da Internet:

1. Navegue para a sua conta de armazenamento no portal.

2. Em **Definições**, escolha **Rede**.

    > [!div class="mx-imgBorder"]
    > ![Opção de menu de rede](./media/configure-network-routing-preference/networking-option.png)

3.  No separador **Firewalls e redes virtuais,** no **âmbito do Network Encaminhamento,** altere a definição **de preferência de encaminhamento** para o **encaminhamento da Internet**.

4.  Clique em **Guardar**.

    > [!div class="mx-imgBorder"]
    > ![opção de encaminhamento de internet](./media/configure-network-routing-preference/internet-routing-option.png)

## <a name="configure-a-route-specific-endpoint"></a>Configure um ponto final específico da rota

Também pode configurar um ponto final específico da rota. Por exemplo, pode definir a preferência de encaminhamento para o ponto final padrão para o *encaminhamento da Internet*, e depois publicar um ponto final específico de rota que permite que o tráfego entre clientes na internet e a sua conta de armazenamento seja encaminhado através da rede global da Microsoft.

1.  Navegue para a sua conta de armazenamento no portal.

2.  Em **Definições**, escolha **Rede**.

3.  No separador **Firewalls e redes virtuais,** em **Pontos finais específicos da rota,** escolha a preferência de encaminhamento do seu ponto final específico da rota e, em seguida, clique em **Guardar**. Esta preferência afeta apenas o ponto final específico da rota. Esta preferência não afeta a sua preferência de encaminhamento padrão.  

    A imagem a seguir mostra a opção **de encaminhamento de rede** da Microsoft selecionada.

    > [!div class="mx-imgBorder"]
    > ![Opção de encaminhamento de rede microsoft](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Encontre o nome do ponto final para um ponto final específico da rota

Se configurar um ponto final específico da rota, pode encontrar o ponto final nas propriedades da sua conta de armazenamento.

1.  Em **Definições,** escolha **Propriedades.**

    > [!div class="mx-imgBorder"]
    > ![opção de menu de propriedades](./media/configure-network-routing-preference/properties.png)

2.  O ponto final **de encaminhamento de rede da Microsoft** é mostrado para cada serviço que suporta preferências de encaminhamento. Esta imagem mostra o ponto final para os serviços de blob e ficheiros.

    > [!div class="mx-imgBorder"]
    > ![Opção de encaminhamento de rede da Microsoft para pontos finais específicos da rota](./media/configure-network-routing-preference/routing-url.png)


## <a name="see-also"></a>Ver também

- [Preferência de encaminhamento de rede](network-routing-preference.md)
- [Configurar as firewalls e as redes virtuais do Armazenamento do Microsoft Azure](storage-network-security.md)
- [Recomendações de segurança para armazenamento blob](../blobs/security-recommendations.md)
