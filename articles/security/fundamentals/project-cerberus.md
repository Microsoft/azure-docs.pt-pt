---
title: Integridade do firmware - Azure Security
description: Saiba mais sobre medições criptográficas para garantir a integridade do firmware.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f085858a9d550623704efd4f051ed525e55a37e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94557853"
---
# <a name="project-cerberus"></a>Projeto Cerberus

Cerberus é um hardware compatível com o NIST 800-193 com uma identidade que não pode ser clonada. Cerberus é projetado para aumentar ainda mais a postura de segurança da infraestrutura Azure, fornecendo uma forte âncora de confiança para a integridade do firmware.

## <a name="enabling-an-anchor-of-trust"></a>Permitir uma âncora de confiança
Cada chip Cerberus tem uma identidade criptográfica única que é estabelecida usando uma cadeia de certificados assinada enraizada numa autoridade de certificados da Microsoft (CA). As medições obtidas a partir de Cerberus podem ser utilizadas para validar a integridade de componentes tais como:

- Anfitrião
- Controlador BMC
- Todos os periféricos, incluindo o cartão de interface de rede e [o sistema-on-a-chip](https://en.wikipedia.org/wiki/System_on_a_chip) (SoC)

Esta âncora de confiança ajuda a defender o firmware da plataforma a partir de:

- Binários de firmware comprometidos em execução na plataforma
- Malware e hackers que exploram bugs no sistema operativo, aplicação ou hipervisor
- Certos tipos de ataques à cadeia de abastecimento (fabrico, montagem, trânsito)
- Insiders maliciosos com privilégios administrativos ou acesso a hardware

## <a name="cerberus-attestation"></a>Atestado cerberus
A Cerberus autentica a integridade do firmware para componentes do servidor utilizando um Manifesto de Firmware de Plataforma (PFM). O PFM define uma lista de versões de firmware autorizadas e fornece uma medição da plataforma ao [Serviço de Attestation host](measured-boot-host-attestation.md)Azure Host . O Serviço de Attestation host valida as medições e faz uma determinação de permitir apenas que os anfitriões de confiança se juntem à frota Azure e acolhem cargas de trabalho dos clientes.

Em conjunto com o Serviço de Attestation Host, as capacidades da Cerberus aumentam e promovem uma infraestrutura de produção altamente segura da Azure.

> [!NOTE]
> Para saber mais, consulte a informação do [Projeto Cerberus](https://github.com/opencomputeproject/Project_Olympus/tree/master/Project_Cerberus) no GitHub.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que fazemos para impulsionar a integridade e segurança da plataforma, consulte:

- [Segurança do firmware](firmware.md)
- [Bota segura](secure-boot.md)
- [Atestado de arranque e hospedeiro medido](measured-boot-host-attestation.md)
- [Encriptação de dados inativos](encryption-atrest.md)
- [Segurança do hipervisor](hypervisor.md)