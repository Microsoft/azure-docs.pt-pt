---
title: Inscrever o dispositivo TPM no Serviço de Aprovisionamento de Dispositivos do Azure com C# | Microsoft Docs
description: Início rápido do Azure-registrar dispositivo TPM no serviço de provisionamento de dispositivos no Hub C# IOT do Azure usando o SDK do serviço. Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 01/16/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: dcb9d2000ff9432f4360dab7f7df28b5b32482bf
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035755"
---
# <a name="enroll-tpm-device-to-iot-hub-device-provisioning-service-using-c-service-sdk"></a>Inscrever o dispositivo TPM no Serviço de Aprovisionamento de Dispositivos no Hub IoT com o SDK do serviço C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Este artigo mostra como criar programaticamente um registro individual para um dispositivo TPM no serviço de provisionamento de dispositivos no Hub IOT do Azure usando o [ C# SDK do serviço](https://github.com/Azure/azure-iot-sdk-csharp) e um aplicativo .NET Core de exemplo. C# Opcionalmente, você pode registrar um dispositivo TPM simulado no serviço de provisionamento usando essa entrada de registro individual. Embora essas etapas funcionem em computadores Windows e Linux, este artigo usa um computador de desenvolvimento do Windows.

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

1. Verifique se você tem o [Visual Studio 2019](https://www.visualstudio.com/vs/) instalado no seu computador.

1. Verifique se você tem o [SDK do .NET Core](https://www.microsoft.com/net/download/windows) instalado no computador.

1. Conclua as etapas em [Configurar o serviço de provisionamento de dispositivos no Hub IOT com o portal do Azure antes de](./quick-setup-auto-provision.md) continuar.

1. Adicional Se você quiser registrar um dispositivo simulado no final deste guia de início rápido, siga o procedimento em [criar e provisionar um dispositivo TPM simulado usando C# o SDK do dispositivo](quick-create-simulated-device-tpm-csharp.md) até a etapa em que você obtém uma chave de endosso para o dispositivo. Salve a chave de endosso, a ID de registro e, opcionalmente, a ID do dispositivo, pois você precisará usá-las mais tarde neste guia de início rápido.

   > [!NOTE]
   > Não siga as etapas para criar um registro individual usando o portal do Azure.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Obter a cadeia de ligação para o serviço de aprovisionamento

Para o exemplo neste início rápido, precisa da cadeia de ligação para o seu serviço de aprovisionamento.

1. Entre no portal do Azure, selecione **todos os recursos**e, em seguida, o serviço de provisionamento de dispositivos.

1. Escolha **políticas de acesso compartilhado**e, em seguida, selecione a política de acesso que você deseja usar para abrir suas propriedades. Em **política de acesso**, copie e salve a cadeia de conexão de chave primária.

    ![Obter a cadeia de ligação do serviço de aprovisionamento a partir do portal](media/quick-enroll-device-tpm-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-individual-enrollment-sample"></a>Criar o exemplo de inscrição individual

Esta seção mostra como criar um aplicativo de console do .NET Core que adiciona um registro individual para um dispositivo TPM ao serviço de provisionamento. Com algumas modificações, também pode seguir estes passos para criar uma aplicação de consola [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) para adicionar à inscrição individual. Para saber mais sobre como desenvolver com o IoT Core, consulte a [documentação do desenvolvedor do Windows IOT Core](https://docs.microsoft.com/windows/iot-core/).

1. Abra o Visual Studio e selecione **criar um novo projeto**. Em **criar um novo projeto**, escolha o modelo de projeto **aplicativo de console (.NET Core)** para C# e selecione **Avançar**.

1. Nomeie o projeto *CreateTpmEnrollment*e selecione **criar**.

    ![Configurar projeto C# de área de trabalho clássica do Visual Windows](media/quick-enroll-device-tpm-csharp/configure-tpm-app-vs2019.png)

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **CreateTpmEnrollment** e selecione **gerenciar pacotes NuGet**.

1. No **Gerenciador de pacotes NuGet**, selecione **procurar**, pesquise e escolha **Microsoft. Azure. Devices. Provisioning. Service**e, em seguida, selecione **instalar**.

   ![Janela Gestor de Pacote NuGet](media//quick-enroll-device-tpm-csharp/add-nuget.png)

   Esta etapa baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do cliente do serviço de provisionamento IOT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) e suas dependências.

1. Adicione as seguintes `using` instruções após as outras `using` instruções na parte superior de `Program.cs`:
  
   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Adicione os campos a seguir à `Program` classe e faça as alterações listadas.

   ```csharp
   private static string ProvisioningConnectionString = "{Your provisioning service connection string}";
   private const string RegistrationId = "sample-registrationid-csharp";
   private const string TpmEndorsementKey =
       "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
       "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
       "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
       "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
       "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
       
   // Optional parameters
   private const string OptionalDeviceId = "myCSharpDevice";
   private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
   ```

   * Substitua o `ProvisioningConnectionString` valor de espaço reservado pela cadeia de conexão do serviço de provisionamento para o qual você deseja criar o registro.

   * Opcionalmente, pode alterar o ID do registo, a chave de endossamento, o ID do dispositivo e o estado de aprovisionamento.

   * Se você estiver usando este guia de início rápido junto com o guia de início rápido [criar e C# provisionar um dispositivo TPM simulado usando o SDK do dispositivo](quick-create-simulated-device-tpm-csharp.md) para provisionar um dispositivo simulado, substitua a chave de endosso e a ID de registro pelos valores anotados nessa TUTORIAIS. Você pode substituir a ID do dispositivo pelo valor sugerido no início rápido, usar seu próprio valor ou usar o valor padrão neste exemplo.

1. Adicione o método a seguir à `Program` classe.  Esse código cria uma entrada de registro individual e, `CreateOrUpdateIndividualEnrollmentAsync` em seguida, `ProvisioningServiceClient` chama o método no para adicionar o registro individual ao serviço de provisionamento.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");

       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new individualEnrollment config
           Console.WriteLine("\nCreating a new individualEnrollment...");
           Attestation attestation = new TpmAttestation(TpmEndorsementKey);
           IndividualEnrollment individualEnrollment =
                   new IndividualEnrollment(
                           RegistrationId,
                           attestation);

           // The following parameters are optional. Remove them if you don't need them.
           individualEnrollment.DeviceId = OptionalDeviceId;
           individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
           #endregion

           #region Create the individualEnrollment
           Console.WriteLine("\nAdding new individualEnrollment...");
           IndividualEnrollment individualEnrollmentResult =
               await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
           Console.WriteLine("\nIndividualEnrollment created with success.");
           Console.WriteLine(individualEnrollmentResult);
           #endregion
        
       }
   }
   ```

1. Por fim, substitua o corpo do `Main` método pelas seguintes linhas:

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. Compilar a solução.

## <a name="run-the-individual-enrollment-sample"></a>Executar o exemplo de inscrição individual
  
Execute o exemplo no Visual Studio para criar a inscrição individual para o seu dispositivo TPM.

Após a criação bem-sucedida, a janela de prompt de comando exibe as propriedades do novo registro individual.

Você pode verificar se o registro individual foi criado. Vá para o resumo do serviço de provisionamento de dispositivos e selecione **gerenciar registros**e, em seguida, selecione **registros individuais**. Deverá ver uma nova entrada de inscrição que corresponde ao ID de registo utilizado no exemplo.

![Propriedades de inscrição no portal](media/quick-enroll-device-tpm-csharp/verify-enrollment-portal-vs2019.png)

Selecione a entrada para verificar a chave de endosso e outras propriedades da entrada.

Se você esteve seguindo as etapas no guia de início rápido [criar e provisionar um dispositivo C# TPM simulado usando o SDK do dispositivo](quick-create-simulated-device-tpm-csharp.md) , você pode continuar com as etapas restantes nesse guia de início rápido para registrar seu dispositivo simulado. Certifique-se de que ignora os passos para criar uma inscrição individual com o portal do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja explorar o exemplo C# de serviço, não limpe os recursos criados neste guia de início rápido. Caso contrário, use as etapas a seguir para excluir todos os recursos criados por este guia de início rápido.

1. Feche a C# janela de saída de exemplo em seu computador.

1. Navegue até o serviço de provisionamento de dispositivos no portal do Azure, selecione **gerenciar registros**e, em seguida, selecione a guia **registros individuais** . Selecione a *ID de registro* para a entrada de registro que você criou usando este guia de início rápido e selecione **excluir**.

1. Se você seguiu as etapas em [criar e provisionar um dispositivo TPM simulado usando C# o SDK do dispositivo](quick-create-simulated-device-tpm-csharp.md) para criar um dispositivo TPM simulado, execute as seguintes etapas:

    1. Feche a janela do simulador TPM e a janela de saída de exemplo para o dispositivo simulado.

    1. No portal do Azure, navegue para o Hub IoT onde o seu dispositivo foi aprovisionado. No menu em **gerenciadores**, selecione **dispositivos IOT**, marque a caixa de seleção ao lado de seu dispositivo e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou programaticamente uma entrada de registro individual para um dispositivo TPM. Opcionalmente, você criou um dispositivo simulado de TPM no seu computador e o provisionou para o Hub IoT usando o serviço de provisionamento de dispositivos no Hub IoT do Azure. Para ficar a conhecer aprofundadamente o aprovisionamento de dispositivos, prossiga no tutorial para a configuração do Serviço Aprovisionamento de Dispositivos no portal do Azure.

> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service tutorials](./tutorial-set-up-cloud.md) (Tutoriais do Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure)
