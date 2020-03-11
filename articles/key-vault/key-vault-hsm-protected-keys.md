---
title: Como gerar e transferir chaves protegidas por HSM para o Cofre de Chaves Azure - Cofre chave Azure [ Cofre de Chaves Azure ] Microsoft Docs
description: Use este artigo para ajudá-lo a planear, gerar e, em seguida, transferir as suas próprias chaves protegidas por HSM para usar com o Cofre chave Azure. Também conhecido como BYOK ou traga a sua própria chave.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 048e5072c592cf2de32e533014c99034572a1c47
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082902"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importar chaves protegidas por HSM para o Cofre-Chave

Para obter mais garantias, quando utilizar o Cofre chave Azure, pode importar ou gerar chaves em módulos de segurança de hardware (HSMs) que nunca saem do limite HSM. Este cenário é muitas vezes referido como *trazer a sua própria chave*, ou BYOK. O Azure Key Vault utiliza a família nCipher nShield de HSMs (FIPS 140-2 Level 2 validado) para proteger as suas chaves.

Esta funcionalidade não está disponível para o Azure China 21Vianet.

> [!NOTE]
> Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre chave Azure?](key-vault-overview.md)  
> Para começar um tutorial, que inclui a criação de um cofre chave para chaves protegidas por HSM, veja [o que é o Cofre chave Azure?](key-vault-overview.md)

## <a name="supported-hsms"></a>HSMs suportados

A transferência de chaves protegidas por HSM para o Cofre chave é suportada através de dois métodos diferentes dependendo dos HSMs que utiliza. Utilize a tabela abaixo para determinar qual o método que deve ser utilizado para que os seus HSMs gerem e, em seguida, transfira as suas próprias chaves protegidas por HSM para usar com o Cofre de Chaves Azure. 

|Nome do fornecedor|Tipo de fornecedor|Modelos HSM suportados|Método de transferência hsm-chave suportado|
|---|---|---|---|
|nCipher|Fabricante|<ul><li>nShield família de HSMs</li></ul>|[Use o método BYOK legado](hsm-protected-keys-legacy.md)|
|Rio Thales|Fabricante|<ul><li>Família SafeNet Luna HSM 7 com versão 7.3 ou mais recente</li></ul>| [Utilize o novo método BYOK (pré-visualização)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Fortanix|HSM como serviço|<ul><li>Serviço de Gestão de Chaves Auto-Defendedores (SDKMS)</li></ul>|[Utilize o novo método BYOK (pré-visualização)](hsm-protected-keys-vendor-agnostic-byok.md)|










## <a name="next-steps"></a>Passos seguintes

Siga as [Melhores Práticas do Cofre chave](key-vault-best-practices.md) para garantir segurança, durabilidade e monitorização das suas chaves.
