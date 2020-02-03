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
ms.openlocfilehash: affa182145645b9a91801a9c6b38e682e6bd77ec
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720066"
---
# <a name="about-keys-secrets-and-certificates"></a>Sobre chaves, segredos e certificados

Azure Key Vault permite que Microsoft Azure aplicativos e usuários armazenem e usem vários tipos de dados de segredo/chave:

- Chaves de criptografia: dá suporte a vários tipos de chave e algoritmos e habilita o uso de HSM (módulos de segurança de hardware) para chaves de valor alto. 
- Segredos: fornece armazenamento seguro de segredos, como senhas e cadeias de conexão de banco de dados.
- Certificados: oferece suporte a certificados, que são criados com base em chaves e segredos e adicionam um recurso de renovação automatizada.
- Armazenamento do Azure: o pode gerenciar chaves de uma conta de armazenamento do Azure para você. Internamente, Key Vault pode listar (sincronizar) chaves com uma conta de armazenamento do Azure e regenerar (girar) as chaves periodicamente. 

Para obter informações mais gerais sobre o Cofre chave, veja [o que é o Cofre chave Azure?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

As seções a seguir oferecem informações gerais aplicáveis em toda a implementação do serviço de Key Vault.

### <a name="supporting-standards"></a>Padrões de suporte

As especificações JavaScript Object Notation (JSON) e José (assinatura de objeto JavaScript e criptografia) são informações importantes sobre o plano de fundo.  

-   [Chave Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)  
-   [Encriptação web JSON (JWE)](https://tools.ietf.org/html/draft-ietf-jose-json-web-encryption-40)  
-   [Algoritmos Web JSON (JWA)](https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40)  
-   [JSON Web Signature (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41)  

### <a name="data-types"></a>Tipos de dados

Consulte as especificações de José para tipos de dados relevantes para chaves, criptografia e assinatura.  

-   **algoritmo** - um algoritmo suportado para uma operação chave, por exemplo, RSA1_5  
-   **cifra-valor -** octetos de texto cifrados, codificados usando Base64URL  
-   **valor digestão** - a saída de um algoritmo de hash, codificado usando Base64URL  
-   **tipo chave** - um dos tipos-chave suportados, por exemplo RSA (Rivest-Shamir-Adleman).  
-   **valor simples -** octetos de texto simples, codificados usando Base64URL  
-   **assinatura-valor** - saída de um algoritmo de assinatura, codificado usando Base64URL  
-   **base64URL** - um valor binário codificado de Base64URL [RFC4648]  
-   **boolean -** verdadeiro ou falso  
-   **Identidade** - uma identidade do Azure Ative Directory (AAD).  
-   **IntDate** - um valor decimal JSON que representa o número de segundos de 1970-01-01T0:0:0Z UTC até à data/hora UTC especificada. Consulte RFC3339 para obter detalhes sobre data/hora, em geral e UTC em particular.  

### <a name="objects-identifiers-and-versioning"></a>Objetos, identificadores e controle de versão

Os objetos armazenados em Key Vault têm controle de versão sempre que uma nova instância de um objeto é criada. Cada versão recebe um identificador e uma URL exclusivos. Quando um objeto é criado pela primeira vez, ele recebe um identificador de versão exclusivo e marcado como a versão atual do objeto. A criação de uma nova instância com o mesmo nome de objeto dá ao novo objeto um identificador de versão exclusivo, fazendo com que ele se torne a versão atual.  

Os objetos no Key Vault podem ser endereçados usando o identificador atual ou um identificador específico da versão. Por exemplo, dada uma Chave com o nome `MasterKey`, realizar operações com o identificador atual faz com que o sistema utilize a versão mais recente disponível. Executar operações com o identificador específico da versão faz com que o sistema Use essa versão específica do objeto.  

Os objetos são identificados exclusivamente dentro de Key Vault usando uma URL. Dois objetos no sistema têm a mesma URL, independentemente da localização geográfica. A URL completa para um objeto é chamada de identificador de objeto. A URL consiste em um prefixo que identifica o Key Vault, o tipo de objeto, o nome do objeto fornecido pelo usuário e uma versão do objeto. O nome do objeto não diferencia maiúsculas de minúsculas e é imutável. Os identificadores que não incluem a versão do objeto são chamados de identificadores base.  

Para mais informações, consulte [Autenticação, pedidos e respostas](authentication-requests-and-responses.md)

Um identificador de objeto tem o seguinte formato geral:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Em que:  

|||  
|-|-|  
|`keyvault-name`|O nome de um cofre de chaves no serviço de Key Vault Microsoft Azure.<br /><br /> Os nomes de Key Vault são selecionados pelo usuário e são globalmente exclusivos.<br /><br /> Key Vault nome deve ser uma cadeia de caracteres 3-24, contendo apenas 0-9, a-z, A-Z e-.|  
|`object-type`|O tipo do objeto, "Keys" ou "segredos".|  
|`object-name`|Um `object-name` é um utilizador fornecido nome e deve ser único dentro de um Cofre chave. O nome deve ser uma cadeia de caracteres 1-127, contendo apenas 0-9, a-z, A-Z e-.|  
|`object-version`|Um `object-version` é um identificador de cadeia de 32 caracteres gerado pelo sistema que é opcionalmente usado para abordar uma versão única de um objeto.|  

## <a name="key-vault-keys"></a>Chaves de Key Vault

### <a name="keys-and-key-types"></a>Chaves e tipos de chave

As teclas criptográficas no Cofre chave são representadas como objetos jSON Web Key [JWK]. As especificações base de JWK/JWA também são estendidas para habilitar tipos de chave exclusivos para a implementação de Key Vault. Por exemplo, a importação de chaves usando o empacotamento específico de fornecedor do HSM permite o transporte seguro de chaves que podem ser usadas apenas em Key Vault HSMs.  

- **Teclas "soft"** : Uma chave processada em software pela Key Vault, mas é encriptada em repouso utilizando uma chave de sistema que está num HSM. Os clientes podem importar uma chave existente de RSA ou EC (curva elíptica) ou solicitar que Key Vault gerar uma.
- **Teclas "hard"** : Uma chave processada num HSM (Módulo de Segurança de Hardware). Essas chaves são protegidas em um dos mundos de segurança Key Vault HSM (há um mundo de segurança por geografia para manter o isolamento). Os clientes podem importar uma chave RSA ou EC, em forma flexível ou exportando de um dispositivo HSM compatível. Os clientes também podem solicitar Key Vault para gerar uma chave. Este tipo de chave adiciona o atributo key_hsm ao JWK obter para transportar o material chave HSM.

     Para obter mais informações sobre fronteiras geográficas, consulte [o Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

Key Vault dá suporte apenas a chaves de curva elíptica e RSA. 

-   **EC**: Tecla curva elíptica "macia".
-   **Tecla EC-HSM**: Tecla curva elíptica "dura".
-   **RSA**: Tecla RSA "macia".
-   **RSA-HSM**: Tecla RSA "Hard".

Key Vault dá suporte a chaves RSA de tamanhos 2048, 3072 e 4096. O Key Vault dá suporte a tipos de chave de curva elíptica P-256, P-384, P-521 e P-256K (SECP256K1).

### <a name="cryptographic-protection"></a>Proteção criptográfica

Os módulos de criptografia que o Key Vault usa, seja o HSM ou software, validados por FIPS (padrões de processamento de informações federais). Você não precisa fazer nada especial para executar no modo FIPS. As chaves **criadas** ou **importadas** como protegidas por HSM são processadas dentro de um HSM, validado para o Nível 2 do FIPS 140-2. As teclas **criadas** ou **importadas** como protegidas por software, são processadas dentro de módulos criptográficos validados para o Nível 1 40-2 FIPS 1. Para mais informações, consulte [Chaves e tipos de chaves](#keys-and-key-types).

###  <a name="ec-algorithms"></a>Algoritmos do EC
 Os identificadores de algoritmo a seguir têm suporte com chaves EC e EC-HSM em Key Vault. 

#### <a name="curve-types"></a>Tipos de curva

-   **P-256** - A curva NIST P-256, definida no [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256K** - A curva SEC SECP256K1, definida na [SEC 2: Recomendado elíptico Curcurve Domain Parâmetros](https://www.secg.org/sec2-v2.pdf).
-   **P-384** - A curva NIST P-384, definida no [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** - A curva NIST P-521, definida no [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>ASSINAR/VERIFICAR

-   **ES256** - ECDSA para digeridos sha-256 e chaves criadas com curva P-256. Este algoritmo é descrito no [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** - ECDSA para digeridos sha-256 e chaves criadas com curva P-256K. Esse algoritmo é a padronização pendente.
-   **ES384** - ECDSA para digeridos sha-384 e chaves criadas com curva P-384. Este algoritmo é descrito no [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** - ECDSA para digeridos SHA-512 e chaves criadas com curva P-521. Este algoritmo é descrito no [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>Algoritmos RSA  
 Os seguintes identificadores de algoritmo têm suporte com as chaves RSA e RSA-HSM no Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, CRIPTOGRAFAR/DESCRIPTOGRAFAR

-   **RSA1_5** - Encriptação chave RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP** - RSAES utilizando o acolchoamento de encriptação assimétrica ideal (OAEP) [RFC3447], com os parâmetros predefinidos especificados pelo RFC 3447 na secção A.2.1. Esses parâmetros padrão estão usando uma função de hash do SHA-1 e uma função de geração de máscara de MGF1 com SHA-1.  

#### <a name="signverify"></a>ASSINAR/VERIFICAR

-   **RS256** - RSASSA-PKCS-v1_5 usando SHA-256. O valor de resumo fornecido pelo aplicativo deve ser computado usando SHA-256 e deve ter 32 bytes de comprimento.  
-   **RS384** - RSASSA-PKCS-v1_5 usando SHA-384. O valor de resumo fornecido pelo aplicativo deve ser computado usando SHA-384 e deve ter 48 bytes de comprimento.  
-   **RS512** - RSASSA-PKCS-v1_5 usando SHA-512. O valor de resumo fornecido pelo aplicativo deve ser computado usando SHA-512 e deve ter 64 bytes de comprimento.  
-   **RSNULL** - Ver [RFC2437], um caso de utilização especializado para permitir certos cenários de TLS.  

###  <a name="key-operations"></a>Operações de chave

O Key Vault dá suporte às seguintes operações em objetos de chave:  

-   **Criar**: Permite que um cliente crie uma chave no Cofre chave. O valor da chave é gerado por Key Vault e armazenado e não é liberado para o cliente. Chaves assimétricas podem ser criadas em Key Vault.  
-   **Importação**: Permite que um cliente importe uma chave existente para o Cofre Chave. Chaves assimétricas podem ser importadas para Key Vault usando vários métodos de empacotamento diferentes dentro de uma construção JWK. 
-   **Atualização**: Permite a um cliente com permissões suficientes para modificar os metadados (atributos chave) associados a uma chave anteriormente armazenada no Cofre chave.  
-   **Eliminar**: Permite a um cliente com permissões suficientes para apagar uma chave do Cofre de Chaves.  
-   **Lista**: Permite que um cliente enumere todas as chaves num dado Cofre chave.  
-   **Versões de lista**: Permite que um cliente enumere todas as versões de uma determinada chave num dado Key Vault.  
-   **Obter**: Permite que um cliente recupere as partes públicas de uma determinada chave num Cofre chave.  
-   **Backup**: Exporta uma chave de forma protegida.  
-   **Restaurar**: Importa uma chave previamente apoiada.  

Para mais informações, consulte [as principais operações na referência](/rest/api/keyvault)a API do Cofre Chave .  

Depois que uma chave tiver sido criada no Key Vault, as seguintes operações criptográficas poderão ser executadas usando a chave:  

-   **Sinal e Verificação**: Estritamente, esta operação é "sinal de hash" ou "verificar hash", uma vez que o Key Vault não suporta o hashing dos conteúdos como parte da criação de assinaturas. Os aplicativos devem fazer o hash dos dados a serem assinados localmente e solicitar que Key Vault assinar o hash. A verificação de hashes assinados é suportada como uma operação de conveniência para aplicações que podem não ter acesso a material-chave [público]. Para obter o melhor desempenho do aplicativo, verifique se as operações são executadas localmente.  
-   **Encriptação da chave / Embrulho**: Uma chave armazenada no Cofre chave pode ser usada para proteger outra chave, tipicamente uma chave de encriptação de conteúdo simétrico (CEK). Quando a chave no Key Vault é assimétrica, a criptografia de chave é usada. Por exemplo, as operações RSA-OAEP e WRAPKEY/UNWRAPKEY são equivalentes a CRIPTOGRAFAr/descriptografar. Quando a chave no Key Vault é simétrica, o encapsulamento de chave é usado. Por exemplo, AES-KW. A operação WRAPKEY é suportada como uma conveniência para aplicações que podem não ter acesso a material chave [público]. Para obter o melhor desempenho do aplicativo, as operações de WRAPKEY devem ser executadas localmente.  
-   **Encriptar e desencriptar:** Uma chave armazenada no Cofre de Chaves pode ser usada para encriptar ou desencriptar um único bloco de dados. O tamanho do bloco é determinado pelo tipo de chave e pelo algoritmo de criptografia selecionado. A operação Encrypt está prevista para conveniência, para aplicações que podem não ter acesso a material chave [público]. Para obter o melhor desempenho do aplicativo, as operações de criptografia devem ser executadas localmente.  

Embora WRAPKEY/UNWRAPKEY usando chaves assimétricas possa parecer supérfluo (pois a operação é equivalente a CRIPTOGRAFAr/descriptografar), o uso de operações distintas é importante. A distinção fornece a separação semântica e de autorização dessas operações e a consistência quando outros tipos de chave são suportados pelo serviço.  

Key Vault não dá suporte a operações de exportação. Depois que uma chave é provisionada no sistema, ela não pode ser extraída ou seu material de chave modificado. No entanto, os usuários de Key Vault podem exigir sua chave para outros casos de uso, como após terem sido excluídos. Nesse caso, eles podem usar as operações de BACKUP e restauração para exportar/importar a chave em um formulário protegido. As chaves criadas pela operação de BACKUP não podem ser usadas fora do Key Vault. Como alternativa, a operação de importação pode ser usada em várias instâncias de Key Vault.  

Os utilizadores podem restringir qualquer uma das operações criptográficas que o Key Vault suporta numa base por chave utilizando a propriedade key_ops do objeto JWK.  

Para obter mais informações sobre objetos JWK, consulte [jSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

###  <a name="key-attributes"></a>Atributos de chave

Além do material-chave, podem ser especificados os seguintes atributos. Em uma solicitação JSON, a palavra-chave Attributes e chaves, ' {' '} ', são necessárias mesmo se não houver nenhum atributo especificado.  

- *ativado:* booleano, opcional, padrão é **verdade**. Especifica se a chave está habilitada e utilizável para operações criptográficas. O atributo *ativado* é utilizado em conjunto com *nbf* e *exp*. Quando ocorrer uma operação entre *a nbf* e a *ex-exp,* só será permitida se *ativada* for **verdadeira**. As operações fora da janela de *exp* *nbf* / são automaticamente proibidas, com exceção de certos tipos de funcionamento em [condições específicas](#date-time-controlled-operations).
- *nbf*: IntDate, opcional, o padrão é agora. O atributo *nbf* (não antes) identifica o tempo anterior ao qual a chave NÃO DEVE SER utilizada para operações criptográficas, com exceção de certos tipos de funcionamento em [condições específicas](#date-time-controlled-operations). O processamento do atributo *nbf* requer que a data/hora atual DEVE seja posterior ou igual à data/hora não antes enumerada no atributo *nbf.* Key Vault pode fornecer algumas pequenas reserva, normalmente não mais do que alguns minutos, para considerar a distorção do relógio. Seu valor deve ser um número que contenha um valor IntDate.  
- *exp*: IntDate, opcional, o padrão é "para sempre". O *atributo exp* (tempo de validade) identifica o tempo de validade em ou após o qual a chave NÃO DEVE SER utilizada para o funcionamento criptográfico, exceto para certos tipos de funcionamento em [condições específicas](#date-time-controlled-operations). O processamento do atributo *exp* exige que a data/hora atual DEVE seja antes da data/hora de validade enumerada no atributo *exp.* Key Vault pode fornecer algumas pequenas reserva, normalmente não mais do que alguns minutos, para considerar a distorção do relógio. Seu valor deve ser um número que contenha um valor IntDate.  

Há atributos adicionais somente leitura que são incluídos em qualquer resposta que inclua atributos de chave:  

- *criado*: IntDate, opcional. O atributo *criado* indica quando esta versão da chave foi criada. O valor é nulo para chaves criadas antes da adição deste atributo. Seu valor deve ser um número que contenha um valor IntDate.  
- *atualizado*: IntDate, opcional. O atributo *atualizado* indica quando esta versão da chave foi atualizada. O valor é nulo para as chaves que foram atualizadas pela última vez antes da adição deste atributo. Seu valor deve ser um número que contenha um valor IntDate.  

Para obter mais informações sobre o IntDate e outros tipos de dados, consulte [os tipos de dados](#data-types)  

#### <a name="date-time-controlled-operations"></a>Operações controladas por data e hora

As chaves ainda não válidas e expiradas, fora da *janela* de exp *nbf* / , funcionarão para **desencriptar,** **desembrulhar,** e **verificar** as operações (não devolverão 403, Proibido). A lógica para usar o estado ainda não válido é permitir que uma chave seja testada antes do uso da produção. A lógica para usar o estado expirado é permitir operações de recuperação em dados que foram criados quando a chave era válida. Além disso, pode desativar o acesso a uma chave utilizando as políticas do Cofre chave, ou atualizando o atributo-chave *ativado* a **falso**.

Para obter mais informações sobre os tipos de dados, consulte [os tipos de dados](#data-types).

Para obter mais informações sobre outros possíveis atributos, consulte a [Chave Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

### <a name="key-tags"></a>Marcas de chave

Você pode especificar metadados adicionais específicos do aplicativo na forma de marcas. Key Vault dá suporte a até 15 marcas, cada uma delas pode ter um nome de caractere 256 e um valor de caractere 256.  

>[!Note]
>As etiquetas são legíveis por um chamador se tiverem a *lista* ou *obterem* permissão para esse tipo de objeto (chaves, segredos ou certificados).

###  <a name="key-access-control"></a>Key access control (Controlo de acesso a chaves)

O controle de acesso para chaves gerenciadas pelo Key Vault é fornecido no nível de um Key Vault que atua como o contêiner de chaves. A política de controle de acesso para chaves é distinta da política de controle de acesso para segredos na mesma Key Vault. Os usuários podem criar um ou mais cofres para manter as chaves e são necessários para manter a segmentação apropriada do cenário e o gerenciamento de chaves. O controle de acesso para chaves é independente do controle de acesso para segredos.  

As permissões a seguir podem ser concedidas, em uma base de entidade por usuário/serviço, na entrada de controle de acesso chaves em um cofre. Essas permissões espelham fortemente as operações permitidas em um objeto de chave.  Conceder acesso a uma entidade de serviço no Key Vault é uma operação OneTime e permanecerá o mesmo para todas as assinaturas do Azure. Você pode usá-lo para implantar quantos certificados desejar. 

- Permissões para operações de gerenciamento de chaves
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

## <a name="key-vault-secrets"></a>Segredos de Key Vault 

### <a name="working-with-secrets"></a>Trabalhando com segredos

Da perspectiva de um desenvolvedor, Key Vault APIs aceitam e retornam valores secretos como cadeias de caracteres. Internamente, o Key Vault armazena e gerencia segredos como sequências de octetos (bytes de 8 bits), com um tamanho máximo de 25K bytes cada. O serviço de Key Vault não fornece semântica para segredos. Ele simplesmente aceita os dados, criptografa-os, armazena-os e retorna um identificador secreto ("ID"). O identificador pode ser usado para recuperar o segredo em um momento posterior.  

Para dados altamente sensíveis, os clientes devem considerar camadas adicionais de proteção para dados. Encriptar dados utilizando uma chave de proteção separada antes do armazenamento no Cofre chave é um exemplo.  

Key Vault também dá suporte a um campo contentType para segredos. Os clientes podem especificar o tipo de conteúdo de um segredo para ajudar a interpretar os dados secretos quando eles são recuperados. O comprimento máximo desse campo é de 255 caracteres. Não há valores predefinidos. O uso sugerido é como uma dica para interpretar os dados secretos. Por exemplo, uma implementação pode armazenar senhas e certificados como segredos e, em seguida, usar esse campo para diferenciar. Não há valores predefinidos.  

### <a name="secret-attributes"></a>Atributos secretos

Além dos dados secretos, os seguintes atributos podem ser especificados:  

- *exp*: IntDate, opcional, o padrão é **para sempre**. O atributo *exp* (tempo de validade) identifica o tempo de validade em ou após o qual os dados secretos NÃO devem ser recuperados, exceto em [situações específicas](#date-time-controlled-operations). Este campo destina-se apenas a fins **informantes,** uma vez que informa os utilizadores do serviço de cofre seletiva que um determinado segredo pode não ser utilizado. Seu valor deve ser um número que contenha um valor IntDate.   
- *nbf*: IntDate, opcional, por defeito é **agora**. O atributo *nbf* (não antes) identifica o tempo anterior ao qual os dados secretos NÃO devem ser recuperados, exceto em [situações específicas](#date-time-controlled-operations). Este campo é apenas para fins **informantes.** Seu valor deve ser um número que contenha um valor IntDate. 
- *ativado:* booleano, opcional, padrão é **verdade**. Esse atributo especifica se os dados secretos podem ser recuperados. O atributo ativado é utilizado em conjunto com a *nbf* e *exp* quando ocorre uma operação entre *nbf* e *exp,* só será permitido se estiver ativado. As operações fora da janela *nbf* e *exp* são automaticamente proibidas, exceto em [situações específicas](#date-time-controlled-operations).  

Há atributos adicionais somente leitura que são incluídos em qualquer resposta que inclua atributos secretos:  

- *criado*: IntDate, opcional. O atributo created indica quando esta versão do segredo foi criada. Esse valor é nulo para segredos criados antes da adição deste atributo. Seu valor deve ser um número que contenha um valor IntDate.  
- *atualizado*: IntDate, opcional. O atributo updated indica quando esta versão do segredo foi atualizada. Esse valor é nulo para os segredos que foram atualizados pela última vez antes da adição deste atributo. Seu valor deve ser um número que contenha um valor IntDate.

#### <a name="date-time-controlled-operations"></a>Operações controladas por data e hora

Uma **operação** secreta funcionará por segredos ainda não válidos e expirados, fora da janela de *exp* da *NBF* / . Chamar uma **operação** secreta para um segredo ainda não válido, pode ser usado para fins de teste. Recuperar **(obter**ting) um segredo expirado, pode ser usado para operações de recuperação.

Para obter mais informações sobre os tipos de dados, consulte [os tipos de dados](#data-types).  

### <a name="secret-access-control"></a>Secret access control (Controlo de acesso a segredos)

O controle de acesso para segredos gerenciados no Key Vault, é fornecido no nível do Key Vault que contém esses segredos. A política de controle de acesso para segredos é distinta da política de controle de acesso para chaves no mesmo Key Vault. Os usuários podem criar um ou mais cofres para manter os segredos e são necessários para manter a segmentação apropriada do cenário e o gerenciamento de segredos.   

As permissões a seguir podem ser usadas, em uma base por entidade de segurança, na entrada de controle de acesso dos segredos em um cofre e espelham com mais detalhes as operações permitidas em um objeto secreto:  

- Permissões para operações de gerenciamento de segredo
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

### <a name="secret-tags"></a>Marcas secretas  
Você pode especificar metadados adicionais específicos do aplicativo na forma de marcas. Key Vault dá suporte a até 15 marcas, cada uma delas pode ter um nome de caractere 256 e um valor de caractere 256.  

>[!Note]
>As etiquetas são legíveis por um chamador se tiverem a *lista* ou *obterem* permissão para esse tipo de objeto (chaves, segredos ou certificados).

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

A chave endereçável se torna mais relevante com certificados KV não exportáveis. As operações da chave KV endereçadas são mapeadas a partir do campo de *utilização* da chave da política de certificados KV utilizada para criar o Certificado KV.  

São suportados dois tipos de teclas – *RSA* ou *RSA HSM* com certificados. Exportável só é permitido com RSA, não tem suporte do RSA HSM.  

### <a name="certificate-attributes-and-tags"></a>Atributos e marcas de certificado

Além dos metadados do certificado, uma chave endereçável e um segredo endereçável, um Key Vault certificado também contém atributos e marcas.  

#### <a name="attributes"></a>Atributos

Os atributos de certificado são espelhados para atributos da chave endereçável e do segredo criado quando o certificado KV é criado.  

Um certificado Key Vault tem os seguintes atributos:  

-   *ativado:* booleano, opcional, padrão é **verdade**. Pode ser especificado para indicar se os dados do certificado podem ser recuperados como segredo ou operável como uma chave. Também utilizado em conjunto com *nbf* e *exp* quando uma operação ocorre entre *nbf* e *exp*, e só será permitido se ativado for definido para verdade. As operações fora da *janela nbf* e *exp* são automaticamente proibidas.  

Há atributos adicionais somente leitura que são incluídos na resposta:

-   *criado*: IntDate: indica quando esta versão do certificado foi criada.  
-   *atualizado*: IntDate: indica quando esta versão do certificado foi atualizada.  
-   *exp*: IntDate: contém o valor da data de validade do certificado x509.  
-   *nbf*: IntDate: contém o valor da data do certificado x509.  

> [!Note] 
> Se um certificado de Key Vault expirar, a chave endereçável e o segredo se tornarão inoperáveis.  

#### <a name="tags"></a>Etiquetas

 O cliente especificou o dicionário de pares chave-valor, semelhante a marcas em chaves e segredos.  

 > [!Note]
> As etiquetas são legíveis por um chamador se tiverem a *lista* ou *obterem* permissão para esse tipo de objeto (chaves, segredos ou certificados).

### <a name="certificate-policy"></a>Política de certificado

Uma política de certificado contém informações sobre como criar e gerenciar o ciclo de vida de um certificado de Key Vault. Quando um certificado com chave privada é importado para o cofre de chaves, uma política padrão é criada lendo o certificado X509.  

Quando um certificado de Key Vault é criado do zero, é necessário fornecer uma política. A política especifica como criar essa Key Vault versão do certificado ou a próxima Key Vault versão do certificado. Depois que uma política é estabelecida, ela não é necessária com operações de criação sucessivas para versões futuras. Há apenas uma instância de uma política para todas as versões de um certificado Key Vault.  

Em um alto nível, uma política de certificado contém as seguintes informações:  

-   Propriedades do certificado X509: contém o nome da entidade, nomes alternativos da entidade e outras propriedades usadas para criar uma solicitação de certificado X509.  
-   Propriedades da chave: contém os campos tipo de chave, comprimento da chave, exportável e reutilização de chave. Esses campos instruem o cofre de chaves sobre como gerar uma chave.  
-   Propriedades secretas: contém propriedades secretas, como tipo de conteúdo de segredo endereçável para gerar o valor secreto, para recuperar o certificado como um segredo.  
-   Ações de tempo de vida: contém ações de tempo de vida para o certificado KV. Cada ação de tempo de vida contém:  

     - Gatilho: especificado por meio de dias antes da expiração ou porcentagem do período de tempo de vida  

     - Ação: especificar tipo de ação – *emailContactos ou* *renovar automaticamente*  

-   Emissor: parâmetros sobre o emissor do certificado a ser usado para emitir certificados X509.  
-   Atributos de política: contém atributos associados à política  

#### <a name="x509-to-key-vault-usage-mapping"></a>Key Vault o X509 para mapeamento de uso

A tabela a seguir representa o mapeamento da política de uso de chave X509 para efetivas operações de chave de uma chave criada como parte de uma Key Vault criação de certificado.

|**Bandeiras de utilização da chave X509**|**Operações chave do cofre chave**|**Comportamento padrão**|
|----------|--------|--------|
|DataEncipherment|criptografar, descriptografar| N/A |
|DecipherOnly|Codifique| N/A  |
|DigitalSignature|assinar, verificar| Key Vault padrão sem especificação de uso no momento da criação do certificado | 
|EncipherOnly|encrypt| N/A |
|KeyCertSign|assinar, verificar|N/A|
|KeyEncipherment|wrapKey, unwrapKey| Key Vault padrão sem especificação de uso no momento da criação do certificado | 
|Não-repúdio|assinar, verificar| N/A |
|crlsign|assinar, verificar| N/A |

### <a name="certificate-issuer"></a>Emissor do Certificado

Um objeto de certificado Key Vault mantém uma configuração usada para se comunicar com um provedor de emissor de certificado selecionado para solicitar certificados X509.  

-   Parceiros de Key Vault com os seguintes provedores de emissor de certificado para certificados TLS/SSL

|**Nome do fornecedor**|**Localizações**|
|----------|--------|
|DigiCert|Com suporte em todos os locais de serviço do cofre de chaves na nuvem pública e no Azure governamental|
|GlobalSign|Com suporte em todos os locais de serviço do cofre de chaves na nuvem pública e no Azure governamental|

Antes que um emissor de certificado possa ser criado em um Key Vault, as etapas de pré-requisito 1 e 2 a seguir devem ser realizadas com êxito.  

1. Integração a provedores de autoridade de certificação (CA)  

    -   Um administrador da organização deve integrar sua empresa (por exemplo, Contoso) com pelo menos um provedor de autoridade de certificação.  

2. O administrador cria credenciais do solicitante para Key Vault registrar (e renovar) certificados TLS/SSL  

    -   Fornece a configuração a ser usada para criar um objeto emissor do provedor no cofre de chaves  

Para obter mais informações sobre a criação de objetos Emitentes a partir do portal certificates, consulte o [blog Key Vault Certificates](https://aka.ms/kvcertsblog)  

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

## <a name="azure-storage-account-key-management"></a>Gerenciamento de chaves da conta de armazenamento do Azure

Key Vault pode gerenciar as chaves de conta de armazenamento do Azure:

- Internamente, Key Vault pode listar (sincronizar) chaves com uma conta de armazenamento do Azure. 
- Key Vault regenera (gira) as chaves periodicamente.
- Os valores de chave nunca são retornados em resposta ao chamador.
- Key Vault gerencia chaves de contas de armazenamento e contas de armazenamento clássicas.

Para mais informações, consulte as chaves da conta de armazenamento do [cofre de chaves Azure](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>Controle de acesso da conta de armazenamento

As seguintes permissões podem ser usadas ao autorizar uma entidade de usuário ou aplicativo para executar operações em uma conta de armazenamento gerenciada:  

- Permissões para a conta de armazenamento gerenciado e operações de definição de SaS
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

## <a name="see-also"></a>Consulte Também

- [Autenticação, pedidos e respostas](authentication-requests-and-responses.md)
- [Guia do Programador do Key Vault](/azure/key-vault/key-vault-developers-guide)
