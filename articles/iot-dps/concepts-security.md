---
title: Conceitos de segurança no serviço de aprovisionamento de dispositivos do Azure IoT Hub | Documentos da Microsoft
description: Descreve os conceitos específicos a dispositivos com o serviço aprovisionamento de dispositivos e IoT Hub de aprovisionamento de segurança
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: e35330874c647eba2cddde694563c8a1d9e83df5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775122"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Conceitos de segurança do serviço de aprovisionamento de dispositivos IoT Hub 

Serviço de aprovisionamento de dispositivo IoT Hub é um serviço auxiliar para o IoT Hub que utiliza para configurar dispositivos sem toques de aprovisionamento para um hub IoT especificado. Com o serviço de aprovisionamento de dispositivos, pode [aprovisionar automaticamente](concepts-auto-provisioning.md) milhões de dispositivos de forma segura e dimensionável. Este artigo fornece uma visão geral do *segurança* conceitos envolvidos no aprovisionamento de dispositivos. Este artigo é relevante para todas as pessoas envolvidas na obtenção de um dispositivo pronto para implantação.

## <a name="attestation-mechanism"></a>Mecanismo de atestado

O mecanismo de atestação é o método usado para confirmar a identidade de um dispositivo. O mecanismo de atestação também é relevante para a lista de inscrição, que informa ao serviço de aprovisionamento que método de atestado para utilizar com um determinado dispositivo.

> [!NOTE]
> O IoT Hub utiliza "esquema de autenticação" para um conceito semelhante desse serviço.

Serviço aprovisionamento de dispositivos suporta os seguintes formulários do atestado de:
* **Certificados X.509** com base no fluxo de autenticação de certificado X.509 padrão.
* **Trusted Platform Module (TPM)** com base num desafio de valor de uso único, usando o padrão TPM para as chaves para apresentar um token de assinatura de acesso partilhado (SAS) assinado. Essa forma de atestado não necessita de um TPM físico no dispositivo, mas espera que o serviço atestar a utilizar a chave de endossamento pela [especificação do TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Chave simétrica** com base na assinatura de acesso partilhado (SAS) [tokens de segurança](../iot-hub/iot-hub-devguide-security.md#security-tokens), que incluem uma assinatura com hash e uma expiração incorporada. Para obter mais informações, consulte [atestado de chave simétrico](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Módulo de hardware de segurança

O módulo de hardware de segurança, ou de um HSM, é utilizada para armazenamento seguro e baseado em hardware de segredos de dispositivo e é a forma mais segura de armazenamento secreta. Certificados X.509 e SAS tokens podem ser armazenados no HSM. HSMs podem ser utilizadas com ambos os mecanismos de atestado de aprovisionamento suporta.

> [!TIP]
> Recomendamos vivamente utilizar um HSM com dispositivos para armazenar com segurança segredos nos seus dispositivos.

Segredos do dispositivo também podem ser armazenados no software (memória), mas é uma forma menos segura do armazenamento de um HSM.

## <a name="trusted-platform-module"></a>Trusted Platform Module

TPM pode referir-se para um padrão para armazenar de forma segura as chaves utilizadas para autenticar a plataforma, ou ele pode consultar a interface de e/s utilizada para interagir com os módulos implementando o padrão. Os TPMs podem existir como distintos a hardware, hardware integrada, com base em firmware ou baseada em software. Saiba mais sobre [TPMs e TPM atestado](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Serviço aprovisionamento de dispositivos suporta apenas TPM 2.0.

Atestado de TPM baseia-se um desafio de valor de uso único, que utiliza as chaves de raiz de endossamento e o armazenamento para apresentar um token de assinatura de acesso partilhado (SAS) assinado.

### <a name="endorsement-key"></a>Chave de endossamento

A chave de endossamento é uma chave assimétrica contida dentro de TPM, o que foi gerado internamente ou inserido no tempo de fabrico e é exclusivo para cada TPM. A chave de endossamento não pode ser alterada ou removida. A parte da chave de endossamento privada nunca é lançada fora do TPM, enquanto a parte pública da chave de endossamento é utilizada para reconhecer um TPM original. Saiba mais sobre o [chave de endossamento](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Chave de raiz de armazenamento

A chave de raiz de armazenamento é armazenada no TPM e é utilizada para proteger chaves TPM criadas por aplicativos, para que estas chaves não podem ser utilizadas sem o TPM. A chave de raiz de armazenamento é gerada quando assumir a propriedade do TPM; Quando o TPM seja limpo, para que um novo utilizador pode assumir a propriedade, é gerada uma nova chave de raiz de armazenamento. Saiba mais sobre o [chave de raiz de armazenamento](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>Certificados X.509

Usando certificados X.509 como um mecanismo de atestado é uma excelente maneira de aumentar a produção e simplificar o aprovisionamento de dispositivos. Certificados X.509, normalmente, são organizados numa cadeia de certificados de confiança na qual cada certificado na cadeia está assinado pela chave privada do certificado superior seguinte e assim por diante, terminar num certificado de raiz autoassinado. Essa organização estabelece uma cadeia de delegados de confiança a partir do certificado de raiz gerado por uma raiz fidedigna autoridade de certificação (AC) para baixo por meio de cada AC intermediária para o certificado de "folha" de entidade final instalado num dispositivo. Para obter mais informações, consulte [autenticação de dispositivo usando certificados de AC X.509](/azure/iot-hub/iot-hub-x509ca-overview). 

Muitas vezes, a cadeia de certificados representa alguns hierarquia lógica ou física associada a dispositivos. Por exemplo, um fabricante poderá:
- emitir um certificado de AC de raiz autoassinado
- Utilize o certificado de raiz para gerar um certificado de AC intermediário exclusivo para cada fábrica
- utilizar o certificado de cada fábrica para gerar um certificado de AC intermediária exclusivo para cada linha de produção de fábrica, as máquinas
- e, por último, utilize o certificado de linha de produção, para gerar um certificado de dispositivo exclusivo (entidade final) para cada dispositivo fabricado na linha. 

Para obter mais informações, consulte [compreensão Conceptual de certificados X.509 de AC no setor de IoT](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Certificado de raiz

Um certificado de raiz é um certificado X.509 autoassinado que representa uma autoridade de certificação (AC). É o terminus ou âncora de confiança, da cadeia de certificados. Certificados de raiz podem ser Self-emitidos por uma organização ou comprados numa autoridade de certificado de raiz. Para obter mais informações, consulte [certificados de AC de X.509 obter](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). O certificado de raiz pode também ser referido como um certificado de AC de raiz.

### <a name="intermediate-certificate"></a>Certificado intermédio

Um certificado intermédio é um certificado X.509, o que foi assinado pelo certificado de raiz (ou outro certificado intermédio com o certificado de raiz na cadeia). O último certificado intermédio de uma cadeia é utilizado para assinar os certificados de folha. Um certificado intermédio pode também ser referido como um certificado de AC intermediária.

### <a name="end-entity-leaf-certificate"></a>Certificado de "folha" de entidade final

O certificado de folha ou o certificado de entidade final, identifica o titular de certificado. Ele tem o certificado de raiz na sua cadeia de certificados, bem como zero ou mais certificados intermédios. O certificado de folha não é utilizado para assinar todos os outros certificados. Exclusivamente identifica o dispositivo ao serviço de aprovisionamento e é por vezes referido como o certificado do dispositivo. Durante a autenticação, o dispositivo utiliza a chave privada associada este certificado para responder a uma prova de desafio de posse do serviço.

Folha de certificados utilizados com um [inscrição Individual](./concepts-service.md#individual-enrollment) entrada tem um requisito que o **nome do requerente** tem de ser definido para o ID de registo de entrada de inscrição Individual. Folha certificados utilizados com um [grupo de inscrição](./concepts-service.md#enrollment-group) entrada deve ter o **nome do requerente** definido como o ID de dispositivo desejado que será mostrado no **registos de registo** para o dispositivo autenticado no grupo de inscrição.

Para obter mais informações, consulte [autenticar os dispositivos assinados com certificados X.509 de AC](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Controlar o acesso de dispositivo ao serviço de aprovisionamento com certificados X.509

O serviço de aprovisionamento expõe dois tipos de entrada de inscrição que pode utilizar para controlar o acesso para dispositivos que utilizam o mecanismo de atestado de X.509:  

- [Inscrição individual](./concepts-service.md#individual-enrollment) entradas são configuradas com o certificado de dispositivo associado a um dispositivo específico. Estas entradas de controlam de inscrições de dispositivos específicos.
- [Grupo de inscrição](./concepts-service.md#enrollment-group) entradas estão associadas a um certificado de AC de raiz ou intermediário específico. Estas entradas controlam inscrições para todos os dispositivos que tenham intermediários ou o certificado na respetiva cadeia de certificado de raiz. 

Quando um dispositivo se liga ao serviço de aprovisionamento, o serviço dá prioridade à entradas de registo mais específicas sobre entradas de inscrição menos específicas. Ou seja, se existir uma inscrição individual para o dispositivo, o serviço de aprovisionamento aplica-se essa entrada. Se não há nenhuma inscrição individual para o dispositivo e existe um grupo de inscrição para o primeiro certificado intermediário na cadeia de certificados do dispositivo, o serviço aplica-se dessa entrada e assim por diante, até a cadeia para a raiz. O serviço aplica-se a primeira entrada aplicável que encontra, que:

- Se a primeira entrada de inscrição encontrada estiver ativada, o serviço Aprovisiona o dispositivo.
- Se a primeira entrada de inscrição encontrada estiver desativada, o serviço não Aprovisiona o dispositivo.  
- Se nenhuma entrada de inscrição é encontrada em qualquer um dos certificados na cadeia de certificados do dispositivo, o serviço não Aprovisiona o dispositivo. 

Esse mecanismo e estrutura hierárquica de cadeias de certificados fornece poderosas flexibilidade na forma como pode controlar o acesso para dispositivos individuais, bem como para grupos de dispositivos. Por exemplo, imagine cinco dispositivos com as seguintes cadeias de certificados: 

- *Dispositivo 1*: certificado de raiz-A -> certificados de dispositivo 1 do certificado >
- *O dispositivo 2*: certificado de raiz-A -> certificados de dispositivo 2 do certificado >
- *Dispositivo 3*: certificado de raiz-certificados um-certificado de dispositivo 3 > >
- *O dispositivo 4*: certificado de raiz -> B -> certificados de dispositivo 4 do certificado
- *O dispositivo 5*: certificado de raiz -> B -> certificados de dispositivo 5 do certificado

Inicialmente, pode criar uma entrada de inscrição de único grupo ativado para o certificado de raiz ativar o acesso para todos os cinco dispositivos. Se o certificado mais tarde para B torna-se comprometido, pode criar uma entrada de grupo de inscrição de desativado para o certificado B impedir *dispositivo 4* e *dispositivo 5* a inscrição. Se ainda posterior *dispositivo 3* torna-se comprometido, pode criar uma entrada de inscrição individual desativado para o seu certificado. Isso revoga o acesso a *3 do dispositivo*, mas ainda permite *1 dispositivo* e *2 do dispositivo* inscrever.