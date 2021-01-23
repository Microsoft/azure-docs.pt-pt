---
title: Recuar e restaurar o Ative Directory
description: Saiba como fazer o back up e restaurar os controladores de domínio do Ative Directory.
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8db2dab605e90e4748b11a632d6651c23d631b6c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733558"
---
# <a name="back-up-and-restore-active-directory-domain-controllers"></a>Fazer recuar e restaurar controladores de domínio do Ative Directory

Apoiar o Ative Directory, e garantir restauros bem sucedidos em casos de corrupção, compromisso ou desastre é uma parte crítica da manutenção do Ative Directory.

Este artigo descreve os procedimentos adequados para fazer o backup e restaurar os controladores de domínio do Ative Directory com a Azure Backup, sejam máquinas virtuais Azure ou servidores no local. Discute um cenário em que é necessário restaurar um controlador de domínio inteiro no seu estado no momento da cópia de segurança. Para ver qual cenário de restauro é apropriado para si, consulte [este artigo.](/windows-server/identity/ad-ds/manage/ad-forest-recovery-determine-how-to-recover)  

>[!NOTE]
> Este artigo não discute a restauração de itens do [Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md). Para obter informações sobre a restauração dos utilizadores do Azure Ative Directory, consulte [este artigo](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="best-practices"></a>Melhores práticas

- Certifique-se de que pelo menos um controlador de domínio está apoiado. Se tiver apoiado mais do que um controlador de domínio, certifique-se de que todos os que detêm as [funções FSMO (Operação Master One Flexível)](/windows-server/identity/ad-ds/plan/planning-operations-master-role-placement) estão apoiados.

- Apoie o Ative Directory frequentemente. A cópia de segurança nunca deve ser mais do que a duração da lápide (por defeito 60 dias), porque os objetos mais antigos do que a vida útil da lápide serão "tombstoned" e já não considerados válidos.

- Tenha um plano claro de recuperação de desastres que inclua instruções sobre como restaurar os controladores de domínio. Para preparar a restauração de uma floresta de Diretório Ativo, leia o Guia de Recuperação florestal do [Diretório Ativo.](/windows-server/identity/ad-ds/manage/ad-forest-recovery-guide)

- Se precisar restaurar um controlador de domínio e ter um controlador de domínio funcional restante no domínio, pode fazer um novo servidor em vez de restaurar a partir de cópia de segurança. Adicione a função de servidor **de serviços de domínio de diretório ativo** ao novo servidor para torná-lo um controlador de domínio no domínio existente. Em seguida, os dados do Ative Directory serão replicados para o novo servidor. Para remover o controlador de domínio anterior do Ative Directory, siga os passos [deste artigo](/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup) para executar a limpeza de metadados.

>[!NOTE]
>O Azure Backup não inclui a restauração do nível do item para o Ative Directory. Se desejar restaurar objetos eliminados e aceder a um controlador de domínio, utilize o Depósito de Reciclagem do [Diretório Ativo](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#ad_recycle_bin_mgmt). Se este método não estiver disponível, pode utilizar a cópia de segurança do controlador de domínio para restaurar os objetos eliminados com a ferramenta **ntdsutil.exe,** conforme [explicado aqui](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac).
>
>Para obter informações sobre a realização de uma restauração autoritária da SYSVOL, consulte [este artigo.](/windows-server/identity/ad-ds/manage/ad-forest-recovery-authoritative-recovery-sysvol)

## <a name="backing-up-azure-vm-domain-controllers"></a>Controladores de domínio Azure VM

Se o controlador de domínio for um VM Azure, pode fazer uma cópia de segurança do servidor utilizando a [Cópia de Segurança VM do Azure](backup-azure-vms-introduction.md).

Leia sobre [considerações operacionais para controladores de domínio virtualizados](/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#operational-considerations-for-virtualized-domain-controllers) para garantir cópias de segurança bem sucedidas (e futuras restaurações) dos seus controladores de domínio Azure VM.

## <a name="backing-up-on-premises-domain-controllers"></a>Controladores de domínio no local

Para fazer uma cobertura de um controlador de domínio no local, é necessário fazer o back up dos dados do Estado do Sistema do servidor.

- Se estiver a utilizar o MARS, siga [estas instruções.](backup-azure-system-state.md)
- Se estiver a utilizar o MABS (Azure Backup Server), siga [estas instruções](backup-mabs-system-state-and-bmr.md).

>[!NOTE]
> Restaurar os controladores de domínio no local (quer do estado do sistema quer dos VMs) para a nuvem Azure não é suportado. Se quiser a opção de failover de um ambiente de Diretório Ativo no local para Azure, considere a utilização da [Recuperação do Local de Azure](../site-recovery/site-recovery-active-directory.md).

## <a name="restoring-active-directory"></a>Restaurar o Diretório Ativo

Os dados do Diretório Ativo podem ser restaurados num de dois modos: **autoritário** ou **não- autoritária**. Numa restauração autorizada, os dados do Ative Directory restaurados sobrepor-se-ão aos dados encontrados nos outros controladores de domínio na floresta.

No entanto, neste cenário estamos a reconstruir um controlador de domínio num domínio existente, por isso deve ser realizada uma restauração **não autoritária.**

Durante a restauração, o servidor será iniciado no Modo de Restauro de Serviços de Diretório (DSRM). Terá de fornecer a palavra-passe do Administrador para o Modo de Restauro de Serviços de Diretório.

>[!NOTE]
>Se a palavra-passe DSRM for esquecida, pode reiniciá-la utilizando [estas instruções](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754363(v=ws.11)).

### <a name="restoring-azure-vm-domain-controllers"></a>Restaurar controladores de domínio Azure VM

Para restaurar um controlador de domínio Azure VM, consulte [O controlador de domínio Restore VMs](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).

Se estiver a restaurar um único controlador de domínio VM ou vMs de controlador de domínio num único domínio, restaure-os como qualquer outro VM. O Modo de Restauro de Serviços de Diretório (DSRM) também está disponível, pelo que todos os cenários de recuperação do Diretório Ativo são viáveis.

Se precisar de restaurar um único controlador de domínio VM numa configuração de domínio múltipla, restaure os discos e crie um VM [utilizando o PowerShell](backup-azure-vms-automation.md#restore-the-disks).

Se estiver a restaurar o último controlador de domínio restante no domínio, ou a restaurar vários domínios numa floresta, recomendamos uma [recuperação florestal.](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)

>[!NOTE]
> Controladores de domínio virtualizados, a partir do Windows 2012 utilizam [salvaguardas baseadas em virtualização.](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100#virtualization-based-safeguards) Com estas salvaguardas, o diretório ative compreende se o VM restaurado é um controlador de domínio, e executa as medidas necessárias para restaurar os dados do Ative Directory.

### <a name="restoring-on-premises-domain-controllers"></a>Restaurar controladores de domínio no local

Para restaurar um controlador de domínio no local, siga as instruções para restaurar o estado do sistema no Windows Server, utilizando as orientações para [considerações especiais para a recuperação do estado do sistema num controlador de domínio](backup-azure-restore-system-state.md#special-considerations-for-system-state-recovery-on-a-domain-controller).

## <a name="next-steps"></a>Próximos passos

- [Matriz de suporte para backup Azure](backup-support-matrix.md)