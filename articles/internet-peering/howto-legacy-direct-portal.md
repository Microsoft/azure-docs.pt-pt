---
title: Converter um legado Espreitar diretamente para um recurso Azure utilizando o portal Azure
titleSuffix: Azure
description: Converter um legado Espreitar diretamente para um recurso Azure utilizando o portal Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 496774f034c861131624b552b1acefca9f399ea8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84700268"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Converter um legado Espreitar diretamente para um recurso Azure utilizando o portal Azure

Este artigo descreve como converter um legado existente Espreitando diretamente para um recurso Azure usando o portal Azure.

Se preferir, pode completar este guia utilizando [o PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e o [passo de observação direto](walkthrough-direct-all.md) antes de iniciar a configuração.


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Converter um legado Espreitar diretamente para um recurso Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Inscreva-se no portal e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Converter um legado Espreitar direto

Como Fornecedor de Serviços de Internet, pode converter ligações de observação diretas antigas utilizando [a Criação de um Peering]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Na página **'Criar um Peering',** no separador **Básicos,** preencha as caixas como mostrado aqui:

    > [!div class="mx-imgBorder"] 
    > ![Registar Peering Service](./media/setup-basics-tab.png)

*    Selecione a sua Assinatura Azure.

* Para o grupo de recursos, pode escolher um grupo de recursos existente da lista de drop-down ou criar um novo grupo selecionando Criar novos. Vamos criar um novo grupo de recursos para este exemplo.

* O nome corresponde ao nome do recurso e pode ser o que escolher.

* A região é selecionada automaticamente se escolher um grupo de recursos existente. Se escolheu criar um novo grupo de recursos, também precisa escolher a região de Azure onde deseja que o recurso resida.

>[!NOTE]
>A região onde reside um grupo de recursos é independente do local onde pretende criar olhando com a Microsoft. Mas é uma boa prática organizar os seus recursos de observação dentro de grupos de recursos que residem nas regiões mais próximas do Azure. Por exemplo, para espreitar em Ashburn, você pode criar um grupo de recursos no Leste dos EUA ou no Leste dos EUA.

* Selecione o seu ASN na caixa **PeerASN.**

>[!IMPORTANT] 
>Só pode escolher uma ASN com Validação Estado como Aprovado antes de submeter um pedido de espreitar. Se acabou de submeter o seu pedido peerAsn, aguarde cerca de 12 horas para que a associação ASN seja aprovada. Se a ASN selecionada estiver pendente de validação, verá uma mensagem de erro. Se não vir a ASN que precisa de escolher, verifique se selecionou a subscrição correta. Em caso afirmativo, verifique se já criou o PeerAsn utilizando **[a assinatura Associate Peer ASN para a Azure](https://go.microsoft.com/fwlink/?linkid=2129592)**.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Lançar o recurso e configurar configurações básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configure ligações e submeta
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificar o espreitamento direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, consulte [as PERGUNTAS Frequentes de Observação da Internet.](faqs.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um espreitamento direto utilizando o portal](howto-direct-portal.md)
