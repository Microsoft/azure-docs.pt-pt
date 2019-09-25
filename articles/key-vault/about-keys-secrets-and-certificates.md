---
title: Sobre Azure Key Vault chaves, segredos e certificados-Azure Key Vault
description: Visão geral de Azure Key Vault interface REST e detalhes do desenvolvedor para chaves, segredos e certificados.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: cca4f794fd3f84b991c7882307f74bcfadf6835b
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71241060"
---
# <a name="about-keys-secrets-and-certificates"></a>Sobre chaves, segredos e certificados

Azure Key Vault permite que Microsoft Azure aplicativos e usuários armazenem e usem vários tipos de dados de segredo/chave:

- Chaves de criptografia: Dá suporte a vários tipos de chave e algoritmos e habilita o uso de HSM (módulos de segurança de hardware) para chaves de valor alto. 
- Confidenciais Fornece armazenamento seguro de segredos, como senhas e cadeias de conexão de banco de dados.
- Certificado Dá suporte a certificados, que são criados com base em chaves e segredos e adicionam um recurso de renovação automatizada.
- Armazenamento do Azure: Pode gerenciar chaves de uma conta de armazenamento do Azure para você. Internamente, Key Vault pode listar (sincronizar) chaves com uma conta de armazenamento do Azure e regenerar (girar) as chaves periodicamente. 

Para obter mais informações gerais sobre Key Vault, consulte [o que é Azure Key Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

As seções a seguir oferecem informações gerais aplicáveis em toda a implementação do serviço de Key Vault.

### <a name="supporting-standards"></a>Padrões de suporte

As especificações JavaScript Object Notation (JSON) e José (assinatura de objeto JavaScript e criptografia) são informações importantes sobre o plano de fundo.  

-   [Chave da Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Criptografia Web JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algoritmos Web JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Assinatura Web JSON (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="data-types"></a>Tipos de dados

Consulte as especificações de José para tipos de dados relevantes para chaves, criptografia e assinatura.  

-   **algoritmo** -um algoritmo com suporte para uma operação de chave, por exemplo, RSA1_5  
-   **valores de texto cifrado-valor** -Cipher, codificados usando Base64URL  
-   **Digest-Value** -a saída de um algoritmo de hash, codificada usando Base64URL  
-   **Key-Type** -um dos tipos de chave com suporte, por exemplo RSA (Rivest-Shamir-adlema).  
-   **valores de texto sem formatação –** octetos de texto não criptografado, codificados usando Base64URL  
-   **assinatura-valor** -saída de um algoritmo de assinatura, codificado usando Base64URL  
-   **base64URL** -um valor binário codificado BASE64URL [RFC4648]  
-   **booliano** -verdadeiro ou falso  
-   **Identidade** -uma identidade de Azure Active Directory (AAD).  
-   **IntDate** -um valor decimal JSON que representa o número de segundos de 1970-01-01T0:0: 0Z UTC até a data/hora UTC especificada. Consulte RFC3339 para obter detalhes sobre data/hora, em geral e UTC em particular.  

### <a name="objects-identifiers-and-versioning"></a>Objetos, identificadores e controle de versão

Os objetos armazenados em Key Vault têm controle de versão sempre que uma nova instância de um objeto é criada. Cada versão recebe um identificador e uma URL exclusivos. Quando um objeto é criado pela primeira vez, ele recebe um identificador de versão exclusivo e marcado como a versão atual do objeto. A criação de uma nova instância com o mesmo nome de objeto dá ao novo objeto um identificador de versão exclusivo, fazendo com que ele se torne a versão atual.  

Os objetos no Key Vault podem ser endereçados usando o identificador atual ou um identificador específico da versão. Por exemplo, dada uma chave com o nome `MasterKey`, executar operações com o identificador atual faz com que o sistema use a versão mais recente disponível. Executar operações com o identificador específico da versão faz com que o sistema Use essa versão específica do objeto.  

Os objetos são identificados exclusivamente dentro de Key Vault usando uma URL. Dois objetos no sistema têm a mesma URL, independentemente da localização geográfica. A URL completa para um objeto é chamada de identificador de objeto. A URL consiste em um prefixo que identifica o Key Vault, o tipo de objeto, o nome do objeto fornecido pelo usuário e uma versão do objeto. O nome do objeto não diferencia maiúsculas de minúsculas e é imutável. Os identificadores que não incluem a versão do objeto são chamados de identificadores base.  

Para obter mais informações, consulte [autenticação, solicitações e respostas](authentication-requests-and-responses.md)

Um identificador de objeto tem o seguinte formato geral:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Em que:  

|||  
|-|-|  
|`keyvault-name`|O nome de um cofre de chaves no serviço de Key Vault Microsoft Azure.<br /><br /> Os nomes de Key Vault são selecionados pelo usuário e são globalmente exclusivos.<br /><br /> Key Vault nome deve ser uma cadeia de caracteres 3-24, contendo apenas 0-9, a-z, A-Z e-.|  
|`object-type`|O tipo do objeto, "Keys" ou "segredos".|  
|`object-name`|Um `object-name` é um nome de usuário fornecido para e deve ser exclusivo dentro de um Key Vault. O nome deve ser uma cadeia de caracteres 1-127, contendo apenas 0-9, a-z, A-Z e-.|  
|`object-version`|Um `object-version` é um identificador de cadeia de caracteres 32, gerado pelo sistema, que é opcionalmente usado * o endereçar uma versão exclusiva de um objeto.|  

## <a name="key-vault-keys"></a>Chaves de Key Vault

### <a name="keys-and-key-types"></a>Chaves e tipos de chave

As chaves de criptografia no Key Vault são representadas como objetos de chave da Web JSON [JWK]. As especificações base de JWK/JWA também são estendidas para habilitar tipos de chave exclusivos para a implementação de Key Vault. Por exemplo, a importação de chaves usando o empacotamento específico de fornecedor do HSM permite o transporte seguro de chaves que podem ser usadas apenas em Key Vault HSMs.  

- **Chaves "soft"** : Uma chave processada no software por Key Vault, mas é criptografada em repouso usando uma chave do sistema que está em um HSM. Os clientes podem importar uma chave existente de RSA ou EC (curva elíptica) ou solicitar que Key Vault gerar uma.
- **Chaves "rígidas"** : Uma chave processada em um HSM (módulo de segurança de hardware). Essas chaves são protegidas em um dos mundos de segurança Key Vault HSM (há um mundo de segurança por geografia para manter o isolamento). Os clientes podem importar uma chave RSA ou EC, em forma flexível ou exportando de um dispositivo HSM compatível. Os clientes também podem solicitar Key Vault para gerar uma chave. Esse tipo de chave adiciona o atributo key_hsm ao JWK obter para transportar o material de chave HSM.

     Para obter mais informações sobre limites geográficos, consulte [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

Key Vault dá suporte apenas a chaves de curva elíptica e RSA. 

-   **EC**: Chave de curva elíptica "soft".
-   **EC-HSM**: Chave de curva elíptica "Hard".
-   **RSA**: Chave RSA "soft".
-   **RSA-HSM**: Chave RSA "Hard".

Key Vault dá suporte a chaves RSA de tamanhos 2048, 3072 e 4096. O Key Vault dá suporte a tipos de chave de curva elíptica P-256, P-384, P-521 e P-256K (SECP256K1).

### <a name="cryptographic-protection"></a>Proteção criptográfica

Os módulos de criptografia que o Key Vault usa, seja o HSM ou software, validados por FIPS (padrões de processamento de informações federais). Você não precisa fazer nada especial para executar no modo FIPS. As chaves **criadas** ou **importadas** como protegidas por HSM são processadas dentro de um HSM, validadas para o FIPS 140-2 nível 2. As chaves **criadas** ou **importadas** como protegidas por software são processadas dentro de módulos criptográficos validados para o FIPS 140-2 nível 1. Para obter mais informações, consulte [chaves e tipos de chave](#keys-and-key-types).

###  <a name="ec-algorithms"></a>Algoritmos do EC
 Os identificadores de algoritmo a seguir têm suporte com chaves EC e EC-HSM em Key Vault. 

#### <a name="curve-types"></a>Tipos de curva

-   **P-256** -a curva NIST p-256, definida no [DSS FIPS pub 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256K** -a SECP256K1 de curva da SEC, [definida na SEC 2: Parâmetros](https://www.secg.org/sec2-v2.pdf)de domínio de curva elíptica recomendados.
-   **P-384** -a curva NIST p-384, definida no [DSS FIPS pub 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** -a curva NIST p-521, definida no [DSS FIPS pub 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>ASSINAR/VERIFICAR

-   **ES256** -ECDSA para resumos e chaves do SHA-256 criados com a curva P-256. Esse algoritmo é descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** -ECDSA para resumos e chaves do SHA-256 criados com a curva P-256K. Esse algoritmo é a padronização pendente.
-   **ES384** -ECDSA para resumos e chaves do SHA-384 criados com a curva P-384. Esse algoritmo é descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** -ECDSA para resumos e chaves do SHA-512 criados com a curva P-521. Esse algoritmo é descrito em [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>Algoritmos RSA  
 Os seguintes identificadores de algoritmo têm suporte com as chaves RSA e RSA-HSM no Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, CRIPTOGRAFAR/DESCRIPTOGRAFAR

-   Criptografia de chave **RSA1_5** -RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP** -RSAES usando o OAEP (preenchimento de criptografia assimétrica ideal) [RFC3447], com os parâmetros padrão especificados pela RFC 3447 na seção A. 2.1. Esses parâmetros padrão estão usando uma função de hash do SHA-1 e uma função de geração de máscara de MGF1 com SHA-1.  

#### <a name="signverify"></a>ASSINAR/VERIFICAR

-   **RS256** -RSASSA-PKCS-V1_5 usando SHA-256. O valor de resumo fornecido pelo aplicativo deve ser computado usando SHA-256 e deve ter 32 bytes de comprimento.  
-   **RS384** -RSASSA-PKCS-V1_5 usando SHA-384. O valor de resumo fornecido pelo aplicativo deve ser computado usando SHA-384 e deve ter 48 bytes de comprimento.  
-   **RS512** -RSASSA-PKCS-V1_5 usando SHA-512. O valor de resumo fornecido pelo aplicativo deve ser computado usando SHA-512 e deve ter 64 bytes de comprimento.  
-   **RSNULL** -consulte [RFC2437], um caso de uso especializado para habilitar determinados cenários de TLS.  

###  <a name="key-operations"></a>Operações de chave

O Key Vault dá suporte às seguintes operações em objetos de chave:  

-   **Criar**: Permite que um cliente crie uma chave no Key Vault. O valor da chave é gerado por Key Vault e armazenado e não é liberado para o cliente. Chaves assimétricas podem ser criadas em Key Vault.  
-   **Importar**: Permite que um cliente importe uma chave existente para Key Vault. Chaves assimétricas podem ser importadas para Key Vault usando vários métodos de empacotamento diferentes dentro de uma construção JWK. 
-   **Atualização**: Permite que um cliente com permissões suficientes modifique os metadados (atributos de chave) associados a uma chave armazenada anteriormente no Key Vault.  
-   **Excluir**: Permite que um cliente com permissões suficientes para excluir uma chave de Key Vault.  
-   **Lista**: Permite que um cliente liste todas as chaves em um determinado Key Vault.  
-   **Listar versões**: Permite que um cliente liste todas as versões de uma determinada chave em determinado Key Vault.  
-   **Obter**: Permite que um cliente recupere as partes públicas de uma determinada chave em um Key Vault.  
-   **Backup**: Exporta uma chave em um formulário protegido.  
-   **Restaurar**: Importa uma chave de backup anterior.  

Para obter mais informações, consulte [operações de chave na referência da API REST do Key Vault](/rest/api/keyvault).  

Depois que uma chave tiver sido criada no Key Vault, as seguintes operações criptográficas poderão ser executadas usando a chave:  

-   **Assinar e verificar**: Estritamente, essa operação é "assinar hash" ou "verificar hash", pois Key Vault não dá suporte a hash de conteúdo como parte da criação da assinatura. Os aplicativos devem fazer o hash dos dados a serem assinados localmente e solicitar que Key Vault assinar o hash. Há suporte para a verificação de hashes assinados como uma operação de conveniência para aplicativos que podem não ter acesso ao material de chave [Public]. Para obter o melhor desempenho do aplicativo, verifique se as operações são executadas localmente.  
-   **Criptografia/encapsulamento de chave**: Uma chave armazenada em Key Vault pode ser usada para proteger outra chave, normalmente uma CEK (chave de criptografia de conteúdo simétrica). Quando a chave no Key Vault é assimétrica, a criptografia de chave é usada. Por exemplo, as operações RSA-OAEP e WRAPKEY/UNWRAPKEY são equivalentes a CRIPTOGRAFAr/descriptografar. Quando a chave no Key Vault é simétrica, o encapsulamento de chave é usado. Por exemplo, AES-KW. A operação WRAPKEY tem suporte como uma conveniência para aplicativos que podem não ter acesso ao material de chave [Public]. Para obter o melhor desempenho do aplicativo, as operações de WRAPKEY devem ser executadas localmente.  
-   **Criptografar e descriptografar**: Uma chave armazenada em Key Vault pode ser usada para criptografar ou descriptografar um único bloco de dados. O tamanho do bloco é determinado pelo tipo de chave e pelo algoritmo de criptografia selecionado. A operação Encrypt é fornecida para sua conveniência, para aplicativos que podem não ter acesso ao material da chave [Public]. Para obter o melhor desempenho do aplicativo, as operações de criptografia devem ser executadas localmente.  

Embora WRAPKEY/UNWRAPKEY usando chaves assimétricas possa parecer supérfluo (pois a operação é equivalente a CRIPTOGRAFAr/descriptografar), o uso de operações distintas é importante. A distinção fornece a separação semântica e de autorização dessas operações e a consistência quando outros tipos de chave são suportados pelo serviço.  

Key Vault não dá suporte a operações de exportação. Depois que uma chave é provisionada no sistema, ela não pode ser extraída ou seu material de chave modificado. No entanto, os usuários de Key Vault podem exigir sua chave para outros casos de uso, como após terem sido excluídos. Nesse caso, eles podem usar as operações de BACKUP e restauração para exportar/importar a chave em um formulário protegido. As chaves criadas pela operação de BACKUP não podem ser usadas fora do Key Vault. Como alternativa, a operação de importação pode ser usada em várias instâncias de Key Vault.  

Os usuários podem restringir qualquer uma das operações criptográficas que Key Vault oferece suporte por chave usando a propriedade key_ops do objeto JWK.  

Para obter mais informações sobre objetos JWK, consulte [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="key-attributes"></a>Atributos de chave

Além do material da chave, os atributos a seguir podem ser especificados. Em uma solicitação JSON, a palavra-chave Attributes e chaves, ' {' '} ', são necessárias mesmo se não houver nenhum atributo especificado.  

- *habilitado*: booliano, opcional, o padrão é **true**. Especifica se a chave está habilitada e utilizável para operações criptográficas. O atributo *Enabled* é usado em conjunto com *NBF* e *exp*. Quando ocorrer uma operação entre *NBF* e *exp*, ele só será permitido se *habilitado* for definido como **true**. As operações fora da janela *NBF* / *exp* são automaticamente desautorizadas, exceto para determinados tipos de operação sob [condições específicas](#date-time-controlled-operations).
- *nbf*: IntDate, opcional, o padrão é agora. O atributo *NBF* (não antes) identifica a hora antes da qual a chave não deve ser usada para operações criptográficas, exceto para determinados tipos de operação sob [condições específicas](#date-time-controlled-operations). O processamento do atributo *NBF* requer que a data/hora atual deva ser posterior ou igual à data/hora anterior, listada no atributo *NBF* . Key Vault pode fornecer algumas pequenas reserva, normalmente não mais do que alguns minutos, para considerar a distorção do relógio. Seu valor deve ser um número que contenha um valor IntDate.  
- *exp*: IntDate, opcional, o padrão é "para sempre". O atributo *exp* (tempo de expiração) identifica o tempo de expiração em ou após o qual a chave não deve ser usada para operação criptográfica, exceto para determinados tipos de operação sob [condições específicas](#date-time-controlled-operations). O processamento do atributo *exp* requer que a data/hora atual deva ser anterior à data/hora de vencimento listada no atributo *exp* . Key Vault pode fornecer algumas pequenas reserva, normalmente não mais do que alguns minutos, para considerar a distorção do relógio. Seu valor deve ser um número que contenha um valor IntDate.  

Há atributos adicionais somente leitura que são incluídos em qualquer resposta que inclua atributos de chave:  

- *criado*em: IntDate, opcional. O atributo *created* indica quando esta versão da chave foi criada. O valor é nulo para chaves criadas antes da adição deste atributo. Seu valor deve ser um número que contenha um valor IntDate.  
- *atualizado*em: IntDate, opcional. O atributo *updated* indica quando esta versão da chave foi atualizada. O valor é nulo para as chaves que foram atualizadas pela última vez antes da adição deste atributo. Seu valor deve ser um número que contenha um valor IntDate.  

Para obter mais informações sobre IntDate e outros tipos de dados, consulte [tipos de dados](#data-types)  

#### <a name="date-time-controlled-operations"></a>Operações controladas por data e hora

As chaves ainda não válidas e expiradas, fora da janela *NBF* / *exp* , funcionarão para **descriptografar**, **desencapsular**e **verificar** as operações (não retornarão 403, proibido). A lógica para usar o estado ainda não válido é permitir que uma chave seja testada antes do uso da produção. A lógica para usar o estado expirado é permitir operações de recuperação em dados que foram criados quando a chave era válida. Além disso, você pode desabilitar o acesso a uma chave usando políticas de Key Vault ou atualizando o atributo de chave *habilitado* para **false**.

Para obter mais informações sobre tipos de dados, consulte [tipos de dados](#data-types).

Para obter mais informações sobre outros atributos possíveis, consulte a [chave da Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="key-tags"></a>Marcas de chave

Você pode especificar metadados adicionais específicos do aplicativo na forma de marcas. Key Vault dá suporte a até 15 marcas, cada uma delas pode ter um nome de caractere 256 e um valor de caractere 256.  

>[!Note]
>As marcas podem ser lidas por um chamador se tiverem a *lista* ou *obterem* permissão para esse tipo de objeto (chaves, segredos ou certificados).

###  <a name="key-access-control"></a>Key access control (Controlo de acesso a chaves)

O controle de acesso para chaves gerenciadas pelo Key Vault é fornecido no nível de um Key Vault que atua como o contêiner de chaves. A política de controle de acesso para chaves é distinta da política de controle de acesso para segredos na mesma Key Vault. Os usuários podem criar um ou mais cofres para manter as chaves e são necessários para manter a segmentação apropriada do cenário e o gerenciamento de chaves. O controle de acesso para chaves é independente do controle de acesso para segredos.  

As permissões a seguir podem ser concedidas, em uma base de entidade por usuário/serviço, na entrada de controle de acesso chaves em um cofre. Essas permissões espelham fortemente as operações permitidas em um objeto de chave.  Conceder acesso a uma entidade de serviço no Key Vault é uma operação OneTime e permanecerá o mesmo para todas as assinaturas do Azure. Você pode usá-lo para implantar quantos certificados desejar. 

- Permissões para operações de gerenciamento de chaves
  - *obter*: Ler a parte pública de uma chave, além de seus atributos
  - *list*: Listar as chaves ou versões de uma chave armazenada em um cofre de chaves
  - *atualização*: Atualizar os atributos de uma chave
  - *criar*: Criar novas chaves
  - *importar*: Importar uma chave para um cofre de chaves
  - *excluir*: Excluir o objeto de chave
  - *recuperar*: Recuperar uma chave excluída
  - *backup*: Fazer backup de uma chave em um cofre de chaves
  - *restaurar*: Restaurar uma chave de backup para um cofre de chaves

- Permissões para operações criptográficas
  - *decrypt*: Use a chave para desproteger uma sequência de bytes
  - *encrypt*: Use a chave para proteger uma sequência arbitrária de bytes
  - *unwrapKey*: Usar a chave para desproteger as chaves simétricas encapsuladas
  - *wrapKey*: Usar a chave para proteger uma chave simétrica
  - *verificar*: Use a chave para verificar resumos  
  - *assinar*: Usar a chave para assinar resumos
    
- Permissões para operações privilegiadas
  - *limpar*: Limpar (excluir permanentemente) uma chave excluída

Para obter mais informações sobre como trabalhar com chaves, consulte [operações de chave na referência da API REST do Key Vault](/rest/api/keyvault). Para obter informações sobre como estabelecer permissões, consulte [cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [cofres-atualizar política de acesso](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="key-vault-secrets"></a>Segredos de Key Vault 

### <a name="working-with-secrets"></a>Trabalhando com segredos

Da perspectiva de um desenvolvedor, Key Vault APIs aceitam e retornam valores secretos como cadeias de caracteres. Internamente, o Key Vault armazena e gerencia segredos como sequências de octetos (bytes de 8 bits), com um tamanho máximo de 25K bytes cada. O serviço de Key Vault não fornece semântica para segredos. Ele simplesmente aceita os dados, criptografa-os, armazena-os e retorna um identificador secreto ("ID"). O identificador pode ser usado para recuperar o segredo em um momento posterior.  

Para dados altamente confidenciais, os clientes devem considerar camadas adicionais de proteção para os dados. A criptografia de dados usando uma chave de proteção separada antes do armazenamento no Key Vault é um exemplo.  

Key Vault também dá suporte a um campo contentType para segredos. Os clientes podem especificar o tipo de conteúdo de um segredo para ajudar a interpretar os dados secretos quando eles são recuperados. O comprimento máximo desse campo é de 255 caracteres. Não há valores predefinidos. O uso sugerido é como uma dica para interpretar os dados secretos. Por exemplo, uma implementação pode armazenar senhas e certificados como segredos e, em seguida, usar esse campo para diferenciar. Não há valores predefinidos.  

### <a name="secret-attributes"></a>Atributos secretos

Além dos dados secretos, os seguintes atributos podem ser especificados:  

- *exp*: IntDate, opcional, o padrão é para **sempre**. O atributo *exp* (tempo de expiração) identifica o tempo de expiração em ou após o qual os dados secretos não devem ser recuperados, exceto em [situações específicas](#date-time-controlled-operations). Esse campo é para fins **informativos** apenas quando informa aos usuários do serviço de cofre de chaves que um segredo específico não pode ser usado. Seu valor deve ser um número que contenha um valor IntDate.   
- *nbf*: IntDate, opcional, o padrão é **agora**. O atributo *NBF* (não antes) identifica a hora antes da qual os dados secretos não devem ser recuperados, exceto em [situações específicas](#date-time-controlled-operations). Este campo é apenas para fins **informativos** . Seu valor deve ser um número que contenha um valor IntDate. 
- *habilitado*: booliano, opcional, o padrão é **true**. Esse atributo especifica se os dados secretos podem ser recuperados. O atributo Enabled é usado em conjunto com *NBF* e *exp* quando ocorre uma operação entre *NBF* e *exp*. ele só será permitido se habilitado for definido como **true**. As operações fora da janela *NBF* e *exp* são automaticamente desautorizadas, exceto em [situações específicas](#date-time-controlled-operations).  

Há atributos adicionais somente leitura que são incluídos em qualquer resposta que inclua atributos secretos:  

- *criado*em: IntDate, opcional. O atributo created indica quando esta versão do segredo foi criada. Esse valor é nulo para segredos criados antes da adição deste atributo. Seu valor deve ser um número que contenha um valor IntDate.  
- *atualizado*em: IntDate, opcional. O atributo updated indica quando esta versão do segredo foi atualizada. Esse valor é nulo para os segredos que foram atualizados pela última vez antes da adição deste atributo. Seu valor deve ser um número que contenha um valor IntDate.

#### <a name="date-time-controlled-operations"></a>Operações controladas por data e hora

A operação **Get** de um segredo funcionará para segredos que ainda não são válidos e expirados, fora da janela *NBF* / *exp* . Chamar a operação **Get** de um segredo, para um segredo ainda não válido, pode ser usado para fins de teste. Recuperar (**receber**) um segredo expirado pode ser usado para operações de recuperação.

Para obter mais informações sobre tipos de dados, consulte [tipos de dados](#data-types).  

### <a name="secret-access-control"></a>Secret access control (Controlo de acesso a segredos)

O controle de acesso para segredos gerenciados no Key Vault, é fornecido no nível do Key Vault que contém esses segredos. A política de controle de acesso para segredos é distinta da política de controle de acesso para chaves no mesmo Key Vault. Os usuários podem criar um ou mais cofres para manter os segredos e são necessários para manter a segmentação apropriada do cenário e o gerenciamento de segredos.   

As permissões a seguir podem ser usadas, em uma base por entidade de segurança, na entrada de controle de acesso dos segredos em um cofre e espelham com mais detalhes as operações permitidas em um objeto secreto:  

- Permissões para operações de gerenciamento de segredo
  - *obter*: Ler um segredo  
  - *list*: Listar os segredos ou as versões de um segredo armazenado em um Key Vault  
  - *definir*: Criar um segredo  
  - *excluir*: Eliminar um segredo  
  - *recuperar*: Recuperar um segredo excluído
  - *backup*: Fazer backup de um segredo em um cofre de chaves
  - *restaurar*: Restaurar um segredo de backup para um cofre de chaves

- Permissões para operações privilegiadas
  - *limpar*: Limpar (excluir permanentemente) um segredo excluído

Para saber mais sobre como trabalhar com segredos, confira [operações secretas na referência da API REST do Key Vault](/rest/api/keyvault). Para obter informações sobre como estabelecer permissões, consulte [cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [cofres-atualizar política de acesso](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Marcas secretas  
Você pode especificar metadados adicionais específicos do aplicativo na forma de marcas. Key Vault dá suporte a até 15 marcas, cada uma delas pode ter um nome de caractere 256 e um valor de caractere 256.  

>[!Note]
>As marcas podem ser lidas por um chamador se tiverem a *lista* ou *obterem* permissão para esse tipo de objeto (chaves, segredos ou certificados).

## <a name="key-vault-certificates"></a>Key Vault certificados

O suporte a certificados de Key Vault fornece o gerenciamento de seus certificados X509 e os seguintes comportamentos:  

-   Permite que um proprietário de certificado crie um certificado por meio de um processo de criação de Key Vault ou por meio da importação de um certificado existente. Inclui certificados autoassinados e gerados por autoridades de certificação.
-   Permite que o proprietário de um Key Vault certificado implemente o armazenamento seguro e o gerenciamento de certificados X509 sem interação com o material da chave privada.  
-   Permite que um proprietário de certificado crie uma política que direciona Key Vault para gerenciar o ciclo de vida de um certificado.  
-   Permite que os proprietários de certificado forneçam informações de contato para notificação sobre eventos do ciclo de vida de expiração e renovação do certificado.  
-   Dá suporte à renovação automática com os emissores selecionados-Key Vault provedores de certificado X509 do parceiro/autoridades de certificação.

>[!Note]
>Provedores/autoridades não-parceiros também são permitidos, mas não dará suporte ao recurso de renovação automática.

### <a name="composition-of-a-certificate"></a>Composição de um certificado

Quando um certificado de Key Vault é criado, uma chave endereçável e um segredo também são criados com o mesmo nome. A chave de Key Vault permite operações de chave e o segredo Key Vault permite a recuperação do valor do certificado como um segredo. Um certificado de Key Vault também contém metadados de certificado X509 público.  

O identificador e a versão de certificados são semelhantes às chaves e aos segredos. Uma versão específica de uma chave endereçável e um segredo criado com o Key Vault versão do certificado está disponível na resposta do certificado Key Vault.
 
![Os certificados são objetos complexos](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Chave exportável ou não exportável

Quando um certificado de Key Vault é criado, ele pode ser recuperado do segredo endereçável com a chave privada no formato PFX ou PEM. A política usada para criar o certificado deve indicar que a chave é exportável. Se a política indicar não exportável, a chave privada não faz parte do valor quando recuperada como um segredo.  

A chave endereçável se torna mais relevante com certificados KV não exportáveis. As operações de chave KV endereçáveis são mapeadas do campo *KeyUsage* da política de certificado kV usada para criar o certificado kV.  

Há suporte para dois tipos de chave – *RSA* ou *RSA HSM* com certificados. Exportável só é permitido com RSA, não tem suporte do RSA HSM.  

### <a name="certificate-attributes-and-tags"></a>Atributos e marcas de certificado

Além dos metadados do certificado, uma chave endereçável e um segredo endereçável, um Key Vault certificado também contém atributos e marcas.  

#### <a name="attributes"></a>Atributos

Os atributos de certificado são espelhados para atributos da chave endereçável e do segredo criado quando o certificado KV é criado.  

Um certificado Key Vault tem os seguintes atributos:  

-   *habilitado*: booliano, opcional, o padrão é **true**. Pode ser especificado para indicar se os dados do certificado podem ser recuperados como segredo ou operável como uma chave. Também é usado em conjunto com *NBF* e *exp* quando ocorre uma operação entre *NBF* e *exp*, e somente será permitido se habilitado for definido como true. As operações fora da janela *NBF* e *exp* são automaticamente desautorizadas.  

Há atributos adicionais somente leitura que são incluídos na resposta:

-   *criado*em: IntDate: indica quando esta versão do certificado foi criada.  
-   *atualizado*em: IntDate: indica quando esta versão do certificado foi atualizada.  
-   *exp*: IntDate: contém o valor da data de expiração do certificado X509.  
-   *nbf*: IntDate: contém o valor da data do certificado X509.  

> [!Note] 
> Se um certificado de Key Vault expirar, a chave endereçável e o segredo se tornarão inoperáveis.  

#### <a name="tags"></a>Tags

 O cliente especificou o dicionário de pares chave-valor, semelhante a marcas em chaves e segredos.  

 > [!Note]
> As marcas podem ser lidas por um chamador se tiverem a *lista* ou *obterem* permissão para esse tipo de objeto (chaves, segredos ou certificados).

### <a name="certificate-policy"></a>Política de certificado

Uma política de certificado contém informações sobre como criar e gerenciar o ciclo de vida de um certificado de Key Vault. Quando um certificado com chave privada é importado para o cofre de chaves, uma política padrão é criada lendo o certificado X509.  

Quando um certificado de Key Vault é criado do zero, é necessário fornecer uma política. A política especifica como criar essa Key Vault versão do certificado ou a próxima Key Vault versão do certificado. Depois que uma política é estabelecida, ela não é necessária com operações de criação sucessivas para versões futuras. Há apenas uma instância de uma política para todas as versões de um certificado Key Vault.  

Em um alto nível, uma política de certificado contém as seguintes informações:  

-   Propriedades do certificado X509: Contém o nome da entidade, nomes alternativos da entidade e outras propriedades usadas para criar uma solicitação de certificado X509.  
-   Propriedades da chave: contém os campos tipo de chave, comprimento da chave, exportável e reutilização de chave. Esses campos instruem o cofre de chaves sobre como gerar uma chave.  
-   Propriedades secretas: contém propriedades secretas, como tipo de conteúdo de segredo endereçável para gerar o valor secreto, para recuperar o certificado como um segredo.  
-   Ações de tempo de vida: contém ações de tempo de vida para o certificado KV. Cada ação de tempo de vida contém:  

     - Gatilho: especificado por meio de dias antes da expiração ou porcentagem do período de tempo de vida  

     - Ação: especificando o tipo de ação – *emailContacts* ou *renovar autorenovação*  

-   Emissor Parâmetros sobre o emissor do certificado a ser usado para emitir certificados X509.  
-   Atributos de política: contém atributos associados à política  

#### <a name="x509-to-key-vault-usage-mapping"></a>Key Vault o X509 para mapeamento de uso

A tabela a seguir representa o mapeamento da política de uso de chave X509 para efetivas operações de chave de uma chave criada como parte de uma Key Vault criação de certificado.

|**Sinalizadores de uso de chave X509**|**Key Vault Ops chave**|**Comportamento padrão**|
|----------|--------|--------|
|DataEncipherment|criptografar, descriptografar| N/A |
|DecipherOnly|Codifique| N/A  |
|DigitalSignature|assinar, verificar| Key Vault padrão sem especificação de uso no momento da criação do certificado | 
|EncipherOnly|encrypt| N/A |
|KeyCertSign|assinar, verificar|N/A|
|KeyEncipherment|wrapKey, unwrapKey| Key Vault padrão sem especificação de uso no momento da criação do certificado | 
|Não-repúdio|assinar, verificar| N/A |
|crlsign|assinar, verificar| N/A |

### <a name="certificate-issuer"></a>Emissor do certificado

Um objeto de certificado Key Vault mantém uma configuração usada para se comunicar com um provedor de emissor de certificado selecionado para solicitar certificados X509.  

-   Parceiros de Key Vault com os seguintes provedores de emissor de certificado para certificados SSL

|**Nome do provedor**|**Localizações**|
|----------|--------|
|DigiCert|Com suporte em todos os locais de serviço do cofre de chaves na nuvem pública e no Azure governamental|
|GlobalSign|Com suporte em todos os locais de serviço do cofre de chaves na nuvem pública e no Azure governamental|

Antes que um emissor de certificado possa ser criado em um Key Vault, as etapas de pré-requisito 1 e 2 a seguir devem ser realizadas com êxito.  

1. Integração a provedores de autoridade de certificação (CA)  

    -   Um administrador da organização deve integrar sua empresa (por exemplo, Contoso) com pelo menos um provedor de autoridade de certificação.  

2. O administrador cria credenciais do solicitante para Key Vault registrar (e renovar) certificados SSL  

    -   Fornece a configuração a ser usada para criar um objeto emissor do provedor no cofre de chaves  

Para obter mais informações sobre como criar objetos de emissor no portal de certificados, consulte o [blog Key Vault certificados](https://aka.ms/kvcertsblog)  

Key Vault permite a criação de vários objetos de emissor com configuração de provedor de emissor diferente. Depois que um objeto emissor é criado, seu nome pode ser referenciado em uma ou várias políticas de certificado. A referência ao objeto emissor instrui Key Vault a usar a configuração conforme especificado no objeto emissor ao solicitar o certificado X509 do provedor de CA durante a criação e a renovação do certificado.  

Os objetos do emissor são criados no cofre e só podem ser usados com certificados KV no mesmo cofre.  

### <a name="certificate-contacts"></a>Contatos de certificado

Os contatos de certificado contêm informações de contato para enviar notificações disparadas por eventos de tempo de vida do certificado. As informações de contatos são compartilhadas por todos os certificados no cofre de chaves. Uma notificação é enviada a todos os contatos especificados para um evento para qualquer certificado no cofre de chaves.  

Se a política de um certificado for definida como renovação automática, uma notificação será enviada nos eventos a seguir.  

- Antes da renovação do certificado
- Após a renovação do certificado, informando se o certificado foi renovado com êxito ou se houve um erro, exigindo a renovação manual do certificado.  

  Quando uma política de certificado definida para ser renovada manualmente (somente email), uma notificação é enviada quando é hora de renovar o certificado.  

### <a name="certificate-access-control"></a>Controle de acesso de certificado

 O controle de acesso para certificados é gerenciado pelo Key Vault e é fornecido pelo Key Vault que contém esses certificados. A política de controle de acesso para certificados é distinta das políticas de controle de acesso para chaves e segredos no mesmo Key Vault. Os usuários podem criar um ou mais cofres para armazenar certificados, a fim de manter a segmentação apropriada do cenário e o gerenciamento de certificados.  

 As permissões a seguir podem ser usadas, em uma base por entidade de segurança, na entrada de controle de acesso dos segredos em um cofre de chaves e refletem de forma mais alta as operações permitidas em um objeto secreto:  

- Permissões para operações de gerenciamento de certificado
  - *obter*: Obter a versão atual do certificado ou qualquer versão de um certificado 
  - *list*: Listar os certificados atuais ou versões de um certificado  
  - *atualização*: Atualizar um certificado
  - *criar*: Criar um certificado Key Vault
  - *importar*: Importar material do certificado para um Key Vault certificado
  - *excluir*: Excluir um certificado, sua política e todas as suas versões  
  - *recuperar*: Recuperar um certificado excluído
  - *backup*: Fazer backup de um certificado em um cofre de chaves
  - *restaurar*: Restaurar um certificado de backup para um cofre de chaves
  - *managecontacts*: Gerenciar Key Vault contatos de certificado  
  - *manageissuers*: Gerenciar autoridades de certificação/emissores Key Vault
  - *emissors*: Obter as autoridades/emissores de um certificado
  - *listissuers*: Listar as autoridades/emissores de um certificado  
  - *emissores*: Criar ou atualizar as autoridades/os emissores de um certificado Key Vault  
  - *deleteissuers*: Excluir as autoridades/os emissores de um Key Vault certificado  
 
- Permissões para operações privilegiadas
  - *limpar*: Limpar (excluir permanentemente) um certificado excluído

Para obter mais informações, consulte as [operações de certificado na referência da API REST do Key Vault](/rest/api/keyvault). Para obter informações sobre como estabelecer permissões, consulte [cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [cofres-atualizar política de acesso](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Gerenciamento de chaves da conta de armazenamento do Azure

Key Vault pode gerenciar as chaves de conta de armazenamento do Azure:

- Internamente, Key Vault pode listar (sincronizar) chaves com uma conta de armazenamento do Azure. 
- Key Vault regenera (gira) as chaves periodicamente.
- Os valores de chave nunca são retornados em resposta ao chamador.
- Key Vault gerencia chaves de contas de armazenamento e contas de armazenamento clássicas.

Para obter mais informações, consulte [Azure Key Vault chaves da conta de armazenamento](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>Controle de acesso da conta de armazenamento

As seguintes permissões podem ser usadas ao autorizar uma entidade de usuário ou aplicativo para executar operações em uma conta de armazenamento gerenciada:  

- Permissões para a conta de armazenamento gerenciado e operações de definição de SaS
  - *obter*: Obtém informações sobre uma conta de armazenamento 
  - *list*: Listar contas de armazenamento gerenciadas por um Key Vault
  - *atualização*: Atualizar uma conta de armazenamento
  - *excluir*: Eliminar uma conta do Storage  
  - *recuperar*: Recuperar uma conta de armazenamento excluída
  - *backup*: Fazer backup de uma conta de armazenamento
  - *restaurar*: Restaurar uma conta de armazenamento de backup para um Key Vault
  - *definir*: Criar ou atualizar uma conta de armazenamento
  - *regeneratekey*: Regenerar um valor de chave especificado para uma conta de armazenamento
  - *getss*: Obter informações sobre uma definição de SAS para uma conta de armazenamento
  - *listas*: Listar definições de SAS de armazenamento para uma conta de armazenamento
  - *deletesas*: Excluir uma definição de SAS de uma conta de armazenamento
  - *conjuntos*de: Criar ou atualizar uma nova definição/atributo de SAS para uma conta de armazenamento

- Permissões para operações privilegiadas
  - *limpar*: Limpar (excluir permanentemente) uma conta de armazenamento gerenciada

Para obter mais informações, consulte as [operações da conta de armazenamento na referência da API REST do Key Vault](/rest/api/keyvault). Para obter informações sobre como estabelecer permissões, consulte [cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [cofres-atualizar política de acesso](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Consultar Também

- [Autenticação, solicitações e respostas](authentication-requests-and-responses.md)
- [Guia do Programador do Key Vault](/azure/key-vault/key-vault-developers-guide)
