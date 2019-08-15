---
title: Solução Azure VMware por CloudSimple-monitorar a atividade de nuvem privada
description: Descreve as informações disponíveis sobre a atividade na solução do Azure VMware por ambiente CloudSimple, incluindo alertas, eventos, tarefas e auditoria.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8c17bd203b7843de64734a74e7e41a22e42e3501
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991004"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>Monitorar a solução VMware por atividade de CloudSimple
 
Os logs de atividade do CloudSimple fornecem uma percepção das operações realizadas no portal do CloudSimple.  A lista inclui alertas, eventos, tarefas e auditoria.  Use os logs de atividade para determinar quem, quando e quais operações foram executadas.  Os logs de atividades não incluem nenhuma operação de leitura feita por um usuário.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Aceder ao portal da CloudSimple

Acesse o [portal do CloudSimple](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Informações da atividade

Para acessar as páginas de atividade, selecione **atividade** no menu lateral.

![Visão geral da página de atividade](media/activity-page-overview.png)

Para exibir detalhes sobre qualquer uma das atividades na página atividade, selecione a atividade. Um painel de detalhes é aberto à direita. As ações no painel dependem do tipo de atividade. Clique em **X** para fechar o painel.

Clique em um cabeçalho de coluna para classificar a exibição.  Você pode filtrar colunas para ver valores específicos.  Baixe o relatório de atividades clicando no ícone **baixar como CSV** .

## <a name="alerts"></a>Alertas
 
Alertas são notificações de qualquer atividade significativa em seu ambiente CloudSimple.  Os alertas incluem eventos que afetam a cobrança ou o acesso do usuário.

Para confirmar os alertas e removê-los da lista, selecione um ou mais na lista e clique em **reconhecer**. 

As colunas de informações a seguir estão disponíveis para alertas. Clique em **Editar colunas** e selecione as colunas que você deseja exibir.

| Coluna | Descrição | 
------------ | ------------- | 
| Tipo de alerta | Categoria do alerta.|
| Time | Hora em que o alerta ocorreu. |
| Severity | Significância do alerta.|
| Nome do Recurso | Nome atribuído ao recurso, como o nome da nuvem privada. | 
| Tipo de Recurso | Categoria do recurso: Nuvem privada, rack em nuvem. |
| ID de recurso | Identificador do recurso. |
| Descrição | Descrição do que disparou o alerta. |
| Reconhecido | Indica se o alerta é confirmado. |

## <a name="events"></a>Events

Eventos mostram a atividade de usuário e sistema no portal do CloudSimple. A página eventos lista a atividade associada a um recurso específico e a gravidade do impacto. 

As colunas de informações a seguir estão disponíveis para alertas. Clique em **Editar colunas** e selecione as colunas que você deseja exibir.

| Coluna | Descrição | 
------------ | ------------- | 
| Time | Data e hora em que o evento ocorreu. |
| Tipo de evento | Código numérico que identifica o evento. |
| Severity | Severidade do evento.|
| Nome do Recurso | Nome atribuído ao recurso, como o nome da nuvem privada. | 
| Tipo de Recurso | Categoria do recurso: Nuvem privada, rack em nuvem. |
| Descrição | Descrição do que disparou o alerta. |

## <a name="tasks"></a>Tarefas

As tarefas são atividades de nuvem privada que devem levar 30 segundos ou mais para serem concluídas. (As atividades que devem levar menos de 30 segundos são relatadas apenas como eventos.) Abra as páginas tarefas para acompanhar o progresso das tarefas de sua nuvem privada. 

As colunas de informações a seguir estão disponíveis para alertas. Clique em **Editar colunas** e selecione as colunas que você deseja exibir.

| Coluna | Descrição | 
------------ | ------------- | 
| ID da tarefa | Identificador exclusivo da tarefa. |
| Operação | Ação executada pela tarefa. |
| Utilizador | Usuário atribuído para concluir a tarefa. |
| Nome do Recurso | Nome atribuído ao recurso. |
| Tipo de Recurso | Categoria do recurso: Nuvem privada, rack em nuvem. |
| ID de recurso | Identificador do recurso. |
| Start | Hora de início da tarefa. |
| fim | Hora de término da tarefa. |
| Estado | Status da tarefa atual. |
| Tempo decorrido | Tempo que a tarefa levou para ser concluída (se estiver concluída) ou que está sendo executada (se em andamento). |
| Descrição | Descrição da tarefa. |

## <a name="audit"></a>Auditoria

Os logs de auditoria controlam a atividade do usuário. Você pode usar os logs de auditoria para monitorar a atividade do usuário para todos os usuários. 

As colunas de informações a seguir estão disponíveis para alertas. Clique em **Editar colunas** e selecione as colunas que você deseja exibir.

| Coluna | Descrição | 
------------ | ------------- | 
| Time | Hora da entrada de auditoria. |
| Operação | Ação executada pela tarefa. |
| Utilizador | Usuário atribuído à tarefa. |
| Nome do Recurso | Nome atribuído ao recurso. |
| Tipo de Recurso | Categoria do recurso: Nuvem privada, rack em nuvem. |
| ID de recurso | Identificador do recurso. |
| Resultado | Resultado da atividade, como **êxito**. |
| Tempo decorrido | Tempo para concluir a tarefa. |
| Descrição | Descrição da ação. |

## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)
