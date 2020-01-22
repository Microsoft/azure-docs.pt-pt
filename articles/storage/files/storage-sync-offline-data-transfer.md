---
title: Migrar dados para Sincronização de Arquivos do Azure com Azure Data Box
description: Migre dados em massa de forma que sejam compatíveis com Sincronização de Arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b1c167c71907e5f8af1006dfabd8f81ce4425d09
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76291163"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>Migrar dados em massa para Sincronização de Arquivos do Azure
Você pode migrar dados em massa para Sincronização de Arquivos do Azure de duas maneiras:

* **Carregue seus arquivos usando sincronização de arquivos do Azure.** Esse é o método mais simples. Mova os arquivos localmente para o Windows Server 2012 R2 ou posterior e instale o agente de Sincronização de Arquivos do Azure. Depois de configurar a sincronização, os arquivos serão carregados do servidor. (Atualmente, nossos clientes enfrentam uma velocidade de upload média de 1 TiB a cada dois dias.) Para garantir que o servidor não use muita largura de banda para seu datacenter, talvez você queira configurar um [agendamento de limitação de largura de banda](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Transfira os arquivos offline.** Se você não tiver largura de banda suficiente, talvez não consiga carregar arquivos no Azure em um período de tempo razoável. O desafio é a sincronização inicial de todo o conjunto de arquivos. Para superar esse desafio, use ferramentas de migração em massa offline, como a [família de Azure data Box](https://azure.microsoft.com/services/storage/databox). 

Este artigo explica como migrar arquivos offline de forma que seja compatível com Sincronização de Arquivos do Azure. Siga estas instruções para evitar conflitos de arquivo e preservar as listas de controle de acesso (ACLs) de arquivo e pasta e os carimbos de data/hora depois de habilitar a sincronização.

## <a name="migration-tools"></a>Ferramentas de migração
O processo que descrevemos neste artigo funciona não apenas para Data Box, mas também para outras ferramentas de migração offline. Ele também funciona para ferramentas como AzCopy, Robocopy ou ferramentas de parceiros e serviços que funcionam diretamente pela Internet. No entanto, para superar o desafio de upload inicial, siga as etapas neste artigo para usar essas ferramentas de forma que seja compatível com Sincronização de Arquivos do Azure.

Em alguns casos, você precisa passar de um Windows Server para outro Windows Server antes de adotar Sincronização de Arquivos do Azure. O [serviço de migração de armazenamento](https://aka.ms/storagemigrationservice) (SMS) pode ajudar com isso. Se você precisa migrar para uma versão de sistema operacional do servidor com suporte pelo Sincronização de Arquivos do Azure (Windows Server 2012R2 e up) ou simplesmente precisa migrar porque você está comprando um novo sistema para Sincronização de Arquivos do Azure, o SMS tem vários recursos e vantagens que ajudarão a obter seu MIGR feito sem problemas.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Benefícios de usar uma ferramenta para transferir dados offline
Aqui estão os principais benefícios do uso de uma ferramenta de transferência como Data Box para a migração offline:

- Você não precisa carregar todos os seus arquivos pela rede. Para namespaces grandes, essa ferramenta pode economizar tempo e largura de banda de rede significativa.
- Quando você usa Sincronização de Arquivos do Azure, não importa qual ferramenta de transferência usa (Data Box, serviço de importação/exportação do Azure e assim por diante), o servidor ao vivo carrega somente os arquivos que são alterados depois que você move os dados para o Azure.
- O Sincronização de Arquivos do Azure sincroniza as ACLs de arquivo e pasta mesmo se a ferramenta de migração em massa offline não transportar ACLs.
- Data Box e Sincronização de Arquivos do Azure não exigem nenhum tempo de inatividade. Ao usar Data Box para transferir dados para o Azure, você usa a largura de banda de rede com eficiência e preserva a fidelidade do arquivo. Você também mantém seu namespace atualizado carregando apenas os arquivos que são alterados depois de mover os dados para o Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Pré-requisitos para a transferência de dados offline
Você não deve habilitar a sincronização no servidor que está migrando antes de concluir a transferência de dados offline. Outras coisas a serem consideradas antes de começar são as seguintes:

- Se você planeja usar Data Box para sua migração em massa: examine os [pré-requisitos de implantação para data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planejar a topologia de Sincronização de Arquivos do Azure final: [planejar uma implantação de sincronização de arquivos do Azure](storage-sync-files-planning.md)
- Selecione as contas de armazenamento do Azure que irão conter os compartilhamentos de arquivos com os quais você deseja sincronizar. Certifique-se de que a migração em massa ocorra para compartilhamentos temporários de preparo na mesma conta (s) de armazenamento. A migração em massa só pode ser habilitada utilizando um compartilhamento final e um de preparo que residem na mesma conta de armazenamento.
- Uma migração em massa só pode ser utilizada quando você cria uma nova relação de sincronização com um local do servidor. Não é possível habilitar uma migração em massa com uma relação de sincronização existente.


## <a name="process-for-offline-data-transfer"></a>Processo para transferência de dados offline
Veja como configurar Sincronização de Arquivos do Azure de forma que seja compatível com ferramentas de migração em massa, como Azure Data Box:

![Diagrama mostrando como configurar Sincronização de Arquivos do Azure](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Passo | Detalhe |
|---|---------------------------------------------------------------------------------------|
| ![Passo 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Ordene seu data Box](../../databox/data-box-deploy-ordered.md). A família de Data Box oferece [vários produtos](https://azure.microsoft.com/services/storage/databox/data) para atender às suas necessidades. Ao receber sua Data Box, siga sua [documentação para copiar os dados](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) para esse caminho UNC no Data Box: *\\< DeviceIPAddres\>* \<StorageAccountName_AzFile\>ShareName \<. Aqui, *ShareName* é o nome do compartilhamento de preparo. Envie o Data Box de volta para o Azure. |
| ![Passo 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Aguarde até que os arquivos apareçam nos compartilhamentos de arquivos do Azure que você escolheu como compartilhamentos temporários de preparo. *Não habilite a sincronização para esses compartilhamentos.* |
| ![Passo 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Crie um novo compartilhamento vazio para cada compartilhamento de arquivos que Data Box criado para você. Esse novo compartilhamento deve estar na mesma conta de armazenamento que o compartilhamento de Data Box. [Como criar um novo compartilhamento de arquivos do Azure](storage-how-to-create-file-share.md). |
| ![Passo 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Crie um grupo de sincronização](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) em um serviço de sincronização de armazenamento. Referencie o compartilhamento vazio como um ponto de extremidade de nuvem. Repita essa etapa para cada compartilhamento de arquivos Data Box. [Configurar sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md). |
| ![Passo 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Adicione seu diretório do Live Server como um ponto de extremidade do servidor](storage-sync-files-deployment-guide.md#create-a-server-endpoint). No processo, especifique que você moveu os arquivos para o Azure e faça referência aos compartilhamentos de preparo. Você pode habilitar ou desabilitar a disposição em camadas de nuvem conforme necessário. Ao criar um ponto de extremidade do servidor em seu servidor dinâmico, faça referência ao compartilhamento de preparo. Na folha **Adicionar ponto de extremidade do servidor** , em **transferência de dados offline**, selecione **habilitado**e, em seguida, selecione o compartilhamento de preparo que deve estar na mesma conta de armazenamento que o ponto de extremidade da nuvem. Aqui, a lista de compartilhamentos disponíveis é filtrada por conta de armazenamento e compartilhamentos que ainda não estão sincronizando. |

![Captura de tela da interface do usuário do portal do Azure, mostrando como habilitar a transferência de dados offline durante a criação de um novo ponto de extremidade do servidor](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Sincronizando o compartilhamento
Depois de criar o ponto de extremidade do servidor, a sincronização será iniciada. O processo de sincronização determina se cada arquivo no servidor também existe no compartilhamento de preparo em que Data Box deposita os arquivos. Se o arquivo existir lá, o processo de sincronização copiará o arquivo do compartilhamento de preparo em vez de carregá-lo do servidor. Se o arquivo não existir no compartilhamento de preparo ou se uma versão mais recente estiver disponível no servidor local, o processo de sincronização carregará o arquivo do servidor local.

> [!IMPORTANT]
> Você pode habilitar o modo de migração em massa somente enquanto estiver criando um ponto de extremidade do servidor. Depois de estabelecer um ponto de extremidade do servidor, você não pode integrar dados de migração em massa de um servidor já sincronizado no namespace.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACLs e carimbos de data/hora em arquivos e pastas
Sincronização de Arquivos do Azure garante que as ACLs de arquivo e pasta sejam sincronizadas a partir do servidor ativo, mesmo que a ferramenta de migração em massa que você usou não tenha transportado inicialmente as ACLs. Por isso, o compartilhamento de preparo não precisa conter nenhuma ACL para arquivos e pastas. Quando você habilita o recurso de migração de dados offline ao criar um novo ponto de extremidade do servidor, todas as ACLs de arquivo são sincronizadas no servidor. Os carimbos de data/hora recém-criados e modificados também são sincronizados.

## <a name="shape-of-the-namespace"></a>Forma do namespace
Quando você habilita a sincronização, o conteúdo do servidor determina a forma do namespace. Se os arquivos forem excluídos do servidor local após a conclusão do instantâneo de Data Box e da migração, esses arquivos não serão movidos para o namespace de sincronização ao vivo. Eles permanecem no compartilhamento de preparo, mas não são copiados. Isso é necessário porque a sincronização mantém o namespace de acordo com o servidor dinâmico. O *instantâneo* de data Box é apenas um aterramento de preparo para uma cópia de arquivo eficiente. Não é a autoridade para a forma do namespace ao vivo.

## <a name="cleaning-up-after-bulk-migration"></a>Limpando após a migração em massa 
Conforme o servidor conclui sua sincronização inicial do namespace, o Data Box arquivos migrados em massa usam o compartilhamento de arquivos de preparo. Na folha **de propriedades de ponto de extremidade do servidor** no portal do Azure, na seção **transferência de dados offline** , o status muda de **em andamento** para **concluído**. 

![Captura de tela da folha de propriedades do ponto de extremidade do servidor, em que os controles status e desabilitar para transferência de dados offline estão localizados](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Agora você pode limpar o compartilhamento de preparo para economizar custos:

1. Na folha **Propriedades do ponto de extremidade do servidor** , quando o status for **concluído**, selecione **desabilitar transferência de dados offline**.
2. Considere excluir o compartilhamento de preparo para economizar custos. O compartilhamento de preparo provavelmente não contém ACLs de arquivo e pasta, portanto, isso não é muito útil. Para fins de backup de ponto no tempo, crie um [instantâneo real da sincronização do compartilhamento de arquivos do Azure](storage-snapshots-files.md). Você pode [Configurar o backup do Azure para tirar instantâneos]( ../../backup/backup-afs.md) de uma agenda.

Desabilite o modo de transferência de dados offline somente quando o estado for **concluído** ou quando desejar cancelar devido a uma configuração incorreta. Se você desabilitar o modo durante uma implantação, os arquivos começarão a ser carregados do servidor, mesmo que o compartilhamento de preparo ainda esteja disponível.

> [!IMPORTANT]
> Depois de desabilitar o modo de transferência de dados offline, você não poderá habilitá-lo novamente, mesmo que o compartilhamento de preparo da migração em massa ainda esteja disponível.

## <a name="next-steps"></a>Passos seguintes
- [Planejar uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Implementar o Azure File Sync](storage-sync-files-deployment-guide.md)
