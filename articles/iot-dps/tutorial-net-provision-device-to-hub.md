---
title: Tutorial - Dispositivo de provisão utilizando o Serviço de Provisionamento de Dispositivos Azure IoT Hub (.NET)
description: Este tutorial mostra como pode forrar o seu dispositivo a um único hub IoT utilizando o Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS) utilizando .NET.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: f9a14ee6ee3e10b36d64ec11fc23807efe2bfaf2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966569"
---
# <a name="tutorial-enroll-the-device-to-an-iot-hub-using-the-azure-iot-hub-provisioning-service-client-net"></a>Tutorial: Inscreva o dispositivo num hub IoT utilizando o Cliente de Serviço de Provisionamento Azure IoT Hub (.NET)

No tutorial anterior, aprendeu a configurar um dispositivo para se ligar ao Serviço Aprovisionamento de Dispositivos. Neste tutorial, vai aprender a utilizar este serviço para aprovisionar o seu dispositivo num hub IoT individual através da **_Inscrição Individual_** e de **_Grupos de Inscrição_**. Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Inscrever o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registado

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, confirme que configura o seu dispositivo e o respetivo *Módulo de Segurança de Hardware*, tal como explicado no tutorial [Configurar um dispositivo para ser aprovisionado com o Serviço Aprovisionamento de Dispositivos no Hub IoT](./tutorial-set-up-device.md).

* Visual Studio

> [!NOTE]
> Não é preciso o Visual Studio. A instalação do [.NET](https://www.microsoft.com/net) é suficiente e os programadores podem utilizar o editor preferido no Windows ou Linux.  

Este tutorial simula o período durante ou imediatamente após o processo de fabrico de hardware, quando as informações do dispositivo são adicionadas ao serviço de aprovisionamento. Este código é, normalmente, executado no PC ou num dispositivo de fábrica que possa executar o código .NET e não deve ser adicionado aos dispositivos.


## <a name="enroll-the-device"></a>Inscrever o dispositivo

Este passo envolve a adição dos artefactos de segurança exclusiva do dispositivo ao Serviço de Aprovisionamento de Dispositivos. Estes artefactos de segurança são os seguintes:

- Para dispositivos baseados em TPM:
    - A *Chave de Endossamento* única para cada chip ou simulação de TPM. Leia [Understand TPM Endorsement Key](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770443(v=ws.11)) (Compreender a Chave de Endossamento de TPM) para obter mais informações.
    - Do *ID de Registo* que é utilizado para identificar exclusivamente um dispositivo no espaço de nomes/âmbito. Este pode ou não ser o mesmo que o ID de dispositivo. O ID é obrigatório para todos os dispositivos. Para os dispositivos baseados em TPM, o ID de registo pode derivar do próprio TPM, como, por exemplo, um hash SHA-256 da Chave de Endossamento de TPM.

- Para dispositivos baseados em X.509:
    - O [certificado X.509 emitido para o dispositivo](/windows/win32/seccertenroll/about-x-509-public-key-certificates), sob a forma de ficheiro *.pem* ou *.cer*. Para inscrição individual, tem de utilizar o *certificado de folha* para o seu sistema X.509, ao passo que, para grupos de inscrição, tem de utilizar o *certificado de arranque* ou um *certificado de signatário* equivalente.
    - Do *ID de Registo* que é utilizado para identificar exclusivamente um dispositivo no espaço de nomes/âmbito. Este pode ou não ser o mesmo que o ID de dispositivo. O ID é obrigatório para todos os dispositivos. Para dispositivos baseados em X.509, o ID de registo é derivado do nome comum do certificado (CN). Para obter mais informações sobre estes requisitos, veja os [Conceitos de dispositivo](./concepts-service.md).

Existem duas formas de inscrever o dispositivo no Serviço Aprovisionamento de Dispositivos:

- **Inscrições Individuais** representam uma entrada para um único dispositivo que pode ser registado com o Serviço Aprovisionamento de Dispositivos. As inscrições individuais podem utilizar certificados X.509 ou tokens SAS (num TPM real ou virtual) como mecanismos de atestação. Recomendamos a utilização das inscrições individuais para os dispositivos que precisam de configurações iniciais exclusivas ou para os dispositivos que só podem utilizar tokens SAS através de TPM como o mecanismo de atestação. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

- **Grupos de inscrição** Representam um grupo de dispositivos que partilham um mecanismo de atestado específico. Recomendamos a utilização de um grupo de inscrição para um grande número de dispositivos que partilhem uma configuração inicial pretendida ou para dispositivos que pertencerão todos ao mesmo inquilino. Os grupos de inscrição são apenas X.509 e todos os partilham um certificado de assinatura na respetiva cadeia de certificados X.509.

### <a name="enroll-the-device-using-individual-enrollments"></a>Inscrever o dispositivo com as Inscrições Individuais

1. No Visual Studio, crie um projeto de Aplicação de Consola do Visual C# com o modelo de projeto **Aplicação de Consola**. Designe o projeto de **DeviceProvisioning**.
    
1. No Explorador de Soluções, clique com o botão direito do rato no projeto **DeviceProvisioning** e, em seguida, clique em **Gerir Pacotes NuGet...**.

1. Na janela **Gestor de Pacotes NuGet**, selecione **Pesquisar** e procure **microsoft.azure.devices.provisioning.service**. Selecione a entrada e clique em **Instalar** para instalar o pacote **Microsoft.Azure.Devices.Provisioning.Service** e aceite os termos de utilização. Este procedimento transfere, instala e adiciona uma referência ao pacote NuGet do [SDK de Serviço de Aprovisionamento de Dispositivo do Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) e às respetivas dependências.

1. Adicione as `using` seguintes declarações no topo do ficheiro **.cs Programa:**
   
    ```csharp
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

1. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do Serviço Aprovisionamento de Dispositivos indicado na secção anterior.
   
    ```csharp
    static readonly string ServiceConnectionString = "{Device Provisioning Service connection string}";

    private const string SampleRegistrationId = "sample-individual-csharp";
    private const string SampleTpmEndorsementKey =
            "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
            "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
            "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
            "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
            "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

1. Adicione o seguinte para implementar a inscrição do dispositivo:

    ```csharp
    static async Task SetRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetRegistrationData");

        Attestation attestation = new TpmAttestation(SampleTpmEndorsementKey);

        IndividualEnrollment individualEnrollment = new IndividualEnrollment(SampleRegistrationId, attestation);

        individualEnrollment.DeviceId = OptionalDeviceId;
        individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;

        Console.WriteLine("\nAdding new individualEnrollment...");
        var serviceClient = ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString);

        IndividualEnrollment individualEnrollmentResult =
            await serviceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);

        Console.WriteLine("\nIndividualEnrollment created with success.");
        Console.WriteLine(individualEnrollmentResult);
    }
    ```

1. Por fim, adicione o seguinte código ao método **Principal** para abrir a ligação ao seu hub IoT e inicie a inscrição:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Provisioning example");

        SetRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.ReadLine();
    ```
        
1. No Visual Studio Solution Explorer, clique com o botão direito na sua solução e, em seguida, clique em **Definir Projetos startUp...**. Selecione **o projeto de arranque único** e, em seguida, selecione o projeto **DeviceProvisioning** no menu suspenso.  

1. Execute a aplicação de dispositivo .NET **DeviceProvisiong**. Deve configurar o aprovisionamento para o dispositivo: 

    ![Execução do registo individual](./media/tutorial-net-provision-device-to-hub/individual.png)

Quando o dispositivo for inscrito com êxito, deverá ver que é apresentado no portal do seguinte modo:

   ![Inscrição bem-sucedida no portal](./media/tutorial-net-provision-device-to-hub/individual-portal.png)

### <a name="enroll-the-device-using-enrollment-groups"></a>Inscrever o dispositivo com os Grupos de Inscrições

> [!NOTE]
> O exemplo do grupo de inscrição exige um certificado X.509.

1. No Explorador de Soluções do Visual Studio, abra o projeto **DeviceProvisioning** criado acima. 

1. Adicione as `using` seguintes declarações no topo do ficheiro **.cs Programa:**
    
    ```csharp
    using System.Security.Cryptography.X509Certificates;
    ```

1. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela localização do certificado X509.
   
    ```csharp
    private const string X509RootCertPathVar = "{X509 Certificate Location}";
    private const string SampleEnrollmentGroupId = "sample-group-csharp";
    ```

1. Adicione o seguinte para **Program.cs** implementar a inscrição no grupo:

    ```csharp
    public static async Task SetGroupRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetGroupRegistrationData");

        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString))
        {
            Console.WriteLine("\nCreating a new enrollmentGroup...");

            var certificate = new X509Certificate2(X509RootCertPathVar);

            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);

            EnrollmentGroup enrollmentGroup = new EnrollmentGroup(SampleEnrollmentGroupId, attestation);

            Console.WriteLine(enrollmentGroup);
            Console.WriteLine("\nAdding new enrollmentGroup...");

            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);

            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
        }
    }
    ```

1. Por fim, substitua o seguinte código ao método **Principal** para abrir a ligação ao seu hub IoT e iniciar a inscrição do grupo:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Group Provisioning example");

        SetGroupRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Execute a aplicação de dispositivo .NET **DeviceProvisiong**. Deve configurar o aprovisionamento do grupo para o dispositivo: 

    ![Execução do registo de grupo](./media/tutorial-net-provision-device-to-hub/group.png)

    Quando o grupo de dispositivos for inscrito com êxito, deverá ver que é apresentado no portal do seguinte modo:

   ![Inscrição de grupo bem-sucedida no portal](./media/tutorial-net-provision-device-to-hub/group-portal.png)


## <a name="start-the-device"></a>Iniciar o dispositivo

Neste momento, a configuração seguinte está pronta para o registo do dispositivo:

1. O dispositivo ou grupo de dispositivos são inscritos no Serviço Aprovisionamento de Dispositivos e 
2. O dispositivo está pronto com a segurança configurada e acessível através da aplicação mediante a utilização do SDK de cliente do Serviço Aprovisionamento de Dispositivos.

Inicie o dispositivo para permitir que a aplicação cliente iniciar o registo no Serviço Aprovisionamento de Dispositivos.  


## <a name="verify-the-device-is-registered"></a>Verificar se o dispositivo está registado

Após o arranque do dispositivo, devem ocorrer as ações seguintes. Veja o [Exemplo de Cliente de Dispositivo de Aprovisionamento](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device) para obter mais detalhes. 

1. O dispositivo envia um pedido de registo ao Serviço Aprovisionamento de Dispositivos.
2. Relativamente aos dispositivos TPM, o Serviço Aprovisionamento de Dispositivos devolve um desafio de registo, ao qual o seu dispositivo vai responder. 
3. Após o registo bem-sucedido, o Serviço Aprovisionamento de Dispositivos reenvia o URI do hub IoT, o ID do dispositivo e a chave de encriptação para o dispositivo. 
4. Em seguida, a aplicação cliente do Hub IoT no dispositivo liga-se ao seu hub. 
5. Quando a ligação ao hub for estabelecida com êxito, deverá ver o dispositivo apresentado no **Device Explorer** do hub IoT. 

    ![Ligação com êxito ao hub no portal](./media/tutorial-net-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Inscrever o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registado

Avance para o próximo tutorial para saber como aprovisionar vários dispositivos em hubs com balanceamento de carga. 

> [!div class="nextstepaction"]
> [Aprovisionar dispositivos em vários hubs IoT com balanceamento de carga](./tutorial-provision-multiple-hubs.md)