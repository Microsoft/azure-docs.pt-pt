---
title: Backup Center - FAQ
description: Este artigo responde frequentemente a perguntas sobre backup Center
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: b10a9e73e65cf12c43ce28b429a8f12e0b960a76
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997687"
---
# <a name="backup-center---frequently-asked-questions"></a>Backup Center - Perguntas frequentes

## <a name="management"></a>Gestão

### <a name="can-backup-center-be-used-across-tenants"></a>O Backup Center pode ser usado entre inquilinos?

Sim, se você estiver usando [o Farol Azure](https://docs.microsoft.com/azure/lighthouse/overview) e tiver delegado acesso a subscrições em diferentes inquilinos, você pode usar o Backup Center como um único painel de vidro para gerir backups através de inquilinos.

### <a name="can-backup-center-be-used-to-manage-both-recovery-services-vaults-and-backup-vaults"></a>O Backup Center pode ser usado para gerir os cofres dos Serviços de Recuperação e os cofres de reserva?

Sim, o Backup Center pode gerir os [cofres dos Serviços de Recuperação](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) e [os cofres de reserva.](backup-vault-overview.md)

### <a name="is-there-a-delay-before-data-surfaces-in-backup-center"></a>Há algum atraso antes da divulgação de dados no Backup Center?

O Backup Center destina-se a fornecer informações em tempo real. Pode haver alguns segundos de intervalo entre o tempo em que uma entidade aparece num ecrã de cofre individual, e o tempo que a mesma entidade aparece no Backup Center.

## <a name="configuration"></a>Configuração

### <a name="do-i-need-to-configure-anything-to-see-data-in-backup-center"></a>Preciso de configurar alguma coisa para ver os dados no Backup Center?

N.º O Centro de Reserva sai pronto para fora da caixa. No entanto, para visualizar [relatórios de backup](https://docs.microsoft.com/azure/backup/configure-reports) no Centro de Backup, é necessário configurar relatórios para os seus cofres.

### <a name="do-i-need-to-have-any-special-permissions-to-use-backup-center"></a>Preciso de alguma autorização especial para usar o Backup Center?

O Backup Center, como tal, não precisa de novas permissões. Desde que tenha o nível certo de acesso RBAC para os recursos que está a gerir, pode usar o Backup Center para estes recursos. Por exemplo, para visualizar informações sobre as suas cópias de segurança, precisará do acesso do **Reader** aos seus cofres. Para configurar backup e executar outras ações relacionadas com backup, você precisará de **funções de Backup Contributor** ou **Backup Operator.** Saiba mais sobre [os papéis do RBAC para Azure Backup](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="pricing"></a>Preços

### <a name="do-i-need-to-pay-anything-extra-to-use-backup-explorer"></a>Preciso de pagar algo extra para usar o Backup Explorer?

Atualmente, não existem custos adicionais (além dos custos de reserva) para usar o Backup Center. No entanto, se estiver a utilizar [relatórios de backup](https://docs.microsoft.com/azure/backup/configure-reports) no Backup Center, há um [custo envolvido](https://azure.microsoft.com/pricing/details/monitor/) na utilização de Registos do Monitor Azure para relatórios de backup.

## <a name="next-steps"></a>Passos seguintes

Leia as outras FAQs:

* [Perguntas comuns sobre cofres dos Serviços de Recuperação](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq)
* [Perguntas comuns sobre backups Azure VM](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq)
