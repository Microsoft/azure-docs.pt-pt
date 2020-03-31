---
title: Monitorizar a atividade da Nuvem Privada
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve a informação disponível sobre a atividade no ambiente Azure VMware by CloudSimple, incluindo alertas, eventos, tarefas e auditoria.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1228f3a54e02d4fe7a5133e2bfba55c38e34718a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019677"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>Monitor VMware Solution by CloudSimple atividade

Os registos de atividade da CloudSimple fornecem uma visão das operações efetuadas no portal CloudSimple.  A lista inclui alertas, eventos, tarefas e auditoria.  Utilize os registos de atividade para determinar quem, quando e que operações foram realizadas.  Os registos de atividade não incluem quaisquer operações de leitura efetuadas por um utilizador.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em .

## <a name="access-the-cloudsimple-portal"></a>Aceder ao portal da CloudSimple

Aceda ao [portal CloudSimple](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Informação sobre atividades

Para aceder às páginas de Atividade, selecione **Atividade** no menu lateral.

![Visão geral da página de atividade](media/activity-page-overview.png)

Para ver detalhes sobre qualquer uma das atividades na página de atividade, selecione a atividade. Um painel de detalhes abre à direita. As ações no painel dependem do tipo de atividade. Clique em **X** para fechar o painel.

Clique num cabeçalho de coluna para classificar o visor.  Pode filtrar colunas para valores específicos para visualizar.  Descarregue o relatório de atividade clicando no Download como ícone **CSV.**

## <a name="alerts"></a>Alertas

Os alertas são notificações de qualquer atividade significativa no seu ambiente CloudSimple.  Os alertas incluem eventos que afetam a faturação ou o acesso ao utilizador.

Para reconhecer alertas e removê-los da lista, selecione um ou mais da lista e clique em **Reconhecer**.

As seguintes colunas de informação estão disponíveis para alertas. Clique nas **colunas Editar** e selecione colunas que pretende ver.

| Coluna | Descrição |
------------ | ------------- |
| Tipo de alerta | Categoria de alerta.|
| Hora | Hora do alerta ocorrer. |
| Gravidade | Significado do alerta.|
| Nome do Recurso | Nome atribuído ao recurso, como o nome Private Cloud. |
| Tipo de Recurso | Categoria de recurso: Nuvem Privada, Cloud Rack. |
| ID do Recurso | Identificador do recurso. |
| Descrição | Descrição do que desencadeou o alerta. |
| Confirmado | Indicação de se o alerta é reconhecido. |

## <a name="events"></a>Eventos

Os eventos mostram a atividade do utilizador e do sistema no portal CloudSimple. A página Eventos lista a atividade associada a um recurso específico e a gravidade do impacto.

As seguintes colunas de informação estão disponíveis para alertas. Clique nas **colunas Editar** e selecione colunas que pretende ver.

| Coluna | Descrição |
------------ | ------------- |
| Hora | Data e hora do evento ocorreu. |
| Tipo de Evento | Código numérico que identifica o evento. |
| Gravidade | Gravidade do evento.|
| Nome do Recurso | Nome atribuído ao recurso, como o nome Private Cloud. |
| Tipo de Recurso | Categoria de recurso: Nuvem Privada, Cloud Rack. |
| Descrição | Descrição do que desencadeou o alerta. |

## <a name="tasks"></a>Tarefas

As tarefas são atividades da Cloud Privada que se espera que levem 30 segundos ou mais para serem concluídas. (As atividades que deverão demorar menos de 30 segundos são reportadas apenas como eventos.) Abra as páginas Tasks para acompanhar o progresso das tarefas para a sua Nuvem Privada.

As seguintes colunas de informação estão disponíveis para alertas. Clique nas **colunas Editar** e selecione colunas que pretende ver.

| Coluna | Descrição |
------------ | ------------- |
| ID de tarefa | Identificador único para a tarefa. |
| Operação | Ação que a tarefa executa. |
| Utilizador | Utilizador designado para completar a tarefa. |
| Nome do Recurso | Nome atribuído ao recurso. |
| Tipo de Recurso | Categoria de recurso: Nuvem Privada, Cloud Rack. |
| ID do Recurso | Identificador do recurso. |
| Iniciar | Hora de início para a tarefa. |
| Fim | Fim da tarefa. |
| Estado | Estado de tarefa atual. |
| Tempo Decorrido | Tempo que a tarefa demorou a concluir (se concluída) ou está atualmente a demorar (se estiver em curso). |
| Descrição | Descrição da tarefa. |

## <a name="audit"></a>Auditoria

Os registos de auditoria acompanham a atividade do utilizador. Pode utilizar registos de auditoria para monitorizar a atividade do utilizador para todos os utilizadores.

As seguintes colunas de informação estão disponíveis para alertas. Clique nas **colunas Editar** e selecione colunas que pretende ver.

| Coluna | Descrição |
------------ | ------------- |
| Hora | Hora da entrada da auditoria. |
| Operação | Ação que a tarefa executa. |
| Utilizador | Utilizador atribuído à tarefa. |
| Nome do Recurso | Nome atribuído ao recurso. |
| Tipo de Recurso | Categoria de recurso: Nuvem Privada, Cloud Rack. |
| ID do Recurso | Identificador do recurso. |
| Resultado | Resultado da atividade, como **o Sucesso.** |
| Tempo Decorrido | Hora de completar a tarefa. |
| Descrição | Descrição da ação. |

## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)
