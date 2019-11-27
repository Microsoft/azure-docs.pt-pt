---
title: Gerenciar registros de dispositivo usando SDKs do Azure DPS
description: Como gerenciar registros de dispositivo no serviço de provisionamento de dispositivos no Hub IoT usando os SDKs de serviço
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 75a24fd6b8cafe03ae8090e6f8bf71a27459c7eb
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228806"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Como gerenciar registros de dispositivo com SDKs do serviço de provisionamento de dispositivos do Azure
Um *registro de dispositivo* cria um registro de um único dispositivo ou um grupo de dispositivos que podem, em algum momento, ser registrados com o serviço de provisionamento de dispositivos. O registro de registro contém a configuração inicial desejada para os dispositivos como parte desse registro, incluindo o Hub IoT desejado. Este artigo mostra como gerenciar registros de dispositivo para seu serviço de provisionamento programaticamente usando os SDKs do serviço de provisionamento do Azure IoT.  Os SDKs estão disponíveis no GitHub no mesmo repositório que os SDKs do IoT do Azure.

## <a name="prerequisites"></a>Pré-requisitos
* Obtenha a cadeia de conexão da instância do serviço de provisionamento de dispositivos.
* Obtenha os artefatos de segurança do dispositivo para o [mecanismo de atestado](concepts-security.md#attestation-mechanism) usado:
    * [**Trusted Platform Module (TPM)** ](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Registro individual: ID de registro e chave de endosso do TPM de um dispositivo físico ou do simulador do TPM.
        * O grupo de registro não se aplica ao atestado de TPM.
    * [**X. 509**](/azure/iot-dps/concepts-security):
        * Registro individual: o [certificado de folha](/azure/iot-dps/concepts-security) do dispositivo físico ou [do emulador](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) de databases do SDK.
        * Grupo de registros: o [certificado de autoridade de certificação/raiz](/azure/iot-dps/concepts-security#root-certificate) ou o [certificado intermediário](/azure/iot-dps/concepts-security#intermediate-certificate), usado para produzir o certificado de dispositivo em um dispositivo físico.  Ele também pode ser gerado no emulador de SDKs do SDK.
* Chamadas de API exatas podem ser diferentes devido a diferenças de idioma. Examine os exemplos fornecidos no GitHub para obter detalhes:
   * [Exemplos de cliente do serviço de provisionamento Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Exemplos de cliente do serviço de provisionamento do node. js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [Exemplos de cliente do serviço de provisionamento do .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Criar um registro de dispositivo
Há duas maneiras de registrar seus dispositivos com o serviço de provisionamento:

* Um **grupo de registro** é uma entrada para um grupo de dispositivos que compartilham um mecanismo de atestado comum de certificados X. 509, assinados pelo [certificado raiz](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) ou pelo [certificado intermediário](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). É recomendável usar um grupo de registro para um grande número de dispositivos que compartilham uma configuração inicial desejada ou para dispositivos que todos vão para o mesmo locatário. Observe que você só pode registrar dispositivos que usam o mecanismo de atestado X. 509 como *grupos de registro*. 

    Você pode criar um grupo de registro com os SDKs seguindo este fluxo de trabalho:

    1. Para o grupo de registro, o mecanismo de atestado usa o certificado raiz X. 509.  Chame a API do SDK do serviço ```X509Attestation.createFromRootCertificate``` com o certificado raiz para criar o atestado para o registro.  O certificado raiz X. 509 é fornecido em um arquivo PEM ou como uma cadeia de caracteres.
    1. Crie uma nova variável ```EnrollmentGroup``` usando a ```attestation``` criada e uma ```enrollmentGroupId```exclusiva.  Opcionalmente, você pode definir parâmetros como ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    2. Chame a API do SDK do serviço ```createOrUpdateEnrollmentGroup``` em seu aplicativo de back-end com ```EnrollmentGroup``` para criar um grupo de registros.

* Um **registro individual** é uma entrada para um único dispositivo que pode ser registrado. Registros individuais podem usar certificados X. 509 ou tokens SAS (de um TPM físico ou virtual) como mecanismos de atestado. É recomendável usar registros individuais para dispositivos que exigem configurações iniciais exclusivas ou para dispositivos que só podem usar tokens SAS via TPM ou TPM virtual como o mecanismo de atestado. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

    Você pode criar um registro individual com os SDKs seguindo este fluxo de trabalho:
    
    1. Escolha seu mecanismo de ```attestation```, que pode ser TPM ou X. 509.
        1. **TPM**: usando a chave de endosso de um dispositivo físico ou do simulador do TPM como a entrada, você pode chamar a API do SDK do serviço ```TpmAttestation``` para criar um atestado para o registro. 
        2. **X. 509**: usando o certificado do cliente como a entrada, você pode chamar a API do SDK do serviço ```X509Attestation.createFromClientCertificate``` para criar um atestado para o registro.
    2. Crie uma nova variável ```IndividualEnrollment``` com o uso do ```attestation``` criado e um ```registrationId``` exclusivo como entrada, que está em seu dispositivo ou gerado a partir do simulador do TPM.  Opcionalmente, você pode definir parâmetros como ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    3. Chame a API do SDK do serviço ```createOrUpdateIndividualEnrollment``` em seu aplicativo de back-end com ```IndividualEnrollment``` para criar um registro individual.

Depois de ter criado um registro com êxito, o serviço de provisionamento de dispositivos retorna um resultado de registro. Este fluxo de trabalho é demonstrado nos exemplos [mencionados anteriormente](#prerequisites).

## <a name="update-an-enrollment-entry"></a>Atualizar uma entrada de registro

Depois de criar uma entrada de registro, talvez você queira atualizar o registro.  Os cenários potenciais incluem a atualização da propriedade desejada, a atualização do método de atestado ou a revogação do acesso ao dispositivo.  Há APIs diferentes para registro individual e registro de grupo, mas sem distinção para o mecanismo de atestado.

Você pode atualizar uma entrada de registro seguindo este fluxo de trabalho:
* **Inscrição individual**:
    1. Obtenha o registro mais recente do serviço de provisionamento primeiro com a API do SDK do serviço ```getIndividualEnrollment```.
    2. Modifique o parâmetro do registro mais recente, conforme necessário. 
    3. Usando o registro mais recente, chame a API SDK do serviço ```createOrUpdateIndividualEnrollment``` para atualizar sua entrada de registro.
* **Registro de grupo**:
    1. Obtenha o registro mais recente do serviço de provisionamento primeiro com a API do SDK do serviço ```getEnrollmentGroup```.
    2. Modifique o parâmetro do registro mais recente, conforme necessário.
    3. Usando o registro mais recente, chame a API SDK do serviço ```createOrUpdateEnrollmentGroup``` para atualizar sua entrada de registro.

Este fluxo de trabalho é demonstrado nos exemplos [mencionados anteriormente](#prerequisites).

## <a name="remove-an-enrollment-entry"></a>Remover uma entrada de registro

* O **registro individual** pode ser excluído chamando ```deleteIndividualEnrollment``` de API do SDK do serviço usando ```registrationId```.
* O **registro de grupo** pode ser excluído chamando ```deleteEnrollmentGroup``` de API do SDK do serviço usando ```enrollmentGroupId```.

Este fluxo de trabalho é demonstrado nos exemplos [mencionados anteriormente](#prerequisites).

## <a name="bulk-operation-on-individual-enrollments"></a>Operação em massa em registros individuais

Você pode executar a operação em massa para criar, atualizar ou remover vários registros individuais seguindo este fluxo de trabalho:

1. Crie uma variável que contenha vários ```IndividualEnrollment```.  A implementação dessa variável é diferente para cada idioma.  Examine o exemplo de operação em massa no GitHub para obter detalhes.
2. Chame a API do SDK do serviço ```runBulkOperation``` com um ```BulkOperationMode``` para a operação desejada e sua variável para registros individuais. Há suporte para quatro modos: criar, atualizar, updateIfMatchEtag e excluir.

Depois de executar uma operação com êxito, o serviço de provisionamento de dispositivos retornaria um resultado de operação em massa.

Este fluxo de trabalho é demonstrado nos exemplos [mencionados anteriormente](#prerequisites).
