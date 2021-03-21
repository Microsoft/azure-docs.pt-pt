---
title: Backup on-line e dados on-demand restaurados em Azure Cosmos DB.
description: Este artigo descreve como a cópia de segurança automática, a restauração de dados a pedido funciona. Também explica a diferença entre modos de backup contínuos e periódicos.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 2629e9c6e048620d9490a1e091a16c138fd1e615
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99525437"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Cópia de segurança online e restauro de dados a pedido no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB tira automaticamente cópias de segurança dos dados em intervalos regulares. As cópias de segurança automáticas são feitas sem afetar o desempenho ou a disponibilidade das operações da base de dados. Todas as cópias de segurança são armazenadas separadamente num serviço de armazenamento. As cópias de segurança automáticas são úteis em cenários em que tenha eliminado ou atualizado acidentalmente a conta, a base de dados ou o contentor do Azure Cosmos e, mais tarde, precisa de recuperar os dados. Existem dois modos de reserva:

* **Modo de cópia de segurança periódica** - Este modo é o modo de cópia de segurança predefinido para todas as contas existentes. Neste modo, a cópia de segurança é feita num intervalo periódico e os dados são restaurados criando um pedido junto da equipa de suporte. Neste modo, configura um intervalo de backup e retenção para a sua conta. O período máximo de retenção estende-se por um mês. O intervalo mínimo de reserva pode ser de uma hora.  Para saber mais, consulte o artigo do [modo de cópia de segurança periódico.](configure-periodic-backup-restore.md)

* **Modo de backup contínuo** (atualmente em pré-visualização pública) – Escolha este modo enquanto cria a conta DB Azure Cosmos. Este modo permite-lhe restaurar qualquer ponto de tempo nos últimos 30 dias. Para saber mais, consulte o [modo de backup contínuo Introdução,](continuous-backup-restore-introduction.md)configurar cópias de segurança contínuas com artigos do portal [Azure,](continuous-backup-restore-portal.md) [PowerShell,](continuous-backup-restore-powershell.md) [CLI](continuous-backup-restore-command-line.md)e [Gestor de Recursos.](continuous-backup-restore-template.md)

  > [!NOTE]
  > Se configurar uma nova conta com cópia de segurança contínua, pode fazer a restauração do autosserviço através do portal Azure, PowerShell ou CLI. Se a sua conta estiver configurada em modo contínuo, não pode mudá-la de volta para o modo periódico. Atualmente, as contas existentes com modo de backup periódico não podem ser alteradas em modo contínuo.  

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode aprender como configurar e gerir modos de backup periódicos e contínuos para a sua conta:

* [Configure e gere a política de backup periódica.](configure-periodic-backup-restore.md)
* O que é o modo [de backup contínuo?](continuous-backup-restore-introduction.md)
* Configure e gere a cópia de segurança contínua utilizando [o portal Azure](continuous-backup-restore-portal.md), [PowerShell,](continuous-backup-restore-powershell.md) [CLI](continuous-backup-restore-command-line.md)ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gerir as permissões necessárias](continuous-backup-restore-permissions.md) para restaurar os dados com o modo de backup contínuo.
