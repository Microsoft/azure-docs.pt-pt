---
title: Dispositivo de disenroll do Serviço de Provisionamento de Dispositivos Azure IoT Hub
description: Como desinsupear um dispositivo para evitar o provisionamento através do Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 120446472038076e34f62b47ba79348e5de8b972
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010933"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Como desinsurar um dispositivo do Serviço de Provisionamento de Dispositivos Azure IoT Hub

A gestão adequada das credenciais de dispositivos é crucial para sistemas de alto perfil como soluções IoT. Uma das melhores práticas para estes sistemas é ter um plano claro de como revogar o acesso aos dispositivos quando as suas credenciais, quer um token de assinaturas de acesso partilhado (SAS) ou um certificado X.509, possam ser comprometidos. 

A inscrição no Serviço de Provisionamento de Dispositivos permite a [provisionamento de](about-iot-dps.md#provisioning-process)um dispositivo . Um dispositivo provisionado é aquele que foi registado no IoT Hub, permitindo-lhe receber o seu [primeiro dispositivo twin](~/articles/iot-hub/iot-hub-devguide-device-twins.md) state e começar a reportar dados de telemetria. Este artigo descreve como desativar um dispositivo da sua instância de serviço de fornecimento, impedindo-o de ser novamente a provisionado no futuro.

> [!NOTE] 
> Esteja atento à política de retíria dos dispositivos para os quais revoga o acesso. Por exemplo, um dispositivo que tenha uma política de relagem infinita pode continuar a tentar registar-se com o serviço de fornecimento. Esta situação consome recursos de serviço e possivelmente afeta o desempenho.

## <a name="disallow-devices-by-using-an-individual-enrollment-entry"></a>Não permitir dispositivos utilizando uma entrada individual de inscrição

As inscrições individuais aplicam-se a um único dispositivo e podem utilizar certificados X.509 ou fichas SAS (num TPM real ou virtual) como mecanismo de atestado. (Os dispositivos que utilizam fichas SAS como mecanismo de atestado só podem ser a provisionados através de uma inscrição individual.) Para não permitir um dispositivo que tenha uma inscrição individual, pode desativar ou eliminar a sua entrada de inscrição. 

Para não permitir temporariamente o dispositivo, desativando a sua entrada de inscrição: 

1. Inscreva-se no portal Azure e selecione **Todos os recursos** do menu esquerdo.
2. Na lista de recursos, selecione o serviço de fornecimento de que pretende não permitir o seu dispositivo.
3. No seu serviço de fornecimento, **selecione Gerir as inscrições** e, em seguida, selecione o **separador Inscrições Individuais.**
4. Selecione a entrada de inscrição para o dispositivo que pretende não permitir. 

    ![Selecione a sua inscrição individual](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Na sua página de inscrição, desloque-se para baixo e selecione **Desativar** o interruptor **de entrada Ativar** e, em seguida, selecione **Guardar**.  

   ![Desativar a inscrição individual no portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Não permitir permanentemente o dispositivo, eliminando a sua entrada de inscrição:

1. Inscreva-se no portal Azure e selecione **Todos os recursos** do menu esquerdo.
2. Na lista de recursos, selecione o serviço de fornecimento de que pretende não permitir o seu dispositivo.
3. No seu serviço de fornecimento, **selecione Gerir as inscrições** e, em seguida, selecione o **separador Inscrições Individuais.**
4. Selecione a caixa de verificação ao lado da entrada de inscrição para o dispositivo que pretende não permitir. 
5. Selecione **Eliminar** na parte superior da janela e, em seguida, selecione **Sim** para confirmar que deseja remover a inscrição. 

   ![Excluir a inscrição individual no portal](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Depois de terminar o procedimento, deverá ver a sua entrada removida da lista de inscrições individuais.  

## <a name="disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Desativar um certificado de CA intermédio ou raiz X.509 utilizando um grupo de inscrição

Os certificados X.509 são normalmente organizados numa cadeia de certificados de confiança. Se um certificado em qualquer fase de uma cadeia ficar comprometido, a confiança é quebrada. O certificado deve ser proibido para impedir que o Serviço de Provisionamento de Dispositivos provisa a jusante em qualquer cadeia que contenha esse certificado. Para saber mais sobre os certificados X.509 e como são utilizados com o serviço de fornecimento, consulte [os certificados X.509](./concepts-x509-attestation.md#x509-certificates). 

Um grupo de inscrições é uma entrada para dispositivos que partilham um mecanismo comum de atestado de certificados X.509 assinados pela mesma AC intermédia ou raiz. A entrada do grupo de inscrição está configurada com o certificado X.509 associado à AC intermédia ou raiz. A entrada é também configurada com quaisquer valores de configuração, como o twin state e a ligação do hub IoT, que são partilhados por dispositivos com esse certificado na sua cadeia de certificados. Para não permitir o certificado, pode desativar ou eliminar o seu grupo de inscrição.

Não permitir temporariamente o certificado, desativando o seu grupo de matrículas: 

1. Inscreva-se no portal Azure e selecione **Todos os recursos** do menu esquerdo.
2. Na lista de recursos, selecione o serviço de fornecimento que pretende não permitir o certificado de assinatura.
3. No seu serviço de fornecimento, **selecione Gerir as inscrições** e, em seguida, selecione o **separador Grupos de Inscrição.**
4. Selecione o grupo de inscrição usando o certificado que pretende não permitir.
5. Selecione **Desativar** o interruptor **de entrada Ativa** e, em seguida, selecione **Guardar**.  

   ![Desativar a entrada do grupo de inscrição no portal](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Não permitir permanentemente o certificado, eliminando o seu grupo de matrículas:

1. Inscreva-se no portal Azure e selecione **Todos os recursos** do menu esquerdo.
2. Na lista de recursos, selecione o serviço de fornecimento de que pretende não permitir o seu dispositivo.
3. No seu serviço de fornecimento, **selecione Gerir as inscrições** e, em seguida, selecione o **separador Grupos de Inscrição.**
4. Selecione a caixa de verificação ao lado do grupo de inscrição para o certificado que pretende não permitir. 
5. Selecione **Eliminar** na parte superior da janela e, em seguida, selecione **Sim** para confirmar que pretende remover o grupo de inscrição. 

   ![Apagar entrada de grupo de inscrição no portal](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Depois de terminar o procedimento, deverá ver a sua entrada removida da lista de grupos de inscrição.  

> [!NOTE]
> Se eliminar um grupo de inscrição para um certificado, os dispositivos que tenham o certificado na sua cadeia de certificados poderão ainda inscrever-se se existir um grupo de inscrição habilitado para o certificado raiz ou outro certificado intermédio mais elevado na sua cadeia de certificados.

## <a name="disallow-specific-devices-in-an-enrollment-group"></a>Não permitir dispositivos específicos num grupo de inscrição

Os dispositivos que implementam o mecanismo de atestado X.509 utilizam a cadeia de certificados do dispositivo e a chave privada para autenticar. Quando um dispositivo se conecta e autentica com o Serviço de Provisionamento de Dispositivos, o serviço procura primeiro uma inscrição individual que corresponda às credenciais do dispositivo. O serviço procura então os grupos de inscrição para determinar se o dispositivo pode ser a provisionado. Se o serviço encontrar uma inscrição individual desativada para o dispositivo, impede que o dispositivo se conecte. O serviço impede a ligação mesmo que exista um grupo de inscrição ativado para um CA intermédio ou raiz na cadeia de certificados do dispositivo. 

Para não permitir um dispositivo individual num grupo de inscrição, siga estes passos:

1. Inscreva-se no portal Azure e selecione **Todos os recursos** do menu esquerdo.
2. A partir da lista de recursos, selecione o serviço de fornecimento que contém o grupo de inscrição para o dispositivo que pretende não permitir.
3. No seu serviço de fornecimento, **selecione Gerir as inscrições** e, em seguida, selecione o **separador Inscrições Individuais.**
4. Selecione o botão **de inscrição individual Adicionar** na parte superior. 
5. Na página **'Adicionar Inscrição',** selecione **X.509** como Mecanismo **de** Atestação para o dispositivo.

    Faça o upload do certificado do dispositivo e introduza a identificação do dispositivo a não ser permitida. Para o certificado, utilize o certificado de entidade final assinado instalado no dispositivo. O dispositivo utiliza o certificado de entidade final assinado para autenticação.

    ![Definir as propriedades do dispositivo para o dispositivo não permitido](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Desloque-se para a parte inferior da página **'Adicionar Inscrição'** e selecione **Desativar** o interruptor **de entrada Ativa** e, em seguida, selecione **Guardar**. 

    [![Utilizar entrada individual desativada para desativar dispositivo a partir da inscrição em grupo, no portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Quando criar com sucesso a sua inscrição, deverá ver a inscrição do dispositivo desativado listada no separador **Inscrições Individuais.** 

## <a name="next-steps"></a>Passos seguintes

O desinscrição também faz parte do processo de desprovisionamento maior. A desprovisionação de um dispositivo inclui tanto o desinscrição do serviço de fornecimento como a desregistação do hub IoT. Para conhecer todo o processo, consulte [Como desprovisionar dispositivos que foram previamente auto-aprovisionados](how-to-unprovision-devices.md)