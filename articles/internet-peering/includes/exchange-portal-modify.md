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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774565"
---
Esta secção descreve como realizar as seguintes operações de modificação para o peering direto:

### <a name="add-exchange-peering-connections"></a>Adicionar ligações de persparo de troca

1. Clique no botão **+ Adicionar ligações** na parte superior e configurar uma nova ligação de pares.
    > [!div class="mx-imgBorder"]
    > ![Vista de recursos de observação de recursos](../media/setup-exchange-modify-addconnection.png)
1. Preencha o formulário de ligação de **peering exchange** e clique em **Guardar**. Para ajudar a configurar uma ligação de pares, reveja os passos em "Criar e fornecer um peering direto" acima.
    > [!div class="mx-imgBorder"]
    > ![Vista de recursos de observação de recursos](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Remover ligações de persparo de troca

1. Clique numa ligação de observação que pretende eliminar e, em seguida, clique no **...**  > Eliminar o botão de **ligação.**
    > [!div class="mx-imgBorder"]
    > ![Excluir a ligação de espreitar](../media/setup-exchange-modify-deleteconnection.png)
1. Introduza o ID de recurso na caixa **Confirmar Excluir,** como mostrado nas caixas realçadas e clique em **Eliminar**.
    > [!div class="mx-imgBorder"]
    > ![Peering Connection DeleteConfirm](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Adicione a sessão IPv4/IPv6 sobre ligações ativas

1. Clique numa ligação de observação que pretende modificar e, em seguida, clique no **...**  > Editar botão de **ligação.**
    > [!div class="mx-imgBorder"]
    > ![Edição de ligação de pares](../media/setup-exchange-modify-editconnection.png)
1. Adicione informações de **endereço IPv4** ou **IPv6** e clique em **Guardar**.
    > [!div class="mx-imgBorder"]
    > ![Modificar de ligação de observação](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Remova a sessão IPv4/IPv6 sobre ligações ativas

A remoção de uma sessão IPv4/IPv6 de uma ligação existente não é suportada atualmente no portal. Contacte [o peering da Microsoft](mailto:peeringexperience@microsoft.com).