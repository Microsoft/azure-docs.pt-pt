---
title: Roll X.509 certificados em Azure IoT Central
description: Como rolar certificados X.509 com a sua Aplicação Central IoT
author: dominicbetts
ms.author: dobett
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c25af944b4c748307f4f974ca8616ecc9f7b28c3
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714529"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>Como rolar certificados de dispositivo X.509 na Aplicação Central IoT

Durante o ciclo de vida da sua solução IoT, terá de rolar certificados. Duas das principais razões para os certificados de rolamento seriam uma violação de segurança e expirações de certificados.

Se tiver uma falha de segurança, os certificados de rolamento são uma boa prática para ajudar a proteger o seu sistema. Como parte da Metodologia de Violação de [Assumi,](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)a Microsoft defende a necessidade de ter processos de segurança reativos em vigor, juntamente com medidas preventivas. Os certificados de rolamento do seu dispositivo devem ser incluídos como parte destes processos de segurança. A frequência em que os certificados derão dependem das necessidades de segurança da sua solução. Os clientes com soluções que envolvam dados altamente sensíveis podem lançar certificados diariamente, enquanto outros lançam os seus certificados de dois em dois anos.


## <a name="obtain-new-x509-certificates"></a>Obtenha novos certificados X.509

Pode criar os seus próprios certificados X.509 utilizando uma ferramenta como a OpenSSL. Esta abordagem é ótima para testar certificados X.509, mas fornece poucas garantias em torno da segurança. Utilize esta abordagem apenas para testes, a menos que esteja preparado para agir como seu próprio fornecedor de CA.

## <a name="enrollment-groups-and-security-breaches"></a>Grupos de inscrição e falhas de segurança

Para atualizar uma inscrição em grupo em resposta a uma falha de segurança, deve utilizar a seguinte abordagem que atualiza imediatamente o certificado atual:

1. Navegue para a **Administração**  no painel esquerdo e selecione **a ligação do dispositivo**.

2. Selecione **Grupos de Inscrição** e selecione o nome do grupo na lista.

3. Para atualização de certificados, **selecione Gerir o primário** ou gerir o **secundário**.

4. Adicione e verifique o certificado raiz X.509 no grupo de inscrição.

   Complete estes passos para os certificados primários e secundários, se ambos estiverem comprometidos.

## <a name="enrollment-groups-and-certificate-expiration"></a>Grupos de inscrição e expiração do certificado

Se estiver a rolar certificados para lidar com expirações de certificados, utilize a seguinte abordagem para atualizar imediatamente o certificado atual:

1. Navegue para a **Administração**  no painel esquerdo e selecione **a ligação do dispositivo**.

2. Selecione **Grupos de Inscrição** e selecione o nome do grupo na lista.

3. Para atualização de certificado, **selecione Gerir Principal**.

4. Adicione e verifique o certificado raiz X.509 no grupo de inscrição.

5. Mais tarde, quando o certificado secundário tiver expirado, volte e atualize o certificado secundário.

## <a name="individual-enrollments-and-security-breaches"></a>Inscrições individuais e falhas de segurança

Se estiver a rolar certificados em resposta a uma falha de segurança, utilize a seguinte abordagem para atualizar imediatamente o certificado atual:

1. Selecione **Dispositivos** e selecione o dispositivo.

2. Selecione **Connect**, e selecione o método de ligação como **Inscrição Individual**

3. Selecione **certificados (X.509)** como mecanismo.

    ![Gerir as matrículas individuais](./media/how-to-roll-x509-certificates/certificate-update.png)

4. Para a atualização do certificado, selecione o ícone da pasta para selecionar o novo certificado a carregar para a inscrição. Selecione **Guardar**.

    Complete estes passos para os certificados primários e secundários, se ambos estiverem comprometidos

## <a name="individual-enrollments-and-certificate-expiration"></a>Inscrições individuais e caducidade do certificado

Se estiver a rolar certificados para lidar com expirações de certificados, deve utilizar a configuração do certificado secundário da seguinte forma para reduzir o tempo de inatividade para dispositivos que tentam provisão.

Quando o certificado secundário se aproxima do termo e precisa de ser enrolado, pode rodar para usar a configuração primária. A rotação entre os certificados primário e secundário reduz assim o tempo de inatividade para os dispositivos que tentam provisão.

1. Selecione **Dispositivos** e selecione o dispositivo.

2. Selecione **Connect**, e selecione o método de ligação como **Inscrição Individual**

3. Selecione **certificados (X.509)** como mecanismo.

    ![Gerir as matrículas individuais](./media/how-to-roll-x509-certificates/certificate-update.png)

4. Para a atualização do certificado secundário, selecione o ícone da pasta para selecionar o novo certificado a carregar para a inscrição. Selecione **Guardar**.

5. Mais tarde, quando o certificado primário tiver expirado, volte e atualize o certificado primário.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a lançar certificados X.509 na sua aplicação Azure IoT Central, pode [ligar-se à Azure IoT Central](concepts-get-connected.md).


