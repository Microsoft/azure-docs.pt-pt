---
title: Roll X.509 certificados no Serviço de Provisionamento de Dispositivos Hub Azure IoT
description: Como enrolar certificados X.509 com a sua instância de Serviço de Provisionamento de Dispositivos (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 4d5ddb229cd6a41235990437bc0f8db08e3381ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74974892"
---
# <a name="how-to-roll-x509-device-certificates"></a>Como enrolar certificados de dispositivoX.509

Durante o ciclo de vida da sua solução IoT, terá de enrolar certificados. Dois dos principais motivos para os certificados de rolamento seriam uma violação de segurança e expirações de certificados. 

Os certificados de rolamento são uma boa prática de segurança para ajudar a proteger o seu sistema em caso de violação. Como parte da [Metodologia de Violação de Assume,](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)a Microsoft defende a necessidade de ter processos de segurança reativos em vigor juntamente com medidas preventivas. A reversão dos certificados do seu dispositivo deve ser incluída como parte destes processos de segurança. A frequência em que rola os seus certificados dependerá das necessidades de segurança da sua solução. Os clientes com soluções que envolvam dados altamente sensíveis podem rolar certificado diariamente, enquanto outros rolam os seus certificados de dois em dois anos.

Os certificados do dispositivo rolante envolverão a atualização do certificado armazenado no dispositivo e no hub IoT. Posteriormente, o dispositivo pode reabastecer-se com o hub IoT utilizando o fornecimento automático normal com o Serviço [de](concepts-auto-provisioning.md) Provisionamento de Dispositivos.


## <a name="obtain-new-certificates"></a>Obter novos certificados

Existem muitas formas de obter novos certificados para os seus dispositivos IoT. Estes incluem a obtenção de certificados da fábrica do dispositivo, a geração dos seus próprios certificados e a criação de um certificado de gestão de terceiros para si. 

Os certificados são assinados entre si para formar uma cadeia de confiança de um certificado de ac raiz para um [certificado de folha](concepts-security.md#end-entity-leaf-certificate). Um certificado de assinatura é o certificado utilizado para assinar o certificado de folha no final da cadeia de confiança. Um certificado de assinatura pode ser um certificado ca raiz, ou um certificado intermédio em cadeia de confiança. Para mais informações, consulte [os certificados X.509](concepts-security.md#x509-certificates).
 
Há duas maneiras diferentes de obter um certificado de assinatura. A primeira forma, recomendada para os sistemas de produção, é comprar um certificado de assinatura a uma autoridade de certificados de raiz (CA). Desta forma, a segurança das cadeias até uma fonte de confiança. 

A segunda forma é criar os seus próprios certificados X.509 usando uma ferramenta como a OpenSSL. Esta abordagem é ótima para testar certificados X.509, mas fornece poucas garantias em torno da segurança. Recomendamos que utilize apenas esta abordagem para testes, a menos que se prepare para agir como seu próprio fornecedor de CA.
 

## <a name="roll-the-certificate-on-the-device"></a>Rolar o certificado no dispositivo

Os certificados de um dispositivo devem ser sempre armazenados num local seguro como um módulo de segurança de [hardware (HSM)](concepts-device.md#hardware-security-module). A forma como rola os certificados do dispositivo dependerá da forma como foram criados e instalados nos dispositivos em primeiro lugar. 

Se recebeu os certificados de terceiros, tem de ver como eles fazem os certificados. O processo pode ser incluído no seu acordo com eles, ou pode ser um serviço separado que oferecem. 

Se estiver a gerir os seus próprios certificados de dispositivo, terá de construir o seu próprio oleoduto para atualizar certificados. Certifique-se de que os certificados de folhas antigos e novos têm o mesmo nome comum (CN). Ao ter o mesmo NC, o dispositivo pode reabastecer-se sem criar um registo duplicado. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Rolar o certificado no centro ioT

O certificado do dispositivo pode ser adicionado manualmente a um hub IoT. O certificado também pode ser automatizado utilizando uma instância de serviço de fornecimento de dispositivos. Neste artigo, assumimos que uma instância de serviço de fornecimento de dispositivos está a ser usada para apoiar o fornecimento automático.

Quando um dispositivo é inicialmente provisionado através do fornecimento automático, ele arranca e contacta o serviço de provisionamento. O serviço de provisionamento responde através da realização de uma verificação de identidade antes de criar uma identidade de dispositivo num hub IoT utilizando o certificado de folha do dispositivo como credencial. O serviço de provisionamento diz então ao dispositivo a que é atribuído o hub IoT, e o dispositivo utiliza o seu certificado de folha para autenticar e ligar ao hub IoT. 

Uma vez que um novo certificado de folha foi enrolado no dispositivo, ele não pode mais ligar-se ao hub IoT porque está usando um novo certificado para ligar. O hub IoT só reconhece o dispositivo com o certificado antigo. O resultado da tentativa de ligação do dispositivo será um erro de ligação "não autorizado". Para resolver este erro, tem de atualizar a entrada de inscrição do dispositivo para ter em conta o novo certificado de folha do dispositivo. Em seguida, o serviço de provisionamento pode atualizar as informações de registo do dispositivo IoT Hub, conforme necessário quando o dispositivo é reprovisionado. 

Uma possível exceção a esta falha de ligação seria um cenário em que criou um Grupo de [Inscrição](concepts-service.md#enrollment-group) para o seu dispositivo no serviço de provisionamento. Neste caso, se não estiver a rodar os certificados de raiz ou intermédios na cadeia de fidedignidade do dispositivo, o dispositivo será reconhecido se o novo certificado fizer parte da cadeia de confiança definida no grupo de matrículas. Se este cenário surgir como uma reação a uma falha de segurança, deve pelo menos colocar na lista negra os certificados específicos do dispositivo no grupo que são considerados violados. Para mais informações, consulte [dispositivos específicos da Blacklist num grupo de inscrições](https://docs.microsoft.com/azure/iot-dps/how-to-revoke-device-access-portal#blacklist-specific-devices-in-an-enrollment-group).

A atualização das inscrições para certificados enrolados é realizada na página **de 'Gerir' as matrículas.** Para aceder a essa página, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a instância ioT Hub Device Provisioning Service que tem a entrada de inscrição para o seu dispositivo.

2. Clique em **Gerir inscrições**.

    ![Gerir as matrículas](./media/how-to-roll-certificates/manage-enrollments-portal.png)


A forma como lida com a atualização da inscrição dependerá se está a usar matrículas individuais ou inscrições em grupo. Também os procedimentos recomendados diferem dependendo se você é certificados de rolamento por causa de uma violação de segurança, ou expiração do certificado. As seguintes secções descrevem como lidar com estas atualizações.


## <a name="individual-enrollments-and-security-breaches"></a>Matrículas individuais e falhas de segurança

Se estiver a enrolar certificados em resposta a uma falha de segurança, deve utilizar a seguinte abordagem que elimina imediatamente o certificado atual:

1. Clique em **Individual Registrations**, e clique na entrada de identificação de inscrição na lista. 

2. Clique no botão de **certificado atual Eliminar** e, em seguida, clique no ícone da pasta para selecionar o novo certificado a ser carregado para a entrada de inscrição. Clique em **Guardar** quando terminar.

    Estes passos devem ser concluídos para o certificado primário e secundário, se ambos estiverem comprometidos.

    ![Gerir as matrículas individuais](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Uma vez removido o certificado comprometido do serviço de provisionamento, o certificado ainda pode ser utilizado para efazer ligações do dispositivo ao hub IoT, desde que exista um registo do dispositivo para o mesmo. Pode abordar estas duas formas: 

    A primeira maneira seria navegar manualmente para o seu hub IoT e remover imediatamente o registo do dispositivo associado ao certificado comprometido. Em seguida, quando o dispositivo voltar a fornecer um certificado atualizado, será criado um novo registo do dispositivo.     

    ![Remover o registo do dispositivo do hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A segunda forma seria utilizar o suporte de reprovisionamento para reabastecer o dispositivo para o mesmo hub IoT. Esta abordagem pode ser utilizada para substituir o certificado para o registo do dispositivo no hub IoT. Para mais informações, consulte [Como reabastecer dispositivos](how-to-reprovision.md).

## <a name="individual-enrollments-and-certificate-expiration"></a>Inscrições individuais e caducidade do certificado

Se estiver a rodar certificados para lidar com as caducidade do certificado, deverá utilizar a configuração do certificado secundário da seguinte forma para reduzir o tempo de inatividade para dispositivos que tentem fornecer.

Mais tarde, quando o certificado secundário também se aproxima da expiração, e precisa de ser enrolado, pode rodar para utilizar a configuração primária. A rotação entre os certificados primários e secundários desta forma reduz o tempo de inatividade para os dispositivos que tentam fornecer.


1. Clique em **Individual Registrations**, e clique na entrada de identificação de inscrição na lista. 

2. Clique no **Certificado Secundário** e, em seguida, clique no ícone da pasta para selecionar o novo certificado a ser carregado para a entrada de inscrição. Clique em **Guardar**.

    ![Gerir as matrículas individuais utilizando o certificado secundário](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Mais tarde, quando o certificado primário tiver expirado, volte e elimine o certificado primário clicando no botão de **certificado atual Delete.**

## <a name="enrollment-groups-and-security-breaches"></a>Grupos de matrículas e falhas de segurança

Para atualizar uma inscrição em grupo em resposta a uma falha de segurança, deve utilizar uma das seguintes abordagens que eliminarão imediatamente a raiz atual ca, ou certificado intermédio.

#### <a name="update-compromised-root-ca-certificates"></a>Atualizar certificados ca raiz comprometidos

1. Clique no separador **Certificados** para a sua instância de serviço de fornecimento de dispositivos.

2. Clique no certificado comprometido na lista e, em seguida, clique no botão **Eliminar.** Confirme a eliminação inserindo o nome do certificado e clique **EM OK**. Repita este processo para todos os certificados comprometidos.

    ![Eliminar certificado ca raiz](./media/how-to-roll-certificates/delete-root-cert.png)

3. Siga os passos descritos nos [certificados de CA verificados configurando](how-to-verify-certificates.md) para adicionar e verificar novos certificados ca raiz.

4. Clique no separador **'Gerir as inscrições'** para a sua instância de serviço de fornecimento de dispositivos e clique na lista dos **Grupos de Inscrição.** Clique no nome do seu grupo de matrículas na lista.

5. Clique no **Certificado CA**e selecione o seu novo certificado CA raiz. Em seguida, clique em **Guardar**. 

    ![Selecione o novo certificado ca raiz](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Uma vez removido o certificado comprometido do serviço de provisionamento, o certificado ainda pode ser utilizado para efazer ligações do dispositivo ao hub IoT, desde que existam registos do dispositivo para o mesmo. Pode abordar estas duas formas: 

    A primeira maneira seria navegar manualmente para o seu hub IoT e remover imediatamente o registo do dispositivo associado ao certificado comprometido. Em seguida, quando os seus dispositivos voltarem a fornecer com certificados atualizados, será criado um novo registo de dispositivos para cada um deles.     

    ![Remover o registo do dispositivo do hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A segunda forma seria utilizar o suporte de reprovisionamento para reabastecer os seus dispositivos para o mesmo hub IoT. Esta abordagem pode ser utilizada para substituir certificados para registos de dispositivos no centro IoT. Para mais informações, consulte [Como reabastecer dispositivos](how-to-reprovision.md).



#### <a name="update-compromised-intermediate-certificates"></a>Atualizar certificados intermédios comprometidos

1. Clique em **Grupos de Inscrição**e, em seguida, clique no nome do grupo na lista. 

2. Clique em **Certificado Intermédio**e **Elimine o certificado atual.** Clique no ícone da pasta para navegar para o novo certificado intermédio a ser carregado para o grupo de inscrições. Clique em **Guardar** quando terminar. Estes passos devem ser concluídos tanto para o certificado primário como secundário, se ambos estiverem comprometidos.

    Este novo certificado intermédio deve ser assinado por um certificado de acraiz verificado que já tenha sido adicionado ao serviço de provisionamento. Para mais informações, consulte [os certificados X.509](concepts-security.md#x509-certificates).

    ![Gerir as matrículas individuais](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Uma vez removido o certificado comprometido do serviço de provisionamento, o certificado ainda pode ser utilizado para efazer ligações do dispositivo ao hub IoT, desde que existam registos do dispositivo para o mesmo. Pode abordar estas duas formas: 

    A primeira maneira seria navegar manualmente para o seu hub IoT e remover imediatamente o registo do dispositivo associado ao certificado comprometido. Em seguida, quando os seus dispositivos voltarem a fornecer com certificados atualizados, será criado um novo registo de dispositivos para cada um deles.     

    ![Remover o registo do dispositivo do hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A segunda forma seria utilizar o suporte de reprovisionamento para reabastecer os seus dispositivos para o mesmo hub IoT. Esta abordagem pode ser utilizada para substituir certificados para registos de dispositivos no centro IoT. Para mais informações, consulte [Como reabastecer dispositivos](how-to-reprovision.md).


## <a name="enrollment-groups-and-certificate-expiration"></a>Grupos de matrículas e caducidade do certificado

Se estiver a rodar certificados para manusear as caducidade do certificado, deverá utilizar a configuração do certificado secundário da seguinte forma para garantir que não há tempo de inatividade para os dispositivos que tentem fornecer.

Mais tarde, quando o certificado secundário também se aproxima da expiração, e precisa de ser enrolado, pode rodar para utilizar a configuração primária. A rotação entre os certificados primários e secundários desta forma não garante qualquer tempo de inatividade para os dispositivos que tentam fornecer. 

#### <a name="update-expiring-root-ca-certificates"></a>Atualizar certificados ca raiz expirando

1. Siga os passos descritos nos [certificados de CA verificados configurando](how-to-verify-certificates.md) para adicionar e verificar novos certificados ca raiz.

2. Clique no separador **'Gerir as inscrições'** para a sua instância de serviço de fornecimento de dispositivos e clique na lista dos **Grupos de Inscrição.** Clique no nome do seu grupo de matrículas na lista.

3. Clique no **Certificado CA**e selecione o seu novo certificado CA raiz sob a configuração do **Certificado Secundário.** Em seguida, clique em **Guardar**. 

    ![Selecione o novo certificado ca raiz](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Mais tarde, quando o certificado primário tiver expirado, clique no separador **Certificados** para a sua instância de serviço de provisionamento de dispositivos. Clique no certificado expirado na lista e, em seguida, clique no botão **Eliminar.** Confirme a eliminação inserindo o nome do certificado e clique em **OK**.

    ![Eliminar certificado ca raiz](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Atualização dos certificados intermédios de caducidade


1. Clique em **Grupos de Inscrição**e clique no nome do grupo na lista. 

2. Clique no **Certificado Secundário** e, em seguida, clique no ícone da pasta para selecionar o novo certificado a ser carregado para a entrada de inscrição. Clique em **Guardar**.

    Este novo certificado intermédio deve ser assinado por um certificado de acraiz verificado que já tenha sido adicionado ao serviço de provisionamento. Para mais informações, consulte [os certificados X.509](concepts-security.md#x509-certificates).

   ![Gerir as matrículas individuais utilizando o certificado secundário](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Mais tarde, quando o certificado primário tiver expirado, volte e elimine o certificado primário clicando no botão de **certificado atual Delete.**


## <a name="reprovision-the-device"></a>Reprovisionamento do dispositivo

Uma vez que o certificado seja enrolado tanto no dispositivo como no Serviço de Fornecimento de Dispositivos, o dispositivo pode reabastecer-se contactando o serviço de fornecimento de dispositivos. 

Uma forma fácil de programar dispositivos para reprovisionamento é programar o dispositivo para contactar o serviço de fornecimento para passar pelo fluxo de provisionamento se o dispositivo receber um erro "não autorizado" de tentar ligar-se ao hub IoT.

Outra forma é que tanto os certificados antigos como os novos certificados sejam válidos para uma sobreposição curta, e utilizem o hub IoT para enviar um comando aos dispositivos para que se reregistem através do serviço de provisionamento para atualizar as suas informações de ligação IoT Hub. Como cada dispositivo pode processar comandos de forma diferente, terá de programar o seu dispositivo para saber o que fazer quando o comando é invocado. Existem várias formas de comandar o seu dispositivo através do IoT Hub, e recomendamos a utilização de [métodos ou trabalhos diretos](../iot-hub/iot-hub-devguide-direct-methods.md) para iniciar o processo. [jobs](../iot-hub/iot-hub-devguide-jobs.md)

Uma vez concluída a reprovisionamento, os dispositivos poderão ligar-se ao IoT Hub utilizando os seus novos certificados.


## <a name="blacklist-certificates"></a>Certificados da lista negra

Em resposta a uma falha de segurança, poderá ter de colocar na lista negra um certificado de dispositivo. Para colocar na lista negra um certificado de dispositivo, desative a inscrição do dispositivo/certificado alvo. Para mais informações, consulte os dispositivos de blacklist no artigo 'Gerir a [desinscrição'.](how-to-revoke-device-access-portal.md)

Uma vez que um certificado é incluído como parte de uma inscrição para deficientes, quaisquer tentativas de registo com um hub IoT usando que os certificados falharão mesmo que seja ativado como parte de outra inscrição.
 



## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre os certificados X.509 no Serviço de Provisionamento de Dispositivos, consulte [Security](concepts-security.md) 
- Para saber como fazer a prova de posse dos certificados X.509 CA com o Serviço de Provisionamento de Dispositivos Hub Azure IoT, consulte [como verificar certificados](how-to-verify-certificates.md)
- Para saber como usar o portal para criar um grupo de inscrições, consulte a Gestão de [inscrições de dispositivos com o portal Azure.](how-to-manage-enrollments.md)










