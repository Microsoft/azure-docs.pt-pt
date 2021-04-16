---
title: Tutorial - Utilize scripts da Microsoft para criar certificados de teste x.509 para Azure IoT Hub | Microsoft Docs
description: Tutorial - Use scripts personalizados para criar certificados de CA e dispositivos para Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: ff4b63f49a87dd9ca6b0ef458bdcf1c285a34a18
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378215"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>Tutorial: Utilizar scripts fornecidos pela Microsoft para criar certificados de teste

A Microsoft fornece scripts PowerShell e Bash para ajudá-lo a entender como criar os seus próprios certificados X.509 e autenticá-los num Hub IoT. Os scripts estão localizados num [repositório](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)gitHub. São fornecidos apenas para fins de demonstração. Os certificados por eles criados não devem ser utilizados para a produção. Os certificados contêm senhas codificadas ("1234") e expiram após 30 dias. Para um ambiente de produção, você precisará usar as suas próprias melhores práticas para a criação de certificados e gestão vitalícia.

## <a name="powershell-scripts"></a>Scripts PowerShell

### <a name="step-1---setup"></a>Passo 1 - Configuração

Obtenha OpenSSL para Windows. Consulte <https://www.openssl.org/docs/faq.html#MISC4> os locais para o descarregar ou <https://www.openssl.org/source/> construir a partir da fonte. Em seguida, executar os scripts preliminares:

1. Copie os scripts deste [repositório](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) GitHub para o diretório local no qual pretende trabalhar. Todos os ficheiros serão criados como crianças deste diretório.

1. Inicie a PowerShell como administrador.

1. Mude para o diretório onde carregou os scripts.

1. Na linha de comando, desabrocha a variável `$ENV:OPENSSL_CONF` ambiente para o diretório no qual está localizado o ficheiro de configuração de abertura (openssl.cnf).

1. Corra `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` para que o PowerShell possa executar os scripts.

1. Execute `. .\ca-certs.ps1`. Isto traz as funções do script para o espaço de nome global PowerShell.

1. Execute `Test-CACertsPrerequisites`. A PowerShell utiliza a Loja de Certificados do Windows para gerir certificados. Este comando verifica que não haverá colisões de nomes mais tarde com certificados existentes e que o OpenSSL está configurado corretamente.

### <a name="step-2---create-certificates"></a>Passo 2 - Criar certificados

Execute `New-CACertsCertChain [ecc|rsa]`. O ECC é recomendado para certificados de CA, mas não é necessário. Este script atualiza a sua loja de diretório e certificado do Windows com os seguintes certificados ca e intermédios:

* intermediário1.pem
* intermediário2.pem
* intermediário3.pem
* RootCA.cer
* RootCA.pem

Depois de executar o script, adicione o novo certificado ca (RootCA.pem) ao seu IoT Hub:

1. Vá ao seu Hub IoT e navegue para Certificados.

1. Selecione **Adicionar**.

1. Introduza um nome de exibição para o certificado de CA.

1. Faça o upload do certificado de A.C.

1. Selecione **Guardar**.

### <a name="step-3---prove-possession"></a>Passo 3 - Provar a posse

Agora que carregou o seu certificado de AC para o seu IoT Hub, terá de provar que é realmente o dono:

1. Selecione o novo certificado de CA.

1. Selecione **Código de Verificação** de Geração no diálogo Detalhes do **Certificado.** Para mais informações, consulte [Prove a posse de um certificado de AC.](tutorial-x509-prove-possession.md)

1. Crie um certificado que contenha o código de verificação. Por exemplo, se o código de verificação `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` for, executar o seguinte para criar um novo certificado no seu diretório de trabalho contendo o sujeito `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` . O script cria um certificado chamado `VerifyCert4.cer` .

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. Faça o upload `VerifyCert4.cer` para o seu IoT Hub no diálogo Detalhes do **Certificado.**

1. Selecione **Verificar**.

### <a name="step-4---create-a-new-device"></a>Passo 4 - Criar um novo dispositivo

Crie um dispositivo para o seu Hub IoT:

1. No seu Hub IoT, navegue para a secção **dispositivos IoT.**

1. Adicione um novo dispositivo com ID `mydevice` .

1. Para autenticação, escolha **X.509 CA Assinado.**

1. Corra `New-CACertsDevice mydevice` para criar um novo certificado de dispositivo. Isto cria os seguintes ficheiros no seu diretório de trabalho:

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>Passo 5 - Teste o certificado do dispositivo

Vá à [Autenticação do Certificado de Teste](tutorial-x509-test-certificate.md) para determinar se o certificado do dispositivo pode autenticar no seu Hub IoT. Necessitará da versão PFX do seu certificado, `mydevice.pfx` .

### <a name="step-6---cleanup"></a>Passo 6 - Limpeza

A partir do menu inicial, abra **os Certificados de Computador e navegue** para  **Certificados - Computador Local > pessoal**. Remover certificados emitidos por "Azure IoT CA TestOnly*". Da mesma forma, remova os certificados adequados da Autoridade>de **Certificação de Raízes Fidedignas > Certificados e >Certificados Intermédios > Certificados**.

## <a name="bash-scripts"></a>Roteiros de Bash

### <a name="step-1---setup"></a>Passo 1 - Configuração

1. Começa bash.

1. Mude para o diretório em que quer trabalhar. Todos os ficheiros serão criados neste diretório.

1. Copiar `*.cnf` e para o seu `*.sh` diretório de trabalho.

### <a name="step-2---create-certificates"></a>Passo 2 - Criar certificados

1. Execute `./certGen.sh create_root_and_intermediate`. Isto cria os seguintes ficheiros no **diretório de certificados:**

    * azure-iot-test-only.chain.ca.cert.pem
    * azure-iot-test-only.intermediate.cert.pem
    * azure-iot-test-only.root.ca.cert.pem

1. Vá ao seu Hub IoT e navegue para **Certificados.**

1. Selecione **Adicionar**.

1. Introduza um nome de exibição para o certificado de CA.

1. Faça o upload apenas do certificado de AC para o seu Hub IoT. O nome do certificado é `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. Selecione **Guardar**.

### <a name="step-3---prove-possession"></a>Passo 3 - Provar a posse

1. Selecione o novo certificado de CA criado no passo anterior.

1. Selecione **Código de Verificação** de Geração no diálogo Detalhes do **Certificado.** Para mais informações, consulte [Prove a posse de um certificado de AC.](tutorial-x509-prove-possession.md)

1. Crie um certificado que contenha o código de verificação. Por exemplo, se o código de verificação `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` for, executar o seguinte para criar um novo certificado no seu diretório de trabalho nomeado `verification-code.cert.pem` que contenha o sujeito `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` .

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. Faça o upload do certificado para o seu hub IoT no diálogo Detalhes do **Certificado.**

1. Selecione **Verificar**.

### <a name="step-4---create-a-new-device"></a>Passo 4 - Criar um novo dispositivo

Crie um dispositivo para o seu hub IoT:

1. No seu Hub IoT, navegue para a secção dispositivos IoT.

1. Adicione um novo dispositivo com ID `mydevice` .

1. Para autenticação, escolha **X.509 CA Assinado.**

1. Corra `./certGen.sh create_device_certificate mydevice` para criar um novo certificado de dispositivo. Isto cria dois ficheiros `new-device.cert.pem` nomeados e `new-device.cert.pfx` ficheiros no seu diretório de trabalho.

### <a name="step-5---test-your-device-certificate"></a>Passo 5 - Teste o certificado do dispositivo

Vá à [Autenticação do Certificado de Teste](tutorial-x509-test-certificate.md) para determinar se o certificado do dispositivo pode autenticar no seu Hub IoT. Necessitará da versão PFX do seu certificado, `new-device.cert.pfx` .

### <a name="step-6---cleanup"></a>Passo 6 - Limpeza

Porque o script de bash simplesmente cria certificados no seu diretório de trabalho, apenas elimine-os quando você estiver feito testes.

## <a name="next-steps"></a>Passos Seguintes

Para testar o seu certificado, vá à [Autenticação do Certificado de Teste](tutorial-x509-test-certificate.md) para determinar se o seu certificado pode autenticar o seu dispositivo no seu IoT Hub.
