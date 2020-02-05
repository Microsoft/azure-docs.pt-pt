---
title: Azure VMware Solutions (AVS) - Alocar endereços IP públicos
description: Descreve como alocar endereços IP públicos para máquinas virtuais no ambiente AVS Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87133f5efb9f096d3fdb0956aab1caac58b4bd94
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024301"
---
# <a name="allocate-public-ip-addresses-for-avs-private-cloud-environment"></a>Alocar endereços IP públicos para ambiente De Nuvem Privada AVS

Abra o separador IPs público na página da Rede para alocar endereços IP públicos para máquinas virtuais no seu ambiente AVS Private Cloud.

1. [Aceda ao portal AVS](access-cloudsimple-portal.md) e selecione **Rede** no menu lateral.
2. Selecione **IPs públicos**.
3. Clique em **Novo IP Público**.

    ![Página de IPs público](media/public-ips-page.png)

4. Insira um nome para identificar a entrada do endereço IP.
5. Mantenha a localização padrão.
6. Utilize o slider para alterar o tempo de paragem inativo, se necessário.
7. Insira o endereço IP local para o qual pretende atribuir um endereço IP público.
8. Introduza um nome DNS associado.
9. Clique em **Submit** (Submeter).

![Atribuição de IPs públicos](media/network-public-ip-allocate.png)

Inicia-se a tarefa de atribuição do endereço IP público. Pode verificar o estado da tarefa na página **Atividade > Tarefas.** Quando a atribuição estiver concluída, a nova entrada é mostrada na página de IPs públicos.
