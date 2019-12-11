---
title: Provisionar o dispositivo X. 509 simulado para o Hub IoT do Azure usandoC#
description: Início rápido – criar e provisionar um dispositivo X. 509 C# simulado usando o SDK do dispositivo para o serviço de provisionamento de dispositivos do Hub IOT do Azure (DPS). Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 639ab07113a0e62cac43af5b79f052da1efd93ab
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976524"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Início rápido: criar e provisionar um dispositivo X. 509 C# simulado usando o SDK do dispositivo para o serviço de provisionamento de dispositivos no Hub IOT

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Estas etapas mostram como usar os exemplos de [IOT do Azure para C# ](https://github.com/Azure-Samples/azure-iot-samples-csharp) para simular um dispositivo X. 509 em um computador de desenvolvimento que executa o sistema operacional Windows. O exemplo também conecta o dispositivo simulado a um hub IoT usando o serviço de provisionamento de dispositivos.

Se não estiver familiarizado com o processo de aprovisionamento automático, reveja também [Conceitos de aprovisionamento automático](concepts-auto-provisioning.md). Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar. 

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:
- [Grupos de inscrição](concepts-service.md#enrollment-group): utilizados para inscrever vários dispositivos relacionados.
- [Inscrições Individuais](concepts-service.md#individual-enrollment): utilizadas para inscrever um dispositivo individual.

Este artigo irá demonstrar as inscrições individuais.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento 

1. Verifique se você tem o [SDK do .NET Core 2,1 ou posterior](https://www.microsoft.com/net/download/windows) instalado em seu computador. 

1. Verifique se `git` está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Veja as [ferramentas de cliente Git da Software Freedom Conservancy](https://git-scm.com/download/) relativamente à mais recente versão das ferramentas de `git` a instalar, que incluem o **Git Bash**, a aplicação de linha de comandos que pode utilizar para interagir com o seu repositório Git local. 

1. Abra uma linha de comandos ou o Git Bash. Clone os exemplos de IoT do C# Azure para o repositório github:
    
    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Criar um certificado de dispositivo X.509 autoassinado e entrada de inscrição individual

Nesta secção, vai utilizar um certificado X.509 autoassinado e é importante ter em consideração o seguinte:

* Os certificados autoassinados são apenas para teste e não devem ser utilizados na produção.
* A data de expiração predefinida para um certificado autoassinado é de um ano.

Você usará o código de exemplo do [cliente de provisionamento amostra-X. 509 atestado](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) para criar o certificado a ser usado com a entrada de registro individual para o dispositivo simulado.


1. Numa linha de comandos, altere os diretórios para o diretório de projeto para o exemplo de aprovisionamento de dispositivo X.509.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. O código de exemplo é configurado para utilizar certificados X.509 armazenados dentro de um ficheiro formatado PKCS12 protegido por palavra-passe (certificate.pfx). Além disso, você precisa de um arquivo de certificado de chave pública (Certificate. cer) para criar um registro individual posteriormente neste guia de início rápido. Para gerar um certificado autoassinado e respetivos ficheiros .cer e .pfx associados, execute o seguinte comando:

    ```cmd
    powershell .\GenerateTestCertificate.ps1
    ```

3. O script pede-lhe uma palavra-passe PFX. Memorize esta palavra-passe, deve utilizá-la ao executar o exemplo.

    ![ Introduza a palavra-passe PFX](./media/quick-create-simulated-device-x509-csharp/generate-certificate.png)  


4. Entre no portal do Azure, selecione o botão **todos os recursos** no menu esquerdo e abra o serviço de provisionamento.

5. No menu serviço de provisionamento de dispositivos, selecione **gerenciar registros**. Selecione a guia registros **individuais** e selecione o botão **adicionar registro individual** na parte superior. 

6. No painel **adicionar registro** , insira as seguintes informações:
   - Selecione **X.509** como o *Mecanismo* de atestado de identidades.
   - No *arquivo. PEM ou. cer do certificado primário*, escolha *selecionar um arquivo* para selecionar o arquivo de certificado **Certificate. cer** criado nas etapas anteriores.
   - Deixe **ID de Dispositivo** em branco. O dispositivo será aprovisionado com o seu ID de dispositivo definido como o nome comum (CN) no certificado X.509, **iothubx509device1**. Este também será o nome utilizado para o ID de registo da entrada de inscrição individual. 
   - Opcionalmente, pode fornecer as seguintes informações:
       - Selecione um hub IoT ligado ao seu serviço de aprovisionamento.
       - Atualize o **estado inicial do dispositivo duplo** com a configuração inicial pretendida para o dispositivo.
   - Depois de concluído, pressione o botão **salvar** . 

     [![Adicionar inscrição individual para fins de atestado X.509 no portal](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png#lightbox)
    
   Após a instalação bem-sucedida, a sua entrada de inscrição X.509 aparece como **iothubx509device1** na coluna *ID de Registo* do separador *Inscrições Individuais*. 

## <a name="provision-the-simulated-device"></a>Aprovisionar o dispositivo simulado

1. Na folha de **visão geral** do serviço de provisionamento, observe o valor de **_escopo da ID_** .

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do painel do portal](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. Escreva o seguinte comando para criar e executar o exemplo de aprovisionamento de dispositivos X.509. Substitua o valor `<IDScope>` pelo Âmbito de ID para o serviço de aprovisionamento. 

    ```cmd
    dotnet run <IDScope>
    ```

3. Quando lhe for pedido, introduza a palavra-passe para o ficheiro PFX que criou anteriormente. Repare nas mensagens que simulam o arranque e a ligação do dispositivo ao Serviço Aprovisionamento de Dispositivos para obter as informações do seu hub IoT. 

    ![Saída de dispositivo de exemplo](./media/quick-create-simulated-device-x509-csharp/sample-output.png) 

4. Certifique-se de que o dispositivo foi aprovisionado. No provisionamento bem-sucedido do dispositivo simulado para o Hub IoT vinculado ao seu serviço de provisionamento, a ID do dispositivo aparece na folha **dispositivos IOT** do Hub. 

    ![O dispositivo é registado no hub IoT](./media/quick-create-simulated-device-x509-csharp/registration.png) 

    Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para obter mais informações, veja [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT)


## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar trabalhando e explorando o exemplo de cliente do dispositivo, não limpe os recursos criados neste guia de início rápido. Se você não planeja continuar, use as etapas a seguir para excluir todos os recursos criados por este guia de início rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
1. Feche a janela do simulador TPM no seu computador.
1. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o serviço de provisionamento de dispositivos. Na parte superior da folha de **visão geral** , pressione **delete** na parte superior do painel.  
1. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o Hub IOT. Na parte superior da folha de **visão geral** , pressione **delete** na parte superior do painel.  

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um dispositivo X. 509 simulado em seu computador Windows e o provisionou no Hub IoT usando o serviço de provisionamento de dispositivos no Hub IoT do Azure no Portal. Para saber como registrar seu dispositivo X. 509 de forma programática, continue no início rápido para o registro programático de dispositivos X. 509. 

> [!div class="nextstepaction"]
> [Início rápido do Azure-registrar dispositivos X. 509 no serviço de provisionamento de dispositivos no Hub IoT do Azure](quick-enroll-device-x509-csharp.md)
