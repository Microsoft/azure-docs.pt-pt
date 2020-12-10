---
title: Tutorial - Fornecimento de dispositivos X.509 para Azure IoT Hub usando um módulo de segurança de hardware personalizado (HSM)
description: Este tutorial usa grupos de inscrição. Neste tutorial, você aprende a providenciar dispositivos X.509 usando um Módulo de Segurança de Hardware personalizado (HSM) e o dispositivo C SDK para O Serviço de Provisionamento de Dispositivos IoT IoT (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 11/18/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 25d084b8af148707685b2cbb4368394a12d99db2
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97005312"
---
# <a name="tutorial-provision-multiple-x509-devices-using-enrollment-groups"></a>Tutorial: Fornecimento de vários dispositivos X.509 utilizando grupos de inscrição

Neste tutorial, você aprenderá a providenciar grupos de dispositivos IoT que usam certificados X.509 para autenticação. O código de amostra do [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) será utilizado para a provisionar a sua máquina de desenvolvimento como dispositivo IoT. 

O Serviço Aprovisionamento de Dispositivos no IoT do Azure suporta dois tipos de inscrição:

* [Grupos de inscrição](concepts-service.md#enrollment-group): utilizados para inscrever vários dispositivos relacionados.
* [Inscrições Individuais](concepts-service.md#individual-enrollment): utilizadas para inscrever um dispositivo individual.

Este tutorial é semelhante aos tutoriais anteriores que demonstram como usar grupos de inscrição para conjuntos de dispositivos. No entanto, os certificados X.509 serão usados neste tutorial em vez de chaves simétricas. Reveja os tutoriais anteriores nesta secção para obter uma abordagem simples utilizando [teclas simétricas](./concepts-symmetric-key-attestation.md).

Este tutorial demonstrará a amostra personalizada do [HSM](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client/samples/custom_hsm_example) que fornece uma implementação de canhoto para interagir com armazenamento seguro baseado em hardware. Um [Módulo de Segurança de Hardware (HSM)](./concepts-service.md#hardware-security-module) é utilizado para armazenamento seguro e baseado em hardware de segredos de dispositivos. Um HSM pode ser usado com chave simétrica, certificado X.509 ou atestado de TPM para fornecer armazenamento seguro para segredos. O armazenamento baseado em hardware dos segredos do dispositivo não é necessário, mas é fortemente recomendado para ajudar a proteger informações sensíveis como a chave privada do certificado do seu dispositivo.

Se não estiver familiarizado com o processo de autoprovisionamento, reveja a visão geral [do provisionamento.](about-iot-dps.md#provisioning-process) Além disso, certifique-se de ter completado os passos no [Serviço de Provisionamento de Dispositivos IoT Hub com o portal Azure](quick-setup-auto-provision.md) antes de continuar com este tutorial. 


Neste tutorial completará os seguintes objetivos:

> [!div class="checklist"]
> * Crie uma cadeia de certificados de confiança para organizar um conjunto de dispositivos usando certificados X.509.
> * Prova completa de posse com certificado de assinatura utilizado com a cadeia de certificados.
> * Criar uma nova inscrição em grupo que utilize a cadeia de certificados
> * Criar o ambiente de desenvolvimento para o provisionamento de um dispositivo utilizando código a partir do [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)
> * Forneça um dispositivo que utilize a cadeia de certificados com a amostra personalizada do Módulo de Segurança de Hardware (HSM) no SDK.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são para um ambiente de desenvolvimento do Windows. Para Linux ou macOS, consulte a secção apropriada no preparar o [seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) na documentação SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 com o ['desenvolvimento do ambiente de trabalho com C++'](https://docs.microsoft.com/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) habilitado. Visual Studio 2015 e Visual Studio 2017 também são suportados.

* Versão mais recente do [Git](https://git-scm.com/download/) instalada.

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Preparar o ambiente de desenvolvimento Azure IoT C SDK

Nesta secção, irá preparar um ambiente de programação utilizado para criar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c). O SDK inclui código de amostra e ferramentas usadas por dispositivos X.509 que se prestam com DPS.

1. Descarregue o [sistema de construção CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio ([Visual Studio](https://visualstudio.microsoft.com/vs/) e o desenvolvimento 'Desktop com carga de trabalho [C++'](https://docs.microsoft.com/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) sejam instalados na sua máquina, **antes** de iniciar a `CMake` instalação. Depois de os pré-requisitos estarem assegurados e a transferência verificada, instale o sistema de compilação CMake.

2. Encontre o nome da etiqueta para a [última versão](https://github.com/Azure/azure-iot-sdk-c/releases/latest) do Azure IoT C SDK.

3. Abra uma linha de comandos ou a shell do Git Bash. Executar os seguintes comandos para clonar a mais recente versão do repositório [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub. Utilize a etiqueta encontrada no passo anterior como o valor para o `-b` parâmetro:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operação deve demorar vários minutos a ser concluída.

4. Crie um subdiretório `cmake` no diretório de raiz do repositório git e navegue para essa pasta. 

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. O `cmake` diretório que criou conterá a amostra personalizada de HSM e o código de provisionamento do dispositivo de amostra que utiliza o HSM personalizado para fornecer autenticação X.509. 

    Execute o seguinte comando no seu `cmake` diretório para construir uma versão do SDK específica para a sua plataforma de desenvolvimento. A construção incluirá uma referência à amostra personalizada de HSM. 

    Ao especificar o caminho utilizado `-Dhsm_custom_lib` abaixo, certifique-se de utilizar o caminho em relação ao `cmake` diretório que criou anteriormente. O caminho relativo mostrado abaixo é apenas um exemplo.

    ```cmd
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    ```

    Se `cmake` não encontrar o compilador de C++, poderá obter erros de compilação ao executar o comando acima. Se isto acontecer, tente executar o comando seguinte na [linha de comandos do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

    Assim que a construção tiver sucesso, uma solução Visual Studio será gerada no seu `cmake` diretório. As últimas linhas de saída são semelhantes à seguinte saída:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: D:/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-x509-certificate-chain"></a>Criar uma cadeia de certificados X.509

Nesta secção, você, irá gerar uma cadeia de certificados X.509 de três certificados para testes com este tutorial. Os certificados terão a seguinte hierarquia.

![Cadeia de certificados de dispositivo tutorial](./media/tutorial-custom-hsm-enrollment-group-x509/example-device-cert-chain.png#lightbox)

[Certificado de raiz](concepts-x509-attestation.md#root-certificate): Irá completar [a prova de posse](how-to-verify-certificates.md) para verificar o certificado de raiz. Esta verificação permitirá à DPS confiar nesse certificado e verificar os certificados por si assinados.

[Certificado Intermédio](concepts-x509-attestation.md#intermediate-certificate): É comum que os certificados intermédios sejam utilizados para agrupar dispositivos logicamente por linhas de produtos, divisões da empresa ou outros critérios. Este tutorial utilizará uma cadeia de certificados composta por um certificado intermédio. O certificado intermédio será assinado pelo certificado raiz. Este certificado também será utilizado no grupo de inscrição criado em DPS para agrupar logicamente um conjunto de dispositivos. Esta configuração permite gerir um conjunto inteiro de dispositivos que têm certificados de dispositivo assinados pelo mesmo certificado intermédio. Pode criar grupos de inscrição para permitir ou desativar um grupo de dispositivos. Para obter mais informações sobre a desativação de um grupo de dispositivos, consulte [Não permitir um certificado de CA intermédio ou raiz X.509 utilizando um grupo de inscrição](how-to-revoke-device-access-portal.md#disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group)

[Certificado do dispositivo](concepts-x509-attestation.md#end-entity-leaf-certificate): O certificado do dispositivo (folha) será assinado pelo certificado intermédio e armazenado no dispositivo juntamente com a sua chave privada. O dispositivo apresentará este certificado e chave privada, juntamente com a cadeia de certificados ao tentar provisionar. 

Para criar a cadeia de certificados:

1. Abra um pedido de comando de Git Bash. Passos completos 1 e 2 utilizando as instruções de concha bash que estão localizadas na [Gestão de certificados ca de teste para amostras e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md#managing-test-ca-certificates-for-samples-and-tutorials).

    Este passo cria um diretório de trabalho para os scripts de certificado, e gera o certificado de raiz e intermédio exemplo para a cadeia de certificados usando openssl. 

    Aviso na saída que mostra a localização do certificado de raiz auto-assinado. Este certificado passará por [comprovativo de posse](how-to-verify-certificates.md) para verificar a propriedade mais tarde.

    ```output
    Creating the Root CA Certificate
    CA Root Certificate Generated At:
    ---------------------------------
        ./certs/azure-iot-test-only.root.ca.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                fc:cc:6b:ab:3b:9a:3e:fe
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:30 2020 GMT
                Not After : Nov 22 21:30:30 2020 GMT
            Subject: CN=Azure IoT Hub CA Cert Test Only
    ```        

    Aviso na saída que mostra a localização do certificado intermédio assinado/emitido pelo certificado raiz. Este certificado será usado com o grupo de inscrição que irá criar mais tarde.

    ```output
    Intermediate CA Certificate Generated At:
    -----------------------------------------
        ./certs/azure-iot-test-only.intermediate.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 1 (0x1)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:33 2020 GMT
                Not After : Nov 22 21:30:33 2020 GMT
            Subject: CN=Azure IoT Hub Intermediate Cert Test Only
    ```    

2. Em seguida, executar o seguinte comando para criar um novo certificado de dispositivo/folha com um nome de sujeito que dê como parâmetro. Utilize o nome de exemplo dado para este tutorial, `custom-hsm-device-01` . Este nome de assunto será o ID do dispositivo para o seu dispositivo IoT. 

    > [!WARNING]
    > Não use um nome com espaços. Este nome de sujeito é o ID do dispositivo que está a ser abastado. Deve seguir as regras para uma identificação do dispositivo. Para mais informações, consulte [as propriedades de identidade do Dispositivo.](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)

    ```cmd
    ./certGen.sh create_device_certificate_from_intermediate "custom-hsm-device-01"
    ```

    Note a seguinte saída que mostra onde está o novo certificado do dispositivo. O certificado do dispositivo é assinado (emitido) pelo certificado intermédio.

    ```output
    -----------------------------------
    ./certs/new-device.cert.pem: OK
    Leaf Device Certificate Generated At:
    ----------------------------------------
        ./certs/new-device.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 9 (0x9)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub Intermediate Cert Test Only
            Validity
                Not Before: Nov 10 09:20:33 2020 GMT
                Not After : Dec 10 09:20:33 2020 GMT
            Subject: CN=custom-hsm-device-01
    ```    
    
3. Executar o seguinte comando para criar um ficheiro de cadeia de certificados completo .pem que inclui o novo certificado do dispositivo.

    ```Bash
    cd ./certs && cat new-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-device-full-chain.cert.pem && cd ..
    ```

    Utilize um editor de texto e abra o ficheiro da cadeia *de certificados,./certs/new-device-full-chain.cert.pem*. O texto da cadeia de certificados contém a cadeia completa dos três certificados. Utilizará este texto como cadeia de certificados com o código HSM personalizado mais tarde neste tutorial.

    O texto em cadeia completo tem o seguinte formato:
 
    ```output 
    -----BEGIN CERTIFICATE-----
        <Text for the device certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the intermediate certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the root certificate includes public key>
    -----END CERTIFICATE-----
    ```

5. Note que a chave privada do novo certificado do dispositivo está escrita para *./private/new-device.key.pem*. O texto desta chave será necessário pelo dispositivo durante o provisionamento. O texto será adicionado ao exemplo hsm personalizado mais tarde.

    > [!WARNING]
    > O texto dos certificados contém apenas informações-chave públicas. 
    >
    > No entanto, o dispositivo também deve ter acesso à chave privada do certificado do dispositivo. Isto é necessário porque o dispositivo deve efetuar a verificação utilizando essa chave no tempo de funcionamento quando se tenta provisões. A sensibilidade desta chave é uma das principais razões pelas quais é recomendado utilizar o armazenamento baseado em hardware num HSM real para ajudar a proteger as chaves privadas.



## <a name="configure-the-custom-hsm-stub-code"></a>Configure o código de canhoto personalizado HSM

As especificidades de interagir com o armazenamento baseado em hardware seguro variam dependendo do hardware. Como resultado, a cadeia de certificados utilizada pelo dispositivo neste tutorial será codificada no código de canhoto HSM personalizado. Num cenário real, a cadeia de certificados seria armazenada no hardware HSM real para fornecer uma melhor segurança para informações sensíveis. Métodos semelhantes aos métodos de encaixe mostrados nesta amostra seriam então implementados para ler os segredos desse armazenamento baseado em hardware. 

Embora o hardware HSM não seja necessário, não é aconselhável ter informações sensíveis, como a chave privada do certificado, verificadas no código fonte. Isto expõe a chave a qualquer um que possa ver o código. Isto só é feito neste artigo para ajudar na aprendizagem.

Para atualizar o código de canhoto HSM personalizado para este tutorial:

1. Lance o Visual Studio e abra o novo ficheiro de solução que foi criado no `cmake` diretório que criou na raiz do repositório azure-iot-sdk-c git. O ficheiro da solução está `azure_iot_sdks.sln` nomeado.

2. No Solution Explorer for Visual Studio, navegue para **Provisioning_Samples > custom_hsm_example > Ficheiros Source** e abra *custom_hsm_example.c*.

3. Atualize o valor de cadeia da `COMMON_NAME` constante de cadeia utilizando o nome comum utilizado ao gerar o certificado do dispositivo.

    ```c
    static const char* const COMMON_NAME = "custom-hsm-device-01";
    ```

4. No mesmo ficheiro, é necessário atualizar o valor de cadeia de cadeia constante utilizando o texto da corrente de `CERTIFICATE` certificado que guardou em *./certs/new-device-full-chain.cert.pem* depois de gerar os seus certificados.

    A sintaxe do texto do certificado deve seguir o padrão abaixo sem espaços extra ou análise feita pelo Visual Studio.

    ```c
    // <Device/leaf cert>
    // <intermediates>
    // <root>
    static const char* const CERTIFICATE = "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFPDCCAySgAwIBAgIBATANBgkqhkiG9w0BAQsFADAqMSgwJgYDVQQDDB9BenVy\n"
        ...
    "MTEyMjIxMzAzM1owNDEyMDAGA1UEAwwpQXp1cmUgSW9UIEh1YiBJbnRlcm1lZGlh\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----";        
    ```

    Atualizar corretamente este valor de cadeia neste passo pode ser muito enfadonho e sujeito a erro. Para gerar a sintaxe adequada no seu pedido Git Bash, copie e cole os seguintes comandos de carapaça para o seu comando Git Bash e prima **ENTER**. Estes comandos gerarão a sintaxe para o valor constante da `CERTIFICATE` cadeia.

    ```Bash
    input="./certs/new-device-full-chain.cert.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    Copiar e colar o texto do certificado de saída para o novo valor constante. 


5. No mesmo ficheiro, o valor de cadeia da `PRIVATE_KEY` constante também deve ser atualizado com a chave privada para o certificado do seu dispositivo.

    A sintaxe do texto chave privado deve seguir o padrão abaixo sem espaços extra ou análise feita pelo Visual Studio.

    ```c
    static const char* const PRIVATE_KEY = "-----BEGIN RSA PRIVATE KEY-----\n"
    "MIIJJwIBAAKCAgEAtjvKQjIhp0EE1PoADL1rfF/W6v4vlAzOSifKSQsaPeebqg8U\n"
        ...
    "X7fi9OZ26QpnkS5QjjPTYI/wwn0J9YAwNfKSlNeXTJDfJ+KpjXBcvaLxeBQbQhij\n"
    "-----END RSA PRIVATE KEY-----";
    ```

    Atualizar corretamente este valor de cadeia neste passo também pode ser muito enfadonho e sujeito a erro. Para gerar a sintaxe adequada no seu pedido Git Bash, copie e cole os seguintes comandos de concha de bash, e prima **ENTER**. Estes comandos gerarão a sintaxe para o valor constante da `PRIVATE_KEY` cadeia.

    ```Bash
    input="./private/new-device.key.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    Copie e cole o texto chave de saída para o novo valor constante. 

6. Salve *custom_hsm_example.c.*


## <a name="verify-ownership-of-the-root-certificate"></a>Verificar a propriedade do certificado raiz

1. Utilizando as instruções do [Registo da parte pública de um certificado X.509 e obtenha um código de verificação,](how-to-verify-certificates.md#register-the-public-part-of-an-x509-certificate-and-get-a-verification-code)carrece o certificado raiz ( ) e obtenha um código de `./certs/azure-iot-test-only.root.ca.cert.pem` verificação de DPS.

2. Assim que tiver um código de verificação do DPS para o certificado raiz, execute o seguinte comando a partir do seu diretório de trabalho de script de certificado para gerar um certificado de verificação.
 
    O código de verificação aqui dado é apenas um exemplo. Use o código que gerou a partir de DPS.    

    ```Bash
    ./certGen.sh create_verification_certificate 1B1F84DE79B9BD5F16D71E92709917C2A1CA19D5A156CB9F    
    ```    

    Este script cria um certificado assinado pelo certificado raiz com o nome do sujeito definido para o código de verificação. Este certificado permite que a DPS verifique que tem acesso à chave privada do certificado raiz. Note a localização do certificado de verificação na saída do script. Este certificado é gerado em `.pfx` formato.

    ```output
    Leaf Device PFX Certificate Generated At:
    --------------------------------------------
        ./certs/verification-code.cert.pfx
    ```

3. Como mencionado no [Upload do certificado de verificação assinado,](how-to-verify-certificates.md#upload-the-signed-verification-certificate)faça upload do certificado de verificação e clique em **Verificar** em DPS para completar a prova de posse do certificado raiz.


## <a name="update-the-certificate-store-on-windows-based-devices"></a>Atualize a loja de certificados em dispositivos baseados no Windows

Em dispositivos não Windows, pode passar a cadeia de certificados do código como loja de certificados.

Nos dispositivos baseados no Windows, deve adicionar os certificados de assinatura (raiz e intermédio) a uma [loja de certificados](https://docs.microsoft.com/windows/win32/secauthn/certificate-stores)Windows . Caso contrário, os certificados de assinatura não serão transportados para DPS por um canal seguro com Segurança da Camada de Transporte (TLS).

Para adicionar os certificados de assinatura à loja de certificados em dispositivos baseados no Windows:

1. Num pedido de festa de Git, navegue para o `certs` subdiretório que contém os seus certificados de assinatura e converta-os da `.pfx` seguinte forma.

    Certificado de raiz:

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.root.ca.key.pem -in ./azure-iot-test-only.root.ca.cert.pem -export -out ./root.pfx
    ```
    
    Certificado intermédio:   

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.intermediate.key.pem -in ./azure-iot-test-only.intermediate.cert.pem -export -out ./intermediate.pfx
    ```

2. Clique com o botão De **arranque** do Windows. Em seguida, clique à esquerda **Executar**. Insira *certmgr.mcs* e clique **em Ok** para iniciar o snap-in do gestor de certificados MMC.

3. No gestor de **certificados, em Certificados - Utilizador Atual,** clique **em Autoridades de Certificação de Raiz Fidedignas**. Em seguida, no menu, clique em **Action**  >  **All Tasks**  >  **Import** para importar `root.pfx` .

    * Certifique-se de pesquisar por **Informações Pessoais Exchange (.pfx)**
    * Use `1234` como senha.
    * Coloque o certificado na loja **de certificados Trusted Root Certification Authorities.**

4. No gestor de **certificados, em Certificados - Utilizador Atual,** clique em **Autoridades de Certificação Intermédias.** Em seguida, no menu, clique em **Action**  >  **All Tasks**  >  **Import** para importar `intermediate.pfx` .

    * Certifique-se de pesquisar por **Informações Pessoais Exchange (.pfx)**
    * Use `1234` como senha.
    * Coloque o certificado na loja **de certificados das Autoridades de Certificação Intermédia.**

Os seus certificados de assinatura são agora confiáveis no dispositivo baseado no Windows e a cadeia completa pode ser transportada para DPS.



## <a name="create-an-enrollment-group"></a>Criar um grupo de inscrições

1. Inscreva-se no portal Azure, selecione o botão **Todos os recursos** no menu esquerdo e abra o serviço de Provisionamento de Dispositivos.

2. Selecione o **separador Descodusagens** de Gestão e, em seguida, selecione o botão **de grupo de inscrição Adicionar** no topo.

3. No painel **Do Grupo de Inscrição** adicionar, introduza as seguintes informações e, em seguida, prima o botão **Guardar.**

      ![Adicione o grupo de inscrições para o atestado X.509 no portal](./media/tutorial-custom-hsm-enrollment-group-x509/custom-hsm-enrollment-group-x509.png#lightbox)

    | Campo        | Valor           |
    | :----------- | :-------------- |
    | **Nome do grupo** | Para este tutorial, insira **dispositivos personalizados-hsm-x509** |
    | **Tipo de Attestation** | **Selecionar Certificado** |
    | **Dispositivo do IoT Edge** | Selecione **Falso** |
    | **Tipo de Certificado** | Selecione **Certificado Intermédio** |
    | **Certificado primário .pem ou .cer arquivo** | Navegue para o intermediário que criou anteriormente *(./certs/azure-iot-test-only.intermediate.cert.pem*) |


## <a name="configure-the-provisioning-device-code"></a>Configure o código do dispositivo de provisionamento

Nesta secção, atualize o código de amostra para o fornecimento do dispositivo com a sua instância de Serviço de Provisionamento de Dispositivos. Se o dispositivo for autenticado, será atribuído a um hub IoT ligado à instância do Serviço de Provisionamento de Dispositivos.

1. No portal Azure, selecione o **separador 'Vista Geral'** para o serviço de Provisionamento de Dispositivos e note o valor **_ID Scope._**

    ![Extrair informações de ponto final do Serviço Aprovisionamento de Dispositivos do painel do portal](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. No Solution Explorer for Visual Studio, navegue para **Provisioning_Samples > prov_dev_client_sample > Ficheiros Source** e abra *prov_dev_client_sample.c*.

3. Localize a constante `id_scope` e substitua o valor pelo seu **Âmbito do ID** que copiou anteriormente. 

    ```c
    static const char* id_scope = "0ne00000A0A";
    ```

4. Localize a definição da função `main()` no mesmo ficheiro. Certifique-se de que `hsm_type` a variável está definida `SECURE_DEVICE_TYPE_X509` como mostrado abaixo.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    //hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

5. Clique com o botão direito do rato no projeto **prov\_dev\_client\_sample** e selecione **Definir como Projeto de Arranque**.

6. No menu Visual Studio, selecione **Debug**  >  **Start sem depurar** para executar a solução. Quando solicitado para reconstruir o projeto, selecione **Sim** para reconstruir o projeto antes de executar.

    A seguinte saída é um exemplo da amostra do cliente do dispositivo de provisionamento que é reinicialda com sucesso e ligação ao serviço de fornecimento. O dispositivo foi atribuído a um hub IoT e registado:

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-01
    Press enter key to exit:
    ```

7. No portal, navegue para o hub IoT ligado ao seu serviço de fornecimento e selecione o **separador dispositivos IoT.** No fornecimento bem sucedido do dispositivo X.509 ao hub, o seu ID do dispositivo aparece na lâmina dos **dispositivos IoT,** com *status* conforme **ativado**. Pode ser necessário **premir** o botão Refresh na parte superior. 

    ![O dispositivo HSM personalizado está registado no hub IoT](./media/tutorial-custom-hsm-enrollment-group-x509/hub-provisioned-custom-hsm-x509-device.png) 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de testar e explorar esta amostra do cliente do dispositivo, use os seguintes passos para eliminar todos os recursos criados por este tutorial.

1. Feche a janela da saída do exemplo de dispositivo cliente no seu computador.
1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu serviço de Provisionamento de Dispositivos. Abrir **As Inscrições** para o seu serviço e, em seguida, selecionar o **separador Grupos de Inscrição.** Selecione a caixa de verificação ao lado do Nome de *Grupo* do grupo de dispositivos que criou neste tutorial e prima o botão **Eliminar** na parte superior do painel. 
1. Clique em **Certificados** em DPS. Para cada certificado que carregou e verificou neste tutorial, clique no certificado e clique no botão **Apagar** para o remover.
1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu hub IoT. Abra **os dispositivos IoT** para o seu hub. Selecione a caixa de verificação ao lado do *ID* do dispositivo que registou neste tutorial. Clique no botão **Eliminar** na parte superior do painel.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você a forte um dispositivo X.509 usando um HSM personalizado para o seu hub IoT. Para aprender a providenciar dispositivos IoT para vários centros, continue para o próximo tutorial. 

> [!div class="nextstepaction"]
> [Tutorial: Dispositivos de provisão através de hubs IoT equilibrados em carga](tutorial-provision-multiple-hubs.md)
