---
title: incluir ficheiro
titleSuffix: Azure
description: incluir ficheiro
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e3e87e59f5b3c95051d9ee53e4b8d87afe9d9ba8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774565"
---
Esta seção descreve como executar as seguintes operações de modificação para o emparelhamento direto:

### <a name="add-exchange-peering-connections"></a>Adicionar conexões de emparelhamento do Exchange

1. Clique no botão **+ Adicionar conexões** na parte superior e configure uma nova conexão de emparelhamento.
    > [!div class="mx-imgBorder"]
    > ![exibição de recurso de emparelhamento](../media/setup-exchange-modify-addconnection.png)
1. Preencha o formulário de **conexão de emparelhamento do Exchange** e clique em **salvar**. Para obter ajuda com a configuração de uma conexão de emparelhamento, examine as etapas na seção "criar e provisionar um emparelhamento direto" acima.
    > [!div class="mx-imgBorder"]
    > ![exibição de recurso de emparelhamento](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Remover conexões de emparelhamento do Exchange

1. Clique em uma conexão de emparelhamento que você deseja excluir e, em seguida, clique no botão **...**  > **excluir conexão** .
    > [!div class="mx-imgBorder"]
    > ![exclusão de conexão de emparelhamento](../media/setup-exchange-modify-deleteconnection.png)
1. Insira a ID do recurso na caixa **Confirmar exclusão** , conforme mostrado nas caixas realçadas e clique em **excluir**.
    > [!div class="mx-imgBorder"]
    > DeleteConfirm de conexão de emparelhamento ![](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Adicionar sessão IPv4/IPv6 em conexões ativas

1. Clique em uma conexão de emparelhamento que você deseja modificar e, em seguida, clique no botão **...**  > **Editar conexão** .
    > [!div class="mx-imgBorder"]
    > ![a edição de conexão de emparelhamento](../media/setup-exchange-modify-editconnection.png)
1. Adicione as informações de endereço **IPv4** ou **endereço IPv6** e clique em **salvar**.
    > [!div class="mx-imgBorder"]
    > ![modificar a conexão de emparelhamento](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Remover sessão IPv4/IPv6 em conexões ativas

Atualmente, não há suporte para a remoção de uma sessão IPv4/IPv6 de uma conexão existente no Portal. Entre em contato com [o emparelhamento da Microsoft](mailto:peeringexperience@microsoft.com).