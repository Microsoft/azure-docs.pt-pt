---
title: Solução Azure VMware by CloudSimple - Endereço IP público
description: Conheça os endereços IP públicos e os seus benefícios na Solução VMware Azure by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "77024981"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Visão geral do endereço IP público CloudSimple

Um endereço IP público permite que os recursos da Internet comuniquem os recursos da Private Cloud num endereço IP privado. O endereço IP privado é uma máquina virtual ou um equilibrador de carga de software no seu VCenter Private Cloud. O endereço IP público permite-lhe expor os serviços que correm na sua Nuvem Privada para a internet.

O endereço IP público é dedicado ao endereço IP privado até que não o desatribua. Um endereço IP público só pode ser atribuído a um endereço IP privado.

Um recurso associado a um endereço IP público usa sempre o endereço IP público para acesso à Internet. Por padrão, apenas o acesso à Internet de saída é permitido num endereço IP público.  O tráfego de entrada no endereço IP público é negado.  Para permitir o tráfego de entrada, crie uma regra de firewall para o endereço IP público para a porta específica.

## <a name="benefits"></a>Benefícios

A utilização de um endereço IP público para comunicar a entrada fornece:

* Prevenção de ataques de negação de serviço distribuído (DDoS). Esta proteção é ativada automaticamente para o endereço IP público.
* Monitorização do tráfego sempre ligado e mitigação em tempo real de ataques comuns ao nível da rede. Estas defesas são as mesmas defesas usadas pelos serviços online da Microsoft.
* Toda a escala da rede global Azure. A rede pode ser usada para distribuir e mitigar o tráfego de ataques em todas as regiões.  

## <a name="next-steps"></a>Passos seguintes

* Saiba como [alocar um endereço IP público](public-ips.md)