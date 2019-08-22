---
title: Certificar seu dispositivo de visualização de Plug and Play de IoT | Microsoft Docs
description: Este tutorial descreve como adicionar as informações do produto ao catálogo de dispositivos do Azure Certified para IoT, conectar seu dispositivo ao serviço de certificação IoT do Azure e executar os testes de certificação de Plug and Play de IoT.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 06/21/2019
ms.openlocfilehash: 1516a132372a81d06d82de2409c48220f27b8d87
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878083"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>Tutorial: Certificar seu dispositivo de visualização de Plug and Play de IoT

Para publicar um dispositivo de visualização de IoT Plug and Play no [Catálogo de dispositivos certificado pelo Azure para IOT](https://aka.ms/iotdevcat), ele deve passar por um conjunto de testes de certificação. Use o portal do [Azure Certified para IOT](https://aka.ms/ACFI) para enviar seu dispositivo para certificação. O [serviço de certificação IOT do Azure](https://aka.ms/azure-iot-aics) executa os testes de certificação.

Neste tutorial, você aprende:

> [!div class="checklist"]
> * Quais são os requisitos de certificação de Plug and Play de IoT.
> * Como adicionar o nome do produto e as informações ao Portal.
> * Como conectar e descobrir interfaces de Plug and Play de IoT.
> * Como examinar as interfaces de Plug and Play IoT e executar testes de certificação.
> * Como publicar o dispositivo de Plug and Play de IoT certificado para o catálogo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Uma conta do Microsoft Partner Center.
* A ID do Microsoft Partner Network.

Para obter mais informações, consulte [como carregar o portal do Azure Certified para IOT](howto-onboard-portal.md) .

## <a name="certification-requirements"></a>Requisitos de certificação

Para certificar seu dispositivo de Plug and Play de IoT, seu dispositivo deve atender aos seguintes requisitos:

* O código do dispositivo de Plug and Play de IoT deve ser instalado em seu dispositivo.
* O código do dispositivo de Plug and Play de IoT é criado com o SDK do Azure IoT.
* O código do dispositivo deve dar suporte ao [serviço de provisionamento de dispositivos no Hub IOT do Azure](../iot-dps/about-iot-dps.md).
* O código do dispositivo deve implementar a [interface de informações do dispositivo](concepts-common-interfaces.md).
* O modelo de funcionalidade e o código do dispositivo funcionam com IoT Central.

Todos os dispositivos atualmente no catálogo são considerados dispositivos IoT Plug and Play pré-certificados. Ele não garante a qualidade e a conformidade do produto final de componentes de software de IoT Plug and Play, como SDK e a linguagem de definição de alta qualidade digital.

Todos os dispositivos de Plug and Play IoT pré-certificados devem recertificar-se da disponibilidade geral do Plug and Play de IoT com base na versão final dos requisitos de certificação e componentes de software fornecidos pela Microsoft.

## <a name="add-product-name"></a>Adicionar nome do produto

O **produto** é um nome amigável para um modelo de produto que um cliente pode comprar. Para adicionar um produto:

1. Entre no portal do [Azure Certified para IOT](https://aka.ms/ACFI) .
1. Vá para a página **produtos** no portal no menu à esquerda e selecione **+ novo**.
1. Insira o nome amigável do produto e selecione **criar**. O nome digitado aqui é diferente do nome exibido no catálogo do dispositivo.

## <a name="add-product-information"></a>Adicionar informações do produto

Depois de criar com êxito o produto no portal, o produto será exibido na página **produtos** . Para adicionar as informações do produto:

1. Selecione o link produto criado localizado na página **produto** na coluna produto. O estado deve estar no estado de rascunho.
1. Selecione o link **Editar** ao lado do título **informações do produto** . Insira informações sobre seu produto na página informações do produto e verifique se você concluiu todos os campos obrigatórios.
1. Selecione **Guardar**. O botão **salvar** só aparece quando você preenche todos os campos obrigatórios. Se os campos estiverem incompletos, o botão indica **salvar e concluir mais tarde**.
1. Examine o conteúdo que você inseriu. Preencha todos os campos obrigatórios para publicar o dispositivo no catálogo de dispositivos. Você sempre pode voltar para fazer edições nas informações do produto na página de detalhes do produto clicando no link **Editar** ao lado do título **informações do produto** .

## <a name="connect-and-discover-interfaces"></a>Conectar e descobrir interfaces

Para executar os testes de certificação, conecte seu dispositivo ao [serviço de certificação IOT do Azure](https://aka.ms/azure-iot-aics) (AICS) que está disponível no Portal.

Essas etapas são uma etapa única para executar testes de certificação e não é necessário alterar o código de dispositivo do produto. Para iniciar, siga estas etapas para se conectar ao AICS:

1. Entre no portal usando sua conta do Partner Center.
1. Clique em **conectar + teste** para iniciar o fluxo de certificação.
1. Escolha o [método de autenticação](../iot-dps/concepts-security.md#attestation-mechanism) para provisionar seu dispositivo para AICS usando o [serviço de provisionamento de dispositivos no Hub IOT do Azure](../iot-dps/about-iot-dps.md).
   * Se você estiver usando um [certificado x. 509](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites), carregue seu certificado x. 509 gerado. Talvez você queira examinar o código de exemplo que mostra como usar os certificados X. 509: [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c), [C#](../iot-hub/iot-hub-security-x509-get-started.md).
   * Se você estiver usando uma [chave simétrica](../iot-dps/concepts-symmetric-key-attestation.md), copie e cole a chave simétrica em seu código de dispositivo.
   * Não há suporte para o método de autenticação do TPM no momento.
1. Copie e cole as seguintes IDs geradas em seu código de dispositivo.
   * [ID de registro](../iot-dps/use-hsm-with-sdk.md)
   * [ID DO DPS](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [Ponto de extremidade de DPS](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. Quando o código do dispositivo for criado e implantado no dispositivo, selecione **conectar** para descobrir as interfaces de plug and Play de IOT.
1. Quando a conexão com o AICS for bem-sucedida, selecione **Avançar** para examinar as interfaces de plug and Play IOT descobertas.

## <a name="run-tests-and-publish-the-device"></a>Executar testes e publicar o dispositivo

Na página revisão, você pode examinar as interfaces de Plug and Play IoT descobertas. Use esta página para verificar se os primitivos implementados na interface são exibidos corretamente. Você também pode verificar o local da interface.

1. Certifique-se de que as entradas de carga sejam inseridas para os campos obrigatórios. Esses campos incluem informações de carga para o comando primitivo para a interface especificada.
1. Quando você tiver inserido todas as informações necessárias, selecione **Avançar**.
1. Para executar os testes para as interfaces de Plug and Play de IoT implementadas, selecione **executar testes**.
1. Todos os testes são executados automaticamente. Se algum teste falhar, selecione **Exibir logs** para exibir as mensagens de erro de AICS e a telemetria bruta enviada ao Hub IOT do Azure.
1. Para concluir os testes de certificação, selecione **concluir**.
1. Publique o dispositivo de Plug and Play de IoT certificado para o catálogo. Para adicionar o dispositivo certificado ao catálogo, selecione **Adicionar ao catálogo** na barra de ferramentas. Se a opção **Adicionar ao catálogo** estiver esmaecida, significa que as informações do produto estão incompletas ou os testes falharam. 
1. Selecione o link "certificado e no catálogo" para exibir o dispositivo publicado no catálogo do dispositivo.

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu sobre a certificação de Plug and Play dispositivo IoT, a próxima etapa sugerida é aprender mais sobre o gerenciamento de modelos de recursos:

> [!div class="nextstepaction"]
> [Gerenciar modelos](./howto-manage-models.md)
