---
title: Sobre o domínio de segurança do HSM gerido pela Azure
description: Visão geral do domínio de segurança gerido do HSM, um conjunto de credenciais fundamentais necessárias para recuperar um HSM gerido
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: amitbapat
ms.author: ambapat
ms.date: 09/15/2020
ms.openlocfilehash: 37e2541d0b53c96fd3f85da31b2c0ce5b68b551a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90997212"
---
# <a name="about-the-managed-hsm-security-domain"></a>Sobre o domínio de segurança gerido do HSM

O Domínio de Segurança gerido do HSM (SD) é um conjunto de credenciais fundamentais necessárias para recuperar um HSM gerido se houver um desastre. O Domínio de Segurança é gerado no hardware gerido do HSM e nos enclaves de software de serviço e representa a "propriedade" do HSM.

Todos os HSM geridos devem ter um domínio de segurança para operar. Quando solicita um novo HSM gerido, é provisionado mas não é ativado até descarregar o Domínio de Segurança. Quando um HSM gerido está no estado provisitado, mas não ativado, existem duas maneiras de o ativar:
- Descarregar o seu Domínio de Segurança é o método padrão, e permite-lhe armazenar com segurança o Domínio de Segurança para utilizar com outro HSM gerido ou para se recuperar de um desastre total.
- Faça o upload de um domínio de segurança existente que já tenha, o que lhe permite criar várias instâncias geridas de HSM que partilham o mesmo Domínio de Segurança.

## <a name="download-your-security-domain"></a>Descarregue o seu domínio de segurança

Quando um HSM Gerido é a provisionado mas não ativado, o download do Domínio de Segurança captura as credenciais fundamentais necessárias para recuperar de uma perda completa de todo o hardware. Para descarregar o Domínio de Segurança, tem de criar pelo menos 3 (máximo 10) pares de chaves RSA e enviar estas chaves públicas para o serviço quando solicitar o download do Domínio de Segurança. Também precisa de especificar o número mínimo de chaves necessárias (quórum) para desencriptar o Domínio de Segurança no futuro. O HSM gerido rubricará o Domínio de Segurança e criptografá-lo-á com as chaves públicas que fornece utilizando o [algoritmo de partilha secreta de Shamir.](https://dl.acm.org/doi/10.1145/359168.359176) A bolha do domínio de segurança que descarrega só pode ser desencriptada quando pelo menos um quórum de chaves privadas estiver disponível; deve manter as chaves privadas seguras para garantir a segurança do Domínio de Segurança. Uma vez que o download esteja concluído, o HSM Gerido estará em estado ativado pronto a ser utilizado.  

> [!IMPORTANT]
> Para uma recuperação completa de desastres, você deve ter o domínio de segurança, e o quórum de chaves privadas que foram usados para protegê-lo, e um backup completo do HSM. Se perder o Domínio de Segurança ou o suficiente das teclas RSA (chave privada) para perder quórum, e não houver casos de funcionamento do HSM gerido, a recuperação de desastres não será possível.

## <a name="upload-a-security-domain"></a>Faça upload de um domínio de segurança

Quando um HSM gerido é a provisionado mas não ativado, pode iniciar um processo de recuperação do Domínio de Segurança. O HSM gerido gerará um par de chaves RSA e devolverá a chave pública. Em seguida, pode fazer o upload do Domínio de Segurança para o HSM Gerido. Antes de carregar, o cliente (Azure CLI ou PowerShell) terá de ser fornecido com o número mínimo de chaves privadas que utilizou durante o download do domínio de segurança. O cliente desencriptará o Domínio de Segurança usando este quórum e reencrimará-o utilizando a chave pública que descarregou quando solicitou a recuperação. Uma vez concluído o upload, o HSM gerido estará em estado ativado.

## <a name="backup-and-restore-behavior"></a>Backup e restaurar comportamento

As cópias de segurança (ou a cópia de segurança completa ou uma única cópia de segurança da chave) só podem ser restauradas com sucesso se a fonte Managed HSM (onde a cópia de segurança foi criada) e o destino Managed HSM (onde a cópia de segurança será restaurada) partilharem o mesmo Domínio de Segurança. Desta forma, um Domínio de Segurança também define um limite criptográfico para cada HSM gerido.

## <a name="next-steps"></a>Passos seguintes

- Leia uma [visão geral do HSM gerido](overview.md)
- Saiba mais [sobre gestão gerida do HSM com O Azure CLI](key-management.md)
- Rever [as melhores práticas do HSM gerido](best-practices.md)
