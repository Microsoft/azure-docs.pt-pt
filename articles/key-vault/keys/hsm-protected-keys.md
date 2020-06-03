---
title: Como gerar e transferir chaves protegidas pelo HSM para o Cofre da Chave Azure - Cofre da Chave Azure Microsoft Docs
description: Use este artigo para ajudá-lo a planear, gerar e, em seguida, transferir as suas próprias chaves protegidas pelo HSM para usar com o Cofre da Chave Azure. Também conhecido como BYOK ou traga a sua própria chave.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 011ef67e5dbbf5b391e8bdaad20a42688022a0a9
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84296770"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importar chaves protegidas por HSM para o Key Vault

Para obter uma garantia adicional, quando utilizar o Azure Key Vault, pode importar ou gerar chaves em módulos de segurança de hardware (HSMs) que nunca saem do limite HSM. Este cenário costuma chamar-se *Bring Your Own Key* ou BYOK. O Azure Key Vault utiliza nCipher nShield family of HSMs (FIPS 140-2 Nível 2 validado) para proteger as suas chaves.

Esta funcionalidade não se encontra disponível para o Azure China 21Vianet.

> [!NOTE]
> Para mais informações sobre o Azure Key Vault, veja [o que é o Cofre da Chave Azure?](../general/overview.md)  
> Para um tutorial de iniciação, que inclui a criação de um cofre chave para chaves protegidas pelo HSM, veja [o que é a Azure Key Vault?](../general/overview.md)

## <a name="supported-hsms"></a>HSMs apoiados

A transferência de chaves protegidas pelo HSM para o Key Vault é suportada através de dois métodos diferentes, dependendo dos HSMs que utiliza. Utilize a tabela abaixo para determinar que método deve ser utilizado para os seus HSMs gerar e, em seguida, transfira as suas próprias chaves protegidas pelo HSM para usar com o Cofre da Chave Azure. 

|Nome do fornecedor|Tipo de Fornecedor|Modelos HSM suportados|Método de transferência de chaves HSM suportado|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Fabricante,<br/>HSM como serviço|<ul><li>nShield família de HSMs</li><li>nShield como um serviço</ul>|**Método 1:** [nCipher BYOK](hsm-protected-keys-ncipher.md) (com forte atestado para importação de chaves e validação de HSM)<br/>**Método 2:** [Utilizar o novo método BYOK](hsm-protected-keys-byok.md) |
|Thales|Fabricante|<ul><li>Família SafeNet Luna HSM 7 com a versão 7.3 ou mais recente do firmware</li></ul>| [Use o novo método BYOK](hsm-protected-keys-byok.md)|
|Fortanix|Fabricante,<br/>HSM como serviço|<ul><li>Serviço de Gestão de Chaves auto-defendido (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Use o novo método BYOK](hsm-protected-keys-byok.md)|
|Rio Marvell|Fabricante|Todos os HSMs de LiquidSecurity com<ul><li>Versão firmware 2.0.4 ou posterior</li><li>Versão firmware 3.2 ou mais recente</li></ul>|[Use o novo método BYOK](hsm-protected-keys-byok.md)|
|Criptomático|ISV (Sistema de Gestão de Chaves empresariais)|Múltiplas marcas e modelos HSM, incluindo<ul><li>nCipher</li><li>Thales</li><li>Rio Utimaco</li></ul>Consulte [o site Cryptomathic para mais detalhes](https://www.cryptomathic.com/azurebyok)|[Use o novo método BYOK](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>Passos seguintes

* Siga [as melhores práticas do Key Vault](../general/best-practices.md) para garantir segurança, durabilidade e monitorização das suas chaves.
* Consulte a [especificação BYOK](https://docs.microsoft.com/azure/key-vault/keys/byok-specification) para uma descrição completa do novo método BYOK
