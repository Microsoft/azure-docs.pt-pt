---
title: Solução Azure VMware by CloudSimple - Alocar endereços IP públicos
description: Descreve como alocar endereços IP públicos para máquinas virtuais no ambiente private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024301"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Alocar endereços IP públicos para ambiente cloud privado

Abra o separador IPs público na página da Rede para alocar endereços IP públicos para máquinas virtuais no seu ambiente Cloud Privado.

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md) e selecione **Rede** no menu lateral.
2. Selecione **IPs públicos**.
3. Clique em **Novo IP Público**.

    ![Página de IPs público](media/public-ips-page.png)

4. Insira um nome para identificar a entrada do endereço IP.
5. Mantenha a localização padrão.
6. Utilize o slider para alterar o tempo de paragem inativo, se necessário.
7. Insira o endereço IP local para o qual pretende atribuir um endereço IP público.
8. Introduza um nome DNS associado.
9. Clique em **Submeter**.

![Atribuição de IPs públicos](media/network-public-ip-allocate.png)

Inicia-se a tarefa de atribuição do endereço IP público. Pode verificar o estado da tarefa na página **Tarefas > Atividade.** Quando a atribuição estiver concluída, a nova entrada é mostrada na página de IPs públicos.
