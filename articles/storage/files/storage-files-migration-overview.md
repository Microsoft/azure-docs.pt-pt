---
title: Migrar para partilhas de ficheiros do Azure
description: Saiba mais sobre migrações para ações de ficheiros Azure e encontre o seu guia de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 4a874e6f1e026a1888b9039799be71c95f040ac6
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202353"
---
# <a name="migrate-to-azure-file-shares"></a>Migrar para partilhas de ficheiros do Azure

Este artigo abrange os aspetos básicos de uma migração para as ações de ficheiros Azure.

Este artigo contém bases migratórias e uma tabela de guias de migração. Estes guias ajudam-no a mover os seus ficheiros para ações de ficheiros Azure. Os guias são organizados com base no local onde estão os seus dados e em que modelo de implementação (apenas em nuvem ou híbrido) está a mudar-se.

## <a name="migration-basics"></a>Princípios da migração

O Azure tem vários tipos disponíveis de armazenamento em nuvem. Um aspeto fundamental das migrações de ficheiros para a Azure é determinar qual a opção de armazenamento Azure que é a correta para os seus dados.

[As ações de ficheiros Azure](storage-files-introduction.md) são adequadas para dados de ficheiros de uso geral. Estes dados incluem qualquer coisa para a sua utilização de uma participação de SMB ou NFS no local. Com [o Azure File Sync,](storage-sync-files-planning.md)pode cache o conteúdo de várias partilhas de ficheiros Azure em servidores que executam o Windows Server no local.

Para uma aplicação que atualmente é executada num servidor no local, armazenar ficheiros numa partilha de ficheiros Azure pode ser uma boa escolha. Pode mover a aplicação para a Azure e utilizar as ações de ficheiros Azure como armazenamento partilhado. Também pode considerar [discos Azure](../../virtual-machines/managed-disks-overview.md) para este cenário.

Algumas aplicações em nuvem não dependem de SMB ou de acesso de dados locais a máquinas ou acesso partilhado. Para essas aplicações, o armazenamento de objetos como [as bolhas Azure é muitas vezes](../blobs/storage-blobs-overview.md) a melhor escolha.

A chave em qualquer migração é capturar toda a fidelidade de ficheiro aplicável ao mover os seus ficheiros do seu local de armazenamento atual para Azure. A fidelidade que a opção de armazenamento Azure suporta e o quanto o seu cenário requer também ajuda a escolher o armazenamento Azure certo. Os dados de ficheiros de uso geral dependem tradicionalmente de metadados de ficheiros. Os dados da aplicação podem não ser.

Aqui estão os dois componentes básicos de um ficheiro:

- **Fluxo de dados**: O fluxo de dados de um ficheiro armazena o conteúdo do ficheiro.
- **Metadados de ficheiro**: Os metadados de ficheiro têm estes subcomponentes:
   * Atributos de arquivo como apenas leitura
   * Permissões de ficheiros, que podem ser referidas como *permissões NTFS* ou *ACLs de ficheiros e pastas*
   * Timetamps, mais notavelmente a criação, e últimos testes de tempo modificados
   * Um fluxo de dados alternativo, que é um espaço para armazenar grandes quantidades de propriedades não padrão

A fidelidade de ficheiros numa migração pode ser definida como a capacidade de:

- Guarde todas as informações de ficheiros aplicáveis na fonte.
- Transfira ficheiros com a ferramenta de migração.
- Armazenar ficheiros no armazenamento alvo da migração.

Para garantir que a sua migração prossegue sem problemas, identifique [a melhor ferramenta de cópia para as suas necessidades](#migration-toolbox) e corresponda a um alvo de armazenamento com a sua fonte.

Tendo em conta as informações anteriores, pode ver que o armazenamento do alvo para ficheiros de uso geral em Azure são [ações de ficheiroS Azure](storage-files-introduction.md).

Ao contrário do armazenamento de objetos em blobs Azure, uma partilha de ficheiros Azure pode armazenar metadados de ficheiros nativo. As ações de ficheiros Azure também preservam a hierarquia de ficheiros e pastas, atributos e permissões. As permissões NTFS podem ser armazenadas em ficheiros e pastas porque estão no local.

Um utilizador do Ative Directory, que é o seu controlador de domínio no local, pode aceder de forma nativa a uma partilha de ficheiros Azure. Assim como um utilizador de Azure Ative Directory Domain Services (Azure AD DS). Cada um usa a sua identidade atual para obter acesso com base em permissões de partilha e em ACLs de ficheiros e pastas. Este comportamento é semelhante a um utilizador que se conecta a uma partilha de ficheiros no local.

O fluxo de dados alternativo é o principal aspeto da fidelidade de ficheiros que atualmente não pode ser armazenado num ficheiro numa partilha de ficheiros Azure. É preservado no local quando o Azure File Sync é usado.

Saiba mais sobre [a autenticação AZure AD](storage-files-identity-auth-active-directory-enable.md) e [a autenticação Azure AD DS](storage-files-identity-auth-active-directory-domain-service-enable.md) para ações de ficheiros Azure.

## <a name="migration-guides"></a>Guias de migração

A tabela que se segue lista guias de migração detalhados.

Como usar a mesa:

1. Localize a linha para o sistema de origem em que os seus ficheiros estão armazenados atualmente.

1. Escolha um destes alvos:

   - Uma implementação híbrida utilizando o Azure File Sync para cache o conteúdo das ações de ficheiros Azure no local
   - Azure partilha ficheiros na nuvem

   Selecione a coluna-alvo que corresponda à sua escolha.

1. Dentro da intersecção entre a origem e o alvo, uma lista de cenários de migração disponíveis. Selecione um para ligar diretamente ao guia de migração detalhado.

Um cenário sem ligação ainda não tem um guia de migração publicado. Verifique ocasionalmente esta tabela para obter atualizações. Novos guias serão publicados quando estiverem disponíveis.

| Origem | Destino: </br>Implantação híbrida | Destino: </br>Implantação apenas em nuvem |
|:---|:--|:--|
| | Combinação de ferramentas:| Combinação de ferramentas: |
| Windows Server 2012 R2 e mais tarde | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Azure File Sync e Azure Data Box](storage-sync-offline-data-transfer.md)</li><li>[Azure File Sync e ficheiros pré-semeados na nuvem](storage-sync-offline-data-transfer.md#azure-file-sync-and-pre-seeded-files-in-the-cloud)</li><li>Serviço de Sincronização e Migração de Arquivos Azure</li></ul> | <ul><li>Azure File Sync</li><li>Azure File Sync e Caixa de Dados</li><li>Serviço de Sincronização e Migração de Arquivos Azure</li><li>RoboCopy</li></ul> |
| Windows Server 2012 e mais cedo | <ul><li>Azure File Sync e Caixa de Dados</li><li>Serviço de Sincronização e Migração de Arquivos Azure</li></ul> | <ul><li>Serviço de Sincronização e Migração de Arquivos Azure</li><li>RoboCopy</li></ul> |
| Armazenamento ligado à rede (NAS) | <ul><li>[Azure File Sync e RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Linux ou Samba | <ul><li>[Azure File Sync e RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Microsoft Azure StorSimple Cloud Appliance 8100 ou StorSimple Cloud Appliance 8600 | <ul><li>[Azure File Sync e StorSimple Cloud Appliance 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Caixa de ferramentas de migração

### <a name="file-copy-tools"></a>Ferramentas de cópia de ficheiros

Existem várias ferramentas de cópia de ficheiros disponíveis da Microsoft e outras. Para selecionar a ferramenta certa para o seu cenário de migração, deve considerar estas questões fundamentais:

* A ferramenta suporta a origem e os locais-alvo da sua cópia de ficheiro?

* A ferramenta suporta o seu percurso de rede ou protocolos disponíveis (tais como REST, SMB ou NFS) entre os locais de armazenamento de origem e alvo?

* A ferramenta preserva a fidelidade de ficheiros necessária suportada pela sua origem e locais-alvo?

    Em alguns casos, o armazenamento do seu alvo não suporta a mesma fidelidade que a sua fonte. Se o armazenamento do alvo for suficiente para as suas necessidades, a ferramenta deve corresponder apenas às capacidades de fidelidade de ficheiros do alvo.

* A ferramenta tem funcionalidades que a permitem encaixar na sua estratégia de migração?

    Por exemplo, considere se a ferramenta permite minimizar o seu tempo de inatividade.
    
    Quando uma ferramenta suporta uma opção de espelhar uma fonte para um alvo, pode frequentemente executá-la várias vezes na mesma fonte e alvo enquanto a fonte permanece acessível.

    A primeira vez que executar a ferramenta, copia a maior parte dos dados. Esta corrida inicial pode durar um pouco. Muitas vezes dura mais do que deseja para tirar a fonte de dados offline para os seus processos de negócio.

    Ao espelhar uma fonte para um alvo (como com **robocopia /MIR),** pode voltar a executar a ferramenta na mesma fonte e alvo. A corrida é muito mais rápida porque precisa de transportar apenas alterações de origem que ocorram após a execução anterior. A reexecução de uma ferramenta de cópia desta forma pode reduzir significativamente o tempo de inatividade.

A tabela a seguir classifica as ferramentas da Microsoft e a sua adequação atual para ações de ficheiros Azure:

| Recomendado | Ferramenta | Suporte para ações de ficheiros Azure | Preservação da fidelidade de ficheiros |
| :-: | :-- | :---- | :---- |
|![Sim, recomendado.](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | Suportado. As ações de ficheiros Azure podem ser montadas como unidades de rede. | Fidelidade total.* |
|![Sim, recomendado.](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Integrados de forma nativa em ações de ficheiros Azure. | Fidelidade total.* |
|![Sim, recomendado.](media/storage-files-migration-overview/circle-green-checkmark.png)| Serviço de Migração de Armazenamento | Apoiado indiretamente. As ações de ficheiros Azure podem ser montadas à medida que a rede conduz nos servidores-alvo de SMS. | Fidelidade total.* |
|![Sim, recomendado.](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy, versão 10.4 ou mais tarde| Suportado. | Fidelidade total.* |
|![Sim, recomendado.](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | Suportado. | A DataBox suporta agora totalmente os metadados. [A Caixa de Dados também pode ser utilizada em combinação com o Azure File Sync](storage-sync-offline-data-transfer.md). |
|![Não totalmente recomendado](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure Storage Explorer, versão 1.14 | Suportado. | Não copia ACLs. Suporta os tempos de semana.  |
|![Não recomendado](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Suportado. | Não copia metadados. |
|||||

*\* Fidelidade total: cumpre ou excede as capacidades de partilha de ficheiros Azure.*

### <a name="migration-helper-tools"></a>Ferramentas de ajuda à migração

Esta secção descreve ferramentas que o ajudam a planear e executar migrações.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy da Microsoft Corporation

O RoboCopy é uma das ferramentas mais aplicáveis às migrações de ficheiros. Vem como parte do Windows. A [documentação principal do RoboCopy](/windows-server/administration/windows-commands/robocopy) é um recurso útil para as muitas opções desta ferramenta.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize da JAM Software GmbH

O Azure File Sync escala principalmente com o número de itens (ficheiros e pastas) e não com a quantidade total de armazenamento. A ferramenta TreeSize permite-lhe determinar o número de itens nos volumes do Seu Servidor Windows.

Pode utilizar a ferramenta para criar uma perspetiva antes de uma [implementação do Azure File Sync](storage-sync-files-deployment-guide.md). Também pode usá-lo quando o nível da nuvem estiver ativado após a implantação. Nesse cenário, vê-se o número de itens e quais os diretórios que mais utilizam o cache do servidor.

A versão testada da ferramenta é a versão 4.4.1. É compatível com ficheiros com camadas de nuvem. A ferramenta não provocará a recolha de ficheiros hierárquicos durante o seu funcionamento normal.

## <a name="next-steps"></a>Passos seguintes

1. Crie um plano para o qual a implementação de ações de ficheiros Azure (apenas em nuvem ou híbrida) pretende.
1. Reveja a lista de guias de migração disponíveis para encontrar o guia detalhado que corresponda à sua origem e implementação de ações de ficheiros Azure.

Aqui está mais informações sobre as tecnologias Azure Files mencionadas neste artigo:

* [Visão geral da partilha de ficheiros Azure](storage-files-introduction.md)
* [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
* [Azure File Sync: Cloud Tiering](storage-sync-cloud-tiering-overview.md)