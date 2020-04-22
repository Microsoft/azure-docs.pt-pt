---
title: Migrar para partilhas de ficheiros do Azure
description: Saiba mais sobre migrações para as partilhas de ficheiros Azure e encontre o seu guia de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d6141d48d67dd44c348961c6e09acf4e2531a61e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685992"
---
# <a name="migrate-to-azure-file-shares"></a>Migrar para partilhas de ficheiros do Azure

Este artigo aborda os aspetos básicos de uma migração para as ações de ficheiros Azure.

Este artigo contém bases de migração e uma tabela de guias de migração. Estes guias ajudam-no a transferir os seus ficheiros para partilhas de ficheiros Azure. Os guias são organizados com base no local onde estão os seus dados e em que modelo de implementação (apenas em nuvem ou híbrido) está a mover-se.

## <a name="migration-basics"></a>Básicos de migração

O Azure tem vários tipos de armazenamento em nuvem disponíveis. Um aspeto fundamental das migrações de ficheiros para o Azure é determinar qual a opção de armazenamento azure certa para os seus dados.

[As ações de ficheiros Azure](storage-files-introduction.md) são adequadas para dados de ficheiros de uso geral. Estes dados incluem qualquer coisa para a sua utilização de uma quota smb ou NFS no local. Com o [Azure File Sync,](storage-sync-files-planning.md)pode cache o conteúdo de várias partilhas de ficheiros Azure em servidores que executam o Windows Server no local.

Para uma aplicação que atualmente funciona num servidor no local, armazenar ficheiros numa partilha de ficheiros Azure pode ser uma boa escolha. Pode mover a aplicação para o Azure e utilizar as partilhas de ficheiros Azure como armazenamento partilhado. Também pode considerar [os Discos Azure](../../virtual-machines/windows/managed-disks-overview.md) para este cenário.

Algumas aplicações em nuvem não dependem de SMB ou de acesso a dados locais ou de acesso partilhado. Para essas aplicações, o armazenamento de objetos como [as bolhas Azure](../blobs/storage-blobs-overview.md) é muitas vezes a melhor escolha.

A chave em qualquer migração é capturar toda a fidelidade aplicável ao transferir os seus ficheiros do local de armazenamento atual para o Azure. Quanta fidelidade a opção de armazenamento Azure suporta e quanto o seu cenário requer também ajuda a escolher o armazenamento Azure certo. Os dados dos ficheiros de uso geral dependem tradicionalmente dos metadados dos ficheiros. Os dados da aplicação podem não.

Aqui estão os dois componentes básicos de um ficheiro:

- **Fluxo de dados**: O fluxo de dados de um ficheiro armazena o conteúdo do ficheiro.
- **Metadados**de ficheiros : Os metadados do ficheiro têm estes subcomponentes:
   * Atributos de arquivo como apenas leitura
   * Permissões de ficheiros, que podem ser referidas como *permissões NTFS* ou *ficheiros e pastas ACLs*
   * Carimbos temporais, nomeadamente a criação e os selos temporais modificados
   * Um fluxo de dados alternativo, que é um espaço para armazenar grandes quantidades de propriedades não padrão

A fidelidade do ficheiro numa migração pode ser definida como a capacidade de:

- Guarde todas as informações de ficheiro aplicáveis na fonte.
- Transferir ficheiros com a ferramenta de migração.
- Guarde ficheiros no armazenamento alvo da migração.

Para garantir que a sua migração prossegue sem problemas, identifique [a melhor ferramenta de cópia para as suas necessidades](#migration-toolbox) e corresponda a um alvo de armazenamento com a sua fonte.

Tendo em conta as informações anteriores, pode ver que o armazenamento alvo para ficheiros de uso geral no Azure são ações de [ficheiros Azure](storage-files-introduction.md).

Ao contrário do armazenamento de objetos em blobs Azure, uma partilha de ficheiros Azure pode armazenar metadados de ficheiros de forma nativa. As partilhas de ficheiros Azure também preservam a hierarquia de ficheiros e pastas, atributos e permissões. As permissões ntfs podem ser armazenadas em ficheiros e pastas porque estão no local.

Um utilizador do Ative Directory, que é o seu controlador de domínio no local, pode aceder de forma nativa a uma partilha de ficheiros Azure. Assim como um utilizador dos Serviços de Domínio de Diretório Ativo Azure (Azure AD DS). Cada um usa a sua identidade atual para obter acesso com base em permissões de partilha e em ACLs de ficheiros e pastas. Este comportamento é semelhante ao de um utilizador que se conecta a uma partilha de ficheiros no local.

O fluxo de dados alternativo é o principal aspeto da fidelidade de ficheiros que atualmente não pode ser armazenado num ficheiro numa partilha de ficheiros Azure. É preservado no local quando o Azure File Sync é usado.

Saiba mais sobre a autenticação da [Azure AD](storage-files-identity-auth-active-directory-enable.md) e a [autenticação Azure AD DS](storage-files-identity-auth-active-directory-domain-service-enable.md) para ações de ficheiros Azure.

## <a name="migration-guides"></a>Guias de migração

A tabela seguinte enumera guias de migração detalhados.

Como utilizar a tabela:

1. Localize a linha para o sistema de origem em que os seus ficheiros estão atualmente armazenados.

1. Escolha um destes alvos:

   - Uma implantação híbrida utilizando o Azure File Sync para cache o conteúdo das ações de ficheiro sinuoso azure no local
   - Ações de ficheiro azure na nuvem

   Selecione a coluna de destino que corresponda à sua escolha.

1. Dentro da intersecção entre fonte e alvo, uma tabela de células lista cenários de migração disponíveis. Selecione um para ligar diretamente ao guia de migração detalhado.

Um cenário sem ligação ainda não tem um guia de migração publicado. Verifique ocasionalmente esta tabela para obter atualizações. Novos guias serão publicados quando estiverem disponíveis.

| Origem | Destino: </br>Implantação híbrida | Destino: </br>Implantação apenas em nuvem |
|:---|:--|:--|
| | Combinação de ferramentas:| Combinação de ferramentas: |
| Windows Server 2012 R2 e mais tarde | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Caixa de dados Azure File Sync e Azure](storage-sync-offline-data-transfer.md)</li><li>Serviço de Migração de Ficheiros Azure e Armazenamento</li></ul> | <ul><li>Azure File Sync</li><li>Sincronização de ficheiros Azure e Caixa de Dados</li><li>Serviço de Migração de Ficheiros Azure e Armazenamento</li><li>RoboCopy</li></ul> |
| Windows Server 2012 e mais cedo | <ul><li>Sincronização de ficheiros Azure e Caixa de Dados</li><li>Serviço de Migração de Ficheiros Azure e Armazenamento</li></ul> | <ul><li>Serviço de Migração de Ficheiros Azure e Armazenamento</li><li>RoboCopy</li></ul> |
| Armazenamento ligado à rede (NAS) | <ul><li>[Sincronização de ficheiros Azure e RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Linux ou Samba | <ul><li>[Sincronização de ficheiros Azure e RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Microsoft Azure StorSimple Cloud Appliance 8100 ou StorSimple Cloud Appliance 8600 | <ul><li>[Sincronização de ficheiros Azure e StorSimple Cloud Appliance 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Caixa de ferramentas de migração

### <a name="file-copy-tools"></a>Ferramentas de cópia de ficheiros

Existem várias ferramentas de cópia de ficheiros disponíveis da Microsoft e outras. Para selecionar a ferramenta certa para o seu cenário de migração, deve considerar estas questões fundamentais:

* A ferramenta suporta a origem e os locais de destino para a cópia do seu ficheiro?

* A ferramenta suporta a sua trajetória de rede ou protocolos disponíveis (como REST, SMB ou NFS) entre a fonte e os locais de armazenamento alvo?

* A ferramenta preserva a fidelidade de ficheiro necessária suportada pela sua origem e localização-alvo?

    Em alguns casos, o seu armazenamento alvo não suporta a mesma fidelidade que a sua fonte. Se o armazenamento do alvo for suficiente para as suas necessidades, a ferramenta deve corresponder apenas às capacidades de fidelidade de ficheiro do alvo.

* A ferramenta tem funcionalidades que a permitem encaixar na sua estratégia de migração?

    Por exemplo, considere se a ferramenta permite minimizar o seu tempo de inatividade.
    
    Quando uma ferramenta suporta uma opção de espelhar uma fonte para um alvo, pode muitas vezes executá-la várias vezes na mesma fonte e alvo enquanto a fonte permanece acessível.

    A primeira vez que executa a ferramenta, copia a maior parte dos dados. Esta corrida inicial pode durar um pouco. Muitas vezes dura mais do que deseja para desligar a fonte de dados para os seus processos de negócio.

    Ao espelhar uma fonte num alvo (como com **a robocópia /MIR),** pode voltar a colocar a ferramenta na mesma fonte e alvo. A corrida é muito mais rápida porque precisa de transportar apenas as mudanças de origem que ocorrem após a execução anterior. A reexecução de uma ferramenta de cópia desta forma pode reduzir significativamente o tempo de inatividade.

A tabela que se segue classifica as ferramentas da Microsoft e a sua atual adequação às partilhas de ficheiros Azure:

| Recomendado | Ferramenta | Suporte para ações de ficheiros Azure | Preservação da fidelidade do arquivo |
| :-: | :-- | :---- | :---- |
|![Sim, recomendado](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | Suportado. As ações de ficheiros Azure podem ser montadas como unidades de rede. | Fidelidade total.* |
|![Sim, recomendado](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Integrado sinuosamente em ações de ficheiros Azure. | Fidelidade total.* |
|![Sim, recomendado](media/storage-files-migration-overview/circle-green-checkmark.png)| Serviço de Migração de Armazenamento | Indiretamente apoiado. As partilhas de ficheiros Azure podem ser montadas à medida que as unidades de rede nos servidores-alvo sMS. | Fidelidade total.* |
|![Não totalmente recomendado](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Data Box | Suportado. | Não copia metadados. [A Caixa de Dados pode ser utilizada com o Sincronizado de Ficheiros Azure](storage-sync-offline-data-transfer.md). |
|![Não recomendado](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | Suportado. | Não copia metadados. |
|![Não recomendado](media/storage-files-migration-overview/circle-red-x.png)| Explorador do Storage do Azure | Suportado. | Não copia metadados. |
|![Não recomendado](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Suportado. | Não copia metadados. |
|||||

*\*Fidelidade total: cumpre ou excede as capacidades de partilha de ficheiros Azure.*

### <a name="migration-helper-tools"></a>Ferramentas de ajuda à migração

Esta secção descreve ferramentas que o ajudam a planear e executar migrações.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy da Microsoft Corporation

RoboCopy é uma das ferramentas mais aplicáveis às migrações de ficheiros. Vem como parte do Windows. A [documentação roboCopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) principal é um recurso útil para as muitas opções desta ferramenta.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize da JAM Software GmbH

O Azure File Sync dimensiona principalmente com o número de itens (ficheiros e pastas) e não com o valor total de armazenamento. A ferramenta TreeSize permite determinar o número de itens nos volumes do Windows Server.

Pode utilizar a ferramenta para criar uma perspetiva antes de uma implementação do Sync de [Ficheiros Azure](storage-sync-files-deployment-guide.md). Também pode usá-lo quando o tiering da nuvem estiver engatado após a implantação. Nesse cenário, vê-se o número de itens e quais os diretórios que mais utilizam a cache do seu servidor.

A versão testada da ferramenta é a versão 4.4.1. É compatível com ficheiros com nível de nuvem. A ferramenta não causará a recolha de ficheiros hierárquicos durante o seu funcionamento normal.

## <a name="next-steps"></a>Passos seguintes

1. Crie um plano para o qual a implantação de ações de ficheiros Azure (apenas em nuvem ou híbrida) deseja.
1. Reveja a lista de guias de migração disponíveis para encontrar o guia detalhado que corresponde à sua origem e implementação de ações de ficheiros Azure.

Aqui está mais informações sobre as tecnologias Azure Files mencionadas neste artigo:

* [Visão geral da partilha de ficheiros Azure](storage-files-introduction.md)
* [Planear uma implementação da Sincronização de Ficheiros do Azure](storage-sync-files-planning.md)
* [Sincronização de ficheiros Azure: Tiering em nuvem](storage-sync-cloud-tiering.md)
