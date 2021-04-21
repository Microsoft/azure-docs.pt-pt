---
title: Gerir sensores no Portal Defender para IoT
description: Saiba como embarcar, ver e gerir sensores no portal Defender para IoT.
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: f407a65f60a1b969f17ebe00be39a888a09ec83d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752719"
---
# <a name="manage-sensors-in-the-defender-for-iot-portal"></a>Gerir sensores no Portal Defender para IoT

Este artigo descreve como embarcar, visualizar e gerir sensores no [portal Defender para IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

## <a name="onboard-sensors"></a>Integrar sensores

Você está a bordo de um sensor registando-o com O Azure Defender para IoT e descarregando um ficheiro de ativação de sensores.

### <a name="register-the-sensor"></a>Registar o sensor

Para se registar:

1. Aceda à página **welcome** no [portal Defender for IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Selecione **sensor a bordo**.
1. Crie um nome de sensor. Recomendamos que inclua o endereço IP do sensor que instalou como parte do nome, ou utilize um nome facilmente identificável. Isto irá garantir um rastreio mais fácil e um nome consistente entre o nome de registo no portal Azure [Defender para IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) e o IP do sensor implantado exibido na consola sensor.
1. Associe o sensor a uma assinatura Azure.
1. Escolha um modo de ligação do sensor utilizando o toggle **ligado** cloud. Se o toggle estiver ligado, o sensor está ligado à nuvem. Se o toggle estiver desligado, o sensor é gerido localmente.

   - **Sensores ligados à nuvem**: A informação que o sensor deteta é apresentada na consola do sensor. As informações de alerta são entregues através de um hub IoT e podem ser partilhadas com outros serviços da Azure, como o Azure Sentinel. Além disso, os pacotes de inteligência de ameaça podem ser empurrados do Azure Defender para o portal IoT para sensores. Inversamente, quando o sensor não está ligado à nuvem, tem de descarregar pacotes de inteligência de ameaças e depois enviá-los para os sensores da empresa. Para permitir que o Defender para ioT empurre pacotes para os sensores, ative as **atualizações automáticas de inteligência de ameaça.** Para obter mais informações, consulte [a pesquisa e pacotes de inteligência da Ameaça.](how-to-work-with-threat-intelligence-packages.md)
   Escolha um hub IoT para servir de porta de entrada entre este sensor e o Azure Defender para o portal IoT. Defina um nome e zona do site. Também pode adicionar tags descritivas. O nome, zona e etiquetas do site são entradas descritivas na [página de Sites e Sensores.](#view-onboarded-sensors)

   - **Sensores geridos localmente**: A informação que os sensores detetam é exibida na consola do sensor. Se estiver a trabalhar numa rede com lacunas de ar e quiser uma visão unificada de toda a informação detetada por vários sensores geridos localmente, trabalhe com a consola de gestão no local.

   Para sensores ligados à nuvem, o nome definido durante o embarque é o nome que aparece na consola do sensor. Não podes mudar este nome diretamente da consola. Para sensores geridos localmente, o nome aplicado durante o embarque será armazenado no Azure, mas pode ser atualizado na consola de sensores.

### <a name="download-the-sensor-activation-file"></a>Descarregue o ficheiro de ativação do sensor

O ficheiro de ativação do sensor contém instruções sobre o modo de gestão do sensor. Descarrega um ficheiro de ativação único para cada sensor que implementa. Um utilizador que se inscreve na consola do sensor pela primeira vez envia o ficheiro de ativação para o sensor.

Para descarregar um ficheiro de ativação:

1. Na página sensor **de bordo,** selecione **o ficheiro de ativação de descarregamento**.
1. Tornar o ficheiro acessível ao utilizador que está a iniciar sessão na consola de sensores pela primeira vez.

## <a name="view-onboarded-sensors"></a>Ver sensores a bordo

No [portal Defender para IoT,](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)pode ver informações operacionais importantes sobre sensores a bordo.

1. Selecione **Sites e Sensores.** A página mostra quantos sensores estavam a bordo, o número de sensores que estão ligados à nuvem e geridos localmente, bem como:

- o nome do sensor atribuído durante o embarque.
- o tipo de ligação (ligado à nuvem ou gerido localmente)
- a zona associada ao sensor.
- A versão sensor instalada
- O estado de ligação do sensor à nuvem.
- A última vez que o sensor foi detetado a ligar-se à nuvem.

## <a name="manage-onboarded-sensors"></a>Gerir sensores a bordo

Utilize o [portal Defender para IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) para tarefas de gestão relacionadas com sensores.

Os sensores a bordo podem ser vistos na página **'Sites e Sensores'.** Também pode editar informações de sensores a partir desta página.

### <a name="export-sensor-details"></a>Detalhes do sensor de exportação

Para exportar informações de sensores a bordo, selecione o ícone **Exportação** no topo da página **'Sites e Sensores'.**

### <a name="edit-sensor-zone-details"></a>Editar detalhes da zona do sensor

Utilize as opções **de edição de Sites e Sensores** para editar o nome e a zona do sensor.

Para editar:

1. Selecione a **elipse** **(...)** para o sensor que pretende editar.
1. Selecione **Editar**.
1. Atualize a zona do sensor ou crie uma nova zona.

### <a name="delete-a-sensor"></a>Apagar um sensor

Se eliminar um sensor ligado à nuvem, a informação não será enviada para o hub IoT. Elimine os sensores ligados localmente quando já não estiver a trabalhar com eles.

Para eliminar um sensor:

1. Selecione a elipse **(...**) para o sensor que pretende eliminar.
1. Confirme a eliminação.

### <a name="reactivate-a-sensor"></a>Reativar um sensor 

Pode ser necessário reativar o seu sensor porque pretende:

- **Trabalhar em modo ligado à nuvem em vez de modo gerido localmente**: Após a reativação, as deteções de sensores são apresentadas no sensor e as informações de alerta recentemente detetadas são entregues através do hub IoT. Esta informação pode ser partilhada com outros serviços da Azure, como o Azure Sentinel.

- **Trabalhar em modo gerido localmente em vez de modo ligado à nuvem**: Após a reativação, as informações de deteção do sensor são apresentadas apenas no sensor.

- **Associar o sensor a um novo hub IoT**: Para o fazer, re-registar o sensor com um novo hub e, em seguida, descarregar um novo ficheiro de ativação.

Para reativar um sensor:

1. Aceda à página **de Sites e Sensores** no [portal Defender para IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

2. Selecione o sensor para o qual pretende carregar um novo ficheiro de ativação.

3. Apague o sensor.

4. A bordo do sensor novamente no novo modo ou com um novo hub IoT selecionando a **bordo um sensor** a partir da página Iniciar.

5. Descarregue o ficheiro de ativação.

1. Inscreva-se no Defender para consola de sensores IoT.

7. Na consola do sensor, selecione **Definições do Sistema** e, em seguida, selecione **Reativação**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Faça o upload do seu ficheiro de ativação para reativar o sensor.":::

8. Selecione **Upload** e selecione o ficheiro guardado na página do sensor onboard.

9. **Selecione Ativar**.

## <a name="next-steps"></a>Passos seguintes

[Ativar e configurar o seu sensor](how-to-activate-and-set-up-your-sensor.md)
