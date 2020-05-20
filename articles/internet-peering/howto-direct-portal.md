---
title: Criar ou modificar um epeering direto utilizando o portal Azure
titleSuffix: Azure
description: Criar ou modificar um epeering direto utilizando o portal Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 59b9079b500817c31586c0a566082a867d7e7f41
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683994"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Criar ou modificar um epeering direto utilizando o portal Azure

Este artigo descreve como criar um microsoft direct peering para um Fornecedor de Serviços de Internet ou Fornecedor de Intercâmbio de Internet usando o portal Azure. Este artigo também mostra como verificar o estado do recurso, atualizá-lo ou eliminá-lo e desfortá-lo.

Se preferir, pode completar este guia utilizando o Azure [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e o [passe direto](walkthrough-direct-all.md) antes de iniciar a configuração.
* Se já tem ligações diretas com a Microsoft que não são convertidas em recursos Azure, consulte [Converter um legado Direct peering para um recurso Azure utilizando o portal](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Criar e fornecer um peering direto

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Inscreva-se no portal e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Criar um olhar direto

Como Fornecedor de Serviços de Internet ou Fornecedor de Intercâmbio de Internet, pode criar um novo pedido de peering direto [criando um Peering]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Na página **Criar uma página de Peering,** no separador **Basics,** preencha as caixas como mostrado aqui:


    ![Registar Peering Service](./media/setup-basics-tab.png)

2. Selecione a sua Assinatura Azure.

3. Para o grupo Recursos, pode escolher um grupo de recursos existente da lista de drop-down ou criar um novo grupo selecionando criar novos. Vamos criar um novo grupo de recursos para este exemplo.

4. O nome corresponde ao nome do recurso e pode ser tudo o que escolher.

5. A região é selecionada automaticamente se escolher um grupo de recursos existente. Se escolheu criar um novo grupo de recursos, também precisa escolher a região do Azure onde pretende que o recurso resida.

    >[!NOTE]
    > A região onde reside um grupo de recursos é independente do local onde pretende criar o peering com a Microsoft. Mas é uma boa prática organizar os seus recursos de observação dentro de grupos de recursos que residem nas regiões mais próximas de Azure. Por exemplo, para os olhinhos em Ashburn, você pode criar um grupo de recursos no Leste dos EUA ou LESTE US2.

6. Selecione o seu ASN na caixa **PeerASN.**

    >[!IMPORTANT]
    >Só pode escolher um ASN com Validação state como Aprovado antes de submeter um pedido de peering. Se acabou de apresentar o seu pedido de PeerAsn, aguarde cerca de 12 horas para que a associação ASN seja aprovada. Se o ASN que selecionar estiver pendente de validação, verá uma mensagem de erro. Se não vir o ASN que precisa de escolher, verifique se selecionou a subscrição correta. Em caso afirmativo, verifique se já criou o PeerAsn utilizando a **[assinatura Associate Peer ASN para a assinatura Azure](https://go.microsoft.com/fwlink/?linkid=2129592)**.

7. Selecione **Seguinte: Configuração** para continuar.



    ![Registar Peering Service](./media/setup-direct-basics-filled-tab.png)


#### <a name="configure-connections-and-submit"></a>Configurar ligações e submeter
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificar o peering direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modificar um olhar direto
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Desprovisionamento de um peering direto
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar o peering de troca utilizando o portal](howto-exchange-portal.md)
* [Converter um legado Exchange peering para um recurso Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações, consulte [as FAQs de observação da Internet](faqs.md).
