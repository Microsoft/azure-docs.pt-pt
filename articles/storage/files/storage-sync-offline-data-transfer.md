---
title: Migrar dados para O Sincronizado de Ficheiros Azure com caixa de dados Azure
description: Migrar dados a granel de uma forma compatível com o Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d0331419de89775062f1309c5d854cd7325c68e4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656753"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Migrar dados em massa para o Azure File Sync com o Azure DataBox
Pode migrar dados a granel para o Azure File Sync de duas formas:

* **Faça upload dos seus ficheiros utilizando o Azure File Sync.** Este é o método mais simples. Desloque os seus ficheiros localmente para o Windows Server 2012 R2 ou posteriormente e instale o agente Dessincronização de ficheiros Azure. Depois de configurar a sincronização, os seus ficheiros serão carregados a partir do servidor. (Os nossos clientes experimentam atualmente uma velocidade média de carregamento de 1 TiB a cada dois dias.) Para garantir que o seu servidor não utiliza muita largura de banda para o seu datacenter, é melhor configurar um horário de estrangulamento da largura de [banda](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Transfira os seus ficheiros offline.** Se não tiver largura de banda suficiente, poderá não conseguir enviar ficheiros para o Azure num período de tempo razoável. O desafio é a sincronização inicial de todo o conjunto de ficheiros. Para superar este desafio, utilize ferramentas de migração a granel offline, como a [família Azure Data Box.](https://azure.microsoft.com/services/storage/databox) 

Este artigo explica como migrar ficheiros offline de uma forma compatível com o Azure File Sync. Siga estas instruções para evitar conflitos de ficheiros e para preservar as listas de controlo de acesso de ficheiros e pastas (ACLs) e os selos de tempo depois de ativar a sincronização.

## <a name="migration-tools"></a>Ferramentas de migração
O processo que descrevemos neste artigo funciona não só para data box, mas também para outras ferramentas de migração offline. Também funciona para ferramentas como AzCopy, Robocopy ou ferramentas e serviços parceiros que funcionam diretamente através da internet. No entanto, para superar o desafio inicial de upload, siga os passos deste artigo para usar estas ferramentas de uma forma compatível com o Azure File Sync.

Em alguns casos, é necessário passar de um Servidor Windows para outro Servidor Windows antes de adotar o Webe File Sync. O Serviço de [Migração](https://aka.ms/storagemigrationservice) de Armazenamento (SMS) pode ajudar nisso. Quer precise de migrar para uma versão do Server OS que é suportada pelo Azure File Sync (Windows Server 2012R2 e acima) ou simplesmente precisa de migrar porque está a comprar um novo sistema para o Azure File Sync, o SMS tem inúmeras funcionalidades e vantagens que ajudarão a fazer a sua migração sem problemas.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Benefícios de usar uma ferramenta para transferir dados offline
Aqui estão os principais benefícios de usar uma ferramenta de transferência como data box para migração offline:

- Não tens de enviar todos os teus ficheiros pela rede. Para grandes espaços de nome, esta ferramenta poderia economizar largura de banda e tempo significativos.
- Quando utiliza o Azure File Sync, independentemente da ferramenta de transferência que utilize (Data Box, Serviço de Importação/Exportação Azure, e assim por diante), o seu servidor ao vivo envia apenas os ficheiros que mudam depois de transferir os dados para o Azure.
- O Azure File Sync sincroniza os seus ficheiros e pastas ACLs mesmo que a ferramenta de migração a granel offline não transporte ACLs.
- Data Box e Azure File Sync não requerem tempo de inatividade. Quando utiliza a Data Box para transferir dados para o Azure, utiliza a largura de banda da rede de forma eficiente e preserva a fidelidade do ficheiro. Também mantém o seu espaço de nome atualizado, carregando apenas os ficheiros que mudam depois de mover os dados para o Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Pré-requisitos para a transferência offline de dados
Não deve ativar a sincronização no servidor que está a migrar antes de concluir a transferência de dados offline. Outras coisas a considerar antes de começar são as seguintes:

- Se planeia utilizar a Data Box para a sua migração a granel: Reveja os [pré-requisitos](../../databox/data-box-deploy-ordered.md#prerequisites)de implementação da Data Box .
- Planeie a sua topologia final de sincronização de ficheiros Azure: Plano para uma implementação de Sincronização de [Ficheiros Azure](storage-sync-files-planning.md)
- Selecione conta de armazenamento Azure(s) que deterá as ações de ficheiro com as quais pretende sincronizar. Certifique-se de que a sua migração a granel acontece com ações temporárias de encenação na mesma Conta de Armazenamento. A migração a granel só pode ser possibilitada utilizando uma parte final e uma partilha que residem na mesma conta de armazenamento.
- Uma migração a granel só pode ser utilizada quando se cria uma nova relação de sincronização com a localização do servidor. Não se pode permitir uma migração a granel com uma relação sincronizada existente.


## <a name="process-for-offline-data-transfer"></a>Processo para transferência de dados offline
Aqui está como configurar o Azure File Sync de uma forma compatível com ferramentas de migração a granel, como a Caixa de Dados Azure:

![Diagrama mostrando como configurar o Sincronizado de Ficheiros Azure](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Passo | Detalhe |
|---|---------------------------------------------------------------------------------------|
| ![Passo 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Encomende a sua Caixa de Dados.](../../databox/data-box-deploy-ordered.md) A família Data Box oferece [vários produtos](https://azure.microsoft.com/services/storage/databox/data) para atender às suas necessidades. Quando receber a sua Caixa de Dados, siga a sua [documentação para copiar os seus dados](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) para este caminho unc na Caixa de Dados: * \\<DeviceIPAddres\>\<StorageAccountName_AzFile\>\<ShareName\>*. Aqui, *ShareName* é o nome da partilha de encenação. Envie a Caixa de Dados de volta para Azure. |
| ![Passo 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Espere até que os seus ficheiros apareçam nas ações de arquivo do Azure que escolheu como ações temporárias de encenação. *Não permita sincronizar estas ações.* |
| ![Passo 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | <ul><li>Crie uma nova parte vazia para cada partilha de ficheiros que a Data Box criou para si. Esta nova parte deve estar na mesma conta de armazenamento que a parte data Box. [Como criar uma nova partilha de ficheiros Azure.](storage-how-to-create-file-share.md)</li><li>[Crie um grupo de sincronização](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) num Serviço de Sincronização de Armazenamento. Referência a parte vazia como um ponto final de nuvem. Repita este passo para cada partilha de ficheiros data Box. Configurar o Sincronizado de [Ficheiros Azure](storage-sync-files-deployment-guide.md).</li></ul> |
| ![Passo 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Adicione o seu diretório de servidor ao vivo como ponto final do servidor](storage-sync-files-deployment-guide.md#create-a-server-endpoint). No processo, especifique que transferiu os ficheiros para o Azure e faça referência às ações de encenação. Pode ativar ou desativar o tiering da nuvem conforme necessário. Ao criar um ponto final do servidor no seu servidor ao vivo, consulte a parte de encenação. Na lâmina de ponto final do **servidor Adicionar,** em transferência **de dados offline,** selecione **Ativado,** e, em seguida, selecione a parte de paragem que deve estar na mesma conta de armazenamento que o ponto final da nuvem. Aqui, a lista de ações disponíveis é filtrada por conta de armazenamento e ações que ainda não estão sincronizadas. A imagem que segue esta tabela mostra como fazer referência à partilha DataBox durante a criação de pontofinal do servidor no portal Azure. |
| ![Passo 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | Uma vez adicionado o ponto final do servidor no passo anterior, os dados começam a fluir automaticamente a partir da fonte certa. A [secção de sincronização da partilha](#syncing-the-share) explica quando os dados fluem da partilha DataBox ou do Servidor windows |
| |

![Screenshot da interface de utilizador do portal Azure, mostrando como ativar a transferência de dados offline enquanto cria um novo ponto final do servidor](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Sincronizar a parte
Depois de criar o ponto final do servidor, a sincronização começará. O processo de sincronização determina se cada ficheiro no servidor também existe na parte de paragem onde a Data Box depositou os ficheiros. Se o ficheiro existir lá, o processo de sincronização copia o ficheiro a partir da parte de encenação em vez de o enviar a partir do servidor. Se o ficheiro não existir na parte de paragem, ou se uma versão mais recente estiver disponível no servidor local, o processo de sincronização envia o ficheiro a partir do servidor local.

Ao sincronizar a parte, a sincronização irá fundir quaisquer atributos de ficheiros, permissões ou carimbos de tempo em falta das variantes de ficheiros no servidor local, combinando-os com as suas congéneres de ficheiros a partir da partilha DataBox. Isto garante que cada ficheiro e pasta chega com toda a fidelidade possível de ficheiros na partilha de ficheiros Azure.

> [!IMPORTANT]
> Só pode ativar o modo de migração a granel enquanto estiver a criar um ponto final do servidor. Depois de estabelecer um ponto final do servidor, não pode integrar dados migrados a granel a partir de um servidor já sincronizado no espaço de nome.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACLs e carimbos de tempo em ficheiros e pastas
O Azure File Sync garante que os ACLs de ficheiros e pastas estão sincronizados a partir do servidor ao vivo, mesmo que a ferramenta de migração a granel que utilizou não tenha inicialmente transportado ACLs. Por causa disso, a partilha de encenação não precisa de conter quaisquer ACLs para ficheiros e pastas. Quando ativa a funcionalidade de migração de dados offline à medida que cria um novo ponto final do servidor, todos os ACLs de ficheiros estão sincronizados no servidor. Os selos temporais recém-criados e modificados também são sincronizados.

## <a name="shape-of-the-namespace"></a>Forma do espaço de nome
Quando ativa a sincronização, o conteúdo do servidor determina a forma do espaço de nome. Se os ficheiros forem eliminados do servidor local após o instantâneo da Data Box e o acabamento da migração, estes ficheiros não se movem para o espaço de nome sincronia ao vivo e sincronizado. Ficam na parte da encenação, mas não são copiados. Isto é necessário porque a sincronização mantém o espaço de nome de acordo com o servidor ao vivo. O *instantâneo* da Data Box é apenas um local de preparação para cópias eficientes de ficheiros. Não é a autoridade para a forma do espaço de nome ao vivo.

## <a name="cleaning-up-after-bulk-migration"></a>Limpeza após migração a granel 
À medida que o servidor completa a sincronização inicial do espaço de nome, os ficheiros migrados em massa da Data Box utilizam a partilha de ficheiros de encenação. Na lâmina 'Propriedades de **Endpoint' do Servidor** no portal Azure, na secção de Transferência de **Dados Offline,** o estado muda de **In Progress** para **Concluído**. 

![Screenshot da lâmina 'Propriedades de Endpoint' do Servidor, onde o estado e desativam os controlos para a transferência de dados offline estão localizados](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Agora pode limpar a parte de preparação para economizar custos:

1. Na lâmina **'Propriedades'** do Ponto final do servidor, quando o estado estiver **concluído,** selecione **Desativar**a transferência de dados offline .
2. Considere apagar a parte de preparação para economizar custos. A parte de encenação provavelmente não contém ficheiros e pastas ACLs, por isso é improvável que seja útil. Para efeitos de backup ponto-a-tempo, crie uma imagem real [da partilha de ficheiros Azure sincronizada](storage-snapshots-files.md). Pode [configurar o Azure Backup para tirar fotografias]( ../../backup/backup-afs.md) num horário.

Desative o modo de transferência de dados offline apenas quando o estado estiver **concluído** ou quando pretender cancelar devido a uma configuração errada. Se desativar o modo durante uma implementação, os ficheiros começarão a ser carregados a partir do servidor, mesmo que a sua parte de paragem ainda esteja disponível.

> [!IMPORTANT]
> Depois de desativar o modo de transferência de dados offline, não pode voltar a permitir, mesmo que a parte de paragem da migração a granel ainda esteja disponível.

## <a name="next-steps"></a>Passos seguintes
- [Plano para uma implementação de Sincronização de Ficheiros Azure](storage-sync-files-planning.md)
- [Implementar Azure File Sync](storage-sync-files-deployment-guide.md)
