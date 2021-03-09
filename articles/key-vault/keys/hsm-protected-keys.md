---
title: Como gerar & transferir chaves protegidas pelo HSM - Cofre de Chaves Azure
description: Aprenda a planear, gerar e, em seguida, transfira as suas próprias chaves protegidas pelo HSM para usar com o Azure Key Vault. Também conhecido como BYOK ou traga a sua própria chave.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/24/2021
ms.author: ambapat
ms.openlocfilehash: a7e709ba9a4de5ff77524a2d2b1b64a5933131a2
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489417"
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
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Fabricante,<br/>HSM como serviço|<ul><li>nShield família de HSMs</li><li>nShield como um serviço</ul>|**Método 1:** [nCipher BYOK](hsm-protected-keys-ncipher.md) (precedido)<br/>**Método 2:** [Utilizar o novo método BYOK](hsm-protected-keys-byok.md) (recomendado)|
|Thales|Fabricante|<ul><li>Família Luna HSM 7 com a versão 7.3 ou mais recente</li></ul>| [Use o novo método BYOK](hsm-protected-keys-byok.md)|
|Fortanix|Fabricante,<br/>HSM como serviço|<ul><li>Self-Defending Serviço de Gestão de Chaves (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Use o novo método BYOK](hsm-protected-keys-byok.md)|
|Rio Marvell|Fabricante|Todos os HSMs de LiquidSecurity com<ul><li>Versão firmware 2.0.4 ou posterior</li><li>Versão firmware 3.2 ou mais recente</li></ul>|[Use o novo método BYOK](hsm-protected-keys-byok.md)|
|Criptomático|ISV (Sistema de Gestão de Chaves empresariais)|Múltiplas marcas e modelos HSM, incluindo<ul><li>nCipher</li><li>Thales</li><li>Rio Utimaco</li></ul>Consulte [o site Cryptomathic para mais detalhes](https://www.cryptomathic.com/azurebyok)|[Use o novo método BYOK](hsm-protected-keys-byok.md)|
|Securosys SA|Fabricante,<br/>HSM como serviço|Família Primus HSM, Securosys Clouds HSM|[Use o novo método BYOK](hsm-protected-keys-byok.md)|
|StorMagic|ISV (Sistema de Gestão de Chaves empresariais)|Múltiplas marcas e modelos HSM, incluindo<ul><li>Rio Utimaco</li><li>Thales</li><li>nCipher</li></ul>Consulte [o site StorMagic para mais detalhes](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[Use o novo método BYOK](hsm-protected-keys-byok.md)|
|IBM|Fabricante|IBM 476x, CryptoExpress|[Use o novo método BYOK](hsm-protected-keys-byok.md)|
|Rio Utimaco|Fabricante,<br/>HSM como serviço|u.trust Anchor, CryptoServer|[Use o novo método BYOK](hsm-protected-keys-byok.md)|
|||||

## <a name="next-steps"></a>Passos seguintes

* Reveja a [visão geral](../general/security-overview.md) de segurança do Cofre chave para garantir segurança, durabilidade e monitorização das suas chaves.
* Consulte a [especificação BYOK](./byok-specification.md) para uma descrição completa do novo método BYOK