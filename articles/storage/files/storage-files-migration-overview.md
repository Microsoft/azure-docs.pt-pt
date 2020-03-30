---
title: Migrar para as ações de ficheiros da Azure
description: Saiba mais sobre migrações para as partilhas de ficheiros Azure e encontre o seu guia de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 903ce52120fce7c23c6a3754498b81fc6fc2430f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247321"
---
# <a name="migrate-to-azure-file-shares"></a>Migrar para as ações de ficheiros da Azure

Este artigo aborda os aspetos básicos de uma migração para as ações de ficheiros Azure.

Paralelamente aos fundamentos da migração, este artigo contém uma lista de guias migratórios existentes e individualizados. Estes guias de migração ajudam-no a mover os seus ficheiros para partilhas de ficheiros Azure e são organizados pelo local onde os seus dados residem hoje e qual o modelo de implementação (apenas em nuvem ou híbrido) que planeia mover-se.

## <a name="migration-basics"></a>Básicos de migração

Existem vários tipos diferentes de armazenamento em nuvem disponíveis em Azure. Um aspeto fundamental de uma migração de ficheiros para o Azure é determinar qual a opção de armazenamento do Azure certa para os seus dados.

As ações de ficheiros Azure são ótimas para dados de ficheiros de finalidade geral. Realmente qualquer coisa para que uses uma participação de SMB ou NFS no local. Com o [Azure File Sync,](storage-sync-files-planning.md)pode, opcionalmente, cache o conteúdo de várias partilhas de ficheiros Azure em vários Servidores Windows no local.

Se tiver uma aplicação atualmente em funcionamento num servidor no local, então armazenar ficheiros em ações de ficheiros Azure pode ser o mais adequado para si, dependendo da aplicação. Pode levantar a aplicação para executar em Azure e utilizar as ações de ficheiro sinuosa do Azure como armazenamento partilhado. Também pode considerar [os Discos Azure](../../virtual-machines/windows/managed-disks-overview.md) para este cenário. Para aplicações nascidas na nuvem que não dependem do SMB ou do acesso local a máquinas aos seus dados ou acesso partilhado, o armazenamento de [objetos, como as bolhas Azure,](../blobs/storage-blobs-overview.md)é muitas vezes a melhor escolha.

A chave em qualquer migração é capturar toda a fidelidade aplicável ao migrar os seus ficheiros do seu local de armazenamento atual para o Azure. Uma ajuda na escolha do armazenamento azure certo é também o aspeto da quantidade de fidelidade suportada pela opção de armazenamento Azure e é exigida pelo seu cenário. Os dados do ficheiro de finalidade geral dependem tradicionalmente dos metadados dos ficheiros. Os dados da aplicação podem não. Há dois componentes básicos num ficheiro:

- **Fluxo de dados**: O fluxo de dados de um ficheiro armazena o conteúdo do ficheiro.
- **Metadados**de ficheiros : Os meta dados do ficheiro têm vários sub componentes:
   * Atributos de ficheiro: Apenas leitura, por exemplo.
   * Permissões de ficheiros: Referidas como *permissões NTFS* ou *ficheiros e pastas ACLs*.
   * Carimbos temporais: Mais notavelmente os selos-relógio *criados* e *últimos modificados.*
   * Fluxo de dados alternativo: Um espaço para armazenar maiores quantidades de propriedades não padrão.

A fidelidade do ficheiro, numa migração, pode, portanto, ser definida como a capacidade de armazenar todas as informações de ficheiroaplicáveis na fonte, a capacidade de as transferir com a ferramenta de migração e a capacidade de as armazenar no armazenamento-alvo da migração.

Para garantir que a sua migração prossiga da forma mais suave possível, identifique [a melhor ferramenta de cópia para as suas necessidades](#migration-toolbox) e corresponda a um alvo de armazenamento com a sua fonte.

Tendo em conta as informações anteriores, torna-se claro qual é o armazenamento-alvo para ficheiros de finalidade geral no Azure: Ações de [ficheiros Azure](storage-files-introduction.md). Em comparação com o armazenamento de objetos em blobs Azure, os metadados de ficheiros podem ser armazenados de forma nativa em ficheiros numa partilha de ficheiros Azure.

As ações de ficheiros Azure também preservam a hierarquia de ficheiros e pastas. Além disso,
* As permissões NTFS podem ser armazenadas em ficheiros e pastas tal como estão no local.
* Os utilizadores de AD (ou utilizadores de DS Azure AD) podem aceder de forma nativa a uma partilha de ficheiros Azure. 
    Usam a sua identidade atual e obtêm acesso com base em permissões de partilha, bem como em FICHEIROs e Pastas ACLs. Um comportamento diferente quando os utilizadores se conectam a uma partilha de ficheiros no local.
*  O fluxo de dados alternativo é o principal aspeto da fidelidade de ficheiros que atualmente não pode ser armazenado num ficheiro numa partilha de ficheiros Azure.
   É preservado no local quando o Azure File Sync está envolvido.

* [Saiba mais sobre a autenticação de AD para ações de ficheiros Azure](storage-files-identity-auth-active-directory-enable.md)
* [Saiba mais sobre a autenticação dos Serviços de Domínio de Diretório Ativo azure (AAD DS) para ações de ficheiros Azure](storage-files-identity-auth-active-directory-domain-service-enable.md)

## <a name="migration-guides"></a>Guias de migração

A tabela seguinte enumera guias de migração detalhados.

Navegue-o por:
1. Localize a linha para o sistema de origem em que os seus ficheiros estão atualmente armazenados.
2. Decida se tem como alvo uma implantação híbrida onde utiliza o Azure File Sync para cache o conteúdo de uma ou mais partilhas de ficheiros Azure no local, ou se gosta de utilizar as partilhas de ficheiros Azure diretamente na nuvem. Selecione a coluna-alvo que reflita a sua decisão.
3. Dentro da intersecção entre fonte e alvo, uma tabela de células lista cenários de migração disponíveis. Selecione um deles para ligar diretamente ao guia de migração detalhado.

Um cenário sem ligação ainda não tem um guia de migração publicado. Verifique ocasionalmente esta tabela para obter atualizações. Novos guias serão publicados quando disponíveis.

| Origem | Destino: </br>Implantação híbrida | Destino: </br>Implantação apenas em nuvem |
|:---|:--|:--|
| | Combinação de ferramentas:| Combinação de ferramentas: |
| Windows Server 2012 R2 e mais recente | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Sincronização de ficheiros Azure + DataBox](storage-sync-offline-data-transfer.md)</li><li>Serviço de Migração de Armazenamento + Sincronização de Ficheiros Azure</li></ul> | <ul><li>Azure File Sync</li><li>Sincronização de ficheiros Azure + DataBox</li><li>Serviço de Migração de Armazenamento + Sincronização de Ficheiros Azure</li><li>RoboCopy</li></ul> |
| Windows Server 2012 e mais antigo | <ul><li>Sincronização de ficheiros Azure + DataBox</li><li>Serviço de Migração de Armazenamento + Sincronização de Ficheiros Azure</li></ul> | <ul><li>Serviço de Migração de Armazenamento + Sincronização de Ficheiros Azure</li><li>RoboCopy</li></ul> |
| Armazenamento ligado à rede (NAS) | <ul><li>[Sincronização de ficheiros Azure + RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Linux / Samba | <ul><li>[RoboCopy + Sincronização de Ficheiros Azure](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| StorSimple 8100 / 8600 | <ul><li>[Sincronização de ficheiros Azure + 8020 Aparelho virtual](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple 1200 | <ul><li>[Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Caixa de ferramentas de migração

### <a name="file-copy-tools"></a>Ferramentas de cópia de ficheiros

Existem várias ferramentas de cópia de ficheiros Microsoft e não Microsoft disponíveis. Para selecionar a ferramenta de cópia de ficheiro seletiva certa para o seu cenário de migração, deve considerar três questões fundamentais:

* A ferramenta de cópia suporta a origem e a localização do alvo para uma determinada cópia do ficheiro? 
    * Suporta a sua trajetória de rede e/ou protocolos disponíveis (por exemplo, REST/SMB/NFS) de e para os locais de armazenamento de origem e alvo?
* A ferramenta de cópia preserva a fidelidade de ficheiro necessária que é suportada pela localização fonte/alvo? Em alguns casos, o seu armazenamento alvo não suporta a mesma fidelidade que a sua fonte. Já tomou a decisão de que o armazenamento do alvo é suficiente para as suas necessidades, pelo que a ferramenta de cópia só precisa de corresponder às capacidades de fidelidade do ficheiro dos alvos.
* A ferramenta de cópia tem funcionalidades que a tornam enquadrada na minha estratégia de migração? 
    * Por exemplo, considere se tem opções que lhe permitem minimizar o seu tempo de inatividade. Uma boa pergunta a fazer é: Posso executar esta cópia várias vezes na mesma, por utilizadores ativamente acedidos à localização? Em caso afirmativo, pode reduzir significativamente a quantidade de tempo de inatividade. Compare-o com uma situação em que só pode iniciar a cópia quando a fonte deixar de mudar, para garantir uma cópia completa.

A tabela que se segue classifica as ferramentas da Microsoft e a sua atual adequação às partilhas de ficheiros Azure:

| Recomendado | Ferramenta | Suporta ações de ficheiros Azure | Preserva a fidelidade do ficheiro |
| :-: | :-- | :---- | :---- |
|![Sim, recomendado.](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | Suportado. As ações de ficheiros Azure podem ser montadas como unidades de rede. | Fidelidade total* |
|![Sim, recomendado.](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Integrado sinuosamente em ações de ficheiros Azure. | Fidelidade total* |
|![Sim, recomendado.](media/storage-files-migration-overview/circle-green-checkmark.png)| Serviço de Migração de Armazenamento (SMS) | Indiretamente apoiado. As partilhas de ficheiros Azure podem ser montadas à medida que as unidades de rede num servidor alvo SMS. | Fidelidade total* |
|![Não totalmente recomendado.](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Caixa de Dados Azure | Suportado. | Não copia metadados. [Pode ser usado em combinação com O Sincronizado de Ficheiros Azure](storage-sync-offline-data-transfer.md). |
|![Não recomendada.](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | Suportado. | Não copia metadados. |
|![Não recomendada.](media/storage-files-migration-overview/circle-red-x.png)| Explorador do Storage do Azure | Suportado. | Não copia metadados. |
|![Não recomendada.](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Suportado. | Não copia metadados. |
|||||

*\*Fidelidade total: cumpre ou excede as capacidades de partilha de ficheiros Azure.*

### <a name="migration-helper-tools"></a>Ferramentas de ajuda à migração

Esta secção lista ferramentas que ajudam a planear e executar migrações.

* **RoboCopy, da Microsoft Corporation**

    Uma das ferramentas de cópia mais aplicáveis para arquivar migrações, vem como parte do Microsoft Windows. Devido às muitas opções desta ferramenta, a documentação principal do [RoboCopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) é uma fonte útil.

* **TreeSize, da JAM Software GmbH**

    O Azure File Sync escala principalmente com o número de itens (ficheiros e pastas) e menos com a quantidade total do TiB. A ferramenta pode ser utilizada para determinar o número de ficheiros e pastas nos volumes do Windows Server. Além disso, pode ser usado para criar uma perspetiva antes de uma [implementação](storage-sync-files-deployment-guide.md) do Web file Sync - mas também depois, quando o tiering em nuvem está engatado e você gosta de ver não só o número de itens, mas também em que diretórios o seu cache de servidor é mais utilizado.
    Esta ferramenta (versão testada 4.4.1) é compatível com ficheiros com nível cloud. Não provocará a recolha de ficheiros hierárquicos durante o seu funcionamento normal.


## <a name="next-steps"></a>Passos seguintes

1. Crie um plano para o qual a implantação de ações de ficheiros Azure (apenas em nuvem ou híbrida) se esforce.
2. Reveja a lista de guias de migração disponíveis para encontrar o guia detalhado que corresponde à sua origem e implementação de ações de ficheiros Azure.

Há mais informações disponíveis sobre as tecnologias Azure Files mencionadas neste artigo:

* [Visão geral da partilha de ficheiros Azure](storage-files-introduction.md)
* [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
* [Sincronização de ficheiros Azure: Tiering em nuvem](storage-sync-cloud-tiering.md)
