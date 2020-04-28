---
title: Gerir as matrículas do dispositivo utilizando SDKs DPS Azure
description: Como gerir as inscrições do dispositivo no Serviço de Provisionamento de Dispositivos IoT Hub (DPS) utilizando os SDKs de Serviço
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5cb0e25ec70956e66f7b867f0d0b9473160fc3ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74975079"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Como gerir as inscrições com o Serviço de Provisionamento de Dispositivos Azure SDKs
Uma *inscrição* de dispositivo cria um registo de um único dispositivo ou de um grupo de dispositivos que podem, em algum momento, registar-se no Serviço de Provisionamento de Dispositivos. O registo de inscrição contém a configuração inicial desejada para o(s) dispositivo(s) como parte dessa inscrição, incluindo o centro IoT desejado. Este artigo mostra-lhe como gerir as matrículas do dispositivo para o seu serviço de provisionamento programáticamente utilizando os SDKs do Serviço de Provisionamento Azure IoT.  Os SDKs estão disponíveis no GitHub no mesmo repositório que os SDKs Azure IoT.

## <a name="prerequisites"></a>Pré-requisitos
* Obtenha a cadeia de ligação da sua instância de Serviço de Provisionamento de Dispositivos.
* Obtenha os artefactos de segurança do dispositivo para o mecanismo de [atestado](concepts-security.md#attestation-mechanism) utilizado:
    * [**Módulo de Plataforma Fidedigna (TPM)**](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Inscrição individual: ID de registo e chave de averbamento TPM de um dispositivo físico ou de Simulador TPM.
        * O grupo de inscrições não se aplica ao atestado TPM.
    * [**X.509:**](/azure/iot-dps/concepts-security)
        * Inscrição individual: O [certificado Leaf](/azure/iot-dps/concepts-security) do dispositivo físico ou do Emulador SDK [DICE.](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)
        * Grupo de inscrições: O [certificado CA/raiz](/azure/iot-dps/concepts-security#root-certificate) ou o [certificado intermédio,](/azure/iot-dps/concepts-security#intermediate-certificate)utilizado para a elaboração de certificado de dispositivo num dispositivo físico.  Também pode ser gerado a partir do emulador SDK DICE.
* Chamadas exatas da API podem ser diferentes devido às diferenças linguísticas. Por favor, reveja as amostras fornecidas no GitHub para obter detalhes:
   * [Amostras do Cliente do Serviço de Provisionamento Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Amostras de clientes de serviço de provisionamento nonóno.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [.NET Provisioning Service Amostras de clientes](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Criar uma inscrição de dispositivo
Existem duas formas de inscrever os seus dispositivos no serviço de provisionamento:

* Um grupo de **inscrições** é uma entrada para um grupo de dispositivos que partilham um mecanismo comum de atestado de certificados X.509, assinado sacar o [certificado](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) raiz ou o [certificado intermédio.](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate) Recomendamos a utilização de um grupo de inscrições para um grande número de dispositivos que partilhem uma configuração inicial desejada, ou para dispositivos que vão todos para o mesmo inquilino. Note que só pode inscrever dispositivos que utilizem o mecanismo de atesta X.509 como *grupos de inscrição*. 

    Pode criar um grupo de inscrições com os SDKs seguindo este fluxo de trabalho:

    1. Para o grupo de inscrições, o mecanismo de atestado utiliza certificado raiz X.509.  Ligue para o Serviço ```X509Attestation.createFromRootCertificate``` SDK API com certificado de raiz para criar atestado para inscrição.  O certificado de raiz X.509 é fornecido num ficheiro PEM ou como uma corda.
    1. Criar uma ```EnrollmentGroup``` nova ```attestation``` variável usando ```enrollmentGroupId```o criado e um único.  Opcionalmente, pode definir parâmetros ```IoTHubHostName``` ```ProvisioningStatus```como, ```Device ID```.
    2. Ligue para o Serviço ```createOrUpdateEnrollmentGroup``` SDK API na sua aplicação backend com ```EnrollmentGroup``` para criar um grupo de inscrições.

* Uma **inscrição individual** é uma entrada para um único dispositivo que pode registar-se. As matrículas individuais podem utilizar certificados X.509 ou tokens SAS (de um TPM físico ou virtual) como mecanismos de atestado. Recomendamos a utilização de inscrições individuais para dispositivos que requeiram configurações iniciais únicas, ou para dispositivos que só podem utilizar tokens SAS via TPM ou TPM virtual como mecanismo de atestado. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

    Pode criar uma inscrição individual com os SDKs seguindo este fluxo de trabalho:
    
    1. Escolha ```attestation``` o seu mecanismo, que pode ser TPM ou X.509.
        1. **TPM**: Utilizando a chave de apoio de um dispositivo físico ou do Simulador TPM como entrada, pode ligar para o Serviço SDK API ```TpmAttestation``` para criar atestado para inscrição. 
        2. **X.509**: Utilizando o certificado de cliente como entrada, pode ```X509Attestation.createFromClientCertificate``` ligar para o Serviço SDK API para criar atestado para inscrição.
    2. Crie ```IndividualEnrollment``` uma nova ```attestation``` variável com ```registrationId``` a utilização do criado e único como entrada, que está no seu dispositivo ou gerada a partir do Simulador TPM.  Opcionalmente, pode definir parâmetros ```IoTHubHostName``` ```ProvisioningStatus```como, ```Device ID```.
    3. Ligue para o Serviço ```createOrUpdateIndividualEnrollment``` SDK API na sua aplicação backend com ```IndividualEnrollment``` para criar uma inscrição individual.

Depois de ter criado com sucesso uma inscrição, o Serviço de Provisionamento de Dispositivos devolve um resultado de inscrição. Este fluxo de trabalho é demonstrado nas amostras [anteriormente mencionadas](#prerequisites).

## <a name="update-an-enrollment-entry"></a>Atualizar uma entrada de inscrição

Depois de ter criado uma inscrição, pode querer atualizar a inscrição.  Os cenários potenciais incluem a atualização da propriedade desejada, a atualização do método de atestado ou o acesso ao dispositivo de revogação.  Existem diferentes APIs para inscrições individuais e inscrições em grupo, mas não há distinção para mecanismo de atestado.

Pode atualizar uma inscrição após este fluxo de trabalho:
* **Inscrição individual**:
    1. Obtenha a mais recente inscrição do serviço de ```getIndividualEnrollment```provisionamento primeiro com o Service SDK API .
    2. Modificar o parâmetro da última matrícula, se necessário. 
    3. Utilizando as últimas inscrições, ligue ```createOrUpdateIndividualEnrollment``` para o Serviço SDK API para atualizar a sua inscrição.
* **Inscrição em grupo:**
    1. Obtenha a mais recente inscrição do serviço de ```getEnrollmentGroup```provisionamento primeiro com o Service SDK API .
    2. Modificar o parâmetro da última matrícula, se necessário.
    3. Utilizando as últimas inscrições, ligue ```createOrUpdateEnrollmentGroup``` para o Serviço SDK API para atualizar a sua inscrição.

Este fluxo de trabalho é demonstrado nas amostras [anteriormente mencionadas](#prerequisites).

## <a name="remove-an-enrollment-entry"></a>Remover uma inscrição

* **As inscrições individuais** podem ser eliminadas ```registrationId```através da utilização de API ```deleteIndividualEnrollment``` do Serviço SDK .
* **A inscrição em grupo** pode ser ```deleteEnrollmentGroup``` eliminada ```enrollmentGroupId```através da chamada Service SDK API utilizando .

Este fluxo de trabalho é demonstrado nas amostras [anteriormente mencionadas](#prerequisites).

## <a name="bulk-operation-on-individual-enrollments"></a>Operação a granel em matrículas individuais

Pode executar uma operação a granel para criar, atualizar ou remover várias matrículas individuais após este fluxo de trabalho:

1. Crie uma variável que contenha múltiplos ```IndividualEnrollment```.  A implementação desta variável é diferente para cada idioma.  Reveja a amostra de operação a granel no GitHub para obter mais detalhes.
2. Ligue para o Serviço ```runBulkOperation``` SDK API com uma ```BulkOperationMode``` operação desejada e a sua variável para inscrições individuais. São suportados quatro modos: criar, atualizar, atualizar IfMatchEtag e eliminar.

Depois de ter realizado uma operação com sucesso, o Serviço de Provisionamento de Dispositivos devolveria um resultado de operação a granel.

Este fluxo de trabalho é demonstrado nas amostras [anteriormente mencionadas](#prerequisites).
