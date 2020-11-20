---
title: Gerir as inscrições dos dispositivos utilizando SDKs Azure DPS
description: Como gerir as inscrições de dispositivos no Serviço de Provisionamento de Dispositivos IoT Hub (DPS) utilizando os SDKs de serviço
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
ms.custom: fasttrack-edit, iot
services: iot-dps
ms.openlocfilehash: 45a2b7a64006ab6963290be3ac86a3a5d1e4916d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959888"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Como gerir as inscrições de dispositivos com SDKs de serviço de fornecimento de dispositivos Azure
Uma *inscrição de dispositivo* cria um registo de um único dispositivo ou de um grupo de dispositivos que podem em algum momento registar-se com o Serviço de Provisionamento de Dispositivos. O registo de inscrição contém a configuração inicial desejada para o(s) dispositivo(s) como parte dessa inscrição, incluindo o hub IoT desejado. Este artigo mostra-lhe como gerir as inscrições do dispositivo para o seu serviço de fornecimento programáticamente usando os SDKs do Serviço de Provisionamento Azure IoT.  Os SDKs estão disponíveis no GitHub no mesmo repositório que os SDKs Azure IoT.

## <a name="prerequisites"></a>Pré-requisitos
* Obtenha a cadeia de ligação a partir da sua instância de Serviço de Provisionamento de Dispositivos.
* Obtenha os artefactos de segurança do dispositivo para o [mecanismo de atestado](concepts-service.md#attestation-mechanism) utilizado:
    * [**Módulo de plataforma fidedigno (TPM)**](./concepts-tpm-attestation.md):
        * Inscrição individual: ID de inscrição e chave de endosso TPM a partir de um dispositivo físico ou de TPM Simulator.
        * O grupo de inscrições não se aplica ao atestado de TPM.
    * [**X.509:**](./concepts-service.md#attestation-mechanism)
        * Inscrição individual: O [certificado Leaf](./concepts-service.md#attestation-mechanism) do dispositivo físico ou do Emulador SDK [DICE.](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)
        * Grupo de inscrição: O [certificado CA/raiz](./concepts-x509-attestation.md#root-certificate) ou o [certificado intermédio,](./concepts-x509-attestation.md#intermediate-certificate)utilizado para produzir certificado de dispositivo num dispositivo físico.  Também pode ser gerado a partir do emulador SDK DICE.
* Chamadas API exatas podem ser diferentes devido a diferenças linguísticas. Por favor, reveja as amostras fornecidas no GitHub para mais detalhes:
   * [Amostras de clientes de serviço de fornecimento de Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Node.js Amostras de Cliente de Serviço de Prestação de Serviços](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [.NET Provisioning Service Client samples](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Criar uma inscrição de dispositivo
Existem duas formas de inscrever os seus dispositivos com o serviço de fornecimento:

* Um **grupo de inscrições** é uma entrada para um grupo de dispositivos que partilham um mecanismo comum de atestado de certificados X.509, assinado pelo [certificado raiz](./concepts-x509-attestation.md#root-certificate) ou pelo certificado [intermédio](./concepts-x509-attestation.md#intermediate-certificate). Recomendamos a utilização de um grupo de inscrições para um grande número de dispositivos que partilham uma configuração inicial desejada, ou para dispositivos que vão todos para o mesmo inquilino. Note que só pode inscrever dispositivos que utilizem o mecanismo de atestado X.509 como *grupos de inscrição*. 

    Pode criar um grupo de inscrições com os SDKs seguindo este fluxo de trabalho:

    1. Para o grupo de inscrição, o mecanismo de atestação utiliza o certificado raiz X.509.  Ligue para o Serviço SDK API ```X509Attestation.createFromRootCertificate``` com certificado de raiz para criar atestado para inscrição.  O certificado de raiz X.509 é fornecido num ficheiro PEM ou como uma cadeia.
    1. Crie uma nova ```EnrollmentGroup``` variável utilizando o ```attestation``` criado e um ```enrollmentGroupId``` único.  Opcionalmente, pode definir parâmetros como ```IoTHubHostName``` , ```ProvisioningStatus``` .
    2. Ligue para o Serviço SDK API ```createOrUpdateEnrollmentGroup``` na sua aplicação backend ```EnrollmentGroup``` com a criação de um grupo de inscrição.

* Uma **inscrição individual** é uma entrada para um único dispositivo que pode registar-se. As inscrições individuais podem utilizar certificados X.509 ou fichas SAS (de um TPM físico ou virtual) como mecanismos de atestação. Recomendamos a utilização de inscrições individuais para dispositivos que exijam configurações iniciais únicas, ou para dispositivos que só podem usar fichas SAS via TPM ou TPM virtual como mecanismo de atestado. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

    Pode criar uma inscrição individual com os SDKs após este fluxo de trabalho:
    
    1. Escolha o seu ```attestation``` mecanismo, que pode ser TPM ou X.509.
        1. **TPM**: Utilizando a Chave de Endossamento a partir de um dispositivo físico ou do TPM Simulator como entrada, pode ligar para a Service SDK API ```TpmAttestation``` para criar atestado para inscrição. 
        2. **X.509**: Utilizando o certificado de cliente como entrada, pode ligar para a Service SDK API ```X509Attestation.createFromClientCertificate``` para criar atestado para inscrição.
    2. Crie uma nova ```IndividualEnrollment``` variável com a utilização do ```attestation``` criado e único como ```registrationId``` entrada, que está no seu dispositivo ou gerada a partir do Simulador de TPM.  Opcionalmente, pode definir parâmetros como ```Device ID``` ```IoTHubHostName``` , . ```ProvisioningStatus``` .
    3. Ligue para o Serviço SDK API ```createOrUpdateIndividualEnrollment``` na sua aplicação backend ```IndividualEnrollment``` com a criação de uma inscrição individual.

Depois de ter criado com sucesso uma inscrição, o Serviço de Provisionamento de Dispositivos devolve um resultado de inscrição. Este fluxo de trabalho é demonstrado nas amostras [mencionadas anteriormente.](#prerequisites)

## <a name="update-an-enrollment-entry"></a>Atualizar uma entrada de inscrição

Depois de ter criado uma inscrição, poderá querer atualizar a inscrição.  Os cenários potenciais incluem a atualização da propriedade desejada, a atualização do método de atestado ou a revogação do acesso ao dispositivo.  Existem diferentes APIs para a inscrição individual e a inscrição em grupo, mas nenhuma distinção para o mecanismo de atestado.

Pode atualizar uma inscrição após este fluxo de trabalho:
* **Inscrição individual**:
    1. Obtenha a mais recente inscrição do serviço de fornecimento primeiro com o Service SDK API ```getIndividualEnrollment``` .
    2. Modifique o parâmetro da última inscrição, se necessário. 
    3. Utilizando a mais recente inscrição, ligue para o Serviço SDK API ```createOrUpdateIndividualEnrollment``` para atualizar a sua entrada de inscrição.
* **Inscrição em grupo:**
    1. Obtenha a mais recente inscrição do serviço de fornecimento primeiro com o Service SDK API ```getEnrollmentGroup``` .
    2. Modifique o parâmetro da última inscrição, se necessário.
    3. Utilizando a mais recente inscrição, ligue para o Serviço SDK API ```createOrUpdateEnrollmentGroup``` para atualizar a sua entrada de inscrição.

Este fluxo de trabalho é demonstrado nas amostras [mencionadas anteriormente.](#prerequisites)

## <a name="remove-an-enrollment-entry"></a>Remover uma entrada de inscrição

* **As inscrições individuais** podem ser eliminadas através da chamada Service SDK API ```deleteIndividualEnrollment``` utilizando ```registrationId``` .
* **A inscrição em grupo** pode ser eliminada através da chamada Service SDK API ```deleteEnrollmentGroup``` utilizando ```enrollmentGroupId``` .

Este fluxo de trabalho é demonstrado nas amostras [mencionadas anteriormente.](#prerequisites)

## <a name="bulk-operation-on-individual-enrollments"></a>Operação a granel em matrículas individuais

Pode realizar uma operação a granel para criar, atualizar ou remover várias matrículas individuais após este fluxo de trabalho:

1. Crie uma variável que contenha ```IndividualEnrollment``` múltiplos.  A implementação desta variável é diferente para cada língua.  Reveja a amostra de operação a granel no GitHub para obter mais detalhes.
2. Ligue para o Serviço SDK API ```runBulkOperation``` com uma ```BulkOperationMode``` operação desejada e a sua variável para inscrições individuais. Quatro modos são suportados: criar, atualizar, actualizarIfMatchEtag e eliminar.

Depois de ter realizado uma operação com sucesso, o Serviço de Provisionamento de Dispositivos devolveria um resultado de operação a granel.

Este fluxo de trabalho é demonstrado nas amostras [mencionadas anteriormente.](#prerequisites)