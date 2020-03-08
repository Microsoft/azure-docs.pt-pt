---
title: Serviço de Provisionamento de Dispositivos Azure IoT Hub - Conceitos de segurança
description: Descreve conceitos de fornecimento de segurança específicos de dispositivos com Serviço de Provisionamento de Dispositivos (DPS) e Hub IoT
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3191e9886604af9b2a26b71a89cee699197585c4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378881"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Conceitos de segurança do serviço de provisionamento de dispositivos IoT Hub 

O Serviço de Provisionamento de Dispositivos IoT Hub é um serviço de ajuda para o IoT Hub que utiliza para configurar o fornecimento de dispositivos de toque zero a um hub IoT especificado. Com o Serviço de Provisionamento de Dispositivos, pode [fornecer](concepts-auto-provisioning.md) automaticamente milhões de dispositivos de forma segura e escalável. Este artigo dá uma visão geral dos conceitos de *segurança* envolvidos no fornecimento de dispositivos. Este artigo é relevante para todas as pessoas envolvidas na preparação de um dispositivo para implantação.

## <a name="attestation-mechanism"></a>Mecanismo de atestado

O mecanismo de atestado é o método utilizado para confirmar a identidade de um dispositivo. O mecanismo de atestado também é relevante para a lista de inscrições, que indica ao serviço de provisionamento qual o método de atestado a utilizar com um determinado dispositivo.

> [!NOTE]
> O IoT Hub utiliza o "esquema de autenticação" para um conceito semelhante nesse serviço.

O Serviço de Provisionamento de Dispositivos suporta as seguintes formas de atestado:
* **Certificados X.509 baseados** no fluxo padrão de autenticação do certificado X.509.
* **Módulo de Plataforma Fidedigna (TPM)** baseado num desafio nonce, utilizando a norma TPM para as teclas apresentarem um símbolo assinado de Assinatura de Acesso Partilhado (SAS). Esta forma de atestado não requer um TPM físico no dispositivo, mas o serviço espera atestar usando a chave de averbamento de acordo com a [especificação TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Chave simétrica** baseada em [fichas](../iot-hub/iot-hub-devguide-security.md#security-tokens)de segurança de assinatura de acesso partilhado (SAS), que incluem uma assinatura de hashed e uma expiração incorporada. Para mais informações, consulte o atestado da [chave simétrica](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Módulo de segurança de hardware

O módulo de segurança de hardware, ou HSM, é usado para armazenamento seguro e baseado em hardware de segredos do dispositivo, e é a forma mais segura de armazenamento secreto. Tanto os certificados X.509 como as fichas SAS podem ser armazenados no HSM. Os HSMs podem ser utilizados com ambos os mecanismos de atestado que os suportes de fornecimento.

> [!TIP]
> Recomendamos vivamente a utilização de um HSM com dispositivos para armazenar segredos de forma segura nos seus dispositivos.

Os segredos do dispositivo também podem ser armazenados em software (memória), mas é uma forma de armazenamento menos segura do que um HSM.

## <a name="trusted-platform-module"></a>Módulo de Plataforma Fidedigna

O TPM pode consultar uma norma para armazenar chaves de forma segura utilizadas para autenticar a plataforma, ou pode consultar a interface De/S utilizada para interagir com os módulos que implementam a norma. Os TPMs podem existir como hardware discreto, hardware integrado, baseado em firmware ou baseado em software. Saiba mais sobre [TPMs e atestado TPM](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). O Serviço de Provisionamento de Dispositivos suporta apenas o TPM 2.0.

O atestado TPM baseia-se num desafio nonce, que utiliza as chaves de raiz de endosso e armazenamento para apresentar um símbolo assinado de Assinatura de Acesso Partilhado (SAS).

### <a name="endorsement-key"></a>Chave de endosso

A chave de averbamento é uma chave assimétrica contida no TPM, que foi gerada ou injetada internamente no tempo de fabrico e é única para cada TPM. A chave de averbamento não pode ser alterada ou removida. A parte privada da chave de apoio nunca é divulgada fora do TPM, enquanto a parte pública da chave de apoio é usada para reconhecer um TPM genuíno. Saiba mais sobre a [chave de apoio](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Chave raiz de armazenamento

A chave raiz de armazenamento é armazenada no TPM e é utilizada para proteger as teclas TPM criadas por aplicações, de modo a que estas teclas não possam ser utilizadas sem o TPM. A chave raiz de armazenamento é gerada quando toma posse do TPM; quando se limpa o TPM para que um novo utilizador possa tomar posse, gera-se uma nova chave de raiz de armazenamento. Saiba mais sobre a chave raiz de [armazenamento.](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx)

## <a name="x509-certificates"></a>Certificados X.509

A utilização de certificados X.509 como mecanismo de atestado é uma excelente forma de escalar a produção e simplificar o fornecimento de dispositivos. Os certificados X.509 são normalmente organizados numa cadeia de fidedignidade em que cada certificado da cadeia é assinado pela chave privada do próximo certificado superior, e assim por diante, terminando num certificado de raiz auto-assinado. Este acordo estabelece uma cadeia de confiança delegada a partir do certificado de raiz gerado por uma autoridade de certificado de raiz de confiança (CA) através de cada CA intermédio até ao certificado "folha" de entidade final instalado num dispositivo. Para saber mais, consulte a [Autenticação do Dispositivo utilizando certificados X.509 CA](/azure/iot-hub/iot-hub-x509ca-overview). 

Muitas vezes, a cadeia de certificados representa alguma hierarquia lógica ou física associada aos dispositivos. Por exemplo, um fabricante pode:
- emitir um certificado de CA raiz auto-assinado
- usar o certificado de raiz para gerar um certificado único de CA intermédio para cada fábrica
- utilizar o certificado de cada fábrica para gerar um certificado único de CA intermédio para cada linha de produção na fábrica
- e, finalmente, utilizar o certificado de linha de produção, para gerar um certificado único de dispositivo (entidade final) para cada dispositivo fabricado na linha. 

Para saber mais, consulte a [compreensão conceptual dos certificados De X.509 CA na indústria IoT](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Certificado de raiz

Um certificado de raiz é um certificado X.509 auto-assinado que representa uma autoridade de certificados (CA). É o terminus, ou âncora de confiança, da cadeia de certificados. Os certificados de raiz podem ser autoemitidos por uma organização ou adquiridos a uma autoridade de certificados de raiz. Para saber mais, consulte [Os certificados De Ca X.509](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). O certificado de raiz também pode ser referido como um certificado ca raiz.

### <a name="intermediate-certificate"></a>Certificado intermédio

Um certificado intermédio é um certificado X.509, assinado pelo certificado de raiz (ou por outro certificado intermédio com o certificado de raiz na sua cadeia). O último certificado intermédio numa corrente é utilizado para assinar o certificado de folha. Um certificado intermédio também pode ser referido como um certificado de AC intermédio.

### <a name="end-entity-leaf-certificate"></a>Certificado "folha" de entidade final

O certificado de folha, ou certificado de entidade final, identifica o titular do certificado. Possui o certificado de raiz na sua cadeia de certificados, bem como certificados zero ou mais intermédios. O certificado de folha não é utilizado para assinar outros certificados. Identifica exclusivamente o dispositivo para o serviço de provisionamento e é por vezes referido como o certificado do dispositivo. Durante a autenticação, o dispositivo utiliza a chave privada associada a este certificado para responder a um comprovativo de desafio de posse do serviço.

Os certificados de folha utilizados com uma [inscrição individual](./concepts-service.md#individual-enrollment) têm a obrigação de que o Nome do **Assunto** deve ser definido para o ID de registo da inscrição individual. Os certificados de folha utilizados com uma entrada em [grupo de inscrição](./concepts-service.md#enrollment-group) devem ter o nome do **assunto** definido para o ID do dispositivo pretendido, que será mostrado nos **Registos** de Registo para o dispositivo autenticado no grupo de matrículas.

Para saber mais, consulte [dispositivos autenticadores assinados com certificados X.509 CA](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Controlar o acesso do dispositivo ao serviço de provisionamento com certificados X.509

O serviço de provisionamento expõe dois tipos de entrada de inscrição que pode utilizar para controlar o acesso a dispositivos que utilizam o mecanismo de atestado X.509:  

- As inscrições [individuais](./concepts-service.md#individual-enrollment) estão configuradas com o certificado do dispositivo associado a um dispositivo específico. Estas entradas controlam as matrículas para dispositivos específicos.
- As inscrições no grupo de [matrículas](./concepts-service.md#enrollment-group) estão associadas a um certificado ca intermédio ou raiz específico. Estas entradas controlam as matrículas de todos os dispositivos que tenham esse certificado intermédio ou raiz na sua cadeia de certificados. 

Quando um dispositivo se liga ao serviço de provisionamento, o serviço prioriza entradas de inscrição mais específicas em vez de entradas de matrícula menos específicas. Ou seja, se existir uma inscrição individual para o dispositivo, o serviço de provisionamento aplica essa entrada. Se não houver inscrição individual para o dispositivo e um grupo de inscrição para o primeiro certificado intermédio na cadeia de certificados do dispositivo, o serviço aplica essa entrada, e assim por diante, até à raiz. O serviço aplica a primeira entrada aplicável que encontra, de modo a:

- Se a primeira entrada de matrícula saem ativadas, o serviço prevê o dispositivo.
- Se a primeira entrada de matrícula encontrada estiver desativada, o serviço não for provisionável o dispositivo.  
- Se não for encontrada nenhuma inscrição para qualquer um dos certificados da cadeia de certificados do dispositivo, o serviço não disponibiliza o dispositivo. 

Este mecanismo e a estrutura hierárquica das cadeias de certificados proporcionam uma forte flexibilidade na forma como pode controlar o acesso a dispositivos individuais, bem como a grupos de dispositivos. Por exemplo, imagine cinco dispositivos com as seguintes cadeias de certificados: 

- *Dispositivo 1*: certificado de raiz -> certificado A -> certificado 1 dispositivo
- *Dispositivo 2*: certificado de raiz -> certificado A -> certificado dispositivo 2
- *Dispositivo 3*: certificado de raiz -> certificado A -> certificado dispositivo 3
- *Dispositivo 4*: certificado de raiz -> certificado B -> certificado 4
- *Dispositivo 5*: certificado de raiz -> certificado B -> certificado 5

Inicialmente, pode criar uma única entrada de inscrição em grupo ativada para o certificado raiz para permitir o acesso aos cinco dispositivos. Se o certificado B ficar mais tarde comprometido, pode criar uma entrada de grupo de inscrição para o certificado B para evitar que o *Dispositivo 4* e o *Dispositivo 5* se inscrevam. Se ainda mais tarde *o Dispositivo 3* ficar comprometido, pode criar uma inscrição individual desativada para o seu certificado. Isto revoga o acesso ao *Dispositivo 3,* mas ainda permite que o *Dispositivo 1* e o *Dispositivo 2* se inscrevam.