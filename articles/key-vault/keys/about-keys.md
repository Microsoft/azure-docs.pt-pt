---
title: Sobre chaves - Cofre da Chave Azure
description: Visão geral da interface Azure Key Vault REST e detalhes do desenvolvedor para chaves.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 2ae7b28d5e9e7a520ee8cbd090b6681d5ad7015a
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422761"
---
# <a name="about-keys"></a>Acerca de chaves

O Azure Key Vault fornece dois tipos de recursos para armazenar e gerir chaves criptográficas:

|Tipo de recurso|Principais métodos de proteção|URL de base de ponto final de avião de dados|
|--|--|--|
| **Cofres** | Protegido por software<br/><br/>e<br/><br/>Protegido por HSM (com Premium SKU)</li></ul> | https://{vault-name}.vault.azure.net |
| **Piscinas HSM geridas** | Protegido pelo HSM | https://{hsm-name}.managedhsm.azure.net |
||||

- **Cofres** - Os cofres fornecem uma solução de gestão de baixo custo, fácil de implantar, multi-inquilino, resiliente à zona (quando disponível), solução de gestão de chaves altamente disponível adequada para cenários de aplicação em nuvem mais comuns.
- **HSMs geridos** - O HSM gerido fornece um único inquilino, resiliente de zona (quando disponível), HSMs altamente disponíveis para armazenar e gerir as suas chaves criptográficas. Mais adequado para aplicações e cenários de utilização que manuseiem chaves de alto valor. Também ajuda a cumprir os mais rigorosos requisitos de segurança, conformidade e regulamentação. 

> [!NOTE]
> Os cofres também permitem armazenar e gerir vários tipos de objetos como segredos, certificados e chaves de conta de armazenamento, além de chaves criptográficas.

As teclas criptográficas no Cofre de Chaves são representadas como objetos JSON Web Key [JWK]. As especificações javaScript object (JSON) e JavaScript Object Signing and Encryption (JOSE) são:

-   [Tecla web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Encriptação web JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algoritmos Web JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Assinatura Web JSON (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

As especificações base JWK/JWA também são alargadas para permitir tipos-chave exclusivos das implementações do Cofre de Chaves Azure e do HSM gerido. 

As teclas protegidas pelo HSM (também designadas por teclas HSM) são processadas num HSM (Módulo de Segurança de Hardware) e permanecem sempre no limite de proteção HSM. 

- Os cofres utilizam HSMs validados **FIPS 140-2 para** proteger as chaves HSM na infraestrutura de backend HSM partilhada. 
- As piscinas HSM geridas utilizam módulos HSM validados **FIPS 140-2 Nível 3** para proteger as suas chaves. Cada piscina HSM é uma instância isolada de inquilino único com o seu próprio domínio de [segurança](../managed-hsm/security-domain.md) proporcionando um completo isolamento criptográfico de todas as outras piscinas HSM que partilham a mesma infraestrutura de hardware.

Estas chaves estão protegidas em piscinas HSM de um único inquilino. Pode importar uma chave RSA, CE e simétrica, em forma macia ou exportando de um dispositivo HSM suportado. Também pode gerar chaves em piscinas HSM. Quando importa as chaves HSM utilizando o método descrito na [especificação BYOK (traga](../keys/byok-specification.md)a sua própria chave), permite material chave de transporte seguro em piscinas geridas de HSM. 

Para obter mais informações sobre as fronteiras geográficas, consulte [o Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-and-protection-methods"></a>Tipos-chave e métodos de proteção

O Cofre-Chave suporta rSA, CE e chaves simétricas. 

### <a name="hsm-protected-keys"></a>Chaves protegidas por HSM

|Tipo de chave|Cofres (apenas Premium SKU)|Piscinas HSM geridas|
|--|--|--|--|
**EC-HSM** : Chave da curva elíptica|FIPS 140-2 Nível 2 HSM|FIPS 140-2 Nível 3 HSM
**RSA-HSM** : Chave RSA|FIPS 140-2 Nível 2 HSM|FIPS 140-2 Nível 3 HSM
**oct-HSM** : Simétrico|Não suportado|FIPS 140-2 Nível 3 HSM
||||

### <a name="software-protected-keys"></a>Chaves protegidas por software

|Tipo de chave|Cofres|Piscinas HSM geridas|
|--|--|--|--|
**RSA** : Chave RSA "protegida por software"|FIPS 140-2 Nível 1|Não suportado
**CE** : Chave da curva elíptica "protegida por software"|FIPS 140-2 Nível 1|Não suportado
||||

Consulte [tipos chave, algoritmos e operações](about-keys-details.md) para detalhes sobre cada tipo de chave, algoritmos, operações, atributos e tags.

## <a name="next-steps"></a>Passos seguintes
- [Sobre o Key Vault](../general/overview.md)
- [Acerca do HSM Gerido](../managed-hsm/overview.md)
- [Acerca de segredos](../secrets/about-secrets.md)
- [Acerca de certificados](../certificates/about-certificates.md)
- [Visão geral da API do Key Vault REST](../general/about-keys-secrets-certificates.md)
- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Key Vault](../general/developers-guide.md)