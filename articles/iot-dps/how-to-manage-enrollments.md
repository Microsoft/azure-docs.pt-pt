---
title: Gerenciar registros de dispositivo para o serviço de provisionamento de dispositivos no Hub IoT do Azure no portal do Azure
description: Como gerenciar registros de dispositivo para seu DPS (serviço de provisionamento de dispositivos) no portal do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 8bd896573dbd0b24df11c0d66009098de19654ac
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974943"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Como gerenciar registros de dispositivo com o portal do Azure

Um *registro de dispositivo* cria um registro de um único dispositivo ou um grupo de dispositivos que podem, em algum momento, ser registrados com o serviço de provisionamento de dispositivos no Hub IOT do Azure. O registro de registro contém a configuração inicial desejada para os dispositivos como parte desse registro, incluindo o Hub IoT desejado. Este artigo mostra como gerenciar registros de dispositivo para o serviço de provisionamento.


## <a name="create-a-device-enrollment"></a>Criar um registro de dispositivo

Há duas maneiras de registrar seus dispositivos com o serviço de provisionamento:

* Um **grupo de registro** é uma entrada para um grupo de dispositivos que compartilham um mecanismo de atestado comum de certificados X. 509, assinados pelo mesmo certificado de autenticação, que pode ser o [certificado raiz](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) ou o [certificado intermediário](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), usado para produzir o certificado de dispositivo no dispositivo físico. É recomendável usar um grupo de registro para um grande número de dispositivos que compartilham uma configuração inicial desejada ou para dispositivos que todos vão para o mesmo locatário. Observe que você só pode registrar dispositivos que usam o mecanismo de atestado X. 509 como *grupos de registro*. 

    Você pode criar um grupo de registros no portal para um grupo de dispositivos usando as seguintes etapas:

  1. Faça logon no portal do Azure e clique em **todos os recursos** no menu à esquerda.  
  1. Clique no serviço de provisionamento de dispositivos no qual você deseja registrar o dispositivo na lista de recursos.  
  1. Em seu serviço de provisionamento:  
     a. Clique em **gerenciar registros**e, em seguida, selecione a guia **grupos de registros** .  
     b. Clique no botão **+ Adicionar**, na parte superior.  
     c. Quando o painel "Adicionar grupo de registro" for exibido, insira as informações para a entrada da lista de registro.  O **nome do grupo** é obrigatório. Selecione também "CA ou intermediário" para o **tipo de certificado**e carregue o **certificado primário** raiz para o grupo de dispositivos.  
     d. Clique em **Guardar**. Após a criação bem-sucedida de seu grupo de registro, você deverá ver o nome do grupo aparecer na guia **grupos de registro** .  

     [![grupo de registro no portal](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* Um **registro individual** é uma entrada para um único dispositivo que pode ser registrado. Registros individuais podem usar certificados X509 ou tokens SAS (de um TPM físico ou virtual) como mecanismos de atestado. É recomendável usar registros individuais para dispositivos que exigem configurações iniciais exclusivas ou para dispositivos que só podem usar tokens SAS via TPM ou TPM virtual como o mecanismo de atestado. As inscrições individuais podem ter o ID de dispositivo do hub IoT pretendido especificado.

    Você pode criar um registro individual no portal usando as seguintes etapas:

    1. Faça logon no portal do Azure e clique em **todos os recursos** no menu à esquerda.
    1. Clique no serviço de provisionamento de dispositivos no qual você deseja registrar o dispositivo na lista de recursos.
    1. Em seu serviço de provisionamento:  
       a. Clique em **gerenciar registros**e, em seguida, selecione a guia **registros individuais** .  
       b. Clique no botão **+ Adicionar**, na parte superior.   
       c. Quando o painel "adicionar registro" for exibido, insira as informações para a entrada da lista de registro. Primeiro, selecione o **mecanismo** de atestado para o dispositivo (X. 509 ou TPM). O atestado X. 509 exige que você carregue o **certificado principal** de folha para o dispositivo. O TPM exige que você insira a **chave de atestado** e a ID de **registro** do dispositivo.  
       d. Clique em **Guardar**. Após a criação bem-sucedida de seu grupo de registro, você deverá ver seu dispositivo aparecer na guia **registros individuais** .  

       [![registro individual no portal](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Atualizar uma entrada de registro
Você pode atualizar uma entrada de registro existente no portal usando as seguintes etapas:

1. Abra o serviço de provisionamento de dispositivos no portal do Azure e clique em **gerenciar registros**. 
1. Navegue até a entrada de registro que você deseja modificar. Clique na entrada, que abre uma informação de resumo sobre o registro do dispositivo. 
1. Nessa página, você pode modificar os itens que não sejam o tipo de segurança e as credenciais, como o Hub IoT ao qual o dispositivo deve ser vinculado, bem como a ID do dispositivo. Você também pode modificar o estado inicial do dispositivo. 
1. Depois de concluído, clique em **salvar** para atualizar o registro do dispositivo. 

    ![Atualizar o registro no portal](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Remover um registro de dispositivo
Nos casos em que os dispositivos não precisam ser provisionados para nenhum Hub IoT, você pode remover a entrada de registro relacionada no portal usando as seguintes etapas:

1. Abra o serviço de provisionamento de dispositivos no portal do Azure e clique em **gerenciar registros**. 
1. Navegue até e selecione a entrada de registro que você deseja remover. 
1. Clique no botão **excluir** na parte superior e, em seguida, selecione **Sim** quando for solicitado a confirmar. 
1. Quando a ação for concluída, você verá sua entrada removida da lista de registros de dispositivo. 
 
    ![Remover o registro no portal](./media/how-to-manage-enrollments/remove-enrollment.png)


