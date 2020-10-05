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
ms.openlocfilehash: 8ac5b0976e677cc48fb3fe47c50797b07acdbc44
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91708871"
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

## <a name="key-types-protection-methods-and-algorithms"></a>Tipos-chave, métodos de proteção e algoritmos

O Cofre-Chave suporta rSA, CE e chaves simétricas. 

### <a name="hsm-protected-keys"></a>Chaves protegidas por HSM

|Tipo de chave|Cofres (apenas Premium SKU)|Piscinas HSM geridas|
|--|--|--|--|
**EC-HSM**: Chave da curva elíptica|FIPS 140-2 Nível 2 HSM|FIPS 140-2 Nível 3 HSM
**RSA-HSM**: Chave RSA|FIPS 140-2 Nível 2 HSM|FIPS 140-2 Nível 3 HSM
**oct-HSM**: Simétrico|Não suportado|FIPS 140-2 Nível 3 HSM
||||

### <a name="software-protected-keys"></a>Chaves protegidas por software

|Tipo de chave|Cofres|Piscinas HSM geridas|
|--|--|--|--|
**RSA**: Chave RSA "protegida por software"|FIPS 140-2 Nível 1|Não suportado
**CE**: Chave da curva elíptica "protegida por software"|FIPS 140-2 Nível 1|Não suportado
||||

### <a name="supported-algorithms"></a>Algoritmos suportados

|Tipos/tamanhos/curvas| Encriptar/Desencriptar<br>(Embrulhar/Desembrulhar) | Assinar/Verificar | 
| --- | --- | --- |
|EC-P256, EC-P256K, EC-P384, CE-521|ND|ES256<br>ES256K<br>ES384<br>ES512|
|RSA 2K, 3K, 4K| RSA1_5<br>RSA-OAEP<br>RSA-OAEP-256|PS256<br>PS384<br>PS512<br>RS256<br>RS384<br>RS512<br>RIO RSNULL| 
|AES 128-bit, 256-bit| AES-KW<br>AES-GCM<br>AES-CBC| ND| 
|||

###  <a name="ec-algorithms"></a>Algoritmos da CE
 Os seguintes identificadores de algoritmo são suportados com chaves EC-HSM

#### <a name="curve-types"></a>Tipos de Curvas

-   **P-256** - A curva NIST P-256, definida no [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256K** - A curva SEC SECP256K1, definida em [SEC 2: Parâmetros de domínio de curva elíptica recomendados](https://www.secg.org/sec2-v2.pdf).
-   **P-384** - A curva NIST P-384, definida no [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** - A curva NIST P-521, definida no [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>SIGN/VERIFICAR

-   **ES256** - ECDSA para digestão SHA-256 e teclas criadas com curva P-256. Este algoritmo é descrito no [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** - ECDSA para digestão SHA-256 e teclas criadas com curva P-256K. Este algoritmo está pendente de normalização.
-   **ES384** - ECDSA para digestão SHA-384 e teclas criadas com curva P-384. Este algoritmo é descrito no [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** - ECDSA para digestão SHA-512 e teclas criadas com curva P-521. Este algoritmo é descrito no [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>Algoritmos RSA  
 Os seguintes identificadores de algoritmo são suportados com chaves RSA e RSA-HSM  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ENCRIPTAR/DESENCRIPTAR

-   **RSA1_5** - RSAES-PKCS1-V1_5 [RFC3447] encriptação chave  
-   **RSA-OAEP** - RSAES utilizando o acolchoado de encriptação assimétrica ideal (OAEP) [RFC3447], com os parâmetros padrão especificados pelo RFC 3447 na secção A.2.1. Estes parâmetros predefinidos estão a usar uma função de haxixe de SHA-1 e uma função de geração de máscara de MGF1 com SHA-1.  
-  **RSA-OAEP-256** – RSAES usando o acolchoamento de encriptação assimétrica ideal com uma função de hash de SHA-256 e uma função de geração de máscara de MGF1 com SHA-256

#### <a name="signverify"></a>SIGN/VERIFICAR

-   **PS256** - RSASSA-PSS usando SHA-256 e MGF1 com SHA-256, conforme descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384** - RSASSA-PSS usando SHA-384 e MGF1 com SHA-384, conforme descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512** - RSASSA-PSS usando SHA-512 e MGF1 com SHA-512, conforme descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256** - RSASSA-PKCS-v1_5 utilizando SHA-256. O valor da digestão fornecida pela aplicação deve ser calculado utilizando SHA-256 e deve ter 32 bytes de comprimento.  
-   **RS384** - RSASSA-PKCS-v1_5 utilizando SHA-384. O valor da digestão fornecida pela aplicação deve ser calculado utilizando SHA-384 e deve ter 48 bytes de comprimento.  
-   **RS512** - RSASSA-PKCS-v1_5 utilizando SHA-512. O valor da digestão fornecida pela aplicação deve ser calculado utilizando SHA-512 e deve ter 64 bytes de comprimento.  
-   **RSNULL** - Ver [RFC2437](https://tools.ietf.org/html/rfc2437), um caso de utilização especializado para permitir certos cenários TLS.  

###  <a name="symmetric-key-algorithms"></a>Algoritmos-chave simétricos
- **AES-KW** - AES Key Wrap[(RFC3394](https://tools.ietf.org/html/rfc3394)).
- **Encriptação AES-GCM** - AES no Modo de Contador Galois[(NIST SP800-38d)](https://csrc.nist.gov/publications/sp800)
- **Encriptação AES-CBC** - AES no Modo de Cadeia de Blocos Cipher[(NIST SP800-38a)](https://csrc.nist.gov/publications/sp800)

> [!NOTE] 
> A atual implementação AES-GCM e as APIs correspondentes são experimentais. A implementação e as APIs podem alterar-se substancialmente nas futuras iterações. 

##  <a name="key-operations"></a>Operações-chave

O HSM gerido suporta as seguintes operações em objectos-chave:  

-   **Criar**: Permite que um cliente crie uma chave no Cofre de Chaves. O valor da chave é gerado pela Key Vault e armazenado, e não é lançado ao cliente. As teclas assimétricas podem ser criadas no Cofre de Chaves.  
-   **Importação**: Permite que um cliente importe uma chave existente para o Cofre-Chave. As chaves assimétricas podem ser importadas para o Cofre-Chave utilizando uma série de métodos de embalagem diferentes dentro de uma construção JWK. 
-   **Atualização**: Permite a um cliente com permissões suficientes para modificar os metadados (atributos-chave) associados a uma chave previamente armazenada dentro do Key Vault.  
-   **Excluir**: Permite a um cliente com permissões suficientes para eliminar uma chave do Key Vault.  
-   **Lista**: Permite que um cliente enumada todas as chaves num dado Cofre de Chaves.  
-   **Versões da lista**: Permite que um cliente enuse todas as versões de uma determinada chave num dado Cofre de Chaves.  
-   **Obter**: Permite que um cliente recupere as partes públicas de uma determinada chave num Cofre de Chaves.  
-   **Backup**: Exporta uma chave de forma protegida.  
-   **Restauro**: Importa uma chave previamente apoiada.  

Para obter mais informações, consulte [as operações chave na referência API do Key Vault REST](/rest/api/keyvault).  

Uma vez criada uma chave no Cofre de Chaves, podem ser realizadas as seguintes operações criptográficas utilizando a chave:  

-   **Assine e Verifique**: Estritamente, esta operação é "sign haxixe" ou "verificar haxixe", uma vez que o Cofre de Chaves não suporta o hashing do conteúdo como parte da criação de assinaturas. As aplicações devem ter os dados a serem assinados localmente e, em seguida, solicitar que o Cofre-Chave assine o haxixe. A verificação dos haques assinados é suportada como uma operação de conveniência para aplicações que podem não ter acesso a material chave [público]. Para melhor desempenho da aplicação, as operações VERIFICAR devem ser realizadas localmente.  
-   **Encriptação / Embrulho de teclas**: Uma chave armazenada no Cofre de Chaves pode ser utilizada para proteger outra chave, tipicamente uma chave de encriptação de conteúdo simétrico (CEK). Quando a chave no Cofre de Chaves é assimétrica, a encriptação da chave é usada. Por exemplo, as operações RSA-OAEP e WRAPKEY/UNWRAPKEY são equivalentes a ENCRIPTAÇÃO/DESENCRIPTAÇÃO. Quando a chave no Cofre de Chaves é simétrica, o invólucro de chave é usado. Por exemplo, AES-KW. A operação WRAPKEY é suportada como uma conveniência para aplicações que podem não ter acesso a material chave [público]. Para melhor desempenho da aplicação, as operações WRAPKEY devem ser realizadas localmente.  
-   **Encriptar e Desencriptar:** Uma chave armazenada no Key Vault pode ser usada para encriptar ou desencriptar um único bloco de dados. O tamanho do bloco é determinado pelo tipo chave e algoritmo de encriptação selecionado. A operação Criptografia é fornecida por conveniência, para aplicações que podem não ter acesso a material chave [público]. Para melhor desempenho da aplicação, as operações de ENCRIPTA devem ser realizadas localmente.  

Embora wrapkey/UNWRAPKEY utilizando chaves assimétricas possa parecer supérfluo (uma vez que a operação é equivalente a ENCRIPTAÇÃO/DESENCRIPTA), a utilização de operações distintas é importante. A distinção proporciona separação semântica e de autorização destas operações, e consistência quando outros tipos-chave são suportados pelo serviço.  

O Cofre-Chave não suporta operações export. Uma vez que uma chave é a provisionada no sistema, não pode ser extraída ou o seu material-chave modificado. No entanto, os utilizadores do Key Vault podem exigir a sua chave para outros casos de utilização, como por exemplo depois de ter sido eliminado. Neste caso, podem utilizar as operações de BACKUP e RESTORE para exportar/importar a chave de forma protegida. As chaves criadas pela operação BACKUP não são utilizáveis fora do Key Vault. Alternativamente, a operação IMPORT pode ser utilizada contra várias instâncias do Cofre de Chaves.  

Os utilizadores podem restringir qualquer uma das operações criptográficas que o Key Vault suporta numa base por chave utilizando a propriedade key_ops do objeto JWK.  

Para obter mais informações sobre objetos JWK, consulte [a Tecla Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

## <a name="key-attributes"></a>Atributos-chave

Para além do material de chave, é possível especificar os seguintes atributos. Num Pedido JSON, as palavras-chave e aparelhos de atributos, '{' '}', são necessárias mesmo que não existam atributos especificados.  

- *ativado:* boolean, opcional, o padrão é **verdadeiro**. Especifica se a chave está ativada e utilizável para operações criptográficas. O atributo *ativado* é utilizado em conjunto com *a NBF* e *exp*. Quando ocorre uma operação entre *a NBF* e *a Exp,* só será permitida se *ativada* for **verdadeira**. As operações fora da janela *nbf*  /  *exp* são automaticamente proibidas, exceto para certos tipos de operação em [condições particulares](#date-time-controlled-operations).
- *nbf*: IntDate, opcional, o padrão é agora. O *atributo nbf* (não antes) identifica o tempo anterior ao qual a chave NÃO deve ser utilizada para operações criptográficas, exceto para certos tipos de operação em [condições específicas](#date-time-controlled-operations). O processamento do atributo *nbf* requer que a data/hora atual deve ser após ou igual à data/hora não antes enumerada no atributo *nbf.* O Cofre-Chave PODE providenciar alguma pequena margem de manobra, normalmente não mais do que alguns minutos, para explicar a distorção do relógio. O seu valor DEVE ser um número que contém um valor IntDate.  
- *exp*: IntDate, opcional, o padrão é "para sempre". O atributo *exp* (tempo de validade) identifica o tempo de validade em ou após o qual a chave NÃO deve ser utilizada para o funcionamento criptográfico, exceto para certos tipos de operação em [condições particulares](#date-time-controlled-operations). O processamento do atributo *exp* requer que a data/hora atual deve ser antes da data/hora de validade enumerada no atributo *exp.* O Cofre-Chave PODE providenciar alguma pequena margem de manobra, normalmente não mais do que alguns minutos, para explicar a distorção do relógio. O seu valor DEVE ser um número que contém um valor IntDate.  

Existem atributos adicionais apenas de leitura que estão incluídos em qualquer resposta que inclua atributos-chave:  

- *criado*: IntDate, opcional. O atributo *criado* indica quando esta versão da chave foi criada. O valor é nulo para as teclas criadas antes da adição deste atributo. O seu valor DEVE ser um número que contém um valor IntDate.  
- *atualizado*: IntDate, opcional. O atributo *atualizado* indica quando esta versão da chave foi atualizada. O valor é nulo para as teclas que foram atualizadas pela última vez antes da adição deste atributo. O seu valor DEVE ser um número que contém um valor IntDate.  

Para obter mais informações sobre o IntDate e outros tipos de dados, consulte [Sobre chaves, segredos e certificados: [Tipos de dados](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>Operações controladas à data

As teclas ainda não válidas e expiradas, fora da janela *nbf*  /  *exp,* trabalharão para **desencriptar,** **desembrulhar,** e **verificar** operações (não devolverão 403, Proibido). A razão para a utilização do estado ainda não válido é permitir que uma chave seja testada antes da utilização da produção. A razão para a utilização do estado caducado é permitir operações de recuperação de dados que foram criados quando a chave era válida. Além disso, pode desativar o acesso a uma chave utilizando as políticas do Key Vault ou atualizando o atributo de chave *ativado* para **falso**.

Para obter mais informações sobre tipos de dados, consulte [os tipos de Dados.](../general/about-keys-secrets-certificates.md#data-types)

Para obter mais informações sobre outros atributos possíveis, consulte a [Tecla Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

## <a name="key-tags"></a>Etiquetas de chaves

Pode especificar metadados específicos de aplicação adicionais sob a forma de etiquetas. O Key Vault suporta até 15 tags, cada uma das quais pode ter um nome de 256 caracteres e um valor de 256 caracteres.  

> [!NOTE] 
> As etiquetas são legíveis por um chamador se tiverem a *lista* ou *obterem* permissão para essa chave.

##  <a name="key-access-control"></a>Key access control (Controlo de acesso a chaves)

O controlo de acesso para chaves geridas pelo Key Vault é fornecido ao nível de um Cofre-Chave que funciona como o recipiente das chaves. A política de controlo de acesso para chaves é distinta da política de controlo de acesso para segredos no mesmo Cofre chave. Os utilizadores podem criar um ou mais cofres para segurar as chaves, e são obrigados a manter o cenário apropriado segmentação e gestão de chaves. O controlo de acesso para chaves é independente do controlo de acessos para segredos.  

As seguintes permissões podem ser concedidas, por base principal de utilizador/serviço, na entrada de controlo de acesso das chaves num cofre. Estas permissões espelham de perto as operações permitidas num objeto chave.  Conceder acesso a um diretor de serviço no cofre principal é uma operação única, e permanecerá igual para todas as subscrições do Azure. Pode usá-lo para distribuir os certificados que quiser. 

- Permissões para operações-chave de gestão
  - *obter*: Leia a parte pública de uma chave, mais os seus atributos
  - *lista*: Listar as chaves ou versões de uma chave armazenada num cofre de chaves
  - *atualização*: Atualizar os atributos para uma chave
  - *criar*: Criar novas chaves
  - *importação*de uma chave para um cofre chave
  - *eliminar*: Eliminar o objeto chave
  - *recuperar*: Recuperar uma chave eliminada
  - *backup*: Faça backup de uma chave em um cofre chave
  - *restaurar*: Restaurar uma chave de apoio para um cofre chave

- Permissões para operações criptográficas
  - *desencriptação*: Use a chave para desprotegir uma sequência de bytes
  - *encriptação*: Use a chave para proteger uma sequência arbitrária de bytes
  - *DesembrulharKey*: Use a chave para chaves simétricas embrulhadas desprotegidas
  - *wrapKey*: Use a chave para proteger uma chave simétrica
  - *verificar:* Utilize a chave para verificar as digestão  
  - *sinal*: Use a chave para assinar digestões
    
- Permissões para operações privilegiadas
  - *purga:* Purga (eliminar permanentemente) uma chave eliminada

Para obter mais informações sobre o trabalho com as teclas, consulte [as operações key in the Key Vault REST API reference](/rest/api/keyvault). Para obter informações sobre o estabelecimento de permissões, consulte [Cofres - Criar ou Atualizar](/rest/api/keyvault/vaults/createorupdate) e [Abóbadas - Atualizar a Política de Acesso](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="next-steps"></a>Passos seguintes
- [Sobre o Key Vault](../general/overview.md)
- [Acerca do HSM Gerido](../managed-hsm/overview.md)
- [Acerca de segredos](../secrets/about-secrets.md)
- [Acerca de certificados](../certificates/about-certificates.md)
- [Visão geral da API do Key Vault REST](../general/about-keys-secrets-certificates.md)
- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Key Vault](../general/developers-guide.md)
