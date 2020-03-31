---
title: Sobre chaves, segredos e certificados azure key vault - Cofre chave Azure
description: Visão geral da interface rest do Cofre de Chaves Azure e detalhes do desenvolvedor para chaves, segredos e certificados.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: dd8be482009e067bf9016cc8e351fc42a2db39c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271736"
---
# <a name="about-keys-secrets-and-certificates"></a>Sobre chaves, segredos e certificados

O Azure Key Vault permite que as aplicações e utilizadores do Microsoft Azure armazenem e utilizem vários tipos de dados secretos/chave:

- Chaves criptográficas: Suporta vários tipos e algoritmos de chave e permite a utilização de Módulos de Segurança de Hardware (HSM) para teclas de alto valor. 
- Segredos: Fornece armazenamento seguro de segredos, tais como senhas e cadeias de ligação de base de dados.
- Certificados: Suporta certificados, que são construídos em cima de chaves e segredos e adicionam uma funcionalidade de renovação automatizada.
- Armazenamento Azure: Pode gerir chaves de uma conta de Armazenamento Azure para si. Internamente, o Cofre chave pode listar (sincronizar) chaves com uma Conta de Armazenamento Azure e regenerar (rodar) as teclas periodicamente. 

Para obter informações mais gerais sobre o Cofre chave, veja [o que é o Cofre chave Azure?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

As seguintes secções oferecem informações gerais aplicáveis em toda a implementação do serviço Key Vault.

### <a name="supporting-standards"></a>Normas de apoio

As especificações javaScript object notation (JSON) e JavaScript Object Signing and Encryption (JOSE) são informações importantes de fundo.  

-   [Chave Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)  
-   [Encriptação web JSON (JWE)](https://tools.ietf.org/html/draft-ietf-jose-json-web-encryption-40)  
-   [Algoritmos Web JSON (JWA)](https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40)  
-   [JSON Web Signature (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41)  

### <a name="data-types"></a>Tipos de dados

Consulte as especificações JOSE para os tipos de dados relevantes para chaves, encriptação e assinatura.  

-   **algoritmo** - um algoritmo suportado para uma operação chave, por exemplo, RSA1_5  
-   **cifra-valor -** octetos de texto cifrados, codificados usando Base64URL  
-   **valor digestão** - a saída de um algoritmo de hash, codificado usando Base64URL  
-   **tipo chave** - um dos tipos-chave suportados, por exemplo RSA (Rivest-Shamir-Adleman).  
-   **valor simples -** octetos de texto simples, codificados usando Base64URL  
-   **assinatura-valor** - saída de um algoritmo de assinatura, codificado usando Base64URL  
-   **base64URL** - um valor binário codificado de Base64URL [RFC4648]  
-   **boolean -** verdadeiro ou falso  
-   **Identidade** - uma identidade do Azure Ative Directory (AAD).  
-   **IntDate** - um valor decimal JSON que representa o número de segundos de 1970-01-01T0:0:0Z UTC até à data/hora UTC especificada. Consulte o RFC3339 para obter mais informações sobre data/horários, em geral e utc em particular.  

### <a name="objects-identifiers-and-versioning"></a>Objetos, identificadores e versonagem

Os objetos armazenados no Cofre chave são versonizados sempre que for criada uma nova instância de um objeto. A cada versão é atribuída a um identificador e URL únicos. Quando um objeto é criado pela primeira vez, é dado um identificador de versão única e marcado como a versão atual do objeto. A criação de um novo exemplo com o mesmo nome de objeto dá ao novo objeto um identificador de versão único, fazendo com que se torne a versão atual.  

Os objetos no Cofre-Chave podem ser abordados utilizando o identificador atual ou um identificador específico da versão. Por exemplo, dada uma `MasterKey`Chave com o nome, executar operações com o identificador atual faz com que o sistema utilize a versão mais recente disponível. A realização de operações com o identificador específico da versão faz com que o sistema utilize essa versão específica do objeto.  

Os objetos são identificados exclusivamente dentro do Cofre chave utilizando um URL. Nenhum dos dois objetos no sistema tem o mesmo URL, independentemente da geolocalização. O URL completo de um objeto chama-se Identificador de Objetos. O URL consiste num prefixo que identifica o Cofre de Chave, tipo de objeto, utilizador fornecido Nome do Objeto e uma Versão de Objeto. O Nome do Objeto é insensível e imutável. Os identificadores que não incluem a Versão do Objeto são referidos como Identificadores de Base.  

Para mais informações, consulte [Autenticação, pedidos e respostas](authentication-requests-and-responses.md)

Um identificador de objetos tem o seguinte formato geral:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Em que:  

|||  
|-|-|  
|`keyvault-name`|O nome de um cofre chave no serviço Microsoft Azure Key Vault.<br /><br /> Os nomes key vault são selecionados pelo utilizador e são globalmente únicos.<br /><br /> O nome do cofre de chaves deve ser uma corda de caracteres 3-24, contendo apenas 0-9, a-z, A-Z, e ..|  
|`object-type`|O tipo de objeto, "chaves" ou "segredos".|  
|`object-name`|Um `object-name` é um utilizador fornecido nome e deve ser único dentro de um Cofre chave. O nome deve ser uma cadeia de caracteres 1-127, contendo apenas 0-9, a-z, A-Z, e - .|  
|`object-version`|Um `object-version` é um identificador de cadeia de 32 caracteres gerado pelo sistema que é opcionalmente usado para abordar uma versão única de um objeto.|  

## <a name="key-vault-keys"></a>Chaves do cofre da chave

### <a name="keys-and-key-types"></a>Chaves e tipos de chaves

As teclas criptográficas no Cofre chave são representadas como objetos jSON Web Key [JWK]. As especificações base JWK/JWA também são estendidas para permitir tipos-chave exclusivos da implementação do Cofre Chave. Por exemplo, a importação de chaves utilizando embalagens específicas do fornecedor HSM, permite o transporte seguro de chaves que só podem ser utilizadas em HSMs key vault.  

- **Teclas "soft"**: Uma chave processada em software pela Key Vault, mas é encriptada em repouso utilizando uma chave de sistema que está num HSM. Os clientes podem importar uma chave RSA ou EC (Curva Elíptica) existente, ou solicitar que o Cofre chave gere uma.
- **Teclas "hard"**: Uma chave processada num HSM (Módulo de Segurança de Hardware). Estas chaves estão protegidas num dos Principais Mundos de Segurança HSM (há um Mundo de Segurança por geografia para manter o isolamento). Os clientes podem importar uma chave RSA ou CE, de forma suave ou exportando de um dispositivo HSM compatível. Os clientes também podem solicitar o Key Vault para gerar uma chave. Este tipo de chave adiciona o atributo key_hsm ao JWK obter para transportar o material chave HSM.

     Para obter mais informações sobre fronteiras geográficas, consulte [o Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

O Cofre chave suporta apenas as teclas RSA e Elliptic Curve. 

-   **EC**: Tecla curva elíptica "macia".
-   **Tecla EC-HSM**: Tecla curva elíptica "dura".
-   **RSA**: Tecla RSA "macia".
-   **RSA-HSM**: Tecla RSA "Hard".

O Cofre chave suporta as teclas RSA dos tamanhos 2048, 3072 e 4096. O Cofre chave suporta os tipos de teclas Da Curva Elíptica P-256, P-384, P-521 e P-256K (SECP256K1).

### <a name="cryptographic-protection"></a>Proteção criptográfica

Os módulos criptográficos que o Key Vault utiliza, seja HSM ou software, são validados por FIPS (Normas Federais de Processamento de Informação). Não é preciso fazer nada de especial para correr no modo FIPS. As chaves **criadas** ou **importadas** como protegidas por HSM são processadas dentro de um HSM, validado para o Nível 2 do FIPS 140-2. As teclas **criadas** ou **importadas** como protegidas por software, são processadas dentro de módulos criptográficos validados para o Nível 1 40-2 FIPS 1. Para mais informações, consulte [Chaves e tipos de chaves](#keys-and-key-types).

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

###  <a name="key-operations"></a>Operações-chave

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

-   **Sinal e Verificação**: Estritamente, esta operação é "sinal de hash" ou "verificar hash", uma vez que o Key Vault não suporta o hashing dos conteúdos como parte da criação de assinaturas. As aplicações devem hash os dados a assinar localmente, em seguida, solicitar que key vault assine o hash. A verificação de hashes assinados é suportada como uma operação de conveniência para aplicações que podem não ter acesso a material-chave [público]. Para um melhor desempenho de aplicação, verifique se as operações são realizadas localmente.  
-   **Encriptação da chave / Embrulho**: Uma chave armazenada no Cofre chave pode ser usada para proteger outra chave, tipicamente uma chave de encriptação de conteúdo simétrico (CEK). Quando a chave no Cofre chave é assimétrica, a encriptação da chave é usada. Por exemplo, as operações RSA-OAEP e WRAPKEY são equivalentes a ENCRIPTAÇÃO/DECRYPT. Quando a chave no Cofre-Chave é simétrica, o invólucro da chave é utilizado. Por exemplo, AES-KW. A operação WRAPKEY é suportada como uma conveniência para aplicações que podem não ter acesso a material chave [público]. Para um melhor desempenho de aplicação, as operações WRAPKEY devem ser realizadas localmente.  
-   **Encriptar e desencriptar:** Uma chave armazenada no Cofre de Chaves pode ser usada para encriptar ou desencriptar um único bloco de dados. O tamanho do bloco é determinado pelo tipo chave e algoritmo de encriptação selecionado. A operação Encrypt está prevista para conveniência, para aplicações que podem não ter acesso a material chave [público]. Para um melhor desempenho da aplicação, as operações de encriptação devem ser realizadas localmente.  

Embora o WRAPKEY/UNWRAPKEY utilize teclas assimétricas pode parecer supérfluo (uma vez que a operação é equivalente a ENCRIPTAÇÃO/DECRYPT), a utilização de operações distintas é importante. A distinção proporciona a separação semântica e de autorização destas operações, e a consistência quando outros tipos-chave são suportados pelo serviço.  

O Cofre Chave não suporta operações de exportação. Uma vez que uma chave é aprovisionada no sistema, não pode ser extraída ou o seu material-chave modificado. No entanto, os utilizadores da Key Vault podem exigir a sua chave para outros casos de utilização, como por exemplo, depois de ter sido eliminado. Neste caso, podem utilizar as operações DE BACKUP e RESTAURO para exportar/importar a chave de forma protegida. As chaves criadas pela operação BACKUP não são utilizáveis fora do Cofre chave. Em alternativa, a operação IMPORT pode ser utilizada contra várias instâncias do Cofre-Chave.  

Os utilizadores podem restringir qualquer uma das operações criptográficas que o Key Vault suporta numa base por chave utilizando a propriedade key_ops do objeto JWK.  

Para obter mais informações sobre objetos JWK, consulte [jSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

###  <a name="key-attributes"></a>Principais atributos

Para além do material de chave, é possível especificar os seguintes atributos. Num Pedido JSON, as palavras-chave e aparelhos atributos, '{' '}', são necessários mesmo que não existam atributos especificados.  

- *ativado:* booleano, opcional, padrão é **verdade**. Especifica se a chave está ativada e utilizável para operações criptográficas. O atributo *ativado* é utilizado em conjunto com *nbf* e *exp*. Quando ocorrer uma operação entre *a nbf* e a *ex-exp,* só será permitida se *ativada* for **verdadeira**. As operações fora da janela *nbf* / *exp* são automaticamente proibidas, com exceção de certos tipos de funcionamento em [condições específicas](#date-time-controlled-operations).
- *nbf*: IntDate, opcional, o padrão é agora. O atributo *nbf* (não antes) identifica o tempo anterior ao qual a chave NÃO DEVE SER utilizada para operações criptográficas, com exceção de certos tipos de funcionamento em [condições específicas](#date-time-controlled-operations). O processamento do atributo *nbf* requer que a data/hora atual DEVE seja posterior ou igual à data/hora não antes enumerada no atributo *nbf.* O Cofre chave pode providenciar uma pequena margem de manobra, normalmente não mais do que alguns minutos, para explicar a distorção do relógio. O seu valor DEVE ser um número que contenha um valor IntDate.  
- *exp*: IntDate, opcional, o padrão é "para sempre". O *atributo exp* (tempo de validade) identifica o tempo de validade em ou após o qual a chave NÃO DEVE SER utilizada para o funcionamento criptográfico, exceto para certos tipos de funcionamento em [condições específicas](#date-time-controlled-operations). O processamento do atributo *exp* exige que a data/hora atual DEVE seja antes da data/hora de validade enumerada no atributo *exp.* O Cofre chave pode fornecer alguma pequena margem de manobra, normalmente não mais do que alguns minutos, para explicar a distorção do relógio. O seu valor DEVE ser um número que contenha um valor IntDate.  

Existem atributos adicionais de leitura que estão incluídos em qualquer resposta que inclua atributos-chave:  

- *criado*: IntDate, opcional. O atributo *criado* indica quando esta versão da chave foi criada. O valor é nulo para as chaves criadas antes da adição deste atributo. O seu valor DEVE ser um número que contenha um valor IntDate.  
- *atualizado*: IntDate, opcional. O atributo *atualizado* indica quando esta versão da chave foi atualizada. O valor é nulo para as chaves que foram atualizadas pela última vez antes da adição deste atributo. O seu valor DEVE ser um número que contenha um valor IntDate.  

Para obter mais informações sobre o IntDate e outros tipos de dados, consulte [os tipos de dados](#data-types)  

#### <a name="date-time-controlled-operations"></a>Operações controladas por data

As chaves ainda não válidas e expiradas, fora da janela *da NBF* / *exp,* funcionarão para **desencriptar,** **desembrulhar,** e **verificar** as operações (não devolverão 403, Proibido). A razão para a utilização do estado ainda não válido é permitir que uma chave seja testada antes da utilização da produção. A razão para a utilização do estado expirado é permitir operações de recuperação de dados que foram criados quando a chave era válida. Além disso, pode desativar o acesso a uma chave utilizando as políticas do Cofre chave, ou atualizando o atributo-chave *ativado* a **falso**.

Para obter mais informações sobre os tipos de dados, consulte [os tipos de dados](#data-types).

Para obter mais informações sobre outros possíveis atributos, consulte a [Chave Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

### <a name="key-tags"></a>Etiquetas-chave

Pode especificar metadados específicos de aplicação adicionais sob a forma de etiquetas. Key Vault suporta até 15 tags, cada uma das quais pode ter um nome de 256 caracteres e um valor de 256 caracteres.  

>[!Note]
>As etiquetas são legíveis por um chamador se tiverem a *lista* ou *obterem* permissão para esse tipo de objeto (chaves, segredos ou certificados).

###  <a name="key-access-control"></a>Key access control (Controlo de acesso a chaves)

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

## <a name="key-vault-secrets"></a>Segredos do Cofre chave 

### <a name="working-with-secrets"></a>Trabalhar com segredos

Do ponto de vista de um desenvolvedor, as APIs do Cofre Chave aceitam e devolvem valores secretos como cordas. Internamente, key vault armazena e gere segredos como sequências de octetos (bytes de 8 bits), com um tamanho máximo de 25 k bytes cada. O serviço Key Vault não fornece semântica para segredos. Apenas aceita os dados, encripta-os, armazena-os e devolve um identificador secreto ("id"). O identificador pode ser usado para recuperar o segredo mais tarde.  

Para dados altamente confidenciais, os clientes devem considerar camadas adicionais de proteção para os dados. A encriptação de dados com uma chave de proteção separada antes do armazenamento no Key Vault é um exemplo disso.  

O Key Vault também suporta um campo de conteúdoType para segredos. Os clientes podem especificar o tipo de conteúdo de um segredo para ajudar na interpretação dos dados secretos quando são recuperados. O comprimento máximo deste campo é de 255 caracteres. Não há valores pré-definidos. O uso sugerido é uma dica para interpretar os dados secretos. Por exemplo, uma implementação pode armazenar palavras-passe e certificados como segredos, em seguida, usar este campo para diferenciar. Não há valores predefinidos.  

### <a name="secret-attributes"></a>Atributos secretos

Além dos dados secretos, podem ser especificados os seguintes atributos:  

- *exp*: IntDate, opcional, o padrão é **para sempre**. O atributo *exp* (tempo de validade) identifica o tempo de validade em ou após o qual os dados secretos NÃO devem ser recuperados, exceto em [situações específicas](#date-time-controlled-operations). Este campo destina-se apenas a fins **informantes,** uma vez que informa os utilizadores do serviço de cofre seletiva que um determinado segredo pode não ser utilizado. O seu valor DEVE ser um número que contenha um valor IntDate.   
- *nbf*: IntDate, opcional, por defeito é **agora**. O atributo *nbf* (não antes) identifica o tempo anterior ao qual os dados secretos NÃO devem ser recuperados, exceto em [situações específicas](#date-time-controlled-operations). Este campo é apenas para fins **informantes.** O seu valor DEVE ser um número que contenha um valor IntDate. 
- *ativado:* booleano, opcional, padrão é **verdade**. Este atributo especifica se os dados secretos podem ser recuperados. O atributo ativado é utilizado em conjunto com a *nbf* e *exp* quando ocorre uma operação entre **true** *nbf* e *exp,* só será permitido se estiver ativado. As operações fora da janela *nbf* e *exp* são automaticamente proibidas, exceto em [situações específicas](#date-time-controlled-operations).  

Existem atributos adicionais de leitura que estão incluídos em qualquer resposta que inclua atributos secretos:  

- *criado*: IntDate, opcional. O atributo criado indica quando esta versão do segredo foi criada. Este valor é nulo para segredos criados antes da adição deste atributo. O seu valor deve ser um número que contenha um valor IntDate.  
- *atualizado*: IntDate, opcional. O atributo atualizado indica quando esta versão do segredo foi atualizada. Este valor é nulo para segredos que foram atualizados pela última vez antes da adição deste atributo. O seu valor deve ser um número que contenha um valor IntDate.

#### <a name="date-time-controlled-operations"></a>Operações controladas por data

Uma **operação** secreta funcionará por segredos ainda não válidos e expirados, fora da janela da *NBF* / *Exp.* Chamar uma **operação** secreta para um segredo ainda não válido, pode ser usado para fins de teste. Recuperar **(obter**ting) um segredo expirado, pode ser usado para operações de recuperação.

Para obter mais informações sobre os tipos de dados, consulte [os tipos de dados](#data-types).  

### <a name="secret-access-control"></a>Secret access control (Controlo de acesso a segredos)

O Controlo de Acesso para segredos geridos no Cofre chave é fornecido ao nível do Cofre chave que contém esses segredos. A política de controlo de acesso sacana é distinta da política de controlo de acesso para chaves no mesmo Cofre chave. Os utilizadores podem criar um ou mais cofres para guardar segredos, e são obrigados a manter o cenário apropriado de segmentação e gestão de segredos.   

As seguintes permissões podem ser usadas, por principal, nos segredos acesso à entrada de controlo de um cofre, e espelhar de perto as operações permitidas num objeto secreto:  

- Permissões para operações de gestão secreta
  - *obter*: Ler um segredo  
  - *lista*: Lista rumos os segredos ou versões de um segredo armazenado num Cofre-Chave  
  - *conjunto*: Criar um segredo  
  - *excluir*: Apagar um segredo  
  - *recuperar*: Recuperar um segredo apagado
  - *backup*: Backup um segredo em um cofre chave
  - *restaurar*: Restaurar um segredo apoiado para um cofre chave

- Permissões para operações privilegiadas
  - *Purga*: Purgar (apagar permanentemente) um segredo apagado

Para obter mais informações sobre o trabalho com segredos, consulte [operações secretas na referência da Key Vault REST API](/rest/api/keyvault). Para obter informações sobre o estabelecimento de permissões, consulte [Cofres - Criar ou Atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres - Atualizar a Política](/rest/api/keyvault/vaults/updateaccesspolicy)de Acesso . 

### <a name="secret-tags"></a>Etiquetas secretas  
Pode especificar metadados específicos de aplicação adicionais sob a forma de etiquetas. Key Vault suporta até 15 tags, cada uma das quais pode ter um nome de 256 caracteres e um valor de 256 caracteres.  

>[!Note]
>As etiquetas são legíveis por um chamador se tiverem a *lista* ou *obterem* permissão para esse tipo de objeto (chaves, segredos ou certificados).

## <a name="key-vault-certificates"></a>Certificados de cofre de chaves

O suporte de certificados Key Vault prevê a gestão dos seus certificados x509 e os seguintes comportamentos:  

-   Permite que um proprietário de certificado crie um certificado através de um processo de criação do Key Vault ou através da importação de um certificado existente. Inclui certificados auto-assinados e certificados gerados pela Autoridade de Certificados.
-   Permite que um proprietário de certificado Key Vault implemente armazenamento e gestão seguros de certificados X509 sem interação com material chave privado.  
-   Permite que um proprietário de certificado crie uma política que direcione o Key Vault para gerir o ciclo de vida de um certificado.  
-   Permite que os proprietários de certificados forneçam informações de contacto para notificação sobre eventos de ciclo de vida de expiração e renovação de certificado.  
-   Suporta a renovação automática com emitentes selecionados - Parceiro Key Vault X509 fornecedores de certificados /autoridades de certificados.

>[!Note]
>Os prestadores/autoridades não parceiros também são autorizados, mas não apoiarão a funcionalidade de renovação automática.

### <a name="composition-of-a-certificate"></a>Composição de um certificado

Quando um certificado key vault é criado, uma chave e segredo endereçados também são criados com o mesmo nome. A chave key vault permite operações chave e o segredo do Cofre chave permite a recuperação do valor do certificado como segredo. Um certificado Key Vault também contém metadados de certificados x509 públicos.  

O identificador e a versão dos certificados são semelhantes aos das chaves e segredos. Uma versão específica de uma chave endereçada e um segredo criado com a versão do certificado Key Vault está disponível na resposta do certificado Key Vault.
 
![Os certificados são objetos complexos](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Chave exportável ou não exportável

Quando um certificado key vault é criado, pode ser recuperado do segredo endereçado com a chave privada em formato PFX ou PEM. A política utilizada para a criação do certificado deve indicar que a chave é exportável. Se a apólice indica não ser exportável, então a chave privada não faz parte do valor quando recuperada como segredo.  

A chave endereçada torna-se mais relevante com certificados KV não exportáveis. As operações da chave KV endereçadas são mapeadas a partir do campo de *utilização* da chave da política de certificados KV utilizada para criar o Certificado KV.  

São suportados dois tipos de teclas – *RSA* ou *RSA HSM* com certificados. Exportável só é permitido com RSA, não suportado pela RSA HSM.  

### <a name="certificate-attributes-and-tags"></a>Atributos e Etiquetas de Certificado

Além dos metadados de certificados, uma chave endereçada e um segredo endereçado, um certificado Key Vault também contém atributos e tags.  

#### <a name="attributes"></a>Atributos

Os atributos do certificado são espelhados em atributos da chave endereçada e do segredo criado quando o certificado KV é criado.  

Um certificado key vault tem os seguintes atributos:  

-   *ativado:* booleano, opcional, padrão é **verdade**. Pode ser especificado para indicar se os dados do certificado podem ser recuperados como secretos ou operáveis como chave. Também utilizado em conjunto com *nbf* e *exp* quando uma operação ocorre entre *nbf* e *exp*, e só será permitido se ativado for definido para verdade. As operações fora da *janela nbf* e *exp* são automaticamente proibidas.  

Existem atributos adicionais de leitura que estão incluídos em resposta:

-   *criado*: IntDate: indica quando esta versão do certificado foi criada.  
-   *atualizado*: IntDate: indica quando esta versão do certificado foi atualizada.  
-   *exp*: IntDate: contém o valor da data de validade do certificado x509.  
-   *nbf*: IntDate: contém o valor da data do certificado x509.  

> [!Note] 
> Se um certificado de cofre expirar, é a chave endereçada e o segredo torna-se inoperável.  

#### <a name="tags"></a>Etiquetas

 O dicionário especificado pelo cliente de pares de valor-chave, semelhante a etiquetas em chaves e segredos.  

 > [!Note]
> As etiquetas são legíveis por um chamador se tiverem a *lista* ou *obterem* permissão para esse tipo de objeto (chaves, segredos ou certificados).

### <a name="certificate-policy"></a>Política de certificados

Uma política de certificados contém informações sobre como criar e gerir o ciclo de vida de um certificado key vault. Quando um certificado com chave privada é importado para o cofre chave, uma política padrão é criada através da leitura do certificado x509.  

Quando um certificado key vault é criado de raiz, uma apólice precisa de ser fornecida. A política especifica como criar esta versão de certificado Key Vault, ou a próxima versão do certificado Key Vault. Uma vez estabelecida uma política, não é necessária com sucessivas operações de criação para futuras versões. Só há um exemplo de uma apólice para todas as versões de um certificado key vault.  

A um nível elevado, uma política de certificados contém as seguintes informações:  

-   Propriedades do certificado X509: Contém nome sujeito, nomes alternativos sujeitos e outras propriedades usadas para criar um pedido de certificado x509.  
-   Propriedades-chave: contém o tipo de chave, o comprimento da chave, os campos-chave exportáveis e reutilizar os campos-chave. Estes campos instruem o cofre chave sobre como gerar uma chave.  
-   Propriedades secretas: contém propriedades secretas, tais como o tipo de conteúdo de segredo endereçado para gerar o valor secreto, para recuperar certificado como segredo.  
-   Ações vitalícias: contém ações vitalícias para o Certificado KV. Cada ação vitalícia contém:  

     - Gatilho: especificado através de dias antes da expiração ou percentagem de tempo de vida  

     - Ação: especificar tipo de ação – *emailContactos ou* *renovar automaticamente*  

-   Emitente: Parâmetros sobre o emitente do certificado a utilizar para emitir certificados x509.  
-   Atributos políticos: contém atributos associados à política  

#### <a name="x509-to-key-vault-usage-mapping"></a>Mapeamento de utilização do Cofre-Chave X509 para Key Vault

O quadro seguinte representa o mapeamento da política de utilização chave x509 para operações-chave eficazes de uma chave criada como parte de uma criação de certificado key vault.

|**Bandeiras de utilização da chave X509**|**Operações chave do cofre chave**|**Comportamento predefinido**|
|----------|--------|--------|
|DataEncipherment|encriptar, desencriptar| N/D |
|Decifração|desencriptação| N/D  |
|Assinatura Digital|assinar, verificar| Padrão do cofre chave sem uma especificação de utilização no tempo de criação do certificado | 
|EncipherOnly|encrypt| N/D |
|KeyCertSign|assinar, verificar|N/D|
|KeyEncipherment|wrapKey, desembrulharKey| Padrão do cofre chave sem uma especificação de utilização no tempo de criação do certificado | 
|Não Repudiação|assinar, verificar| N/D |
|crlsign|assinar, verificar| N/D |

### <a name="certificate-issuer"></a>Emitente de certificado

Um objeto de certificado Key Vault contém uma configuração utilizada para comunicar com um fornecedor de emitente de certificado selecionado para encomendar certificados x509.  

-   Parceiros Key Vault com seguintes fornecedores de emitentes de certificados para certificados TLS/SSL

|**Nome do fornecedor**|**Localizações**|
|----------|--------|
|DigiCert|Apoiado em todos os principais locais de serviço de cofres em nuvem pública e Governo Azure|
|GlobalSign|Apoiado em todos os principais locais de serviço de cofres em nuvem pública e Governo Azure|

Antes que um emitente de certificado possa ser criado num Cofre chave, seguindo os passos pré-requisitos 1 e 2 deve ser realizado com sucesso.  

1. Fornecedores de Abordo da Autoridade de Certificados (CA)  

    -   Um administrador da organização deve embarcar na sua empresa (ex. Contoso) com pelo menos um fornecedor de CA.  

2. Administrador cria credenciais de solicitadores para key vault para inscrever (e renovar) certificados TLS/SSL  

    -   Fornece a configuração a ser usada para criar um objeto emitente do fornecedor no cofre chave  

Para obter mais informações sobre a criação de objetos Emitentes a partir do portal certificates, consulte o [blog Key Vault Certificates](https://aka.ms/kvcertsblog)  

O Key Vault permite a criação de múltiplos objetos emitentes com configuração diferente do fornecedor de emitentes. Uma vez criado um objeto emitente, o seu nome pode ser referenciado em uma ou múltiplas políticas de certificado. A referência ao objeto emitente instrui o Key Vault a utilizar a configuração conforme especificado no objeto emitente ao solicitar o certificado x509 ao fornecedor de CA durante a criação e renovação do certificado.  

Os objetos emitentes são criados no cofre e só podem ser usados com certificados KV no mesmo cofre.  

### <a name="certificate-contacts"></a>Contactos de certificados

Os contactos do certificado contêm informações de contacto para enviar notificações desencadeadas por eventos de vida de certificado. A informação de contactos é partilhada por todos os certificados do cofre chave. Uma notificação é enviada para todos os contactos especificados para um evento para qualquer certificado no cofre chave.  

Se a política de um certificado for definida para a renovação automática, então uma notificação é enviada nos seguintes eventos.  

- Antes da renovação do certificado
- Após a renovação do certificado, indicando se o certificado foi renovado com sucesso, ou se houve um erro, exigindo a renovação manual do certificado.  

  Quando uma política de certificadoque está definida para ser renovada manualmente (apenas por e-mail), uma notificação é enviada quando é hora de renovar o certificado.  

### <a name="certificate-access-control"></a>Controlo de Acesso ao Certificado

 O controlo de acesso aos certificados é gerido pela Key Vault, e é fornecido pelo Cofre chave que contém esses certificados. A política de controlo de acesso para certificados é distinta das políticas de controlo de acesso para chaves e segredos no mesmo Cofre chave. Os utilizadores podem criar um ou mais cofres para deter certificados, para manter o cenário de segmentação e gestão adequada dos certificados.  

 As seguintes permissões podem ser usadas, por principal, nos segredos acesso à entrada de controlo de acesso num cofre chave, e espelham de perto as operações permitidas num objeto secreto:  

- Permissões para operações de gestão de certificados
  - *obter*: Obtenha a versão de certificado atual, ou qualquer versão de um certificado 
  - *lista*: Enumerar os certificados em vigor ou versões de um certificado  
  - *atualização*: Atualizar um certificado
  - *criar*: Criar um certificado de cofre chave
  - *importação*: Material de certificado de importação num certificado key vault
  - *eliminar*um certificado, a sua política e todas as suas versões  
  - *recuperar*: Recuperar um certificado eliminado
  - *backup*: Backup um certificado em um cofre chave
  - *restaurar*: Restaurar um certificado de back-up para um cofre chave
  - *contactos de gestão*: Gerir os contactos de certificado saqueado chave  
  - *managingem emitentes*: Gerir as autoridades/emitentes do cofre chave
  - *getemememem*: Obter autoridades/emitentes de certificado
  - *enumeradores*: Lista das autoridades/emitentes de um certificado  
  - *setemedrs*: Criar ou atualizar as autoridades/emitentes do certificado Key Vault  
  - *eliminar emitentes*: Eliminar as autoridades/emitentes do certificado Key Vault  
 
- Permissões para operações privilegiadas
  - *Purga*: Purga (excluir permanentemente) um certificado eliminado

Para mais informações, consulte as operações do [Certificado na referência aAPI do Cofre Chave](/rest/api/keyvault). Para obter informações sobre o estabelecimento de permissões, consulte [Cofres - Criar ou Atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres - Atualizar a Política](/rest/api/keyvault/vaults/updateaccesspolicy)de Acesso .

## <a name="azure-storage-account-key-management"></a>Gestão da chave da conta de armazenamento Azure

O Cofre chave pode gerir as chaves da conta de armazenamento Azure:

- Internamente, o Key Vault pode listar chaves (sincronizadas) com uma conta de armazenamento Azure. 
- O cofre-chave regenera (gira) as teclas periodicamente.
- Os valores-chave nunca são devolvidos em resposta ao chamador.
- Key Vault gere chaves de ambas as contas de armazenamento e contas de armazenamento clássicas.

Para mais informações, consulte as chaves da conta de armazenamento do [cofre de chaves Azure](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>Controlo de acesso à conta de armazenamento

As seguintes permissões podem ser utilizadas ao autorizar um utilizador ou um responsável de aplicação a efetuar operações numa conta de armazenamento gerida:  

- Permissões para contas de armazenamento geridas e operações de definição SaS
  - *obter*: Obtém informações sobre uma conta de armazenamento 
  - *lista*: Lista de contas de armazenamento geridas por um Cofre chave
  - *atualização*: Atualizar uma conta de armazenamento
  - *eliminar*: Eliminar uma conta de armazenamento  
  - *recuperar*: Recuperar uma conta de armazenamento eliminada
  - *backup*: Backup uma conta de armazenamento
  - *restaurar*: Restaurar uma conta de armazenamento apoiada para um cofre chave
  - *conjunto*: Criar ou atualizar uma conta de armazenamento
  - *chave regenerativa:* Regenerar um valor-chave especificado para uma conta de armazenamento
  - *getsas*: Obtenha informações sobre uma definição SAS para uma conta de armazenamento
  - *listsas*: Lista de definições SAS de armazenamento para uma conta de armazenamento
  - *deletesas*: Eliminar uma definição SAS de uma conta de armazenamento
  - *conjuntos:* Criar ou atualizar uma nova definição/atributos SAS para uma conta de armazenamento

- Permissões para operações privilegiadas
  - *purga*: Purgar (excluir permanentemente) uma conta de armazenamento gerida

Para mais informações, consulte as operações da [conta de armazenamento na referência aadia](/rest/api/keyvault)do cofre de chaves . Para obter informações sobre o estabelecimento de permissões, consulte [Cofres - Criar ou Atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres - Atualizar a Política](/rest/api/keyvault/vaults/updateaccesspolicy)de Acesso .

## <a name="see-also"></a>Veja também

- [Autenticação, pedidos e respostas](authentication-requests-and-responses.md)
- [Guia do Programador do Cofre de Chaves](/azure/key-vault/key-vault-developers-guide)
