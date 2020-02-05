---
title: Azure VMware Solutions (AVS) - Monitor AVS Atividade privada cloud
description: Descreve as informações disponíveis sobre atividades na Solução Azure VMware por ambiente AVS, incluindo alertas, eventos, tarefas e auditoria.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5d6fd1b92db62ab7cc9edd47c601910b8148bb95
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019677"
---
# <a name="monitor-vmware-solutions-avs-activity"></a>Monitorizar a atividade das Soluções VMware (AVS)

Os registos de atividade da AVS fornecem uma visão das operações efetuadas no portal AVS. A lista inclui alertas, eventos, tarefas e auditoria. Use os logs de atividade para determinar quem, quando e quais operações foram executadas. Os logs de atividades não incluem nenhuma operação de leitura feita por um usuário.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Aceda ao portal AVS

Aceda ao [portal AVS.](access-cloudsimple-portal.md)

## <a name="activity-information"></a>Informações da atividade

Para acessar as páginas de atividade, selecione **atividade** no menu lateral.

![Visão geral da página de atividade](media/activity-page-overview.png)

Para exibir detalhes sobre qualquer uma das atividades na página atividade, selecione a atividade. Um painel de detalhes é aberto à direita. As ações no painel dependem do tipo de atividade. Clique em **X** para fechar o painel.

Clique em um cabeçalho de coluna para classificar a exibição. Você pode filtrar colunas para ver valores específicos. Baixe o relatório de atividades clicando no ícone **baixar como CSV** .

## <a name="alerts"></a>Alertas

Os alertas são notificações de qualquer atividade significativa no seu ambiente AVS. Os alertas incluem eventos que afetam a cobrança ou o acesso do usuário.

Para confirmar os alertas e removê-los da lista, selecione um ou mais na lista e clique em **reconhecer**.

As colunas de informações a seguir estão disponíveis para alertas. Clique em **Editar colunas** e selecione as colunas que você deseja exibir.

| Column | Descrição |
------------ | ------------- |
| Tipo de alerta | Categoria do alerta.|
| Tempo | Hora em que o alerta ocorreu. |
| Gravidade | Significância do alerta.|
| Nome do Recurso | Nome atribuído ao recurso, como o nome AVS Private Cloud. |
| Tipo de Recurso | Categoria de recurso: AVS Private Cloud, Cloud Rack. |
| ID do Recurso | Identificador do recurso. |
| Descrição | Descrição do que disparou o alerta. |
| Reconhecido | Indica se o alerta é confirmado. |

## <a name="events"></a>Eventos

Os eventos mostram a atividade do utilizador e do sistema no portal AVS. A página eventos lista a atividade associada a um recurso específico e a gravidade do impacto.

As colunas de informações a seguir estão disponíveis para alertas. Clique em **Editar colunas** e selecione as colunas que você deseja exibir.

| Column | Descrição |
------------ | ------------- |
| Tempo | Data e hora em que o evento ocorreu. |
| Tipo de evento | Código numérico que identifica o evento. |
| Gravidade | Severidade do evento.|
| Nome do Recurso | Nome atribuído ao recurso, como o nome AVS Private Cloud. |
| Tipo de Recurso | Categoria de recurso: AVS Private Cloud, Cloud Rack. |
| Descrição | Descrição do que disparou o alerta. |

## <a name="tasks"></a>Tarefas

As tarefas são atividades da AVS Private Cloud que deverão demorar 30 segundos ou mais a ser concluídas. (As atividades que deverão demorar menos de 30 segundos são reportadas apenas como eventos.) Abra as páginas Tasks para acompanhar o progresso das tarefas para a sua Nuvem Privada AVS.

As colunas de informações a seguir estão disponíveis para alertas. Clique em **Editar colunas** e selecione as colunas que você deseja exibir.

| Column | Descrição |
------------ | ------------- |
| ID de tarefa | Identificador exclusivo da tarefa. |
| Operação | Ação executada pela tarefa. |
| Utilizador | Usuário atribuído para concluir a tarefa. |
| Nome do Recurso | Nome atribuído ao recurso. |
| Tipo de Recurso | Categoria de recurso: AVS Private Cloud, Cloud Rack. |
| ID do Recurso | Identificador do recurso. |
| Iniciar | Hora de início da tarefa. |
| Terminar | Hora de término da tarefa. |
| Estado | Status da tarefa atual. |
| Tempo decorrido | Tempo que a tarefa levou para ser concluída (se estiver concluída) ou que está sendo executada (se em andamento). |
| Descrição | Descrição da tarefa. |

## <a name="audit"></a>Auditoria

Os logs de auditoria controlam a atividade do usuário. Você pode usar os logs de auditoria para monitorar a atividade do usuário para todos os usuários.

As colunas de informações a seguir estão disponíveis para alertas. Clique em **Editar colunas** e selecione as colunas que você deseja exibir.

| Column | Descrição |
------------ | ------------- |
| Tempo | Hora da entrada de auditoria. |
| Operação | Ação executada pela tarefa. |
| Utilizador | Usuário atribuído à tarefa. |
| Nome do Recurso | Nome atribuído ao recurso. |
| Tipo de Recurso | Categoria de recurso: AVS Private Cloud, Cloud Rack. |
| ID do Recurso | Identificador do recurso. |
| Resultado | Resultado da atividade, como **êxito**. |
| Tempo tomado | Tempo para concluir a tarefa. |
| Descrição | Descrição da ação. |

## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [as Nuvens Privadas aVS](cloudsimple-private-cloud.md)
