---
title: Monitorizar e operar cópias de segurança através do Centro de Cópias de Segurança
description: Este artigo explica como monitorizar e operar backups em escala usando backup Center
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 86b81110d6abeb1425e18ee45dfe65a96f69687d
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506130"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>Monitorize e opere backups usando o backup center

Como administrador de reserva, pode usar o Backup Center como uma única vidraça de vidro para monitorizar os seus trabalhos e o inventário de backup no dia-a-dia. Também pode utilizar o Backup Center para realizar as suas operações regulares, tais como responder a pedidos de backup a pedido, restaurar backups, criar políticas de backup, e assim por diante.

## <a name="supported-scenarios"></a>Cenários suportados

* O backup center é atualmente suportado para backup Azure VM, SQL em backup Azure VM, SAP HANA em backup Azure VM, backup Azure Files, backup Azure Blobs, backup de Discos Geridos Azure e Base de Dados Azure para backup do Servidor PostgreSQL.
* Consulte a matriz de [suporte](backup-center-support-matrix.md) para obter uma lista detalhada de cenários suportados e não apoiados.

## <a name="backup-instances"></a>Instâncias de backup

O backback center permite uma fácil pesquisa e descoberta de instâncias de backup em toda a sua propriedade de backup.

A seleção do **separador 'Exemplos de cópias de segurança'** no centro de backup permite-lhe visualizar detalhes de todas as instâncias de backup a que tem acesso.

 Pode ver as seguintes informações sobre cada uma das suas instâncias de backup:

* Assinatura de datasource
* Grupo de recursos de fonte de dados
* Último ponto de recuperação
* Cofre associado à instância de backup
* Estado de proteção

 Também pode filtrar a lista de casos de backup nos seguintes parâmetros:

* Assinatura de datasource
* Grupo de recursos de fonte de dados
* Localização de fonte de dados
* Tipo de fonte de dados
* Cofre
* Estado de proteção
* Etiquetas de fonte de dados

Clicar com o botão direito em qualquer um dos itens da grelha permite-lhe executar ações na instância de backup dada, como navegar para o recurso, desencadear backups e restauros a pedido ou parar o backup.

![Backup Center - Instâncias](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>Trabalhos de reserva

O backup center permite-lhe visualizar informações detalhadas sobre todos os empregos que foram criados na sua propriedade de reserva e tomar as medidas apropriadas para trabalhos falhados.

A seleção do item do menu **de trabalhos de cópia de segurança** no backup center proporciona uma visão de todos os seus trabalhos. Cada trabalho contém as seguintes informações:

* Instância de backup associada ao trabalho
* Assinatura de datasource
* Grupo de recursos de fonte de dados
* Localização de fonte de dados
* Operação de trabalho
* Estatuto do trabalho
* Hora de início da tarefa
* Duração do trabalho

A seleção de um item na grelha permite-lhe visualizar mais detalhes sobre o trabalho dado. Clicar com o direito num item ajuda-o a navegar para o recurso para tomar as medidas necessárias.

![Backup Center - Empregos](./media/backup-center-monitor-operate/backup-center-jobs.png)

Usando o **separador de empregos de reserva,** você pode ver empregos até os últimos sete dias. Para visualizar trabalhos mais antigos, utilize [relatórios de backup](backup-center-obtain-insights.md).

## <a name="vaults"></a>Cofres

A seleção do item do menu **Vaults** no centro de backup permite-lhe ver uma lista de todos os cofres dos Serviços de [Recuperação](backup-azure-recovery-services-vault-overview.md) e [cofres de reserva](backup-vault-overview.md) aos quais tem acesso. Pode filtrar a lista com os seguintes parâmetros:

* Assinatura do cofre
* Grupo de recursos de abóbada
* Nome do Vault
* Tipo de fonte de dados associado à política

A seleção de qualquer item da lista permite-lhe navegar para um dado cofre.

![Backup Center - Cofres](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Políticas de backup

O backup center permite-lhe visualizar e editar informações chave para qualquer uma das suas políticas de backup.

A seleção do item do menu **Políticas de Cópia** de Segurança permite-lhe visualizar todas as políticas que criou através da sua propriedade de backup. Pode filtrar a lista por subscrição de cofre, grupo de recursos, tipo de fonte de dados e cofre. Clicar com o direito num item na grelha permite-lhe ver itens associados para essa política, editar a política ou até mesmo eliminá-la se necessário.

![Backup Center - Políticas](./media/backup-center-monitor-operate/backup-center-policies.png)

## <a name="next-steps"></a>Passos seguintes

* [Governe a sua propriedade de reserva](backup-center-govern-environment.md)
* [Execute ações usando o backup center](backup-center-actions.md)
* [Obtenha informações sobre as suas cópias de segurança](backup-center-obtain-insights.md)
