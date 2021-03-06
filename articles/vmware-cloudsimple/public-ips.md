---
title: Solução Azure VMware by CloudSimple - Aloque endereços IP públicos
description: Descreve como alocar endereços IP públicos para máquinas virtuais no ambiente Private Cloud
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bb46ad726cd3b99324e9bb96b998ed1b4da885de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899189"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Alocar endereços IP públicos para ambiente Private Cloud

Abra o separador IPs Público na página 'Rede' para alocar endereços IP públicos para máquinas virtuais no seu ambiente Private Cloud.

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md) e selecione **Rede** no menu lateral.
2. Selecione **IPs públicos**.
3. Clique **em Novo IP Público**.

    ![Página de IPs públicos](media/public-ips-page.png)

4. Insira um nome para identificar a entrada do endereço IP.
5. Mantenha a localização padrão.
6. Utilize o deslizador para alterar o tempo limite de marcha lenta, se necessário.
7. Insira o endereço IP local para o qual pretende atribuir um endereço IP público.
8. Introduza um nome DNS associado.
9. Clique **em Submeter.**

![Alocar iPs públicos](media/network-public-ip-allocate.png)

Começa a tarefa de atribuir o endereço IP público. Pode verificar o estado da tarefa na página **'Tarefas >** Atividades'. Quando a atribuição estiver concluída, a nova entrada é mostrada na página de IPs público.
