---
title: Gerir inscrições de dispositivos com o portal do Azure | Documentos da Microsoft
description: Como gerir inscrições de dispositivos para o serviço de aprovisionamento de dispositivos no Portal do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 51b072bfd0827528a5504133dff8c1cdd7a7ca86
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089391"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Como gerir inscrições de dispositivos com o Portal do Azure

R *inscrição de dispositivos* cria um registo de um único dispositivo ou um grupo de dispositivos que pode ser em algum momento registado com o serviço de aprovisionamento de dispositivos do Azure IoT Hub. O registo de inscrição contém a configuração inicial pretendida para o dispositivo como parte do que a inscrição, incluindo o hub IoT desejado (s). Este artigo mostra-lhe como gerir inscrições de dispositivos para o serviço de aprovisionamento.


## <a name="create-a-device-enrollment"></a>Criar uma inscrição de dispositivos

Existem duas formas, pode inscrever os seus dispositivos com o serviço de aprovisionamento:

* Uma **grupo de inscrição** é uma entrada para um grupo de dispositivos que partilham um mecanismo de atestado comuns de certificados X.509, assinado pelo certificado de assinatura mesmo, que pode ser o [certificado de raiz](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) ou o [certificado intermédio](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), utilizado para gerar o certificado de dispositivo no dispositivo físico. Recomendamos que utilize um grupo de inscrição para um grande número de dispositivos que partilham uma configuração inicial pretendida ou para dispositivos pertencerão todos ao mesmo inquilino. Tenha em atenção que só pode inscrever dispositivos que utilizam o mecanismo de atestado de X.509 como *grupos de inscrição*. 

    Pode criar um grupo de inscrição no portal para um grupo de dispositivos através dos seguintes passos:

  1. Inicie sessão no portal do Azure e clique em **todos os recursos** no menu esquerdo.  
  1. Clique no serviço de aprovisionamento de dispositivos de mensagens em fila que pretende inscrever o seu dispositivo a partir da lista de recursos.  
  1. No seu serviço de aprovisionamento:  
     a. Clique em **gerir inscrições**, em seguida, selecione a **grupos de inscrição** separador.  
     b. Clique no botão **+ Adicionar**, na parte superior.  
     c. Quando for apresentado o painel "Adicionar grupo de inscrição", introduza as informações para a entrada da lista de inscrição.  **Nome do grupo** é necessária. Também selecionar "AC ou intermediário" para **tipo de certificado**e carregar a raiz **certificado primário** para o grupo de dispositivos.  
     d. Clique em **Guardar**. A criação do seu grupo de inscrição com êxito, deverá ver o nome do grupo aparecer sob o **grupos de inscrição** separador.  

     [![Grupo de inscrição no portal](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* Uma **inscrição Individual** é uma entrada para um único dispositivo que pode ser registado. Inscrições individuais podem utilizar x509 certificados ou tokens SAS (a partir de um TPM físico ou virtual) como mecanismos de atestação. Recomendamos a utilização das inscrições individuais para dispositivos que precisam de configurações iniciais exclusivas ou para dispositivos que só podem utilizar tokens SAS através de TPM ou de virtual TPM como o mecanismo de atestação. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

    Pode criar uma inscrição individual no portal utilizando os seguintes passos:

    1. Inicie sessão no portal do Azure e clique em **todos os recursos** no menu esquerdo.
    1. Clique no serviço de aprovisionamento de dispositivos de mensagens em fila que pretende inscrever o seu dispositivo a partir da lista de recursos.
    1. No seu serviço de aprovisionamento:  
       a. Clique em **gerir inscrições**, em seguida, selecione a **inscrições individuais** separador.  
       b. Clique no botão **+ Adicionar**, na parte superior.   
       c. Quando for apresentado o painel "Adicionar inscrição", introduza as informações para a entrada da lista de inscrição. Primeiro, selecione o atestado **mecanismo** para o dispositivo (X.509 ou TPM). Atestado de X.509 necessita que carregue a folha **certificado primário** para o dispositivo. TPM requer que introduza o **atestado de chave** e **ID de registo** para o dispositivo.  
       d. Clique em **Guardar**. A criação do seu grupo de inscrição com êxito, deverá ver o dispositivo aparecer sob o **inscrições individuais** separador.  

       [![Inscrição individual no portal](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Atualizar uma entrada de inscrição
Pode atualizar uma entrada de inscrição existente no portal utilizando os seguintes passos:

1. Abra o serviço de aprovisionamento de dispositivos no portal do Azure e clique em **gerir inscrições**. 
1. Navegue para a entrada de inscrição que pretende modificar. Clique na entrada, que abre uma informações de resumo sobre a inscrição de dispositivos. 
1. Nesta página, pode modificar os itens que não seja o tipo de segurança e credenciais, tal como o IoT hub do dispositivo devem ser ligadas a, bem como o ID de dispositivo. Também pode modificar o estado de twin do dispositivo inicial. 
1. Depois de concluído, clique em **guardar** para atualizar a sua inscrição de dispositivos. 

    ![Atualizar a inscrição no portal](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Remover uma inscrição de dispositivos
Em casos em que seu dispositivo ou dispositivos não precisam de ser aprovisionada para qualquer hub de IoT, pode remover a entrada de inscrição relacionado no portal utilizando os seguintes passos:

1. Abra o serviço de aprovisionamento de dispositivos no portal do Azure e clique em **gerir inscrições**. 
1. Navegue para e selecione a entrada de inscrição que pretende remover. 
1. Clique nas **elimine** na parte superior e, em seguida, selecione **Sim** quando lhe for pedido para confirmar. 
1. Depois de concluída a ação, verá sua participação removida da lista de inscrições de dispositivos. 
 
    ![Remover inscrição no portal](./media/how-to-manage-enrollments/remove-enrollment.png)


