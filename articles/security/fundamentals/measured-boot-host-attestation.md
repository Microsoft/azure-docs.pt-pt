---
title: Firmware medido boot e host atestado - Azure Security
description: Visão geral técnica do firmware Azure medido boot e atestado hospedeiro.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f4906d4e0590df047bac4ee15cb0e996a59c3d5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96498868"
---
# <a name="measured-boot-and-host-attestation"></a>Atestado de arranque e hospedeiro medido
Este artigo descreve como a Microsoft garante a integridade e segurança dos anfitriões através de um atestado medido de arranque e anfitrião.

## <a name="measured-boot"></a>Bota medida

O [Módulo plataforma fidedigna](/windows/security/information-protection/tpm/trusted-platform-module-top-node) (TPM) é um componente de auditoria à prova de violação e criptograficamente seguro com firmware fornecido por terceiros de confiança. O registo de configuração de boot contém medições acorrentadas a haxixe registadas nos registos de configuração da plataforma (PCR) quando o anfitrião sofreu pela última vez a sequência de armadilhagem de botas. A seguinte figura mostra este processo de gravação. Incrementalmente adicionando uma medição previamente hashed para o haxixe da próxima medição e executando o algoritmo de hashing na união realiza acorrente de haxixe.

![Diagrama que mostra acorrentação de hash do Serviço de Attestation host.](./media/measured-boot-host-attestation/hash-chaining.png)

O atestado é realizado quando um hospedeiro fornece provas do seu estado de configuração utilizando o seu registo de configuração de arranque (TCGLog). A falsificação de um tronco de arranque é difícil porque o TPM não expõe os seus valores pcr para além das operações de leitura e extensão. Além disso, as credenciais fornecidas pelo Serviço de Attestation host estão seladas a valores específicos do PCR. O uso da corrente de haxixe torna computacionalmente inviável falsificar ou desvendar as credenciais fora da banda.

## <a name="host-attestation-service"></a>Serviço de Attestation anfitrião

O Serviço de Attestation host é uma medida preventiva que verifica se as máquinas hospedeiras são fidedignas antes de poderem interagir com os dados do cliente ou cargas de trabalho. O Serviço atesta anfitrião verifica-se validando uma declaração de conformidade (prova verificável do cumprimento do hospedeiro) enviada por cada anfitrião contra uma política de atestado (definição do estado seguro). A integridade deste sistema é assegurada por uma [raiz de confiança](https://www.uefi.org/sites/default/files/resources/UEFI%20RoT%20white%20paper_Final%208%208%2016%20%28003%29.pdf) fornecida por um TPM.

O Host Attestation Service está presente em cada cluster Azure dentro de um ambiente especializado e fechado. O ambiente fechado inclui outros serviços de gatekeeper que participam no protocolo de máquina de serviços de máquina de anfitrião. Uma infraestrutura de chave pública (PKI) atua como um intermediário para validar a proveniência dos pedidos de atestado e como um emitente de identidade (contingente no atestado de acolhimento bem sucedido). As credenciais pós-atestado emitidas ao hospedeiro certificado estão seladas à sua identidade. Só o anfitrião solicitado pode desvendar as credenciais e alavancar-las para obter permissões incrementais. Isto evita ataques de homem no meio e falsificação.

Se um hospedeiro Azure chegar da fábrica com uma configuração errada de segurança ou for adulterado no datacenter, o seu TCGLog contém indicadores de compromisso sinalizados pelo Serviço de Atestação hoste no próximo atestado, o que provoca uma falha no atestado. Falhas no atestado impedem a frota Azure de confiar no hospedeiro ofensivo. Esta prevenção bloqueia eficazmente todas as comunicações de e para o hospedeiro e desencadeia um fluxo de trabalho de incidentes. A investigação e uma análise detalhada pós-morte são conduzidas para determinar as causas das raízes e quaisquer potenciais indícios de compromisso. Só depois de concluída a análise é que um anfitrião é remediado e tem a oportunidade de se juntar à frota Azure e assumir cargas de trabalho dos clientes.

Segue-se uma arquitetura de alto nível do serviço de atestado de anfitrião:

![Diagrama que mostra a arquitetura do Serviço de Attestation host.](./media/measured-boot-host-attestation/host-attestation-arch.png)

## <a name="attestation-measurements"></a>Medições de atestado

Seguem-se exemplos das muitas medições capturadas hoje.

### <a name="secure-boot-and-secure-boot-keys"></a>Chaves de arranque segura e chave de arranque segura
Ao validar que a base de dados de assinaturas e as assinaturas revogadas digerem as digestão, o Serviço de Attestation host garante que o agente cliente considera o software certo de confiança. Ao validar as assinaturas da base de dados chave de inscrição do público e da chave da plataforma pública, o Serviço de Atestação hoste confirma que apenas as partes fidedignas têm permissão para modificar as definições do software considerado fidedigno. Por último, garantindo que o boot seguro está ativo, o Serviço de Attestation host valida estas definições.

### <a name="debug-controls"></a>Controlos de depurg
Os depurggers são ferramentas poderosas para os desenvolvedores. No entanto, o acesso sem restrições à memória e a outros comandos de depurg poderia enfraquecer a proteção de dados e a integridade do sistema se fosse dado a uma parte não confiável. O Host Attestation Service garante que qualquer tipo de depuração é desativada no arranque das máquinas de produção.

### <a name="code-integrity"></a>Integridade do código
O UEFI [Secure Boot](secure-boot.md) garante que apenas um software de baixo nível confiável pode ser executado durante a sequência de arranque. No entanto, os mesmos controlos também devem ser aplicados no ambiente pós-arranque aos condutores e outros executáveis com acesso ao modo kernel. Para o efeito, é utilizada uma política de integridade do código (CI) para definir quais condutores, binários e outros executáveis são considerados fidedignos especificando assinaturas válidas e inválidas. Estas políticas são aplicadas. Violações da política geram alertas para a equipa de resposta a incidentes de segurança para investigação.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que fazemos para impulsionar a integridade e segurança da plataforma, consulte:

- [Segurança do firmware](firmware.md)
- [Bota segura](secure-boot.md)
- [Projeto Cerberus](project-cerberus.md)
- [Encriptação de dados inativos](encryption-atrest.md)
- [Segurança do hipervisor](hypervisor.md)