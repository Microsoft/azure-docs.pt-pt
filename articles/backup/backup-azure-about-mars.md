---
title: Sobre o Agente MARS
description: Saiba como o Agente MARS suporta os cenários de backup
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 64f43f42fc23b1ca9591b6a49c3acce6c52c09d6
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86134968"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Sobre o agente microsoft Azure Recovery Services (MARS)

Este artigo descreve como o serviço Azure Backup utiliza o agente Microsoft Azure Recovery Services (MARS) para fazer backup e restaurar ficheiros, pastas e o estado de volume ou sistema de um computador no local para o Azure.

O agente MARS suporta os seguintes cenários de backup:

![Cenários de backup mars](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Ficheiros e Pastas**: Proteja seletivamente ficheiros e pastas do Windows.
- **Nível de volume**: Proteja todo o volume do Windows da sua máquina.
- **Nível do sistema**: Proteja todo o estado do sistema Windows.

O agente MARS suporta os seguintes cenários de restauro:

![Cenários de recuperação do MARS](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Mesmo Servidor**: O servidor no qual a cópia de segurança foi originalmente criada.
  - **Ficheiros e Pastas**: Escolha os ficheiros e pastas individuais que pretende restaurar.
  - **Nível de volume**: Escolha o ponto de volume e recuperação que pretende restaurar e, em seguida, restaurá-lo para o mesmo local ou para uma localização alternativa na mesma máquina.  Crie uma cópia dos ficheiros existentes, substitua os ficheiros existentes ou ignore a recuperação dos ficheiros existentes.
  - **Nível do sistema**: Escolha o estado do sistema e o ponto de recuperação para restaurar a mesma máquina num local especificado.

- **Servidor Alternativo**: Um servidor diferente do servidor onde a cópia de segurança foi tomada.
  - **Ficheiros e Pastas**: Escolha os ficheiros e pastas individuais cujo ponto de recuperação pretende restaurar para uma máquina alvo.
  - **Nível de volume**: Escolha o ponto de volume e recuperação que pretende restaurar para outro local. Crie uma cópia dos ficheiros existentes, substitua os ficheiros existentes ou ignore a recuperação dos ficheiros existentes.
  - **Nível do sistema**: Escolha o estado do sistema e o ponto de recuperação para restaurar como um ficheiro Estado do Sistema a uma máquina alternativa.

## <a name="backup-process"></a>Processo de cópia de segurança

1. A partir do portal Azure, crie um [cofre dos Serviços de Recuperação](install-mars-agent.md#create-a-recovery-services-vault)e escolha ficheiros, pastas e o estado do sistema a partir dos **objetivos de Backup**.
2. [Descarregue as credenciais de cofre dos Serviços de Recuperação e o instalador](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) de agentes para uma máquina no local.

3. [Instale o agente](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent) e utilize as credenciais de cofre descarregadas para registar a máquina no cofre dos Serviços de Recuperação.
4. A partir da consola do agente no cliente, [configufique a cópia de segurança](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy) para especificar o que fazer, quando fazer backup (o horário), quanto tempo as cópias de segurança devem ser mantidas no Azure (a política de retenção) e começar a proteger.

![Diagrama do agente de backup Azure](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>Informações adicionais

- A **Cópia de Segurança Inicial** (primeira cópia de segurança) é executado de acordo com as definições de backup.  O agente MARS utiliza o VSS para tirar uma imagem pontual dos volumes selecionados para cópia de segurança. O agente utiliza apenas a operação Do Windows System Writer para capturar o instantâneo. Não usa nenhuma aplicação de escritores VSS, e não captura imagens consistentes com aplicações. Depois de tirar a fotografia com VSS, o agente MARS cria um disco rígido virtual (VHD) na pasta de cache especificada quando configura a cópia de segurança. O agente também armazena as datas de verificação para cada bloco de dados.

- **As cópias de segurança incrementais** (backups subsequentes) são executadas de acordo com o calendário especificado. Durante as cópias de segurança incrementais, os ficheiros alterados são identificados e um novo VHD é criado. O VHD é comprimido e encriptado, e depois é enviado para o cofre. Após os acabamentos incrementais de backup, o novo VHD é fundido com o VHD criado após a replicação inicial. Este VHD fundido fornece o estado mais recente a ser usado para comparação para cópias de segurança em curso.

- O agente MARS pode executar a tarefa de backup em **modo otimizado** utilizando o diário de alteração USN (Update Sequence Number) ou em **modo não otimizado,** verificando alterações nos diretórios ou ficheiros através da digitalização de todo o volume. O modo nãotimizado é mais lento porque o agente tem de digitalizar cada ficheiro no volume e compará-lo com os metadados para determinar os ficheiros alterados.  A **cópia de segurança inicial** será sempre executada em modo nãotimizado. Se o backup anterior falhar, o próximo trabalho de backup programado será executado em modo nãotimizado. Para saber mais sobre estes modos e como os verificar, consulte [este artigo](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode).

### <a name="additional-scenarios"></a>Cenários adicionais

- Faça o **backup de ficheiros e pastas específicas dentro de máquinas virtuais Azure**: O método principal para fazer o backup das máquinas virtuais Azure (VMs) é utilizar uma extensão de backup Azure no VM. A extensão confirma todo o VM. Se pretender fazer o back up ficheiros e pastas específicos dentro de um VM, pode instalar o agente MARS nos VMs Azure. Para mais informações, consulte [Arquitetura: Cópia de segurança Azure VM incorporada.](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup)

- **Sementeira offline**: As cópias de segurança completas iniciais dos dados para o Azure normalmente transferem grandes quantidades de dados e requerem mais largura de banda de rede. As cópias de segurança subsequentes transferem apenas a quantidade de dados delta, ou incremental. O Azure Backup comprime as cópias de segurança iniciais. Através do processo de *sementeira offline,* o Azure Backup pode utilizar discos para enviar os dados de backup iniciais comprimidos offline para Azure. Para obter mais informações, consulte [a cópia de segurança offline do Azure Backup utilizando a Caixa de Dados Azure](offline-backup-azure-data-box.md).

## <a name="next-steps"></a>Próximos passos

[Matriz de suporte do agente MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Agente MARS FAQ](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
