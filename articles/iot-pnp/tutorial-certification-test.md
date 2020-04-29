---
title: Certifique o seu dispositivo de pré-visualização IoT Plug e Play Preview / Microsoft Docs
description: Este tutorial descreve como adicionar as informações do seu produto ao catálogo de dispositivos Azure Certified para ioT, ligar o seu dispositivo ao serviço de certificação Azure IoT e, em seguida, executar os testes de certificação IoT Plug e Play.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 12/27/2019
ms.openlocfilehash: dc2c33659f3f3a3df0f11fcc6ab36a9fc993da43
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75550167"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>Tutorial: Certifique o seu dispositivo de pré-visualização IoT Plug e Play

Para publicar um dispositivo IoT Plug e Play Preview no catálogo de [dispositivos Azure Certified para ioT,](https://aka.ms/iotdevcat)deve passar num conjunto de testes de certificação. Utilize o portal [Azure Certified para ioT](https://aka.ms/ACFI) para submeter o seu dispositivo para certificação. O serviço de [certificação Azure IoT](https://aka.ms/azure-iot-aics) executa os testes de certificação.

Neste tutorial, aprende-se:

> [!div class="checklist"]
> * Quais são os requisitos de certificação IoT Plug e Play.
> * Como adicionar o nome do seu produto e informação ao portal.
> * Como ligar e descobrir interfaces IoT Plug e Play.
> * Como rever as interfaces IoT Plug e Play e executar testes de certificação.
> * Como publicar o dispositivo IoT Plug e Play certificado para o catálogo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Uma conta do Microsoft Partner Center.
* O ID da rede do Parceiro Microsoft.

Para mais informações, consulte [Como embarcar no portal Azure Certified para IoT.](howto-onboard-portal.md)

## <a name="certification-requirements"></a>Requisitos de certificação

Para certificar o seu dispositivo IoT Plug and Play, o seu dispositivo deve satisfazer os seguintes requisitos:

* O seu código de dispositivo IoT Plug and Play deve ser instalado no seu dispositivo.
* O seu código de dispositivo IoT Plug and Play é construído com o SDK Azure IoT.
* O seu código de dispositivo deve suportar o Serviço de Provisionamento de [Dispositivos Hub Azure IoT](../iot-dps/about-iot-dps.md).
* O código do dispositivo deve implementar a Interface de [Informação](concepts-common-interfaces.md)do Dispositivo .
* O modelo de capacidade e o código do dispositivo funcionam com a IoT Central.

Todos os dispositivos atualmente no catálogo são considerados dispositivos IoT Plug e Play pré-certificados. Não garante a qualidade e a conformidade do produto final dos componentes do software IoT Plug and Play, como o SDK e o Digital Twin Definition Language.

Todos os dispositivos IoT Plug and Play pré-certificados devem retificar a disponibilidade geral de IoT Plug and Play com base na versão final dos requisitos de certificação e dos componentes de software fornecidos pela Microsoft.

## <a name="add-product-name"></a>Adicionar nome do produto

**O produto** é um nome amigável para um modelo de produto que um cliente pode comprar. Para adicionar um produto:

1. Inscreva-se no [Portal Azure Certified para IoT.](https://aka.ms/ACFI)
1. Vá à página **produtos** no portal a partir do menu esquerdo e selecione **+ Novo**.
1. Introduza o seu nome de produto amigável e selecione **Criar**. O nome aqui introduzido é diferente do nome apresentado no catálogo do dispositivo.

## <a name="add-product-information"></a>Adicionar informações sobre o produto

Depois de ter criado com sucesso o produto no portal, o produto é apresentado na página **Produtos.** Para adicionar as informações do produto:

1. Selecione a ligação de produto criada na página do **Produto** na coluna do produto. O Estado deve estar em estado de recrutamento.
1. Selecione o link **Editar** ao lado da rubrica **de informações do Produto.** Introduza informações sobre o seu produto na página de informação do produto e certifique-se de que completa todos os campos necessários.
1. Selecione **Guardar**. O botão **Guardar** só aparece quando completar todos os campos necessários. Se os campos estiverem incompletos, o botão diz **"Guardar e terminar mais tarde**" .
1. Reveja o conteúdo que inseriu. Complete todos os campos necessários para publicar o dispositivo no catálogo do dispositivo. Pode sempre voltar a fazer edites na página de detalhes do produto, clicando no link de **edição** ao lado da rubrica de informação do **Produto.**

## <a name="connect-and-discover-interfaces"></a>Ligar e descobrir interfaces

Para executar os testes de certificação, ligue o seu dispositivo ao serviço de [certificação Azure IoT](https://aka.ms/azure-iot-aics) (AICS) que está disponível no portal.

Estes passos são um passo de tempo para a execução de testes de certificação e não é necessário alterar o código do seu dispositivo de produto. Para começar, siga estes passos para ligar ao AICS:

1. Inscreva-se no portal utilizando a sua conta Partner Center.
1. Clique no **teste Connect +** para iniciar o fluxo de certificação.
1. Escolha o método de [autenticação](../iot-dps/concepts-security.md#attestation-mechanism) para fornecer o seu dispositivo à AICS utilizando o Serviço de Provisionamento de [Dispositivos Hub Azure IoT](../iot-dps/about-iot-dps.md).
   * Se estiver a utilizar um [certificado X.509,](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites)carregue o certificado X.509 gerado. É melhor rever o código da amostra que mostra como utilizar certificados X.509: [C,](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c) [C#](../iot-hub/iot-hub-security-x509-get-started.md).
   * Se estiver a utilizar uma [chave simétrica,](../iot-dps/concepts-symmetric-key-attestation.md)copie e cole a chave simétrica no código do seu dispositivo.
   * O método de autenticação TPM não é suportado neste momento.
1. Copie e cole as seguintes iDs geradas no código do seu dispositivo.
   * [ID de registo](../iot-dps/use-hsm-with-sdk.md)
   * [DPS ID](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [Ponto final dPS](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. Quando o código do dispositivo for construído e implantado no dispositivo, selecione **Connect** para descobrir as interfaces IoT Plug e Play.
1. Quando a ligação ao AICS for bem sucedida, selecione **Next** para rever as interfaces IoT Plug e Play.

## <a name="run-tests-and-publish-the-device"></a>Executar testes e publicar o dispositivo

Na página de revisão, pode rever as interfaces IoT Plug e Play descobertas. Utilize esta página para verificar corretamente os primitivos implementados no visor da interface. Também pode verificar a localização da interface.

1. Certifique-se de que as inputs de carga útil são introduzidas para os campos necessários. Estes campos incluem informações de carga útil para o comando primitivo para a interface especificada.
1. Quando tiver introduzido todas as informações necessárias, selecione **Next**.
1. Para executar os testes para as interfaces ioT plug e play implementados, selecione **Testes de execução**.
1. Todos os testes são executados automaticamente. Se algum teste falhar, selecione **registos de visualização** para visualizar as mensagens de erro da AICS e a telemetria bruta enviada para o Hub Azure IoT.
1. Para completar os testes de certificação, selecione **Finish**.
1. Publique o dispositivo IoT Plug and Play certificado para o catálogo. Para adicionar o dispositivo certificado ao catálogo, selecione **Adicionar para catalogar** na barra de ferramentas. Se o **catálogo Adicionar ao catálogo** estiver acinzentado, significa que ou as informações do produto estão incompletas ou os testes falharam. 
1. Selecione o link "CERTIFIED And IN THE CATALOG" para ver o seu dispositivo publicado no catálogo do dispositivo.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a certificar o dispositivo IoT Plug and Play, o próximo passo sugerido é aprender mais sobre os modelos de capacidade de gestão:

> [!div class="nextstepaction"]
> [Gerir modelos](./howto-manage-models.md)
