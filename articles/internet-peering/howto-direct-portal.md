---
title: Criar ou modificar um persco direto utilizando o portal Azure
titleSuffix: Azure
description: Criar ou modificar um persco direto utilizando o portal Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 1a89ce873c53e94036aa4f8ac2c2870365924187
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537213"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Criar ou modificar um persco direto utilizando o portal Azure

Este artigo descreve como criar um Microsoft Direct a espreitar para um Fornecedor de Serviços de Internet ou Fornecedor de Troca de Internet utilizando o portal Azure. Este artigo também mostra como verificar o estado do recurso, atualizá-lo ou eliminá-lo e desprovisioná-lo.

Se preferir, pode completar este guia utilizando a Azure [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e o [passo de observação direto](walkthrough-direct-all.md) antes de iniciar a configuração.
* Se já tem ligações diretas de observação com a Microsoft que não são convertidas em recursos Azure, consulte [Converter um legado Direto a espreitar para um recurso Azure utilizando o portal](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Criar e providenciar um persimento direto

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Inscreva-se no portal e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Criar um espreitamento direto

Como Fornecedor de Serviços de Internet ou Fornecedor de Troca de Internet, pode criar um novo pedido de observação direta [criando um Peering]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Na página **'Criar um Peering',** no separador **Básicos,** preencha as caixas como mostrado aqui:


    ![O screenshot mostra o separador Criar uma página de peering Basics com o novo link Create chamado.](./media/setup-basics-tab.png)

2. Selecione a sua Assinatura Azure.

3. Para o grupo de recursos, pode escolher um grupo de recursos existente da lista de drop-down ou criar um novo grupo selecionando Criar novos. Vamos criar um novo grupo de recursos para este exemplo.

4. O nome corresponde ao nome do recurso e pode ser o que escolher.

5. A região é selecionada automaticamente se escolher um grupo de recursos existente. Se escolheu criar um novo grupo de recursos, também precisa escolher a região de Azure onde deseja que o recurso resida.

    >[!NOTE]
    > A região onde reside um grupo de recursos é independente do local onde pretende criar olhando com a Microsoft. Mas é uma boa prática organizar os seus recursos de observação dentro de grupos de recursos que residem nas regiões mais próximas do Azure. Por exemplo, para espreitar em Ashburn, você pode criar um grupo de recursos no Leste dos EUA ou no Leste dos EUA.

6. Selecione o seu ASN na caixa **PeerASN.**

    >[!IMPORTANT]
    >Só pode escolher uma ASN com Validação Estado como Aprovado antes de submeter um pedido de espreitar. Se acabou de submeter o seu pedido peerAsn, aguarde cerca de 12 horas para que a associação ASN seja aprovada. Se a ASN selecionada estiver pendente de validação, verá uma mensagem de erro. Se não vir a ASN que precisa de escolher, verifique se selecionou a subscrição correta. Em caso afirmativo, verifique se já criou o PeerAsn utilizando **[a assinatura Associate Peer ASN para a Azure](https://go.microsoft.com/fwlink/?linkid=2129592)**.

7. Selecione **Seguinte: Configuração** para continuar.



    ![A screenshot mostra o separador Criar uma página de peering Basics com todos os valores introduzidos.](./media/setup-direct-basics-filled-tab.png)


#### <a name="configure-connections-and-submit"></a>Configure ligações e submeta
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Verificar o espreitamento direto
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modifique um espreitamento direto
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Desprovisionar um olhar direto
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar o Persto cambial utilizando o portal](howto-exchange-portal.md)
* [Converter um legado Exchange olhando para um recurso Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, consulte [as PERGUNTAS Frequentes de Observação da Internet.](faqs.md)
