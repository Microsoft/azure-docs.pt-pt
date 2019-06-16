---
title: Como disenroll um dispositivo a partir do serviço de aprovisionamento de dispositivos do Azure IoT Hub
description: Como disenroll um dispositivo para impedir que o aprovisionamento através do serviço de aprovisionamento de dispositivos do Azure IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 0dadf0ec248dac01e5cc65779004477bf4afc823
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62113584"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Como disenroll um dispositivo a partir do serviço de aprovisionamento de dispositivos do Azure IoT Hub

Apropriadas de gerenciamento de credenciais do dispositivo é fundamental para sistemas de alto perfil como soluções de IoT. Uma prática recomendada para estes sistemas é ter um plano claro de como revogar o acesso para dispositivos quando as credenciais, se um token de acesso partilhado de assinaturas (SAS) ou um certificado X.509, possam ter sido comprometidas. 

Inscrição no serviço de aprovisionamento de dispositivos permite que um dispositivo ser [aprovisionado automaticamente](concepts-auto-provisioning.md). Um dispositivo aprovisionado é aquele que foi registado com o IoT Hub, permitindo que ele receber seu inicial [dispositivo duplo](~/articles/iot-hub/iot-hub-devguide-device-twins.md) de estado e começam a reportar dados de telemetria. Este artigo descreve como disenroll um dispositivo a partir da sua instância do serviço aprovisionamento, que o impedem de que está a ser aprovisionado novamente no futuro.

> [!NOTE] 
> Tenha em atenção de dispositivos que se revogar o acesso para a política de repetição. Por exemplo, um dispositivo que tem uma política de repetição infinita continuamente poderá tentar registar com o serviço de aprovisionamento. Nessa situação consome recursos do serviço e, possivelmente, afeta o desempenho.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Lista de bloqueios de dispositivos através de uma entrada de inscrição individual

Inscrições individuais aplicam-se a um dispositivo individual e podem utilizar certificados X.509 ou tokens SAS (num TPM real ou virtual) como o mecanismo de atestação. (Para dispositivos que usam SAS tokens, como seu mecanismo de atestado pode ser provisionado apenas por meio de uma inscrição individual.) Na lista de proibições um dispositivo que tenha uma inscrição individual, pode desativar ou eliminar a entrada de inscrição. 

À temporariamente lista de bloqueio do dispositivo ao desativar a sua entrada de inscrição: 

1. Inicie sessão no portal do Azure e selecione **todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione a lista de bloqueio do dispositivo no serviço de aprovisionamento.
3. No seu serviço de aprovisionamento, selecione **gerir inscrições**e, em seguida, selecione a **inscrições individuais** separador.
4. Selecione a entrada de inscrição para o dispositivo que pretende criar uma lista de bloqueio. 

    ![Selecione a sua inscrição individual](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Na sua página de inscrição, desloque para baixo e selecione **desativar** para o **ativar entrada** mudar e, em seguida, selecione **guardar**.  

   ![Desativar a entrada de inscrição individual no portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

À permanentemente lista de bloqueio do dispositivo ao eliminar a entrada de inscrição:

1. Inicie sessão no portal do Azure e selecione **todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione a lista de bloqueio do dispositivo no serviço de aprovisionamento.
3. No seu serviço de aprovisionamento, selecione **gerir inscrições**e, em seguida, selecione a **inscrições individuais** separador.
4. Selecione a caixa de verificação junto a entrada de inscrição para o dispositivo que pretende criar uma lista de bloqueio. 
5. Selecione **elimine** na parte superior da janela e, em seguida, selecione **Sim** para confirmar que pretende remover a inscrição. 

   ![Eliminar a entrada de inscrição individual no portal](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Depois de concluir o procedimento, verá sua participação removida da lista de inscrições individuais.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Lista de proibições um certificado de AC de raiz ou intermediário X.509 ao utilizar um grupo de inscrição

Certificados X.509, normalmente, são organizados numa cadeia de certificados de confiança. Se um certificado em qualquer fase de uma cadeia de ficar comprometido, a confiança é quebrada. O certificado tem de ser bloqueado para impedir que o serviço aprovisionamento de dispositivos de aprovisionamento dispositivos downstream qualquer cadeia que contém esse certificado. Para saber mais sobre certificados X.509 e como elas são usadas com o serviço de aprovisionamento, consulte [certificados X.509](./concepts-security.md#x509-certificates). 

Um grupo de inscrição é uma entrada para os dispositivos que partilham um mecanismo de atestado comuns de certificados X.509 assinado por intermédio iguais ou AC de raiz. A entrada de inscrição de grupo está configurada com o certificado X.509 associado intermediários ou o AC de raiz. A entrada também é configurada com qualquer configuração valores, tais como Estado duplo e a ligação do hub IoT, que são partilhados por dispositivos com esse certificado na respetiva cadeia de certificados. Na lista de proibições o certificado, pode desativar ou eliminar o grupo de inscrição.

Na temporariamente lista de proibições o certificado ao desativar o respetivo grupo de inscrição: 

1. Inicie sessão no portal do Azure e selecione **todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de aprovisionamento que pretende que o certificado de assinatura de lista de bloqueio.
3. No seu serviço de aprovisionamento, selecione **gerir inscrições**e, em seguida, selecione a **grupos de inscrição** separador.
4. Selecione o grupo de inscrição com o certificado que pretende criar uma lista de bloqueio.
5. Selecione **desativar** sobre o **ativar entrada** mudar e, em seguida, selecione **guardar**.  

   ![Desativar entrada de grupo de inscrição no portal](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Na permanentemente lista de proibições o certificado ao eliminar o grupo de inscrição:

1. Inicie sessão no portal do Azure e selecione **todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione a lista de bloqueio do dispositivo no serviço de aprovisionamento.
3. No seu serviço de aprovisionamento, selecione **gerir inscrições**e, em seguida, selecione a **grupos de inscrição** separador.
4. Selecione a caixa de verificação junto ao grupo de inscrição para o certificado que pretende criar uma lista de bloqueio. 
5. Selecione **elimine** na parte superior da janela e, em seguida, selecione **Sim** para confirmar que pretende remover o grupo de inscrição. 

   ![Eliminar a entrada de grupo de inscrição no portal](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Depois de concluir o procedimento, verá sua participação removida da lista de grupos de inscrição.  

> [!NOTE]
> Se eliminar um grupo de inscrição para um certificado, os dispositivos que tenham o certificado na respetiva cadeia de certificados ainda poderão inscrever-se se um grupo de inscrição ativado para o certificado de raiz ou outro certificado intermédio apresentado acima no respetivo certificado existe a cadeia.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Lista de bloqueios de dispositivos específicos num grupo de inscrição

Dispositivos que implementam o mecanismo de atestado de X.509 utilizam cadeia de certificados do dispositivo e a chave privada para autenticar. Quando um dispositivo liga-se e efetua a autenticação no serviço aprovisionamento de dispositivos, o serviço procura primeiro uma inscrição individual que corresponde ao credenciais do dispositivo. O serviço de pesquisa, em seguida, grupos de inscrição para determinar se o dispositivo pode ser aprovisionado. Se o serviço encontrar uma inscrição individual desativada para o dispositivo, ele impede que o dispositivo se liguem. O serviço impede que a ligação, mesmo se existe um grupo de inscrição ativado para um utilizador intermédio ou de uma AC de raiz na cadeia de certificados do dispositivo. 

Na lista de proibições um dispositivo individual num grupo de inscrição, siga estes passos:

1. Inicie sessão no portal do Azure e selecione **todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de aprovisionamento que contém o grupo de inscrição para o dispositivo que pretende criar uma lista de bloqueio.
3. No seu serviço de aprovisionamento, selecione **gerir inscrições**e, em seguida, selecione a **inscrições individuais** separador.
4. Selecione o **adicionar inscrição individual** botão na parte superior. 
5. Sobre o **adicionar inscrição** página, selecione **X.509** como o atestado **mecanismo** para o dispositivo.

    Carregar o certificado do dispositivo e introduza o ID de dispositivo do dispositivo a ser bloqueado. Para o certificado, utilize o certificado de entidade final assinado instalado no dispositivo. O dispositivo utiliza o certificado de entidade final assinado para autenticação.

    ![Definir as propriedades do dispositivo para o dispositivo bloqueada](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Desloque-se para a parte inferior dos **adicionar inscrição** página e selecione **desativar** no **permitir entrada** mudar e, em seguida, selecione **guardar**. 

    [![Utilização desativada a entrada de inscrição individual para desativar do dispositivo de inscrição de grupo, no portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Ao criar a sua inscrição com êxito, deverá ver a sua inscrição de dispositivo desativado listada no **inscrições individuais** separador. 

## <a name="next-steps"></a>Passos Seguintes

A anulação de inscrições também faz parte do processo de desaprovisionamento maior. Um dispositivo de desaprovisionamento inclui tanto a anulação de inscrições do serviço de aprovisionamento e a anulação do IoT hub. Para saber mais sobre o processo completo, consulte o artigo [como desaprovisionar os dispositivos que foram anteriormente aprovisionados](how-to-unprovision-devices.md) 

