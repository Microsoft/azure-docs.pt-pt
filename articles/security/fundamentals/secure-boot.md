---
title: Firmware secure boot - Azure Security
description: Visão geral técnica do firmware Azure firmware de arranque seguro.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: feb28b1d448d0146046ed789d1389a3a42f344de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94557848"
---
# <a name="secure-boot"></a>Arranque Seguro

O Secure Boot é uma característica da [Interface de Firmware Extensível Unificado](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) (UEFI) que exige que todos os componentes de firmware e software de baixo nível sejam verificados antes do carregamento. Durante o arranque, a UEFI Secure Boot verifica a assinatura de cada peça de software de arranque, incluindo os controladores de firmware UEFI (também conhecidos como ROMs de opção), aplicações extensíveis de Interface de Firmware (EFI) e controladores e binários do sistema operativo. Se as assinaturas forem válidas ou fidedignas pelo Fabricante de Equipamentos Originais (OEM), as botas da máquina e o firmware dão controlo ao sistema operativo.

## <a name="components-and-process"></a>Componentes e processo

O Secure Boot baseia-se nestes componentes críticos:

- Chave da plataforma (PK) - Estabelece confiança entre o proprietário da plataforma (Microsoft) e o firmware. A metade pública é PKpub e a metade privada é PKpriv.
- Base de dados chave de inscrição (KEK) - Estabelece confiança entre o SISTEMA e o firmware da plataforma. A metade pública é KEKpub e a metade privada é KEKpriv.
- Base de dados de assinaturas (db) - Detém as digestões para signatários fidedignos (chaves e certificados públicos) dos módulos de código de firmware e software autorizados a interagir com o firmware da plataforma.
- Base de dados de assinaturas revogadas (dbx) – Contém digestões revogadas de módulos de código que tenham sido identificados como maliciosos, vulneráveis, comprometidos ou não fidedidos. Se um haxixe estiver na assinatura db e as assinaturas revogadas db, a base de dados de assinaturas revogadas tem precedentes.

A seguinte figura e processo explica como estes componentes são atualizados:

![Diagrama que mostra componentes Secure Boot.](./media/secure-boot/secure-boot.png)

O OEM armazena o Secure Boot digerir na RAM nãovolárceu da máquina (NV-RAM) no momento do fabrico.

1. A base de dados de assinaturas (db) é preenchida com os signatários ou hashes de imagem de aplicações UEFI, carregadores de sistema operativo (como o Microsoft Operating System Loader ou Boot Manager) e os controladores UEFI que são confiáveis.
2. A base de dados de assinaturas revogadas (dbx) é povoada com digestão de módulos que já não são confiáveis.
3. A base de dados chave de inscrição (KEK) é preenchida com chaves de assinatura que podem ser usadas para atualizar a base de dados de assinaturas e a base de dados de assinaturas revogadas. As bases de dados podem ser editadas através de atualizações assinadas com a chave correta ou através de atualizações por um utilizador autorizado fisicamente presente usando menus de firmware.
4. Depois de as bases de dados db, dbx e KEK terem sido adicionadas e a validação e teste final do firmware estar concluída, o OEM bloqueia o firmware da edição e gera uma chave de plataforma (PK). O PK pode ser usado para assinar atualizações para o KEK ou para desligar o Secure Boot.

Durante cada fase do processo de arranque, as digestão do firmware, bootloader, sistema operativo, controladores de núcleo e outros artefactos da cadeia de arranque são calculadas e comparadas com valores aceitáveis. Firmware e software que são descobertos como falsos não estão autorizados a carregar. Assim, a injeção de malware de baixo nível ou os ataques de malware pré-arranque podem ser bloqueados.

## <a name="secure-boot-on-the-azure-fleet"></a>Arranque Seguro na frota Azure
Hoje em dia, todas as máquinas que estão a bordo e implantadas na frota de computação Azure para acolher cargas de trabalho dos clientes provêm de pisos de fábrica com Secure Boot habilitado. As ferramentas e processos direcionados estão em vigor em todas as fases do pipeline de construção e integração de hardware para garantir que o enabler do Secure Boot não seja revertido por acidente ou por intenção maliciosa.

A validação de que as digestão db e dbx estão corretas garante:

- Bootloader está presente em uma das entradas db
- A assinatura do Bootloader é válida
- Botas de anfitrião com software de confiança

 Ao validar as assinaturas de KEKpub e PKpub, podemos confirmar que apenas as partes fidedignas têm permissão para modificar as definições do software considerado confiável. Por último, garantindo que o arranque seguro está ativo, podemos validar que estas definições estão a ser aplicadas.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que fazemos para impulsionar a integridade e segurança da plataforma, consulte:

- [Segurança do firmware](firmware.md)
- [Atestado de arranque e hospedeiro medido](measured-boot-host-attestation.md)
- [Projeto Cerberus](project-cerberus.md)
- [Encriptação de dados inativos](encryption-atrest.md)
- [Segurança do hipervisor](hypervisor.md)