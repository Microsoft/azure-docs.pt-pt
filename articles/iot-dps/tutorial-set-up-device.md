---
title: Tutorial - Dispositivo de configuração para o serviço de provisionamento de dispositivos Azure IoT Hub
description: Este tutorial mostra como pode configurar o dispositivo de fornecimento através do Serviço de Provisionamento de Dispositivos IoT Hub (DPS) durante o processo de fabrico do dispositivo
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: dd82b8ac3a510d1b16b0d2f42d3e50803162a119
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959803"
---
# <a name="tutorial-set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Tutorial: Crie um dispositivo para o fornecimento utilizando o Serviço de Provisionamento de Dispositivos Azure IoT Hub

No tutorial anterior, aprendeu a configurar o Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure para aprovisionar automaticamente os seus dispositivos no seu hub IoT. Este tutorial mostra-lhe como configurar o seu dispositivo durante o processo de fabrico, permitindo que o mesmo seja aprovisionado automaticamente no hub IoT. O dispositivo é aprovisionado com base no [Mecanismo de atestação](concepts-service.md#attestation-mechanism), após o primeiro arranque e ligação ao serviço de aprovisionamento. Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Compilar o SDK de Cliente dos Serviços Aprovisionamento de Dispositivos para plataformas específicas
> * Extrair os artefactos de segurança
> * Configurar o software de registo de dispositivos

Este tutorial espera que tenha criado a instância do Serviço de Aprovisionamento de Dispositivos e um hub IoT com as instruções no tutorial [Configurar recursos na cloud](tutorial-set-up-cloud.md) anterior.

Este tutorial utiliza o [repositório Azure IoT SDKs and libraries for C](https://github.com/Azure/azure-iot-sdk-c) (SDKs e bibliotecas do Azure IoT para C), que contém o SDK de Cliente do Serviço Aprovisionamento de Dispositivos para C. O SDK fornece atualmente suporte para TPM e X.509 para dispositivos em execução em implementações Windows ou Ubuntu. Este tutorial baseia-se no uso de um cliente de desenvolvimento do Windows, que também assume proficiência básica com o Visual Studio. 

Se não estiver familiarizado com o processo de provisão automática, reveja a visão geral do [provisionamento](about-iot-dps.md#provisioning-process) antes de continuar. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são para um ambiente de desenvolvimento do Windows. Para Linux ou macOS, consulte a secção apropriada no preparar o [seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) na documentação SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 com o ['desenvolvimento do ambiente de trabalho com C++'](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) habilitado. Visual Studio 2015 e Visual Studio 2017 também são suportados.

* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

## <a name="build-a-platform-specific-version-of-the-sdk"></a>Compilar uma versão do SDK específica para uma plataforma

O SDK de Cliente do Serviço Aprovisionamento de Dispositivos ajuda-o a implementar o software de registo de dispositivos. Contudo, antes de poder utilizá-lo, tem de compilar uma versão do SDK que seja específica da plataforma de cliente de desenvolvimento e do mecanismo de atestação. Neste tutorial, você constrói um SDK que usa o Visual Studio numa plataforma de desenvolvimento windows, para um tipo de atestado suportado:

1. Descarregue o [sistema de construção CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho "Desenvolvimento do ambiente de trabalho em C++") estejam instalados no computador, **antes** de iniciar a instalação de `CMake`. Depois de os pré-requisitos estarem assegurados e a transferência verificada, instale o sistema de compilação CMake.

2. Encontre o nome da etiqueta para a [última versão](https://github.com/Azure/azure-iot-sdk-c/releases/latest) do SDK.

3. Abra uma linha de comandos ou a shell do Git Bash. Executar os seguintes comandos para clonar a mais recente versão do repositório [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub. Utilize a etiqueta encontrada no passo anterior como o valor para o `-b` parâmetro:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operação deve demorar vários minutos a ser concluída.

4. Crie um subdiretório `cmake` no diretório de raiz do repositório git e navegue para essa pasta. Executar os seguintes comandos a partir do `azure-iot-sdk-c` diretório:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Crie o SDK para a sua plataforma de desenvolvimento com base nos mecanismos de atestado que irá utilizar. Utilize um dos seguintes comandos (tenha também em atenção os dois carateres de ponto final à direita de cada comando). Após a conclusão, o CMake compila o subdiretório `/cmake` com conteúdo específico do seu dispositivo:
 
    - Em dispositivos que utilizam o simulador de TPM para a atestação:

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - Para qualquer outro dispositivo (TPM/HSM/X.509 físico ou um certificado X.509 simulado):

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```


Está agora pronto para utilizar o SDK para compilar o código de registo do dispositivo. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>Extrair os artefactos de segurança 

O passo seguinte é extrair os artefactos de segurança do mecanismo de atestação que o seu dispositivo utiliza. 

### <a name="physical-devices"></a>Dispositivos físicos 

Consoante tenha criado o SDK para utilizar um atestado para um TPM/HSM físico ou esteja a utilizar certificados X.509, recolha os artefactos de segurança da seguinte forma:

- Num dispositivo TPM, tem de determinar a **Chave de Endossamento** associada ao mesmo junto do fabricante do chip do TPM. Pode derivar um **ID de Registo** exclusivo para o seu dispositivo TPM ao encriptar a chave de endossamento.  

- Para um dispositivo X.509, tem de obter os certificados emitidos para o(s) seu(s) dispositivo(s). O serviço de aprovisionamento expõe dois tipos de entrada de inscrição que controlam o acesso para dispositivos que utilizem o mecanismo de atestado X.509. Os certificados necessários dependem dos tipos de inscrição que irá utilizar.

    - Inscrições individuais: inscrição de um único dispositivo específico. Este tipo de entrada de inscrição requer [certificados "folha" de entidade final](concepts-x509-attestation.md#end-entity-leaf-certificate).
    
    - Grupos de inscrição: este tipo de entrada de inscrição requer certificados intermediários ou de raiz. Para obter mais informações, veja [Controlar o acesso a dispositivos para o serviço de aprovisionamento com certificados X.509](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

### <a name="simulated-devices"></a>Dispositivos simulados

Consoante tenha criado o SDK para utilizar um atestado para um dispositivo simulado que utilize TPM ou certificados X.509, recolha os artefactos de segurança da seguinte forma:

- Num dispositivo  TPM simulado:

   1. Abra uma Linha de Comandos do Windows, navegue para o subdiretório `azure-iot-sdk-c` e execute o simulador de TPM. O simulador escuta através de um socket nas portas 2321 e 2322. Não feche esta janela de comando; terá de manter este simulador em execução até ao fim do início rápido seguinte. 

      No subdiretório `azure-iot-sdk-c`, execute o seguinte comando para iniciar o simulador:

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

      > [!NOTE]
      > Se utilizar a linha de comandos do Git Bash para este passo, terá de alterar as barras invertidas para barras, por exemplo: `./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe`.

   1. Com o Visual Studio, abra a solução gerada na pasta *cmake* denominada `azure_iot_sdks.sln` e compile-a com o comando “Build solution” no menu “Build” (“Compilar”).

   1. No painel *Explorador de Soluções* do Visual Studio, navegue para a pasta **Aprovisionar\_Ferramentas**. Clique com o botão direito do rato no projeto **tpm_device_provision** e selecione **Configurar como Projeto de Arranque**. 

   1. Execute a solução com um dos comandos "Start" no menu "Debug" (“Depuração”). A janela de saída apresenta o **_ID de Registo_** e a **_Chave de Endossamento_** do simulador de TPM, que são necessários para a inscrição do dispositivo. Copie estes valores para utilização posterior. Pode fechar esta janela (com o ID de Registo e a Chave de Endossamento), mas deixe a janela do simulador de TPM que iniciou no passo 1 em execução.

- Num dispositivo X.509 simulado:

  1. Com o Visual Studio, abra a solução gerada na pasta *cmake* denominada `azure_iot_sdks.sln` e compile-a com o comando “Build solution” no menu “Build” (“Compilar”).

  1. No painel *Explorador de Soluções* do Visual Studio, navegue para a pasta **Aprovisionar\_Ferramentas**. Clique com o botão direito do rato no projeto **dice\_device\_enrollment** e selecione **Set as Startup Project** (Definir como Projeto de Arranque”). 
  
  1. Execute a solução com um dos comandos "Start" no menu "Debug" (“Depuração”). Na janela de saída, introduza **i** para inscrição individual, quando lhe for pedido. A janela de saída apresenta um certificado X.509 gerado localmente para o seu dispositivo simulado. Copie a saída para a área de transferência, começando em *-----BEGIN CERTIFICATE-----* e terminando no primeiro *-----END CERTIFICATE-----*, garantindo que também inclui estas duas linhas. Apenas necessita do primeiro certificado na janela de saída.
 
  1. Crie um ficheiro com o nome **_X509testcert.pem_**, abra-o num editor de texto à sua escolha e copie os conteúdos da área de transferência para o mesmo. Guarde o ficheiro, pois vai utilizá-lo mais tarde para a inscrição de dispositivos. Quando o software de registo for executado, utiliza o mesmo certificado durante o aprovisionamento automático.    

Estes artefactos de segurança são necessários durante a inscrição do seu dispositivo no Serviço Aprovisionamento de Dispositivos. O Serviço Aprovisionamento aguarda que o dispositivo seja arrancado e se ligue ao mesmo num momento posterior. Da primeira vez que o dispositivo for arrancado, a lógica do SDK de Cliente interage com o seu chip (ou simulador) para extrair os artefactos de segurança do dispositivo e verifica o registo no Serviço Aprovisionamento de Dispositivos. 

## <a name="create-the-device-registration-software"></a>Configurar o software de registo de dispositivos

O último passo consiste em escrever uma aplicação de registo que utiliza o SDK de Cliente do Serviço Aprovisionamento de Dispositivos para registar o dispositivo no serviço Hub IoT. 

> [!NOTE]
> Neste passo, vamos pressupor que está a ser utilizado um dispositivo simulado, o que é conseguido ao executar uma aplicação de registo de exemplo de SDK a partir da sua estação de trabalho. No entanto, estes mesmos conceitos aplicam-se se estiver a compilar uma aplicação de registo para implementação num dispositivo físico. 

1. No portal do Azure, selecione o painel **Descrição Geral** do seu Serviço Aprovisionamento de Dispositivos e copie o valor **_Âmbito do ID_**. O *ID do Âmbito* é gerado pelo serviço e garante que é exclusivo. É imutável e utilizado para identificar exclusivamente o IDs de registo.

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do painel do portal](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

1. No *Explorador de Soluções* do Visual Studio da sua máquina, navegue para a pasta **Aprovisionar\_Exemplos**. Selecione o projeto de exemplo com o nome **prov\_dev\_client\_sample** e abra o ficheiro de origem **prov\_dev\_client\_sample.c**.

1. Atribua o valor _Âmbito de ID_ obtido no passo 1 à variável `id_scope` (ao remover os parênteses de início/`[` e de fim/`]`): 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    Para referência, a variável `global_prov_uri`, que permite que a API de registo de cliente do Hub IoT `IoTHubClient_LL_CreateFromDeviceAuth` se ligue à instância do Serviço Aprovisionamento de Dispositivos indicado.

1. Na função **main()**, no mesmo ficheiro, comente/anule o comentário da variável `hsm_type` que corresponde ao mecanismo de atestação que o software de registo de dispositivos está a utilizar (TPM ou X.509): 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

1. Guarde as alterações e volte a compilar o exemplo **prov\_dev\_client\_sample** ao selecionar “Build solution” no menu “Build”. 

1. Clique com o botão direito do rato no projeto **prov\_dev\_client\_sample**, na pasta **Provision\_Samples** e selecione **Set as Startup Project** (Definir como Projeto de Arranque). NÃO execute ainda a aplicação de exemplo.

> [!IMPORTANT]
> Não execute/inicie ainda o dispositivo! Antes de iniciar o dispositivo, tem de concluir o processo ao inscrevê-lo no Serviço Aprovisionamento de Dispositivos. A secção “Próximos passos” abaixo orienta-o para o artigo seguinte.

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>APIs de SDK utilizadas durante o registo (apenas para referência)

Para referência, o SDK disponibiliza as seguintes APIs para a sua aplicação utilizar durante o registo. Estas APIs ajudam o dispositivo a ligar-se e a registar-se no Serviço Aprovisionamento de Dispositivos quando é arrancado. Por sua vez, o dispositivo recebe as informações necessárias para estabelecer ligação à sua instância do Hub IoT:

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

Poderá também concluir que tem de refinar a aplicação de registo do cliente de Serviço Aprovisionamento de Dispositivos, ao utilizar um dispositivo simulado primeiro e uma configuração de serviço de teste. Assim que a aplicação estiver a funcionar no ambiente de teste, pode criá-la para o seu dispositivo específico e copiar o ficheiro executável para a imagem do dispositivo. 

## <a name="clean-up-resources"></a>Limpar os recursos

Neste momento, os serviços Aprovisionamento de Dispositivos e Hub IoT poderão estar em execução no portal. Se quiser abandonar a configuração de aprovisionamento de dispositivos e/ou retardar a conclusão desta série de tutoriais, recomendamos encerrá-los para evitar incorrer em custos desnecessários.

1. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu Serviço Aprovisionamento de Dispositivos. Na parte superior do painel **Todos os recursos**, clique em **Eliminar**.  
1. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu hub IoT. Na parte superior do painel **Todos os recursos**, clique em **Eliminar**.  

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Compilar o SDK de Cliente do Serviço de Aprovisionamento de Dispositivos para plataformas específicas
> * Extrair os artefactos de segurança
> * Configurar o software de registo de dispositivos

Avance para o próximo tutorial para saber como aprovisionar o dispositivo no seu hub IoT ao inscrevê-lo no Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure para aprovisionamento automático.

> [!div class="nextstepaction"]
> [Provision the device to your IoT hub](tutorial-provision-device-to-hub.md) (Aprovisionar o dispositivo no seu hub IoT)