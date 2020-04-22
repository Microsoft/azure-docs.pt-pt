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
ms.openlocfilehash: 3894bf046ed4ee3f068e43dbc5bc5b7f2a1002b4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681037"
---
Esta secção descreve como realizar as seguintes operações de modificação para o peering direto.

### <a name="add-direct-peering-connections"></a>Adicionar ligações de peering direto
1. Selecione o botão **+ Adicionar ligações** e configurar uma nova ligação de pares.
    > [!div class="mx-imgBorder"]
    > ![Vista de recursos de observação de recursos](../media/setup-direct-modify-addconnection.png)

1. Preencha o formulário **de ligação de peering direto** e selecione **Guardar**. Para ajudar a configurar uma ligação de pares, reveja os passos na secção "Criar e fornecer um peering direto".
    > [!div class="mx-imgBorder"]
    > ![Formulário de ligação de peering direto](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Remover ligações de peering direto

Remover uma ligação não é suportado no portal Azure. Para mais informações, contacte a [Microsoft a espreitar](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Atualizar ou desvalorizar largura de banda em ligações ativas
1. Selecione uma ligação de pares que pretende modificar e, em seguida, selecione **...**  >  **Editar ligação**.
    > [!div class="mx-imgBorder"]
    > ![Editar ligação](../media/setup-direct-modify-editconnection.png)

1. Modifique a largura de banda movendo o slider e, em seguida, selecione **Guardar**.
    > [!div class="mx-imgBorder"]
    > ![Modificar a largura de banda](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>Adicione informações de sessão IPv4 ou IPv6 sobre ligações ativas
1. Selecione uma ligação de pares que pretende modificar e, em seguida, selecione **...**  >  **Editar a ligação** como mostrado no passo 1.
1. Introduza a **pré-fixação da sessão IPv4** ou **a pré-fixação da sessão IPv6** e selecione **Guardar**.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Remova informações de sessão IPv4 ou IPv6 sobre ligações ativas
Remover a **pré-fixação da sessão IPv4** ou a informação de **prefixo da sessão IPv6** não é suportada no portal. Para mais informações, contacte a [Microsoft a espreitar](mailto:peeringexperience@microsoft.com).
