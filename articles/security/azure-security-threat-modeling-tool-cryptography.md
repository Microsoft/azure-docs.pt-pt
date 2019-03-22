---
title: Criptografia - ferramenta de modelagem de ameaças da Microsoft - Azure | Documentos da Microsoft
description: atenuações para ameaças expostas na ferramenta de modelagem de ameaças
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 608792d8389a87bad3521d3a48947b20dd036d67
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57887107"
---
# <a name="security-frame-cryptography--mitigations"></a>Quadro de segurança: Criptografia | Atenuações 

| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Aplicação Web** | <ul><li>[Utilizar apenas as codificações de bloco simétrica aprovados e comprimentos de chave](#cipher-length)</li><li>[Uso aprovado modos de codificação de bloco e de vetores de inicialização para cifras simétricas](#vector-ciphers)</li><li>[Uso aprovado assimétricos algoritmos, comprimentos de chave e preenchimento](#padding)</li><li>[Uso aprovado geradores de números aleatórios](#numgen)</li><li>[Não utilize cifras stream simétrica](#stream-ciphers)</li><li>[Utilize algoritmos de hash MAC/HMAC/codificadas aprovados](#mac-hash)</li><li>[Utilizar apenas as funções de hash criptográficos aprovados](#hash-functions)</li></ul> |
| **Base de Dados** | <ul><li>[Utilize algoritmos de criptografia forte para encriptar os dados na base de dados](#strong-db)</li><li>[Pacotes do SSIS devem ser criptografados e assinados digitalmente](#ssis-signed)</li><li>[Adicione a assinatura digital para capacidades de segurança de bases de dados fundamentais](#securables-db)</li><li>[Utilizar o SQL server EKM para proteger chaves de encriptação](#ekm-keys)</li><li>[Utilizar a funcionalidade de AlwaysEncrypted se as chaves de encriptação não devem ser reveladas ao motor de base de dados](#keys-engine)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Store chaves criptográficas com segurança no dispositivo de IoT](#keys-iot)</li></ul> | 
| **Gateway de Cloud da IoT** | <ul><li>[Gerar uma chave simétrica aleatória de tamanho suficiente para a autenticação para o IoT Hub](#random-hub)</li></ul> | 
| **Cliente móvel do Dynamics CRM** | <ul><li>[Certifique-se de que é uma política de gestão de dispositivos no local que requer uma utilização PIN e permite remoto limpar](#pin-remote)</li></ul> | 
| **Cliente do Dynamics CRM Outlook** | <ul><li>[Certifique-se que uma política de gestão do dispositivo está num local que requer um bloqueio PIN/palavra-passe/automático e encripta todos os dados (por exemplo, o BitLocker)](#bitlocker)</li></ul> | 
| **Servidor de identidades** | <ul><li>[Certifique-se de que as chaves de assinatura são agregadas ao longo ao utilizar o servidor de identidades](#rolled-server)</li><li>[Certifique-se de que o ID de cliente criptograficamente fortes, segredo do cliente são utilizadas no servidor de identidades](#client-server)</li></ul> | 

## <a id="cipher-length"></a>Utilizar apenas as codificações de bloco simétrica aprovados e comprimentos de chave

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Produtos tem de utilizar apenas os codificações em bloco simétrica e comprimentos de chave associados que foram aprovados explicitamente pelo Advisor Crypto na sua organização. Algoritmos simétricos aprovados na Microsoft incluem as seguintes codificações de bloco:</p><ul><li>Para o novo código de AES-128, AES 192 e AES-256 são aceitáveis</li><li>Para fins de compatibilidade com o código existente, é aceitável três-chave 3DES</li><li>Para os produtos usando as codificações de bloco simétrica:<ul><li>Advanced Encryption Standard (AES) é necessário para o novo código</li><li>Chave de três triple Data Encryption Standard (3DES) é permitido no código existente para compatibilidade com versões anteriores</li><li>Todas as outras codificações de bloco, incluindo RC2, DES, 2 chave 3DES, DESX e Skipjack, só podem ser utilizadas para desencriptar os dados antigos e têm de ser substituídas se utilizada para encriptação</li></ul></li><li>Para algoritmos de encriptação simétrica bloco, um comprimento de chave mínimo de 128 bits, é necessário. O algoritmo de encriptação de bloco único recomendado para o novo código é AES (AES-128, AES 192 e AES-256 são aceitáveis tudo)</li><li>Três-chave 3DES é atualmente aceitável se já estiver em uso no código existente; Recomenda-se a transição para o AES. DES, DESX, RC2 e Skipjack já não são consideradas seguras. Esses algoritmos só podem ser utilizados para desencriptar os dados existentes para fins de compatibilidade com versões anteriores e dados devem ser encriptados de voltar a utilizar uma cifra de bloco recomendada</li></ul><p>Tenha em atenção que todas as codificações de bloco simétrica tem de ser utilizadas com um modo de cifra aprovados, o que requer a utilização de um vetor de inicialização apropriado (IV). Um IV adequado, normalmente, é um número aleatório e não um valor constante</p><p>A utilização de legado ou de outra forma não aprovados algoritmos criptográficos e comprimentos de chave de menores para a leitura de dados existentes (em vez de escrever dados novos) pode ter permissão depois de rever o quadro de criptografia da sua organização. No entanto, tem de ficheiros para uma exceção em relação a esse requisito. Além disso, em Implantações corporativas, produtos devem considerar os administradores de aviso quando criptografia fraca é usada para ler os dados. Tais avisos devem ser explicativo e passíveis de ação. Em alguns casos, poderá ser apropriado para que a política de grupo, controlar a utilização de criptografia fraca</p><p>Permitido algoritmos de .NET para agilidade criptográfica gerido (por ordem de preferência)</p><ul><li>AesCng (compatível com FIPS)</li><li>AuthenticatedAesCng (compatível com FIPS)</li><li>AESCryptoServiceProvider (compatível com FIPS)</li><li>AESManaged (não-compatíveis com FIPS)</li></ul><p>Tenha em atenção que nenhum desses algoritmos podem ser especificados através da `SymmetricAlgorithm.Create` ou `CryptoConfig.CreateFromName` métodos sem fazer alterações no arquivo Machine. config. Além disso, tenha em atenção que AES nas versões do .NET antes do .NET 3.5 com o nome `RijndaelManaged`, e `AesCng` e `AuthenticatedAesCng` são > disponível por meio do CodePlex e exigir CNG no sistema operacional subjacente</p>

## <a id="vector-ciphers"></a>Uso aprovado modos de codificação de bloco e de vetores de inicialização para cifras simétricas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Todas as codificações de bloco simétrica devem ser utilizadas com um modo de cifra simétrica aprovados. Os modos de aprovados apenas são CBC e o CTS. Em particular, o modo de livro (ECB) de código eletrônico da operação deve ser evitado; utilização de ECB, é necessário consultar de quadro de criptografia da sua organização. Toda a utilização de OFB, CFB, CTR, CCM e GCM ou qualquer outro modo de encriptação deve ser revisado pelo Conselho de criptografia da sua organização. Reutilizar o mesmo vetor de inicialização (IV) com as codificações de bloco em "transmissão em fluxo modos de cifras," como CTR, pode fazer com que os dados encriptados seja revelada. Todas as codificações de bloco simétrica também devem ser utilizadas com um vetor de inicialização apropriado (IV). Um IV apropriado é criptograficamente fortes, número aleatório e nunca um valor constante. |

## <a id="padding"></a>Uso aprovado assimétricos algoritmos, comprimentos de chave e preenchimento

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>A utilização de algoritmos criptográficos banidas apresenta um risco significativo para segurança de produto e deve ser evitada. Produtos tem de utilizar apenas os algoritmos criptográficos e comprimentos de chave e o preenchimento foram explicitamente aprovados pelo Conselho de criptografia da sua organização associados.</p><ul><li>**RSA -** podem ser utilizadas para encriptação, troca de chaves e assinatura. Criptografia RSA tem de utilizar apenas os modos de preenchimento OAEP ou RSA KEM. Código existente, pode utilizar v1.5 PKCS n. º 1 modo de apenas para compatibilidade de preenchimento. Utilização de preenchimento nulo explicitamente é excluída. Chaves de > = 2048 bits é necessário para o novo código. Código existente pode suportar chaves < 2048 bits apenas para efeitos compatibilidade após uma revisão pelo Conselho de criptografia da sua organização. Chaves de < a 1024 bits só pode ser utilizados para desencriptação/verificação de dados antigos e tem de ser substituído se utilizada para encriptação ou operações de assinatura</li><li>**ECDSA -** podem ser utilizadas para assinatura apenas. ECDSA com > = chaves de 256 bits é necessário para o novo código. Com base em ECDSA assinaturas tem de utilizar uma das três curvas NIST aprovado (p-256, p-384 ou P521). Curvas que foi minuciosamente analisadas podem ser utilizadas apenas após uma revisão com o quadro de criptografia da sua organização.</li><li>**ECDH -** podem ser utilizadas para apenas a troca de chaves. ECDH com > = chaves de 256 bits é necessário para o novo código. Troca de chaves com base em ECDH tem de utilizar uma das três curvas NIST aprovado (p-256, p-384 ou P521). Curvas que foi minuciosamente analisadas podem ser utilizadas apenas após uma revisão com o quadro de criptografia da sua organização.</li><li>**DSA -** pode ser aceitável após a revisão e aprovação do Conselho de criptografia da sua organização. Entre em contato com o supervisor de segurança para agendar a revisão de quadro de criptografia da sua organização. Se a utilização do DSA for aprovada, tenha em atenção que terá de proíbem a utilização de chaves de menos de 2048 bits de comprimento. A CNG oferece suporte a 2048 bits e maiores comprimentos de chave a partir do Windows 8.</li><li>**Diffie-Hellman -** podem ser utilizadas para a sessão gestão de chaves apenas. Comprimento da chave > = 2048 bits é necessário para o novo código. Código existente pode suportar comprimentos de chave < 2048 bits apenas para efeitos compatibilidade após uma revisão pelo Conselho de criptografia da sua organização. Chaves de < a 1024 bits não podem ser utilizados.</li><ul>

## <a id="numgen"></a>Uso aprovado geradores de números aleatórios

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Produtos tem de utilizar os geradores de números aleatórios aprovados. Pseudoaleatórios funções como o rand de função de tempo de execução do C, a classe Random do .NET Framework ou funções de sistema como GetTickCount por conseguinte, tem, nunca ser utilizadas em desse código. É proibido o uso do criptografia de curva elíptica dupla generator (DUAL_EC_DRBG) algoritmo de números aleatórios</p><ul><li>**A CNG -** BCryptGenRandom (utilize do sinalizador BCRYPT_USE_SYSTEM_PREFERRED_RNG recomendado, a menos que o chamador possa ser executadas em qualquer IRQL maior que 0 [ou seja, PASSIVE_LEVEL])</li><li>**CAPI -** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (novas implementações devem usar BCryptGenRandom ou CryptGenRandom) * rand_s * SystemPrng (para o modo de kernel)</li><li>**. NET -** RNGCryptoServiceProvider ou RNGCng</li><li>**Aplicações da Windows Store -** Windows.Security.Cryptography.CryptographicBuffer.GenerateRandom ou. GenerateRandomNumber</li><li>**Apple OS X (10.7+)/iOS(2.0+) -** int SecRandomCopyBytes (SecRandomRef aleatório, contagem de size_t, uint8_t \*bytes)</li><li>**Apple OS X (< 10.7)-** utilizar/desenvolvimento/aleatório para obter números aleatórios</li><li>**Java(including Google Android Java Code) -** java.security.SecureRandom classe. Tenha em atenção que para Android 4.3 (foram Bean), os desenvolvedores devem seguir o Android recomendado solução e atualizar seus aplicativos para inicializar explicitamente PRNG com entropia de /dev/urandom ou /dev/random</li></ul>|

## <a id="stream-ciphers"></a>Não utilize cifras stream simétrica

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Cifras stream simétrica, como RC4, não pode ser utilizadas. Em vez de cifras de stream simétrica, produtos devem utilizar um bloco cifrado, especificamente AES com um comprimento de chave de, pelo menos, 128 bits. |

## <a id="mac-hash"></a>Utilize algoritmos de hash MAC/HMAC/codificadas aprovados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Produtos tem de utilizar apenas aprovado, o código de autenticação de mensagem (MAC) ou algoritmos de código (HMAC) de autenticação de mensagens baseada em hash.</p><p>Um código de autenticação de mensagem (MAC) é uma informação anexados a uma mensagem que permite que seu destinatário verificar a autenticidade do remetente de ambos os e a integridade da mensagem com uma chave secreta. O uso de qualquer um de um MAC com base em hash ([HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) ou [bloco cifrado-com base em MAC](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) é permitido como desde que o hash de todas as subjacente ou a criptografia simétrica algoritmos também são aprovados para utilização; atualmente inclui as funções de HMAC SHA2 (HMAC-SHA256, HMAC SHA384 e HMAC-SHA512) e o CMAC/OMAC1 e OMAC2 bloqueiam os MACs de com base na codificação de mensagens em fila (eles se baseiam em AES).</p><p>Utilização de HMAC-SHA1 pode ser permitida para compatibilidade de plataforma, mas será solicitado a uma exceção a este procedimento de ficheiros e tem de passar pela revisão de criptografia da sua organização. Não é permitida a truncagem de HMACs para menos de 128 bits. Usando os métodos de hash de uma chave e os dados não está aprovada e devem passar por quadro de criptografia da sua organização ler antes de utilização do cliente.</p>|

## <a id="hash-functions"></a>Utilizar apenas as funções de hash criptográficos aprovados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Produtos tem de utilizar a família SHA-2 de algoritmos de hash (SHA256, SHA384 e SHA512). Se for necessário um valor de hash mais curto, por exemplo, um comprimento de saída de 128 bits para que uma estrutura de dados criada com o hash MD5 mais curto em mente, as equipes de produtos podem truncar um dos hashes de SHA2 (normalmente SHA256). Observe que SHA384 é uma versão truncada de SHA512. Não é permitida a truncagem de hashes criptográficos por motivos de segurança para menos de 128 bits. Novo código não tem de utilizar o MD2, MD4, MD5, SHA-0, SHA-1 ou algoritmos de hash RIPEMD. Colisões de hashes são viáveis em termos computacionais para esses algoritmos, que efetivamente quebra.</p><p>Permitido algoritmos de hash de .NET para agilidade criptográfica gerido (por ordem de preferência):</p><ul><li>SHA512Cng (compatível com FIPS)</li><li>SHA384Cng (compatível com FIPS)</li><li>SHA256Cng (compatível com FIPS)</li><li>SHA512Managed (não-compatíveis com FIPS) (utilize SHA512 como nome do algoritmo em chamadas para HashAlgorithm.Create ou CryptoConfig.CreateFromName)</li><li>SHA384Managed (não-compatíveis com FIPS) (utilize SHA384 como o nome do algoritmo em chamadas para HashAlgorithm.Create ou CryptoConfig.CreateFromName)</li><li>SHA256Managed (não-compatíveis com FIPS) (utilize SHA256 como nome do algoritmo em chamadas para HashAlgorithm.Create ou CryptoConfig.CreateFromName)</li><li>SHA512CryptoServiceProvider (compatível com FIPS)</li><li>SHA256CryptoServiceProvider (compatível com FIPS)</li><li>SHA384CryptoServiceProvider (compatível com FIPS)</li></ul>| 

## <a id="strong-db"></a>Utilize algoritmos de criptografia forte para encriptar os dados na base de dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Escolher um algoritmo de encriptação](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Passos** | Algoritmos de encriptação definem transformações de dados que não podem ser invertidas facilmente por utilizadores não autorizados. SQL Server permite que os administradores e desenvolvedores por onde escolher entre vários algoritmos, incluindo DES, DES triplo, TRIPLE_DES_3KEY, RC2, RC4, RC4 de 128 bits, DESX, AES de 128 bits, AES de 192 bits e AES de 256 bits |

## <a id="ssis-signed"></a>Pacotes do SSIS devem ser criptografados e assinados digitalmente

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Identificar a origem de pacotes com assinaturas digitais](https://msdn.microsoft.com/library/ms141174.aspx), [de ameaças e atenuação de vulnerabilidade (Serviços de integração)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Passos** | A origem de um pacote é a pessoa ou organização que criou o pacote. Executar um pacote a partir de uma origem desconhecida ou não fidedigna pode ser arriscado. Para impedir a adulteração não autorizados dos pacotes do SSIS, assinaturas digitais devem ser utilizadas. Além disso, para garantir a confidencialidade dos pacotes durante o armazenamento/trânsito, pacotes do SSIS tem a encriptação |

## <a id="securables-db"></a>Adicione a assinatura digital para capacidades de segurança de bases de dados fundamentais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [ADD SIGNATURE (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Passos** | Em casos em que a integridade das bases de dados críticos com capacidade de segurança tem de ser verificado, devem ser usadas assinaturas digitais. Capacidades de segurança da base de dados, como um procedimento armazenado, a função, o assembly ou o acionador podem ser assinadas digitalmente. Segue-se um exemplo de quando pode ser útil: Digamos que um ISV (fornecedor independente de Software) forneceu suporte para um software entregue a um dos seus clientes. Antes de fornecer suporte, o ISV iria querer Certifique-se de que uma base de dados com capacidade de segurança no software não foram adulterado por engano ou por uma tentativa maliciosa. Se o com capacidade de segurança estiver assinada digitalmente, o ISV pode verificar a sua assinatura digital e validar a sua integridade.| 

## <a id="ekm-keys"></a>Utilizar o SQL server EKM para proteger chaves de encriptação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [SQL Server Extensible Key Management (EKM)](https://msdn.microsoft.com/library/bb895340), [gestão de chaves extensível com o Azure Key Vault (SQL Server)](https://msdn.microsoft.com/library/dn198405) |
| **Passos** | Gerenciamento extensível de chaves do SQL Server permite que as chaves de encriptação que protegem os ficheiros de base de dados sejam armazenados num dispositivo de externamente, como um smart card, o dispositivo USB ou o módulo EKM/HSM. Isto também permite a proteção de dados dos administradores de banco de dados (exceto os membros do grupo sysadmin). Dados podem ser encriptados utilizando chaves de encriptação que apenas o utilizador de base de dados tem acesso no módulo EKM/HSM externo. |

## <a id="keys-engine"></a>Utilizar a funcionalidade de AlwaysEncrypted se as chaves de encriptação não devem ser reveladas ao motor de base de dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | MsSQL2016 de versão - V12, do SQL |
| **Referências**              | [Sempre encriptado (motor de base de dados)](https://msdn.microsoft.com/library/mt163865) |
| **Passos** | Always Encrypted é uma funcionalidade concebida para proteger dados confidenciais, como números de cartão de crédito ou números de identificação nacional (por exemplo, EUA números de cpf), armazenados em bases de dados de base de dados do Azure SQL ou SQL Server. Always Encrypted permite aos clientes encriptar dados confidenciais dentro de aplicativos de cliente e nunca revelar as chaves de encriptação para o motor de base de dados (base de dados SQL ou SQL Server). Como resultado, o Always Encrypted fornece uma separação entre os proprietários dos dados (e os podem ver) e aqueles que gerir os dados (mas não devem ter acesso) |

## <a id="keys-iot"></a>Store chaves criptográficas com segurança no dispositivo de IoT

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | SO do dispositivo - Windows IoT Core, conectividade do dispositivo - SDKs de dispositivo do IoT do Azure |
| **Referências**              | [TPM no Windows IoT Core](https://developer.microsoft.com/windows/iot/docs/tpm), [configurar TPM no Windows IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/setuptpm), [TPM de SDK de dispositivo de IoT do Azure](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Passos** | Chaves de Symmetric ou privada do certificado de forma segura num hardware protegidas armazenamento como chips TPM ou Smart Card. Windows 10 IoT Core suporta o utilizador de um TPM e existem vários TPMs compatíveis que podem ser usados: https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm. Recomenda-se para utilizar TPM discreto ou Firmware. Um TPM de Software só deve ser utilizado para fins de testes e de desenvolvimento. Depois de um TPM está disponível e as chaves são aprovisionadas no mesmo, o código que gera o token deve ser escrito sem pré-programá-la informações confidenciais nela. | 

### <a name="example"></a>Exemplo
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Como pode ser visto, a chave primária do dispositivo não está presente no código. Em vez disso, ele é armazenado no TPM na ranhura 0. Dispositivo TPM gera um token SAS de curta duração que, em seguida, é utilizado para ligar ao IoT Hub. 

## <a id="random-hub"></a>Gerar uma chave simétrica aleatória de tamanho suficiente para a autenticação para o IoT Hub

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Cloud da IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Escolha de gateway - IoT Hub do Azure |
| **Referências**              | N/A  |
| **Passos** | IoT Hub contém um registo de identidade de dispositivo e ao aprovisionar um dispositivo, gera automaticamente uma chave simétrica aleatória. Recomenda-se para utilizar esta funcionalidade de registo de identidade do Hub de IoT do Azure para gerar a chave utilizada para autenticação. IoT Hub também permite que uma chave de ser especificado ao criar o dispositivo. Se uma chave é gerada fora do IoT Hub durante o aprovisionamento de dispositivos, recomenda-se para criar uma chave simétrica aleatória ou pelo menos de 256 bits. |

## <a id="pin-remote"></a>Certifique-se de que é uma política de gestão de dispositivos no local que requer uma utilização PIN e permite remoto limpar

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente móvel do Dynamics CRM | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Certifique-se de que é uma política de gestão de dispositivos no local que requer uma utilização PIN e permite remoto limpar |

## <a id="bitlocker"></a>Certifique-se que uma política de gestão do dispositivo está num local que requer um bloqueio PIN/palavra-passe/automático e encripta todos os dados (por exemplo, o BitLocker)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente do Dynamics CRM Outlook | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Certifique-se que uma política de gestão do dispositivo está num local que requer um bloqueio PIN/palavra-passe/automático e encripta todos os dados (por exemplo, o BitLocker) |

## <a id="rolled-server"></a>Certifique-se de que as chaves de assinatura são agregadas ao longo ao utilizar o servidor de identidades

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidades | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Servidor de identidades - chaves, criptografia e assinaturas](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Passos** | Certifique-se de que as chaves de assinatura são agregadas ao longo ao utilizar o servidor de identidades. A ligação na seção referências explica como isso deve ser planejado sem causar interrupções para aplicativos da entidade confiadora no servidor de identidades. |

## <a id="client-server"></a>Certifique-se de que o ID de cliente criptograficamente fortes, segredo do cliente são utilizadas no servidor de identidades

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidades | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | <p>Certifique-se de que o ID de cliente criptograficamente fortes, segredo do cliente são utilizadas no servidor de identidades. As diretrizes seguintes devem ser utilizadas ao gerar um ID de cliente e o segredo:</p><ul><li>Gerar um GUID aleatório como o ID de cliente</li><li>Gerar uma chave de 256 bits criptograficamente aleatória como o segredo</li></ul>|
