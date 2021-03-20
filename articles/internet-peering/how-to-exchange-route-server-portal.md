---
title: Conexão de persituos para parceiros de troca com servidor de rota utilizando o Portal
titleSuffix: Azure
description: Criar ou modificar um persco de Troca com servidor de rota utilizando o portal Azure
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 7e10cd91eadd338217845b1504e8e9160bccfc98
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537196"
---
# <a name="create-or-modify-an-exchange-peering-with-route-server-in-azure-portal"></a>Criar ou modificar um persco de troca com servidor de rota no portal Azure

Este artigo descreve como criar uma Microsoft Exchange a espreitar com um servidor de rota utilizando o portal Azure. Este artigo também mostra como verificar o estado do recurso, atualizá-lo ou eliminá-lo e desprovisioná-lo.


## <a name="before-you-begin"></a>Antes de começar
* Reveja os [pré-requisitos](prerequisites.md) e o [Persimento do Persimento cambial](walkthrough-exchange-all.md) antes de iniciar a configuração.
* Se já tem persigos de Troca com a Microsoft que não são convertidos para recursos Azure, consulte [Converter um legado Exchange olhando para um recurso Azure utilizando o portal](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Criar e providenciar um persto cambial

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Inscreva-se no portal e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering-with-route-server"></a><a name=create></a>Criar um persigoramento de troca com servidor de rota


Como Fornecedor de Troca de Internet, pode criar um pedido de permuta [através da criação de um peering]( https://go.microsoft.com/fwlink/?linkid=2129593).

1. Na página **'Criar um Peering',** no separador **Básicos,** preencha as caixas como mostrado aqui:

    > [!div class="mx-imgBorder"] 
    > ![Registar Peering Service](./media/setup-basics-tab.png)

* Selecione a sua Assinatura Azure.

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

1. Na página 'Criar um Peering', no separador Configuração, preencha as caixas como mostrado aqui:

    > [!div class="mx-imgBorder"]
    > ![Servidor de rota de configuração](./media/setup-exchange-conf-tab-routeserver.png)
 
    * Para o tipo de peering, selecione **Direto**
    * Para a rede Microsoft, selecione **AS8075 com o servidor de rota de câmbio**. 
    * Selecione SKU como **Basic Free**. Não selecione premium grátis, pois está reservado para aplicações especiais.
    * Selecione a localização **do Metrô** onde pretende configurar olhando.

1. Em **Conexões de Peering**, selecione **Criar novos**

1.  Em **Conexão De Pares Diretos,** preencha os seguintes detalhes da sessão de BGP:

    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra o painel de ligação de pares direto com detalhes adicionados.](./media/setup-exchange-conf-tab-direct-route.png)


     * Instalação de observação, selecione a localização física adequada para o espreitamento
     * Provedor de Endereços de Sessão, selecione Peer
     * O prefixo IPv4 da sessão será fornecido pelo peer do fornecedor de câmbio
     * O endereço IPv4 da sessão por pares será selecionado pelo permuta para o servidor de rota a partir do seu intervalo de prefixo IP.
     * O endereço IPv4 da sessão da Microsoft será o IP do router atribuído a partir da gama de prefixos IP.
     * A sessão IPv6 é opcional neste momento.
     * O prefixo IPv4 anunciado máximo pode chegar a 20000. 
     * A utilização para o Serviço de Peering é desativada por predefinição. Pode ser ativado assim que o fornecedor de câmbio tiver assinado um Acordo de Serviço de Pares com a Microsoft.

1. Após a conclusão, clique em **Guardar**. 

1. Em Create a peering, verá a validação passada. Uma vez passada a validação, clique em **Criar**

    > [!div class="mx-imgBorder"]
    > ![Validação de configurações](./media/setup-exchange-conf-tab-validation.png)

    >[!NOTE]
    >Para os fornecedores normais de serviços de Internet (ISP) que são um parceiro do Microsoft Peering Service, é necessário o registo de prefixos IP do cliente. No entanto, no caso de parceiros de intercâmbio com um servidor de rota, é necessário registar as ASNs do cliente e não prefixos. A mesma chave ASN seria válida para o registo de prefixos do cliente.

1. Selecione **ASNs registados** na secção Definições.

    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra o painel de peering com o item do menu A S Ns registado chamado.](./media/setup-exchange-registered-asn.png)

1. **Selecione Adicionar ASN registado** para criar um novo cliente ASN sob a sua subscrição de troca.

    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra o Registo de um painel A S N com nome e caixas de texto A S N.](./media/setup-exchange-register-new-asn.png)

1. Em Registar uma ASN, selecione um Nome, povoe o cliente ASN e clique em Guardar.

1. Sob as ASNs registadas, haverá uma Chave prefixo associada atribuída a cada ASN. Como fornecedor de trocas, terá de fornecer esta Chave prefixo ao seu cliente para que possa registar o Serviço de Permuta sob a sua subscrição.

    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra o painel A S Ns registado com teclas de prefixo.](./media/setup-exchange-register-asn-prefixkey.png)




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
