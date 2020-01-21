---
title: Distribuir certificados X. 509 no serviço de provisionamento de dispositivos no Hub IoT do Azure
description: Como distribuir certificados X. 509 com sua instância do serviço de provisionamento de dispositivos (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 4d5ddb229cd6a41235990437bc0f8db08e3381ce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974892"
---
# <a name="how-to-roll-x509-device-certificates"></a>Como distribuir certificados de dispositivo X. 509

Durante o ciclo de vida da sua solução de IoT, você precisará reverter os certificados. Dois dos principais motivos para a reversão de certificados seriam uma violação de segurança e expirações de certificado. 

Certificados contínuos é uma prática recomendada de segurança para ajudar a proteger seu sistema em caso de violação. Como parte da [metodologia de supor a violação](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), a Microsoft defende a necessidade de ter processos de segurança reativos em vigor juntamente com medidas preventivas. A distribuição dos certificados de dispositivo deve ser incluída como parte desses processos de segurança. A frequência em que você acumula seus certificados dependerá das necessidades de segurança de sua solução. Os clientes com soluções que envolvem dados altamente confidenciais podem lançar o certificado diariamente, enquanto outros distribuem seus certificados a cada dois anos.

Os certificados de dispositivo sem interrupção envolverão a atualização do certificado armazenado no dispositivo e o Hub IoT. Posteriormente, o dispositivo pode se reprovisionar com o Hub IoT usando o [provisionamento automático](concepts-auto-provisioning.md) normal com o serviço de provisionamento de dispositivos.


## <a name="obtain-new-certificates"></a>Obter novos certificados

Há várias maneiras de obter novos certificados para seus dispositivos IoT. Isso inclui a obtenção de certificados da fábrica de dispositivos, a geração de seus próprios certificados e a existência de terceiros para gerenciar o certificado para você. 

Os certificados são assinados entre si para formar uma cadeia de confiança de um certificado de autoridade de certificação raiz para um [certificado de folha](concepts-security.md#end-entity-leaf-certificate). Um certificado de autenticação é o certificado usado para assinar o certificado de folha no final da cadeia de confiança. Um certificado de autenticação pode ser um certificado de autoridade de certificação raiz ou um certificado intermediário na cadeia de confiança. Para obter mais informações, consulte [certificados X. 509](concepts-security.md#x509-certificates).
 
Há duas maneiras diferentes de obter um certificado de autenticação. A primeira maneira, que é recomendada para sistemas de produção, é comprar um certificado de autenticação de uma autoridade de certificação raiz (CA). Dessa forma, o encadeia a segurança para uma fonte confiável. 

A segunda maneira é criar seus próprios certificados X. 509 usando uma ferramenta como a OpenSSL. Essa abordagem é excelente para testar certificados X. 509, mas fornece algumas garantias relacionadas à segurança. Recomendamos que você use essa abordagem apenas para teste, a menos que esteja preparado para atuar como seu próprio provedor de autoridade de certificação.
 

## <a name="roll-the-certificate-on-the-device"></a>Reverter o certificado no dispositivo

Os certificados em um dispositivo sempre devem ser armazenados em um local seguro, como um [HSM (módulo de segurança de hardware)](concepts-device.md#hardware-security-module). A maneira como você reverte os certificados de dispositivo dependerá de como eles foram criados e instalados nos dispositivos em primeiro lugar. 

Se você tiver seus certificados de terceiros, deverá examinar como eles distribuem seus certificados. O processo pode ser incluído em seu arranjo, ou pode ser um serviço separado que ele oferece. 

Se estiver gerenciando seus próprios certificados de dispositivo, você precisará criar seu próprio pipeline para atualizar certificados. Certifique-se de que os certificados de folha novos e antigos tenham o mesmo nome comum (CN). Com o mesmo CN, o dispositivo pode se reprovisionar sem criar um registro de registro duplicado. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Reverter o certificado no Hub IoT

O certificado do dispositivo pode ser adicionado manualmente a um hub IoT. O certificado também pode ser automatizado usando uma instância do serviço de provisionamento de dispositivos. Neste artigo, vamos supor que uma instância do serviço de provisionamento de dispositivos está sendo usada para dar suporte ao provisionamento automático.

Quando um dispositivo é inicialmente provisionado por meio do provisionamento automático, ele é inicializado e entra em contato com o serviço de provisionamento. O serviço de provisionamento responde executando uma verificação de identidade antes de criar uma identidade de dispositivo em um hub IoT usando o certificado de folha do dispositivo como a credencial. Em seguida, o serviço de provisionamento informa ao dispositivo a qual Hub IoT ele está atribuído, e o dispositivo usa seu certificado folha para autenticar e conectar-se ao Hub IoT. 

Depois que um novo certificado de folha for revertido para o dispositivo, ele não poderá mais se conectar ao Hub IoT porque ele está usando um novo certificado para se conectar. O Hub IoT só reconhece o dispositivo com o certificado antigo. O resultado da tentativa de conexão do dispositivo será um erro de conexão "não autorizado". Para resolver esse erro, você deve atualizar a entrada de registro para o dispositivo a fim de considerar o novo certificado de folha do dispositivo. Em seguida, o serviço de provisionamento pode atualizar as informações de registro do dispositivo do Hub IoT conforme necessário quando o dispositivo é reprovisionado. 

Uma possível exceção a essa falha de conexão seria um cenário em que você criou um [grupo de registro](concepts-service.md#enrollment-group) para seu dispositivo no serviço de provisionamento. Nesse caso, se você não estiver distribuindo os certificados raiz ou intermediários na cadeia de certificados de confiança do dispositivo, o dispositivo será reconhecido se o novo certificado fizer parte da cadeia de confiança definida no grupo de registro. Se esse cenário surgir como uma reação a uma violação de segurança, você deverá, pelo menos, adicionar à lista de bloqueios os certificados de dispositivo específicos no grupo que serão considerados violados. Para obter mais informações, consulte [dispositivos específicos da lista de bloqueios em um grupo de registro](https://docs.microsoft.com/azure/iot-dps/how-to-revoke-device-access-portal#blacklist-specific-devices-in-an-enrollment-group).

A atualização de entradas de registro para certificados acumulados é realizada na página **gerenciar registros** . Para acessar essa página, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até a instância do serviço de provisionamento de dispositivos do Hub IOT que tem a entrada de registro para seu dispositivo.

2. Clique em **Gerir inscrições**.

    ![Gerir inscrições](./media/how-to-roll-certificates/manage-enrollments-portal.png)


A maneira como você lida com a atualização da entrada de registro dependerá se você está usando registros individuais ou registros de grupo. Além disso, os procedimentos recomendados são diferentes, dependendo se você está sem interrupção de certificados devido a uma violação de segurança ou à expiração do certificado. As seções a seguir descrevem como lidar com essas atualizações.


## <a name="individual-enrollments-and-security-breaches"></a>Registros individuais e violações de segurança

Se você estiver sem interrupção de certificados em resposta a uma violação de segurança, deverá usar a seguinte abordagem que exclui o certificado atual imediatamente:

1. Clique em **registros individuais**e clique na entrada ID de registro na lista. 

2. Clique no botão **excluir certificado atual** e, em seguida, clique no ícone de pasta para selecionar o novo certificado a ser carregado para a entrada de registro. Clique em **salvar** quando terminar.

    Essas etapas devem ser concluídas para o certificado primário e o secundário, se ambos estiverem comprometidos.

    ![Gerenciar registros individuais](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Depois que o certificado comprometido tiver sido removido do serviço de provisionamento, o certificado ainda poderá ser usado para fazer conexões de dispositivo com o Hub IoT, desde que exista um registro de dispositivo para ele. Você pode abordar essas duas maneiras: 

    A primeira maneira seria navegar manualmente para o Hub IoT e remover imediatamente o registro do dispositivo associado ao certificado comprometido. Em seguida, quando o dispositivo for provisionado novamente com um certificado atualizado, um novo registro de dispositivo será criado.     

    ![Remover registro de dispositivo do Hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A segunda maneira seria usar o suporte de reprovisionamento para reprovisionar o dispositivo para o mesmo Hub IoT. Essa abordagem pode ser usada para substituir o certificado para o registro de dispositivo no Hub IoT. Para obter mais informações, consulte [como reprovisionar dispositivos](how-to-reprovision.md).

## <a name="individual-enrollments-and-certificate-expiration"></a>Registros individuais e expiração do certificado

Se você estiver sem interrupção de certificados para lidar com expirações de certificado, deverá usar a configuração de certificado secundário da seguinte maneira para reduzir o tempo de inatividade de dispositivos tentando provisionar.

Posteriormente, quando o certificado secundário também se aproximar da expiração e precisar ser revertido, você poderá girar para usar a configuração primária. A rotação entre os certificados primário e secundário dessa maneira reduz o tempo de inatividade dos dispositivos que tentam provisionar.


1. Clique em **registros individuais**e clique na entrada ID de registro na lista. 

2. Clique em **certificado secundário** e, em seguida, clique no ícone de pasta para selecionar o novo certificado a ser carregado para a entrada de registro. Clique em **Guardar**.

    ![Gerenciar registros individuais usando o certificado secundário](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Mais tarde, quando o certificado primário tiver expirado, volte e exclua esse certificado primário clicando no botão **excluir certificado atual** .

## <a name="enrollment-groups-and-security-breaches"></a>Grupos de registro e violações de segurança

Para atualizar um registro de grupo em resposta a uma violação de segurança, você deve usar uma das abordagens a seguir que excluirá a autoridade de certificação raiz atual ou o certificado intermediário imediatamente.

#### <a name="update-compromised-root-ca-certificates"></a>Atualizar certificados de AC raiz comprometidos

1. Clique na guia **certificados** para a instância do serviço de provisionamento de dispositivos.

2. Clique no certificado comprometido na lista e, em seguida, clique no botão **excluir** . Confirme a exclusão inserindo o nome do certificado e clique em **OK**. Repita esse processo para todos os certificados comprometidos.

    ![Excluir certificado de autoridade de certificação raiz](./media/how-to-roll-certificates/delete-root-cert.png)

3. Siga as etapas descritas em [configurar certificados de autoridade de certificação verificados](how-to-verify-certificates.md) para adicionar e verificar novos certificados de AC raiz.

4. Clique na guia **gerenciar registros** para a instância do serviço de provisionamento de dispositivos e clique na lista **grupos de registros** . Clique no nome do grupo de registro na lista.

5. Clique em **certificado de autoridade de certificação**e selecione seu novo certificado de autoridade de certificação raiz. Em seguida, clique em **Guardar**. 

    ![Selecione o novo certificado de autoridade de certificação raiz](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Depois que o certificado comprometido tiver sido removido do serviço de provisionamento, o certificado ainda poderá ser usado para fazer conexões de dispositivo com o Hub IoT, desde que os registros do dispositivo existam lá. Você pode abordar essas duas maneiras: 

    A primeira maneira seria navegar manualmente para o Hub IoT e remover imediatamente o registro do dispositivo associado ao certificado comprometido. Em seguida, quando seus dispositivos se provisionam novamente com certificados atualizados, um novo registro de dispositivo será criado para cada um.     

    ![Remover registro de dispositivo do Hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A segunda maneira seria usar o suporte de reprovisionamento para reprovisionar seus dispositivos para o mesmo Hub IoT. Essa abordagem pode ser usada para substituir certificados para registros de dispositivo no Hub IoT. Para obter mais informações, consulte [como reprovisionar dispositivos](how-to-reprovision.md).



#### <a name="update-compromised-intermediate-certificates"></a>Atualizar certificados intermediários comprometidos

1. Clique em **grupos de registro**e, em seguida, clique no nome do grupo na lista. 

2. Clique em **certificado intermediário**e **exclua certificado atual**. Clique no ícone de pasta para navegar até o novo certificado intermediário a ser carregado para o grupo de registro. Clique em **salvar** quando tiver terminado. Essas etapas devem ser concluídas para o certificado primário e o secundário, se ambos estiverem comprometidos.

    Esse novo certificado intermediário deve ser assinado por um certificado de autoridade de certificação raiz verificado que já foi adicionado ao serviço de provisionamento. Para obter mais informações, consulte [certificados X. 509](concepts-security.md#x509-certificates).

    ![Gerenciar registros individuais](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Depois que o certificado comprometido tiver sido removido do serviço de provisionamento, o certificado ainda poderá ser usado para fazer conexões de dispositivo com o Hub IoT, desde que os registros do dispositivo existam lá. Você pode abordar essas duas maneiras: 

    A primeira maneira seria navegar manualmente para o Hub IoT e remover imediatamente o registro do dispositivo associado ao certificado comprometido. Em seguida, quando seus dispositivos se provisionam novamente com certificados atualizados, um novo registro de dispositivo será criado para cada um.     

    ![Remover registro de dispositivo do Hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A segunda maneira seria usar o suporte de reprovisionamento para reprovisionar seus dispositivos para o mesmo Hub IoT. Essa abordagem pode ser usada para substituir certificados para registros de dispositivo no Hub IoT. Para obter mais informações, consulte [como reprovisionar dispositivos](how-to-reprovision.md).


## <a name="enrollment-groups-and-certificate-expiration"></a>Grupos de registro e expiração do certificado

Se você estiver sem interrupção de certificados para lidar com expirações de certificado, deverá usar a configuração de certificado secundário da seguinte maneira para garantir que não haja tempo de inatividade para os dispositivos que tentarem provisionar.

Posteriormente, quando o certificado secundário também se aproximar da expiração e precisar ser revertido, você poderá girar para usar a configuração primária. A rotação entre os certificados primário e secundário dessa maneira garante nenhum tempo de inatividade para os dispositivos que tentam provisionar. 

#### <a name="update-expiring-root-ca-certificates"></a>Atualizar certificados de autoridade de certificação raiz expirando

1. Siga as etapas descritas em [configurar certificados de autoridade de certificação verificados](how-to-verify-certificates.md) para adicionar e verificar novos certificados de AC raiz.

2. Clique na guia **gerenciar registros** para a instância do serviço de provisionamento de dispositivos e clique na lista **grupos de registros** . Clique no nome do grupo de registro na lista.

3. Clique em **certificado de autoridade de certificação**e selecione seu novo certificado de autoridade de certificação raiz na configuração de **certificado secundário** . Em seguida, clique em **Guardar**. 

    ![Selecione o novo certificado de autoridade de certificação raiz](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Mais tarde, quando o certificado primário tiver expirado, clique na guia **certificados** para a instância do serviço de provisionamento de dispositivos. Clique no certificado expirado na lista e, em seguida, clique no botão **excluir** . Confirme a exclusão inserindo o nome do certificado e clique em **OK**.

    ![Excluir certificado de autoridade de certificação raiz](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Atualizar certificados intermediários expirando


1. Clique em **grupos de registro**e clique no nome do grupo na lista. 

2. Clique em **certificado secundário** e, em seguida, clique no ícone de pasta para selecionar o novo certificado a ser carregado para a entrada de registro. Clique em **Guardar**.

    Esse novo certificado intermediário deve ser assinado por um certificado de autoridade de certificação raiz verificado que já foi adicionado ao serviço de provisionamento. Para obter mais informações, consulte [certificados X. 509](concepts-security.md#x509-certificates).

   ![Gerenciar registros individuais usando o certificado secundário](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Mais tarde, quando o certificado primário tiver expirado, volte e exclua esse certificado primário clicando no botão **excluir certificado atual** .


## <a name="reprovision-the-device"></a>Reprovisionar o dispositivo

Depois que o certificado é distribuído no dispositivo e no serviço de provisionamento de dispositivos, o dispositivo pode se provisionar novamente entrando em contato com o serviço de provisionamento de dispositivos. 

Uma maneira fácil de programar dispositivos para reprovisionar é programar o dispositivo para entrar em contato com o serviço de provisionamento para passar pelo fluxo de provisionamento se o dispositivo receber um erro "não autorizado" da tentativa de conexão com o Hub IoT.

Outra maneira é que os certificados antigos e novos sejam válidos para uma sobreposição curta e use o Hub IoT para enviar um comando aos dispositivos para que eles se registrem novamente por meio do serviço de provisionamento para atualizar as informações de conexão do Hub IoT. Como cada dispositivo pode processar comandos de forma diferente, você precisará programar seu dispositivo para saber o que fazer quando o comando for invocado. Há várias maneiras de você poder fazer o comando do dispositivo por meio do Hub IoT e é recomendável usar métodos ou [trabalhos](../iot-hub/iot-hub-devguide-jobs.md) [diretos](../iot-hub/iot-hub-devguide-direct-methods.md) para iniciar o processo.

Depois que o reprovisionamento for concluído, os dispositivos poderão se conectar ao Hub IoT usando seus novos certificados.


## <a name="blacklist-certificates"></a>Certificados de lista de bloqueios

Em resposta a uma violação de segurança, talvez seja necessário adicionar um certificado de dispositivo à lista de bloqueios. Para adicionar um certificado de dispositivo à lista de bloqueios, desabilite a entrada de registro para o dispositivo/certificado de destino. Para obter mais informações, consulte lista de bloqueios de dispositivos no artigo [gerenciar cancelamento de registro](how-to-revoke-device-access-portal.md) .

Depois que um certificado é incluído como parte de uma entrada de registro desabilitada, qualquer tentativa de registro com um hub IoT usando esses certificados falhará mesmo que ele esteja habilitado como parte de outra entrada de registro.
 



## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre os certificados X. 509 no serviço de provisionamento de dispositivos, consulte [segurança](concepts-security.md) 
- Para saber mais sobre como fazer a prova de posse para certificados de AC X. 509 com o serviço de provisionamento de dispositivos no Hub IoT do Azure, consulte [como verificar certificados](how-to-verify-certificates.md)
- Para saber mais sobre como usar o portal para criar um grupo de registro, consulte [Gerenciando registros de dispositivo com o portal do Azure](how-to-manage-enrollments.md).










