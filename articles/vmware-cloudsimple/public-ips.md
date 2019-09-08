---
title: Solução do Azure VMware por CloudSimple-alocar endereços IP públicos
description: Descreve como alocar endereços IP públicos para máquinas virtuais no ambiente de nuvem privada
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773788"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Alocar endereços IP públicos para o ambiente de nuvem privada

Abra a guia IPs públicos na página rede para alocar endereços IP públicos para máquinas virtuais em seu ambiente de nuvem privada.

1. [Acesse o portal do CloudSimple](access-cloudsimple-portal.md) e selecione **rede** no menu lateral.
2. Selecione **IPS públicos**.
3. Clique em **novo IP público**.

    ![Página IPs públicos](media/public-ips-page.png)

4. Insira um nome para identificar a entrada de endereço IP.
5. Mantenha o local padrão.
6. Use o controle deslizante para alterar o tempo limite de ociosidade, se necessário.
7. Insira o endereço IP local para o qual você deseja atribuir um endereço IP público.
8. Insira um nome DNS associado.
9. Clique em **Submit** (Submeter).

![Alocar IPs públicos](media/network-public-ip-allocate.png)

A tarefa de alocar o endereço IP público começa. Você pode verificar o status da tarefa na página **atividades > tarefas** . Quando a alocação for concluída, a nova entrada será mostrada na página IPs públicos.
