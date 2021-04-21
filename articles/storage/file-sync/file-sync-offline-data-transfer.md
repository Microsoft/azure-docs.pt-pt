---
title: Migrar dados para a Azure File Sync com Azure Data Box
description: Migrar dados em massa offline que são compatíveis com o Azure File Sync. Evite conflitos de ficheiros e preserve ficheiros e pastas ACLs e cartões de tempo depois de ativar a sincronização.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 453a6f9c83b992df62681f366fcf95a77cda9f25
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797099"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Migrar dados em massa para o Azure File Sync com o Azure DataBox
Pode migrar dados em massa para o Azure File Sync de duas formas:

* **Faça o upload dos seus ficheiros utilizando o Azure File Sync.** Este é o método mais simples. Mova os seus ficheiros localmente para o Windows Server 2012 R2 ou mais tarde, e instale o agente Azure File Sync. Depois de configurar a sincronização, os seus ficheiros serão carregados a partir do servidor. (Os nossos clientes experimentam atualmente uma velocidade média de upload de 1 TiB a cada dois dias.) Para garantir que o seu servidor não utiliza muita largura de banda para o seu datacenter, é melhor configurar um calendário de [aceleração](file-sync-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)da largura de banda .
* **Transfira os seus ficheiros para offline.** Se não tiver largura de banda suficiente, poderá não conseguir enviar ficheiros para o Azure num período de tempo razoável. O desafio é a sincronização inicial de todo o conjunto de ficheiros. Para superar este desafio, utilize ferramentas de migração a granel offline, como a [família Azure Data Box.](https://azure.microsoft.com/services/storage/databox) 

Este artigo explica como migrar ficheiros offline de uma forma compatível com Azure File Sync. Siga estas instruções para evitar conflitos de ficheiros e para preservar as listas de controlo de acesso ao ficheiro e pasta (ACLs) e os cartões de tempo após ativar a sincronização.

## <a name="migration-tools"></a>Ferramentas de migração
O processo que descrevemos neste artigo funciona não só para a Caixa de Dados, mas também para outras ferramentas de migração offline. Também funciona para ferramentas como AzCopy, Robocopy, ou ferramentas e serviços parceiros que funcionam diretamente através da internet. No entanto, para superar o desafio inicial do upload, siga os passos deste artigo para usar estas ferramentas de uma forma compatível com o Azure File Sync.

Em alguns casos, tem de passar de um Servidor do Windows para outro Servidor do Windows antes de adotar o Azure File Sync. [O Serviço de Migração de Armazenamento](/windows-server/storage/storage-migration-service/overview) (SMS) pode ajudar nisso. Quer precise de migrar para uma versão Server OS suportada pelo Azure File Sync (Windows Server 2012R2 e acima) ou simplesmente precisa de migrar porque está a comprar um novo sistema para o Azure File Sync, o SMS tem inúmeras funcionalidades e vantagens que ajudarão a fazer a sua migração sem problemas.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Benefícios da utilização de uma ferramenta para transferir dados offline
Aqui estão os principais benefícios de usar uma ferramenta de transferência como a Data Box para migração offline:

- Não tens de carregar todos os teus ficheiros pela rede. Para grandes espaços de nome, esta ferramenta pode economizar largura de banda de rede significativa e tempo.
- Quando utiliza o Azure File Sync, independentemente da ferramenta de transferência que utilize (Data Box, serviço Azure Import/Export, e assim por diante), o seu servidor ao vivo envia apenas os ficheiros que mudam depois de transferir os dados para o Azure.
- O Azure File Sync sincroniza o seu ficheiro e pasta ACLs mesmo que a ferramenta de migração a granel offline não transporte ACLs.
- A Data Box e a Azure File Sync não requerem tempo de inatividade. Quando utiliza a Data Box para transferir dados para o Azure, utiliza a largura de banda da rede de forma eficiente e preserva a fidelidade do ficheiro. Também mantém o seu espaço de nome atualizado, enviando apenas os ficheiros que mudam depois de mover os dados para OZure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Pré-requisitos para a transferência de dados offline
Não deve ativar a sincronização no servidor em que está a migrar antes de concluir a transferência de dados offline. Outras coisas a ter em conta antes de começar são as seguintes:

- Se planeia utilizar a Caixa de Dados para a sua migração a granel: Reveja os [pré-requisitos de implantação para a Caixa de Dados.](../../databox/data-box-deploy-ordered.md#prerequisites)
- Planeie a sua topologia final de Sincronização de Ficheiros Azure: [Plano para uma implementação de Sincronização de Ficheiros Azure](file-sync-planning.md)
- Selecione a conta de armazenamento Azure que irá deter as ações de ficheiro com as quais pretende sincronizar. Certifique-se de que a sua migração a granel acontece a ações temporárias na mesma Conta de Armazenamento. A migração a granel só pode ser possibilitada utilizando uma parte final e uma participação que residem na mesma conta de armazenamento.
- Uma migração a granel só pode ser utilizada quando se cria uma nova relação de sincronização com a localização do servidor. Não se pode permitir uma migração a granel com uma relação sincronizada existente.


## <a name="process-for-offline-data-transfer"></a>Processo de transferência de dados offline
Eis como configurar o Azure File Sync de uma forma compatível com ferramentas de migração a granel, como a Azure Data Box:

![Diagrama mostrando como configurar o Azure File Sync](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Passo | Detalhes |
|---|---------------------------------------------------------------------------------------|
| ![Passo 1](media/storage-sync-files-offline-data-transfer/bullet-1.png) | [Encomende a sua Caixa de Dados.](../../databox/data-box-deploy-ordered.md) A família Data Box oferece [vários produtos](https://azure.microsoft.com/services/storage/databox/data) para atender às suas necessidades. Quando receber a sua Caixa de Dados, siga a sua [documentação para copiar os seus dados](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) para esta trajetória unc na Caixa de *\\ Dados:<DeviceIPAddres \> \<StorageAccountName_AzFile\> \<ShareName\>*. Aqui, *ShareName* é o nome da participação na encenação. Envie a Caixa de Dados de volta para Azure. |
| ![Passo 2](media/storage-sync-files-offline-data-transfer/bullet-2.png) | Aguarde até que os seus ficheiros apareçam nas ações de ficheiros Azure que escolheu como ações de encenação temporária. *Não permita sincronizar estas ações.* |
| ![Passo 3](media/storage-sync-files-offline-data-transfer/bullet-3.png) | <ul><li>Crie uma nova parte vazia para cada partilha de ficheiros que a Data Box criou para si. Esta nova participação deve estar na mesma conta de armazenamento que a partilha da Caixa de Dados. [Como criar uma nova partilha de ficheiros Azure](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).</li><li>[Crie um grupo de sincronização](file-sync-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) num Serviço de Sincronização de Armazenamento. Refira a parte vazia como um ponto final de nuvem. Repita este passo para cada partilha de ficheiros da Caixa de Dados. [Configurar o Azure File Sync](file-sync-deployment-guide.md).</li></ul> |
| ![Passo 4](media/storage-sync-files-offline-data-transfer/bullet-4.png) | [Adicione o seu diretório de servidor ao vivo como um ponto final do servidor](file-sync-deployment-guide.md#create-a-server-endpoint). No processo, especifique que mudou os ficheiros para Azure e faça referência às ações de encenação. Pode ativar ou desativar o nível da nuvem conforme necessário. Ao criar um ponto final do servidor no seu servidor ao vivo, consulte a partilha de encenação. Na lâmina de ponto final do **servidor Add,** em **Offline Data Transfer**, selecione **Ativado** e, em seguida, selecione a parte de paragem que deve estar na mesma conta de armazenamento que o ponto final da nuvem. Aqui, a lista de ações disponíveis é filtrada por conta de armazenamento e ações que ainda não estão a sincronizar. A imagem que se segue a esta tabela mostra como fazer referência à partilha DataBox durante a criação do ponto final do servidor no portal Azure. |
| ![Passo 5](media/storage-sync-files-offline-data-transfer/bullet-5.png) | Depois de ter adicionado o ponto final do servidor no passo anterior, os dados começam a fluir automaticamente a partir da fonte certa. A [secção de Sincronização da partilha](#syncing-the-share) explica quando os dados fluem a partir da partilha DataBox ou do Servidor do Windows |
| |

![Screenshot da interface de utilizador do portal Azure, mostrando como ativar a transferência de dados offline enquanto cria um novo ponto final do servidor](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Sincronização da parte
Depois de criar o ponto final do servidor, iniciar-se-á uma sincronização. O processo de sincronização determina se cada ficheiro no servidor também existe na parte de posição de encenação onde a Data Box depositou os ficheiros. Se o ficheiro existir lá, o processo de sincronização copia o ficheiro a partir da partilha de encenação em vez de o carregar a partir do servidor. Se o ficheiro não existir na partilha de encenação ou se uma versão mais recente estiver disponível no servidor local, o processo de sincronização carrega o ficheiro a partir do servidor local.

Ao sincronizar a partilha, a sincronização fundirá quaisquer atributos de ficheiros em falta, permissões ou calendários das variantes de ficheiros no servidor local, combinando-as com as suas contrapartidas de ficheiros da partilha DataBox. Isto garante que cada ficheiro e pasta chega com toda a fidelidade possível de ficheiros na partilha de ficheiros Azure.

> [!IMPORTANT]
> Só pode ativar o modo de migração a granel enquanto estiver a criar um ponto final do servidor. Depois de estabelecer um ponto final do servidor, não é possível integrar dados migrados a granel a partir de um servidor já sincronizado no espaço de nomes.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACLs e timetamps em ficheiros e pastas
O Azure File Sync garante que os ACLs de ficheiros e pastas são sincronizados a partir do servidor ao vivo, mesmo que a ferramenta de migração a granel que usou não tenha transportado inicialmente ACLs. Por isso, a partilha de encenação não precisa de conter acLs para ficheiros e pastas. Quando ativa a funcionalidade de migração de dados offline à medida que cria um novo ponto final do servidor, todos os ACLs de ficheiro são sincronizados no servidor. Os novos e modificados os tempos também são sincronizados.

## <a name="shape-of-the-namespace"></a>Forma do espaço de nome
Quando ativa a sincronização, o conteúdo do servidor determina a forma do espaço de nome. Se os ficheiros forem eliminados do servidor local após o instantâneo da Caixa de Dados e o acabamento da migração, estes ficheiros não se movem para o espaço de nome sincronizado ao vivo. Ficam na paragem, mas não são copiados. Isto é necessário porque a sincronização mantém o espaço de nome de acordo com o servidor ao vivo. O instantâneo da Caixa *de Dados* é apenas um local de preparação para uma cópia de ficheiro eficiente. Não é a autoridade para a forma do espaço de nome ao vivo.

## <a name="cleaning-up-after-bulk-migration"></a>Limpeza após migração a granel 
À medida que o servidor completa a sua sincronização inicial do espaço de nomes, os ficheiros migrados a granel da Caixa de Dados utilizam a partilha de ficheiros de encenação. Na lâmina **'Endpoint Properties' do servidor** no portal Azure, na secção transferência de **dados offline,** o estado muda de **In Progress** para **Concluído**. 

![Screenshot da lâmina Server Endpoint Properties, onde estão localizados os controlos de estado e desativação para transferência de dados offline](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Agora pode limpar a parte da encenação para poupar custos:

1. Na lâmina **'Endpoint Properties' do servidor,** quando o estado estiver **concluído,** selecione **Desativar a transferência de dados offline**.
2. Considere apagar a parte da encenação para poupar custos. A partilha de encenação provavelmente não contém ACLs de ficheiros e pastas, por isso é improvável que seja útil. Para efeitos de backup ponto-a-tempo, crie uma imagem real [da partilha de ficheiros Azure sincronizada](../files/storage-snapshots-files.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json). Pode [configurar o Azure Backup para tirar fotografias]( ../../backup/backup-afs.md) num horário.

Desative o modo de transferência de dados offline apenas quando o estado estiver **concluído** ou quando pretender cancelar por causa de uma má configuração. Se desativar o modo durante uma implementação, os ficheiros começarão a ser enviados a partir do servidor, mesmo que a sua parte de realização ainda esteja disponível.

> [!IMPORTANT]
> Depois de desativar o modo de transferência de dados offline, não poderá voltar a ativá-lo, mesmo que a parte de paragem da migração a granel ainda esteja disponível.

## <a name="azure-file-sync-and-pre-seeded-files-in-the-cloud"></a>Azure File Sync e ficheiros pré-semeados na nuvem

Se tiver semeado ficheiros numa partilha de ficheiros Azure por outros meios que não o DataBox - por exemplo, via AzCopy, RoboCopy a partir de uma cópia de segurança na nuvem ou de qualquer outro método - então deve continuar a seguir o [processo de Transferência de Dados Offline](#process-for-offline-data-transfer) descrito neste artigo. Basta ignorar o DataBox à medida que o método os seus ficheiros se movem para a nuvem. No entanto, é fundamental garantir que ainda está a seguir o processo de semear os ficheiros numa *partilha de encenação* e não na parte final, a ação conectada a Azure File Sync.

> [!WARNING]
> **Siga o processo de sementeira de ficheiros numa partilha de encenação e não na** parte final , Azure File Sync conectada. Caso não o faça, podem ocorrer conflitos de ficheiros (ambas as versões de ficheiros serão armazenadas) bem como os ficheiros eliminados no servidor ao vivo podem voltar, se ainda existirem no seu conjunto de ficheiros mais antigo e semeado. Além disso, as mudanças de pasta fundir-se-ão entre si, tornando muito difícil separar o espaço de nome após tal erro.

## <a name="next-steps"></a>Passos seguintes
- [Plano para uma implementação de Sincronização de Ficheiros Azure](file-sync-planning.md)
- [Implementar o Azure File Sync](file-sync-deployment-guide.md)