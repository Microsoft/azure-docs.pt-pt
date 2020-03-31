---
title: 'Provisão simulado dispositivo X.509 para Hub Azure IoT usando C #'
description: Quickstart - Crie e forre um dispositivo SDK simulado utilizando o dispositivo C# SDK para o Serviço de Provisionamento de Dispositivos Hub Azure IoT (DPS). Este início rápido utiliza inscrições individuais.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 639ab07113a0e62cac43af5b79f052da1efd93ab
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "74976524"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Quickstart: Criar e fornecer um dispositivo S.509 simulado utilizando o dispositivo C# SDK para o Serviço de Provisionamento de Dispositivos IoT Hub

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Estes passos mostram-lhe como usar as [amostras Azure IoT para C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) para simular um dispositivo X.509 numa máquina de desenvolvimento que executa o Sistema operativo Windows. A amostra também liga o dispositivo simulado a um Hub IoT utilizando o Serviço de Provisionamento de Dispositivos.

Se não está familiarizado com o processo de fornecimento automático, certifique-se de que também revê [conceitos de fornecimento automático.](concepts-auto-provisioning.md) Certifique-se também de que executa os passos descritos em [Configurar o Serviço de Aprovisionamento de Dispositivos no Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar. 

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:
- [Grupos de inscrição](concepts-service.md#enrollment-group): utilizados para inscrever vários dispositivos relacionados.
- [Inscrições Individuais](concepts-service.md#individual-enrollment): utilizadas para inscrever um dispositivo individual.

Este artigo vai demonstrar as inscrições individuais.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento 

1. Certifique-se de que tem o [.NET Core 2.1 SDK ou posteriormente](https://www.microsoft.com/net/download/windows) instalado na sua máquina. 

1. Verifique se `git` está instalado no computador e que é adicionado às variáveis de ambiente às quais a janela de comandos pode aceder. Veja as [ferramentas de cliente Git da Software Freedom Conservancy](https://git-scm.com/download/) relativamente à mais recente versão das ferramentas de `git` a instalar, que incluem o **Git Bash**, a aplicação de linha de comandos que pode utilizar para interagir com o seu repositório Git local. 

1. Abra uma linha de comandos ou o Git Bash. Clone das amostras Azure IoT para C# GitHub repo:
    
    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Criar um certificado de dispositivo X.509 autoassinado e entrada de inscrição individual

Nesta secção, vai utilizar um certificado X.509 autoassinado e é importante ter em consideração o seguinte:

* Os certificados autoassinados são apenas para teste e não devem ser utilizados na produção.
* A data de expiração predefinida para um certificado autoassinado é de um ano.

Utilizará o código de amostra da amostra do cliente do dispositivo de [provisionamento - X.509 Attestation](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) para criar o certificado a utilizar com a inscrição individual do dispositivo simulado.


1. Numa linha de comandos, altere os diretórios para o diretório de projeto para o exemplo de aprovisionamento de dispositivo X.509.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. O código de exemplo é configurado para utilizar certificados X.509 armazenados dentro de um ficheiro formatado PKCS12 protegido por palavra-passe (certificate.pfx). Além disso, precisa de um ficheiro de certificado de chave pública (certificate.cer) para criar uma inscrição individual mais tarde neste arranque rápido. Para gerar um certificado autoassinado e respetivos ficheiros .cer e .pfx associados, execute o seguinte comando:

    ```cmd
    powershell .\GenerateTestCertificate.ps1
    ```

3. O script pede-lhe uma palavra-passe PFX. Memorize esta palavra-passe, deve utilizá-la ao executar o exemplo.

    ![ Introduza a palavra-passe PFX](./media/quick-create-simulated-device-x509-csharp/generate-certificate.png)  


4. Inscreva-se no portal Azure, selecione o botão **Todos os recursos** no menu à esquerda e abra o seu serviço de provisionamento.

5. A partir do menu do Serviço de Fornecimento de Dispositivos, selecione **Gerir as matrículas.** Selecione o separador **Individual Registrations** e selecione o botão **de inscrição individual Adicionar** na parte superior. 

6. No painel **Adicionar Inscrições,** introduza as seguintes informações:
   - Selecione **X.509** como o *Mecanismo* de atestado de identidades.
   - Sob o *certificado primário .pem ou .cer file,* escolha *Selecione um ficheiro* para selecionar o certificado de ficheiro de **certificado.cer** criado nas etapas anteriores.
   - Deixe **ID de Dispositivo** em branco. O dispositivo será aprovisionado com o seu ID de dispositivo definido como o nome comum (CN) no certificado X.509, **iothubx509device1**. Este também será o nome utilizado para o ID de registo da entrada de inscrição individual. 
   - Opcionalmente, pode fornecer as seguintes informações:
       - Selecione um hub IoT ligado ao seu serviço de aprovisionamento.
       - Atualize o **estado inicial do dispositivo duplo** com a configuração inicial pretendida para o dispositivo.
   - Uma vez concluído, prima o botão **Guardar.** 

     [![Adicione inscrições individuais para a atesta X.509 no portal](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png#lightbox)
    
   Após a instalação bem-sucedida, a sua entrada de inscrição X.509 aparece como **iothubx509device1** na coluna *ID de Registo* do separador *Inscrições Individuais*. 

## <a name="provision-the-simulated-device"></a>Aprovisionar o dispositivo simulado

1. A partir da lâmina **de visão geral** para o seu serviço de provisionamento, note o valor do Âmbito de **_Identificação._**

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do painel do portal](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. Escreva o seguinte comando para criar e executar o exemplo de aprovisionamento de dispositivos X.509. Substitua o valor `<IDScope>` pelo Âmbito de ID para o serviço de aprovisionamento. 

    ```cmd
    dotnet run <IDScope>
    ```

3. Quando lhe for pedido, introduza a palavra-passe para o ficheiro PFX que criou anteriormente. Repare nas mensagens que simulam o arranque e a ligação do dispositivo ao Serviço Aprovisionamento de Dispositivos para obter as informações do seu hub IoT. 

    ![Saída de dispositivo de exemplo](./media/quick-create-simulated-device-x509-csharp/sample-output.png) 

4. Certifique-se de que o dispositivo foi aprovisionado. Ao fornecer com sucesso o dispositivo simulado ao hub IoT ligado ao seu serviço de provisionamento, o ID do dispositivo aparece na lâmina de **dispositivos IoT** do hub. 

    ![O dispositivo é registado no hub IoT](./media/quick-create-simulated-device-x509-csharp/registration.png) 

    Se tiver alterado o *estado inicial do dispositivo duplo* face ao valor predefinido na entrada de inscrição do seu dispositivo, este pode extrair o estado pretendido do dispositivo duplo a partir do hub e agir em conformidade. Para obter mais informações, veja [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Compreender e utilizar dispositivos duplos no Hub IoT)


## <a name="clean-up-resources"></a>Limpar recursos

Se pretende continuar a trabalhar e a explorar a amostra do cliente do dispositivo, não limpe os recursos criados neste arranque rápido. Se não pretende continuar, utilize os seguintes passos para eliminar todos os recursos criados por este arranque rápido.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
1. Feche a janela do simulador TPM no seu computador.
1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu serviço de fornecimento de dispositivos. Na parte superior da lâmina **de visão geral,** prima **Delete** na parte superior do painel.  
1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu hub IoT. Na parte superior da lâmina **de visão geral,** prima **Delete** na parte superior do painel.  

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um dispositivo X.509 simulado na sua máquina Windows e aprovisionou-o no seu hub IoT utilizando o Serviço de Provisionamento de Dispositivos Hub Azure IoT no portal. Para aprender a inscrever o seu dispositivo X.509 programáticamente, continue a acelerar para a inscrição programática de dispositivos X.509. 

> [!div class="nextstepaction"]
> [Azure quickstart - Inscreva dispositivos X.509 para o Serviço de Provisionamento de Dispositivos Hub Azure IoT](quick-enroll-device-x509-csharp.md)
