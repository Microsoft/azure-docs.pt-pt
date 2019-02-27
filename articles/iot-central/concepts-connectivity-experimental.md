---
title: Conectividade do dispositivo no Azure IoT Central | Documentos da Microsoft
description: Este artigo apresenta os principais conceitos relacionados com a conectividade do dispositivo no Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: edffc6677609537d8a07aeae45a57c5e88449099
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882667"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Conectividade do dispositivo no Azure IoT Central

Este artigo apresenta os conceitos-chave que estão relacionadas com a conectividade do dispositivo no Microsoft Azure IoT Central.

Utiliza o Azure IoT Central [serviço de aprovisionamento de dispositivos do Azure IoT Hub](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) para registar e ligar dispositivos à escala.

Com o serviço de aprovisionamento de dispositivos:

- Os clientes podem agora gerar credenciais de dispositivos e configurar os dispositivos offline sem ter de registar primeiro os dispositivos no Azure IoT Central.
- Centro de IoT do Azure suporta a ligação de dispositivo com certificados X.509, enquanto continua a suportar e melhorar a conectividade com assinaturas de acesso partilhado.
- Os clientes do Centro de IoT do Azure agora podem utilizar o seu próprio dispositivo IDs para registar dispositivos no Azure IoT Central, que permite a integração simple com sistemas de back office existentes.
- Existe uma forma consistente para ligar dispositivos ao Azure IoT Central.

>[!NOTE]
>Azure IoT Central utiliza o serviço de aprovisionamento de dispositivo IoT Hub para todos os registo de dispositivos e a ligação. [Saiba mais](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

Com base no seu caso de utilização, siga as instruções adequadas para ligar dispositivos ao Azure IoT Central:

- [Ligar um único dispositivo rapidamente (ao utilizar assinaturas de acesso partilhado)](#connect-a-single-device)
- [Ligar dispositivos à escala através de assinaturas de acesso partilhado](#connect-devices-at-scale-using-shared-access-signatures)
- [Ligar dispositivos à escala com certificados X.509](#connect-devices-using-x509-certificates) (recomendado para cargas de trabalho de produção)
- [Ligar-se sem o registo de dispositivos primeiro](#connect-without-first-registering-devices) 

>[!NOTE]
>É o ponto final global para os dispositivos para se ligar e aprovisionar **global.azure-dispositivos-provisioning.net**.

## <a name="connect-a-single-device"></a>Ligar um dispositivo único

Para ligar um único dispositivo ao Azure IoT Central, utilizando as assinaturas de acesso partilhado:

1. Adicionar uma **dispositivo real** partir **Device Explorer**. Selecione **+ novo** > **Real** para adicionar um dispositivo real.
    - Introduza o **ID de dispositivo** (deve estar em minúsculas) ou utilizar o ID de dispositivo sugerido.
    - Introduza o **nome do dispositivo** ou utilize o nome sugerido.

    ![Adicionar Dispositivo](media/concepts-connectivity/add-device.png)

1. Obter os detalhes de ligação, como o **ID de âmbito**, **ID do dispositivo**, e **chave primária**, para o dispositivo foi adicionado ao selecionar **Connect** sobre o página do dispositivo.

    - **[Definir âmbito de ID](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)**  é por aplicação Azure IoT Central e é gerado pelo serviço de aprovisionamento de dispositivos. Este ID é utilizado para garantir um ID de dispositivo exclusivo dentro de uma aplicação.
    - **ID de dispositivo** é o ID de dispositivo exclusivo por aplicação. O dispositivo tem de enviar o ID de dispositivo como parte da chamada de registo.
    - **Chave primária** é um token de assinatura de acesso partilhado, gerado pelo Azure IoT Central para esse dispositivo específico.
 
    ![Detalhes da ligação](media/concepts-connectivity/device-connect.png)

1. Utilize os detalhes de ligação **identidade do dispositivo**, **nome do dispositivo**e o dispositivo **chave primária** em seu código de dispositivo para aprovisionar e ligar o seu dispositivo e começar a ver o fluxo de dados por meio de instantaneamente. Se estiver a utilizar um dispositivo de MXChip IoT DevKit (DevKit), siga [as instruções passo a passo](howto-connect-devkit-experimental.md#add-a-real-device), a partir da secção "Preparar o dispositivo DevKit."

    Aqui estão as referências para outros idiomas, que pode querer utilizar.

    - **Linguagem C:** Siga [este cliente de dispositivo de exemplo do C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) para ligar um dispositivo de exemplo. Utilize as seguintes definições no exemplo:

         ```c
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         ## Enter the Device Id and Symmetric keys 
         prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
        ```

    - **Node.js:**  Siga [as instruções passo a passo](tutorial-add-device-experimental.md#prepare-the-client-code), a partir da secção "Preparar o código do cliente."

## <a name="connect-devices-at-scale-by-using-shared-access-signatures"></a>Ligar dispositivos à escala através de assinaturas de acesso partilhado

Para ligar dispositivos à escala com o Azure IoT Central com assinaturas de acesso partilhado, existem dois passos gerais:

1. **Registo de dispositivos:** Importar para o Azure IoT Central por meio de um ficheiro CSV para registar os dispositivos. Em seguida, utilize o **exportar** ação para exportar os seus dispositivos e obter os detalhes de ligação do dispositivo.
1. **Configuração do dispositivo:** Os dispositivos com os detalhes de ligação de programas (**ID de âmbito**, **ID do dispositivo**, e **chave primária**). Quando cada dispositivo é ativado, ele chamará o serviço de aprovisionamento de dispositivos para obter suas informações de ligação ou a atribuição de aplicações do Azure IoT Central.

>[!NOTE]
>Uma opção avançada também está disponível em que pode ligar dispositivos sem ter de registar primeiro os dispositivos no Azure IoT Central. [Saiba mais](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

### <a name="device-registration"></a>Registo de dispositivo

Para ligar um grande número de dispositivos para a sua aplicação, o Azure IoT Central suporta a importação em massa de dispositivos por meio de um ficheiro CSV. 

1. Importar dispositivos para registá-los em seu aplicativo:

   1. Selecione **Device Explorer** no menu da esquerda.
   1. No painel esquerdo, selecione o modelo de dispositivo para o qual pretende em massa, crie os dispositivos. 
   1. Selecione **importação**e, em seguida, selecione o ficheiro CSV que tem a lista de identificações de dispositivo para ser importado. O ficheiro CSV deve ter as seguintes colunas (e cabeçalhos):

       - IOTC_DeviceID (deve estar em minúsculas)
       - IOTC_DeviceName (opcional)

   1. Após a importação estiver concluída, é apresentada uma mensagem de êxito na grade de dispositivo.

1. Exporte dispositivos para obter os detalhes de ligação:

   O **exportar** ação cria um ficheiro CSV com o ID de dispositivo, o nome do dispositivo e as chaves de dispositivo. Utilize estes detalhes para ligar o dispositivo para o Azure IoT Central. A exportação de em massa dispositivos a partir do seu aplicativo:

   1. Selecione **Device Explorer** no menu da esquerda.
   1. Selecione os dispositivos que pretende exportar e, em seguida, selecione o **exportar** ação.
   1. Depois de concluída a exportação, é apresentada uma mensagem de êxito, juntamente com uma ligação para transferir o ficheiro gerado.
   1. Clique na mensagem de sucesso para transferir o ficheiro numa pasta local no disco.
   1. O ficheiro CSV exportado terá as seguintes colunas que contêm os detalhes da ligação **ID do dispositivo**, **nome do dispositivo**, **dispositivo primário ou secundário chaves**e  **Primário ou secundário thumbprints de certificado**:

       - IOTC_DEVICEID
       - IOTC_DEVICENAME
       - IOTC_SASKEY_PRIMARY
       - IOTC_SASKEY_SECONDARY
       - IOTC_X509THUMBPRINT_PRIMARY
       - IOTC_X509THUMBPRINT_SECONDARY

### <a name="device-setup"></a>Configuração do dispositivo

 Para aprovisionar e ligar o seu dispositivo, utilize os detalhes da ligação **identidade de dispositivo (IOTC_DEVICEID)**, **dispositivo primário chave (IOTC_SASKEY_PRIMARY)**, e **ID de âmbito** no o código do dispositivo. Se ainda não o fez, obtenha os **ID de âmbito** da sua aplicação do Azure IoT Central ao selecionar **administração** > **ligação do dispositivo**  >  **Definir âmbito de ID**.

Se estiver a ligar um dispositivo de DevKit, siga [as instruções passo a passo](howto-connect-devkit-experimental.md#add-a-real-device), a partir da secção "Preparar o dispositivo DevKit."

Aqui estão as referências para outros idiomas, que pode querer utilizar.

- **Linguagem C:** Siga [este cliente de dispositivo de exemplo do C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) para ligar um dispositivo de exemplo. Utilize as seguintes definições no exemplo:

     ```c
     hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

     ## Enter the Device Id and Symmetric keys 
     prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
    ```

- **Node.js:** Siga [as instruções passo a passo](tutorial-add-device-experimental.md#prepare-the-client-code), a partir da secção "Preparar o código do cliente."

## <a name="connect-devices-by-using-x509-certificates"></a>Ligar dispositivos ao utilizar certificados X.509

Usando certificados X.509 como um mecanismo de atestado é uma excelente maneira de aumentar a produção e simplificar o aprovisionamento de dispositivos. Certificados X.509, normalmente, são organizados numa cadeia de certificados de confiança, em que cada certificado na cadeia está assinado pela chave privada do certificado superior seguinte e assim por diante, que termina num certificado de raiz autoassinado. Esta estrutura estabelece uma cadeia de delegados de confiança a partir do certificado de raiz gerado por uma autoridade de certificação de raiz fidedigna (AC) para baixo por meio de cada AC intermediária para o certificado de "folha" de entidade final que está instalado num dispositivo. Para obter mais informações, consulte [autenticação do dispositivo utilizando certificados X.509 de AC](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Para ligar dispositivos ao Azure IoT Central com certificados X.509, há três etapas principais envolvidas:
 
1. **Configurar as definições de ligação** na aplicação do Azure IoT Central, adicionando ou verificar o X.509 certificado raiz ou intermediário que é utilizado para gerar os certificados de dispositivo. Existem três passos para configurar as definições de ligação para certificados X.509:  

    - **Adicionar o X.509 certificado raiz ou intermediário** que está a utilizar para gerar os certificados de dispositivos de folha. Aceda a **Administration** > **ligação do dispositivo** > **certificados**. 
    
       ![Definições de ligação](media/concepts-connectivity/connection-settings.png)

    - **Verifique se o certificado.** Verificar a propriedade do certificado garante que o carregador do certificado tem a chave privada do certificado. Para verificar o certificado:

        - Gere o código de verificação. Selecione o botão junto a **código de verificação** campo para gerar esse código. 
        - Crie um certificado de verificação de X.509 com o código de verificação. Guarde o certificado como um ficheiro. cer. 
        - Carregue o certificado de verificação assinado e selecione **Verifique se**.

        ![Definições de ligação](media/concepts-connectivity/verify-cert.png)

    - **Adicione um certificado secundário.** Durante o ciclo de vida da sua solução de IoT, terá de implementar certificados. Duas das principais razões para implementar os certificados são falhas de segurança e expirações de certificado. Certificados secundários de reduzem o tempo de inatividade para os dispositivos que está a tentar aprovisionar enquanto estiver a atualizar o certificado primário.

      **Para fins de teste apenas a fins:**
    
      Aqui estão algumas ferramentas de linha de comandos de utilitário que pode utilizar para gerar certificados de AC e certificados de dispositivo.

      - Se estiver a utilizar um dispositivo de DevKit, aqui está uma [ferramenta da linha de comandos](https://aka.ms/iotcentral-docs-dicetool) para gerar certificados de AC. Adicione-o à sua aplicação do Azure IoT Central e verifique se os certificados. 

      - Uso [essa ferramenta da linha de comandos](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) para:

          - Crie a cadeia de certificados (siga a etapa 2 da documentação do GitHub). Guardar os certificados como ficheiros. cer e carregá-los para o Azure IoT Central (como certificados primários).
          - Obter o código de verificação a partir da aplicação do Azure IoT Central e gerar o certificado (siga passo 3 da documentação do GitHub) para carregar o certificado para o confirmar. 
          - Crie certificados de folha com o seu ID de dispositivo como um parâmetro para a ferramenta (siga etapa 4). Guarde o certificado e utilizá-lo no seu dispositivo.

1. **Registar dispositivos** importando-os para o Azure IoT Central por meio de um ficheiro CSV.

1. **Configuração do dispositivo**: Utilize o certificado de raiz carregado para gerar os certificados de folha. Certifique-se de utilizar o ID de dispositivo como o CNAME nos certificados de folha e certifique-se de que está em minúsculas. Aqui está uma [ferramenta da linha de comandos](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) para gerar certificados de folha/dispositivo para **teste apenas a fins**.

1. **O dispositivo com informações de serviço de aprovisionamento do programa**, ativá-la obter os detalhes de ligação e a atribuição de aplicações do Azure IoT Central, quando está ativado.

    Para obter mais informações, veja estes artigos:

    - [Implementação de exemplo para Raspberry Pi](https://aka.ms/iotcentral-docs-Raspi-releases)  

    - [Cliente de dispositivo de exemplo em C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-first-registering-devices"></a>Ligar-se sem o registo de dispositivos primeiro

Um dos principais cenários que suporta o Azure IoT Central é para OEMs para dispositivos em massa-fabricante, gerar as credenciais e configurar os dispositivos na fábrica de sem ter de registar primeiro os dispositivos no Azure IoT Central. Depois dos dispositivos estão ativados e eles tentam ligar ao Azure IoT Central, o operador aprova os dispositivos liguem à aplicação do Azure IoT Central.

Eis o fluxo para ligar os dispositivos com esta funcionalidade:

![Definições de ligação](media/concepts-connectivity/device-connection-flow.png)

Siga os passos com base na sua escolha de esquema de autenticação do dispositivo (certificados X.509 ou assinaturas de acesso partilhado):

1. **Configurar ou obter as definições de ligação:**

    - **Certificados X.509:** [Adicionar e verificar o certificado raiz ou intermediário](#connect-devices-using-x509-certificates) e utilizá-lo para gerar os certificados de dispositivo no próximo passo.
    - **Assinaturas de acesso partilhado:** Copie a chave primária (esta chave é a chave de assinatura de acesso partilhado de grupo para esta aplicação do Azure IoT Central) e utilizá-lo para gerar as chaves de assinatura de acesso do dispositivo partilhado no próximo passo.

       ![Definições de ligação para assinaturas de acesso partilhado](media/concepts-connectivity/connection-settings-sas.png)

1. **Gere as credenciais do dispositivo:**

    - **Certificados X.509:** Gere os certificados de folha para os seus dispositivos com o certificado raiz ou intermediário que adicionou a esta aplicação. Certifique-se de utilizar o ID de dispositivo como um CNAME nos certificados de folha e certifique-se de que está em minúsculo. Aqui está uma [ferramenta da linha de comandos](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) para gerar certificados de folha/dispositivo para fins de teste.
    - **Assinaturas de acesso partilhado:** Para gerar chaves de assinatura de acesso do dispositivo partilhado, utilizá-lo [ferramenta da linha de comandos](https://www.npmjs.com/package/dps-keygen). Utilize a chave de assinatura de acesso partilhado primário (chave de assinatura de acesso partilhado de grupo) do passo anterior. Certifique-se de que o dispositivo que ID está em minúsculas.

        Utilize o seguinte comando para obter a cadeia de ligação do dispositivo: 

        ```
        npm i -g dps-keygen
        ```
    
        Utilize o seguinte comando para gerar uma chave de assinatura de acesso partilhado do dispositivo:
                        
        ```
        dps-keygen <Primary_Key(GroupSAS)> <device_id>
        ```

1. **Configure os dispositivos:** Flash cada dispositivo com o **ID de âmbito**, **ID do dispositivo**, e **chave SAS/certificado do dispositivo**e, em seguida, ativar o dispositivo para ligá-lo para a aplicação Azure IoT Central.

1. **Ligue os dispositivos ao Azure IoT Central:** Depois dos dispositivos são ativados, se ligam ao dispositivo aprovisionamento de serviço/Azure IoT Central para o registo.

1. **Associe os dispositivos a um modelo:** Os dispositivos ligados apresentados no **dispositivos não associados** na **Device Explorer**. O estado de aprovisionamento de dispositivo está **registado**. **Associar** os dispositivos para o modelo de dispositivo apropriado e aprovar os dispositivos liguem à aplicação do Azure IoT Central. Os dispositivos obtém os detalhes de ligação para a aplicação Azure IoT Central, e, em seguida, ligar e começar a enviar dados. Aprovisionamento de dispositivos agora estiver concluído e o **estado de aprovisionamento** para os dispositivos passa a **aprovisionado**.

## <a name="device-provisioning-status"></a>Estado de aprovisionamento de dispositivo

Quando um dispositivo real está ligado ao Azure IoT Central, ocorrem os seguintes passos:

1. **Registado**: O dispositivo está registado em primeiro lugar, que significa que o dispositivo é criado no Azure IoT Central e tem o ID de dispositivo para o dispositivo. Um dispositivo é registado quando:

    * É adicionado um novo dispositivo real **Device Explorer**
    * Um conjunto de dispositivos é adicionado ao utilizar **importação** no **Device Explorer**
    * Um dispositivo que ainda não foi registado liga-se com credenciais válidas e é visível em **dispositivos não associados**

    Em todos os casos acima, o **estado de aprovisionamento** é **registado**.

1. **Aprovisionado**: Quando o dispositivo estabelece ligação com credenciais válidas, o Azure IoT Central conclui a etapa de aprovisionamento (ao criar o dispositivo no IoT Hub). Azure IoT Central, em seguida, devolve a cadeia de ligação para o dispositivo, de modo a poder ligar e começar a enviar dados. O dispositivo **estado de aprovisionamento** ativa a partir de **registado** para **aprovisionado**.

1. **Bloqueado**: O operador pode bloquear um dispositivo. Depois de um dispositivo está bloqueado, ele não pode enviar dados para o Azure IoT Central e terá que ser reposto. Dispositivos que estão bloqueados têm o **estado de aprovisionamento** dos **bloqueado**. O operador também pode desbloquear o dispositivo. Depois que ele tem desbloqueado, do dispositivo **estado de aprovisionamento** retorna para seu estado anterior (**registado** ou **aprovisionado**). 

## <a name="get-the-device-connection-string"></a>Obter a cadeia de ligação do dispositivo

Pode obter a cadeia de ligação de dispositivos do Iot Hub hub IoT do Azure com os seguintes passos:

1. Obter detalhes de ligação do dispositivo, como o **ID de âmbito**, **ID do dispositivo**, e **chave primária** do **ligação do dispositivo** página. Para obter estes detalhes, vá para o **dispositivo** página e selecione **Connect**. 

    ![Detalhes da ligação](media/concepts-connectivity/device-connect.png)

1. Obter cadeia de ligação do dispositivo com a ferramenta de linha de comandos do dps-keygen. Utilize o seguinte comando para obter a cadeia de ligação do dispositivo:  

    ```cmd/sh
    npm i -g dps-keygen
    ```

    Para criar uma cadeia de ligação, localize o binário sob o *bin /* pasta:

    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```

    [Saiba mais sobre a ferramenta de dps keygen](https://www.npmjs.com/package/dps-keygen).

## <a name="sdk-support"></a>Suporte do SDK

Os SDKs IoT do Azure oferecem a forma mais fácil para usar o código nos seus dispositivos que se ligam à sua aplicação do Azure IoT Central. Os SDKs seguintes estão disponíveis:

- [SDK do Azure IoT para C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK para Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK do Azure IoT para node. js](https://github.com/azure/azure-iot-sdk-node)
- [SDK do IoT do Azure para Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK para .NET](https://github.com/azure/azure-iot-sdk-csharp)

Cada dispositivo liga-se através de uma cadeia de ligação exclusivo que identifica o dispositivo. Pode ligar um dispositivo apenas para o hub de IoT em que está registado. Quando cria um dispositivo real na sua aplicação do Azure IoT Central, o aplicativo gera uma cadeia de ligação para que possa utilizar.

## <a name="sdk-features-and-iot-hub-connectivity"></a>Funcionalidades do SDK e conectividade do IoT Hub

Todas as comunicações de dispositivos com o IoT Hub utiliza as seguintes opções de conectividade do IoT Hub:

- [Mensagens do dispositivo para a cloud](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Dispositivos duplos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

A tabela seguinte resume como o mapeamento de funcionalidades do dispositivo Azure IoT Central, para funcionalidades do IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Medida: Telemetria | Mensagens do dispositivo para a cloud |
| Propriedades do dispositivo | Propriedades comunicadas do dispositivo duplo |
| Definições | Dispositivo duplo desejado e propriedades comunicadas |

Para saber mais sobre como utilizar os SDKs IoT do Azure, veja os artigos seguintes para o código de exemplo:

- [Ligar um cliente Node.js genérico à aplicação do Azure IoT Central](howto-connect-nodejs-experimental.md)
- [Ligar um dispositivo de Raspberry Pi a sua aplicação do Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Ligar um dispositivo de MXChip IoT DevKit à sua aplicação do Azure IoT Central](howto-connect-devkit-experimental.md)


## <a name="protocols"></a>Protocolos

Os SDKs IoT do Azure suportam os seguintes protocolos de rede para ligar a um hub IoT:

- MQTT
- AMQP
- HTTPS

Para obter informações sobre estes protocolos e orientações sobre como escolher um, consulte [escolher um protocolo de comunicação](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Se o seu dispositivo não é possível utilizar qualquer um dos protocolos suportados, pode utilizar o Azure IoT Edge para a conversão de protocolo. IoT Edge suporta outros cenários de inteligência no edge para a descarga de processamento na periferia da aplicação do Azure IoT Central.

## <a name="security"></a>Segurança

Todos os dados trocados entre dispositivos e a sua aplicação do Azure IoT Central são encriptados. IoT Hub autentica todos os pedidos de um dispositivo que se liga a qualquer um dos pontos finais com acesso de dispositivo IoT Hub. Para evitar a troca de credenciais durante a transmissão, um dispositivo utiliza tokens assinados para autenticar. Para obter mais informações, veja [Controlar o acesso ao Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

## <a name="next-steps"></a>Passos Seguintes

- [Preparar e ligar um dispositivo de MXChip IoT DevKit](howto-connect-devkit-experimental.md)
- [Preparar e ligar um dispositivo de Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Ligar um cliente Node.js genérico à aplicação do Azure IoT Central](howto-connect-nodejs-experimental.md)
