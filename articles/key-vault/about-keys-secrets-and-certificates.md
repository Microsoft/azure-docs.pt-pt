---
title: Sobre o Azure Key Vault chaves, segredos e certificados - Azure Key Vault
description: Descrição geral dos detalhes de interface e desenvolvedor de REST do Azure Key Vault para chaves, segredos e certificados.
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 01d9f763983da2415aba0f9bae81414017bc2f02
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842571"
---
# <a name="about-keys-secrets-and-certificates"></a>Sobre chaves, segredos e certificados

O Azure Key Vault permite aos utilizadores armazenar e utilizar vários tipos de dados de chave/segredo e de aplicações do Microsoft Azure:

- Chaves criptográficas: Suporta vários tipos de chave e algoritmos e permite a utilização de módulos de segurança de Hardware (HSM) para as chaves de valor elevado. 
- Segredos: Fornece armazenamento seguro de segredos, como palavras-passe e cadeias de ligação de base de dados.
- Certificados: Oferece suporte a certificados, que são criados sobre chaves e segredos e adicionar uma funcionalidade de renovação automática.
- Armazenamento do Azure: Pode gerir as chaves de uma conta de armazenamento do Azure por si. Internamente, o Key Vault pode listar chaves (sincronização) com uma conta de armazenamento do Azure e voltar a gerar (girar) periodicamente as chaves. 

Para obter mais informações sobre o Key Vault, consulte [o que é o Azure Key Vault?](/azure/key-vault/key-vault-whatis)

## <a name="azure-key-vault"></a>Azure Key Vault

As seções a seguir oferecem informações gerais aplicável em toda a implementação do serviço Cofre de chaves.

### <a name="supporting-standards"></a>Oferecer suporte aos padrões

O JavaScript Object Notation (JSON) e especificações de assinatura de objeto de JavaScript e a encriptação (JOSE) são informações gerais importantes.  

-   [Chave do JSON Web (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Encriptação de Web JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algoritmos de Web JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Assinatura de Web JSON (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="data-types"></a>Tipos de dados

Consulte as especificações de JOSE para tipos de dados relevantes para chaves de encriptação e assinatura.  

-   **algoritmo** -um algoritmo suportado para uma operação de chave, por exemplo, RSA1_5  
-   **valor de texto cifrado** -cipher octetos de texto, codificados usando Base64URL  
-   **valor de texto implícita** -a saída de um algoritmo de hash, codificado usando Base64URL  
-   **tipo de chave** -um dos tipos de chave suportados, por exemplo RSA (Rivest-Shamir-Adleman).  
-   **valor de texto não criptografado** -octetos de texto sem formatação, codificados usando Base64URL  
-   **valor de assinatura** - saída de um algoritmo de assinatura, codificado usando Base64URL  
-   **base64URL** -um Base64URL [RFC4648] codificado valor binário  
-   **Booleano** -true ou false  
-   **Identidade** – um identidade do Azure Active Directory (AAD).  
-   **IntDate** – um valor decimal de JSON que representa o número de segundos de 1970-01-01T0:0:0Z UTC até a data/hora especificada UTC. Ver RFC3339 para obter detalhes sobre date/times, em geral e a UTC em particular.  

### <a name="objects-identifiers-and-versioning"></a>Objetos, identificadores e controle de versão

Objetos armazenados no Cofre de chaves têm versões sempre que uma nova instância de um objeto é criada. Cada versão é atribuído um identificador exclusivo e um URL. Quando um objeto é criado pela primeira vez, é fornecido um identificador de versão exclusivo e marcada como a versão atual do objeto. Criação de uma nova instância com o mesmo nome de objeto dá-o novo objeto de um identificador de versão exclusivo, fazendo com que ele se tornar a versão atual.  

Objetos no Key Vault podem ser resolvidos utilizando o identificador atual ou um identificador de versão específico. Por exemplo, com uma chave com o nome `MasterKey`, realizar operações com o identificador atual faz com que o sistema para utilizar a versão mais recente disponível. Realizar operações com o identificador de versão específico, faz com que o sistema para utilizar essa versão específica do objeto.  

Objetos são identificados exclusivamente no Key Vault através de um URL. Não existem dois objetos no sistema de tem o mesmo URL, independentemente da localização geográfica. O URL completo para um objeto é denominado o identificador de objeto. O URL consiste num prefixo que identifica o tipo de objeto do Key Vault, o utilizador forneceu o nome do objeto e uma versão de objeto. O nome do objeto é imutável e maiúsculas de minúsculas. Identificadores de que não incluem a versão de objeto são referidas como identificadores de Base.  

Para obter mais informações, consulte [autenticação, os pedidos e respostas](authentication-requests-and-responses.md)

Um identificador de objeto tem o seguinte formato geral:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Em que:  

|||  
|-|-|  
|`keyvault-name`|O nome para um cofre de chaves no serviço do Microsoft Azure Key Vault.<br /><br /> Nomes de Cofre de chaves são selecionados pelo usuário e são globalmente exclusivos.<br /><br /> Nome do Key Vault tem de ser uma cadeia de caracteres de 3 a 24, que contém apenas 0-9, a-z, A-Z e -.|  
|`object-type`|O tipo de objeto, "chaves" ou "segredos".|  
|`object-name`|Um `object-name` é um nome de utilizador fornecido para e tem de ser exclusivo dentro de um cofre de chaves. O nome tem de ser uma cadeia de caracteres de 1-127, que contém apenas 0-9, a-z, A-Z e -.|  
|`object-version`|Um `object-version` é um gerados pelo sistema, 32 identificador de cadeia de caracteres que, opcionalmente, é usado para tratar de uma versão exclusiva de um objeto.|  

## <a name="key-vault-keys"></a>Chaves do Cofre de chaves

### <a name="keys-and-key-types"></a>As chaves e os tipos de chaves

As chaves criptográficas no Cofre de chaves são representadas como objetos de chave do JSON Web [JWK]. As especificações de JWK/JWA bases também são expandidas para permitir que os tipos de chave de exclusivos para a implementação de Cofre de chaves. Por exemplo, a importar chaves com o empacotamento de específicas do fornecedor do HSM, possibilita o transporte seguro de chaves que só podem ser utilizadas nos HSMs do Cofre de chave.  

- **As chaves "Soft"**: Uma chave processados no software pelo Key Vault, mas é encriptada em descanso ao utilizar uma chave de sistema que está num HSM. Os clientes podem importar uma chave existente (curva elíptica) RSA ou EC ou solicitar que o Cofre de chaves gerar um.
- **Chaves de "Disco rígidas"**: Uma chave processada num HSM (módulo de Hardware de segurança). Estas chaves são protegidas em um dos mundos de segurança do Key Vault HSM (há um universo de segurança por geografia para manter o isolamento). Os clientes podem importar uma chave RSA ou EC, de forma suave ou ao exportar a partir de um dispositivo compatível do HSM. Os clientes também podem solicitar o Key Vault para gerar uma chave. Este tipo de chave adiciona o atributo de T para o JWK obter para transportar o material de chave HSM.

     Para obter mais informações sobre dos limites geográficos, consulte [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

Key Vault suporta chaves RSA e criptografia de curva elíptica. 

-   **EC**: "Soft" chave de criptografia de curva elíptica.
-   **EC-HSM**: "Fixas" chave de criptografia de curva elíptica.
-   **RSA**: Chave RSA "Soft".
-   **RSA-HSM**: Chave RSA "Fixas".

Key Vault suporta chaves RSA de 2048, 3072 e 4096 de tamanhos. Key Vault oferece suporte a criptografia de curva elíptica chave tipos p-256, p-384, p-521 e P-256_K (SECP256K1).

### <a name="cryptographic-protection"></a>Proteção criptográfica

Os módulos criptográficos que utiliza o Cofre de chaves, se o HSM ou software, são validados FIPS (Federal Information Processing Standards). Não precisa de fazer nada de especial para executar no modo FIPS. Chaves **criado** ou **importados** como estão protegidas por HSM processado dentro de um HSM, validado pela norma FIPS 140-2 nível 2. Chaves **criado** ou **importados** como protegida por software, são processados no interior de módulos criptográficos validados FIPS 140-2 de nível 1. Para obter mais informações, consulte [chaves e os tipos de chave](#keys-and-key-types).

###  <a name="ec-algorithms"></a>Algoritmos EC
 Os identificadores de algoritmo seguintes são suportados com chaves EC e EC HSM no Cofre de chaves. 

#### <a name="curve-types"></a>Tipos de curva

-   **P-256** -a curva do NIST p-256, definida no [DSS FIPS PUB 186 4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256_K** -a curva de seg o SECP256K1, definido no [seg 2: Recomendado parâmetros de domínio de curva elíptica](https://www.secg.org/sec2-v2.pdf).
-   **P-384** -a curva do NIST p-384, definida no [DSS FIPS PUB 186 4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** -a curva do NIST p-521, definida no [DSS FIPS PUB 186 4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>VERIFICAR/INÍCIO DE SESSÃO

-   **ES256** - digests ECDSA para SHA-256 e chaves criadas com a curva p-256. Esse algoritmo é descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** - digests ECDSA para SHA-256 e chaves criadas com curva P-256_K. Esse algoritmo está pendente de padronização.
-   **ES384** - digests ECDSA para SHA-384 e chaves criadas com a curva p-384. Esse algoritmo é descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** - digests ECDSA para SHA-512 e chaves criadas com a curva p-521. Esse algoritmo é descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>Algoritmos RSA  
 Os identificadores de algoritmo seguintes são suportados com chaves RSA e RSA HSM no Cofre de chaves.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ENCRIPTAÇÃO/DESENCRIPTAÇÃO.

-   **RSA1_5** -encriptação de chave RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA OAEP** - RSAES usando ideal assimétrica encriptação de preenchimento (OAEP) [RFC3447], com os parâmetros de padrão especificados pelo RFC 3447 na secção A.2.1. Esses parâmetros de predefinição estão a utilizar uma função de hash de SHA-1 e uma função de geração de máscara de MGF1 com SHA-1.  

#### <a name="signverify"></a>VERIFICAR/INÍCIO DE SESSÃO

-   **RS256** - RSASSA-PKCS-v1_5 com SHA-256. O valor de texto implícita de aplicação fornecido deve ser calculado utilizando SHA-256 e tem de ser 32 bytes de comprimento.  
-   **RS384** - RSASSA-PKCS-v1_5, SHA-384 a utilizar. O valor de texto implícita de aplicação fornecido deve ser calculado utilizando SHA-384 e tem de ser 48 bytes de comprimento.  
-   **RS512** - RSASSA-PKCS-v1_5 usando SHA-512. O valor de texto implícita de aplicação fornecido deve ser calculado utilizando SHA-512 e tem de ser 64 bytes de comprimento.  
-   **RSNULL** -veja [RFC2437], um caso de utilização especializado para permitir que determinados cenários TLS.  

###  <a name="key-operations"></a>Operações de chaves

Key Vault suporta as seguintes operações nos objetos da chave:  

-   **Criar**: Permite que um cliente criar uma chave no Cofre de chaves. O valor da chave é gerado pelo Cofre de chaves e armazenado e não é liberada para o cliente. As chaves assimétricas podem ser criadas no Cofre de chaves.  
-   **Importar**: Permite que um cliente importar uma chave existente para o Cofre de chaves. As chaves assimétricas podem ser importadas para o Key Vault com uma série de métodos de empacotamento diferentes dentro de uma construção JWK. 
-   **Atualização**: Permite que um cliente com permissões suficientes para modificar os metadados (atributos de chaves) associados uma chave anteriormente armazenada no Key Vault.  
-   **Delete**: Permite que um cliente com permissões suficientes para eliminar uma chave do Key Vault.  
-   **Lista**: Permite que um cliente listar todas as chaves num cofre fornecido.  
-   **Lista de versões**: Permite que um cliente listar todas as versões de uma determinada chave num determinado Key Vault.  
-   **Get**: Permite que um cliente obter as partes públicas de uma determinada chave no Cofre de chaves.  
-   **Cópia de segurança**: Exporta uma chave num formato protegido.  
-   **Restaurar**: Importa uma chave anteriormente cópia de segurança.  

Para obter mais informações, consulte [operações na referência de API de REST do Cofre de chaves da chave](/rest/api/keyvault).  

Quando tiver sido criada uma chave no Cofre de chaves, as seguintes operações de criptografia podem ser realizadas usando a chave:  

-   **Inicie sessão e verificar**: Estritamente, esta operação é "hash de início de sessão" ou "verificar o hash,", pois o Key Vault não oferece suporte a hash de conteúdo como parte da criação de assinatura. Aplicativos devem discutir os dados sejam assinados localmente, em seguida, esse início de sessão do Key Vault o hash do pedido. Verificação de hashes assinados é suportada como uma operação de conveniência para as aplicações que pode não ter acesso ao material essencial [pública]. Para melhor desempenho de aplicações, certifique-se de que as operações são executadas localmente.  
-   **Encriptação de chave / encapsulamento**: Uma chave armazenada no Key Vault pode ser utilizada para proteger a outra chave, normalmente, uma chave de encriptação de conteúdo simétrica (CEK). Quando a chave no Cofre de chaves é assimétrica, criptografia de chave é utilizada. Por exemplo, RSA OAEP e as operações de WRAPKEY/UNWRAPKEY são equivalentes à encriptação/DESENCRIPTAÇÃO. Quando a chave no Cofre de chaves simétrica, chave de encapsulamento de aplicações é utilizada. Por exemplo, AES KW. A operação de WRAPKEY é suportada como uma conveniência para as aplicações que pode não ter acesso ao material essencial [pública]. Para melhor desempenho de aplicações, as operações de WRAPKEY devem ser efetuadas localmente.  
-   **Encriptar e desencriptar**: Uma chave armazenada no Key Vault pode ser utilizada para encriptar ou desencriptar um único bloco de dados. O tamanho do bloco é determinado pelo tipo de chave e algoritmo de encriptação selecionado. A operação de encriptação é fornecida para sua comodidade, para aplicações que pode não ter acesso ao material essencial [pública]. Para melhor desempenho de aplicações, encriptar as operações devem ser executadas localmente.  

Ao WRAPKEY/UNWRAPKEY usar as chaves assimétricas pareça supérflua (como a operação é equivalente a encriptação/DESENCRIPTAÇÃO), a utilização de operações distintas é importante. A distinção fornece a semântica e a separação de autorização destas operações e consistência quando outros tipos de chave são suportados pelo serviço.  

Cofre de chaves não suporta operações de exportação. Assim que uma chave é aprovisionada no sistema, ele não é possível extrair ou modificado de seu material de chave. No entanto, os utilizadores do Cofre de chaves podem exigir a chave para outros casos, tal como depois foi eliminado de utilização. Neste caso, eles podem utilizar as operações de cópia de segurança e RESTAURO para exportar/importar a chave num formato protegido. Criados pela operação de cópia de segurança de chaves não são utilizáveis fora do Cofre de chaves. Em alternativa, a operação de importação pode ser utilizada em várias instâncias do Key Vault.  

Os utilizadores podem restringir qualquer uma das operações criptográficas que suporte o Cofre de chaves numa base por chave usando a propriedade key_ops do objeto JWK.  

Para obter mais informações sobre objetos JWK, consulte [chave de Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="key-attributes"></a>Atributos principais

Para além do material de chave, podem ser especificados os seguintes atributos. Num pedido do JSON, a palavra-chave de atributos e chavetas, ' {' '}', são necessários, mesmo se não houver nenhum atributo especificado.  

- *ativada*: booleana, opcional, a predefinição é **true**. Especifica se a chave é ativado e pode ser utilizada para operações criptográficas. O *habilitado* atributo é utilizado em conjunto com *nbf* e *exp*. Quando uma operação ocorre entre *nbf* e *exp*, apenas irá ser permitida se *ativada* está definido como **verdadeiro**. Operações de fora a *nbf* / *exp* janela não são automaticamente permitidas, exceto para determinados tipos de operação em [condições particulares](#date-time-controlled-operations).
- *nbf*: Padrão de IntDate, opcional, é agora. O *nbf* (não antes) atributo identifica o tempo antes do qual a chave não pode ser utilizada para operações de criptografia, exceto para determinados tipos de operação sob [condições particulares](#date-time-controlled-operations). O processamento do *nbf* atributo requer que a data/hora atual tem de ser posterior ou igual a não-antes de data/hora listada na *nbf* atributo. Cofre de chaves pode fornecer para alguns pequenos leeway, normalmente não mais do que alguns minutos, para levar em conta relógio skew. O valor tem de ser um número que contém um valor de IntDate.  
- *exp*: Padrão de IntDate, opcional, é "sempre". O *exp* atributo (hora de expiração) identifica a hora de expiração em ou depois que a chave não deve ser utilizado para operação criptográfica, exceto para determinados tipos de operação em [particulares condições](#date-time-controlled-operations). O processamento do *exp* atributo requer que a data/hora atual tem de ser antes da data/hora de expiração listados na *exp* atributo. Key Vault pode fornecer para alguns pequenos leeway, normalmente, não mais do que alguns minutos, para levar em conta relógio skew. O valor tem de ser um número que contém um valor de IntDate.  

Existem atributos adicionais de só de leitura que estão incluídos em qualquer resposta que inclui os principais atributos:  

- *created*: IntDate, opcional. O *criado* atributo indica que esta versão da chave foi criada. O valor é nulo para chaves criadas antes da adição deste atributo. O valor tem de ser um número que contém um valor de IntDate.  
- *updated*: IntDate, opcional. O *atualizado* atributo indica que a esta versão da chave foi atualizada. O valor é nulo para as chaves que foram atualizadas pela última vez antes da adição deste atributo. O valor tem de ser um número que contém um valor de IntDate.  

Para obter mais informações sobre IntDate e outros tipos de dados, consulte [tipos de dados](#data-types)  

#### <a name="date-time-controlled-operations"></a>Operações de controlado de data e hora

Não, mas válido e de fora a expirou chaves, o *nbf* / *exp* janela, funcionará para **desencriptar**, **anular a moldagem**e **verificar** operações (não retornará 403, proibido). A lógica para utilizar o estado não ainda válido é permitir que uma chave a serem testados antes do uso de produção. A lógica para utilizar o estado expirado é para permitir operações de recuperação nos dados que foi criadas quando a chave era válida. Além disso, pode desativar o acesso a uma chave através de políticas de Key Vault ou ao atualizar o *habilitado* atributo-chave para **falso**.

Para obter mais informações sobre os tipos de dados, consulte [tipos de dados](#data-types).

Para obter mais informações sobre outros atributos possíveis, consulte a [chave de Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="key-tags"></a>Principais etiquetas

Pode especificar os metadados de específicos de aplicativo adicionais na forma de etiquetas. Key Vault suporta até 15 etiquetas, cada um dos quais pode ter um nome de 256 carateres e um valor de 256 carateres.  

>[!Note]
>As etiquetas são legíveis por um chamador se tiverem o *lista* ou *obter* permissão para esse tipo de objeto (chaves, segredos ou certificados).

###  <a name="key-access-control"></a>Key access control (Controlo de acesso a chaves)

Controlo de acesso para as chaves geridas pelo Cofre de chaves é fornecido no nível de um cofre de chaves que age como o contentor de chaves. A política de controlo de acesso para chaves, é diferente da política de controlo de acesso para segredos no Cofre de chaves do mesmo. Os utilizadores podem criar cofres de um ou mais para armazenar chaves e são necessários para manter uma segmentação de apropriado de cenário e a gestão de chaves. Controlo de acesso para chaves é independente do controlo de acesso para segredos.  

As seguintes permissões podem ser concedidas, num por utilizador / serviço principal base, em que a entrada de controle de acesso de chaves no cofre. Estas permissões espelham de perto as operações permitidas num objeto de chave:  

- Permissões para operações de gestão de chaves
  - *get*: Ler a parte pública de uma chave, juntamente com seus atributos
  - *list*: Listar as chaves ou versões de uma chave armazenada num cofre de chaves
  - *update*: Atualize os atributos de uma chave
  - *Criar*: Criar chaves novas
  - *import*: Importar uma chave para um cofre de chaves
  - *delete*: Eliminar o objeto de chave
  - *recover*: Recuperar uma chave eliminada
  - *backup*: Fazer cópias de segurança de uma chave no Cofre de chaves
  - *restore*: Restaurar uma cópia de segurança da chave para um cofre de chaves

- Permissões para operações de criptografia
  - *decrypt*: Utilizar a chave para desproteger uma seqüência de bytes
  - *encrypt*: Utilizar a chave para proteger uma seqüência arbitrária de bytes
  - *unwrapKey*: Utilizar a chave para desproteger chaves simétricas encapsuladas
  - *wrapKey*: Utilizar a chave para proteger uma chave simétrica
  - *verify*: Utilizar a chave para verificar os resumos de  
  - *sign*: Utilize a tecla para iniciar sessão resumos de
    
- Permissões para operações privilegiadas
  - *remover*: Remover (eliminar definitivamente) uma chave eliminada

Para obter mais informações sobre como trabalhar com chaves, consulte [operações na referência de API de REST do Cofre de chaves da chave](/rest/api/keyvault). Para obter informações sobre a estabelecer permissões, consulte [cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [cofres - política de acesso de atualização](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="key-vault-secrets"></a>Segredos do Key Vault 

### <a name="working-with-secrets"></a>Trabalhar com segredos

Da perspectiva do desenvolvedor, as APIs do Cofre de chave de aceitar e retornar valores secretos como cadeias de caracteres. Internamente, o Cofre de chaves armazena e gerencia segredos como seqüências de octetos (bytes de 8 bits), com um tamanho máximo de 25 mil bytes, cada. O serviço de Key Vault não fornece a semântica para segredos. Ele simplesmente aceita os dados, encripta-, armazena-a e retorna um identificador secreto ("id"). O identificador pode ser usado para recuperar o segredo num momento posterior.  

Para dados altamente confidenciais, os clientes devem considerar camadas adicionais de proteção de dados. Encriptar os dados com uma chave de proteção separados antes de armazenamento no Key Vault é um exemplo.  

Key Vault também oferece suporte a um campo contentType para segredos. Os clientes podem especificar o tipo de conteúdo de um segredo para ajudar a interpretar os dados secretos quando ela é recuperada. O comprimento máximo deste campo é 255 carateres. Não há nenhum valor predefinido. A utilização de sugerida é como uma sugestão para interpretar os dados secretos. Por exemplo, uma implementação pode armazenar palavras-passe e certificados como segredos, em seguida, utilize este campo para diferenciar. Não há nenhum valor predefinido.  

### <a name="secret-attributes"></a>Atributos secretos

Além dos dados secretos, podem ser especificados os seguintes atributos:  

- *exp*: A predefinição de IntDate, opcional, é **Eternamente**. O *exp* atributo (hora de expiração) identifica a hora de expiração em ou depois que os dados secretos devem não ser recuperados, exceto nas [situações específicas](#date-time-controlled-operations). Este campo é para **informativa** fins apenas que ela informa aos usuários do serviço do Cofre de chaves que não pode ser utilizado um segredo específico. O valor tem de ser um número que contém um valor de IntDate.   
- *nbf*: A predefinição de IntDate, opcional, é **agora**. O *nbf* (não antes) atributo identifica o tempo antes do qual os dados secretos não devem ser obtidos, exceto nas [situações específicas](#date-time-controlled-operations). Este campo é para **informativa** apenas a fins. O valor tem de ser um número que contém um valor de IntDate. 
- *ativada*: booleana, opcional, a predefinição é **true**. Esse atributo Especifica se é possível obter os dados secretos. O atributo habilitado é utilizado em conjunto com *nbf* e *exp* quando uma operação ocorre entre *nbf* e *exp*, ele só será permitido se ativada é definido como **true**. Operações de fora a *nbf* e *exp* janela são automaticamente não permitido, exceto na [situações específicas](#date-time-controlled-operations).  

Existem atributos adicionais de só de leitura que estão incluídos em qualquer resposta que inclui atributos secretos:  

- *created*: IntDate, opcional. O atributo criado indica que esta versão do segredo do foi criada. Este valor é nulo para segredos criados antes da adição deste atributo. O valor tem de ser um número que contém um valor de IntDate.  
- *updated*: IntDate, opcional. O atributo atualizado indica que a esta versão do segredo do foi atualizada. Este valor é nulo para segredos que foram atualizados pela última vez antes da adição deste atributo. O valor tem de ser um número que contém um valor de IntDate.

#### <a name="date-time-controlled-operations"></a>Operações de controlado de data e hora

Um segredo **Obtenha** operação funcionará para segredos não ainda válido e expirados, fora a *nbf* / *exp* janela. Chamar um segredo **obter** operação, para um segredo não ainda válido, pode ser utilizada para fins de teste. A obter (**obter**ting) um segredo expirado, pode ser utilizado para operações de recuperação.

Para obter mais informações sobre os tipos de dados, consulte [tipos de dados](#data-types).  

### <a name="secret-access-control"></a>Secret access control (Controlo de acesso a segredos)

Controlo de acesso para segredos geridos no Cofre de chaves, é fornecido no nível do Cofre de chaves que contenha esses segredos. A política de controlo de acesso para segredos, é diferente da política de controlo de acesso para as chaves no Cofre de chaves do mesmo. Os utilizadores podem criar cofres de um ou mais para armazenar segredos e são necessários para manter uma segmentação de apropriado de cenário e a gestão de segredos.   

As seguintes permissões podem ser utilizadas, numa base por principal, na entrada de controle de acesso a segredos no cofre e espelham de perto as operações permitidas num objeto secreto:  

- Permissões para operações de gestão de segredos
  - *get*: Ler um segredo  
  - *list*: Listar os segredos ou versões de um segredo armazenados num cofre de chave  
  - *set*: Criar um segredo  
  - *delete*: Eliminar um segredo  
  - *recover*: Recuperar um segredo eliminado
  - *backup*: Criar cópias de segurança um segredo no Cofre de chaves
  - *restore*: Restaurar uma cópia de segurança segredo para um cofre de chaves

- Permissões para operações privilegiadas
  - *remover*: Remover (eliminar definitivamente) um segredo eliminado

Para obter mais informações sobre como trabalhar com segredos, consulte [operações de segredo na referência de API de REST do Cofre de chaves](/rest/api/keyvault). Para obter informações sobre a estabelecer permissões, consulte [cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [cofres - política de acesso de atualização](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Etiquetas secretas  
Pode especificar os metadados de específicos de aplicativo adicionais na forma de etiquetas. Key Vault suporta até 15 etiquetas, cada um dos quais pode ter um nome de 256 carateres e um valor de 256 carateres.  

>[!Note]
>As etiquetas são legíveis por um chamador se tiverem o *lista* ou *obter* permissão para esse tipo de objeto (chaves, segredos ou certificados).

## <a name="key-vault-certificates"></a>Certificados de Cofre de chaves

Suporte de certificados do Key Vault fornece para gestão do seu x509 certificados e os comportamentos seguintes:  

-   Permite que um proprietário de certificado criar um certificado por meio de um processo de criação do Cofre de chaves ou a importação de um certificado existente. Inclui ambos autoassinados e certificados gerados de autoridade de certificação.
-   Permite que um proprietário de certificado do Key Vault implementar o armazenamento seguro e gestão de X509 certificados, sem interação com o material de chave privada.  
-   Permite que um proprietário de certificado criar uma política que direciona o Key Vault para gerir o ciclo de vida de um certificado.  
-   Permite que os proprietários de certificado fornecer informações de contato para notificações sobre eventos de ciclo de vida de expiração e renovação de certificado.  
-   Suporta a renovação automática com emissores selecionados - Key Vault parceiro X509 fornecedores de certificado / autoridades de certificado.

>[!Note]
>Fornecedores/autoridades de uma parceria não também são permitidas mas não suporta a funcionalidade de renovação automática.

### <a name="composition-of-a-certificate"></a>Composição de um certificado

Quando é criado um certificado do Key Vault, uma chave endereçável e o segredo também são criados com o mesmo nome. A chave do Key Vault permite operações-chave e o segredo do Key Vault permite a obtenção do valor do certificado como um segredo. Um certificado do Key Vault também contém x509 pública metadados de certificado.  

O identificador e a versão de certificados é semelhante de chaves e segredos. Uma versão específica de uma chave endereçável e o segredo criado com a versão de certificado do Key Vault está disponível na resposta de certificado do Key Vault.
 
![Os certificados são objetos complexos](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Chave exportável ou não exportável

Quando é criado um certificado do Key Vault, podem ser recuperada do segredo endereçável com a chave privada no formato PFX ou PEM. A política utilizada para criar o certificado tem de indicar que a chave é exportável. Se a diretiva não exportável, em seguida, a chave privada não é uma parte do valor quando recuperados como um segredo.  

A chave endereçável torna-se mais relevante com certificados de KV não exportável. Operações da chave KV endereçável estão mapeadas a partir *keyusage* campo da política de certificado KV utilizada para criar o certificado de KV.  

São suportados dois tipos de chave – *RSA* ou *RSA HSM* com certificados. Exportável só é permitida com RSA, não são suportada por RSA HSM.  

### <a name="certificate-attributes-and-tags"></a>Atributos de certificado e as etiquetas

Além de metadados do certificado, uma chave endereçável e o segredo endereçável, um certificado do Key Vault também contém atributos e as etiquetas.  

#### <a name="attributes"></a>Atributos

Os atributos de certificado são espelhados nos atributos dos endereçável chave e segredo criado quando o certificado de KV é criado.  

Um certificado do Key Vault tem os seguintes atributos:  

-   *ativada*: booleana, opcional, a predefinição é **true**. Pode ser especificado para indicar se os dados do certificado podem ser obtidos como operável como uma chave ou segredo. Também é utilizado em conjunto com *nbf* e *exp* quando uma operação ocorre entre *nbf* e *exp*e apenas serão permitidas se ativada está definido como true. Operações de fora a *nbf* e *exp* não janela são automaticamente permitidas.  

Existem atributos adicionais de só de leitura que estão incluídos na resposta:

-   *created*: IntDate: indica que esta versão do certificado foi criada.  
-   *updated*: IntDate: indica que a esta versão do certificado foi atualizada.  
-   *exp*: IntDate: contém o valor da data de expiração de x509 certificado.  
-   *nbf*: IntDate: contém o valor da data de x509 certificado.  

> [!Note] 
> Se um certificado do Key Vault expira, seja endereçável chave e segredo tornar-se inoperáveis.  

#### <a name="tags"></a>Etiquetas

 Cliente especificado dicionário de pares chave-valor, semelhantes às etiquetas em chaves e segredos.  

 > [!Note]
> As etiquetas são legíveis por um chamador se tiverem o *lista* ou *obter* permissão para esse tipo de objeto (chaves, segredos ou certificados).

### <a name="certificate-policy"></a>Política de certificado

Uma política de certificado contém informações sobre como criar e gerir o ciclo de vida de um certificado do Key Vault. Quando um certificado com chave privada é importado para o Cofre de chaves, é criada uma política de predefinição lendo o x509 certificado.  

Quando um certificado do Key Vault é criado a partir do zero, uma política tem de ser fornecido. A política especifica como criar esta versão de certificado do Key Vault ou a próxima versão de certificado do Key Vault. Quando uma política for estabelecida, não é necessário com sucessivas criar operações para versões futuras. Há apenas uma instância de uma política para todas as versões de um certificado do Key Vault.  

Num alto nível, uma política de certificado contém as seguintes informações:  

-   Propriedades de certificado X509: Contém o nome do requerente, os nomes alternativos do requerente e outras propriedades utilizadas para criar uma x509 pedido de certificado.  
-   Propriedades da chave: contém o tipo de chave exportável, comprimento da chave e reutilizar os campos de chave. Estes campos instruir sobre como gerar uma chave do Cofre de chaves.  
-   Propriedades do segredo: contém as propriedades do segredo, como o tipo de conteúdo de segredo endereçável para gerar o valor secreto, para obter o certificado como um segredo.  
-   Ações de tempo de vida: contém ações de tempo de vida para o certificado de KV. Cada ação de tempo de vida contém:  

     - Acionador: especificado por meio de dias antes da expiração ou a duração de percentagem de span  

     - Ação: especificar o tipo de ação – *emailContacts* ou *renovação automática*  

-   Emissor: Parâmetros sobre o emissor de certificado a utilizar para emitir x509 certificados.  
-   Atributos de política: contém atributos associados à política  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 para mapeamento de utilização do Cofre de chaves

A tabela seguinte representa o mapeamento do x509 de política de utilização de chave para operações de chaves em vigor a partir de uma chave criada como parte de uma criação de certificados do Key Vault.

|**X509 sinalizadores de utilização de chave**|**Operações de chave do Key Vault**|**Comportamento predefinido**|
|----------|--------|--------|
|DataEncipherment|encriptar, desencriptar| N/A |
|DecipherOnly|desencriptar| N/A  |
|Bits DigitalSignature|iniciar sessão, certifique-se| Padrão de Cofre de chaves sem uma especificação de utilização no momento de criação do certificado | 
|EncipherOnly|encrypt| N/A |
|KeyCertSign|iniciar sessão, certifique-se|N/A|
|KeyEncipherment|wrapKey, unwrapKey| Padrão de Cofre de chaves sem uma especificação de utilização no momento de criação do certificado | 
|NonRepudiation|iniciar sessão, certifique-se| N/A |
|crlsign|iniciar sessão, certifique-se| N/A |

### <a name="certificate-issuer"></a>Emissor do certificado

Um objeto de certificado do Key Vault mantém uma configuração utilizada para comunicar com um provedor de emissor do certificado selecionado para certificados x509 de ordem.  

-   Para parceiros do Key Vault com seguintes fornecedores de emissor de certificados para certificados SSL

|**Nome do fornecedor**|**Localizações**|
|----------|--------|
|DigiCert|Suportado em todas as localizações de serviço do Cofre de chaves na cloud pública e o Azure Government|
|GlobalSign|Suportado em todas as localizações de serviço do Cofre de chaves na cloud pública e o Azure Government|

Antes de um emissor do certificado pode ser criado no Cofre de chaves, passos de pré-requisitos seguintes 1 e 2 devem ser realizados com êxito.  

1. Carregar para fornecedores de autoridade de certificado  

    -   Um administrador da organização deve integrar a empresa (ex. Contoso) com, pelo menos, um fornecedor de AC.  

2. O administrador cria as credenciais de autor do pedido para o Key Vault inscrever (e renovar) certificados SSL  

    -   Fornece a configuração a ser utilizado para criar um objeto de emissor do fornecedor no Cofre de chaves  

Para obter mais informações sobre como criar objetos de emissor a partir do portal de certificados, consulte o [blog de certificados do Key Vault](https://aka.ms/kvcertsblog)  

Key Vault permite a criação de vários objetos de emissor com a configuração do fornecedor de emissor diferentes. Depois de criar um objeto de emissor, seu nome pode ser referenciado numa ou várias políticas de certificado. Referencia o objeto de emissor instrui o Key Vault para utilizar a configuração conforme especificado no objeto de emissor ao pedir o x509 certificado do fornecedor de AC durante a criação do certificado e a renovação.  

Objetos de emissor são criados no cofre e só podem ser utilizados com certificados de KV no mesmo cofre.  

### <a name="certificate-contacts"></a>Contactos do certificado

Contactos do certificado contêm informações de contacto para enviar notificações acionadas por eventos de ciclo de vida do certificado. As informações de contatos são partilhadas por todos os certificados no Cofre de chaves. É enviada uma notificação para todos os contatos especificados para um evento para qualquer certificado no Cofre de chaves.  

Se a política de um certificado estiver definida para renovação automática, em seguida, é enviada uma notificação sobre os eventos seguintes.  

- Antes de renovação de certificado
- Após a renovação de certificado, que indica se o certificado foi renovado com êxito ou se tiver ocorrido um erro, que requerem a renovação manual do certificado.  

  Quando uma política de certificado que está configurada para ser manualmente renovada (apenas para e-mail), é enviada uma notificação quando está na altura de renovar o certificado.  

### <a name="certificate-access-control"></a>Controlo de acesso de certificado

 Controlo de acesso para certificados é gerenciado pelo Cofre de chaves e é fornecido pelo Cofre de chave que contêm esses certificados. A política de controlo de acesso para certificados é diferente do que as políticas de controlo de acesso de chaves e segredos no Cofre de chaves do mesmo. Os utilizadores podem criar um ou mais cofres para armazenar certificados, para manter a segmentação apropriado do cenário e a gestão de certificados.  

 As seguintes permissões podem ser utilizadas numa base por principal, na entrada de controle de acesso a segredos num cofre de chaves e estreitamente espelhos as operações permitidas num objeto secreto:  

- Permissões para operações de gestão de certificado
  - *get*: Obter a versão atual do certificado ou qualquer versão de um certificado 
  - *list*: Listar os certificados atuais ou versões de um certificado  
  - *update*: Atualizar um certificado
  - *Criar*: Criar um certificado do Key Vault
  - *import*: Importar o material de certificado para um certificado do Key Vault
  - *delete*: Eliminar um certificado, a política e todas as suas versões  
  - *recover*: Recuperar um certificado eliminado
  - *backup*: Fazer cópias de segurança de um certificado num cofre de chaves
  - *restore*: Restaurar um certificado de uma cópia de segurança para um cofre de chaves
  - *managecontacts*: Gerir contactos de certificado do Key Vault  
  - *manageissuers*: Gerir o Cofre de chaves autoridades/emissores de certificados
  - *getissuers*: Obter autoridades/emissores um certificado
  - *listissuers*: Lista de autoridades/emissores de um certificado  
  - *setissuers*: Criar ou atualizar autoridades/emissores de um certificado Key Vault  
  - *deleteissuers*: Eliminar autoridades/emissores de um certificado Key Vault  
 
- Permissões para operações privilegiadas
  - *remover*: Remover (eliminar definitivamente) um certificado eliminado

Para obter mais informações, consulte a [operações na referência de API de REST do Cofre de chaves de certificado](/rest/api/keyvault). Para obter informações sobre a estabelecer permissões, consulte [cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [cofres - política de acesso de atualização](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Gestão de chaves do Azure Storage conta

Cofre de chaves pode gerir as chaves de conta de armazenamento do Azure:

- Internamente, o Key Vault pode listar as chaves de (sincronização) com uma conta de armazenamento do Azure. 
- Key Vault gera novamente (roda) as chaves periodicamente.
- Valores de chaves nunca são retornados em resposta ao chamador.
- Cofre de chaves faz a gestão de chaves de contas de armazenamento e contas de armazenamento clássicas.

Para obter mais informações, consulte [chaves de conta de armazenamento do Azure Key Vault](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>Controlo de acesso de conta de armazenamento

As seguintes permissões podem ser utilizadas ao autorizar um utilizador ou aplicação principal para efetuar operações numa conta de armazenamento gerido:  

- Permissões para a conta de armazenamento gerida e operações de definição de SaS
  - *get*: Obtém informações sobre uma conta de armazenamento 
  - *list*: Contas de armazenamento de lista geridas por um Key Vault
  - *update*: Atualizar uma conta de armazenamento
  - *delete*: Eliminar uma conta do Storage  
  - *recover*: Recuperar uma conta de armazenamento eliminada
  - *backup*: Criar uma conta de armazenamento de cópias de segurança
  - *restore*: Restaurar uma conta de armazenamento de cópia de segurança para um cofre de chaves
  - *set*: Criar ou atualizar uma conta de armazenamento
  - *regeneratekey*: Voltar a gerar um valor de chave especificado para uma conta de armazenamento
  - *getsas*: Obter informações sobre uma definição de SAS para uma conta de armazenamento
  - *listsas*: Definições de SAS de armazenamento de lista para uma conta de armazenamento
  - *deletesas*: Eliminar uma definição de SAS de uma conta de armazenamento
  - *setsas*: Criar ou atualizar um novo SAS definição/atributos para uma conta de armazenamento

- Permissões para operações privilegiadas
  - *remover*: Remover (eliminar definitivamente) uma conta de armazenamento gerido

Para obter mais informações, consulte a [operações de conta de armazenamento na referência de API de REST do Cofre de chaves](/rest/api/keyvault). Para obter informações sobre a estabelecer permissões, consulte [cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [cofres - política de acesso de atualização](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Consultar Também

- [Autenticação, os pedidos e respostas](authentication-requests-and-responses.md)
- [Versões do Key Vault](key-vault-versions.md)
- [Guia do Programador do Key Vault](/azure/key-vault/key-vault-developers-guide)
