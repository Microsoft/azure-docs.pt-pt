---
title: Sobre o agente MARS
description: Saiba como o agente MARS dá suporte aos cenários de backup
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: c036d93c09195c0c330cfe86f307d28866131d9f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897328"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Sobre o agente de Serviços de Recuperação do Microsoft Azure (MARS)

Este artigo descreve como o serviço de backup do Azure usa o agente de Serviços de Recuperação do Microsoft Azure (MARS) para fazer backup e restaurar arquivos/pastas, volume ou estado do sistema do computador local para o Azure.

O agente MARS dá suporte aos seguintes cenários de backup:

![painel do cofre dos serviços de recuperação](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Arquivos e pastas**: proteger seletivamente arquivos e pastas do Windows.
- **Nível de volume**: Proteja todo o volume do Windows de seu computador.
- **Nível do sistema**: proteger todo o estado do sistema do Windows.

O agente MARS dá suporte aos seguintes cenários de restauração:

![painel do cofre dos serviços de recuperação](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **Mesmo servidor**: mesmo servidor no qual o backup foi originalmente criado.
    -    **Arquivos e pastas**: você pode procurar e escolher arquivos individuais e pastas que deseja restaurar.
    -    **Nível de volume**: você pode escolher o volume e o ponto de recuperação que deseja restaurar e restaurá-lo no mesmo local ou local alternativo no mesmo computador.  Você pode criar uma cópia dos arquivos existentes, substituir os arquivos existentes ou ignorar a recuperação de arquivos existentes.
    -    **Nível do sistema**: você pode escolher o estado do sistema e o ponto de recuperação para restaurar no mesmo computador em um local especificado.


-   **Servidor alternativo**: outro servidor, ou seja, não o mesmo servidor em que o backup foi feito.
    -    **Arquivos e pastas**: você pode procurar e escolher arquivos individuais e pastas para os quais deseja restaurar o ponto de recuperação em um computador de destino.
    -    **Nível de volume**: você pode escolher o volume e o ponto de recuperação que deseja restaurar para um local alternativo criando uma cópia dos arquivos existentes, sobrescrever arquivos existentes ou ignorar a recuperação de arquivos existentes.
    -    **Nível do sistema**: você pode escolher o estado do sistema e o ponto de recuperação para restaurar como arquivo de estado do sistema para um computador alternativo.

## <a name="backup-process"></a>Processo de cópia de segurança

1.  No portal do Azure, crie um [cofre do serviço de recuperação](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault) e escolha arquivos e pastas e/ou estado do sistema de metas de backup.
2.  [Baixe](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) as credenciais do cofre do serviço de recuperação e o instalador do agente em um computador local. Para proteger o computador local escolhendo a opção de backup, escolha arquivos e pastas e estado do sistema e baixe o agente MARS.
3.  Preparar a infraestrutura:

    a.    Execute o instalador para [instalar](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) o agente.

    b.  Use as credenciais do cofre baixado para registrar o computador no cofre dos serviços de recuperação.
4.  No console do agente no cliente, use [agendar backup](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy) para configurar o backup. Especifique a política de retenção de seus dados de backup e comece a proteger.

![painel do cofre dos serviços de recuperação](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>Cenários adicionais
-   **Arquivos e pastas específicos de backup na VM do Azure** – o método principal para fazer backup de VMs (máquinas virtuais) do Azure é usando uma extensão de backup do Azure na VM. Isso faz o backup de toda a VM. Se desejar fazer backup de arquivos e pastas específicos em uma VM, você poderá instalar o agente MARS em VMs do Azure. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

-   **Propagação offline** -backups completos iniciais de dados no Azure, normalmente transferem grandes quantidades de dados e exigem mais largura de banda de rede em comparação com os backups subsequentes que transferem apenas o Delta/incremental. O backup do Azure compacta os backups iniciais. Por meio do processo de propagação offline, o backup do Azure pode usar discos para carregar os dados de backup inicial compactados offline no Azure. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).


## <a name="next-steps"></a>Passos seguintes
[A matriz de suporte do agente MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Perguntas frequentes-o agente MARS](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
