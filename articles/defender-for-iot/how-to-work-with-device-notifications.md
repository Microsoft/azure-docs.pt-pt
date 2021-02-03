---
title: Trabalhar com as notificações de dispositivo
description: As notificações fornecem informações sobre a atividade da rede que podem exigir a sua atenção, juntamente com recomendações para o manuseamento desta atividade.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: cac8d609272be1d9f34b7e0d6404e0a0ea524df7
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509036"
---
# <a name="work-with-device-notifications"></a>Trabalhar com as notificações de dispositivo

As notificações fornecem informações sobre a atividade da rede que podem exigir a sua atenção, juntamente com recomendações para o manuseamento desta atividade. Por exemplo, poderá receber uma notificação sobre:

- Um dispositivo inativo. Se o dispositivo deixar de fazer parte da sua rede, pode removê-lo. Se o dispositivo estiver inativo, por exemplo, por estar desligado da rede, reconecte o dispositivo e rejeite a notificação.

- Um endereço IP foi detetado num dispositivo que é atualmente identificado apenas por um endereço MAC. Responda autorizando o endereço IP para o dispositivo.

Responder a notificações melhora as informações fornecidas no mapa do dispositivo, inventário de dispositivos e consultas e relatórios de mineração de dados. Também fornece informações sobre alterações legítimas de rede e potenciais configurações de rede.

Para aceder a notificações:

- Selecione **Definições do Sistema** e, em seguida, selecione **A melhoria de dados**.

## <a name="notifications-vs-alerts"></a>Notificações vs. alertas

Além de receber notificações sobre a atividade da rede, poderá receber *alertas*. As notificações fornecem informações sobre alterações de rede ou propriedades de dispositivos não resolvidos que não representam uma ameaça. Os alertas fornecem informações sobre desvios de rede e alterações que podem representar uma ameaça para a rede.

Para ver notificações:

1. Selecione o mapa do **dispositivo** a partir do painel de menus esquerdo da consola.

2. Selecione o ícone **Notificações.** O número vermelho acima do ícone indica o número de notificações.

   :::image type="content" source="media/how-to-enrich-asset-information/notifications-alert-screenshot.png" alt-text="Ícone de notificação.":::

   A janela **Notificações** apresenta todas as notificações detetadas pelo sensor.

   :::image type="content" source="media/how-to-enrich-asset-information/notification-screen.png" alt-text="Notificações.":::

## <a name="filter-the-notifications-window"></a>Filtrar a janela notificações

Utilize filtros de pesquisa para apresentar notificações de interesse para si.

| Filtro por | Descrição |
|--|--|
| Filtro por tipo | Ver notificações que cobrem uma área de interesse específica. Por exemplo, consulte apenas notificações para dispositivos inativos. |
| Filtrar por intervalo de datas | Apresentar notificações que cobrem um intervalo de tempo específico. Por exemplo, ver notificações enviadas apenas na semana passada. |
| Procurar informações específicas | Procure notificações específicas. |

## <a name="notification-events-and-responses"></a>Eventos e respostas de notificação

A tabela seguinte descreve os tipos de eventos de notificação que poderá receber, juntamente com as opções de manipulação dos mesmos. Pode atualizar as informações do dispositivo com um valor recomendado ou rejeitar a notificação. Quando rejeita uma notificação, as informações do dispositivo não são atualizadas com as informações recomendadas. Se o tráfego for detetado novamente, a notificação será reenusada.

| Tipos de eventos de notificação | Descrição | Respostas |
|--|--|--|
| Novo IP detetado | Um novo endereço IP está associado ao dispositivo. Podem ser detetados cinco cenários: <br /><br /> Um endereço IP adicional foi associado a um dispositivo. Este dispositivo também está associado a um endereço MAC existente.<br /><br /> Foi detetado um novo endereço IP para um dispositivo que está a usar um endereço MAC existente. Atualmente, o dispositivo não comunica utilizando um endereço IP.<br /> <br /> Foi detetado um novo endereço IP para um dispositivo que está a usar um nome NetBIOS. <br /><br /> Foi detetado um endereço IP como interface de gestão de um dispositivo associado a um endereço MAC. <br /><br /> Foi detetado um novo endereço IP para um dispositivo que está a utilizar um endereço IP virtual. | **Definir IP adicional ao dispositivo** (dispositivos de fusão) <br /> <br />**Substituir IP existente** <br /> <br /> **Dispensar**<br /> Remova a notificação. |
| Dispositivos inativos | O trânsito não foi detetado num dispositivo há mais de 60 dias. | **Eliminar** <br /> Se este dispositivo não fizer parte da sua rede, retire-o. <br /><br />**Dispensar** <br /> Remova a notificação se o dispositivo fizer parte da sua rede. Se o dispositivo estiver inativo (por exemplo, por estar desligado erradamente da rede), dispense a notificação e reconecte o dispositivo. |
| Novos dispositivos OT | Uma sub-rede inclui um dispositivo OT que não está definido numa sub-rede ICS. <br /><br /> Cada sub-rede que contenha pelo menos um dispositivo OT pode ser definida como uma sub-rede ICS. Isto ajuda a diferenciar os dispositivos OT e IT no mapa. | **Definido como sub-rede ICS** <br /> <br /> **Dispensar** <br />Retire a notificação se o dispositivo não fizer parte da sub-rede. |
| Sem sub-redes configuradas | Não existem sub-redes atualmente configuradas na sua rede. <br /><br /> Configurar sub-redes para uma melhor representação no mapa e a capacidade de diferenciar os dispositivos OT e IT. | **Abrir sub-redes Configuração** e configurar sub-redes. <br /><br />**Dispensar** <br /> Remova a notificação. |
| Alterações no sistema operativo | Um ou mais novos sistemas operativos foram associados ao dispositivo. | Selecione o nome do novo SISTEMA que pretende associar ao dispositivo.<br /><br /> **Dispensar** <br /> Remova a notificação. |
| Novas sub-redes | Foram descobertas novas sub-redes. | **Learn**<br />Adicione automaticamente a sub-rede.<br />**Configuração de sub-rede aberta**<br />Adicione todas as informações de sub-redes em falta.<br />**Dispensar**<br />Remova a notificação. |
| Alterações do tipo de dispositivo | Foi associado um novo tipo de dispositivo ao dispositivo. | **Definir como {...}**<br />Associe o novo tipo ao dispositivo.<br />**Dispensar**<br />Remova a notificação. |

## <a name="respond-to-many-notifications-simultaneously"></a>Responder a muitas notificações simultaneamente

Poderá ser necessário lidar com várias notificações em simultâneo. Por exemplo:

- Se o TI fez uma atualização de SO para um grande conjunto de servidores de rede, pode instruir o sensor a aprender as novas versões do servidor para todos os servidores atualizados. 

- Se um grupo de dispositivos numa determinada linha foi eliminado e já não está ativo, pode instruir o sensor para remover estes dispositivos da consola.

Pode instruir o sensor a aplicar informações recentemente detetadas em vários dispositivos ou ignorá-la.   

Para apresentar notificações e tratar de notificações:

1. Utilize o **filtro por tipo,** a opção de intervalo de datas ou a opção **Select All.** Desmarcar notificações conforme necessário.

2. Instrua o sensor a aplicar informações recentemente detetadas em dispositivos selecionados selecionando **LEARN**. Ou, instrua o sensor a ignorar as informações recentemente detetadas selecionando **o DISMISS**. O número de notificações que pode simultaneamente aprender e dispensar, juntamente com o número de notificações que deve tratar individualmente, é mostrado.

**Novos IPs** e **sem sub-redes** configurados eventos não podem ser tratados simultaneamente. Requerem confirmação manual.

## <a name="improve-device-os-classification-data-enhancement"></a>Melhorar a classificação do SISTEMA do dispositivo: melhoria de dados 

O sensor abre continuamente a formação de novos dispositivos OT. Também abre automaticamente alterações em dispositivos previamente descobertos, incluindo tipos de sistema operativo.

Em certas circunstâncias, podem ser detetados conflitos em sistemas operativos descobertos. Isto pode acontecer porque tem uma versão SO que se refere a sistemas de desktop ou servidor. Se isso acontecer, receberá uma notificação com classificações opcionais de SO.

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="Melhore os dados.":::

Investigue as recomendações para enriquecer a classificação do SO. Esta informação aparece no inventário do dispositivo, relatórios de mineração de dados e outros ecrãs. Também pode melhorar a precisão de alertas, ameaças e análise de risco.

Quando aceitar uma recomendação, as informações do tipo SO serão atualizadas no sensor.

## <a name="see-also"></a>Ver também

[Ver alertas](how-to-view-alerts.md)
