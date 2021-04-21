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
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: b9565095a40052a940d7a7b31f0fd3a27e0e75c2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815005"
---
# <a name="about-keys"></a>Acerca de chaves

O Azure Key Vault fornece dois tipos de recursos para armazenar e gerir chaves criptográficas. Os cofres suportam chaves protegidas por software e protegidas por HSM (Módulo de Segurança de Hardware). Os HSMs geridos suportam apenas chaves protegidas por HSM. 

|Tipo de recurso|Principais métodos de proteção|URL de base de ponto final de avião de dados|
|--|--|--|
| **Cofres** | Protegido por software<br/><br/>e<br/><br/>Protegido por HSM (com Premium SKU)</li></ul> | https://{vault-name}.vault.azure.net |
| **HSMs geridos** | Protegido pelo HSM | https://{hsm-name}.managedhsm.azure.net |
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
- O HSM gerido utiliza módulos HSM validados **FIPS 140-2 Nível 3** para proteger as suas chaves. Cada piscina HSM é uma instância isolada de inquilino único com o seu próprio domínio de [segurança](../managed-hsm/security-domain.md) proporcionando um completo isolamento criptográfico de todos os outros HSMs que partilham a mesma infraestrutura de hardware.

Estas chaves estão protegidas em piscinas HSM de um único inquilino. Pode importar uma chave RSA, CE e simétrica, em forma macia ou exportando de um dispositivo HSM suportado. Também pode gerar chaves em piscinas HSM. Quando importa as chaves HSM utilizando o método descrito na [especificação BYOK (traga](../keys/byok-specification.md)a sua própria chave), permite material chave de transporte seguro em piscinas geridas de HSM. 

Para obter mais informações sobre as fronteiras geográficas, consulte [o Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-and-protection-methods"></a>Tipos-chave e métodos de proteção

O Key Vault suporta as chaves RSA e EC. O HSM gerido suporta rsa, CE e teclas simétricas. 

### <a name="hsm-protected-keys"></a>Chaves protegidas por HSM

|Tipo de chave|Cofres (apenas Premium SKU)|HSMs geridos|
|--|--|--|
|**EC-HSM**: Chave da curva elíptica | Suportado | Suportado|
|**RSA-HSM**: Chave RSA|Suportado|Suportado|
|**oct-HSM**: Chave simétrica|Não suportado|Suportado|
|||

### <a name="software-protected-keys"></a>Chaves protegidas por software

|Tipo de chave|Cofres|HSMs geridos|
|--|--|--|
**RSA**: Chave RSA "protegida por software"|Suportado|Não suportado
**CE**: Chave da curva elíptica "protegida por software"|Suportado|Não suportado
|||

### <a name="compliance"></a>Conformidade

|Tipo chave e destino|Conformidade|
|---|---|
|Chaves protegidas por software em cofres (Premium & Standard SKUs) | FIPS 140-2 Nível 1|
|Chaves protegidas pelo HSM em cofres (Premium SKU)| FIPS 140-2 Level 2|
|Chaves protegidas pelo HSM em HSM gerido|FIPS 140-2 Nível 3|
|||



Consulte [tipos chave, algoritmos e operações](about-keys-details.md) para detalhes sobre cada tipo de chave, algoritmos, operações, atributos e tags.

## <a name="next-steps"></a>Passos seguintes
- [Sobre o Key Vault](../general/overview.md)
- [Acerca do HSM Gerido](../managed-hsm/overview.md)
- [Acerca de segredos](../secrets/about-secrets.md)
- [Acerca de certificados](../certificates/about-certificates.md)
- [Visão geral da API do Key Vault REST](../general/about-keys-secrets-certificates.md)
- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Key Vault](../general/developers-guide.md)
