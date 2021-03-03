---
title: Visão geral do backup operacional para Azure Blobs
description: Saiba mais sobre o backup operacional para Azure Blobs (em pré-visualização).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: b10191c8a01d3cc7a92dee8ca9bf59a506497a60
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746121"
---
# <a name="overview-of-operational-backup-for-azure-blobs-in-preview"></a>Visão geral do backup operacional para Azure Blobs (em pré-visualização)

O backup operacional para blobs é uma solução gerida e local de proteção de dados que permite proteger as suas bolhas de bloco de vários cenários de perda de dados, como corrupçãos, eliminações de bolhas e eliminação acidental de conta de armazenamento. Os dados são armazenados localmente dentro da própria conta de armazenamento de origem e podem ser recuperados para um ponto selecionado no tempo sempre que necessário. Assim, fornece um meio simples, seguro e rentável para proteger as suas bolhas.

O backup operacional para blobs integra-se com [o Backup Center,](backup-center-overview.md)entre outras capacidades de gestão de Backup, para fornecer um único painel de vidro que pode ajudá-lo a governar, monitorizar, operar e analisar backups em escala.

## <a name="how-operational-backup-works"></a>Como funciona o backup operacional

O backup operacional das bolhas é uma solução **de reserva local.** Os dados de reserva não são transferidos para o cofre de reserva, mas são armazenados na própria conta de armazenamento de fontes. No entanto, o cofre de reserva ainda serve como unidade de gestão de backups. Além disso, esta é uma solução **de backup contínua,** o que significa que você não precisa agendar quaisquer backups e todas as alterações serão mantidas e restauradoras do estado em um ponto selecionado no tempo.

A cópia de segurança operacional utiliza as capacidades da plataforma blob para proteger os seus dados e permitir a recuperação quando necessário:

- **Restauro pontual**: [A restauração do ponto de mancha no tempo](https://docs.microsoft.com/azure/storage/blobs/point-in-time-restore-overview) permite restaurar os dados blob para um estado anterior. Isto, por sua vez, utiliza **eliminação suave,** **alterar a versão de feed** e **blob** para reter dados durante a duração especificada. A cópia de segurança operacional tem o cuidado de permitir a restauração pontual, bem como as capacidades subjacentes para garantir que os dados são conservados durante a duração especificada.

- **Excluir o bloqueio**: Eliminar o bloqueio impede que a conta de armazenamento seja eliminada acidentalmente ou por utilizadores não autorizados. A cópia de segurança operacional quando configurada também aplica automaticamente um bloqueio de eliminação para reduzir as possibilidades de perda de dados devido à eliminação da conta de armazenamento.

Consulte a [matriz de suporte](blob-backup-support-matrix.md) para saber mais sobre as limitações da solução atual.

### <a name="protection"></a>Proteção

O backup operacional é configurado e gerido ao nível da **conta de armazenamento,** e aplica-se a todas as bolhas de bloco dentro da conta de armazenamento. A cópia de segurança operacional utiliza uma **política de backup** para gerir a duração da qual os dados de backup (incluindo versões mais antigas e bolhas eliminadas) devem ser mantidos, definindo assim o período a partir do qual pode restaurar os seus dados. A política de backup pode ter uma retenção máxima de 360 dias, ou número equivalente de semanas completas (51) ou meses (11).

Quando configurar a cópia de segurança para uma conta de armazenamento e atribuir uma política de backup com uma retenção de dias 'n', as propriedades subjacentes são definidas como descrito abaixo. Pode ver estas propriedades no **separador de proteção de dados** do serviço blob na sua conta de armazenamento.

- **Restauro pontual**: Definir para 'n' dias, conforme definido na política de backup. Se a conta de armazenamento já tiver um ponto no tempo habilitado com uma retenção de, digamos , dias 'x', antes de configurar a cópia de segurança, a duração de restauro pontual será definida para o maior dos dois valores, isto é, máximo(n,x). Se já tivesse permitido a restauração pontual e especificado que a retenção era maior do que a da política de backup, permanecerá inalterada.

- **Eliminação suave**: De definido para dias 'n+5', ou seja, cinco dias para além da duração especificada na política de backup. Se a conta de armazenamento que está a ser configurada para backup operacional já tinha uma eliminação suave ativada com uma retenção de, digamos, dias 'y', então a retenção de eliminação suave será definida ao máximo dos dois valores, ou seja, max (n+5,y). Se já tivesse ativado a eliminação suave e especificado que a retenção fosse maior do que a política de backup, permanecerá inalterada.

- **Versão para bolhas e feed de alteração de bolhas**: A versão e o feed de alteração estão ativados para contas de armazenamento que foram configuradas para cópia de segurança operacional.

- **Excluir bloqueio**: Configurar a cópia de segurança operacional numa conta de armazenamento também aplica um Bloqueio de Eliminação na conta de armazenamento. O Bloqueio de Eliminação aplicado por Cópia de Segurança pode ser visualizado no **separador Fechaduras** da conta de armazenamento.

Para permitir a proteção destas propriedades nas contas de armazenamento, o cofre de backup deve ser concedido à **função de Contribuinte de Backup** da Conta de Armazenamento nas respetivas contas de armazenamento.

>[!NOTE]
>O backup operacional suporta operações apenas em blocos e as operações em contentores não podem ser restauradas. Se eliminar um recipiente da conta de armazenamento chamando a operação **do Recipiente delete,** esse recipiente não pode ser restaurado com uma operação de restauro. Sugere-se que permita a eliminação suave para melhorar a proteção e recuperação de dados.

### <a name="management"></a>Gestão

Uma vez ativada a cópia de segurança numa conta de armazenamento, é criada uma Instância de Backup correspondente à conta de armazenamento no cofre de reserva. Pode efetuar quaisquer operações relacionadas com backup para uma conta de armazenamento como iniciar restauros, monitorização, proteção de paragem, e assim por diante, através da sua correspondente Instância de Backup.

O backup operacional também se integra diretamente com o Backup Center para ajudá-lo a gerir a proteção de todas as suas contas de armazenamento centralmente, juntamente com todas as outras cargas de trabalho suportadas por Backup. O Backup Center é o seu único painel de vidro para todos os seus requisitos de Backup, como monitorizar empregos e estado de backups e restauros, garantir o cumprimento e governança, analisar o uso de backup e realizar operações relacionadas com o backup e restauro de dados.

### <a name="restore"></a>Restauro

Pode restaurar os dados a partir de qualquer ponto no tempo para o qual existe um ponto de recuperação. Um ponto de recuperação é criado quando uma conta de armazenamento está em estado protegido, e pode ser usado para restaurar os dados desde que caia no período de retenção definido pela política de backup (e assim a capacidade de restauro pontual do serviço blob na conta de armazenamento). A cópia de segurança operacional utiliza o restauro do ponto de mancha no tempo para restaurar os dados de um ponto de recuperação.

A cópia de segurança operacional dá-lhe a opção de restaurar todas as bolhas de bloco na conta de armazenamento, navegar e restaurar recipientes específicos, ou usar fósforos prefixos para restaurar um subconjunto de bolhas. Todas as restaurações só podem ser realizadas na conta de armazenamento de origem.

## <a name="pricing"></a>Preços

Não incorrerá em quaisquer encargos de gestão ou taxa de caso ao usar backup operacional para bolhas. No entanto, incorrerá nas seguintes acusações:

- As restaurações são feitas utilizando a restauração do ponto de mancha no tempo e atraem encargos com base na quantidade de dados processados. Para obter mais informações, consulte [o preço de restauro pontual.](https://docs.microsoft.com/azure/storage/blobs/point-in-time-restore-overview#pricing-and-billing)

- Retenção de dados por causa da [eliminação suave para bolhas,](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview) [Alterar suporte de alimentação em Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-change-feed), e [versões Blob](https://docs.microsoft.com/azure/storage/blobs/versioning-overview).

## <a name="next-steps"></a>Passos seguintes

- [Configure e gerencie o backup Azure Blobs](blob-backup-configure-manage.md)
