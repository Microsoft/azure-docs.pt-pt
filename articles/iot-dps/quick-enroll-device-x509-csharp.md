---
title: 'Início rápido: como registrar o dispositivo X. 509 no serviço de provisionamento de dispositivos do Azure usandoC#'
description: Este início rápido utiliza inscrições em grupo. Neste guia de início rápido, registre os dispositivos X. 509 no serviço de provisionamento de dispositivos do Hub C#IOT do Azure usando.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: e43448337f787115c479f2f53ca57b7a20120108
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903426"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Início Rápido: Inscrever dispositivos X.509 no Serviço de Aprovisionamento de Dispositivos com C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Este início rápido mostra como utilizar o C# para criar programaticamente um [Grupo de inscrição](concepts-service.md#enrollment-group) que utiliza certificados X.509 de AC de raiz ou intermediários. O grupo de registros é criado usando o [SDK Microsoft Azure IOT para .net](https://github.com/Azure/azure-iot-sdk-csharp) e um aplicativo C# .NET Core de exemplo. Um grupo de inscrição controla o acesso ao serviço de aprovisionamento de dispositivos que partilham um certificado de assinatura comum na respetiva cadeia de certificados. Para saber mais, veja [Controlar o acesso a dispositivos para o serviço de aprovisionamento com certificados X.509](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Para obter mais informações sobre como utilizar a Infraestrutura de Chaves Públicas (PKI) baseada em certificados X.509 com o Hub IoT do Azure e o Serviço de Aprovisionamento de Dispositivos, veja [Descrição geral da segurança do certificado de AC X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Este início rápido espera que você já tenha criado um hub IoT e uma instância do serviço de provisionamento de dispositivos. Se você ainda não criou esses recursos, conclua a [configuração do serviço de provisionamento de dispositivos no Hub IOT com o guia de início rápido do portal do Azure](./quick-setup-auto-provision.md) antes de continuar com este artigo.

Embora as etapas neste artigo funcionem em computadores Windows e Linux, este artigo usa um computador de desenvolvimento do Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Instale o [Visual Studio 2019](https://www.visualstudio.com/vs/).
* Instale o [SDK do .NET Core](https://www.microsoft.com/net/download/windows).
* Instale o [Git](https://git-scm.com/download/).

## <a name="prepare-test-certificates"></a>Preparar os certificados de teste

Neste início rápido, precisa de ter um ficheiro .pem ou .cer com a parte pública de um certificado X.509 de AC de raiz ou intermediário. Este certificado tem de ser carregado para o serviço de aprovisionamento e verificado pelo serviço.

O [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) contém ferramentas de teste que podem ajudá-lo a criar uma cadeia de certificados X. 509, carregar um certificado raiz ou intermediário dessa cadeia e fazer uma prova de posse com o serviço para verificar o certificado.

> [!CAUTION]
> Use certificados criados com as ferramentas do SDK somente para teste de desenvolvimento.
> Não use esses certificados na produção.
> Eles contêm Senhas embutidas em código, como *1234*, que expiram após 30 dias.
> Para saber como obter certificados adequados para utilização de produção, veja [Como obter um certificado X.509 de AC](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) na documentação do Hub IoT do Azure.
>

Para usar essas ferramentas de teste para gerar certificados, execute as seguintes etapas:

1. Abra uma janela de prompt de comando ou um shell do git bash e altere para uma pasta de trabalho no seu computador. Execute o seguinte comando para clonar o repositório GitHub do [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c) :

   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   Esta operação deve demorar vários minutos a ser concluída.

   As ferramentas de teste estão localizada em *azure-iot-sdk-c/tools/CACertificates* do repositório que clonou.

1. Siga os passos em [Gerir certificados de AC de teste para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

Além das ferramentas no SDK do C, o [exemplo de verificação de certificado de grupo](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) no sdk do *Microsoft Azure IOT para .net* mostra como fazer uma prova de posse no C# com um certificado de autoridade de certificação X. 509 intermediário ou raiz existente.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Obter a cadeia de ligação para o serviço de aprovisionamento

Para o exemplo neste início rápido, precisa da cadeia de ligação para o seu serviço de aprovisionamento.

1. Entre no portal do Azure, selecione **todos os recursos**e, em seguida, o serviço de provisionamento de dispositivos.

1. Selecione **políticas de acesso compartilhado**e escolha a política de acesso que você deseja usar para abrir suas propriedades. Em **política de acesso**, copie e salve a cadeia de conexão de chave primária.

    ![Obter a cadeia de ligação do serviço de aprovisionamento a partir do portal](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>Criar o exemplo do grupo de inscrição 

Esta seção mostra como criar um aplicativo de console do .NET Core que adiciona um grupo de registro ao serviço de provisionamento. Com algumas modificações, também pode seguir estes passos para criar uma aplicação de consola [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) para adicionar o grupo de inscrição. Para saber mais sobre como programar com o IoT Core, veja a [Documentação para programadores do Windows IoT Core](https://docs.microsoft.com/windows/iot-core/).

1. Abra o Visual Studio e selecione **criar um novo projeto**. Em **criar um novo projeto**, escolha o **aplicativo de console (.NET Core)** para o modelo de C# projeto e selecione **Avançar**.

1. Nomeie o projeto *createregistro*e, em seguida, selecione **criar**.

    ![Configurar projeto C# de área de trabalho clássica do Visual Windows](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto do **myregistro** e selecione **gerenciar pacotes NuGet**.

1. No **Gerenciador de pacotes NuGet**, selecione **procurar**, pesquise e escolha **Microsoft. Azure. Devices. Provisioning. Service**e, em seguida, selecione **instalar**.

    ![Janela Gestor de Pacote NuGet](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Esta etapa baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do cliente do serviço de provisionamento IOT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) e suas dependências.

1. Adicione as seguintes instruções `using` após as outras instruções `using` na parte superior de `Program.cs`:

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Adicione os campos a seguir à classe `Program` e faça as alterações listadas.  

   ```csharp
   private static string ProvisioningConnectionString = "{Your provisioning service connection string}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * Substitua o valor do espaço reservado `ProvisioningConnectionString` pela cadeia de conexão do serviço de provisionamento para o qual você deseja criar o registro.

   * Substitua o valor do espaço reservado `X509RootCertPath` pelo caminho para um arquivo. PEM ou. cer. Esse arquivo representa a parte pública de um certificado X. 509 da AC raiz ou intermediário que foi previamente carregado e verificado com o serviço de provisionamento.

   * Opcionalmente, você pode alterar o valor de `EnrollmentGroupId`. A cadeia só pode conter carateres minúsculos e hífenes.

   > [!IMPORTANT]
   > No código de produção, tenha em atenção as seguintes considerações de segurança:
   >
   > * A pré-programação da cadeia de ligação para o administrador do serviço de aprovisionamento vai contra as melhores práticas de segurança. Em vez disso, a cadeia de ligação deve ser mantida de forma segura, tal como num ficheiro de configuração seguro ou no registo.
   > * Carregue apenas a parte pública do certificado de assinatura. Nunca carregue ficheiros .pfx (PKCS12) ou .pem que contêm chaves privadas para o serviço de aprovisionamento.

1. Adicione o método a seguir à classe `Program`. Esse código cria uma entrada de grupo de registro e, em seguida, chama o método `CreateOrUpdateEnrollmentGroupAsync` em `ProvisioningServiceClient` para adicionar o grupo de registros ao serviço de provisionamento.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");
 
       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new enrollmentGroup config
           Console.WriteLine("\nCreating a new enrollmentGroup...");
           var certificate = new X509Certificate2(X509RootCertPath);
           Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
           EnrollmentGroup enrollmentGroup =
                   new EnrollmentGroup(
                           EnrollmentGroupId,
                           attestation)
                   {
                       ProvisioningStatus = ProvisioningStatus.Enabled
                   };
           Console.WriteLine(enrollmentGroup);
           #endregion
 
           #region Create the enrollmentGroup
           Console.WriteLine("\nAdding new enrollmentGroup...");
           EnrollmentGroup enrollmentGroupResult =
               await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
           Console.WriteLine("\nEnrollmentGroup created with success.");
           Console.WriteLine(enrollmentGroupResult);
           #endregion
 
       }
   }
   ```

1. Por fim, substitua o corpo do método `Main` pelas seguintes linhas:

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. Compile a solução.

## <a name="run-the-enrollment-group-sample"></a>Executar o exemplo do grupo de inscrição
  
Execute o exemplo no Visual Studio para criar o grupo de inscrição. Após a criação bem-sucedida, a janela de prompt de comando exibe as propriedades do novo grupo de registro.

Você pode verificar se o grupo de registro foi criado. Vá para o resumo do serviço de provisionamento de dispositivos e selecione **gerenciar registros**e, em seguida, selecione **grupos de registro**. Deverá ver uma nova entrada de inscrição que corresponde ao ID de registo utilizado no exemplo.

![Propriedades de inscrição no portal](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Selecione a entrada para verificar a impressão digital do certificado e outras propriedades para a entrada.

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja explorar o exemplo C# de serviço, não limpe os recursos criados neste guia de início rápido. Caso contrário, use as etapas a seguir para excluir todos os recursos criados por este guia de início rápido.

1. Feche a C# janela de saída de exemplo em seu computador.

1. Navegue até o serviço de provisionamento de dispositivos no portal do Azure, selecione **gerenciar registros**e, em seguida, selecione **grupos de registro**. Selecione a *ID de registro* para a entrada de registro que você criou usando este guia de início rápido e selecione **excluir**.

1. Em seu serviço de provisionamento de dispositivos no portal do Azure, selecione **certificados**, escolha o certificado que você carregou para este guia de início rápido e selecione **excluir** na parte superior dos **detalhes do certificado**.  

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um grupo de registro para um certificado de autoridade de certificação X. 509 intermediário ou raiz usando o serviço de provisionamento de dispositivos no Hub IoT do Azure. Para ficar a conhecer aprofundadamente o aprovisionamento de dispositivos, prossiga no tutorial para a configuração do Serviço Aprovisionamento de Dispositivos no portal do Azure.

> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service tutorials](./tutorial-set-up-cloud.md) (Tutoriais do Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure)
