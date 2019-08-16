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
ms.openlocfilehash: 12a6af73dc0abc9598184f6c83e9d7652973a99e
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69544451"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Alocar endereços IP públicos para o ambiente de nuvem privada

Abra a guia IPs públicos na página rede para alocar endereços IP públicos para máquinas virtuais em seu ambiente de nuvem privada.

1. [Acesse o portal do CloudSimple](monitor-activity.md) e selecione **rede** no menu lateral.
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
