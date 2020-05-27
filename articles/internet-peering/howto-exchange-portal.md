---
title: Criar ou modificar um persparo de troca utilizando o portal Azure
titleSuffix: Azure
description: Criar ou modificar um persparo de troca utilizando o portal Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 5/21/2020
ms.author: derekol
ms.openlocfilehash: 62aec65498f75e51ecb1df15b525294aef026745
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800517"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Criar ou modificar um persparo de troca utilizando o portal Azure

Este artigo descreve como criar um microsoft exchange peering usando o portal Azure. Este artigo também mostra como verificar o estado do recurso, atualizá-lo ou eliminá-lo e desfortá-lo.

Se preferir, pode completar este guia utilizando o [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e o ["Exchange peering walkthrough"](walkthrough-exchange-all.md) antes de iniciar a configuração.
* Se já tem percevejos de Exchange com a Microsoft que não são convertidos em recursos Azure, consulte [Converter um legado Exchange espreitando um recurso Azure utilizando o portal](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Criar e fornecer um persparo de troca

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Inscreva-se no portal e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Criar um persparo de troca


Como Fornecedor de Intercâmbio de Internet, pode criar um pedido de peering direto [criando um Peering]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Na página **Criar uma página de Peering,** no separador **Basics,** preencha as caixas como mostrado aqui:

>    [!div class="mx-imgBorder"]
>   ![Registar Peering Service](./media/setup-basics-tab.png)

* Selecione a sua Assinatura Azure.

* Para o grupo Recursos, pode escolher um grupo de recursos existente da lista de drop-down ou criar um novo grupo selecionando criar novos. Vamos criar um novo grupo de recursos para este exemplo.

* O nome corresponde ao nome do recurso e pode ser tudo o que escolher.

* A região é selecionada automaticamente se escolher um grupo de recursos existente. Se escolheu criar um novo grupo de recursos, também precisa escolher a região do Azure onde pretende que o recurso resida.

>[!NOTE]
    A região onde reside um grupo de recursos é independente do local onde pretende criar o peering com a Microsoft. Mas é uma boa prática organizar os seus recursos de observação dentro de grupos de recursos que residem nas regiões mais próximas de Azure. Por exemplo, para os olhinhos em Ashburn, você pode criar um grupo de recursos no Leste dos EUA ou LESTE US2.

* Selecione o seu ASN na caixa **PeerASN.**

>[!IMPORTANT] 
Só pode escolher um ASN com Validação state como Aprovado antes de submeter um pedido de peering. Se acabou de apresentar o seu pedido de PeerAsn, aguarde cerca de 12 horas para que a associação ASN seja aprovada. Se o ASN que selecionar estiver pendente de validação, verá uma mensagem de erro. Se não vir o ASN que precisa de escolher, verifique se selecionou a subscrição correta. Em caso afirmativo, verifique se já criou o PeerAsn utilizando a **[assinatura Associate Peer ASN para a assinatura Azure](https://go.microsoft.com/fwlink/?linkid=2129592)**.

* Selecione **Seguinte: Configuração** para continuar.

#### <a name="configure-connections-and-submit"></a>Configurar ligações e submeter
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Verifique um peering de troca
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modificar um peering de troca
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Deprovisionamento de um persparo de troca
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um epeering direto utilizando o portal](howto-direct-portal.md)
* [Converter um legado De espreitar diretamente para um recurso Azure usando o portal](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações, consulte [as FAQs de observação da Internet](faqs.md).
