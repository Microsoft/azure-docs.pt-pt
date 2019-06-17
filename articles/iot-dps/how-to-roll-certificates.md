---
title: Certificados de como implementar X.509 no serviço de aprovisionamento de dispositivos do Azure IoT Hub | Documentos da Microsoft
description: Como implementar certificados X.509 com a instância de serviço aprovisionamento de dispositivos
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 8cf5f262a758efe08ad73e2d8066ad4b736e76d1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60627041"
---
# <a name="how-to-roll-x509-device-certificates"></a>Como implementar certificados de dispositivo X.509

Durante o ciclo de vida da sua solução de IoT, terá de implementar certificados. Duas das principais razões para implementar certificados seria uma violação de segurança e expirações de certificado. 

Implementar certificados, é melhor prática de segurança para ajudar a proteger seu sistema em caso de uma violação. Como parte da [partem do princípio de metodologia de violação](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), a necessidade de ter processos de segurança reativo em vigor, juntamente com medidas preventivas consultores de Microsoft. Implementar os certificados de dispositivo deve ser incluído como parte desses processos de segurança. A freqüência na qual implementar os certificados irão depender as necessidades de segurança da sua solução. Os clientes com soluções que envolvem a dados altamente confidenciais sejam implementadas certificado diariamente, enquanto outros distribuir seus certificados cada dois anos.

Implementar certificados de dispositivo envolvem a atualizar o certificado armazenado no dispositivo e o hub IoT. Depois disso, o dispositivo pode reaprovisionar si próprio com o hub IoT com normal [aprovisionamento automático](concepts-auto-provisioning.md) com o serviço de aprovisionamento de dispositivos.


## <a name="obtain-new-certificates"></a>Obter novos certificados

Existem várias formas de obter novos certificados para os seus dispositivos IoT. Estes incluem a obtenção de certificados da fábrica do dispositivo, gerar seus próprios certificados e ter um de terceiros gerem a criação do certificado para si. 

Certificados são assinados por si para formar uma cadeia de confiança a partir de um certificado de AC de raiz para um [certificado de folha](concepts-security.md#end-entity-leaf-certificate). Um certificado de assinatura é o certificado utilizado para assinar os certificados de folha no final da cadeia de confiança. Um certificado de assinatura pode ser um certificado de AC de raiz ou um certificado intermédio na cadeia de confiança. Para obter mais informações, consulte [certificados X.509](concepts-security.md#x509-certificates).
 
Existem duas formas diferentes de obter um certificado de assinatura. A primeira maneira, o que é recomendada para sistemas de produção, é comprar um certificado de assinatura de uma autoridade de certificação de raiz (AC). Desta forma está ligado a segurança para baixo para uma origem fidedigna. 

A segunda maneira é criar seus próprios certificados X.509, usando uma ferramenta como OpenSSL. Essa abordagem é ótima para certificados X.509 de teste, mas oferece algumas garantias para segurança. Recomendamos que utilize apenas essa abordagem para testes, a menos que se preparou para agir como seu próprio provedor de AC.
 

## <a name="roll-the-certificate-on-the-device"></a>Implementar o certificado no dispositivo

Certificados num dispositivo sempre devem ser armazenados num local seguro, como um [módulo de segurança de hardware (HSM)](concepts-device.md#hardware-security-module). A forma como o implementar certificados de dispositivo dependerá como eles foram criados e instalados em dispositivos em primeiro lugar. 

Se tem os certificados de terceiros, deve examinar como o seus certificados que são implementadas. O processo pode ser incluído na sua organização com os mesmos, ou pode ser um serviço separado que oferecem. 

Se estiver a gerir o seus próprios certificados de dispositivo, terá de criar o seu próprio pipeline para a atualização de certificados. Certifique-se de que ambos os certificados de folha antigos e novos têm o mesmo nome comum (CN). Fazendo com que o mesmo CN, o dispositivo pode reaprovisionar em si sem criar um registo duplicado. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Implementar o certificado no IoT hub

O certificado do dispositivo pode ser adicionado manualmente para um hub IoT. O certificado também pode ser automatizado com uma instância de serviço aprovisionamento de dispositivos. Neste artigo, vamos supor que uma instância do serviço aprovisionamento de dispositivos está a ser utilizada para suportar o aprovisionamento automático.

Quando um dispositivo é inicialmente aprovisionado através do aprovisionamento automático, o que inicializações de segurança e entra em contacto com o serviço de aprovisionamento. O serviço de aprovisionamento responde ao efetuar uma verificação de identidade antes de criar uma identidade de dispositivo num hub IoT com o certificado de folha do dispositivo como a credencial. O serviço de aprovisionamento, em seguida, informa o dispositivo está atribuída a que hub IoT e, em seguida, o dispositivo utiliza o seu certificado de folha para autenticar e ligar ao IoT hub. 

Depois de um novo certificado leaf foi revertido para o dispositivo, ele já não pode ligar ao IoT hub porque está a utilizar um novo certificado para se ligar. O IoT hub reconhece apenas o dispositivo com o certificado antigo. O resultado da tentativa de ligação do dispositivo será um erro de ligação "não autorizado". Para resolver este erro, tem de atualizar a entrada de inscrição para o dispositivo para levar em conta o novo certificado de folha do dispositivo. Em seguida, o serviço de aprovisionamento pode atualizar as informações de registo de dispositivo IoT Hub conforme necessário quando o dispositivo é reaprovisionado. 

Uma possível exceção para esta falha de ligação seria um cenário em que criou um [grupo de inscrição](concepts-service.md#enrollment-group) para o seu dispositivo no serviço de aprovisionamento. Neste caso, se não estiver a implementar a raiz ou certificados intermédios na cadeia de certificados do dispositivo de confiança, em seguida, o dispositivo será reconhecido se o novo certificado faz parte da cadeia de confiança definida no grupo de inscrição. Se este cenário surge como reação a uma violação de segurança, deve, pelo menos, lista de proibições os certificados de dispositivo específico no grupo que são considerados como uma falha de segurança. Para obter mais informações, consulte [lista de bloqueio de dispositivos específicos num grupo de inscrição](https://docs.microsoft.com/azure/iot-dps/how-to-revoke-device-access-portal#blacklist-specific-devices-in-an-enrollment-group).

Atualizar as entradas de inscrição para certificados agregados é realizado no **gerir inscrições** página. Para aceder a essa página, siga estes passos:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue para a instância de serviço de aprovisionamento de dispositivos IoT Hub que tem a entrada de inscrição para o seu dispositivo.

2. Clique em **Gerir inscrições**.

    ![Gerir inscrições](./media/how-to-roll-certificates/manage-enrollments-portal.png)


Como lidar com a atualizar a entrada de inscrição dependerá se estiver a utilizar as inscrições individuais ou inscrições em grupo. Também os procedimentos recomendados ser diferente dependendo se está implantando certificados devido a uma violação de segurança ou expiração do certificado. As secções seguintes descrevem como lidar com essas atualizações.


## <a name="individual-enrollments-and-security-breaches"></a>Inscrições individuais e falhas de segurança

Se estiver a implementar certificados em resposta a uma violação de segurança, deve usar a seguinte abordagem que elimina o certificado atual imediatamente:

1. Clique em **inscrições individuais**e clique na entrada de ID de registo na lista. 

2. Clique nas **certificado atual de eliminar** botão e, em seguida, clique no ícone de pasta para selecionar o novo certificado ser carregado para a entrada de inscrição. Clique em **guardar** quando terminar.

    Estes passos devem ser concluídos para o certificado primário e secundário, se ambos forem comprometidas.

    ![Gerir inscrições individuais](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Depois do certificado comprometido foi removido do serviço de aprovisionamento, o certificado ainda pode ser usado para tornar as ligações de dispositivos no hub IoT, desde que um registo do dispositivo para que ele existe lá. Pode resolver isso duas formas: 

    A primeira maneira seria navegar manualmente para o seu hub IoT e remover imediatamente o registo do dispositivo associado ao certificado comprometido. Em seguida, quando o dispositivo novamente Aprovisiona com um certificado atualizado, será criado um novo registo do dispositivo.     

    ![Remover registo de dispositivos do hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A segunda forma seria utilizar o suporte de reprovisionamento para reaprovisionar o dispositivo no mesmo hub de IoT. Esta abordagem pode ser utilizada para substituir o certificado para o registo de dispositivos no IoT hub. Para obter mais informações, consulte [como reaprovisionar dispositivos](how-to-reprovision.md).

## <a name="individual-enrollments-and-certificate-expiration"></a>Inscrições individuais e a expiração do certificado

Se estiver a implementar certificados para processar as Expirações de certificado, deve utilizar a configuração do certificado secundário da seguinte forma para reduzir o período de indisponibilidade para dispositivos tentar aprovisionar.

Mais tarde ao secundário também certificado está prestes a expirar e tem de ser revertidos, pode girar para utilizar a configuração principal. Alternar entre os certificados primários e secundários desta forma reduz o período de indisponibilidade para dispositivos tentar aprovisionar.


1. Clique em **inscrições individuais**e clique na entrada de ID de registo na lista. 

2. Clique em **certificado secundário** e, em seguida, clique no ícone de pasta para selecionar o novo certificado ser carregado para a entrada de inscrição. Clique em **Guardar**.

    ![Gerir inscrições individuais através do certificado secundário](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Mais tarde quando o certificado principal tiver expirado, voltar atrás e eliminar esse certificado principal, clicando a **certificado atual de eliminar** botão.

## <a name="enrollment-groups-and-security-breaches"></a>Grupos de inscrição e falhas de segurança

Para atualizar uma inscrição de grupo em resposta a uma violação de segurança, deve usar uma das seguintes abordagens que irão eliminar a autoridade de certificação de raiz atual ou certificado intermédio imediatamente.

#### <a name="update-compromised-root-ca-certificates"></a>Atualizar certificados de AC de raiz comprometido

1. Clique nas **certificados** separador para a sua instância de serviço aprovisionamento de dispositivos.

2. Clique no certificado comprometido na lista e, em seguida, clique nas **eliminar** botão. Confirme a eliminação ao introduzir o nome do certificado e clique em **OK**. Repita este processo para todos os certificados comprometidos.

    ![Eliminar certificado de AC de raiz](./media/how-to-roll-certificates/delete-root-cert.png)

3. Siga os passos descritos em [verificado de configurar certificados de AC](how-to-verify-certificates.md) para adicionar e verificar novos certificados de AC de raiz.

4. Clique nas **gerir inscrições** separador para a sua instância de serviço aprovisionamento de dispositivos e clique nas **grupos de inscrição** lista. Clique no nome do grupo de inscrição na lista.

5. Clique em **certificado de AC**e selecione o novo certificado de AC de raiz. Em seguida, clique em **Guardar**. 

    ![Selecione o novo certificado de AC de raiz](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Depois do certificado comprometido foi removido do serviço de aprovisionamento, o certificado ainda pode ser usado para tornar as ligações de dispositivos no hub IoT, desde que os registos de dispositivos para que ele existe lá. Pode resolver isso duas formas: 

    A primeira maneira seria navegar manualmente para o seu hub IoT e remover imediatamente o registo do dispositivo associado ao certificado comprometido. Em seguida, quando seus dispositivos novamente aprovisionar com certificados atualizados, será criado um novo registo do dispositivo para cada um deles.     

    ![Remover registo de dispositivos do hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A segunda forma seria utilizar o suporte de reprovisionamento para reaprovisionar os seus dispositivos no hub IoT mesmo. Esta abordagem pode ser utilizada para substituir certificados para os registos de dispositivos no IoT hub. Para obter mais informações, consulte [como reaprovisionar dispositivos](how-to-reprovision.md).



#### <a name="update-compromised-intermediate-certificates"></a>Atualizar certificados intermédios comprometidos

1. Clique em **grupos de inscrição**e, em seguida, clique no nome de grupo na lista. 

2. Clique em **certificado intermédio**, e **certificado atual de eliminar**. Clique no ícone de pasta para navegar para o novo certificado intermédio para ser carregado para o grupo de inscrição. Clique em **guardar** quando tiver terminado. Estes passos devem ser concluídos para ambos os o certificado primário e secundário, se ambos forem comprometidas.

    Este novo certificado intermédio deve ser assinado por um certificado de AC de raiz verificado que já foi adicionado para o serviço de aprovisionamento. Para obter mais informações, consulte [certificados X.509](concepts-security.md#x509-certificates).

    ![Gerir inscrições individuais](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Depois do certificado comprometido foi removido do serviço de aprovisionamento, o certificado ainda pode ser usado para tornar as ligações de dispositivos no hub IoT, desde que os registos de dispositivos para que ele existe lá. Pode resolver isso duas formas: 

    A primeira maneira seria navegar manualmente para o seu hub IoT e remover imediatamente o registo do dispositivo associado ao certificado comprometido. Em seguida, quando seus dispositivos novamente aprovisionar com certificados atualizados, será criado um novo registo do dispositivo para cada um deles.     

    ![Remover registo de dispositivos do hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A segunda forma seria utilizar o suporte de reprovisionamento para reaprovisionar os seus dispositivos no hub IoT mesmo. Esta abordagem pode ser utilizada para substituir certificados para os registos de dispositivos no IoT hub. Para obter mais informações, consulte [como reaprovisionar dispositivos](how-to-reprovision.md).


## <a name="enrollment-groups-and-certificate-expiration"></a>Grupos de inscrição e de expiração do certificado

Se a implementar certificados para processar as Expirações de certificado, deve utilizar a configuração do certificado secundário da seguinte forma para garantir que nenhum tempo de inatividade para os dispositivos aprovisionar a tentar.

Mais tarde ao secundário também certificado está prestes a expirar e tem de ser revertidos, pode girar para utilizar a configuração principal. Alternar entre os certificados primários e secundários desta forma, garante que sem períodos de indisponibilidade para dispositivos tentar aprovisionar. 

#### <a name="update-expiring-root-ca-certificates"></a>Atualizar certificados de AC de raiz prestes a expirar

1. Siga os passos descritos em [verificado de configurar certificados de AC](how-to-verify-certificates.md) para adicionar e verificar novos certificados de AC de raiz.

2. Clique nas **gerir inscrições** separador para a sua instância de serviço aprovisionamento de dispositivos e clique nas **grupos de inscrição** lista. Clique no nome do grupo de inscrição na lista.

3. Clique em **certificado de AC**e selecione o novo certificado de AC de raiz sob a **certificado secundário** configuração. Em seguida, clique em **Guardar**. 

    ![Selecione o novo certificado de AC de raiz](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Mais tarde quando o certificado principal tiver expirado, clique nas **certificados** separador para a sua instância de serviço aprovisionamento de dispositivos. Clique no certificado expirado na lista e, em seguida, clique nas **eliminar** botão. Confirmar a eliminação de ao introduzir o nome do certificado e clique em **OK**.

    ![Eliminar certificado de AC de raiz](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Atualizar certificados intermédios prestes a expirar


1. Clique em **grupos de inscrição**e clique no nome do grupo na lista. 

2. Clique em **certificado secundário** e, em seguida, clique no ícone de pasta para selecionar o novo certificado ser carregado para a entrada de inscrição. Clique em **Guardar**.

    Este novo certificado intermédio deve ser assinado por um certificado de AC de raiz verificado que já foi adicionado para o serviço de aprovisionamento. Para obter mais informações, consulte [certificados X.509](concepts-security.md#x509-certificates).

   ![Gerir inscrições individuais através do certificado secundário](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Mais tarde quando o certificado principal tiver expirado, voltar atrás e eliminar esse certificado principal, clicando a **certificado atual de eliminar** botão.


## <a name="reprovision-the-device"></a>Reaprovisionar o dispositivo

Depois do certificado é implementado no dispositivo e o serviço de aprovisionamento de dispositivos, o dispositivo reaprovisionar em si pode contactar o serviço de aprovisionamento de dispositivos. 

É uma maneira fácil de programação de dispositivos para reaprovisionar a programar o dispositivo para contactar o serviço de aprovisionamento para percorrer o fluxo de aprovisionamento se o dispositivo recebe um erro "não autorizado" de tentar estabelecer ligação ao IoT hub.

Outra forma é para o antigo e os novos certificados ser válida para uma sobreposição de curta e utilizar o hub IoT para enviar um comando para dispositivos tê-los a voltar a registar através do serviço de aprovisionamento Atualize as informações de ligação do IoT Hub. Uma vez que cada dispositivo pode processar comandos de forma diferente, terá de programar o seu dispositivo para saber o que fazer quando o comando é invocado. Existem várias formas, pode comando seu dispositivo através do IoT Hub e recomendamos que utilize [métodos diretos](../iot-hub/iot-hub-devguide-direct-methods.md) ou [tarefas](../iot-hub/iot-hub-devguide-jobs.md) para iniciar o processo.

Depois de concluída a reprovisionamento, dispositivos será capazes de ligar ao IoT Hub com seus novos certificados.


## <a name="blacklist-certificates"></a>Certificados de lista de bloqueios

Em resposta a uma violação de segurança, poderá ter à lista de bloqueio de um certificado do dispositivo. Na lista de proibições um certificado do dispositivo, desative a entrada de inscrição para o dispositivo de destino/certificado. Para obter mais informações, consulte blacklisting dispositivos no [gerir a anulação de inscrições](how-to-revoke-device-access-portal.md) artigo.

Depois de um certificado está incluído como parte de uma entrada de inscrição desativado, todas as tentativas para registar com um hub IoT com que os certificados irão falhar, mesmo se estiver ativada como parte da entrada de inscrição de outro.
 



## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre certificados X.509 no serviço de aprovisionamento de dispositivos, veja [segurança](concepts-security.md) 
- Para saber mais sobre como fazer uma prova de posse de certificados X.509 de AC com o serviço de aprovisionamento de dispositivos do Azure IoT Hub, consulte o artigo [como verificar os certificados](how-to-verify-certificates.md)
- Para saber mais sobre como utilizar o portal para criar um grupo de inscrição, veja [gestão de inscrições de dispositivos com o portal do Azure](how-to-manage-enrollments.md).










