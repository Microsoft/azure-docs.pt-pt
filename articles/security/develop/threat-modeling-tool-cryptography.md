---
title: Criptografia - Ferramenta de Modelação de Ameaças da Microsoft - Azure [ Microsoft Docs
description: atenuações para ameaças expostas na Ferramenta de Modelação de Ameaças
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
ms.openlocfilehash: c9116472af5b400ded0fea24f98b07bad9d9039b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68728194"
---
# <a name="security-frame-cryptography--mitigations"></a>Quadro de Segurança: Criptografia [ Atenuações 

| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Aplicação Web** | <ul><li>[Utilize apenas cifras de blocos simétricos aprovadas e comprimentos-chave](#cipher-length)</li><li>[Utilize modos de cifra de bloco aprovados e vetores de inicialização para cifras simétricas](#vector-ciphers)</li><li>[Utilize algoritmos assimétricos aprovados, comprimentos-chave e estofamento](#padding)</li><li>[Utilize geradores de númeroaleatórios aleatórios aprovados](#numgen)</li><li>[Não utilize cifras de fluxo simétricos](#stream-ciphers)</li><li>[Utilize algoritmos de haxixe MAC/HMAC/chave aprovados](#mac-hash)</li><li>[Utilize apenas funções de haxixe criptográfico aprovadas](#hash-functions)</li></ul> |
| **Base de dados** | <ul><li>[Use algoritmos de encriptação fortes para encriptar dados na base de dados](#strong-db)</li><li>[Os pacotes SSIS devem ser encriptados e assinados digitalmente](#ssis-signed)</li><li>[Adicione a assinatura digital a securáveis de base de dados críticas](#securables-db)</li><li>[Utilize o servidor SQL EKM para proteger as chaves de encriptação](#ekm-keys)</li><li>[Utilizar a funcionalidade AlwaysEncrypted se as chaves de encriptação não forem reveladas ao motor Base de Dados](#keys-engine)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Armazenar chaves criptográficas de forma segura no dispositivo IoT](#keys-iot)</li></ul> | 
| **Gateway da nuvem iot** | <ul><li>[Gerar uma chave simétrica aleatória de comprimento suficiente para autenticação ao Hub IoT](#random-hub)</li></ul> | 
| **Cliente Móvel Dynamics CRM** | <ul><li>[Certifique-se de que existe uma política de gestão de dispositivos que requer um PIN de utilização e permite a limpeza remota](#pin-remote)</li></ul> | 
| **Cliente de Outlook DE CRM Dynamics** | <ul><li>[Certifique-se de que existe uma política de gestão de dispositivos que requer um pin/password/bloqueio automático e encripta todos os dados (por exemplo, BitLocker)](#bitlocker)</li></ul> | 
| **Servidor de Identidade** | <ul><li>[Certifique-se de que as teclas de assinatura são reroladas ao utilizar o Servidor de Identidade](#rolled-server)</li><li>[Certifique-se de que o ID do cliente criptograficamente forte, o segredo do cliente é usado no Servidor de Identidade](#client-server)</li></ul> | 

## <a name="use-only-approved-symmetric-block-ciphers-and-key-lengths"></a><a id="cipher-length"></a>Utilize apenas cifras de blocos simétricos aprovadas e comprimentos-chave

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Os produtos devem utilizar apenas as cifras de blocos simétricos e os comprimentos-chave associados que foram explicitamente aprovados pelo Crypto Advisor na sua organização. Os algoritmos simétricos aprovados na Microsoft incluem as seguintes cifras de blocos:</p><ul><li>Para o novo código AES-128, AES-192 e AES-256 são aceitáveis</li><li>Para a retrocompatibilidade com o código existente, 3DES de três teclas é aceitável</li><li>Para produtos que utilizem cifras de blocos simétricos:<ul><li>Padrão avançado de encriptação (AES) é necessário para novo código</li><li>A Norma de Encriptação tripla de três chaves (3DES) é permitida no código existente para a compatibilidade retrógrada</li><li>Todas as outras cifras de blocos, incluindo RC2, DES, 2 Key 3DES, DESX e Skipjack, só podem ser utilizadas para desencriptar dados antigos, e devem ser substituídas se forem utilizadas para encriptação</li></ul></li><li>Para algoritmos de encriptação de blocos simétricos, é necessário um comprimento mínimo de chave de 128 bits. O único algoritmo de encriptação de blocorecomendado para novo código é AES (AES-128, AES-192 e AES-256 são todos aceitáveis)</li><li>Atualmente, o 3DES de três teclas é aceitável se já estiver em uso no código existente; Recomenda-se a transição para AES. DES, DESX, RC2 e Skipjack já não são considerados seguros. Estes algoritmos só podem ser utilizados para desencriptar os dados existentes por uma questão de retrocompatibilidade, e os dados devem ser reencriptados usando uma cifra de bloco recomendada</li></ul><p>Por favor, note que todas as cifras de blocos simétricos devem ser utilizadas com um modo de cifra aprovado, que requer a utilização de um vetor de inicialização apropriado (IV). Um IV apropriado, é tipicamente um número aleatório e nunca um valor constante</p><p>A utilização de algoritmos cripto-cripto antigos ou não aprovados e comprimentos-chave menores para a leitura de dados existentes (em oposição à escrita de novos dados) podem ser permitidas após a revisão do Crypto Board da sua organização. No entanto, deve solicitar uma exceção contra este requisito. Além disso, nas implementações empresariais, os produtos devem considerar os administradores de alerta quando o cripto fraco é usado para ler dados. Tais advertências devem ser explicativas e exequíveis. Em alguns casos, pode ser apropriado ter a Política do Grupo controlar o uso de cripto fraco</p><p>Algoritmos permitidos .NET para agilidade cripto gerida (por ordem de preferência)</p><ul><li>AesCng (conformidade com o FIPS)</li><li>AutenticadoAesCng (conformidade com o FIPS)</li><li>AESCryptoServiceProvider (compatível com FIPS)</li><li>AESManaged (não conforme com os FIPS)</li></ul><p>Por favor, note que nenhum destes `SymmetricAlgorithm.Create` algoritmos pode ser especificado através do ou `CryptoConfig.CreateFromName` métodos sem fazer alterações no ficheiro machine.config. Além disso, note que a AES em versões `RijndaelManaged`de `AesCng` .NET antes de .NET 3.5 é nomeado , e estão `AuthenticatedAesCng` >disponíveis através do CodePlex e requerem CNG no Sistema operativo subjacente</p>

## <a name="use-approved-block-cipher-modes-and-initialization-vectors-for-symmetric-ciphers"></a><a id="vector-ciphers"></a>Utilize modos de cifra de bloco aprovados e vetores de inicialização para cifras simétricas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Todas as cifras de blocos simétricos devem ser utilizadas com um modo de cifra simétrica aprovado. Os únicos modos aprovados são CBC e CTS. Em especial, deve evitar-se o modo de funcionamento do livro eletrónico (BCE); a utilização do BCE requer a revisão do Crypto Board da sua organização. Todo o uso de OFB, CFB, CTR, CCM e GCM ou qualquer outro modo de encriptação deve ser revisto pelo Crypto Board da sua organização. A reutilização do mesmo vetor de inicialização (IV) com cifras de blocos em "modos de cifras de streaming", como o CTR, pode fazer com que os dados encriptados sejam revelados. Todas as cifras de blocos simétricos também devem ser utilizadas com um vetor de inicialização apropriado (IV). Um IV apropriado é um número criptograficamente forte, aleatório e nunca um valor constante. |

## <a name="use-approved-asymmetric-algorithms-key-lengths-and-padding"></a><a id="padding"></a>Utilize algoritmos assimétricos aprovados, comprimentos-chave e estofamento

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>A utilização de algoritmos criptográficos proibidos introduz riscos significativos para a segurança do produto e deve ser evitada. Os produtos devem usar apenas esses algoritmos criptográficos e comprimentos-chave associados e estofamento que foram explicitamente aprovados pelo Crypto Board da sua organização.</p><ul><li>**RSA-** pode ser usado para encriptação, troca de chaves e assinatura. A encriptação RSA deve utilizar apenas os modos de enchimento OAEP ou RSA-KEM. O código existente pode utilizar o modo pKCS #1 v1.5 para compatibilidade. A utilização de acolchoamento nulo é expressamente proibida. As teclas >= 2048 bits são necessárias para um novo código. O código existente pode suportar as chaves < 2048 bits apenas para retrocompatibilidade após uma revisão pelo Crypto Board da sua organização. As teclas < 1024 bits só podem ser utilizadas para desencriptar/verificar dados antigos, e devem ser substituídas se forem utilizadas para operações de encriptação ou assinatura</li><li>**ECDSA-** só pode ser utilizado para assinatura. ECDSA com >chaves de 256 bits é necessária para novo código. As assinaturas baseadas no ECDSA devem utilizar uma das três curvas aprovadas pelo NIST (P-256, P-384 ou P521). As curvas que foram analisadas minuciosamente só podem ser usadas após uma revisão com o Crypto Board da sua organização.</li><li>**A ECDH-** só pode ser utilizada para a troca de chaves. O ECDH com >teclas de 256 bits é necessário para um novo código. A troca de chaves baseada no ECDH deve utilizar uma das três curvas aprovadas pela NIST (P-256, P-384 ou P521). As curvas que foram analisadas minuciosamente só podem ser usadas após uma revisão com o Crypto Board da sua organização.</li><li>**A DSA** pode ser aceitável após revisão e aprovação do Crypto Board da sua organização. Contacte o seu conselheiro de segurança para agendar a revisão do Crypto Board da sua organização. Se a sua utilização de DSA for aprovada, note que terá de proibir o uso de teclas com menos de 2048 bits de comprimento. O CNG suporta comprimentos de 2048 bits e maiores comprimentos chave a partir do Windows 8.</li><li>**Diffie-Hellman** pode ser usado apenas para gestão de chaves de sessão. O comprimento da chave >= 2048 bits é necessário para um novo código. O código existente pode suportar comprimentos chave < 2048 bits apenas para retrocompatibilidade após uma revisão pelo Crypto Board da sua organização. As teclas < 1024 bits não podem ser utilizadas.</li><ul>

## <a name="use-approved-random-number-generators"></a><a id="numgen"></a>Utilize geradores de númeroaleatórios aleatórios aprovados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Os produtos devem utilizar geradores de números aleatórios aprovados. Funções pseudoaleatórias como o rand da função de tempo de funcionamento C, o Sistema de Classe-Quadro .NET.Random ou funções do sistema tais como GetTickCount nunca devem, portanto, ser usadas neste código. É proibida a utilização do algoritmo de gerador de números aleatório selepíptico (DUAL_EC_DRBG) de curva dupla (DUAL_EC_DRBG)</p><ul><li>**CNG-** BCryptGenRandom (utilização da bandeira BCRYPT_USE_SYSTEM_PREFERRED_RNG recomendada a menos que o chamador possa funcionar em qualquer IRQL superior a 0 [isto é, PASSIVE_LEVEL])</li><li>**CAPI-criptogenRandom**</li><li>**Win32/64-** RtlGenRandom (novas implementações devem usar BCryptGenRandom ou CryptGenRandom) * rand_s * SystemPrng (para o modo kernel)</li><li>**. NET-** RNGCryptoServiceProvider ou RNGCng</li><li>**Aplicativos windows store-** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom ou . Gerar Número Aleatório</li><li>**Apple OS X (10.7+)/iOS (2.0+)-** int SecRandomCopyBytes (SecRandomRef random, size_t count, uint8_t \*bytes)</li><li>**Apple OS X (<10.7)-** Utilizar /dev/random para recuperar números aleatórios</li><li>**Java (incluindo o código Google Android Java)-** java.security.SecureRandom class. Note que para o Android 4.3 (Jelly Bean), os desenvolvedores devem seguir a seleção recomendada pelo Android e atualizar as suas aplicações para inicializar explicitamente o PRNG com entropia de /dev/urandom ou /dev/random</li></ul>|

## <a name="do-not-use-symmetric-stream-ciphers"></a><a id="stream-ciphers"></a>Não utilize cifras de fluxo simétricos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | As cifras de fluxo simétrico, como a RC4, não devem ser utilizadas. Em vez de cifras de fluxo simétricos, os produtos devem utilizar uma cifra de bloco, especificamente AES com um comprimento chave de pelo menos 128 bits. |

## <a name="use-approved-machmackeyed-hash-algorithms"></a><a id="mac-hash"></a>Utilize algoritmos de haxixe MAC/HMAC/chave aprovados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Os produtos devem utilizar apenas algoritmos de autenticação de mensagens aprovadas (MAC) ou código de autenticação de mensagens baseadas em haxixe (HMAC).</p><p>Um código de autenticação de mensagens (MAC) é uma informação anexa da mensagem que permite ao seu destinatário verificar tanto a autenticidade do remetente como a integridade da mensagem utilizando uma chave secreta. A utilização de um MAC baseado em haxixe[(HMAC)](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)ou [mac baseado em cifra](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) si é permitida desde que todos os algoritmos de encriptação de haxixe ou simétrico simétrico simétrico simétrico simétrico sufiram para utilização; Atualmente, isto inclui as funções HMAC-SHA2 (HMAC-SHA256, HMAC-SHA384 e HMAC-SHA512) e os MACs baseados em cifras de bloco CMAC/OMAC1 (estes são baseados em AES).</p><p>A utilização de HMAC-SHA1 pode ser permitida para a compatibilidade da plataforma, mas será obrigado a apresentar uma exceção a este procedimento e a submeter-se à revisão crypto da sua organização. Não é permitida a truncação de HMACs a menos de 128 bits. A utilização dos métodos do cliente para hash uma chave e os dados não são aprovados, e devem ser submetidos à revisão do Crypto Board da sua organização antes da sua utilização.</p>|

## <a name="use-only-approved-cryptographic-hash-functions"></a><a id="hash-functions"></a>Utilize apenas funções de haxixe criptográfico aprovadas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Os produtos devem usar a família SHA-2 de algoritmos de haxixe (SHA256, SHA384 e SHA512). Se for necessário um hash mais curto, como um comprimento de saída de 128 bits para encaixar uma estrutura de dados desenhada com o hash MD5 mais curto em mente, as equipas de produtos podem truncar um dos hashes SHA2 (tipicamente SHA256). Note que SHA384 é uma versão truncada de SHA512. Não é permitida a truncação de hastes criptográficas para fins de segurança a menos de 128 bits. O novo código não deve utilizar os algoritmos md2, MD4, MD5, SHA-0, SHA-1 ou RIPEMD. As colisões de hash são computacionalmente viáveis para estes algoritmos, que efetivamente os quebra.</p><p>Algoritmos de haxixe permitidos .NET para agilidade cripto gerida (por ordem de preferência):</p><ul><li>SHA512Cng (conformidade fips)</li><li>SHA384Cng (conformidade fips)</li><li>SHA256Cng (conformidade fips)</li><li>SHA512Managed (não compatível com FIPS) (use SHA512 como nome de algoritmo nas chamadas para HashAlgorithm.Create ou CryptoConfig.CreateFromName)</li><li>SHA384Managed (não compatível com FIPS) (use SHA384 como nome de algoritmo nas chamadas para HashAlgorithm.Create ou CryptoConfig.CreateFromName)</li><li>SHA256Managed (não compatível com FIPS) (use SHA256 como nome de algoritmo nas chamadas para HashAlgorithm.Create ou CryptoConfig.CreateFromName)</li><li>SHA512CryptoServiceProvider (conformidade com o FIPS)</li><li>SHA256CryptoServiceProvider (conformidade com o FIPS)</li><li>SHA384CryptoServiceProvider (conformidade com o FIPS)</li></ul>| 

## <a name="use-strong-encryption-algorithms-to-encrypt-data-in-the-database"></a><a id="strong-db"></a>Use algoritmos de encriptação fortes para encriptar dados na base de dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Escolher um algoritmo de encriptação](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Passos** | Os algoritmos de encriptação definem transformações de dados que não podem ser facilmente revertidas por utilizadores não autorizados. O SQL Server permite que administradores e desenvolvedores escolham entre vários algoritmos, incluindo DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, RC4 de 128 bits RC4, DESX, AES de 128 bits, AES de 192 bits e AES de 256 bits |

## <a name="ssis-packages-should-be-encrypted-and-digitally-signed"></a><a id="ssis-signed"></a>Os pacotes SSIS devem ser encriptados e assinados digitalmente

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Identificar a Fonte de Pacotes com Assinaturas Digitais,](https://msdn.microsoft.com/library/ms141174.aspx)Ameaça e Mitigação de [Vulnerabilidades (Serviços de Integração)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Passos** | A fonte de um pacote é o indivíduo ou organização que criou o pacote. Executar um pacote de uma fonte desconhecida ou não confiável pode ser arriscado. Para evitar a adulteração não autorizada dos pacotes SSIS, devem ser utilizadas assinaturas digitais. Além disso, para garantir a confidencialidade dos pacotes durante o armazenamento/trânsito, os pacotes SSIS têm de ser encriptados |

## <a name="add-digital-signature-to-critical-database-securables"></a><a id="securables-db"></a>Adicione a assinatura digital a securáveis de base de dados críticas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [ASSINATURA ADD (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Passos** | Nos casos em que a integridade de uma base de dados crítica é assegurável, devem ser utilizadas assinaturas digitais. Os securáveis da base de dados, tais como um procedimento, função, montagem ou gatilho armazenados, podem ser assinados digitalmente. Abaixo está um exemplo de quando isso pode ser útil: Digamos que um ISV (Fornecedor independente de Software) forneceu suporte a um software entregue a um dos seus clientes. Antes de fornecer suporte, o ISV quereria garantir que uma base de dados titular no software não fosse adulterada nem por engano nem por tentativa maliciosa. Se o titular for assinado digitalmente, o ISV pode verificar a sua assinatura digital e validar a sua integridade.| 

## <a name="use-sql-server-ekm-to-protect-encryption-keys"></a><a id="ekm-keys"></a>Utilize o servidor SQL EKM para proteger as chaves de encriptação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Gestão extensível de chave extensível do servidor SQL (EKM)](https://msdn.microsoft.com/library/bb895340), [Gestão extensível da chave utilizando o cofre da chave azure (Servidor SQL)](https://msdn.microsoft.com/library/dn198405) |
| **Passos** | A SQL Server Extensible Key Management permite que as chaves de encriptação que protegem os ficheiros de base de dados sejam armazenados num dispositivo off-box, como um smartcard, um dispositivo USB ou um módulo EKM/HSM. Isto também permite a proteção de dados dos administradores de bases de dados (exceto membros do grupo sysadmin). Os dados podem ser encriptados utilizando chaves de encriptação a que apenas o utilizador da base de dados tem acesso no módulo EKM/HSM externo. |

## <a name="use-alwaysencrypted-feature-if-encryption-keys-should-not-be-revealed-to-database-engine"></a><a id="keys-engine"></a>Utilizar a funcionalidade AlwaysEncrypted se as chaves de encriptação não forem reveladas ao motor Base de Dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | Versão SQL - V12, MsSQL2016 |
| **Referências**              | [Sempre Encriptado (Motor de Base de Dados)](https://msdn.microsoft.com/library/mt163865) |
| **Passos** | Sempre Encriptado é uma funcionalidade concebida para proteger dados sensíveis, tais como números de cartões de crédito ou números de identificação nacionais (por exemplo, números de segurança social dos EUA), armazenados em bases de dados Azure SQL ou SQL Server. Sempre encriptado permite que os clientes criptografem dados confidenciais dentro das aplicações do cliente e nunca revelem as chaves de encriptação do Motor base de dados (Base de Dados SQL ou Servidor SQL). Como resultado, a Always Encrypted proporciona uma separação entre aqueles que possuem os dados (e podem vê-los) e aqueles que gerem os dados (mas não devem ter acesso) |

## <a name="store-cryptographic-keys-securely-on-iot-device"></a><a id="keys-iot"></a>Armazenar chaves criptográficas de forma segura no dispositivo IoT

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | Dispositivo OS - Windows IoT Core, Conectividade do Dispositivo - SDKs dispositivo Azure IoT |
| **Referências**              | [TPM no Windows IoT Core](https://developer.microsoft.com/windows/iot/docs/tpm), [Configurar TPM no Windows IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/setuptpm), [Dispositivo Azure IoT SDK TPM](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Passos** | Chaves simétricas ou certificados Chaves privadas de forma segura num armazenamento protegido por hardware, como chips TPM ou Smart Card. O Windows 10 IoT Core suporta o utilizador de um TPM e existem https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpmvários TPMs compatíveis que podem ser utilizados: . Recomenda-se a utilização de um Firmware ou Discrete TPM. Um TPM de software só deve ser utilizado para fins de desenvolvimento e teste. Uma vez disponível um TPM e as chaves são aprovisionadas nele, o código que gera o símbolo deve ser escrito sem codificar duramente qualquer informação sensível nele. | 

### <a name="example"></a>Exemplo
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Como se pode ver, a chave primária do dispositivo não está presente no código. Em vez disso, é armazenado no TPM no slot 0. O dispositivo TPM gera um token SAS de curta duração que é então usado para ligar ao Hub IoT. 

## <a name="generate-a-random-symmetric-key-of-sufficient-length-for-authentication-to-iot-hub"></a><a id="random-hub"></a>Gerar uma chave simétrica aleatória de comprimento suficiente para autenticação ao Hub IoT

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway da nuvem iot | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | Escolha gateway - Azure IoT Hub |
| **Referências**              | N/D  |
| **Passos** | O IoT Hub contém um registo de identidade do dispositivo e, ao fornecer um dispositivo, gera automaticamente uma chave simétrica aleatória. Recomenda-se utilizar esta funcionalidade do Registo de Identidade do Hub Azure IoT para gerar a chave utilizada para a autenticação. O IoT Hub também permite especificar uma chave durante a criação do dispositivo. Se uma chave for gerada fora do IoT Hub durante o fornecimento do dispositivo, recomenda-se a criação de uma chave simétrica aleatória ou pelo menos 256 bits. |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-use-pin-and-allows-remote-wiping"></a><a id="pin-remote"></a>Certifique-se de que existe uma política de gestão de dispositivos que requer um PIN de utilização e permite a limpeza remota

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente Móvel Dynamics CRM | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que existe uma política de gestão de dispositivos que requer um PIN de utilização e permite a limpeza remota |

## <a name="ensure-a-device-management-policy-is-in-place-that-requires-a-pinpasswordauto-lock-and-encrypts-all-data-eg-bitlocker"></a><a id="bitlocker"></a>Certifique-se de que existe uma política de gestão de dispositivos que requer um pin/password/bloqueio automático e encripta todos os dados (por exemplo, BitLocker)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente de Outlook DE CRM Dynamics | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que existe uma política de gestão de dispositivos que requer um pin/password/bloqueio automático e encripta todos os dados (por exemplo, BitLocker) |

## <a name="ensure-that-signing-keys-are-rolled-over-when-using-identity-server"></a><a id="rolled-server"></a>Certifique-se de que as teclas de assinatura são reroladas ao utilizar o Servidor de Identidade

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Servidor de Identidade - Chaves, Assinaturas e Criptografia](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Passos** | Certifique-se de que as teclas de assinatura são reroladas ao utilizar o Servidor de Identidade. O link na secção de referências explica como este deve ser planeado sem causar interrupções em aplicações que dependem do Servidor de Identidade. |

## <a name="ensure-that-cryptographically-strong-client-id-client-secret-are-used-in-identity-server"></a><a id="client-server"></a>Certifique-se de que o ID do cliente criptograficamente forte, o segredo do cliente é usado no Servidor de Identidade

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Certifique-se de que o ID do cliente, o segredo do cliente é usado no Servidor de Identidade. As seguintes orientações devem ser utilizadas enquanto geram uma identificação e um segredo do cliente:</p><ul><li>Gerar um GUID aleatório como o ID do cliente</li><li>Gere uma chave criptograficamente aleatória de 256 bits como o segredo</li></ul>|
