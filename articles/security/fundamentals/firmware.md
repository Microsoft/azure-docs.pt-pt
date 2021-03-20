---
title: Firmware security - Azure Security
description: Saiba como a Microsoft protege hardware e firmware da Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 8233cc714d977083f4d55716d35c0b7094a069ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94557960"
---
# <a name="firmware-security"></a>Segurança do firmware
Este artigo descreve como a Microsoft protege o ecossistema de hardware em nuvem e as cadeias de fornecimento.

## <a name="securing-the-cloud-hardware-ecosystem"></a>Segurança do ecossistema de hardware em nuvem
A Microsoft é ativamente parceira dentro do ecossistema de hardware na nuvem para impulsionar melhorias contínuas de segurança através de:

- Colaborando com parceiros de hardware e firmware da Azure (como fabricantes de componentes e integradores de sistemas) para satisfazer os requisitos de hardware e firmware da Azure.

- Permitir aos parceiros realizar uma avaliação contínua e melhorar a postura de segurança dos seus produtos utilizando requisitos definidos pela Microsoft em áreas como:

  - Arranque seguro firmware
  - Recuperação segura do Firmware
  - Atualização segura do Firmware
  - Criptografia de firmware
  - Hardware bloqueado
  - Telemetria granular depurativa
  - Suporte ao sistema para hardware TPM 2.0 para permitir arranque medido

- Envolver e contribuir para o projeto de segurança [open compute Project (OCP)](https://www.opencompute.org/wiki/Security) através do desenvolvimento de especificações. As especificações promovem a consistência e a clareza para uma conceção e arquitetura seguras no ecossistema.

   > [!NOTE]
   > Um exemplo da nossa contribuição para o Projeto de Segurança OCP é a especificação [hardware Secure Boot.](https://docs.google.com/document/d/1Se1Dd-raIZhl_xV3MnECeuu_I0nF-keg4kqXyK4k4Wc/edit#heading=h.5z2d7x9gbhk0)

## <a name="securing-hardware-and-firmware-supply-chains"></a>Segurança das cadeias de fornecimento de hardware e firmware
Os fornecedores e fornecedores de hardware cloud da Azure também são obrigados a aderir aos processos e requisitos de segurança da cadeia de fornecimento desenvolvidos pela Microsoft. Os processos de desenvolvimento e implementação de hardware e firmware são necessários para seguir os processos de Ciclo de Vida de [Desenvolvimento de Segurança](https://www.microsoft.com/securityengineering/sdl) da Microsoft (SDL), tais como:

- Modelação de ameaças
- Avaliações de design seguras
- Avaliações de firmware e testes de penetração
- Ambientes seguros de construção e teste
- Gestão de vulnerabilidades de segurança e resposta a incidentes

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que fazemos para impulsionar a integridade e segurança da plataforma, consulte:

- [Bota segura](secure-boot.md)
- [Atestado de arranque e hospedeiro medido](measured-boot-host-attestation.md)
- [Projeto Cerberus](project-cerberus.md)
- [Encriptação de dados inativos](encryption-atrest.md)
- [Segurança do hipervisor](hypervisor.md)