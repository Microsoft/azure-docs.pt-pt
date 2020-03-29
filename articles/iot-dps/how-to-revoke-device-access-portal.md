---
title: Dispositivo de desinscrição do Serviço de Provisionamento de Dispositivos Azure IoT Hub
description: Como desinscrever um dispositivo para evitar o fornecimento através do Serviço de Provisionamento de Dispositivos Hub Azure IoT (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: af883da67f4e1bc819514e88ff480526e16124db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974926"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Como desinscrever um dispositivo do Serviço de Provisionamento de Dispositivos Do Hub Azure IoT

A gestão adequada das credenciais do dispositivo é crucial para sistemas de alto perfil como soluções IoT. Uma boa prática para estes sistemas é ter um plano claro de como revogar o acesso aos dispositivos quando as suas credenciais, quer um símbolo de acesso partilhado (SAS) ou um certificado X.509, possam ser comprometidos. 

A inscrição no Serviço de Provisionamento de Dispositivos permite que um dispositivo seja [aprovisionado automaticamente.](concepts-auto-provisioning.md) Um dispositivo provisionado é um dispositivo que foi registado no IoT Hub, permitindo-lhe receber o seu dispositivo inicial [de estado gémeo](~/articles/iot-hub/iot-hub-devguide-device-twins.md) e começar a reportar dados de telemetria. Este artigo descreve como desinscrever um dispositivo da sua instância de serviço de provisionamento, impedindo-o de ser novamente provisionado no futuro.

> [!NOTE] 
> Esteja atento à política de retry dos dispositivos para os quais revoga o acesso. Por exemplo, um dispositivo que tenha uma política de retry infinita pode continuar a tentar registar-se com o serviço de provisionamento. Esta situação consome recursos de serviço e possivelmente afeta o desempenho.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Dispositivos da lista negra utilizando uma entrada individual de inscrição

As matrículas individuais aplicam-se a um único dispositivo e podem utilizar certificados X.509 ou tokens SAS (num TPM real ou virtual) como mecanismo de atestado. (Os dispositivos que utilizam tokens SAS como mecanismo de atestado só podem ser provisionados através de uma matrícula individual.) Para colocar na lista negra um dispositivo que tenha uma matrícula individual, pode desativar ou apagar a sua inscrição. 

Para colocar temporariamente na lista negra o dispositivo, desativando a sua entrada de matrícula: 

1. Inscreva-se no portal Azure e selecione **Todos os recursos** do menu esquerdo.
2. Na lista de recursos, selecione o serviço de fornecimento a que pretende colocar na lista negra o seu dispositivo.
3. No seu serviço de provisionamento, selecione **Gerir as matrículas**e, em seguida, selecione o separador **Individual Registrations.**
4. Selecione a entrada de inscrição do dispositivo que pretende colocar na lista negra. 

    ![Selecione a sua inscrição individual](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Na sua página de inscrição, percorra para baixo e selecione **Desativar** para o interruptor de **entrada Ativar** e, em seguida, selecione **Guardar**.  

   ![Desativar a inscrição individual no portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Para colocar a lista negra permanente do dispositivo, apagando a sua inscrição:

1. Inscreva-se no portal Azure e selecione **Todos os recursos** do menu esquerdo.
2. Na lista de recursos, selecione o serviço de fornecimento a que pretende colocar na lista negra o seu dispositivo.
3. No seu serviço de provisionamento, selecione **Gerir as matrículas**e, em seguida, selecione o separador **Individual Registrations.**
4. Selecione a caixa de verificação junto à entrada de inscrição do dispositivo que pretende colocar na lista negra. 
5. **Selecione Apagar** na parte superior da janela e, em seguida, selecione **Sim** para confirmar que pretende remover a matrícula. 

   ![Excluir entrada individual de inscrição no portal](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Depois de terminar o procedimento, deverá ver a sua inscrição removida da lista de matrículas individuais.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Lista negra de um certificado de CA intermédio ou raiz X.509 através de um grupo de inscrições

Os certificados X.509 são normalmente organizados numa cadeia de fidedignidade de certificados. Se um certificado em qualquer fase de uma cadeia ficar comprometido, a confiança é quebrada. O certificado deve ser colocado na lista negra para evitar que o Serviço de Provisionamento de Dispositivos aprovisione dispositivos a jusante em qualquer cadeia que contenha esse certificado. Para saber mais sobre os certificados X.509 e como são utilizados com o serviço de provisionamento, consulte [os certificados X.509](./concepts-security.md#x509-certificates). 

Um grupo de inscrições é uma entrada para dispositivos que partilham um mecanismo comum de atestado de certificados X.509 assinados pelo mesmo CA intermédio ou raiz. A entrada do grupo de matrículas está configurada com o certificado X.509 associado ao CA intermédio ou raiz. A entrada também é configurada com quaisquer valores de configuração, tais como twin state e ioT hub connection, que são partilhados por dispositivos com esse certificado na sua cadeia de certificados. Para colocar o certificado na lista negra, pode desativar ou eliminar o seu grupo de matrículas.

Para colocar temporariamente na lista negra o certificado, desativando o seu grupo de matrículas: 

1. Inscreva-se no portal Azure e selecione **Todos os recursos** do menu esquerdo.
2. Na lista de recursos, selecione o serviço de fornecimento a que pretende colocar na lista negra o certificado de assinatura.
3. No seu serviço de provisionamento, selecione **Gerir as matrículas**e, em seguida, selecione o separador **Grupos de Matrícula.**
4. Selecione o grupo de inscrições utilizando o certificado que pretende colocar na lista negra.
5. Selecione **Desativar** o interruptor de **entrada Ativar** e, em seguida, selecione **Save**.  

   ![Desativar a entrada do grupo de matrículas no portal](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Para colocar permanentemente na lista negra o certificado, apagando o seu grupo de matrículas:

1. Inscreva-se no portal Azure e selecione **Todos os recursos** do menu esquerdo.
2. Na lista de recursos, selecione o serviço de fornecimento a que pretende colocar na lista negra o seu dispositivo.
3. No seu serviço de provisionamento, selecione **Gerir as matrículas**e, em seguida, selecione o separador **Grupos de Matrícula.**
4. Selecione a caixa de verificação ao lado do grupo de matrículas para o certificado que pretende colocar na lista negra. 
5. **Selecione Eliminar** na parte superior da janela e, em seguida, selecione **Sim** para confirmar que pretende remover o grupo de matrículas. 

   ![Excluir a entrada do grupo de matrículas no portal](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Depois de terminar o procedimento, deverá ver a sua inscrição removida da lista de grupos de matrículas.  

> [!NOTE]
> Se eliminar um grupo de inscrição para um certificado, os dispositivos que possuam o certificado na sua cadeia de certificados poderão ainda inscrever-se se um grupo de inscrição habilitado para o certificado raiz ou outro certificado intermédio superior no seu certificado cadeia existe.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Dispositivos específicos da lista negra num grupo de matrículas

Os dispositivos que implementam o mecanismo de atesta X.509 utilizam a cadeia de certificados do dispositivo e a chave privada para autenticar. Quando um dispositivo se conecta e autentica com o Serviço de Provisionamento de Dispositivos, o serviço procura primeiro uma inscrição individual que corresponda às credenciais do dispositivo. O serviço procura então grupos de matrícula para determinar se o dispositivo pode ser aprovisionado. Se o serviço encontrar uma matrícula individual desativada para o dispositivo, impede a ligação do dispositivo. O serviço impede a ligação mesmo que exista um grupo de inscrição ativado para um CA intermédio ou de raiz na cadeia de certificados do dispositivo. 

Para colocar na lista negra um dispositivo individual num grupo de matrículas, siga estes passos:

1. Inscreva-se no portal Azure e selecione **Todos os recursos** do menu esquerdo.
2. A partir da lista de recursos, selecione o serviço de provisionamento que contém o grupo de inscrição para o dispositivo que pretende colocar na lista negra.
3. No seu serviço de provisionamento, selecione **Gerir as matrículas**e, em seguida, selecione o separador **Individual Registrations.**
4. Selecione o botão **de inscrição individual Adicionar** na parte superior. 
5. Na página **Adicionar Inscrições,** selecione **X.509** como mecanismo de **atestado** para o dispositivo.

    Faça o upload do certificado do dispositivo e introduza a identificação do dispositivo na lista negra. Para o certificado, utilize o certificado de entidade final assinado instalado no dispositivo. O dispositivo utiliza o certificado de entidade final assinado para autenticação.

    ![Definir propriedades do dispositivo para o dispositivo na lista negra](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Desloque-se para a parte inferior da página **'Adicionar'** e selecione **Desativar** o interruptor de **entrada Ativar** e, em seguida, selecione **Guardar**. 

    [![Utilize a inscrição individual desativada para desativar o dispositivo a partir da inscrição em grupo, no portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Quando criar com sucesso a sua inscrição, deve ver a inscrição do seu dispositivo desativado listado no separador **Individual Registrations.** 

## <a name="next-steps"></a>Passos seguintes

A desinscrição também faz parte do maior processo de desprovisionamento. A desprovisionamento de um dispositivo inclui a desinscrição do serviço de provisionamento e o desregisto do hub IoT. Para conhecer o processo completo, consulte [como desprovisionar dispositivos que foram previamente aprovisionados automaticamente](how-to-unprovision-devices.md) 

