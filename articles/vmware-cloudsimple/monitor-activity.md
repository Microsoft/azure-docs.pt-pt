---
title: Monitorize a atividade da Nuvem Privada
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve a informação disponível sobre a atividade no ambiente Azure VMware Solution by CloudSimple, incluindo alertas, eventos, tarefas e auditoria.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fb597cc71d228a1daaaa4e52d76b301764ff9292
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899308"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>Monitor VMware Solution by CloudSimple atividade

Os registos de atividades cloudSimple fornecem uma visão das operações feitas no portal CloudSimple.  A lista inclui alertas, eventos, tarefas e auditoria.  Use os registos de atividade para determinar quem, quando e que operações foram realizadas.  Os registos de atividade não incluem quaisquer operações de leitura efetuadas por um utilizador.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Aceder ao portal da CloudSimple

Aceda ao [portal CloudSimple](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Informação sobre atividades

Para aceder às páginas 'Atividades', selecione **Atividade** no menu lateral.

![Visão geral da página da atividade](media/activity-page-overview.png)

Para ver detalhes sobre qualquer uma das atividades na página de atividades, selecione a atividade. Um painel de detalhes abre à direita. As ações no painel dependem do tipo de atividade. Clique em **X** para fechar o painel.

Clique num cabeçalho de coluna para classificar o visor.  Pode filtrar colunas para valores específicos a visualizar.  Baixe o relatório de atividade clicando no Download como ícone **CSV.**

## <a name="alerts"></a>Alertas

Os alertas são notificações de qualquer atividade significativa no seu ambiente CloudSimple.  Os alertas incluem eventos que afetam a faturação ou o acesso ao utilizador.

Para reconhecer os alertas e removê-los da lista, selecione um ou mais da lista e clique em **Reconhecer**.

As seguintes colunas de informação estão disponíveis para alertas. Clique em **Editar colunas** e selecione colunas que pretende visualizar.

| Coluna | Descrição |
------------ | ------------- |
| Tipo de alerta | Categoria de alerta.|
| Hora | Hora do alerta. |
| Gravidade | Significado do alerta.|
| Nome do Recurso | Nome atribuído ao recurso, como o nome Private Cloud. |
| Tipo de Recurso | Categoria de recurso: Nuvem Privada, Cloud Rack. |
| ID do Recurso | Identificador do recurso. |
| Descrição | Descrição do que desencadeou o alerta. |
| Confirmado | Indicação de se o alerta é reconhecido. |

## <a name="events"></a>Eventos

Os eventos mostram a atividade do utilizador e do sistema no portal CloudSimple. A página Eventos lista a atividade associada a um recurso específico e a gravidade do impacto.

As seguintes colunas de informação estão disponíveis para alertas. Clique em **Editar colunas** e selecione colunas que pretende visualizar.

| Coluna | Descrição |
------------ | ------------- |
| Hora | Data e hora do evento ocorreu. |
| Tipo de Evento | Código numérico que identifica o evento. |
| Gravidade | Gravidade do evento.|
| Nome do Recurso | Nome atribuído ao recurso, como o nome Private Cloud. |
| Tipo de Recurso | Categoria de recurso: Nuvem Privada, Cloud Rack. |
| Descrição | Descrição do que desencadeou o alerta. |

## <a name="tasks"></a>Tarefas

As tarefas são atividades private Cloud que se espera que levem 30 segundos ou mais para serem concluídas. (As atividades que se espera que dedem menos de 30 segundos são reportadas apenas como eventos.) Abra as páginas Tasks para acompanhar o progresso das tarefas para a sua Nuvem Privada.

As seguintes colunas de informação estão disponíveis para alertas. Clique em **Editar colunas** e selecione colunas que pretende visualizar.

| Coluna | Descrição |
------------ | ------------- |
| ID de tarefa | Identificador único para a tarefa. |
| Operação | Ação que a tarefa executa. |
| Utilizador | Utilizador designado para completar a tarefa. |
| Nome do Recurso | Nome atribuído ao recurso. |
| Tipo de Recurso | Categoria de recurso: Nuvem Privada, Cloud Rack. |
| ID do Recurso | Identificador do recurso. |
| Iniciar | Hora de início para a tarefa. |
| Fim | Fim do tempo para a tarefa. |
| Estado | Estado atual da tarefa. |
| Tempo Decorrido | Tempo que a tarefa demorou a completar (se concluída) ou está atualmente a demorar (se em curso). |
| Descrição | Descrição da tarefa. |

## <a name="audit"></a>Auditoria

Os registos de auditoria acompanham a atividade do utilizador. Pode utilizar registos de auditoria para monitorizar a atividade do utilizador para todos os utilizadores.

As seguintes colunas de informação estão disponíveis para alertas. Clique em **Editar colunas** e selecione colunas que pretende visualizar.

| Coluna | Descrição |
------------ | ------------- |
| Hora | Hora da entrada na auditoria. |
| Operação | Ação que a tarefa executa. |
| Utilizador | Utilizador designado para a tarefa. |
| Nome do Recurso | Nome atribuído ao recurso. |
| Tipo de Recurso | Categoria de recurso: Nuvem Privada, Cloud Rack. |
| ID do Recurso | Identificador do recurso. |
| Resultado | Resultado da atividade, como o **Sucesso.** |
| Tempo Decorrido | Hora de completar a tarefa. |
| Descrição | Descrição da ação. |

## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [Nuvens Privadas](cloudsimple-private-cloud.md)
