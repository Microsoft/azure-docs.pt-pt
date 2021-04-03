---
title: Certificados Roll X.509 no Serviço de Provisionamento de Dispositivos Azure IoT Hub
description: Como lançar certificados X.509 com a sua instância do Serviço de Provisionamento de Dispositivos (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: bf8b1e04e11dee4e636826430838a467fe034e3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94951133"
---
# <a name="how-to-roll-x509-device-certificates"></a>Como rolar certificados de dispositivo X.509

Durante o ciclo de vida da sua solução IoT, terá de rolar certificados. Duas das principais razões para os certificados de rolamento seriam uma violação de segurança e expirações de certificados. 

Os certificados de rolamento são uma boa prática de segurança para ajudar a proteger o seu sistema em caso de violação. Como parte da Metodologia de Violação de [Assumi,](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)a Microsoft defende a necessidade de ter processos de segurança reativos em vigor, juntamente com medidas preventivas. Os certificados de rolamento do seu dispositivo devem ser incluídos como parte destes processos de segurança. A frequência em que os certificados derão dependem das necessidades de segurança da sua solução. Os clientes com soluções que envolvam dados altamente sensíveis podem lançar certificados diariamente, enquanto outros lançam os seus certificados de dois em dois anos.

Os certificados de dispositivos rolantes implicarão a atualização do certificado armazenado no dispositivo e no hub IoT. Posteriormente, o dispositivo pode reprovisionar-se com o hub IoT utilizando [o fornecimento](about-iot-dps.md#provisioning-process) normal com o Serviço de Provisionamento de Dispositivos (DPS).


## <a name="obtain-new-certificates"></a>Obter novos certificados

Existem muitas formas de obter novos certificados para os seus dispositivos IoT. Estes incluem a obtenção de certificados da fábrica do dispositivo, a geração dos seus próprios certificados e a criação de certificados de terceiros para si. 

Os certificados são assinados entre si para formar uma cadeia de confiança de um certificado de CA raiz para um [certificado de folha.](concepts-x509-attestation.md#end-entity-leaf-certificate) Um certificado de assinatura é o certificado utilizado para assinar o certificado de folha no final da cadeia de confiança. Um certificado de assinatura pode ser um certificado de CA de raiz, ou um certificado intermédio em cadeia de confiança. Para mais informações, consulte [os certificados X.509.](concepts-x509-attestation.md#x509-certificates)
 
Há duas maneiras diferentes de obter um certificado de assinatura. A primeira forma, que é recomendada para os sistemas de produção, é adquirir um certificado de assinatura a uma autoridade de certificados de raiz (CA). Desta forma, acorrenta a segurança a uma fonte de confiança. 

A segunda forma é criar os seus próprios certificados X.509 usando uma ferramenta como a OpenSSL. Esta abordagem é ótima para testar certificados X.509, mas fornece poucas garantias em torno da segurança. Recomendamos que utilize esta abordagem apenas para testes, a menos que esteja preparado para agir como seu próprio fornecedor de CA.
 

## <a name="roll-the-certificate-on-the-device"></a>Rolar o certificado no dispositivo

Os certificados num dispositivo devem ser sempre guardados num local seguro como um [módulo de segurança de hardware (HSM)](concepts-service.md#hardware-security-module). A forma como os certificados dos dispositivos de rolamento dependerão da forma como foram criados e instalados nos dispositivos em primeiro lugar. 

Se obteve os seus certificados de terceiros, deve ver como eles rolam os certificados. O processo pode ser incluído no seu acordo com eles, ou pode ser um serviço separado que oferecem. 

Se estiver a gerir os certificados de dispositivo, terá de construir o seu próprio oleoduto para atualizar certificados. Certifique-se de que os certificados de folhas antigos e novos têm o mesmo nome comum (CN). Ao ter o mesmo CN, o dispositivo pode reprovisionar-se sem criar um registo duplicado. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Rolar o certificado no hub IoT

O certificado do dispositivo pode ser adicionado manualmente a um hub IoT. O certificado também pode ser automatizado utilizando uma instância de serviço de provisionamento de dispositivos. Neste artigo, assumimos que uma instância de serviço de provisionamento de dispositivos está a ser usada para apoiar o fornecimento automático.

Quando um dispositivo é inicialmente a provisionado através de provisões automáticas, ele arranca-se e contacta o serviço de fornecimento. O serviço de fornecimento responde realizando uma verificação de identidade antes de criar uma identidade do dispositivo num hub IoT usando o certificado de folha do dispositivo como credencial. O serviço de fornecimento diz então ao dispositivo a que o hub IoT está atribuído, e o dispositivo utiliza o seu certificado de folha para autenticar e ligar ao hub IoT. 

Uma vez que um novo certificado de folha foi enrolado no dispositivo, ele não pode mais ligar-se ao hub IoT porque está usando um novo certificado para ligar. O hub IoT só reconhece o dispositivo com o certificado antigo. O resultado da tentativa de ligação do dispositivo será um erro de ligação "não autorizado". Para resolver este erro, tem de atualizar a entrada de inscrição do dispositivo para ter em conta o novo certificado de folha do dispositivo. Em seguida, o serviço de fornecimento pode atualizar as informações de registo do dispositivo IoT Hub, conforme necessário, quando o dispositivo for reprovisionado. 

Uma possível exceção a esta falha de ligação seria um cenário em que criou um [Grupo de Inscrição](concepts-service.md#enrollment-group) para o seu dispositivo no serviço de fornecimento. Neste caso, se não estiver a rolar os certificados de raiz ou intermédios na cadeia de certificados do dispositivo, então o dispositivo será reconhecido se o novo certificado fizer parte da cadeia de confiança definida no grupo de inscrição. Se este cenário surgir como uma reação a uma falha de segurança, deve pelo menos não permitir os certificados específicos do dispositivo no grupo que são considerados violados. Para obter mais informações, consulte [Dispositivos específicos desprovidas de informação num grupo de matrículas.](./how-to-revoke-device-access-portal.md#disallow-specific-devices-in-an-enrollment-group)

A atualização das inscrições para certificados enrolados é realizada na página **de inscrições de Gestão.** Para aceder a esta página, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a instância do Serviço de Provisionamento de Dispositivos IoT Hub que tem a entrada de inscrição para o seu dispositivo.

2. Clique em **Gerir inscrições**.

    ![Gerir as matrículas](./media/how-to-roll-certificates/manage-enrollments-portal.png)


A forma como lida com a atualização da inscrição dependerá se estiver a utilizar as matrículas individuais ou as inscrições em grupo. Também os procedimentos recomendados diferem dependendo se você está rolando certificados devido a uma quebra de segurança, ou expiração do certificado. As seguintes secções descrevem como lidar com estas atualizações.


## <a name="individual-enrollments-and-security-breaches"></a>Inscrições individuais e falhas de segurança

Se estiver a rolar certificados em resposta a uma falha de segurança, deve utilizar a seguinte abordagem que elimina imediatamente o certificado atual:

1. Clique **em Inscrições Individuais** e clique na inscrição de ID de inscrição na lista. 

2. Clique no botão **de certificado de exclusão atual** e, em seguida, clique no ícone da pasta para selecionar o novo certificado a carregar para a entrada na inscrição. Clique **em Guardar** quando terminar.

    Estes passos devem ser preenchidos para o certificado primário e secundário, se ambos estiverem comprometidos.

    ![Gerir as matrículas individuais com uma quebra de segurança](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Uma vez removido o certificado comprometido do serviço de fornecimento, o certificado ainda pode ser utilizado para estoiá as ligações do dispositivo ao hub IoT, desde que exista um registo do dispositivo para o mesmo. Pode abordar esta questão de duas maneiras: 

    A primeira forma seria navegar manualmente para o seu hub IoT e remover imediatamente o registo do dispositivo associado ao certificado comprometido. Em seguida, quando o dispositivo voltar a providenciar um certificado atualizado, será criado um novo registo do dispositivo.     

    ![Remover o registo do dispositivo do hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A segunda forma seria utilizar o suporte de reprovisionamento para reprovisionar o dispositivo para o mesmo hub IoT. Esta abordagem pode ser utilizada para substituir o certificado para o registo do dispositivo no hub IoT. Para obter mais informações, consulte [Como reprovisionar dispositivos](how-to-reprovision.md).

## <a name="individual-enrollments-and-certificate-expiration"></a>Inscrições individuais e caducidade do certificado

Se estiver a rolar certificados para lidar com expirações de certificados, deve utilizar a configuração do certificado secundário da seguinte forma para reduzir o tempo de inatividade para dispositivos que tentam provisão.

Mais tarde, quando o certificado secundário também se aproxima da expiração e precisa de ser enrolado, pode rodar para usar a configuração primária. A rotação entre os certificados primário e secundário reduz assim o tempo de inatividade para os dispositivos que tentam provisão.


1. Clique **em Inscrições Individuais** e clique na inscrição de ID de inscrição na lista. 

2. Clique **em Certificado Secundário** e, em seguida, clique no ícone da pasta para selecionar o novo certificado a carregar para a entrada na inscrição. Clique em **Guardar**.

    ![Gerir as matrículas individuais utilizando o certificado secundário expirando](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Mais tarde, quando o certificado primário tiver expirado, volte e elimine o certificado primário clicando no botão **de certificado de exclusão atual.**

## <a name="enrollment-groups-and-security-breaches"></a>Grupos de inscrição e falhas de segurança

Para atualizar uma inscrição em grupo em resposta a uma falha de segurança, deve utilizar uma das seguintes abordagens que eliminará imediatamente a CA raiz atual, ou certificado intermédio.

#### <a name="update-compromised-root-ca-certificates"></a>Atualizar certificados de CA de raiz comprometidos

1. Clique no separador **Certificados** para a sua instância de serviço de Provisionamento de Dispositivos.

2. Clique no certificado comprometido na lista e, em seguida, clique no botão **Eliminar.** Confirme a eliminação introduzindo o nome do certificado e clique **em OK**. Repita este processo para todos os certificados comprometidos.

    ![Eliminar certificado de CA raiz](./media/how-to-roll-certificates/delete-root-cert.png)

3. Siga os passos descritos em [certificados de CA verificados](how-to-verify-certificates.md) para adicionar e verificar novos certificados de CA de raiz.

4. Clique no **separador 'Gerir' para** a sua instância de serviço de Provisionamento de Dispositivos e clique na lista **de Grupos de Inscrição.** Clique no nome do grupo de inscrição na lista.

5. Clique em **Certificado CA** e selecione o seu novo certificado de CA raiz. Em seguida, clique em **Guardar**. 

    ![Selecione o novo certificado de CA raiz para um certificado comprometido](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Uma vez removido o certificado comprometido do serviço de fornecimento, o certificado ainda pode ser utilizado para esquilá-lo para fazer ligações do dispositivo ao hub IoT, desde que existam registos de dispositivos para o mesmo. Pode abordar esta questão de duas maneiras: 

    A primeira forma seria navegar manualmente para o seu hub IoT e remover imediatamente o registo do dispositivo associado ao certificado comprometido. Em seguida, quando os seus dispositivos voltarem a provisão com certificados atualizados, será criado um novo registo do dispositivo para cada um deles.     

    ![Remover o registo do dispositivo do hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A segunda forma seria utilizar o suporte de reprovisionamento para reprovisionar os seus dispositivos para o mesmo hub IoT. Esta abordagem pode ser utilizada para substituir certificados para registos de dispositivos no hub IoT. Para obter mais informações, consulte [Como reprovisionar dispositivos](how-to-reprovision.md).



#### <a name="update-compromised-intermediate-certificates"></a>Atualizar certificados intermédios comprometidos

1. Clique **em Grupos de Inscrição** e, em seguida, clique no nome de grupo na lista. 

2. Clique **em Certificado Intermédio** e **elimine o certificado atual.** Clique no ícone da pasta para navegar para o novo certificado intermédio a ser carregado para o grupo de inscrição. Clique **em Guardar** quando terminar. Estes passos devem ser concluídos tanto para o certificado primário como para o secundário, se ambos estiverem comprometidos.

    Este novo certificado intermédio deve ser assinado por um certificado de CA de raiz verificado que já tenha sido adicionado ao serviço de prestação. Para mais informações, consulte [os certificados X.509.](concepts-x509-attestation.md#x509-certificates)

    ![Gerir as matrículas individuais para um intermediário comprometido](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Uma vez removido o certificado comprometido do serviço de fornecimento, o certificado ainda pode ser utilizado para esquilá-lo para fazer ligações do dispositivo ao hub IoT, desde que existam registos de dispositivos para o mesmo. Pode abordar esta questão de duas maneiras: 

    A primeira forma seria navegar manualmente para o seu hub IoT e remover imediatamente o registo do dispositivo associado ao certificado comprometido. Em seguida, quando os seus dispositivos voltarem a provisão com certificados atualizados, será criado um novo registo do dispositivo para cada um deles.     

    ![Remover o registo do dispositivo do hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A segunda forma seria utilizar o suporte de reprovisionamento para reprovisionar os seus dispositivos para o mesmo hub IoT. Esta abordagem pode ser utilizada para substituir certificados para registos de dispositivos no hub IoT. Para obter mais informações, consulte [Como reprovisionar dispositivos](how-to-reprovision.md).


## <a name="enrollment-groups-and-certificate-expiration"></a>Grupos de inscrição e expiração do certificado

Se estiver a rolar certificados para lidar com expirações de certificados, deve utilizar a configuração do certificado secundário da seguinte forma para garantir que não há tempo de inatividade para dispositivos que tentem provisões.

Mais tarde, quando o certificado secundário também se aproxima da expiração e precisa de ser enrolado, pode rodar para usar a configuração primária. A rotação entre os certificados primário e secundário desta forma garante que não há tempo de inatividade para os dispositivos que tentam provisões. 

#### <a name="update-expiring-root-ca-certificates"></a>Atualizar certificados de CA de raiz expirando

1. Siga os passos descritos em [certificados de CA verificados](how-to-verify-certificates.md) para adicionar e verificar novos certificados de CA de raiz.

2. Clique no **separador 'Gerir' para** a sua instância de serviço de Provisionamento de Dispositivos e clique na lista **de Grupos de Inscrição.** Clique no nome do grupo de inscrição na lista.

3. Clique em **Certificado CA** e selecione o seu novo certificado de CA de raiz sob a configuração do **Certificado Secundário.** Em seguida, clique em **Guardar**. 

    ![Selecione o novo certificado de CA raiz para expiração](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Mais tarde, quando o certificado primário tiver expirado, clique no **separador Certificados** para a sua instância de serviço de Provisionamento de Dispositivos. Clique no certificado expirado na lista e, em seguida, clique no botão **Eliminar.** Confirme a eliminação introduzindo o nome do certificado e clique **em OK**.

    ![Eliminar certificado de CA raiz](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Atualização de certificados intermédios caducados


1. Clique **em Grupos de Inscrição** e clique no nome de grupo na lista. 

2. Clique **em Certificado Secundário** e, em seguida, clique no ícone da pasta para selecionar o novo certificado a carregar para a entrada na inscrição. Clique em **Guardar**.

    Este novo certificado intermédio deve ser assinado por um certificado de CA de raiz verificado que já tenha sido adicionado ao serviço de prestação. Para mais informações, consulte [os certificados X.509.](concepts-x509-attestation.md#x509-certificates)

   ![Gerir os grupos de inscrição utilizando o certificado secundário caducado](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Mais tarde, quando o certificado primário tiver expirado, volte e elimine o certificado primário clicando no botão **de certificado de exclusão atual.**


## <a name="reprovision-the-device"></a>Reprovisionar o dispositivo

Uma vez que o certificado seja enrolado tanto no dispositivo como no Serviço de Provisionamento de Dispositivos, o dispositivo pode reprovisionar-se contactando o serviço de Provisionamento de Dispositivos. 

Uma forma fácil de reprovisionar os dispositivos é programar o dispositivo para entrar em contacto com o serviço de fornecimento para passar pelo fluxo de provisionamento se o dispositivo receber um erro "não autorizado" de tentar ligar-se ao hub IoT.

Outra forma é que tanto os antigos como os novos certificados sejam válidos para uma breve sobreposição, e use o hub IoT para enviar um comando aos dispositivos para que eles se re-registem através do serviço de fornecimento para atualizar as suas informações de conexão IoT Hub. Como cada dispositivo pode processar comandos de forma diferente, terá de programar o seu dispositivo para saber o que fazer quando o comando é invocado. Existem várias formas de comandar o seu dispositivo através do IoT Hub, e recomendamos a utilização de [métodos ou trabalhos diretos](../iot-hub/iot-hub-devguide-direct-methods.md) para iniciar o processo. [](../iot-hub/iot-hub-devguide-jobs.md)

Uma vez concluída a reprovisionamento, os dispositivos poderão ligar-se ao IoT Hub utilizando os seus novos certificados.


## <a name="disallow-certificates"></a>Não permitir certificados

Em resposta a uma falha de segurança, poderá ser necessário não permitir um certificado de dispositivo. Para não permitir um certificado de dispositivo, desative a entrada de inscrição para o dispositivo/certificado alvo. Para obter mais informações, consulte dispositivos de desativação no artigo [de desinscrição de gestão.](how-to-revoke-device-access-portal.md)

Uma vez que um certificado é incluído como parte de uma entrada de inscrição com deficiência, qualquer tentativa de registrar-se com um hub IoT usando esses certificados falhará mesmo que seja ativado como parte de outra inscrição.
 



## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre os certificados X.509 no Serviço de Provisionamento de Dispositivos, consulte o [atestado de certificado X.509](concepts-x509-attestation.md) 
- Para saber como fazer o comprovativo de posse dos certificados X.509 CA com o Serviço de Provisionamento de Dispositivos Azure IoT Hub, consulte [como verificar os certificados](how-to-verify-certificates.md)
- Para saber como usar o portal para criar um grupo de [inscrições, consulte gestão de inscrições de dispositivos com portal Azure.](how-to-manage-enrollments.md)