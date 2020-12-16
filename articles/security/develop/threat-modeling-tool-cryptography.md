---
title: Criptografia - Ferramenta de Modelação de Ameaças da Microsoft - Azure / Microsoft Docs
description: Saiba mais sobre a mitigação da criptografia para ameaças expostas na Ferramenta de Modelação de Ameaças. Consulte informações de mitigação e veja exemplos de código.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 6af4fba8ddf50b795d847a2c7b4e2fbc02fe593f
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587184"
---
# <a name="security-frame-cryptography--mitigations"></a>Quadro de Segurança: Criptografia / Mitigações 

| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Aplicação Web** | <ul><li>[Utilize apenas cifras de bloco simétricos aprovadas e comprimentos de chave](#cipher-length)</li><li>[Utilize modos de cifra de blocos aprovados e vetores de inicialização para cifras simétricas](#vector-ciphers)</li><li>[Utilize algoritmos assimétricos aprovados, comprimentos de chave e estofamento](#padding)</li><li>[Utilizar geradores de números aleatórios aprovados](#numgen)</li><li>[Não utilize cifras de fluxo simétrico](#stream-ciphers)</li><li>[Utilize algoritmos de hash MAC/HMAC/chavedo aprovados](#mac-hash)</li><li>[Utilize apenas funções de haxixe criptográfico aprovadas](#hash-functions)</li></ul> |
| **Base de dados** | <ul><li>[Use algoritmos de encriptação fortes para encriptar dados na base de dados](#strong-db)</li><li>[Os pacotes SSIS devem ser encriptados e assinados digitalmente](#ssis-signed)</li><li>[Adicionar assinatura digital a bases de dados críticas](#securables-db)</li><li>[Use o servidor SQL EKM para proteger as chaves de encriptação](#ekm-keys)</li><li>[Utilize funcionalidade AlwaysEncrypted se as chaves de encriptação não forem reveladas ao motor da base de dados](#keys-engine)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Guarde as chaves criptográficas de forma segura no dispositivo IoT](#keys-iot)</li></ul> | 
| **Gateway de nuvem IoT** | <ul><li>[Gere uma chave simétrica aleatória de comprimento suficiente para a autenticação no IoT Hub](#random-hub)</li></ul> | 
| **Dynamics CRM Cliente Móvel** | <ul><li>[Certifique-se de que existe uma política de gestão de dispositivos que requer um PIN de utilização e permite a limpeza remota](#pin-remote)</li></ul> | 
| **Dynamics CRM Outlook Cliente** | <ul><li>[Certifique-se de que existe uma política de gestão de dispositivos que requer um PIN/password/bloqueio automático e encripta todos os dados (por exemplo, BitLocker)](#bitlocker)</li></ul> | 
| **Servidor de Identidade** | <ul><li>[Certifique-se de que as teclas de assinatura são enroladas ao utilizar o Servidor de Identidade](#rolled-server)</li><li>[Certifique-se de que o ID do cliente criptograficamente forte, o segredo do cliente são usados no Servidor de Identidade](#client-server)</li></ul> | 

## <a name="use-only-approved-symmetric-block-ciphers-and-key-lengths"></a><a id="cipher-length"></a>Utilize apenas cifras de bloco simétricos aprovadas e comprimentos de chave

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Os produtos devem utilizar apenas as cifras de bloco simétrico e os comprimentos de chave associados que tenham sido explicitamente aprovados pelo Crypto Advisor na sua organização. Os algoritmos simétricos aprovados na Microsoft incluem as seguintes cifras de bloco:</p><ul><li>Para o novo código AES-128, AES-192 e AES-256 são aceitáveis</li><li>Para a retrocompatibilidade com o código existente, o 3DES de três chaves é aceitável</li><li>Para os produtos que utilizam cifras de bloco simétrico:<ul><li>O Padrão avançado de encriptação (AES) é necessário para novo código</li><li>A norma de encriptação de dados tripla de três chaves (3DES) é permitida no código existente para retrocompatibilidade</li><li>Todas as outras cifras de blocos, incluindo RC2, DES, 2 Key 3DES, DESX e Skipjack, só podem ser usadas para desencriptar dados antigos, e devem ser substituídas se forem utilizadas para encriptação</li></ul></li><li>Para algoritmos de encriptação de bloco simétrico, é necessário um comprimento mínimo de chave de 128 bits. O único algoritmo de encriptação de bloco recomendado para novo código é AES (AES-128, AES-192 e AES-256 são todos aceitáveis)</li><li>O 3DES de três chaves é atualmente aceitável se já estiver em uso no código existente; recomenda-se a transição para a AES. DES, DESX, RC2 e Skipjack já não são considerados seguros. Estes algoritmos só podem ser utilizados para desencriptar os dados existentes por uma questão de retrocompatibilidade, e os dados devem ser reencrim encriptados usando uma cifra de bloco recomendada</li></ul><p>Por favor, note que todas as cifras de bloco simétrico devem ser utilizadas com um modo de cifra aprovado, que requer a utilização de um vetor de inicialização apropriado (IV). Um IV apropriado, é tipicamente um número aleatório e nunca um valor constante</p><p>A utilização de algoritmos criptos antigos ou não aprovados e comprimentos-chave menores para a leitura dos dados existentes (em oposição à escrita de novos dados) pode ser permitida após a revisão do Crypto Board da sua organização. No entanto, deve solicitar uma exceção contra este requisito. Além disso, nas implementações das empresas, os produtos devem considerar os administradores de alerta quando o crypto fraco é usado para ler dados. Tais advertências devem ser explicativas e exequíveis. Em alguns casos, pode ser apropriado ter a Política de Grupo controlar o uso de criptos fracos</p><p>Algoritmos .NET permitidos para criptotidade gerida (por ordem de preferência)</p><ul><li>AesCng (compatível com o FIPS)</li><li>AutenticadoAesCng (compatível com FIPS)</li><li>AESCryptoServiceProvider (compatível com FIPS)</li><li>AESManaged (não conforme com o FIPS)</li></ul><p>Por favor, note que nenhum destes algoritmos pode ser especificado através do `SymmetricAlgorithm.Create` ou `CryptoConfig.CreateFromName` métodos sem fazer alterações no ficheiro machine.config. Além disso, note que a AES em versões de .NET antes de .NET 3.5 é nomeado `RijndaelManaged` , e são >`AesCng` `AuthenticatedAesCng` disponíveis através do CodePlex e requerem CNG no SISTEMA subjacente</p>

## <a name="use-approved-block-cipher-modes-and-initialization-vectors-for-symmetric-ciphers"></a><a id="vector-ciphers"></a>Utilize modos de cifra de blocos aprovados e vetores de inicialização para cifras simétricas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Todas as cifras de bloco simétrico devem ser utilizadas com um modo de cifra simétrica aprovado. Os únicos modos aprovados são o CBC e o CTS. Em especial, deve evitar-se o modo de funcionamento do livro eletrónico de códigos (BCE); o uso do BCE requer a revisão do Crypto Board da sua organização. Toda a utilização de OFB, CFB, CTR, CCM e GCM ou qualquer outro modo de encriptação deve ser revisto pela Crypto Board da sua organização. Reutilizar o mesmo vetor de inicialização (IV) com cifras de blocos em "modos de cifras de streaming", como o CTR, pode fazer com que os dados encriptados sejam revelados. Todas as cifras de bloco simétricos também devem ser utilizadas com um vetor de inicialização apropriado (IV). Um IV apropriado é um número criptograficamente forte, aleatório e nunca um valor constante. |

## <a name="use-approved-asymmetric-algorithms-key-lengths-and-padding"></a><a id="padding"></a>Utilize algoritmos assimétricos aprovados, comprimentos de chave e estofamento

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>A utilização de algoritmos criptográficos proibidos introduz riscos significativos para a segurança do produto e deve ser evitada. Os produtos devem utilizar apenas os algoritmos criptográficos e os comprimentos e estofamentos associados que tenham sido explicitamente aprovados pela Crypto Board da sua organização.</p><ul><li>**RSA-** pode ser usado para encriptação, troca de chaves e assinatura. A encriptação RSA deve utilizar apenas os modos de enchimento OAEP ou RSA-KEM. O código existente pode utilizar pkcs #1 modo de enchimento v1.5 apenas para compatibilidade. A utilização de estofos nulos é explicitamente proibida. As teclas >= 2048 bits são necessárias para novo código. O código existente pode suportar chaves < bits de 2048 apenas para retrocompatibilidade após uma revisão pelo Crypto Board da sua organização. As chaves < 1024 bits só podem ser utilizadas para desencriptar/verificar dados antigos, e devem ser substituídas se forem utilizadas para operações de encriptação ou assinatura</li><li>**ECDSA-** pode ser usado apenas para assinatura. ECDSA com >=teclas de 256 bits são necessárias para novo código. As assinaturas baseadas no ECDSA devem utilizar uma das três curvas aprovadas pelo NIST (P-256, P-384 ou P521). As curvas que foram analisadas minuciosamente só podem ser usadas após uma revisão com o Crypto Board da sua organização.</li><li>**ECDH-** pode ser usado apenas para troca de chaves. ECDH com >=teclas de 256 bits é necessária para novo código. O intercâmbio de chaves com base no ECDH deve utilizar uma das três curvas aprovadas pelo NIST (P-256, P-384 ou P521). As curvas que foram analisadas minuciosamente só podem ser usadas após uma revisão com o Crypto Board da sua organização.</li><li>**A DSA-** pode ser aceitável após revisão e aprovação do Crypto Board da sua organização. Contacte o seu conselheiro de segurança para agendar a revisão do Crypto Board da sua organização. Se a sua utilização de DSA for aprovada, note que terá de proibir a utilização de chaves com menos de 2048 bits de comprimento. O CNG suporta 2048 bits e comprimentos-chave maiores a partir do Windows 8.</li><li>**Diffie-Hellman-** pode ser usado apenas para gestão de chaves de sessão. O comprimento da chave >= 2048 bits é necessário para novo código. O código existente pode suportar os comprimentos-chave < 2048 bits apenas para retrocompatibilidade após uma revisão pelo Crypto Board da sua organização. As teclas < 1024 bits não podem ser utilizadas.</li><ul>

## <a name="use-approved-random-number-generators"></a><a id="numgen"></a>Utilizar geradores de números aleatórios aprovados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Os produtos devem utilizar geradores de números aleatórios aprovados. Funções de Pseudorandom como o rand de função de execução C, o sistema de classe .NET Framework.Random, ou funções do sistema como GetTickCount nunca devem ser usadas nesse código. É proibida a utilização do algoritmo do gerador de número aleatório de curva elíptica dupla (DUAL_EC_DRBG)</p><ul><li>**CNG-** BCryptGenRandom (utilização da bandeira de BCRYPT_USE_SYSTEM_PREFERRED_RNG recomendada, a menos que o chamador possa funcionar em qualquer IRQL superior a 0 [isto é, PASSIVE_LEVEL])</li><li>**CAPI-** criptGenRandom</li><li>**Win32/64-** RtlGenRandom (novas implementações devem utilizar BCryptGenRandom ou CryptGenRandom) * rand_s * SystemPrng (para o modo kernel)</li><li>**. NET-** RNGCryptoServiceProvider ou RNGCng</li><li>**Aplicativos da Windows Store...** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom ou . Gerar ORandomNumber</li><li>**Apple OS X (10.7+)/iOS (2.0+)-** int SecRandomCopyBytes (SecRandomRef random, size_t count, uint8_t \* bytes)</li><li>**Apple OS X (<10.7)-** Utilize /dev/random para recuperar números aleatórios</li><li>**Java (incluindo o código Java do Google Android)-** java.security.SecureRandom classe. Note que para o Android 4.3 (Jelly Bean), os desenvolvedores devem seguir a solução recomendada para Android e atualizar as suas aplicações para inicializar explicitamente o PRNG com entropia de /dev/urandom ou /dev/random</li></ul>|

## <a name="do-not-use-symmetric-stream-ciphers"></a><a id="stream-ciphers"></a>Não utilize cifras de fluxo simétrico

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | As cifras de fluxo simétrico, tais como o RC4, não devem ser utilizadas. Em vez de cifras de fluxo simétrico, os produtos devem utilizar uma cifra de bloco, especificamente AES com um comprimento de chave de pelo menos 128 bits. |

## <a name="use-approved-machmackeyed-hash-algorithms"></a><a id="mac-hash"></a>Utilize algoritmos de hash MAC/HMAC/chavedo aprovados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Os produtos devem utilizar apenas algoritmos aprovados de código de autenticação de mensagens (MAC) ou de código de autenticação de mensagens (HMAC).</p><p>Um código de autenticação de mensagem (MAC) é uma informação anexada a uma mensagem que permite ao destinatário verificar a autenticidade do remetente e a integridade da mensagem utilizando uma chave secreta. A utilização de um MAC baseado em haxixe[(HMAC)](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)ou [mac baseado em cifras de blocos](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) é admissível desde que todos os algoritmos de encriptação subjacentes ou simétricos sejam também aprovados para utilização; atualmente isto inclui as funções HMAC-SHA2 (HMAC-SHA256, HMAC-SHA384 e HMAC-SHA512) e os MACs baseados em cifras CMAC/OMAC1 e OMAC2 (estes são baseados em AES).</p><p>A utilização de HMAC-SHA1 pode ser permitida para a compatibilidade da plataforma, mas você será obrigado a preencher uma exceção a este procedimento e submeter-se à revisão Crypto da sua organização. Não é permitida a truncação de HMACs a menos de 128 bits. A utilização de métodos de cliente para hash uma chave e os dados não são aprovados, e deve ser submetida à revisão crypto board da sua organização antes de usar.</p>|

## <a name="use-only-approved-cryptographic-hash-functions"></a><a id="hash-functions"></a>Utilize apenas funções de haxixe criptográfico aprovadas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Os produtos devem utilizar a família SHA-2 de algoritmos de haxixe (SHA256, SHA384 e SHA512). Se for necessário um haxixe mais curto, como um comprimento de saída de 128 bits para encaixar numa estrutura de dados concebida com o haxixe MD5 mais curto em mente, as equipas de produtos podem truncar um dos hashes SHA2 (normalmente SHA256). Note que SHA384 é uma versão truncada de SHA512. Não é permitida a truncação de hashes criptográficos para fins de segurança inferiores a 128 bits. O novo código não deve utilizar os algoritmos de haxixe MD2, MD4, MD5, SHA-0, SHA-1 ou RIPEMD. Colisões de haxixe são computacionalmente viáveis para estes algoritmos, o que efetivamente os quebra.</p><p>Algoritmos de haxixe .NET permitidos para criptostrução gerida (por ordem de preferência):</p><ul><li>SHA512Cng (compatível com o FIPS)</li><li>SHA384Cng (compatível com o FIPS)</li><li>SHA256Cng (compatível com o FIPS)</li><li>SHA512Managed (não conforme com o FIPS) (use SHA512 como nome de algoritmo em chamadas para HashAlgorithm.Create ou CryptoConfig.CreateFromName)</li><li>SHA384Managed (não conforme com o FIPS) (use SHA384 como nome de algoritmo em chamadas para HashAlgorithm.Create ou CryptoConfig.CreateFromName)</li><li>SHA256Managed (não conforme com o FIPS) (use SHA256 como nome de algoritmo em chamadas para HashAlgorithm.Create ou CryptoConfig.CreateFromName)</li><li>SHA512CryptoServiceProvider (compatível com FIPS)</li><li>SHA256CryptoServiceProvider (compatível com FIPS)</li><li>SHA384CryptoServiceProvider (compatível com FIPS)</li></ul>| 

## <a name="use-strong-encryption-algorithms-to-encrypt-data-in-the-database"></a><a id="strong-db"></a>Use algoritmos de encriptação fortes para encriptar dados na base de dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Escolher um algoritmo de encriptação](/sql/relational-databases/security/encryption/choose-an-encryption-algorithm) |
| **Passos** | Os algoritmos de encriptação definem transformações de dados que não podem ser facilmente revertidas por utilizadores não autorizados. SQL Server permite que administradores e desenvolvedores escolham entre vários algoritmos, incluindo DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, 128-bit RC4, DESX, 128-bit AES, 192-bit AES e AES de 256 bits |

## <a name="ssis-packages-should-be-encrypted-and-digitally-signed"></a><a id="ssis-signed"></a>Os pacotes SSIS devem ser encriptados e assinados digitalmente

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Identificar a Fonte de Pacotes com Assinaturas Digitais,](/sql/integration-services/security/identify-the-source-of-packages-with-digital-signatures) [Mitigação de Ameaças e Vulnerabilidades (Serviços de Integração)](/sql/integration-services/security/security-overview-integration-services) |
| **Passos** | A fonte de um pacote é o indivíduo ou organização que criou o pacote. Executar um pacote de uma fonte desconhecida ou não fidedquirísta pode ser arriscado. Para evitar a adulteração não autorizada de pacotes SSIS, devem ser utilizadas assinaturas digitais. Além disso, para garantir a confidencialidade dos pacotes durante o armazenamento/trânsito, os pacotes SSIS têm de ser encriptados |

## <a name="add-digital-signature-to-critical-database-securables"></a><a id="securables-db"></a>Adicionar assinatura digital a bases de dados críticas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [ADICIONAR ASSINATURA (Transact-SQL)](/sql/t-sql/statements/add-signature-transact-sql) |
| **Passos** | Nos casos em que a integridade de uma base de dados crítica seja possível, devem ser utilizadas assinaturas digitais. As garantias de base de dados, tais como um procedimento armazenado, função, montagem ou gatilho podem ser assinadas digitalmente. Abaixo está um exemplo de quando isto pode ser útil: Digamos que um ISV (Fornecedor de Software Independente) forneceu suporte a um software entregue a um dos seus clientes. Antes de fornecer suporte, o ISV gostaria de garantir que uma base de dados securável no software não foi adulterada nem por engano nem por uma tentativa maliciosa. Se o securable for assinado digitalmente, o ISV pode verificar a sua assinatura digital e validar a sua integridade.| 

## <a name="use-sql-server-ekm-to-protect-encryption-keys"></a><a id="ekm-keys"></a>Use o servidor SQL EKM para proteger as chaves de encriptação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [SQL Server Gestão de Chaves Extensíveis (EKM)](/sql/relational-databases/security/encryption/extensible-key-management-ekm), [Gestão de Chaves Extensíveis Usando cofre de chave Azure (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server) |
| **Passos** | A Gestão de Chaves Extensíveis do Servidor SQL permite que as chaves de encriptação que protegem os ficheiros da base de dados sejam armazenadas num dispositivo off-box, como um smartcard, dispositivo USB ou módulo EKM/HSM. Isto também permite a proteção de dados de administradores de bases de dados (exceto membros do grupo sysadmin). Os dados podem ser encriptados utilizando chaves de encriptação a que apenas o utilizador da base de dados tem acesso no módulo EKM/HSM externo. |

## <a name="use-alwaysencrypted-feature-if-encryption-keys-should-not-be-revealed-to-database-engine"></a><a id="keys-engine"></a>Utilize funcionalidade AlwaysEncrypted se as chaves de encriptação não forem reveladas ao motor da base de dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | Versão SQL - V12, MsSQL2016 |
| **Referências**              | [Sempre encriptado (motor de base de dados)](/sql/relational-databases/security/encryption/always-encrypted-database-engine) |
| **Passos** | Sempre Encriptado é uma funcionalidade concebida para proteger dados sensíveis, tais como números de cartões de crédito ou números de identificação nacionais (por exemplo, números de segurança social dos EUA), armazenados em bases de dados Azure SQL Database ou SQL Server. Sempre encriptado permite que os clientes criptografem dados sensíveis dentro das aplicações do cliente e nunca revelem as chaves de encriptação do Motor de Base de Dados (SQL Database ou SQL Server). Como resultado, a Always Encrypted fornece uma separação entre os que possuem os dados (e podem vê-los) e aqueles que gerem os dados (mas não devem ter acesso) |

## <a name="store-cryptographic-keys-securely-on-iot-device"></a><a id="keys-iot"></a>Guarde as chaves criptográficas de forma segura no dispositivo IoT

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Dispositivo OS - Windows IoT Core, Conectividade do Dispositivo - SDKs de dispositivo Azure IoT |
| **Referências**              | [TPM no Windows IoT Core](/windows/iot-core/secure-your-device/TPM), [Configurar TPM no Windows IoT Core](/windows/iot-core/secure-your-device/setuptpm), [Azure IoT Device SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Passos** | Chaves simétricas ou certificados Privadas de forma segura num armazenamento protegido de hardware como chips TPM ou Smart Card. O Windows 10 IoT Core suporta o utilizador de um TPM e existem vários TPMs compatíveis que podem ser utilizados: https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm . Recomenda-se a utilização de um Firmware ou um TPM discreto. Um TPM de software só deve ser utilizado para fins de desenvolvimento e teste. Uma vez que um TPM está disponível e as teclas são adquiram nele, o código que gera o token deve ser escrito sem codificar qualquer informação sensível nele. | 

### <a name="example"></a>Exemplo
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Como se pode ver, a chave primária do dispositivo não está presente no código. Em vez disso, é armazenado no TPM na ranhura 0. O dispositivo TPM gera um token SAS de curta duração que é então usado para ligar ao Hub IoT. 

## <a name="generate-a-random-symmetric-key-of-sufficient-length-for-authentication-to-iot-hub"></a><a id="random-hub"></a>Gere uma chave simétrica aleatória de comprimento suficiente para a autenticação no IoT Hub

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem IoT | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Escolha gateway - Azure IoT Hub |
| **Referências**              | N/D  |
| **Passos** | O IoT Hub contém um registo de identidade do dispositivo e, ao mesmo tempo que fornece um dispositivo, gera automaticamente uma chave simétrica aleatória. Recomenda-se a utilização desta característica do Registo de Identidade do Hub Azure IoT para gerar a chave utilizada para a autenticação. O IoT Hub também permite especificar uma chave durante a criação do dispositivo. Se uma chave for gerada fora do Hub IoT durante o fornecimento do dispositivo, recomenda-se criar uma chave simétrica aleatória ou pelo menos 256 bits. |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-use-pin-and-allows-remote-wiping"></a><a id="pin-remote"></a>Certifique-se de que existe uma política de gestão de dispositivos que requer um PIN de utilização e permite a limpeza remota

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM Cliente Móvel | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que existe uma política de gestão de dispositivos que requer um PIN de utilização e permite a limpeza remota |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-pinpasswordauto-lock-and-encrypts-all-data-eg-bitlocker"></a><a id="bitlocker"></a>Certifique-se de que existe uma política de gestão de dispositivos que requer um PIN/password/bloqueio automático e encripta todos os dados (por exemplo, BitLocker)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM Outlook Cliente | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que existe uma política de gestão de dispositivos que requer um PIN/password/bloqueio automático e encripta todos os dados (por exemplo, BitLocker) |

## <a name="ensure-that-signing-keys-are-rolled-over-when-using-identity-server"></a><a id="rolled-server"></a>Certifique-se de que as teclas de assinatura são enroladas ao utilizar o Servidor de Identidade

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Servidor de Identidade - Chaves, Assinaturas e Criptografia](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Passos** | Certifique-se de que as teclas de assinatura são enroladas quando utilizar o Servidor de Identidade. O link na secção de referências explica como isto deve ser planeado sem causar interrupções nas aplicações que dependem do Servidor de Identidade. |

## <a name="ensure-that-cryptographically-strong-client-id-client-secret-are-used-in-identity-server"></a><a id="client-server"></a>Certifique-se de que o ID do cliente criptograficamente forte, o segredo do cliente são usados no Servidor de Identidade

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade | 
| **Fase SDL**               | Compilar |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Certifique-se de que o ID do cliente criptograficamente forte, o segredo do cliente são usados no Servidor de Identidade. As seguintes diretrizes devem ser utilizadas enquanto gera uma ID do cliente e secreta:</p><ul><li>Gere um GUID aleatório como iD do cliente</li><li>Gere uma chave criptograficamente aleatória de 256 bits como o segredo</li></ul>|