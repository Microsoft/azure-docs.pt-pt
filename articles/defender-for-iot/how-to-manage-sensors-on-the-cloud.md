---
title: Gerir sensores e subscrições no Portal Defender para IoT
description: Saiba como embarcar, ver e gerir sensores no portal Defender para IoT.
ms.date: 2/18/2021
ms.topic: how-to
ms.openlocfilehash: 5b4c8b3d10fe88816e07eb775b2bf3827d578b17
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383053"
---
# <a name="manage-sensors-and-subscriptions-in-the-defender-for-iot-portal"></a>Gerir sensores e subscrições no Portal Defender para IoT

Este artigo descreve como embarcar, visualizar e gerir sensores no [portal Defender para IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

## <a name="onboard-sensors"></a>Integrar sensores

Você está a bordo de um sensor registando-o com O Azure Defender para IoT e descarregando um ficheiro de ativação de sensores.

### <a name="register-the-sensor"></a>Registar o sensor

Para se registar:

1. Aceda à página **welcome** no [portal Defender for IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).
1. Selecione **sensor a bordo**.
1. Crie um nome de sensor. Recomendamos que inclua o endereço IP do sensor que instalou como parte do nome, ou utilize um nome facilmente identificável. Isto irá garantir um rastreio mais fácil e um nome consistente entre o nome de registo no portal Azure [Defender para IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) e o IP do sensor implantado exibido na consola sensor.
1. Associe o sensor a uma assinatura Azure.
1. Escolha um modo de gestão de sensores utilizando o toggle **ligado** cloud. Se o toggle estiver ligado, o sensor está ligado à nuvem. Se o toggle estiver desligado, o sensor é gerido localmente.

   - **Sensores ligados à nuvem**: A informação que o sensor deteta é apresentada na consola do sensor. As informações de alerta são entregues através de um hub IoT e podem ser partilhadas com outros serviços da Azure, como o Azure Sentinel.

   - **Sensores geridos localmente**: A informação que os sensores detetam é exibida na consola do sensor. Se estiver a trabalhar numa rede com lacunas de ar e quiser uma visão unificada de toda a informação detetada por vários sensores geridos localmente, trabalhe com a consola de gestão no local.

   Para sensores ligados à nuvem, o nome definido durante o embarque é o nome que aparece na consola do sensor. Não podes mudar este nome diretamente da consola. Para sensores geridos localmente, o nome aplicado durante o embarque será armazenado no Azure e pode ser atualizado na consola de sensores.

1. Escolha um hub IoT para servir de porta de entrada entre este sensor e o Azure Defender para IoT.
1. Se o sensor estiver ligado à nuvem, associe-o a um hub IoT e, em seguida, defina um nome e zona do site. Também pode adicionar tags descritivas. O nome, zona e etiquetas do site são entradas descritivas na [página de Sites e Sensores.](#view-onboarded-sensors)

### <a name="download-the-sensor-activation-file"></a>Descarregue o ficheiro de ativação do sensor

O ficheiro de ativação do sensor contém instruções sobre o modo de gestão do sensor. Descarrega um ficheiro de ativação único para cada sensor que implementa. Um utilizador que se inscreve na consola do sensor pela primeira vez envia o ficheiro de ativação para o sensor.

Para descarregar um ficheiro de ativação:

1. Na página sensor **de bordo,** selecione **o ficheiro de ativação de descarregamento**.
1. Tornar o ficheiro acessível ao utilizador que está a iniciar sessão na consola de sensores pela primeira vez.

## <a name="view-onboarded-sensors"></a>Ver sensores a bordo

No [portal Defender for IoT,](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)pode ver informações básicas sobre sensores a bordo.

1. Selecione **Sites e Sensores.**
1. Use ferramentas de filtro e pesquisa para encontrar informações de inteligência de sensor e ameaça de que necessita.

- Quantos sensores estavam a bordo
- O número de sensores que estão ligados à nuvem e geridos localmente
- O centro associado a um sensor a bordo
- Detalhes adicionados sobre um sensor, como o nome atribuído ao sensor durante o embarque, a zona associada ao sensor, ou outras informações descritivas adicionadas com tags

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

## <a name="offboard-a-subscription"></a>Offboard uma subscrição

As assinaturas são geridas mensalmente. Quando você não embarcar uma subscrição, você será cobrado para essa subscrição até o final do mês. 

Desinstale todos os sensores associados à subscrição antes de iniciar a subscrição. Para obter mais informações sobre como eliminar um sensor, consulte [Eliminar um sensor](#delete-a-sensor). 

Para offboard uma subscrição:

1. Navegue para a página **de preços.**
1. Selecione a subscrição e, em seguida, selecione o ícone **de eliminação** :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false"::: .
1. No popup de confirmação, selecione a caixa de verificação para confirmar que eliminou todos os sensores associados à subscrição.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="Selecione a caixa de verificação e selecione offboard para offboard o seu sensor.":::

1. Selecione o **botão Offboard.** 

O ambiente no local não é afetado, mas deve desinstalar o sensor do ambiente no local ou reatribuir o sensor a outra subscrição, de modo a evitar que quaisquer dados relacionados fluam para a consola de gestão no local. 

## <a name="next-steps"></a>Passos seguintes

[Ativar e configurar o seu sensor](how-to-activate-and-set-up-your-sensor.md)
