---
title: Tutorial - Compreenda a Criptografia e os certificados X.509 para Azure IoT Hub | Microsoft Docs
description: Tutorial - Compreender criptografia e X.509 PKI para Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 2c375a02f534572826e1ebd6b8549e59f6e83640
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378385"
---
# <a name="tutorial-understanding-public-key-cryptography-and-x509-public-key-infrastructure"></a>Tutorial: Compreender a Criptografia da Chave Pública e A Infraestrutura Chave Pública X.509

Pode utilizar certificados X.509 para autenticar dispositivos num Hub Azure IoT. Um certificado é um documento digital que contém a chave pública do dispositivo e pode ser usado para verificar se o dispositivo é o que afirma ser. Os certificados X.509 e as listas de revogação de certificados (CRLs) são documentados pelo [RFC 5280](https://tools.ietf.org/html/rfc5280). Os certificados são apenas uma parte de uma infraestrutura chave pública X.509 (PKI). Para compreender o X.509 PKI, é necessário compreender algoritmos criptográficos, chaves criptográficas, certificados e autoridades de certificação (CAs):

* **Os algoritmos** definem como os dados originais do texto simples são transformados em texto cifrado e de volta ao texto simples.
* **As teclas** são cordas de dados aleatórias ou pseudorandom usadas como entrada para um algoritmo.
* **Os certificados** são documentos digitais que contêm a chave pública de uma entidade e permitem determinar se o sujeito do certificado é quem ou o que pretende ser.
* **As autoridades de certificação** atestam a autenticidade dos sujeitos de certificados.

Pode adquirir um certificado a uma autoridade de certificação (CA). Também pode, para testar e desenvolvimento ou se estiver a trabalhar num ambiente independente, criar uma CA de raiz auto-assinada. Se, por exemplo, possuir um ou mais dispositivos e estiver a testar a autenticação do hub IoT, pode auto-assinar a sua raiz ca e usá-la para emitir certificados de dispositivo. Também pode emitir certificados de dispositivo auto-assinados. Isto é discutido em artigos posteriores.

Antes de discutir os certificados X.509 com mais detalhes e usá-los para autenticar dispositivos num Hub IoT, discutimos a criptografia em que se baseiam os certificados.

## <a name="cryptography"></a>Criptografia

A criptografia é usada para proteger informações e comunicações. Isto é normalmente feito usando técnicas criptográficas para baralhar texto simples (texto comum) em texto cifrado (texto codificado) e de volta novamente. Este processo de scrambling chama-se encriptação. O processo inverso chama-se desencriptação. A criptografia diz respeito aos seguintes objetivos:

* **Confidencialidade**: A informação só pode ser entendida pelo público pretendido.
* **Integridade**: As informações não podem ser alteradas no armazenamento ou em trânsito.
* **Não repudiação**: O criador da informação não pode mais tarde negar essa criação.
* **Autenticação**: O remetente e o recetor podem confirmar a identidade uns dos outros.

## <a name="encryption"></a>Encriptação

O processo de encriptação requer um algoritmo e uma chave. O algoritmo define como os dados são transformados de texto simples em texto cifrado e de volta ao texto simples. Uma chave é uma cadeia aleatória de dados usados como entrada para o algoritmo. Toda a segurança do processo está contida na chave. Por isso, a chave deve ser guardada de forma segura. Os detalhes dos algoritmos mais populares, no entanto, estão disponíveis ao público.

Há dois tipos de encriptação. A encriptação simétrica usa a mesma chave tanto para encriptação como para desencriptação. A encriptação assimétrica utiliza chaves diferentes, mas matematicamente relacionadas, para executar encriptação e desencriptação.

### <a name="symmetric-encryption"></a>Encriptação simétrica

A encriptação simétrica usa a mesma chave para encriptar o texto simples em texto cifrado e desencriptar o texto de cifra de volta para o texto simples. O comprimento necessário da chave, expresso em número de bits, é determinado pelo algoritmo. Depois de a chave ser utilizada para encriptar texto simples, a mensagem encriptada é enviada para o destinatário que, em seguida, desencripta o texto da cifra. A chave simétrica deve ser transmitida de forma segura ao destinatário. Enviar a chave é o maior risco de segurança quando se utiliza um algoritmo simétrico.

![Exemplo de encriptação simétrica](media/tutorial-x509-introduction/symmetric-keys.png)

### <a name="asymmetric-encryption"></a>Criptografia assimétrica

Se apenas for utilizada encriptação simétrica, o problema é que todas as partes na comunicação devem possuir a chave privada. No entanto, é possível que terceiros não autorizados possam capturar a chave durante a transmissão a utilizadores autorizados. Para resolver esta questão, utilize a criptografia assimétrica ou pública.

Na criptografia assimétrica, cada utilizador tem duas chaves matematicamente relacionadas chamadas um par de chaves. Uma chave é pública e a outra é privada. O par de chaves garante que apenas o destinatário tem acesso à chave privada necessária para desencriptar os dados. A seguinte ilustração resume o processo de encriptação assimétrica.

![Exemplo de encriptação assimétrica](media/tutorial-x509-introduction/asymmetric-keys.png)

1. O destinatário cria um par de chaves público-privado e envia a chave pública para uma AC. A AC embala a chave pública num certificado X.509.

1. O partido que envia obtém a chave pública do destinatário da AC.

1. O remetente encripta dados de texto simples usando um algoritmo de encriptação. A chave pública do destinatário é usada para executar encriptação.

1. O remetente transmite o texto da cifra ao destinatário. Não é necessário enviar a chave porque o destinatário já tem a chave privada necessária para desencriptar o texto da cifra.

1. O destinatário desencripta o texto da cifra utilizando o algoritmo assimétrico especificado e a chave privada.

### <a name="combining-symmetric-and-asymmetric-encryption"></a>Combinação de encriptação simétrica e assimétrica

A encriptação simétrica e assimétrica pode ser combinada para tirar partido dos seus pontos fortes relativos. A encriptação simétrica é muito mais rápida do que assimétrica, mas, devido à necessidade de enviar chaves privadas a outras partes, não é tão segura. Para combinar os dois tipos, a encriptação simétrica pode ser usada para converter texto simples em texto cifrado. A encriptação assimétrica é usada para trocar a chave simétrica. Isto é demonstrado pelo seguinte diagrama.

![Encriptação simétrica e assimétrica](media/tutorial-x509-introduction/symmetric-asymmetric-encryption.png)

1. O remetente recupera a chave pública do destinatário.

1. O remetente gera uma chave simétrica e usa-a para encriptar os dados originais.

1. O remetente utiliza a chave pública do destinatário para encriptar a chave simétrica.

1. O remetente transmite a chave simétrica encriptada e o texto cifrado ao destinatário pretendido.

1. O destinatário utiliza a chave privada que corresponde à chave pública do destinatário para desencriptar a chave simétrica do remetente.

1. O destinatário utiliza a chave simétrica para desencriptar o texto da cifra.

### <a name="asymmetric-signing"></a>Assinatura assimétrica

Algoritmos assimétricos podem ser usados para proteger os dados da modificação e provar a identidade do criador de dados. A seguinte ilustração mostra como a assinatura assimétrica ajuda a provar a identidade do remetente.

![Exemplo de assinatura assimétrica](media/tutorial-x509-introduction/asymmetric-signing.png)

1. O remetente transmite dados de texto simples através de um algoritmo de encriptação assimétrica, utilizando a chave privada para encriptação. Note que este cenário reverte a utilização das chaves privadas e públicas descritas na secção anterior que detalham a encriptação assimétrica.

1. O texto cifrado resultante é enviado ao destinatário.

1. O destinatário obtém a chave pública do originador a partir de um diretório.

1. O destinatário desencripta o texto da cifra utilizando a chave pública do autor. O texto simples resultante comprova a identidade do autor da decisão porque só o autor tem acesso à chave privada que inicialmente encriptou o texto original.

## <a name="signing"></a>Assinatura de

A assinatura digital pode ser usada para determinar se os dados foram modificados em trânsito ou em repouso. Os dados são transmitidos através de um algoritmo de haxixe, uma função unidirecionais que produz um resultado matemático a partir da mensagem dada. O resultado chama-se *valor de haxixe,* *digestão de mensagens,* *digestão,* *assinatura* ou *impressão digital*. Um valor de haxixe não pode ser invertido para obter a mensagem original. Uma pequena alteração na mensagem resulta numa alteração significativa na *impressão digital,* o valor do haxixe pode ser usado para determinar se uma mensagem foi alterada. A seguinte ilustração mostra como a encriptação assimétrica e algoritmos de haxixe podem ser usados para verificar se uma mensagem não foi modificada.

![Exemplo de assinatura](media/tutorial-x509-introduction/signing.png)

1. O remetente cria uma mensagem de texto simples.

1. O remetente tem a mensagem de texto simples para criar uma mensagem digerida.

1. O remetente encripta a digestão usando uma chave privada.

1. O remetente transmite a mensagem de texto simples e a digestão encriptada para o destinatário pretendido.

1. O destinatário desencripta a digestão utilizando a chave pública do remetente.

1. O destinatário executa o mesmo algoritmo de haxixe que o remetente usou sobre a mensagem.

1. O destinatário compara a assinatura resultante com a assinatura desencriptado. Se as digestões forem as mesmas, a mensagem não foi modificada durante a transmissão.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os campos que compõem um certificado, consulte [os Certificados de Chave Pública De Conhecimento X.509.](tutorial-x509-certificates.md)

Se já sabe muito sobre os certificados X.509 e pretende gerar versões de teste que pode utilizar para autenticar no seu IoT Hub, consulte os seguintes tópicos:

* [Usando scripts de Microsoft-Supplied para criar certificados de teste](tutorial-x509-scripts.md)
* [Utilização de OpenSSL para criar certificados de teste](tutorial-x509-openssl.md)
* [Utilizar o OpenSSL para criar certificados de teste de Self-Signed](tutorial-x509-self-sign.md)

Se tiver um certificado de certificação da autoridade de certificação (CA) ou certificado de CA subordinado e quiser enviá-lo para o seu hub IoT e provar que é seu dono, consulte [Prova de Posse de um Certificado de CA](tutorial-x509-prove-possession.md).
