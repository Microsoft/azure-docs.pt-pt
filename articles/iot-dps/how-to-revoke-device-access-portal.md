---
title: Cancelar o registro de dispositivo do serviço de provisionamento de dispositivos no Hub IoT do Azure
description: Como cancelar o registro de um dispositivo para impedir o provisionamento por meio do DPS (serviço de provisionamento de dispositivos) do Hub IoT do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: af883da67f4e1bc819514e88ff480526e16124db
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974926"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Como cancelar o registro de um dispositivo do serviço de provisionamento de dispositivos no Hub IoT do Azure

O gerenciamento adequado de credenciais de dispositivo é crucial para sistemas de alto perfil como soluções de IoT. Uma prática recomendada para esses sistemas é ter um plano claro de como revogar o acesso para dispositivos quando suas credenciais, se um token SAS (assinaturas de acesso compartilhado) ou um certificado X. 509, puderem ser comprometidos. 

O registro no serviço de provisionamento de dispositivos permite que um dispositivo seja [provisionado automaticamente](concepts-auto-provisioning.md). Um dispositivo provisionado é aquele que foi registrado com o Hub IoT, permitindo que ele receba seu estado inicial de [dispositivo](~/articles/iot-hub/iot-hub-devguide-device-twins.md) de 1 e comece a relatar dados de telemetria. Este artigo descreve como cancelar o registro de um dispositivo de sua instância do serviço de provisionamento, impedindo que ele seja provisionado novamente no futuro.

> [!NOTE] 
> Lembre-se da política de repetição de dispositivos para os quais você revoga o acesso. Por exemplo, um dispositivo que tem uma política de repetição infinita pode tentar se registrar continuamente no serviço de provisionamento. Essa situação consome recursos de serviço e possivelmente afeta o desempenho.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Dispositivos de lista negra usando uma entrada de registro individual

Os registros individuais se aplicam a um único dispositivo e podem usar certificados X. 509 ou tokens SAS (em um TPM real ou virtual) como o mecanismo de atestado. (Os dispositivos que usam tokens SAS como seu mecanismo de atestado podem ser provisionados apenas por meio de um registro individual.) Para adicionar um dispositivo que tenha um registro individual à lista negra, você pode desabilitar ou excluir sua entrada de registro. 

Para adicionar temporariamente o dispositivo à lista de bloqueios desabilitando sua entrada de registro: 

1. Entre no portal do Azure e selecione **todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de provisionamento do qual você deseja adicionar seu dispositivo.
3. No serviço de provisionamento, selecione **gerenciar registros**e, em seguida, selecione a guia **registros individuais** .
4. Selecione a entrada de registro para o dispositivo que você deseja adicionar à lista de bloqueios. 

    ![Selecione seu registro individual](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Na página de registro, role até a parte inferior e selecione **desabilitar** para a opção **Habilitar entrada** e, em seguida, selecione **salvar**.  

   ![Desabilitar a entrada de registro individual no portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Para adicionar o dispositivo à lista de bloqueios permanentemente excluindo sua entrada de registro:

1. Entre no portal do Azure e selecione **todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de provisionamento do qual você deseja adicionar seu dispositivo.
3. No serviço de provisionamento, selecione **gerenciar registros**e, em seguida, selecione a guia **registros individuais** .
4. Marque a caixa de seleção ao lado da entrada de registro do dispositivo que você deseja adicionar à lista de bloqueios. 
5. Selecione **excluir** na parte superior da janela e, em seguida, selecione **Sim** para confirmar que deseja remover o registro. 

   ![Excluir entrada de registro individual no portal](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Depois de concluir o procedimento, você deverá ver sua entrada removida da lista de registros individuais.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Lista negra de um certificado de autoridade de certificação X. 509 intermediário ou raiz usando um grupo de registro

Os certificados X. 509 normalmente são organizados em uma cadeia de certificados de confiança. Se um certificado em qualquer estágio de uma cadeia for comprometido, a confiança será interrompida. O certificado deve estar na lista negra para impedir que o serviço de provisionamento de dispositivos provisione dispositivos downstream em qualquer cadeia que contenha esse certificado. Para saber mais sobre os certificados X. 509 e como eles são usados com o serviço de provisionamento, consulte [certificados x. 509](./concepts-security.md#x509-certificates). 

Um grupo de registro é uma entrada para dispositivos que compartilham um mecanismo de atestado comum de certificados X. 509 assinados pela mesma AC intermediária ou raiz. A entrada do grupo de registros é configurada com o certificado X. 509 associado à AC intermediária ou raiz. A entrada também é configurada com quaisquer valores de configuração, como conexão de estado de conexões e Hub IoT, que são compartilhados por dispositivos com esse certificado em sua cadeia de certificados. Para adicionar o certificado à lista negra, você pode desabilitar ou excluir seu grupo de registro.

Para adicionar temporariamente o certificado à lista de bloqueios desabilitando seu grupo de registro: 

1. Entre no portal do Azure e selecione **todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de provisionamento do qual você deseja adicionar o certificado de autenticação.
3. No serviço de provisionamento, selecione **gerenciar registros**e, em seguida, selecione a guia **grupos de registro** .
4. Selecione o grupo de registros usando o certificado que você deseja adicionar à lista de bloqueios.
5. Selecione **desabilitar** na opção **Habilitar entrada** e, em seguida, selecione **salvar**.  

   ![Desabilitar a entrada do grupo de registros no portal](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Para colocar o certificado em lista negra permanentemente excluindo seu grupo de registro:

1. Entre no portal do Azure e selecione **todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de provisionamento do qual você deseja adicionar seu dispositivo.
3. No serviço de provisionamento, selecione **gerenciar registros**e, em seguida, selecione a guia **grupos de registro** .
4. Marque a caixa de seleção ao lado do grupo de registros do certificado que você deseja adicionar à lista de bloqueios. 
5. Selecione **excluir** na parte superior da janela e, em seguida, selecione **Sim** para confirmar que deseja remover o grupo de registros. 

   ![Excluir a entrada do grupo de registros no portal](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Depois de concluir o procedimento, você deverá ver sua entrada removida da lista de grupos de registro.  

> [!NOTE]
> Se você excluir um grupo de registros de um certificado, os dispositivos que têm o certificado em sua cadeia de certificados ainda poderão se registrar se um grupo de registro habilitado para o certificado raiz ou outro certificado intermediário for superior em seu certificado cadeia existente.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Dispositivos de lista de bloqueios específicos em um grupo de registros

Os dispositivos que implementam o mecanismo de atestado X. 509 usam a cadeia de certificados do dispositivo e a chave privada para autenticação. Quando um dispositivo se conecta e autentica com o serviço de provisionamento de dispositivos, o serviço primeiro procura um registro individual que corresponda às credenciais do dispositivo. Em seguida, o serviço pesquisa grupos de registro para determinar se o dispositivo pode ser provisionado. Se o serviço encontrar um registro individual desabilitado para o dispositivo, ele impedirá que o dispositivo se conecte. O serviço impede a conexão mesmo se um grupo de registro habilitado para uma AC intermediária ou raiz na cadeia de certificados do dispositivo existir. 

Para lista negra de um dispositivo individual em um grupo de registro, siga estas etapas:

1. Entre no portal do Azure e selecione **todos os recursos** no menu à esquerda.
2. Na lista de recursos, selecione o serviço de provisionamento que contém o grupo de registros para o dispositivo em que você deseja adicionar a listagem negra.
3. No serviço de provisionamento, selecione **gerenciar registros**e, em seguida, selecione a guia **registros individuais** .
4. Selecione o botão **adicionar registro individual** na parte superior. 
5. Na página **adicionar registro** , selecione **X. 509** como o **mecanismo** de atestado para o dispositivo.

    Carregue o certificado do dispositivo e insira a ID do dispositivo a ser listada na lista negra. Para o certificado, use o certificado de entidade final assinado instalado no dispositivo. O dispositivo usa o certificado de entidade final assinada para autenticação.

    ![Definir propriedades de dispositivo para o dispositivo na lista negra](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Role até a parte inferior da página **adicionar registro** e selecione **desabilitar** na opção **Habilitar entrada** e, em seguida, selecione **salvar**. 

    [![usar a entrada de registro individual desabilitada para desabilitar o dispositivo do registro de grupo, no portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Quando você criar o registro com êxito, você deverá ver o registro de dispositivo desabilitado listado na guia **registros individuais** . 

## <a name="next-steps"></a>Passos seguintes

O cancelamento do registro também faz parte do processo maior de desprovisionamento. O desprovisionamento de um dispositivo inclui o cancelamento do registro do serviço de provisionamento e o cancelamento do registro do Hub IoT. Para saber mais sobre o processo completo, consulte [como desprovisionar dispositivos que foram previamente provisionados automaticamente](how-to-unprovision-devices.md) 

