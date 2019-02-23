---
title: Gerir inscrições de dispositivos através de SDKs serviço de aprovisionamento do dispositivo do Azure | Documentos da Microsoft
description: Como gerir inscrições de dispositivos no IoT Hub serviço aprovisionamento de dispositivos com os SDKs de serviço
author: yzhong94
ms.author: yizhon
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: c73a40e46d86632732454ae16ea4f83e3ffa0281
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674757"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Como gerir inscrições de dispositivos com SDKs serviço de aprovisionamento do dispositivo do Azure
R *inscrição de dispositivos* cria um registo de um único dispositivo ou um grupo de dispositivos que pode ser em algum momento registado com o serviço de aprovisionamento de dispositivos. O registo de inscrição contém a configuração inicial pretendida para o dispositivo como parte do que a inscrição, incluindo o hub IoT desejado (s). Este artigo mostra-lhe como gerir inscrições de dispositivos para o serviço de aprovisionamento programaticamente com os SDKs de serviço de aprovisionamento do Azure IoT.  Os SDKs estão disponíveis no GitHub no mesmo repositório como SDKs IoT do Azure.

## <a name="prerequisites"></a>Pré-requisitos
* Obter a cadeia de ligação da sua instância do serviço aprovisionamento de dispositivos.
* Obter o dispositivo artefactos de segurança para o [mecanismo de atestado](concepts-security.md#attestation-mechanism) utilizado:
    * [**Trusted Platform Module (TPM)**](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Inscrição individual: ID de registo e a chave de endossamento de TPM de um dispositivo físico ou de simulador de TPM.
        * Grupo de inscrição não é aplicável a atestado de TPM.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * Inscrição individual: O [certificado de folha](/azure/iot-dps/concepts-security) do dispositivo físico ou do SDK [DICE](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) emulador.
        * Grupo de inscrição: O [certificado de AC/raiz](/azure/iot-dps/concepts-security#root-certificate) ou o [certificado intermédio](/azure/iot-dps/concepts-security#intermediate-certificate), utilizado para gerar o certificado de dispositivo num dispositivo físico.  Também pode ser gerada do emulador SDK DICE.
* Chamadas de API exatas podem ser diferentes devido às diferenças de idioma. Reveja os exemplos fornecidos no GitHub para obter mais detalhes:
   * [Exemplos de cliente do serviço de aprovisionamento de Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Exemplos de cliente do serviço de aprovisionamento de node. js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [Exemplos de cliente de serviço de aprovisionamento de .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Criar uma inscrição de dispositivos
Existem duas formas, pode inscrever os seus dispositivos com o serviço de aprovisionamento:

* Uma **grupo de inscrição** é uma entrada para um grupo de dispositivos que partilham um mecanismo de atestado comuns de certificados X.509, assinados pela [certificado de raiz](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) ou a [certificado intermédio ](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). Recomendamos que utilize um grupo de inscrição para um grande número de dispositivos que partilham uma configuração inicial pretendida ou para dispositivos pertencerão todos ao mesmo inquilino. Tenha em atenção que só pode inscrever dispositivos que utilizam o mecanismo de atestado de X.509 como *grupos de inscrição*. 

    Pode criar um grupo de inscrição com os SDKs segue esse fluxo de trabalho:

    1. Para o grupo de inscrição, o mecanismo de atestado utiliza o certificado de raiz X.509.  Chamar a API do SDK de serviço ```X509Attestation.createFromRootCertificate``` com certificado de raiz para criar o atestado para a inscrição.  Certificado de raiz X.509 é fornecido num ficheiro PEM ou como uma cadeia de caracteres.
    1. Criar uma nova ```EnrollmentGroup``` variável usando o ```attestation``` criada e um exclusivo ```enrollmentGroupId```.  Opcionalmente, pode definir parâmetros, como ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    2. Chamar a API do SDK de serviço ```createOrUpdateEnrollmentGroup``` na sua aplicação de back-end com ```EnrollmentGroup``` para criar um grupo de inscrição.

* Uma **inscrição Individual** é uma entrada para um único dispositivo que pode ser registado. Inscrições individuais podem utilizar certificados X.509 ou tokens SAS (a partir de um TPM físico ou virtual) como mecanismos de atestação. Recomendamos a utilização das inscrições individuais para os dispositivos que precisam de configurações iniciais exclusivas ou para dispositivos que só podem utilizar tokens SAS através de TPM ou de virtual TPM como o mecanismo de atestação. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

    Pode criar uma inscrição individual com os SDKs segue esse fluxo de trabalho:
    
    1. Escolha sua ```attestation``` mecanismo, o que pode ser TPM ou X.509.
        1. **TPM**: Utilizar a chave de endossamento de um dispositivo físico ou de simulador de TPM como entrada, pode chamar a API do serviço SDK ```TpmAttestation``` para criar o atestado para a inscrição. 
        2. **X.509**: Utilizar o certificado de cliente como entrada, pode chamar a API do serviço SDK ```X509Attestation.createFromClientCertificate``` para criar o atestado para a inscrição.
    2. Criar um novo ```IndividualEnrollment``` variável com o uso do ```attestation``` criado bem como uma única ```registrationId``` como entrada, que é no seu dispositivo ou gerado a partir do simulador de TPM.  Opcionalmente, pode definir parâmetros, como ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    3. Chamar a API do SDK de serviço ```createOrUpdateIndividualEnrollment``` na sua aplicação de back-end com ```IndividualEnrollment``` para criar uma inscrição individual.

Depois de criar uma inscrição com êxito, o serviço de aprovisionamento de dispositivos devolve um resultado de inscrição. Este fluxo de trabalho é demonstrado nos exemplos [mencionado anteriormente](#prerequisites).

## <a name="update-an-enrollment-entry"></a>Atualizar uma entrada de inscrição

Depois de criar uma entrada de inscrição, pode querer atualizar a inscrição.  Cenários possíveis incluem a atualizar a propriedade pretendida, atualizar o método de atestado ou revogar o acesso do dispositivo.  Existem APIs diferentes para inscrição individual e a inscrição de grupo, mas nenhuma distinção era feita para o mecanismo de atestado.

Pode atualizar uma entrada de inscrição segue esse fluxo de trabalho:
* **Inscrição individual**:
    1. Chegue a inscrição mais recente do serviço de aprovisionamento com a API do SDK de serviço ```getIndividualEnrollment```.
    2. Modifique o parâmetro de Registro do mais recente, se necessário. 
    3. Utilizar a inscrição mais recente, chamar a API do SDK de serviço ```createOrUpdateIndividualEnrollment``` para atualizar a entrada de inscrição.
* **Inscrição de grupo**:
    1. Chegue a inscrição mais recente do serviço de aprovisionamento com a API do SDK de serviço ```getEnrollmentGroup```.
    2. Modifique o parâmetro de Registro do mais recente, se necessário.
    3. Utilizar a inscrição mais recente, chamar a API do SDK de serviço ```createOrUpdateEnrollmentGroup``` para atualizar a entrada de inscrição.

Este fluxo de trabalho é demonstrado nos exemplos [mencionado anteriormente](#prerequisites).

## <a name="remove-an-enrollment-entry"></a>Remover uma entrada de inscrição

* **Inscrição individual** pode ser eliminado ao chamar a API do SDK de serviço ```deleteIndividualEnrollment``` usando ```registrationId```.
* **Inscrição de grupo** pode ser eliminado ao chamar a API do SDK de serviço ```deleteEnrollmentGroup``` usando ```enrollmentGroupId```.

Este fluxo de trabalho é demonstrado nos exemplos [mencionado anteriormente](#prerequisites).

## <a name="bulk-operation-on-individual-enrollments"></a>Operação em massa no inscrições individuais

Pode executar a operação em massa para criar, atualizar ou remover várias inscrições individuais segue esse fluxo de trabalho:

1. Criar uma variável que contém vários ```IndividualEnrollment```.  Implementação desta variável é diferente para cada idioma.  Rever o exemplo de operação em massa no GitHub para obter detalhes.
2. Chamar a API do SDK de serviço ```runBulkOperation``` com um ```BulkOperationMode``` para operação desejada e a variável para inscrições individuais. São suportados quatro modos: criar, atualizar, updateIfMatchEtag e eliminar.

Após executar uma operação com êxito, o serviço de aprovisionamento de dispositivos retornaria um resultado da operação em massa.

Este fluxo de trabalho é demonstrado nos exemplos [mencionado anteriormente](#prerequisites).
