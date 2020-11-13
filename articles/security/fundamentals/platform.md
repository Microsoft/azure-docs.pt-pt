---
title: Integridade e segurança da plataforma Azure - Azure Security
description: Visão geral técnica da integridade e segurança da plataforma Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 4755bc19a645d196487f0b8e0f4d1ef2120723ca
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557984"
---
# <a name="platform-integrity-and-security-overview"></a>Visão geral da integridade da plataforma e segurança
A frota Azure é composta por milhões de servidores (anfitriões) com milhares de outros adicionados diariamente. Milhares de anfitriões também são submetidos a manutenção diariamente através de reboots, atualizações do sistema operativo ou reparações. Antes que um anfitrião possa entrar na frota e começar a aceitar cargas de trabalho dos clientes, a Microsoft verifica que o anfitrião está num estado seguro e de confiança. Esta verificação garante que não ocorreram alterações maliciosas ou inadvertidas nos componentes da sequência de arranque durante a cadeia de fornecimento ou os fluxos de trabalho de manutenção.

## <a name="securing-azure-hardware-and-firmware"></a>Segurança de hardware e firmware da Azure
Esta série de artigos descreve como a Microsoft garante a integridade e segurança dos anfitriões através de várias fases do seu ciclo de vida, desde o fabrico ao pôr-do-sol. O endereço dos artigos:
 
- [Segurança firmware](firmware.md)
- [Bota Segura UEFI](secure-boot.md)
- [Atestado de arranque e hospedeiro medido](measured-boot-host-attestation.md)
- [Projeto Cerberus](project-cerberus.md)
- [Encriptação inativa](encryption-atrest.md)
- [Segurança hipervisor](hypervisor.md)
 
## <a name="next-steps"></a>Passos seguintes

- Saiba como a Microsoft se associa ativamente dentro do ecossistema de hardware na nuvem para impulsionar [melhorias contínuas de segurança do firmware.](firmware.md)

- Compreenda a sua [responsabilidade partilhada na nuvem.](shared-responsibility.md)