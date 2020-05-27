---
title: Converta um legado De espreitar diretamente para um recurso Azure utilizando o portal Azure
titleSuffix: Azure
description: Converta um legado De espreitar diretamente para um recurso Azure utilizando o portal Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 159e15a6be59991fba574e72dcaf2ed740fb10c2
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846180"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Converta um legado De espreitar diretamente para um recurso Azure utilizando o portal Azure

Este artigo descreve como converter um legado existente Espreitar diretamente para um recurso Azure utilizando o portal Azure.

Se preferir, pode completar este guia utilizando o [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e o [passe direto](walkthrough-direct-all.md) antes de iniciar a configuração.


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Converter um legado De espreitar diretamente para um recurso Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Inscreva-se no portal e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Converter um legado de espreitar direto

Como Fornecedor de Serviços de Internet, pode converter ligações de peering direto sinuosos, utilizando a [Criação de um Peering]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Na página **Criar uma página de Peering,** no separador **Basics,** preencha as caixas como mostrado aqui:

    > [!div class="mx-imgBorder"] 
    > ![Registar Peering Service](./media/setup-basics-tab.png)

*    Selecione a sua Assinatura Azure.

* Para o grupo Recursos, pode escolher um grupo de recursos existente da lista de drop-down ou criar um novo grupo selecionando criar novos. Vamos criar um novo grupo de recursos para este exemplo.

* O nome corresponde ao nome do recurso e pode ser tudo o que escolher.

* A região é selecionada automaticamente se escolher um grupo de recursos existente. Se escolheu criar um novo grupo de recursos, também precisa escolher a região do Azure onde pretende que o recurso resida.

>[!NOTE]
>A região onde reside um grupo de recursos é independente do local onde pretende criar o peering com a Microsoft. Mas é uma boa prática organizar os seus recursos de observação dentro de grupos de recursos que residem nas regiões mais próximas de Azure. Por exemplo, para os olhinhos em Ashburn, você pode criar um grupo de recursos no Leste dos EUA ou LESTE US2.

* Selecione o seu ASN na caixa **PeerASN.**

>[!IMPORTANT] 
>Só pode escolher um ASN com Validação state como Aprovado antes de submeter um pedido de peering. Se acabou de apresentar o seu pedido de PeerAsn, aguarde cerca de 12 horas para que a associação ASN seja aprovada. Se o ASN que selecionar estiver pendente de validação, verá uma mensagem de erro. Se não vir o ASN que precisa de escolher, verifique se selecionou a subscrição correta. Em caso afirmativo, verifique se já criou o PeerAsn utilizando a **[assinatura Associate Peer ASN para a assinatura Azure](https://go.microsoft.com/fwlink/?linkid=2129592)**.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Lançar o recurso e configurar as definições básicas
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurar ligações e submeter
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificar o peering direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações, consulte [as FAQs de observação da Internet](faqs.md).

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um epeering direto utilizando o portal](howto-direct-portal.md)
