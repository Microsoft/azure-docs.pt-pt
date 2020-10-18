---
title: Gerir as inscrições de dispositivos para o Serviço de Provisionamento de Dispositivos Azure IoT Hub no portal Azure
description: Como gerir as inscrições de dispositivos para o seu Serviço de Provisionamento de Dispositivos (DPS) no Portal Azure
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 2fbcacd7f2094f9b0b9dcea3fea4d804fd96923e
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165320"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Como gerir as inscrições de dispositivos com o Portal Azure

Uma *inscrição de dispositivo* cria um registo de um único dispositivo ou de um grupo de dispositivos que podem em algum momento registar-se com o Serviço de Provisionamento de Dispositivos Azure IoT Hub. O registo de inscrição contém a configuração inicial desejada para o(s) dispositivo(s) como parte dessa inscrição, incluindo o hub IoT desejado. Este artigo mostra-lhe como gerir as inscrições de dispositivos para o seu serviço de fornecimento.


## <a name="create-a-device-enrollment"></a>Criar uma inscrição de dispositivo

Existem duas formas de inscrever os seus dispositivos com o serviço de fornecimento:

* Um **grupo de inscrições** é uma entrada para um grupo de dispositivos que partilham um mecanismo comum de atestado de certificados X.509, assinados pelo mesmo certificado de assinatura, que pode ser o [certificado raiz](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) ou o [certificado intermédio,](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)utilizado para produzir certificado de dispositivo em dispositivo físico. Recomendamos a utilização de um grupo de inscrições para um grande número de dispositivos que partilham uma configuração inicial desejada, ou para dispositivos que vão todos para o mesmo inquilino. 

    Pode criar um grupo de inscrições no portal para um grupo de dispositivos utilizando os seguintes passos:

  1. Faça login no portal Azure e clique em **Todos os recursos** do menu da esquerda.  
  1. Clique no serviço de Provisionamento de Dispositivos que pretende inscrever o seu dispositivo a partir da lista de recursos.  
  1. No seu serviço de fornecimento:  
     a. Clique **em Gerir as inscrições**e, em seguida, selecione o **separador Grupos de Inscrição.**  
     b. Clique no botão **Adicionar** na parte superior.  
     c. Quando aparecer o painel "Adicionar Grupo de Inscrição", insira as informações para a inscrição na lista de inscrições.  **É** necessário o nome do grupo. Selecione também "CA ou Intermediário" para **o tipo certificado**, e carrede o certificado **primário** de raiz para o grupo de dispositivos.  
     d. Clique em **Guardar**. Na criação bem sucedida do seu grupo de inscrição, deverá ver o nome do grupo aparecer no separador **Grupos de Inscrição.**  

     [![Grupo de inscrições no portal](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* Uma **inscrição individual** é uma entrada para um único dispositivo que pode registar-se. As inscrições individuais podem utilizar certificados x509 ou fichas SAS (a partir de um TPM físico ou virtual) como mecanismos de atestação. Recomendamos a utilização de inscrições individuais para dispositivos que exijam configurações iniciais únicas, ou para dispositivos que só podem usar fichas SAS via TPM ou TPM virtual como mecanismo de atestado. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

    Pode criar uma inscrição individual no portal utilizando os seguintes passos:

    1. Faça login no portal Azure e clique em **Todos os recursos** do menu da esquerda.
    1. Clique no serviço de Provisionamento de Dispositivos que pretende inscrever o seu dispositivo a partir da lista de recursos.
    1. No seu serviço de fornecimento:  
       a. Clique **em Gerir as inscrições**e, em seguida, selecione o **separador Inscrições Individuais.**  
       b. Clique no botão **Adicionar** na parte superior.   
       c. Quando aparecer o painel "Adicionar Inscrição", insira as informações para a inscrição na lista. Selecione primeiro o **Mecanismo** de Atestação para o dispositivo (X.509 ou TPM). O atestado X.509 requer que faça o upload do **certificado primário** da folha para o dispositivo. O TPM requer que introduza a **Chave de Atestação** e **o ID de registo** para o dispositivo.  
       d. Clique em **Guardar**. Na criação bem sucedida do seu grupo de inscrição, deverá ver o seu dispositivo aparecer no separador **Inscrições Individuais.**  

       [![Inscrição individual no portal](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Atualizar uma entrada de inscrição
Pode atualizar uma inscrição existente no portal utilizando os seguintes passos:

1. Abra o seu serviço de Provisionamento de Dispositivos no portal Azure e clique em **Gerir Inscrições**. 
1. Navegue para a entrada de inscrição que pretende modificar. Clique na entrada, que abre uma informação sumária sobre a inscrição do seu dispositivo. 
1. Nesta página, pode modificar itens que não sejam o tipo de segurança e credenciais, como o hub IoT a que o dispositivo deve estar ligado, bem como o ID do dispositivo. Também pode modificar o estado gémeo do dispositivo inicial. 
1. Uma vez concluído, clique em **Guardar** para atualizar a inscrição do seu dispositivo. 

    ![Atualizar inscrições no portal](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Remover uma inscrição de dispositivo
Nos casos em que o(s) dispositivo(s) não necessite de ser a provisionado em qualquer hub IoT, pode remover a inscrição relacionada no portal utilizando os seguintes passos:

1. Abra o seu serviço de Provisionamento de Dispositivos no portal Azure e clique em **Gerir Inscrições**. 
1. Navegue e selecione a entrada de inscrição que pretende remover. 
1. Clique no botão **Eliminar** na parte superior e, em seguida, selecione **Sim** quando solicitado para confirmar. 
1. Assim que a ação estiver concluída, verá a sua entrada removida da lista de inscrições do dispositivo. 
 
    ![Remover inscrição no portal](./media/how-to-manage-enrollments/remove-enrollment.png)


