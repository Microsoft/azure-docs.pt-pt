---
title: Solução VMware por CloudSimple – endereço IP público do Azure
description: Saiba mais sobre endereços IP públicos e seus benefícios na solução VMware por CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 29d2bdb75a7f4409e6b3ffee2608cfe7adde6b1f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812532"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Visão geral do endereço IP público do CloudSimple

Um endereço IP público permite que os recursos da Internet comuniquem-se a recursos de nuvem privada em um endereço IP privado. O endereço IP privado é uma máquina virtual ou um balanceador de carga de software. O endereço IP privado está em seu vCenter de nuvem privada. O endereço IP público permite que você exponha serviços em execução na sua nuvem privada para a Internet.

O endereço IP público é dedicado ao endereço IP privado até que você o cancele. Um endereço IP público só pode ser atribuído a um endereço IP privado.

Um recurso associado a um endereço IP público sempre usa o endereço IP público para acesso à Internet. Por padrão, somente o acesso à Internet de saída é permitido em um endereço IP público.  O tráfego de entrada no endereço IP público foi negado.  Para permitir o tráfego de entrada, crie uma regra de firewall para o endereço IP público para a porta específica.

## <a name="benefits"></a>Benefícios

O uso de um endereço IP público para comunicar a entrada fornece:

* Prevenção de ataque de DDoS (negação de serviço distribuído). Essa proteção é habilitada automaticamente para o endereço IP público.
* Monitoramento de tráfego sempre ativa e mitigação em tempo real de ataques comuns em nível de rede. Essas defesas são as mesmas usadas pelo Microsoft serviços online.
* Toda a escala da rede global do Azure. A rede pode ser usada para distribuir e atenuar o tráfego de ataque entre regiões.  

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [alocar um endereço IP público](https://docs.azure.cloudsimple.com/public-ips/)
