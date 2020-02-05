---
title: Azure VMware Solutions (AVS) - Endereço IP público
description: Conheça os endereços IP públicos e os seus benefícios em Soluções Azure VMware (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cb9d0e33da4447760ae0be216c1dd9868c498bd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024981"
---
# <a name="avs-public-ip-address-overview"></a>Visão geral do endereço IP público da AVS

Um endereço IP público permite que os recursos da Internet comuniquem os recursos da AVS Private Cloud num endereço IP privado. O endereço IP privado é uma máquina virtual ou um balancer de carga de software no seu VCenter De Nuvem Privada AVS. O endereço IP público permite-lhe expor serviços que estão a funcionar na sua Nuvem Privada AVS à internet.

O endereço IP público é dedicado ao endereço IP privado até que o desalestá. Um endereço IP público só pode ser atribuído a um endereço IP privado.

Um recurso associado a um endereço IP público usa sempre o endereço IP público para acesso à Internet. Por predefinição, apenas o acesso à Internet de saída é permitido num endereço IP público.  O tráfego de entrada no endereço IP público é negado.  Para permitir o tráfego de entrada, crie uma regra de firewall para o endereço IP público para a porta específica.

## <a name="benefits"></a>Vantagens

A utilização de um endereço IP público para comunicar a entrada fornece:

* Prevenção de ataques de negação de serviço distribuídos (DDoS). Esta proteção está automaticamente ativada para o endereço IP público.
* Monitorização de tráfego sempre ligado e mitigação em tempo real de ataques comuns a nível da rede. Estas defesas são as mesmas defesas usadas pelos serviços online da Microsoft.
* Toda a escala da rede global Azure. A rede pode ser usada para distribuir e mitigar o tráfego de ataques em todas as regiões.  

## <a name="next-steps"></a>Passos seguintes

* Saiba como [alocar um endereço IP público](public-ips.md)