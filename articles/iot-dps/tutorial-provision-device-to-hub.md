---
title: Aprovisionar um dispositivo com o Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure | Microsoft Docs
description: Aprovisionar o seu dispositivo num hub IoT individual com o Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 04/12/2018
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9ff134b0747e78773c95fac7ceab4cddd61c601d
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227019"
---
# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Aprovisionar o dispositivo num hub IoT com o Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure

No tutorial anterior, aprendeu a configurar um dispositivo para se ligar ao Serviço Aprovisionamento de Dispositivos. Neste tutorial, vai aprender a utilizar este serviço para aprovisionar o seu dispositivo num hub IoT individual através de aprovisionamento automático e **_listas de inscrição_**. Este tutorial mostrar-lhe como:

> [!div class="checklist"]
> * Inscrever o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registado

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, confirme que configura o seu dispositivo tal como explicado no tutorial [Setup a device to provision using Azure IoT Hub Device Provisioning Service](./tutorial-set-up-device.md) (Configurar um dispositivo para ser aprovisionado com o Serviço Aprovisionamento de Dispositivos no Hub IoT).

Se não estiver familiarizado com o processo de aprovisionamento automático, reveja [Auto-provisioning concepts](concepts-auto-provisioning.md) (Conceitos de aprovisionamento automático) antes de continuar.

<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Inscrever o dispositivo

Este passo envolve a adição dos artefactos de segurança exclusiva do dispositivo ao Serviço de Aprovisionamento de Dispositivos. Esses artefactos de segurança baseiam-se no [Mecanismo de atestado](concepts-device.md#attestation-mechanism) do dispositivo da seguinte forma:

- Para dispositivos baseados em TPM, precisa:
    - Da *Chave de Endossamento* que é exclusiva de cada chip ou simulação de TPM, que é obtida junto do fabricante do chip de TPM.  Leia [Understand TPM Endorsement Key](https://technet.microsoft.com/library/cc770443.aspx) (Compreender a Chave de Endossamento de TPM) para obter mais informações.
    - Do *ID de Registo* que é utilizado para identificar exclusivamente um dispositivo no espaço de nomes/âmbito. Este ID pode ou não ser o mesmo que o ID de dispositivo. O ID é obrigatório para todos os dispositivos. Para os dispositivos baseados em TPM, o ID de registo pode derivar do próprio TPM, como, por exemplo, um hash SHA-256 da Chave de Endossamento de TPM.

      [![Informações de inscrição do TPM no portal](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png#lightbox)  

- Para dispositivos baseados em X.509, precisa:
    - Do [certificado emitido para o chip ou simulação de X.509](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx), sob a forma de ficheiro *.pem* ou *.cer*. Para inscrição individual, tem de utilizar o por dispositivo *certificado autoassinado* para o seu sistema X.509, enquanto para grupos de inscrição, tem de utilizar o *certificado de raiz*. 

      [![Adicionar inscrição individual para fins de atestado X.509 no portal](./media/tutorial-provision-device-to-hub/individual-enrollment.png)](./media/tutorial-provision-device-to-hub/individual-enrollment.png#lightbox)

Existem duas formas de inscrever o dispositivo no Serviço Aprovisionamento de Dispositivos:

- **Grupos de inscrição** Representam um grupo de dispositivos que partilham um mecanismo de atestado específico. Recomendamos a utilização de um grupo de inscrição para um grande número de dispositivos que partilhem uma configuração inicial pretendida ou para dispositivos que pertencerão todos ao mesmo inquilino. Para obter mais informações sobre o atestado de identidade para grupos de inscrição, veja [Segurança](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

    [![Adicionar inscrição de grupo para fins de atestado X.509 no portal](./media/tutorial-provision-device-to-hub/group-enrollment.png)](./media/tutorial-provision-device-to-hub/group-enrollment.png#lightbox)

- **Inscrições Individuais** representam uma entrada para um único dispositivo que pode ser registado com o Serviço Aprovisionamento de Dispositivos. As inscrições individuais podem utilizar certificados x509 ou tokens SAS (num TPM real ou virtual) como mecanismos de atestação. Recomendamos a utilização das inscrições individuais para os dispositivos que precisam de configurações iniciais exclusivas e para os dispositivos que só podem utilizar tokens SAS através de TPM ou de TPM virtual como o mecanismo de atestação. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

Agora, vai inscrever o dispositivo no Serviço Aprovisionamento de Dispositivos com os artefactos de segurança necessários com base no mecanismo de atestação daquele. 

1. Inicie sessão no portal do Azure, clique no botão **Todos os recursos**, no menu do lado esquerdo, e abra o Serviço Aprovisionamento de Dispositivos.

2. No painel de resumo do Serviço Aprovisionamento de Dispositivos, selecione **Gerir inscrições**. Selecione o separador **Inscrições Individuais** ou o separador **Grupos de Inscrição** de acordo com a configuração do dispositivo. Clique no botão **+ Adicionar**, na parte superior. Selecione **TPM** ou **X.509** como o *Mecanismo* de atestação de identidade e introduza os artefactos de segurança adequados, conforme discutido anteriormente. Pode introduzir um **ID de dispositivo Hub IoT** novo. Quando tiver terminado, clique no botão **Guardar**. 

3. Quando o dispositivo for inscrito com êxito, deverá ver que é apresentado no portal do seguinte modo:

    ![Inscrição de TPM bem-sucedida no portal](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)

Após a inscrição, o Serviço Aprovisionamento aguarda que o dispositivo seja arrancado e se ligue ao mesmo num momento posterior. Da primeira vez que o dispositivo for arrancado, a biblioteca do SDK de clientes interage com o seu chip para extrair os artefactos de segurança do dispositivo e verifica o registo no Serviço Aprovisionamento de Dispositivos. 

## <a name="start-the-iot-device"></a>Iniciar o dispositivo IoT

O dispositivo IoT pode ser um dispositivo real ou um dispositivo simulado. Uma vez que o dispositivo IoT está agora inscrito numa instância do Serviço de Aprovisionamento de Dispositivos, o dispositivo pode arrancar e chamar o serviço de aprovisionamento para ser reconhecido através do mecanismo de atestado. Assim que o serviço de aprovisionamento reconhecer o dispositivo, será atribuído a um hub IoT. 

Estão incluídos exemplos de dispositivos simulados, através do atestado de TPM e X.509, para C, Java, C#, Node.js e Python. Por exemplo, um dispositivo simulado com o TPM e o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) seguiria o processo abordado na secção [Simular a primeira sequência de arranque de um dispositivo](quick-create-simulated-device.md#simulate-first-boot-sequence-for-the-device). O mesmo dispositivo com o atestado de certificado X.509 seguiria a secção [Sequência de arranque](quick-create-simulated-device-x509.md#simulate-first-boot-sequence-for-the-device).

Veja o [Guia de procedimentos para o MXChip Iot DevKit](how-to-connect-mxchip-iot-devkit.md) como um exemplo de um dispositivo real.

Inicie o dispositivo para permitir à respetiva aplicação cliente iniciar o registo no Serviço de Aprovisionamento de Dispositivos.  

## <a name="verify-the-device-is-registered"></a>Verificar se o dispositivo está registado

Após o arranque do dispositivo, devem ocorrer as ações seguintes:

1. O dispositivo envia um pedido de registo ao Serviço Aprovisionamento de Dispositivos.
2. Relativamente aos dispositivos TPM, o Serviço Aprovisionamento de Dispositivos devolve um desafio de registo, ao qual o seu dispositivo vai responder. 
3. Após o registo bem-sucedido, o Serviço Aprovisionamento de Dispositivos reenvia o URI do hub IoT, o ID do dispositivo e a chave de encriptação para o dispositivo. 
4. Em seguida, a aplicação cliente do Hub IoT no dispositivo liga-se ao seu hub. 
5. Quando a ligação ao hub for estabelecida com êxito, deverá ver o dispositivo apresentado no explorador de **Dispositivos IoT** do hub IoT. 

    ![Ligação com êxito ao hub no portal](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

Para obter mais informações, consulte o exemplo de cliente para o dispositivo aprovisionamento [prov_dev_client_sample.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c). O exemplo demonstra o aprovisionamento de um dispositivo simulado com o TPM, certificados X.509 e as chaves simétricas. Veja novamente o [TPM](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device), [X.509](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509), e [chave simétrica](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-symm-key) inícios rápidos de atestado para obter instruções passo a passo sobre como utilizar o exemplo.

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Inscrever o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registado

Avance para o próximo tutorial para saber como aprovisionar vários dispositivos em hubs com balanceamento de carga. 

> [!div class="nextstepaction"]
> [Aprovisionar dispositivos em vários hubs IoT com balanceamento de carga](./tutorial-provision-multiple-hubs.md)
