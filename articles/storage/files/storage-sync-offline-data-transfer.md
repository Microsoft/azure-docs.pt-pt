---
title: Utilize o Data Box e outros métodos para ingestão offline no Azure File Sync.
description: Processo e as melhores práticas para ativar a sincronização em massa compatível migração suportam.
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 6781ce4a3cf5f6f883678ac848162790d45a5a0f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669810"
---
# <a name="migrate-to-azure-file-sync"></a>Migrar para o Azure File Sync
Existem várias opções para mover para o Azure File Sync:

### <a name="uploading-files-via-azure-file-sync"></a>Carregar ficheiros através do Azure File Sync
A opção mais simples é mover os ficheiros localmente para o Windows Server 2012 R2 ou posterior e instalar o agente de sincronização de ficheiros do Azure. Assim que a sincronização estiver configurada, irão carregar os ficheiros do servidor. Atualmente, está a experienciar uma velocidade de carregamento médio em todos os nossos clientes de 1 TB sobre todas as 2 dias.
Considere um [agenda de limitação de largura de banda](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter) para garantir que o servidor está a funcionar bem no seu centro de dados.

### <a name="offline-bulk-transfer"></a>Transferência em volume offline
Embora a carregar certamente seja a opção mais simples, poderá não funcionar para se a largura de banda disponível não permitirá a sincronizar os seus ficheiros para o Azure no período de tempo razoável. O desafio para superar aqui é a sincronização inicial do conjunto completo de arquivos. Em seguida, o Azure File Sync apenas irá mover as alterações à medida que ocorrem no espaço de nomes, que normalmente utiliza muito menos largura de banda.
Para superar esse desafio de carregamento inicial, as ferramentas de migração de volume offline, como o Azure [família Data Box](https://azure.microsoft.com/services/storage/databox) pode ser utilizado. O seguinte artigo se concentra no processo que tem de seguir para beneficiar a migração offline de uma forma compatível de sincronização de ficheiros do Azure. Ele descreve as melhores práticas que o ajudará a evitar os ficheiros de conflito e preservar suas ACLs de ficheiros e pastas e carimbos de data / depois de ativar a sincronização.

### <a name="online-migration-tools"></a>Ferramentas de migração online
O processo descrito a seguir não funciona apenas para o Data Box. Funciona para qualquer ferramenta de migração offline (por exemplo, o Data Box) ou ferramentas online, como o AzCopy, Robocopy ou de terceiros de ferramentas e serviços. Portanto, independentemente do método para vencer o desafio de carregamento inicial, ainda é importante seguir os passos descritos abaixo para utilizar estas ferramentas na sincronização de uma forma compatível.


## <a name="offline-data-transfer-benefits"></a>Benefícios de transferência de dados offline
Os principais benefícios da migração offline ao usar uma caixa de dados são os seguintes:

- Após a migração de ficheiros para o Azure através de um processo de transferência em volume offline, como o Data Box, não precisa de carregar todos os arquivos do seu servidor através da rede. Para espaços de nomes grandes, isso pode significar economias significativas na largura de banda de rede e a hora.
- Depois de utilizar o Azure File Sync, independentemente do modo de transporte usado (Data Box, importação do Azure, etc.), o seu servidor ao vivo carrega apenas os ficheiros que foram alterados desde que enviados dados para o Azure.
- O Azure File Sync garante que as ACLs de ficheiros e pastas são sincronizadas também - mesmo que o produto de migração em massa offline não ACLs de transporte.
- Ao utilizar o Azure Data Box e sincronização de ficheiros do Azure, não há nenhum tempo de inatividade. Utilizar o Data Box para transferir dados para o Azure faz uso eficiente da largura de banda de rede, preservando a fidelidade de ficheiro. Ele também mantém seu espaço de nomes atualizado através do carregamento de apenas os ficheiros que foram alterados desde o Data Box foi enviado.

## <a name="plan-your-offline-data-transfer"></a>Planear a sua transferência de dados offline
Antes de começar, reveja as seguintes informações:

- Conclua a migração em massa para uma ou várias partilhas de ficheiros do Azure antes de ativar a sincronização com o Azure File Sync.
- Se planeia utilizar o Data Box para a sua migração em massa: Reveja os [pré-requisitos de implementação para o Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planear a topologia final do Azure File Sync: [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- Selecione a conta (s) de armazenamento do Azure, que irá conter as partilhas de ficheiros que pretende sincronizar com. Certifique-se de que a migração em massa acontece às partilhas de transição temporárias na conta de armazenamento mesmo (s). Só pode ser ativada em massa de migração utilizando um final - e uma partilha de teste que residem na mesma conta de armazenamento.
- Uma migração em massa só pode ser utilizada quando cria uma nova relação de sincronização com uma localização do servidor. Não é possível ativar uma migração em massa com uma relação de sincronização existente.

## <a name="offline-data-transfer-process"></a>Processo de transferência de dados offline
Esta secção descreve o processo de configuração de sincronização de ficheiros do Azure de uma forma compatível com ferramentas de migração em massa, como o Azure Data Box.

![Visualizar as etapas do processo que também são explicadas em detalhe num parágrafo seguinte](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Passo | Detalhe |
|---|---------------------------------------------------------------------------------------|
| ![Passo do processo 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Encomendar o Data Box](../../databox/data-box-deploy-ordered.md). Existem [várias ofertas dentro da família Data Box](https://azure.microsoft.com/services/storage/databox/data) para satisfazer as suas necessidades. Receber o Data Box e siga o Data Box [documentação para copiar os dados](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box). Certifique-se de que os dados são copiados para este caminho UNC na caixa de dados: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>` onde o `ShareName` é o nome da partilha de teste. Envie o Data Box para o Azure. |
| ![Passo do processo 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Aguarde até que os ficheiros apresentados nas partilhas de ficheiros do Azure designada como partilhas de transição temporárias. **Não ative a sincronização a esses compartilhamentos!** |
| ![Passo do processo 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Crie uma nova partilha, que está vazia para cada partilha de ficheiros que o Data Box criou para. Certifique-se de que esta nova partilha está na mesma conta de armazenamento que a partilha do Data Box. [Como criar uma nova partilha de ficheiros do Azure](storage-how-to-create-file-share.md). |
| ![Passo do processo 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Criar um grupo de sincronização](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) um serviço de sincronização de armazenamento e uma referência a partilha vazia como um ponto final da cloud. Repita este passo para cada partilha de ficheiros do Data Box. Reveja os [implementar Azure File Sync](storage-sync-files-deployment-guide.md) orientar e siga os passos necessários para configurar a sincronização de ficheiros do Azure. |
| ![Passo de processos 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Adicionar o seu diretório de servidor ao vivo como um ponto de final de servidor](storage-sync-files-deployment-guide.md#create-a-server-endpoint). Especifique no processo que já moveu os ficheiros para o Azure e as partilhas de testes de referência. É a opção para ativar ou desativar a cloud em camadas conforme necessário. Ao criar um ponto final do servidor no seu servidor ao vivo, precisa fazer referência a partilha de teste. Ative a "Offline de transferência de dados" (imagem abaixo) de mensagens em fila no painel do novo ponto final de servidor e fazer referência a partilha de teste que tem de residir na mesma conta de armazenamento como ponto final da cloud. A lista de partilhas disponíveis é filtrada pela conta de armazenamento e partilhas que já não estão sincronizados. |

![Visualizar a interface de utilizador do portal do Azure para ativar o acesso Offline de transferência de dados ao criar um novo ponto de final do servidor.](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>A sincronizar a partilha
Depois de criar o seu ponto final do servidor, irá iniciar a sincronização. Para cada ficheiro que existe no servidor, a sincronização irá determinar se este ficheiro também existe a partilha de teste em que o Data Box deposited os ficheiros e por isso, sincronização copiará o ficheiro da partilha de teste, em vez de carregá-lo a partir do servidor. Se o ficheiro não existe na partilha de teste ou uma versão mais recente está disponível no servidor local, em seguida, sincronização irá carregar o ficheiro do servidor local.

> [!IMPORTANT]
> Apenas pode ativar o modo de migração em massa durante a criação de um ponto final do servidor. Assim que um ponto final do servidor é estabelecido, não é atualmente possível integrar em massa migrados dados a partir de um servidor de sincronização já para o espaço de nomes.

## <a name="acls-and-timestamps-on-files-and-folders"></a>As ACLs e os carimbos de data / em ficheiros e pastas
O Azure File Sync irá garantir que as ACLs de ficheiros e pastas são sincronizadas a partir do servidor ao vivo, mesmo que a ferramenta de migração em massa que foi utilizada não a transportar as ACLs com inicialmente. Isso significa que está OK para a partilha de teste a que não contenha quaisquer ACLs em ficheiros e pastas. Quando ativa a funcionalidade de migração de dados offline à medida que cria um novo ponto de final de servidor, serão sincronizadas ACLs naquele momento para todos os ficheiros no servidor. O mesmo é verdadeiro para criar - e modificado-carimbos.

## <a name="shape-of-the-namespace"></a>Forma do espaço de nomes
A forma do espaço de nomes é determinada pelo que se encontra no servidor quando a sincronização está ativada. Se os ficheiros são eliminados no servidor local depois do Data Box "-instantâneo" e - migração, em seguida, estes ficheiros não ser colocados em espaço de nomes em direto, a sincronização. Eles ainda estarão na partilha de teste, mas nunca copiados. Que é o comportamento desejado, como sincronização mantém o espaço de nomes, de acordo com o servidor ao vivo. O Data Box "instantâneo" é apenas um teste base para a cópia de ficheiros eficiente e não a autoridade para a forma do espaço de nomes em direto.

## <a name="finishing-bulk-migration-and-clean-up"></a>Conclusão da migração em massa e limpeza
Captura de ecrã abaixo mostra o painel de propriedades do ponto final de servidor no portal do Azure. Na secção de transferência de dados offline, pode observar o estado do processo. Ela mostrará "Em curso" ou "Concluído".

Depois do servidor de concluída a sincronização inicial de todo o namespace, ele irá terminar aproveitando o ficheiro de teste partilha com a maior parte do Data Box migrados ficheiros. Observe nas propriedades do ponto final de servidor para a transferência de dados offline que esteja a alterar o estado como "Complete". Neste momento, é possível limpar a partilha de teste para poupar no custo:

1. Pressione "Desativar a transferência de dados offline" nas propriedades do ponto final de servidor, quando o estado é "concluído".
2. Considere eliminar a partilha de teste para poupar no custo. A partilha de teste é pouco provável que contenham arquivos e pastas ACLs e assim tem uso limitado. Para fins de "ponto no tempo de" cópia de segurança, em vez disso, crie um real [instantâneo de partilha de sincronização de ficheiros do Azure](storage-snapshots-files.md). Pode [ativar cópia de segurança do Azure tirar instantâneos]( ../../backup/backup-azure-files.md) com base numa agenda.

![Visualizar a interface de utilizador do portal do Azure para as propriedades de ponto final do servidor onde estão localizados os controles de estado e desativar para Offline de transferência de dados.](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Apenas deve desativar este modo quando o estado é "concluído" ou pretende realmente abortar devido a configuração incorreta. Se está a desativar a modo a meio uma implementação legítima, ficheiros começarão a carregar a partir do servidor, mesmo que a partilha de teste ainda está disponível.

> [!IMPORTANT]
> Depois de desativar a transferência de dados offline não existe nenhuma forma de ativá-la novamente, mesmo que a partilha de teste da migração em massa está ainda disponível.

## <a name="next-steps"></a>Passos Seguintes
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Implementar Azure File Sync](storage-sync-files-deployment-guide.md)
