---
title: Códigos de erro comuns para Azure Key Vault Microsoft Docs
description: Códigos de erro comuns para Azure Key Vault
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: 9ae13b88d767e43c425ceb86d0be455cebc0e6ac
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462529"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Códigos de erro comuns para Azure Key Vault

Os códigos de erro listados na tabela seguinte podem ser devolvidos por uma operação no cofre da chave Azure

| Código de erro | Mensagem de utilizador |
|--|--|
| VaultAlreadyExists |  A sua tentativa de criar um novo cofre-chave com o nome especificado falhou uma vez que o nome já está em uso. Se apagou recentemente um cofre com este nome, pode ainda estar no estado de apagação suave. Pode verificar se o existiu em estado de soft-deleed [aqui](./key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault) |
| VaultNameNotValid |  O nome do cofre deve ser 24 char, alfanumérico e começar com um alfabeto |
| AcessoDenied |  Podes estar a perder permissões na política de acesso para fazer essa operação. |
| Proibido PorFirewall |  O endereço do cliente não é autorizado e o chamador não é um serviço de confiança. |
| ConflictError |  Está a pedir várias operações no mesmo item.  |
| RegionNotSupporto |  A região azul especificada não é apoiada para este recurso. |
| SkuNotsupported |  O tipo SKU especificado não é suportado para este recurso. |
| RecursosNotFound |  O recurso azul especificado não é encontrado. |
| Grupo de RecursosNotFound | O grupo de recursos azure especificado não é encontrado. |
| CertificadoSExpired |  Verifique a data de validade e o prazo de validade do certificado. |


## <a name="next-steps"></a>Passos seguintes

- Consulte o guia de desenvolvedores do [Azure Key Vault](developers-guide.md)
- Ler mais sobre [autenticação no cofre da Chave](authentication.md)