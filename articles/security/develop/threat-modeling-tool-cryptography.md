---
title: Cryptography-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: mitigações para ameaças expostas no Threat Modeling Tool
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
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728194"
---
# <a name="security-frame-cryptography--mitigations"></a>Quadro de segurança: Criptografia | Mitigações 

| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Aplicativo Web** | <ul><li>[Usar somente codificações de bloco simétrico aprovadas e comprimentos de chave](#cipher-length)</li><li>[Usar modos de codificação de bloco aprovados e vetores de inicialização para codificações simétricas](#vector-ciphers)</li><li>[Usar algoritmos assimétricos aprovados, comprimentos de chave e preenchimento](#padding)</li><li>[Usar geradores de números aleatórios aprovados](#numgen)</li><li>[Não usar codificações de fluxo simétrico](#stream-ciphers)</li><li>[Usar algoritmos de hash MAC/HMAC/chave aprovados](#mac-hash)</li><li>[Usar somente funções de hash de criptografia aprovadas](#hash-functions)</li></ul> |
| **Base de Dados** | <ul><li>[Usar algoritmos de criptografia fortes para criptografar dados no banco de dado](#strong-db)</li><li>[Os pacotes SSIS devem ser criptografados e assinados digitalmente](#ssis-signed)</li><li>[Adicionar assinatura digital a protegíveis de banco de dados crítico](#securables-db)</li><li>[Usar EKM do SQL Server para proteger chaves de criptografia](#ekm-keys)</li><li>[Usar o recurso AlwaysEncrypted se as chaves de criptografia não devem ser reveladas ao mecanismo de banco de dados](#keys-engine)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Armazenar chaves criptográficas com segurança no dispositivo IoT](#keys-iot)</li></ul> | 
| **Gateway de nuvem IoT** | <ul><li>[Gerar uma chave simétrica aleatória de comprimento suficiente para autenticação no Hub IoT](#random-hub)</li></ul> | 
| **Cliente móvel do Dynamics CRM** | <ul><li>[Verifique se uma política de gerenciamento de dispositivo está em vigor que requer um PIN de uso e permite apagamento remoto](#pin-remote)</li></ul> | 
| **Cliente do Outlook do Dynamics CRM** | <ul><li>[Verifique se uma política de gerenciamento de dispositivo está em vigor que requer PIN/senha/bloqueio automático e criptografa todos os dados (por exemplo, o BitLocker)](#bitlocker)</li></ul> | 
| **Servidor de identidade** | <ul><li>[Verifique se as chaves de assinatura são transferidas ao usar o servidor de identidade](#rolled-server)</li><li>[Garantir que a ID do cliente forte criptograficamente, o segredo do cliente sejam usados no servidor de identidade](#client-server)</li></ul> | 

## <a id="cipher-length"></a>Usar somente codificações de bloco simétrico aprovadas e comprimentos de chave

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Os produtos devem usar apenas as codificações de bloco simétricas e os comprimentos de chave associados que foram explicitamente aprovados pelo supervisor de criptografia em sua organização. Os algoritmos simétricos aprovados na Microsoft incluem as seguintes codificações de bloco:</p><ul><li>Para o novo código, AES-128, AES-192 e AES-256 são aceitáveis</li><li>Para compatibilidade com versões anteriores com o código existente, o 3DES de três chaves é aceitável</li><li>Para produtos que usam codificações de bloco simétricas:<ul><li>O criptografia AES (AES) é necessário para o novo código</li><li>O padrão de criptografia de dados triplo de três teclas (3DES) é permitido no código existente para compatibilidade com versões anteriores</li><li>Todas as outras codificações de bloco, incluindo RC2, DES, 2 chaves 3DES, DESX e Skipjack, só podem ser usadas para descriptografar dados antigos e devem ser substituídas se forem usadas para criptografia</li></ul></li><li>Para algoritmos de criptografia de bloco simétrico, é necessário um comprimento mínimo de chave de 128 bits. O único algoritmo de criptografia de bloco recomendado para o novo código é AES (AES-128, AES-192 e AES-256 são todos aceitáveis)</li><li>O 3DES de três teclas é aceitável no momento, se já estiver em uso no código existente; a transição para AES é recomendada. O DES, o DESX, o RC2 e o Skipjack não são mais considerados seguros. Esses algoritmos só podem ser usados para descriptografar dados existentes para fins de compatibilidade com versões anteriores, e os dados devem ser criptografados novamente usando uma codificação de bloco recomendada</li></ul><p>Observe que todas as codificações de bloco simétricas devem ser usadas com um modo de codificação aprovado, que requer o uso de um vetor de inicialização (IV) apropriado. Um IV apropriado, normalmente é um número aleatório e nunca um valor constante</p><p>O uso de algoritmos de criptografia herdados ou não aprovados e comprimentos de chave menores para a leitura de dados existentes (em oposição à gravação de novos dados) pode ser permitido após a revisão da placa de criptografia da sua organização. No entanto, você deve arquivar uma exceção contra esse requisito. Além disso, em implantações empresariais, os produtos devem considerar os administradores de aviso quando a criptografia fraca é usada para ler dados. Esses avisos devem ser explicativos e acionáveis. Em alguns casos, pode ser apropriado ter Política de Grupo controlar o uso de criptografia fraca</p><p>Algoritmos .NET permitidos para agilidade de criptografia gerenciada (em ordem de preferência)</p><ul><li>AesCng (em conformidade com FIPS)</li><li>AuthenticatedAesCng (em conformidade com FIPS)</li><li>AESCryptoServiceProvider (em conformidade com FIPS)</li><li>AESManaged (sem conformidade com FIPS)</li></ul><p>Observe que nenhum desses algoritmos pode ser especificado por meio dos `SymmetricAlgorithm.Create` métodos `CryptoConfig.CreateFromName` ou sem fazer alterações no arquivo Machine. config. Além disso, observe que o AES em versões do .net anteriores ao .net 3,5 `RijndaelManaged`é nomeado `AesCng` e `AuthenticatedAesCng` e estão > disponíveis por meio do CodePlex e exigem a CNG no sistema operacional subjacente</p>

## <a id="vector-ciphers"></a>Usar modos de codificação de bloco aprovados e vetores de inicialização para codificações simétricas

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Todas as codificações de bloco simétrico devem ser usadas com um modo de codificação simétrica aprovado. Os únicos modos aprovados são CBC e CTS. Em particular, o modo de ECB (livro de código eletrônico) da operação deve ser evitado; o uso de ECB requer a revisão da placa de criptografia da sua organização. Todo o uso de OFB, CFB, CTR, CCM e GCM ou qualquer outro modo de criptografia deve ser revisado pela placa de criptografia da sua organização. Reutilizar o mesmo vetor de inicialização (IV) com codificações de bloco em "modos de codificação de streaming", como CTR, pode fazer com que os dados criptografados sejam revelados. Todas as codificações de bloco simétrico também devem ser usadas com um vetor de inicialização (IV) apropriado. Um IV apropriado é um número aleatório criptograficamente forte e nunca um valor constante. |

## <a id="padding"></a>Usar algoritmos assimétricos aprovados, comprimentos de chave e preenchimento

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>O uso de algoritmos criptográficos proibidos introduz um risco significativo à segurança do produto e deve ser evitado. Os produtos devem usar somente os algoritmos criptográficos e os comprimentos de chave associados e o preenchimento que foram explicitamente aprovados pela placa de criptografia da sua organização.</p><ul><li>**RSA –** pode ser usado para criptografia, troca de chaves e assinatura. A criptografia RSA deve usar apenas os modos de preenchimento OAEP ou RSA-KEM. O código existente pode usar o modo de preenchimento PKCS #1 v 1.5 somente para fins de compatibilidade. O uso de preenchimento nulo é explicitamente banido. As chaves > = 2048 bits são necessárias para o novo código. O código existente pode dar suporte a chaves < bits 2048 somente para compatibilidade com versões anteriores após uma análise por placa de criptografia da sua organização. Chaves < 1024 bits só podem ser usadas para descriptografar/verificar dados antigos e devem ser substituídas se usadas para operações de criptografia ou assinatura</li><li>**ECDSA-** pode ser usado somente para assinatura. Os ECDSA com > = chaves de 256 bits são necessárias para o novo código. As assinaturas baseadas em ECDSA devem usar uma das três curvas aprovadas do NIST (P-256, P-384 ou P521). As curvas que foram analisadas minuciosamente podem ser usadas somente após uma revisão com a placa de criptografia da sua organização.</li><li>**ECDH-** pode ser usado somente para troca de chaves. ECDH com > = chaves de 256 bits são necessárias para o novo código. A troca de chaves com base em ECDH deve usar uma das três curvas aprovadas do NIST (P-256, P-384 ou P521). As curvas que foram analisadas minuciosamente podem ser usadas somente após uma revisão com a placa de criptografia da sua organização.</li><li>**DSA-** pode ser aceitável após a revisão e a aprovação da placa de criptografia da sua organização. Entre em contato com seu supervisor de segurança para agendar a revisão da placa de criptografia da sua organização. Se o uso do DSA for aprovado, observe que será necessário proibir o uso de chaves com menos de 2048 bits de comprimento. A CNG dá suporte a comprimentos de chave de 2048 bits e maiores a partir do Windows 8.</li><li>O **Diffie-Hellman-** pode ser usado somente para gerenciamento de chaves de sessão. O comprimento da chave > = 2048 bits é necessário para o novo código. O código existente pode dar suporte a comprimentos de chave < bits de 2048 somente para compatibilidade com versões anteriores após uma análise por placa de criptografia da sua organização. Chaves < 1024 bits não podem ser usadas.</li><ul>

## <a id="numgen"></a>Usar geradores de números aleatórios aprovados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Os produtos devem usar geradores de números aleatórios aprovados. Funções pseudoaleatória como a função Rand de tempo de execução C, a classe .NET Framework System. Random ou funções do sistema, como o ObterContagemMarcaEscala, devem, portanto, nunca ser usadas nesse código. O uso do algoritmo gerador de número aleatório (DUAL_EC_DRBG) de curva elíptica dupla é proibido</p><ul><li>**CNG-** BCryptGenRandom (use o sinalizador BCRYPT_USE_SYSTEM_PREFERRED_RNG recomendado, a menos que o chamador possa ser executado em qualquer IRQL maior que 0 [ou seja, PASSIVE_LEVEL])</li><li>**CAPI-** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (as novas implementações devem usar BCryptGenRandom ou CryptGenRandom) * rand_s * SystemPrng (para o modo kernel)</li><li>**. NET-** RNGCryptoServiceProvider ou RNGCng</li><li>**Aplicativos da Windows Store-** Windows. Security. Cryptography. CryptographicBuffer. GenerateRandom ou. GenerateRandomNumber</li><li>**Apple os X (10.7 +)/Ios (2.0 +)-** int SecRandomCopyBytes (SecRandomRef aleatório, a contagem de size_t \*, uint8_t bytes)</li><li>**Apple os X (< 10.7) –** Usar/dev/random para recuperar números aleatórios</li><li>**Java (incluindo o código Java do Google Android)-** classe Java. Security. SecureRandom. Observe que para Android 4,3 (Jelly Bean), os desenvolvedores devem seguir a solução alternativa recomendada do Android e atualizar seus aplicativos para inicializar explicitamente o PRNG com entropia de/dev/urandom ou/dev/random</li></ul>|

## <a id="stream-ciphers"></a>Não usar codificações de fluxo simétrico

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | As codificações de fluxo simétrico, como RC4, não devem ser usadas. Em vez de codificações de fluxo simétricas, os produtos devem usar uma codificação de bloco, especificamente AES com um comprimento de chave de pelo menos 128 bits. |

## <a id="mac-hash"></a>Usar algoritmos de hash MAC/HMAC/chave aprovados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Os produtos devem usar somente os algoritmos de código de autenticação de mensagem (MAC) ou de código de autenticação de mensagem (HMAC) aprovados.</p><p>Um MAC (código de autenticação de mensagens) é uma informação anexada a uma mensagem que permite que seu destinatário Verifique a autenticidade do remetente e a integridade da mensagem usando uma chave secreta. O uso de um MAC baseado em hash ([HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) ou de um [Mac baseado em criptografia de bloco](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) é permitido, desde que todos os algoritmos de hash subjacentes ou de criptografia simétrica também sejam aprovados para uso; Atualmente, isso inclui as funções HMAC-SHA2 (HMAC-SHA256, HMAC-SHA384 e HMAC-SHA512) e CMAC/OMAC1 e OMAC2 de MACs com base em codificação de bloco (eles são baseados no AES).</p><p>O uso de HMAC-SHA1 pode ser permitido para compatibilidade de plataforma, mas será necessário arquivar uma exceção para esse procedimento e passar pela revisão de criptografia da sua organização. O truncamento de HMACs para menos de 128 bits não é permitido. O uso de métodos de cliente para aplicar hash a uma chave e os dados não é aprovado e deve passar pela revisão da placa de criptografia da sua organização antes do uso.</p>|

## <a id="hash-functions"></a>Usar somente funções de hash de criptografia aprovadas

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Os produtos devem usar a família SHA-2 de algoritmos de hash (SHA256, SHA384 e SHA512). Se for necessário um hash mais curto, como um comprimento de saída de 128 bits para se ajustar a uma estrutura de dados criada com o hash MD5 mais curto em mente, as equipes de produto poderão truncar um dos hashes SHA2 (normalmente SHA256). Observe que SHA384 é uma versão truncada do SHA512. O truncamento de hashes criptográficos para fins de segurança com menos de 128 bits não é permitido. O novo código não deve usar os algoritmos de hash MD2, MD4, MD5, SHA-0, SHA-1 ou RIPEMD. As colisões de hash são computacionalmente viáveis para esses algoritmos, o que os quebra efetivamente.</p><p>Algoritmos de hash .NET permitidos para agilidade de criptografia gerenciada (em ordem de preferência):</p><ul><li>SHA512Cng (em conformidade com FIPS)</li><li>SHA384Cng (em conformidade com FIPS)</li><li>SHA256Cng (em conformidade com FIPS)</li><li>SHA512Managed (sem conformidade com FIPS) (use SHA512 como nome do algoritmo em chamadas para HashAlgorithm. Create ou CryptoConfig. CreateFromName)</li><li>SHA384Managed (sem conformidade com FIPS) (use SHA384 como nome do algoritmo em chamadas para HashAlgorithm. Create ou CryptoConfig. CreateFromName)</li><li>SHA256Managed (sem conformidade com FIPS) (use SHA256 como nome do algoritmo em chamadas para HashAlgorithm. Create ou CryptoConfig. CreateFromName)</li><li>SHA512CryptoServiceProvider (em conformidade com FIPS)</li><li>SHA256CryptoServiceProvider (em conformidade com FIPS)</li><li>SHA384CryptoServiceProvider (em conformidade com FIPS)</li></ul>| 

## <a id="strong-db"></a>Usar algoritmos de criptografia fortes para criptografar dados no banco de dado

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Escolhendo um algoritmo de criptografia](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Passos** | Os algoritmos de criptografia definem as transformações de dados que não podem ser facilmente revertidas por usuários não autorizados. SQL Server permite que administradores e desenvolvedores escolham entre vários algoritmos, incluindo DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, RC4 de 128 bits, DESX, AES de 128 bits, AES de 192 bits e AES de 256 bits |

## <a id="ssis-signed"></a>Os pacotes SSIS devem ser criptografados e assinados digitalmente

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Identificar a fonte de pacotes com assinaturas digitais](https://msdn.microsoft.com/library/ms141174.aspx), [ameaça e mitigação de vulnerabilidades (Integration Services)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Passos** | A origem de um pacote é a pessoa ou organização que criou o pacote. A execução de um pacote de uma fonte desconhecida ou não confiável pode ser arriscada. Para evitar a violação não autorizada de pacotes do SSIS, as assinaturas digitais devem ser usadas. Além disso, para garantir a confidencialidade dos pacotes durante o armazenamento/trânsito, os pacotes do SSIS precisam ser criptografados |

## <a id="securables-db"></a>Adicionar assinatura digital a protegíveis de banco de dados crítico

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Adicionar assinatura (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Passos** | Nos casos em que a integridade de um protegível de banco de dados crítico precisa ser verificada, as assinaturas digitais devem ser usadas. Os protegíveis de banco de dados, como um procedimento armazenado, uma função, um assembly ou um gatilho, podem ser assinados digitalmente. Veja abaixo um exemplo de quando isso pode ser útil: Digamos que um ISV (fornecedor independente de software) tenha fornecido suporte a um software entregue a um de seus clientes. Antes de fornecer suporte, o ISV desejaria garantir que um protegível de banco de dados no software não fosse violado por engano ou por uma tentativa mal-intencionada. Se o protegível for assinado digitalmente, o ISV poderá verificar sua assinatura digital e validar sua integridade.| 

## <a id="ekm-keys"></a>Usar EKM do SQL Server para proteger chaves de criptografia

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [SQL Server EKM (gerenciamento extensível de chaves)](https://msdn.microsoft.com/library/bb895340), [gerenciamento extensível de chaves usando Azure Key Vault (SQL Server)](https://msdn.microsoft.com/library/dn198405) |
| **Passos** | SQL Server o gerenciamento extensível de chaves permite que as chaves de criptografia protejam os arquivos de banco de dados a serem armazenados em um dispositivo não-Box, como um cartão inteligente, dispositivo USB ou EKM/HSM. Isso também permite a proteção de dados de administradores de banco (exceto membros do grupo sysadmin). Os dados podem ser criptografados usando chaves de criptografia às quais somente o usuário do banco de dado tem acesso no módulo EKM/HSM externo. |

## <a id="keys-engine"></a>Usar o recurso AlwaysEncrypted se as chaves de criptografia não devem ser reveladas ao mecanismo de banco de dados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | Versão do SQL-V12, MsSQL2016 |
| **Referências**              | [Always Encrypted (Mecanismo de Banco de Dados)](https://msdn.microsoft.com/library/mt163865) |
| **Passos** | Always Encrypted é um recurso projetado para proteger dados confidenciais, como números de cartão de crédito ou números de identificação nacional (por exemplo, números de previdência social dos EUA), armazenados no banco de dados SQL do Azure ou em bancos de SQL Server. Always Encrypted permite que os clientes criptografem dados confidenciais dentro de aplicativos cliente e nunca revelem as chaves de criptografia para a Mecanismo de Banco de Dados (banco de dados SQL ou SQL Server). Como resultado, Always Encrypted fornece uma separação entre aqueles que possuem os dados (e pode exibi-los) e aqueles que gerenciam os dados (mas não devem ter acesso) |

## <a id="keys-iot"></a>Armazenar chaves criptográficas com segurança no dispositivo IoT

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Sistema operacional do dispositivo-Windows IoT Core, conectividade do dispositivo-SDKs do dispositivo IoT do Azure |
| **Referências**              | [TPM no Windows IOT Core](https://developer.microsoft.com/windows/iot/docs/tpm), [Configurar o TPM no Windows IOT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/setuptpm), [TPM do SDK do dispositivo IOT do Azure](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Passos** | Chaves simétricas ou privadas de certificado com segurança em um armazenamento protegido por hardware, como o TPM ou chips de cartão inteligente. O Windows 10 IoT Core dá suporte ao usuário de um TPM e há vários TPMs compatíveis que podem ser usados https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm:. É recomendável usar um firmware ou um TPM discreto. Um TPM de software deve ser usado somente para fins de desenvolvimento e teste. Quando um TPM está disponível e as chaves são provisionadas nele, o código que gera o token deve ser gravado sem a codificação de informações confidenciais nele. | 

### <a name="example"></a>Exemplo
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Como pode ser visto, a chave primária do dispositivo não está presente no código. Em vez disso, ele é armazenado no TPM no slot 0. O dispositivo TPM gera um token SAS de curta duração que é usado para se conectar ao Hub IoT. 

## <a id="random-hub"></a>Gerar uma chave simétrica aleatória de comprimento suficiente para autenticação no Hub IoT

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Opção de gateway-Hub IoT do Azure |
| **Referências**              | N/A  |
| **Passos** | O Hub IoT contém um registro de identidade do dispositivo e, ao provisionar um dispositivo, gera automaticamente uma chave simétrica aleatória. É recomendável usar esse recurso do registro de identidade do Hub IoT do Azure para gerar a chave usada para autenticação. O Hub IoT também permite que uma chave seja especificada durante a criação do dispositivo. Se uma chave for gerada fora do Hub IoT durante o provisionamento do dispositivo, é recomendável criar uma chave simétrica aleatória ou pelo menos 256 bits. |

## <a id="pin-remote"></a>Verifique se uma política de gerenciamento de dispositivo está em vigor que requer um PIN de uso e permite apagamento remoto

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente móvel do Dynamics CRM | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Verifique se uma política de gerenciamento de dispositivo está em vigor que requer um PIN de uso e permite apagamento remoto |

## <a id="bitlocker"></a>Verifique se uma política de gerenciamento de dispositivo está em vigor que requer PIN/senha/bloqueio automático e criptografa todos os dados (por exemplo, o BitLocker)

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente do Outlook do Dynamics CRM | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Verifique se uma política de gerenciamento de dispositivo está em vigor que requer PIN/senha/bloqueio automático e criptografa todos os dados (por exemplo, o BitLocker) |

## <a id="rolled-server"></a>Verifique se as chaves de assinatura são transferidas ao usar o servidor de identidade

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidade | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Servidor de identidade-chaves, assinaturas e criptografia](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Passos** | Verifique se as chaves de assinatura são transferidas ao usar o servidor de identidade. O link na seção de referências explica como isso deve ser planejado sem causar interrupções aos aplicativos que dependem do servidor de identidade. |

## <a id="client-server"></a>Garantir que a ID do cliente forte criptograficamente, o segredo do cliente sejam usados no servidor de identidade

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidade | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Verifique se a ID de cliente criptograficamente forte e o segredo do cliente são usados no servidor de identidade. As diretrizes a seguir devem ser usadas ao gerar uma ID do cliente e um segredo:</p><ul><li>Gerar um GUID aleatório como a ID do cliente</li><li>Gerar uma chave criptograficamente aleatória de 256 bits como o segredo</li></ul>|
