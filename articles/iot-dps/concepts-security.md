---
title: Serviço de provisionamento de dispositivos no Hub IoT do Azure-conceitos de segurança
description: Descreve os conceitos de provisionamento de segurança específicos para dispositivos com o DPS (serviço de provisionamento de dispositivos) e o Hub IoT
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3191e9886604af9b2a26b71a89cee699197585c4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973463"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Conceitos de segurança do serviço de provisionamento de dispositivos no Hub IoT 

O serviço de provisionamento de dispositivos no Hub IoT é um serviço auxiliar para o Hub IoT que você usa para configurar o provisionamento de dispositivos sem toque para um hub IoT especificado. Com o serviço de provisionamento de dispositivos, você pode [provisionar automaticamente](concepts-auto-provisioning.md) milhões de dispositivos de maneira segura e escalonável. Este artigo fornece uma visão geral dos conceitos de *segurança* envolvidos no provisionamento de dispositivos. Este artigo é relevante para todas as pessoas envolvidas na obtenção de um dispositivo pronto para implantação.

## <a name="attestation-mechanism"></a>Mecanismo de atestado

O mecanismo de atestado é o método usado para confirmar a identidade de um dispositivo. O mecanismo de atestado também é relevante para a lista de registro, que informa ao serviço de provisionamento qual método de atestado usar com um determinado dispositivo.

> [!NOTE]
> O Hub IoT usa "esquema de autenticação" para obter um conceito semelhante nesse serviço.

O serviço de provisionamento de dispositivos dá suporte às seguintes formas de atestado:
* **Certificados x. 509** com base no fluxo de autenticação de certificado x. 509 padrão.
* **Trusted Platform Module (TPM)** com base em um desafio de nonce, usando o padrão do TPM para que as chaves apresentem um token de assinatura de acesso compartilhado (SAS) assinado. Essa forma de atestado não requer um TPM físico no dispositivo, mas o serviço espera atestar usando a chave de endosso de acordo com a [especificação do TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Chave simétrica** baseada em [tokens de segurança](../iot-hub/iot-hub-devguide-security.md#security-tokens)SAS (assinatura de acesso compartilhado), que incluem uma assinatura com hash e uma expiração incorporada. Para obter mais informações, consulte [atestado de chave simétrica](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Módulo de segurança de hardware

O módulo de segurança de hardware, ou HSM, é usado para armazenamento seguro baseado em hardware de segredos de dispositivo e é a forma mais segura de armazenamento secreto. Os certificados X. 509 e os tokens SAS podem ser armazenados no HSM. Os HSMs podem ser usados com ambos os mecanismos de atestado para os quais o provisionamento dá suporte.

> [!TIP]
> É altamente recomendável usar um HSM com dispositivos para armazenar segredos com segurança em seus dispositivos.

Os segredos do dispositivo também podem ser armazenados em software (memória), mas é uma forma menos segura de armazenamento do que um HSM.

## <a name="trusted-platform-module"></a>Trusted Platform Module

O TPM pode se referir a um padrão para armazenar com segurança as chaves usadas para autenticar a plataforma ou pode consultar a interface de e/s usada para interagir com os módulos que implementam o padrão. O TPMs pode existir como hardware discreto, hardware integrado, baseado em firmware ou baseado em software. Saiba mais sobre o [TPMS e o atestado de TPM](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). O serviço de provisionamento de dispositivos só dá suporte ao TPM 2,0.

O atestado de TPM baseia-se em um desafio de nonce, que usa as chaves raiz de armazenamento e endosso para apresentar um token SAS (assinatura de acesso compartilhado) assinado.

### <a name="endorsement-key"></a>Chave de endossamento

A chave de endosso é uma chave assimétrica contida no TPM, que foi gerada internamente ou injetada no tempo de fabricação e é exclusiva para cada TPM. A chave de endosso não pode ser alterada ou removida. A parte privada da chave de endosso nunca é liberada fora do TPM, enquanto a parte pública da chave de endosso é usada para reconhecer um TPM genuíno. Saiba mais sobre a [chave de endosso](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Chave raiz de armazenamento

A chave raiz de armazenamento é armazenada no TPM e é usada para proteger as chaves do TPM criadas por aplicativos, para que essas chaves não possam ser usadas sem o TPM. A chave raiz de armazenamento é gerada quando você se apropria do TPM; Quando você limpa o TPM para que um novo usuário possa assumir a propriedade, uma nova chave de raiz de armazenamento é gerada. Saiba mais sobre a [chave raiz de armazenamento](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>Certificados X. 509

Usar certificados X. 509 como um mecanismo de atestado é uma maneira excelente de dimensionar a produção e simplificar o provisionamento de dispositivos. Os certificados X. 509 normalmente são organizados em uma cadeia de certificados de confiança em que cada certificado na cadeia é assinado pela chave privada do próximo certificado mais alto e assim por diante, terminando em um certificado raiz autoassinado. Esse arranjo estabelece uma cadeia de confiança delegada do certificado raiz gerado por uma AC (autoridade de certificação) raiz confiável por meio de cada AC intermediária para o certificado "folha" da entidade final instalada em um dispositivo. Para saber mais, confira [autenticação de dispositivo usando certificados de autoridade de certificação X. 509](/azure/iot-hub/iot-hub-x509ca-overview). 

Geralmente, a cadeia de certificados representa algumas hierarquias lógicas ou físicas associadas aos dispositivos. Por exemplo, um fabricante pode:
- emitir um certificado de AC raiz autoassinado
- usar o certificado raiz para gerar um certificado de autoridade de certificação intermediário exclusivo para cada fábrica
- usar o certificado de cada fábrica para gerar um certificado de autoridade de certificação intermediário exclusivo para cada linha de produção na planta
- e, finalmente, usar o certificado de linha de produção, para gerar um certificado de dispositivo (entidade final) exclusivo para cada dispositivo fabricado na linha. 

Para saber mais, consulte [compreensão conceitual dos certificados de autoridade de certificação X. 509 no setor IOT](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Certificado raiz

Um certificado raiz é um certificado X. 509 autoassinado que representa uma autoridade de certificação (CA). É a terminal, ou âncora de confiança, da cadeia de certificados. Os certificados raiz podem ser emitidos por conta própria por uma organização ou adquiridos de uma autoridade de certificação raiz. Para saber mais, confira [obter certificados de autoridade de certificação X. 509](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). O certificado raiz também pode ser referido como um certificado de autoridade de certificação raiz.

### <a name="intermediate-certificate"></a>Certificado intermediário

Um certificado intermediário é um certificado X. 509, que foi assinado pelo certificado raiz (ou por outro certificado intermediário com o certificado raiz em sua cadeia). O último certificado intermediário em uma cadeia é usado para assinar o certificado de folha. Um certificado intermediário também pode ser referido como um certificado de autoridade de certificação intermediário.

### <a name="end-entity-leaf-certificate"></a>Certificado de "folha" de entidade final

O certificado de folha ou o certificado de entidade final identifica o proprietário do certificado. Ele tem o certificado raiz em sua cadeia de certificados, bem como zero ou mais certificados intermediários. O certificado de folha não é usado para assinar outros certificados. Ele identifica exclusivamente o dispositivo para o serviço de provisionamento e, às vezes, é chamado de certificado de dispositivo. Durante a autenticação, o dispositivo usa a chave privada associada a esse certificado para responder a um desafio de prova de posse do serviço.

Os certificados folha usados com uma entrada de [registro individual](./concepts-service.md#individual-enrollment) têm um requisito de que o **nome da entidade** deve ser definido como a ID de registro da entrada de registro individual. Os certificados folha usados com uma entrada do [grupo de registros](./concepts-service.md#enrollment-group) devem ter o **nome da entidade** definido como a ID de dispositivo desejada que será mostrada nos registros de **registro** do dispositivo autenticado no grupo de registro.

Para saber mais, confira [autenticando dispositivos assinados com certificados de autoridade de certificação X. 509](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Controlando o acesso do dispositivo ao serviço de provisionamento com certificados X. 509

O serviço de provisionamento expõe dois tipos de entrada de registro que você pode usar para controlar o acesso para dispositivos que usam o mecanismo de atestado X. 509:  

- As entradas de [registro individuais](./concepts-service.md#individual-enrollment) são configuradas com o certificado de dispositivo associado a um dispositivo específico. Essas entradas controlam os registros de dispositivos específicos.
- As entradas do [grupo de registros](./concepts-service.md#enrollment-group) são associadas a um certificado de autoridade de certificação intermediário ou raiz específico. Essas entradas controlam os registros de todos os dispositivos que têm esse certificado raiz ou intermediário em sua cadeia de certificados. 

Quando um dispositivo se conecta ao serviço de provisionamento, o serviço prioriza entradas de registro mais específicas em relação a entradas de registro menos específicas. Ou seja, se um registro individual para o dispositivo existir, o serviço de provisionamento aplicará essa entrada. Se não houver nenhum registro individual para o dispositivo e um grupo de registro para o primeiro certificado intermediário na cadeia de certificados do dispositivo existir, o serviço aplicará essa entrada e assim por diante, na cadeia até a raiz. O serviço aplica a primeira entrada aplicável que encontra, de modo que:

- Se a primeira entrada de registro encontrada estiver habilitada, o serviço provisionará o dispositivo.
- Se a primeira entrada de registro encontrada estiver desabilitada, o serviço não provisionará o dispositivo.  
- Se nenhuma entrada de registro for encontrada para nenhum dos certificados na cadeia de certificados do dispositivo, o serviço não provisionará o dispositivo. 

Esse mecanismo e a estrutura hierárquica de cadeias de certificados fornecem flexibilidade avançada na forma como você pode controlar o acesso a dispositivos individuais, bem como para grupos de dispositivos. Por exemplo, imagine cinco dispositivos com as seguintes cadeias de certificados: 

- *Dispositivo 1*: certificado raiz-> certificado a-> certificado 1 dispositivo
- *Dispositivo 2*: certificado raiz-> certificado a > dispositivo 2
- *Dispositivo 3*: certificado raiz-> certificado a > dispositivo 3
- *Dispositivo 4*: certificado raiz-> certificado B-> certificado do dispositivo 4
- *Dispositivo 5*: certificado raiz-> certificado B-> certificado do dispositivo 5

Inicialmente, você pode criar uma única entrada de registro de grupo habilitado para o certificado raiz para habilitar o acesso para todos os cinco dispositivos. Se o certificado B mais tarde for comprometido, você poderá criar uma entrada de grupo de registros desabilitada para o certificado B para impedir que o *dispositivo 4* e o *dispositivo 5* sejam registrados. Se ainda depois o *dispositivo 3* for comprometido, você poderá criar uma entrada de registro individual desabilitada para seu certificado. Isso revoga o acesso do *dispositivo 3*, mas ainda permite que o *dispositivo 1* e o *dispositivo 2* se registrem.