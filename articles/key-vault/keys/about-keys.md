---
title: Sobre as chaves do cofre de chaves Azure - Cofre chave Azure
description: Visão geral da interface REST do Cofre de Chaves Azure e detalhes do desenvolvedor para chaves.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: f96ec80b529c594a383be8d668fd28b77372cd80
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900925"
---
# <a name="about-azure-key-vault-keys"></a>Sobre as chaves do cofre de chaves Azure

O Cofre de Chaves Azure suporta vários tipos e algoritmos de chave e permite a utilização de Módulos de Segurança de Hardware (HSM) para chaves de alto valor.

As teclas criptográficas no Cofre chave são representadas como objetos jSON Web Key [JWK]. As especificações javaScript object notation (JSON) e JavaScript Object Signing and Crypton (JOSE) são:

-   [Chave Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Encriptação web JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algoritmos Web JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web Signature (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

As especificações base JWK/JWA também são estendidas para permitir tipos-chave exclusivos da implementação do Cofre Chave. Por exemplo, a importação de chaves utilizando embalagens específicas do fornecedor HSM, permite o transporte seguro de chaves que só podem ser utilizadas em HSMs key vault. 

O Cofre chave Azure suporta as teclas Soft e Hard:

- **Teclas "soft"**: Uma chave processada em software pela Key Vault, mas é encriptada em repouso utilizando uma chave de sistema que está num HSM. Os clientes podem importar uma chave RSA ou EC (Curva Elíptica) existente, ou solicitar que o Cofre chave gere uma.
- **Teclas "hard"**: Uma chave processada num HSM (Módulo de Segurança de Hardware). Estas chaves estão protegidas num dos Principais Mundos de Segurança HSM (há um Mundo de Segurança por geografia para manter o isolamento). Os clientes podem importar uma chave RSA ou CE, de forma suave ou exportando de um dispositivo HSM compatível. Os clientes também podem solicitar o Key Vault para gerar uma chave. Este tipo de chave adiciona o atributo key_hsm ao JWK obter para transportar o material chave HSM.

Para obter mais informações sobre fronteiras geográficas, consulte [o Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

## <a name="cryptographic-protection"></a>Proteção criptográfica

O Cofre chave suporta apenas as teclas RSA e Elliptic Curve. 

-   **EC**: Tecla curva elíptica "macia".
-   **Tecla EC-HSM**: Tecla curva elíptica "dura".
-   **RSA**: Tecla RSA "macia".
-   **RSA-HSM**: Tecla RSA "Hard".

O Cofre chave suporta as teclas RSA dos tamanhos 2048, 3072 e 4096. O Cofre chave suporta os tipos de teclas Da Curva Elíptica P-256, P-384, P-521 e P-256K (SECP256K1).

Os módulos criptográficos que o Key Vault utiliza, seja HSM ou software, são validados por FIPS (Normas Federais de Processamento de Informação). Não é preciso fazer nada de especial para correr no modo FIPS. As chaves **criadas** ou **importadas** como protegidas por HSM são processadas dentro de um HSM, validado para o Nível 2 do FIPS 140-2. As teclas **criadas** ou **importadas** como protegidas por software, são processadas dentro de módulos criptográficos validados para o Nível 1 40-2 FIPS 1.

###  <a name="ec-algorithms"></a>Algoritmos CE
 Os identificadores de algoritmo seguem-se com teclas EC e EC-HSM no Cofre chave. 

#### <a name="curve-types"></a>Tipos de curvas

-   **P-256** - A curva NIST P-256, definida no [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256K** - A curva SEC SECP256K1, definida na [SEC 2: Recomendado elíptico Curcurve Domain Parâmetros](https://www.secg.org/sec2-v2.pdf).
-   **P-384** - A curva NIST P-384, definida no [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** - A curva NIST P-521, definida no [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>ASSINAR/VERIFICAR

-   **ES256** - ECDSA para digeridos sha-256 e chaves criadas com curva P-256. Este algoritmo é descrito no [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** - ECDSA para digeridos sha-256 e chaves criadas com curva P-256K. Este algoritmo está pendente de normalização.
-   **ES384** - ECDSA para digeridos sha-384 e chaves criadas com curva P-384. Este algoritmo é descrito no [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** - ECDSA para digeridos SHA-512 e chaves criadas com curva P-521. Este algoritmo é descrito no [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>Algoritmos RSA  
 Os identificadores de algoritmo seguem-se com teclas RSA e RSA-HSM no Cofre chave.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ENCRYPT/DECRYPT

-   **RSA1_5** - Encriptação chave RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP** - RSAES utilizando o acolchoamento de encriptação assimétrica ideal (OAEP) [RFC3447], com os parâmetros predefinidos especificados pelo RFC 3447 na secção A.2.1. Esses parâmetros padrão estão usando uma função de hash de SHA-1 e uma função de geração de máscaras de MGF1 com SHA-1.  

#### <a name="signverify"></a>ASSINAR/VERIFICAR

-   **PS256** - RSASSA-PSS usando SHA-256 e MGF1 com SHA-256, como descrito no [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384** - RSASSA-PSS usando SHA-384 e MGF1 com SHA-384, como descrito no [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512** - RSASSA-PSS usando SHA-512 e MGF1 com SHA-512, como descrito no [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256** - RSASSA-PKCS-v1_5 usando SHA-256. A aplicação fornecida valor de digestão deve ser calculada com recurso a SHA-256 e deve ter 32 bytes de comprimento.  
-   **RS384** - RSASSA-PKCS-v1_5 usando SHA-384. A aplicação fornecida valor de digestão deve ser calculada com recurso a SHA-384 e deve ter 48 bytes de comprimento.  
-   **RS512** - RSASSA-PKCS-v1_5 usando SHA-512. A aplicação fornecida valor de digestão deve ser calculada com recurso a SHA-512 e deve ter 64 bytes de comprimento.  
-   **RSNULL** - Ver [RFC2437], um caso de utilização especializado para permitir certos cenários de TLS.  

##  <a name="key-operations"></a>Operações-chave

O Cofre chave suporta as seguintes operações em objetos-chave:  

-   **Criar**: Permite que um cliente crie uma chave no Cofre chave. O valor da chave é gerado pela Key Vault e armazenado, e não é divulgado para o cliente. As chaves assimétricas podem ser criadas no Cofre chave.  
-   **Importação**: Permite que um cliente importe uma chave existente para o Cofre Chave. As chaves assimétricas podem ser importadas para o Cofre-Chave utilizando vários métodos de embalagem diferentes dentro de uma construção JWK. 
-   **Atualização**: Permite a um cliente com permissões suficientes para modificar os metadados (atributos chave) associados a uma chave anteriormente armazenada no Cofre chave.  
-   **Eliminar**: Permite a um cliente com permissões suficientes para apagar uma chave do Cofre de Chaves.  
-   **Lista**: Permite que um cliente enumere todas as chaves num dado Cofre chave.  
-   **Versões de lista**: Permite que um cliente enumere todas as versões de uma determinada chave num dado Key Vault.  
-   **Obter**: Permite que um cliente recupere as partes públicas de uma determinada chave num Cofre chave.  
-   **Backup**: Exporta uma chave de forma protegida.  
-   **Restaurar**: Importa uma chave previamente apoiada.  

Para mais informações, consulte [as principais operações na referência](/rest/api/keyvault)a API do Cofre Chave .  

Uma vez criada uma chave no Cofre chave, as seguintes operações criptográficas podem ser realizadas utilizando a chave:  

-   **Sinal e Verificação**: Estritamente, esta operação é "sinal de hash" ou "verificar hash", uma vez que o Key Vault não suporta o hashing dos conteúdos como parte da criação de assinaturas. As aplicações devem hash os dados a assinar localmente, em seguida, solicitar que key vault assine o hash. A verificação de hashes assinados é suportada como uma operação de conveniência para aplicações que podem não ter acesso a material-chave [público]. Para um melhor desempenho de aplicação, as operações de VERIFICAÇÃO devem ser realizadas localmente.  
-   **Encriptação da chave / Embrulho**: Uma chave armazenada no Cofre chave pode ser usada para proteger outra chave, tipicamente uma chave de encriptação de conteúdo simétrico (CEK). Quando a chave no Cofre chave é assimétrica, a encriptação da chave é usada. Por exemplo, as operações RSA-OAEP e WRAPKEY são equivalentes a ENCRIPTAÇÃO/DECRYPT. Quando a chave no Cofre-Chave é simétrica, o invólucro da chave é utilizado. Por exemplo, AES-KW. A operação WRAPKEY é suportada como uma conveniência para aplicações que podem não ter acesso a material chave [público]. Para um melhor desempenho de aplicação, as operações WRAPKEY devem ser realizadas localmente.  
-   **Encriptar e desencriptar:** Uma chave armazenada no Cofre de Chaves pode ser usada para encriptar ou desencriptar um único bloco de dados. O tamanho do bloco é determinado pelo tipo chave e algoritmo de encriptação selecionado. A operação Encrypt está prevista para conveniência, para aplicações que podem não ter acesso a material chave [público]. Para um melhor desempenho de aplicação, as operações de ENCRIPTAÇÃO devem ser realizadas localmente.  

Embora o WRAPKEY/UNWRAPKEY utilize teclas assimétricas pode parecer supérfluo (uma vez que a operação é equivalente a ENCRIPTAÇÃO/DECRYPT), a utilização de operações distintas é importante. A distinção proporciona a separação semântica e de autorização destas operações, e a consistência quando outros tipos-chave são suportados pelo serviço.  

O Cofre Chave não suporta operações de exportação. Uma vez que uma chave é aprovisionada no sistema, não pode ser extraída ou o seu material-chave modificado. No entanto, os utilizadores da Key Vault podem exigir a sua chave para outros casos de utilização, como por exemplo, depois de ter sido eliminado. Neste caso, podem utilizar as operações DE BACKUP e RESTAURO para exportar/importar a chave de forma protegida. As chaves criadas pela operação BACKUP não são utilizáveis fora do Cofre chave. Em alternativa, a operação IMPORT pode ser utilizada contra várias instâncias do Cofre-Chave.  

Os utilizadores podem restringir qualquer uma das operações criptográficas que o Key Vault suporta numa base por chave utilizando a propriedade key_ops do objeto JWK.  

Para obter mais informações sobre objetos JWK, consulte [jSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

## <a name="key-attributes"></a>Principais atributos

Para além do material de chave, é possível especificar os seguintes atributos. Num Pedido JSON, as palavras-chave e aparelhos atributos, '{' '}', são necessários mesmo que não existam atributos especificados.  

- *ativado:* booleano, opcional, padrão é **verdade**. Especifica se a chave está ativada e utilizável para operações criptográficas. O atributo *ativado* é utilizado em conjunto com *nbf* e *exp*. Quando ocorrer uma operação entre *a nbf* e a *ex-exp,* só será permitida se *ativada* for **verdadeira**. As operações fora da janela *nbf* / *exp* são automaticamente proibidas, com exceção de certos tipos de funcionamento em [condições específicas](#date-time-controlled-operations).
- *nbf*: IntDate, opcional, o padrão é agora. O atributo *nbf* (não antes) identifica o tempo anterior ao qual a chave NÃO DEVE SER utilizada para operações criptográficas, com exceção de certos tipos de funcionamento em [condições específicas](#date-time-controlled-operations). O processamento do atributo *nbf* requer que a data/hora atual DEVE seja posterior ou igual à data/hora não antes enumerada no atributo *nbf.* O Cofre chave pode providenciar uma pequena margem de manobra, normalmente não mais do que alguns minutos, para explicar a distorção do relógio. O seu valor DEVE ser um número que contenha um valor IntDate.  
- *exp*: IntDate, opcional, o padrão é "para sempre". O *atributo exp* (tempo de validade) identifica o tempo de validade em ou após o qual a chave NÃO DEVE SER utilizada para o funcionamento criptográfico, exceto para certos tipos de funcionamento em [condições específicas](#date-time-controlled-operations). O processamento do atributo *exp* exige que a data/hora atual DEVE seja antes da data/hora de validade enumerada no atributo *exp.* O Cofre chave pode fornecer alguma pequena margem de manobra, normalmente não mais do que alguns minutos, para explicar a distorção do relógio. O seu valor DEVE ser um número que contenha um valor IntDate.  

Existem atributos adicionais de leitura que estão incluídos em qualquer resposta que inclua atributos-chave:  

- *criado*: IntDate, opcional. O atributo *criado* indica quando esta versão da chave foi criada. O valor é nulo para as chaves criadas antes da adição deste atributo. O seu valor DEVE ser um número que contenha um valor IntDate.  
- *atualizado*: IntDate, opcional. O atributo *atualizado* indica quando esta versão da chave foi atualizada. O valor é nulo para as chaves que foram atualizadas pela última vez antes da adição deste atributo. O seu valor DEVE ser um número que contenha um valor IntDate.  

Para obter mais informações sobre o IntDate e outros tipos de dados, consulte [Sobre chaves, segredos e certificados: Tipos de [dados](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>Operações controladas por data

As chaves ainda não válidas e expiradas, fora da janela *da NBF* / *exp,* funcionarão para **desencriptar,** **desembrulhar,** e **verificar** as operações (não devolverão 403, Proibido). A razão para a utilização do estado ainda não válido é permitir que uma chave seja testada antes da utilização da produção. A razão para a utilização do estado expirado é permitir operações de recuperação de dados que foram criados quando a chave era válida. Além disso, pode desativar o acesso a uma chave utilizando as políticas do Cofre chave, ou atualizando o atributo-chave *ativado* a **falso**.

Para obter mais informações sobre os tipos de dados, consulte [os tipos de dados](../general/about-keys-secrets-certificates.md#data-types).

Para obter mais informações sobre outros possíveis atributos, consulte a [Chave Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

## <a name="key-tags"></a>Etiquetas-chave

Pode especificar metadados específicos de aplicação adicionais sob a forma de etiquetas. Key Vault suporta até 15 tags, cada uma das quais pode ter um nome de 256 caracteres e um valor de 256 caracteres.  

>[!Note]
>As etiquetas são legíveis por um chamador se tiverem a *lista* ou *obterem* permissão para esse tipo de objeto (chaves, segredos ou certificados).

##  <a name="key-access-control"></a>Key access control (Controlo de acesso a chaves)

O controlo de acesso às chaves geridas pelo Key Vault é fornecido ao nível de um Cofre chave que funciona como recipiente de chaves. A política de controlo de acesso seleções é distinta da política de controlo de acesso para segredos no mesmo Cofre chave. Os utilizadores podem criar um ou mais cofres para segurar as chaves, e são obrigados a manter o cenário de segmentação e gestão apropriadas das teclas. O controlo de acesso às chaves é independente do controlo de acesso para segredos.  

As seguintes permissões podem ser concedidas, por utilizador/serviço principal, na entrada de controlo de acesso das teclas num cofre. Estas permissões espelham de perto as operações permitidas num objeto chave.  Conceder acesso a um diretor de serviço no cofre chave é uma operação única, e permanecerá o mesmo para todas as subscrições do Azure. Pode usá-lo para implementar os certificados que quiser. 

- Permissões para operações de gestão chave
  - *obter*: Leia a parte pública de uma chave, mais os seus atributos
  - *lista*: Lista ruminar as chaves ou versões de uma chave armazenada num cofre chave
  - *atualização*: Atualizar os atributos para uma chave
  - *criar*: Criar novas teclas
  - *importação*: Importar uma chave para um cofre chave
  - *eliminar*: Apagar o objecto-chave
  - *recuperar*: Recuperar uma chave eliminada
  - *backup*: Apoiar uma chave em um cofre chave
  - *restaurar*: Restaurar uma chave apoiada para um cofre chave

- Permissões para operações criptográficas
  - *desencriptar:* Utilize a chave para desproteger uma sequência de bytes
  - *encriptar:* Utilize a chave para proteger uma sequência arbitrária de bytes
  - *desembrulharChave*: Utilize a chave para desproteger as chaves simétricas embrulhadas
  - *wrapKey*: Utilize a chave para proteger uma chave simétrica
  - *verificar:* Utilize a chave para verificar as digestões  
  - *sinal*: Utilize a chave para assinar digestões
    
- Permissões para operações privilegiadas
  - *Purga*: Purgar (excluir permanentemente) uma chave eliminada

Para obter mais informações sobre o trabalho com as chaves, consulte [as operações chave na referência aAPI do Cofre Chave](/rest/api/keyvault). Para obter informações sobre o estabelecimento de permissões, consulte [Cofres - Criar ou Atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres - Atualizar a Política](/rest/api/keyvault/vaults/updateaccesspolicy)de Acesso . 

## <a name="next-steps"></a>Passos seguintes

- [Sobre o Key Vault](../general/overview.md)
- [Acerca de chaves, segredos e certificados](../general/about-keys-secrets-certificates.md)
- [Acerca de segredos](../secrets/about-secrets.md)
- [Acerca de certificados](../certificates/about-certificates.md)
- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Cofre de Chaves](../general/developers-guide.md)
