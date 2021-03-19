---
title: Boas práticas usando Azure Key Vault Gerido HSM
description: Este documento explica algumas das melhores práticas para usar o Cofre-Chave
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 7a30a7ab6689b602bc9ad4f696a6fe54c80f2151
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90996852"
---
# <a name="best-practices-when-using-managed-hsm"></a>Melhores práticas ao utilizar o HSM Gerido

## <a name="control-access-to-your-managed-hsm"></a>Control Access ao seu HSM gerido

O HSM gerido é um serviço de nuvem que protege as chaves de encriptação. Como estas chaves são sensíveis e críticas ao negócio, certifique-se de garantir o acesso aos seus HSMs geridos, permitindo apenas aplicações e utilizadores autorizados. Este [artigo](access-control.md) fornece uma visão geral do modelo de acesso. Explica a autenticação e autorização e o controlo de acesso baseado em funções.
- Criar um [Grupo de Segurança do Diretório Ativo Azure](../../active-directory/fundamentals/active-directory-manage-groups.md) para os Administradores do HSM (em vez de atribuir funções de Administrador a indivíduos). Isto evitará o "bloqueio administrativo" em caso de supressão individual de contas.
- Bloqueie o acesso aos seus grupos de gestão, subscrições, grupos de recursos e HSMs geridos - Use a Azure RBAC para controlar o acesso aos seus grupos de gestão, subscrições e grupos de recursos
- Criar por atribuições de funções-chave utilizando [o CSC local gerido do HSM](access-control.md#data-plane-and-managed-hsm-local-rbac)
- Use o principal de acesso de menor privilégio para atribuir funções

## <a name="choose-regions-that-support-availability-zones"></a>Escolha regiões que suportem zonas de disponibilidade

- Para garantir a melhor disponibilidade e resiliência de zona, escolha as regiões de Azure onde as [Zonas de Disponibilidade](../../availability-zones/az-overview.md) são suportadas. Estas regiões aparecem como "regiões recomendadas" no portal Azure.

## <a name="backup"></a>Backup

- Certifique-se de que faz cópias de segurança regulares do seu HSM. As cópias de segurança podem ser feitas ao nível do HSM e para as teclas específicas. 

## <a name="turn-on-logging"></a>Ligue o registo

- [Ligue o registo para](logging.md) o seu HSM. Também crie alertas.

## <a name="turn-on-recovery-options"></a>Ligue as opções de recuperação

- [Soft Delete](../general/soft-delete-overview.md) está ligado por padrão.
- Ligue a proteção contra a purga se quiser proteger-se contra a eliminação forçada do HSM mesmo depois de ter sido ligada a eliminação suave.

## <a name="next-steps"></a>Passos seguintes

- Consulte [cópia de segurança/restauro completo](backup-restore.md) para obter informações sobre a cópia de segurança/restauro completo do HSM.
- Consulte [a sessão de registo de HSM gerida](logging.md) para aprender a usar o Azure Monitor para configurar a exploração madeireira
- Consulte [as chaves geridas pelo HSM geridas](key-management.md) para a gestão das chaves.
- Consulte [gestão de funções geridas do HSM](role-management.md) para gerir atribuições de funções.
