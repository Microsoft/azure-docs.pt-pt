---
title: Gerir as inscrições para dispositivos para o Serviço de Provisionamento de Dispositivos Azure IoT Hub no portal Azure
description: Como gerir as matrículas do dispositivo para o seu Serviço de Provisionamento de Dispositivos (DPS) no Portal Azure
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 8bd896573dbd0b24df11c0d66009098de19654ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974943"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Como gerir as matrículas do dispositivo com o Portal Azure

Uma *inscrição* de dispositivo cria um registo de um único dispositivo ou de um grupo de dispositivos que podem, em algum momento, registar-se no Serviço de Provisionamento de Dispositivos Azure IoT Hub. O registo de inscrição contém a configuração inicial desejada para o(s) dispositivo(s) como parte dessa inscrição, incluindo o centro IoT desejado. Este artigo mostra-lhe como gerir as matrículas do dispositivo para o seu serviço de provisionamento.


## <a name="create-a-device-enrollment"></a>Criar uma inscrição de dispositivo

Existem duas formas de inscrever os seus dispositivos no serviço de provisionamento:

* Um grupo de **inscrições** é uma entrada para um grupo de dispositivos que partilham um mecanismo comum de atestado de certificados X.509, assinado saem pelo mesmo certificado de assinatura, que pode ser o [certificado de raiz](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) ou o certificado [intermédio,](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)utilizado para a produção de certificado de dispositivo em dispositivo físico. Recomendamos a utilização de um grupo de inscrições para um grande número de dispositivos que partilhem uma configuração inicial desejada, ou para dispositivos que vão todos para o mesmo inquilino. Note que só pode inscrever dispositivos que utilizem o mecanismo de atesta X.509 como *grupos de inscrição*. 

    Pode criar um grupo de inscrições no portal para um grupo de dispositivos utilizando os seguintes passos:

  1. Inicie sessão no portal Azure e clique em **todos os recursos** do menu à esquerda.  
  1. Clique no serviço de fornecimento de dispositivos que pretende inscrever o seu dispositivo a partir da lista de recursos.  
  1. No seu serviço de provisionamento:  
     a. Clique em **Gerir as matrículas**e, em seguida, selecione o separador **Grupos de Matrículas.**  
     b. Clique no botão **+ Adicionar**, na parte superior.  
     c. Quando aparecer o painel "Adicionar Grupo de Matrículas", introduza as informações para a inscrição na lista de inscrições.  **O nome** do grupo é necessário. Selecione também "CA ou Intermediate" para **o tipo de Certificado,** e carregue o **certificado primário** de raiz para o grupo de dispositivos.  
     d. Clique em **Guardar**. Na criação bem-sucedida do seu grupo de inscrições, deve ver o nome de grupo aparecer no separador **Grupos de Inscrição.**  

     [![Grupo de inscrições no portal](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* Uma **inscrição individual** é uma entrada para um único dispositivo que pode registar-se. As matrículas individuais podem utilizar certificados x509 ou tokens SAS (de um TPM físico ou virtual) como mecanismos de atestado. Recomendamos a utilização de inscrições individuais para dispositivos que requeiram configurações iniciais únicas, ou para dispositivos que só podem utilizar tokens SAS via TPM ou TPM virtual como mecanismo de atestado. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

    Pode criar uma inscrição individual no portal utilizando os seguintes passos:

    1. Inicie sessão no portal Azure e clique em **todos os recursos** do menu à esquerda.
    1. Clique no serviço de fornecimento de dispositivos que pretende inscrever o seu dispositivo a partir da lista de recursos.
    1. No seu serviço de provisionamento:  
       a. Clique em **Gerir as matrículas**e, em seguida, selecione o separador **Individual Registrations.**  
       b. Clique no botão **+ Adicionar**, na parte superior.   
       c. Quando aparecer o painel "Adicionar Inscrição", introduza as informações para a inscrição na lista de inscrições. Primeiro selecione o **Mecanismo** de Aveia para o dispositivo (X.509 ou TPM). O atestado X.509 requer que carregue o **certificado primário** de folha para o dispositivo. A TPM exige que introduza a Chave de **Attestation** e o **ID de Registo** do dispositivo.  
       d. Clique em **Guardar**. Ao conseguir a criação do seu grupo de inscrições, deve ver o seu dispositivo aparecer sob o separador **Individual Registrations.**  

       [![Inscrição individual no portal](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Atualizar uma entrada de inscrição
Pode atualizar uma entrada de inscrição existente no portal utilizando os seguintes passos:

1. Abra o seu serviço de provisionamento de dispositivos no portal Azure e clique em **Gerir as Matrículas**. 
1. Navegue para a entrada de inscrição que pretende modificar. Clique na entrada, que abre uma informação sumária sobre a inscrição do seu dispositivo. 
1. Nesta página, pode modificar itens que não o tipo de segurança e credenciais, como o hub IoT a que o dispositivo deve ser ligado, bem como o ID do dispositivo. Também pode modificar o estado gémeo do dispositivo inicial. 
1. Uma vez concluída, clique em **Guardar** para atualizar a inscrição do dispositivo. 

    ![Atualizar a inscrição no portal](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Remover uma inscrição de um dispositivo
Nos casos em que o seu(s) dispositivo(s) não precise de ser aprovisionado em qualquer hub IoT, pode remover a entrada de inscrição relacionada no portal utilizando os seguintes passos:

1. Abra o seu serviço de provisionamento de dispositivos no portal Azure e clique em **Gerir as Matrículas**. 
1. Navegue e selecione a entrada de inscrição que pretende remover. 
1. Clique no botão **Eliminar** na parte superior e, em seguida, selecione **Sim** quando solicitado para confirmar. 
1. Uma vez concluída a ação, verá a sua entrada removida da lista de inscrições do dispositivo. 
 
    ![Remover a inscrição no portal](./media/how-to-manage-enrollments/remove-enrollment.png)


