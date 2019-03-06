---
title: Migrar dados para o Azure File Sync com o Azure Data Box e outros métodos
description: Migre dados em massa de uma forma que é compatível com o Azure File Sync.
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 3b286bbe2c246345bf6acd84a4fc0c400451c706
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57445352"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>Migrar dados em massa para o Azure File Sync
Pode migrar dados em massa para o Azure File Sync de duas formas:

* **Carregar os ficheiros com o Azure File Sync.** Este é o método mais simples. Mover os ficheiros localmente para o Windows Server 2012 R2 ou posterior e instalar o agente de sincronização de ficheiros do Azure. Depois de configurar a sincronização, os ficheiros serão carregados do servidor. (Os nossos clientes experiência atualmente uma velocidade de carregamento médio de 1 TiB sobre dois em dois dias.) Para garantir que o servidor não use muita a largura de banda para o seu datacenter, pode querer configurar um [agenda de limitação de largura de banda](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Transferi os ficheiros offline.** Se não tiver largura de banda suficiente, poderá não conseguir carregar ficheiros para o Azure no período de tempo razoável. O desafio é a sincronização inicial do conjunto completo de arquivos. Para superar esse desafio, utilizar ferramentas de migração de volume offline, tal como o [família de Azure Data Box](https://azure.microsoft.com/services/storage/databox). 

Este artigo explica como migrar os arquivos offline numa forma que seja compatível com o Azure File Sync. Siga estas instruções para evitar conflitos de ficheiros e preservar o ficheiro e listas de controlo de acesso (ACLs) de pasta e carimbos de data / depois de ativar a sincronização.

## <a name="online-migration-tools"></a>Ferramentas de migração online
O processo que descrevemos nos funciona artigo não apenas para o Data Box, mas também para outras ferramentas de migração offline. Também funciona para ferramentas online, como o AzCopy, Robocopy, ou ferramentas de parceiros e serviços. No entanto ultrapassar o primeiro desafio de carregamento, siga os passos neste artigo para usar essas ferramentas de forma que seja compatível com o Azure File Sync.


## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Vantagens de utilizar uma ferramenta para transferência de dados offline
Aqui estão os principais benefícios de usar uma ferramenta de transferência, como o Data Box para a migração offline:

- Não tem de carregar todos os seus ficheiros através da rede. Para espaços de nomes grandes, esta ferramenta foi possível guardar a largura de banda de rede significativo e a hora.
- Quando utiliza o Azure File Sync, não importa qual ferramenta de transferência é usar (Data Box, serviço importar/exportar do Azure etc.), o servidor ao vivo carrega apenas os ficheiros que alterar depois de mover os dados para o Azure.
- O Azure File Sync sincroniza suas ACLs de ficheiros e pastas, mesmo que a ferramenta de migração em massa offline não transportar as ACLs.
- Data Box e o Azure File Sync exigem sem períodos de indisponibilidade. Quando utilizar o Data Box para transferir dados para o Azure, vai utilizar largura de banda de rede com eficiência e preservar a fidelidade de ficheiro. Também manter seu espaço de nomes atualizados através do carregamento de apenas os ficheiros que alterar depois de mover os dados para o Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Pré-requisitos para a transferência de dados offline
Antes de iniciar a transferência de dados offline:

- Migre os seus dados em massa para uma ou várias partilhas de ficheiros do Azure antes de ativar a sincronização com o Azure File Sync.
- Se planeia utilizar o Data Box para a sua migração em massa, reveja os [pré-requisitos de implementação para o Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planear a topologia final do Azure File Sync. Para obter mais informações, consulte [planear uma implementação do Azure File Sync](storage-sync-files-planning.md).
- Selecione a conta de armazenamento do Azure ou a contas que irão conter as partilhas de ficheiros que pretende sincronizar com. Migre os dados em massa para partilhas de transição temporárias que estão na mesma conta de armazenamento ou contas. Pode usar apenas um compartilhamento de final e uma partilha de teste que estão na mesma conta de armazenamento.
- Crie uma nova relação de sincronização com uma localização do servidor. Não é possível utilizar uma relação de sincronização existente para migrar dados em massa.

## <a name="process-for-offline-data-transfer"></a>Processo de transferência de dados offline
Eis como configurar a sincronização de ficheiros do Azure de forma que seja compatível com ferramentas de migração em massa, como o Azure Data Box:

![Diagrama que mostra como configurar a sincronização de ficheiros do Azure](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Passo | Detalhe |
|---|---------------------------------------------------------------------------------------|
| ![Passo 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Encomendar o Data Box](../../databox/data-box-deploy-ordered.md). As ofertas de famílias do Data Box [vários produtos](https://azure.microsoft.com/services/storage/databox/data) para atender às suas necessidades. Quando receber o Data Box, siga seus [documentação para copiar os dados](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) para este caminho UNC na caixa de dados: *\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>*. Aqui, *ShareName* é o nome da partilha de teste. Envie o Data Box para o Azure. |
| ![Passo 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Aguarde até que os ficheiros apresentados nas partilhas de ficheiros do Azure que escolheu como partilhas de transição temporárias. *Não ative a sincronização a esses compartilhamentos.* |
| ![Passo 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Crie uma nova partilha vazia para cada partilha de ficheiros que o Data Box criou para. Esta nova partilha deve estar na mesma conta de armazenamento que a partilha do Data Box. [Como criar uma nova partilha de ficheiros do Azure](storage-how-to-create-file-share.md). |
| ![Passo 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Criar um grupo de sincronização](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) num serviço de sincronização de armazenamento. Referência da partilha vazia como um ponto final da cloud. Repita este passo para cada partilha de ficheiros do Data Box. [Configurar o Azure File Sync](storage-sync-files-deployment-guide.md). |
| ![Passo 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Adicionar o seu diretório de servidor ao vivo como um ponto de final de servidor](storage-sync-files-deployment-guide.md#create-a-server-endpoint). No processo, especifique que moveu os ficheiros para o Azure e as partilhas de testes de referência. Pode ativar ou desativar a cloud em camadas conforme necessário. Ao criar um ponto final do servidor no seu servidor ao vivo, fazer referência a partilha de teste. Sobre o **adicionar ponto final do servidor** painel, em **Offline de transferência de dados**, selecione **ativado**e, em seguida, selecione a partilha de teste que tem de estar na mesma conta de armazenamento como a cloud ponto final. Aqui, a lista de partilhas disponíveis é filtrada pela conta de armazenamento e partilhas que já não estão sincronizados. |

![Captura de ecrã da interface do utilizador do portal do Azure, que mostra como ativar a transferência de dados offline ao criar um novo ponto de final do servidor](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>A sincronizar a partilha
Depois de criar seu ponto final do servidor, a sincronização será iniciado. O processo de sincronização determina se cada ficheiro no servidor também existe a partilha de teste em que o Data Box deposited os ficheiros. Se o ficheiro existe lá, o processo de Sincronização copia o ficheiro da partilha de teste, em vez de carregá-lo a partir do servidor. Se o ficheiro não existe na partilha de teste, ou se uma versão mais recente está disponível no servidor local, o processo de sincronização carrega o ficheiro do servidor local.

> [!IMPORTANT]
> Pode ativar o modo de migração em massa apenas enquanto está a criar um ponto final do servidor. Depois de estabelecer um ponto final do servidor, não é possível integrar dados migrados de em massa de um servidor de sincronização já o espaço de nomes.

## <a name="acls-and-timestamps-on-files-and-folders"></a>As ACLs e os carimbos de data / em ficheiros e pastas
O Azure File Sync garante que as ACLs de ficheiros e pastas são sincronizadas a partir do servidor ao vivo, mesmo que a ferramenta de migração em massa que utilizou não inicialmente ACLs de transporte. Por este motivo, a partilha de teste não precisa conter qualquer ACLs para ficheiros e pastas. Quando ativa a funcionalidade de migração de dados offline à medida que cria um novo ponto de final de servidor, todas as ACLs de ficheiros são sincronizadas no servidor. Também é sincronizados a carimbos de data / recentemente criado e modificado.

## <a name="shape-of-the-namespace"></a>Forma do espaço de nomes
Quando ativar a sincronização, o conteúdo do servidor determina a forma do espaço de nomes. Se os ficheiros são eliminados do servidor local, o instantâneo do Data Box e a migração concluída, estes ficheiros não é necessário mover para o espaço de nomes em direto, sincronize o limite de tempo. Permanecem na partilha de teste, mas eles não são copiados. Isso é necessário porque a sincronização mantém o espaço de nomes, de acordo com o servidor ao vivo. O Data Box *instantâneo* é apenas um teste base para a cópia de ficheiros eficiente. Não é a autoridade para a forma do espaço de nomes em direto.

## <a name="cleaning-up-after-bulk-migration"></a>A limpeza após a migração em massa 
Que o servidor for concluída a sincronização inicial do espaço de nomes, os ficheiros do Data Box em massa-migrated utilizam a partilha de ficheiros de teste. Na **propriedades do ponto final de servidor** painel no portal do Azure, no **Offline de transferência de dados** secção, o estado é alterado de **em curso** para **concluído** . 

![Captura de ecrã do painel de propriedades de ponto final de servidor, onde estão localizados os controles de estado e desativar para a transferência de dados offline](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Agora é possível limpar a partilha de teste para reduzir os custos:

1. Sobre o **propriedades do ponto final de servidor** painel, quando o estado é **concluído**, selecione **desativar a transferência de dados offline**.
2. Considere eliminar a partilha de teste para reduzir os custos. A partilha de teste provavelmente não contém ACLs de ficheiros e pastas, pelo que não é muito útil. Para fins de ponto no tempo de cópia de segurança, criar um real [instantâneo de partilha de sincronização de ficheiros do Azure](storage-snapshots-files.md). Pode [configurar a cópia de segurança do Azure para tirar instantâneos]( ../../backup/backup-azure-files.md) com base numa agenda.

Desativar o modo de transferência de dados offline apenas quando o estado for **concluído** ou quando quiser cancelar devido a uma configuração incorreta. Se desativar o modo durante uma implantação, arquivos irão iniciar o carregamento do servidor, mesmo que a partilha de teste ainda está disponível.

> [!IMPORTANT]
> Depois de desativar o modo de transferência de dados offline, não é possível ativá-la novamente, mesmo que a partilha de teste da migração em massa está ainda disponível.

## <a name="next-steps"></a>Passos Seguintes
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Implementar Azure File Sync](storage-sync-files-deployment-guide.md)
