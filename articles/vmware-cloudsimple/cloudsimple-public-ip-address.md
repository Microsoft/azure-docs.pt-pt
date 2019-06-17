---
title: Solução de VMware ao CloudSimple - endereço IP público do Azure
description: Saiba mais sobre endereços IP públicos e seus benefícios na solução de VMware ao CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f57b7397f4a2d288cd2b8b55cf23b2d635aa5f8c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209554"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Descrição de geral do CloudSimple pública IP endereço

Um endereço IP público permite recursos de internet comunicar entrada, recursos de nuvem privada num endereço IP privado. O endereço IP privado é uma máquina virtual ou um balanceador de carga de software. O endereço IP privado é no seu vCenter de nuvem privada. O endereço IP público permite-lhe expor serviços em execução na sua nuvem privada à internet.

O endereço IP público dedicado para o endereço IP privado até que a atribuição. Um endereço IP público só pode ser atribuído a um endereço IP privado.

Um recurso associado um endereço IP público sempre utiliza o endereço IP público para acesso à internet. Por predefinição, apenas acesso de internet de saída é permitido num endereço IP público.  O tráfego de entrada no endereço IP público é negado.  Para permitir tráfego de entrada, crie uma regra de firewall para o endereço IP público para a porta específica.

## <a name="benefits"></a>Benefícios

A utilização de um endereço IP público para comunicar entrada fornece:

* Ataques denial of prevenção de ataques do serviço (DDoS). Esta proteção é ativada automaticamente para o endereço IP público.
* Atenuação em tempo real e monitorização tráfego sempre ativa de ataques comuns de nível de rede. Essas defesas estão as defesas mesmo utilizadas pelo Microsoft online services.
* O dimensionamento da rede global do Azure. A rede pode ser utilizada para distribuir e reduzir o tráfego de ataque em várias regiões.  

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [alocar um endereço IP público](https://docs.azure.cloudsimple.com/public-ips/)
