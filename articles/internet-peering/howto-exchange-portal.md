---
title: Criar ou modificar um persco de Troca utilizando o portal Azure
titleSuffix: Azure
description: Criar ou modificar um persco de Troca utilizando o portal Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/2/2020
ms.author: derekol
ms.openlocfilehash: 69201c97882846fb929b3b6f9a90be6647603bcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84700485"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Criar ou modificar um persco de Troca utilizando o portal Azure

Este artigo descreve como criar uma Microsoft Exchange a espreitar utilizando o portal Azure. Este artigo também mostra como verificar o estado do recurso, atualizá-lo ou eliminá-lo e desprovisioná-lo.

Se preferir, pode completar este guia utilizando [o PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e o [Persimento do Persimento cambial](walkthrough-exchange-all.md) antes de iniciar a configuração.
* Se já tem persigos de Troca com a Microsoft que não são convertidos para recursos Azure, consulte [Converter um legado Exchange olhando para um recurso Azure utilizando o portal](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Criar e providenciar um persto cambial

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Inscreva-se no portal e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Criar um perscruta de intercâmbio


Como Fornecedor de Troca de Internet, pode criar um pedido de permuta [através da criação de um peering]( https://go.microsoft.com/fwlink/?linkid=2129593).

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

* Selecione **Seguinte: Configuração** para continuar.

#### <a name="configure-connections-and-submit"></a>Configure ligações e submeta
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Verifique um perscruta de trocas
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modificar um persco de troca
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Deprovisionar um perscruta de trocas
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um espreitamento direto utilizando o portal](howto-direct-portal.md)
* [Converter um legado Espreitar diretamente para um recurso Azure utilizando o portal](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, consulte [as PERGUNTAS Frequentes de Observação da Internet.](faqs.md)
