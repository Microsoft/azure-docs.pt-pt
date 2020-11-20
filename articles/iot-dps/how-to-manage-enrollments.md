---
title: Gerir as inscrições de dispositivos para o Serviço de Provisionamento de Dispositivos Azure IoT Hub no portal Azure
description: Como gerir as inscrições de dispositivos para o seu Serviço de Provisionamento de Dispositivos (DPS) no portal Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: 6ec146a05df1b896f8ca594d29cf13341b70765a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954567"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Como gerir as inscrições de dispositivos com o portal Azure

Uma *inscrição de dispositivo* cria um registo de um único dispositivo ou de um grupo de dispositivos que podem em algum momento registar-se com o Serviço de Provisionamento de Dispositivos Azure IoT Hub. O registo de inscrição contém a configuração inicial para o(s) dispositivo(s) como parte dessa inscrição. Incluído na configuração está o hub IoT a que será atribuído um dispositivo ou uma política de atribuição que configura o hub a partir de um conjunto de hubs. Este artigo mostra-lhe como gerir as inscrições de dispositivos para o seu serviço de fornecimento.


## <a name="create-a-device-enrollment"></a>Criar uma inscrição de dispositivo

Existem duas formas de inscrever os seus dispositivos com o serviço de fornecimento:

* Um **grupo de inscrições** é uma entrada para um grupo de dispositivos que partilham um mecanismo comum de atestado. Recomendamos a utilização de um grupo de inscrições para um grande número de dispositivos que partilham uma configuração inicial, ou para dispositivos que vão todos para o mesmo inquilino. Os dispositivos que utilizam [a chave simétrica](concepts-symmetric-key-attestation.md) ou [os certificados X.509 são suportados.](concepts-x509-attestation.md) 

    Para obter instruções passo a passo sobre a criação e utilização de grupos de inscrição com teclas simétricas, consulte os [dispositivos De provisionamento com teclas simétricas](how-to-legacy-device-symm-key.md) tutoriais.

    Cria-se um grupo de inscrições no portal para um grupo de dispositivos utilizando os seguintes passos:

    1. Faça login no portal Azure e clique em **Todos os recursos** do menu da esquerda.  
    1. Clique no serviço de Provisionamento de Dispositivos que pretende inscrever o seu dispositivo a partir da lista de recursos.  
    1. No seu serviço de fornecimento, clique **em Gerir as inscrições** e, em seguida, clique no botão **de grupo de inscrição Add** no topo.  
     
        ![Grupo de inscrições no portal](./media/how-to-manage-enrollments/add-group-enrollment.png)
        
    1. Quando aparecer o painel "Adicionar Grupo de Inscrição", insira as informações para a sua inscrição e clique em **Guardar**.  
     
        [![Adicione um grupo de inscrições com o portal](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
        
        | Campo | Descrição |
        | :--- | :--- |
        | **Nome do grupo** | Nome necessário para o seu grupo de dispositivos. |
        | **Tipo de Attestation** | Clique **em Certificado** ou Chave **Simétrica** para o tipo de atestado, dependendo do método de atestado que os seus dispositivos utilizarão. |
        | **Tipo de Certificado** | Disponível se estiver a utilizar atestado de certificado. selecione **Certificado CA** ou **Intermediário** com base no certificado assinado os certificados do seu dispositivo. |
        | **Certificado Primário** | Se estiver a assinar os certificados do seu dispositivo com um certificado de CA de raiz, esse certificado de CA raiz deve ter [o comprovativo de posse](how-to-verify-certificates.md) preenchido. Em seguida, pode selecioná-lo como **certificado Primário** para o grupo de dispositivos.<br><br>Se estiver a assinar os certificados do seu dispositivo com um certificado intermédio, estará disponível um botão de upload para permitir o upload do seu certificado intermédio. O certificado que assinou o intermediário deve também ter [prova de posse](how-to-verify-certificates.md) preenchida para o mesmo. |

        
    

* Uma **inscrição individual** é uma entrada para um único dispositivo que pode ser atribuído a um hub IoT. Os dispositivos que utilizam [a chave simétrica,](concepts-symmetric-key-attestation.md) [os certificados X.509](concepts-x509-attestation.md)e [o atestado TPM](concepts-tpm-attestation.md) são suportados. 

    Pode criar uma inscrição individual no portal utilizando os seguintes passos:

    1. Faça login no portal Azure e clique em **Todos os recursos** do menu da esquerda.
    1. Clique no serviço de Provisionamento de Dispositivos que pretende inscrever o seu dispositivo a partir da lista de recursos.
    1. No seu serviço de fornecimento, clique **em Gerir as inscrições** e, em seguida, clique no botão **de inscrição individual Add** no topo.   

       [![Adicionar uma inscrição individual no portal](./media/how-to-manage-enrollments/add-individual-enrollment.png)](./media/how-to-manage-enrollments/add-individual-enrollment.png#lightbox)

    1. Quando aparecer o painel "Adicionar Inscrição", insira as informações para a inscrição individual do dispositivo e clique em **Guardar**. 
     
        [![Inscrição individual no portal](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)
    
        | Campo | Descrição |
        | :--- | :--- |
        | **Mecanismo** | Selecione **X.509**, **TPM** ou **Tecla Simétrica** para o mecanismo de atestação utilizar dependendo do método de atestado que os seus dispositivos utilizarão. |
        | Definições de atestado | Para obter instruções passo a passo sobre a criação e utilização de matrículas individuais com chaves simétricas ou certificados X.509, consulte um dos [dispositivos simétricos](quick-create-simulated-device-symmetric-key-java.md#create-a-device-enrollment) da Provisão ou provision a aceleração do [dispositivo de certificado X.509.](quick-create-simulated-device-x509-java.md#create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry)<br><br>Para obter instruções passo a passo sobre a criação e utilização de matrículas individuais utilizando atestado de TPM, consulte uma das [amostras simuladas do dispositivo TPM.](quick-create-simulated-device-tpm-java.md#create-a-device-enrollment-entry)|
        | **IoT Hub Device ID** |  Este ID representará o seu dispositivo. Deve seguir as regras para uma identificação do dispositivo. Para mais informações, consulte [as propriedades de identidade do Dispositivo.](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)<br><br>Ao utilizar certificados X.509, este texto deve ser o nome do assunto no certificado do dispositivo que carrega para a inscrição. Este nome deve estar em conformidade com as regras de identificação do dispositivo.|
            


## <a name="update-an-enrollment-entry"></a>Atualizar uma entrada de inscrição
Pode atualizar uma inscrição existente no portal utilizando os seguintes passos:

1. Abra o seu serviço de Provisionamento de Dispositivos no portal Azure e clique em **Gerir Inscrições**. 
1. Navegue para a entrada de inscrição que pretende modificar. Clique na entrada, que abre uma informação sumária sobre a inscrição do seu dispositivo. 
1. Nesta página, pode modificar itens que não sejam o tipo de segurança e credenciais, como o hub IoT a que o dispositivo deve estar ligado e o ID do dispositivo. Também pode modificar o estado gémeo do dispositivo inicial. 
1. Uma vez concluído, clique em **Guardar** para atualizar a inscrição do seu dispositivo. 

    ![Atualizar inscrições no portal](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Remover uma inscrição de dispositivo
Nos casos em que o(s) dispositivo(s) não necessite de ser a provisionado em qualquer hub IoT, pode remover a inscrição relacionada no portal utilizando os seguintes passos:

1. Abra o seu serviço de Provisionamento de Dispositivos no portal Azure e clique em **Gerir Inscrições**. 
1. Navegue e selecione a entrada de inscrição que pretende remover. 
1. Clique no botão **Eliminar** na parte superior e, em seguida, selecione **Sim** quando solicitado para confirmar. 
1. Assim que a ação estiver concluída, verá a sua entrada removida da lista de inscrições do dispositivo. 
 
    ![Remover inscrição no portal](./media/how-to-manage-enrollments/remove-enrollment.png)