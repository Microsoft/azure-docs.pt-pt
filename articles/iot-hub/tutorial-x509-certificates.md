---
title: Tutorial - Compreenda os certificados de chave público X.509 para O Azure IoT Hub| Microsoft Docs
description: Tutorial - Compreenda os certificados de chave público X.509 para o Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: fada68ba395b959e557542eb8c230561aad84214
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384328"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>Tutorial: Compreensão dos certificados de chave pública X.509

Os certificados X.509 são documentos digitais que representam um utilizador, computador, serviço ou dispositivo. São emitidas por uma autoridade de certificação (CA), a CA subordinada ou a autoridade de registo e contêm a chave pública do sujeito do certificado. Não contêm a chave privada do sujeito que deve ser guardada de forma segura. Os certificados-chave públicos são documentados pelo [RFC 5280](https://tools.ietf.org/html/rfc5280). São assinados digitalmente e, em geral, contêm as seguintes informações:

* Informações sobre o titular do certificado
* A chave pública que corresponde à chave privada do sujeito
* Informação sobre a A. de emissão
* Os algoritmos de encriptação e/ou de assinatura digital suportados
* Informações para determinar o estado de revogação e validade do certificado

## <a name="certificate-fields"></a>Campos de certificados

Ao longo do tempo, houve três versões de certificado. Cada versão adiciona campos ao anterior. A versão 3 é atual e contém os campos da versão 1 e da versão 2, além dos campos da versão 3. Versão 1 definiu os seguintes campos:

* **Versão**: Um valor (1, 2 ou 3) que identifica o número de versão do certificado
* **Número de série**: Um número único para cada certificado emitido por um CA
* **Ca Algoritmo de assinatura**: Nome do algoritmo que a AC utiliza para assinar o conteúdo do certificado
* **Nome do emitente**: O nome distinto (DN) da A. de emissão do certificado
* **Período de validade**: O período de tempo para o qual o certificado é considerado válido
* **Nome do sujeito**: Nome da entidade representada pelo certificado
* **Informação sobre chave pública do assunto**: Chave pública detida pelo sujeito do certificado

A versão 2 acrescentou os seguintes campos contendo informações sobre o emitente de certificado. Estes campos raramente são usados.

* **Emitente ID Exclusivo**: Um identificador único para a Emissão ca definida pela AC
* **Objeto ID Exclusivo**: Um identificador único para o sujeito do certificado definido pela A. de emissão

Os certificados da versão 3 acrescentaram as seguintes extensões:

* **Identificador chave da autoridade**: Este pode ser um de dois valores:
  * O sujeito da AC e o número de série do certificado de CA que emitiu este certificado
  * Um haxixe da chave pública da AC que emitiu este certificado
* **Identificador-chave objeto**: Haxixe da chave pública do certificado atual
* **Utilização de chaves** Define o serviço para o qual pode ser utilizado um certificado. Este pode ser um ou mais dos seguintes valores:
  * **Assinatura Digital**
  * **Não-Repúdio**
  * **Cifragem de Chaves**
  * **Encipherment de dados**
  * **Acordo-chave**
  * **Sinal de Cert chave**
  * **Sinal de CRL**
  * **Apenas encipher**
  * **Decifrar apenas**
* **Período de utilização da chave privada**: Período de validade para a parte chave privada de um par de chaves
* **Políticas de Certificados**: Políticas utilizadas para validar o tema do certificado
* **Mapeamentos de Políticas**: Mapeia uma política numa organização para a política noutra
* **Nome Alternativo do Assunto**: Lista de nomes alternativos para o sujeito
* **Nome Alternativo emitente**: Lista de nomes alternativos para a AE emissora
* **Atributo Dir Assunto**: Atributos de um diretório X.500 ou LDAP
* **Restrições Básicas**: Permite que o certificado designe se é emitido para um CA, ou para um utilizador, computador, dispositivo ou serviço. Esta extensão também inclui uma restrição de comprimento de caminho que limita o número de CAs subordinados que podem existir.
* **Restrições de nome**: Designa quais espaços de nome são permitidos num certificado emitido pela AC
* **Restrições políticas**: Pode ser usado para proibir mapeamentos de políticas entre CAs
* **Utilização prolongada da chave**: Indica como a chave pública de um certificado pode ser usada para além das finalidades identificadas na extensão **de utilização** da chave
* **Pontos de distribuição de CRL**: Contém um ou mais URLs onde é publicada a lista de revogação do certificado de base (CRL)
* **Inibir qualquer Política**: Inibe a utilização das Políticas de **Emissão** OID (2.5.29.32.0) em certificados de CA subordinados
* **CRL mais fresco**: Contém um ou mais URLs onde o delta CRL da CA emite é publicado
* **Acesso à Informação da Autoridade**: Contém um ou mais URLs em que o certificado de Emissão ca é publicado
* **Acesso à informação do assunto**: Contém informações sobre como recuperar detalhes adicionais para um sujeito de certificado

## <a name="certificate-formats"></a>Formatos de certificado

Os certificados podem ser guardados em vários formatos. A autenticação Azure IoT Hub normalmente utiliza os formatos PEM e PFX.

### <a name="binary-certificate"></a>Certificado binário

Isto contém um certificado binário de formulário bruto utilizando a codificação DER ASN.1.

### <a name="ascii-pem-format"></a>Formato ASCII PEM

Um certificado PEM (extensão.pem) contém um certificado codificado base64, com início com -----BEGIN CERTIFICATE----- e terminando com -----END CERTIFICATE-----. O formato PEM é muito comum e é exigido pelo IoT Hub ao carregar certos certificados.

### <a name="ascii-pem-key"></a>Chave ASCII (PEM)

Contém uma chave DER codificada com base64 com metadados possivelmente adicionais sobre o algoritmo utilizado para a proteção de palavras-passe.

### <a name="pkcs7-certificate"></a>Certificado PKCS#7

Um formato concebido para o transporte de dados assinados ou encriptados. É definido por [RFC 2315](https://tools.ietf.org/html/rfc2315). Pode incluir toda a cadeia de certificados.

### <a name="pkcs8-key"></a>Chave PKCS#8

O formato de uma loja de chaves privada definida por [RFC 5208](https://tools.ietf.org/html/rfc5208).

### <a name="pkcs12-key-and-certificate"></a>Chave e certificado PKCS#12

Um formato complexo que pode armazenar e proteger uma chave e toda a cadeia de certificados. É comumente usado com uma extensão .pfx. PKCS#12 é sinónimo do formato PFX.

## <a name="for-more-information"></a>Para obter mais informações:

Para obter mais informações, consulte os seguintes tópicos:

* [O guia do leigo para o jargão de certificado X.509](https://techcommunity.microsoft.com/t5/internet-of-things/the-layman-s-guide-to-x-509-certificate-jargon/ba-p/2203540)
* [Compreensão conceptual dos certificados X.509 CA na indústria IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-concept)

## <a name="next-steps"></a>Passos seguintes

Se pretender gerar certificados de teste que pode utilizar para autenticar dispositivos no seu Hub IoT, consulte os seguintes tópicos:

* [Usando scripts de Microsoft-Supplied para criar certificados de teste](tutorial-x509-scripts.md)
* [Utilização de OpenSSL para criar certificados de teste](tutorial-x509-openssl.md)
* [Utilizar o OpenSSL para criar certificados de teste de Self-Signed](tutorial-x509-self-sign.md)

Se tiver um certificado de certificação da autoridade de certificação (CA) ou certificado de CA subordinado e quiser enviá-lo para o seu hub IoT e provar que é seu dono, consulte [Prova de Posse de um Certificado de CA](tutorial-x509-prove-possession.md).
