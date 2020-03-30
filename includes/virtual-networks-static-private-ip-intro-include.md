---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 3d0c475cebedf28b41f16fbde312a1aac14ef207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060589"
---
Uma máquina virtual (VM) é automaticamente atribuída a um endereço IP privado a partir de uma gama que especifica, com base na subnet na qual o VM é implantado. O VM mantém o endereço até que o VM seja eliminado. O Azure atribui dinamicamente o próximo endereço IP privado disponível a partir da subnet onde cria um VM. Se pretender um endereço IP específico da subnet atribuído ao VM, atribua um endereço IP estático.
