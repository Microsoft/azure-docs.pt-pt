---
title: Orientação de recuperação de desastres para Avere vFXT para Azure
description: Como proteger os dados em Avere vFXT para o Azure de eliminação ou interrupções acidentais
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 28278f76497d6e9d0fee221bb4ef32fe6d369db0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966653"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Orientação de recuperação de desastres para Avere vFXT para Azure

Este artigo descreve estratégias para proteger o seu Avere vFXT para fluxo de trabalho azure e dá orientações para fazer backup de dados para que possa recuperar de acidentes ou interrupções.

Avere vFXT para azure armazena temporariamente dados na sua cache. Os dados são armazenados a longo prazo em sistemas de armazenamento back-end - sistemas de hardware no local, contentores de armazenamento Azure Blob, ou ambos.

Para evitar interrupções e possíveis perdas de dados, considere estas quatro áreas:

* Proteção contra o tempo de inatividade se um Avere vFXT para o sistema Azure ficar indisponível
* Proteger dados na cache do cluster
* Proteger dados no armazenamento de hardware nas back-end
* Proteger dados no armazenamento em nuvem de Azure Blob

Cada Avere vFXT para o cliente Azure deve criar o seu próprio plano abrangente de recuperação de desastres que inclua planos para estes itens. Também pode construir resiliência em aplicações que utiliza com o cluster vFXT. Leia os links em [próximos passos](#next-steps) para pedir ajuda.

## <a name="protect-against-downtime"></a>Proteger contra o tempo de inatividade

O despedimento é incorporado no Avere vFXT para o produto Azure:

* O cluster está altamente disponível, e os nós individuais do cluster podem falhar com a interrupção mínima.
* Os dados alterados na cache são escritos regularmente para ficheiros centrais de back-end (hardware NAS ou Azure Blob) para armazenamento a longo prazo.

Cada aglomerado Avere vFXT para Azure deve estar localizado numa única zona de disponibilidade, mas pode utilizar aglomerados redundantes localizados em diferentes zonas ou regiões diferentes para fornecer acesso rapidamente em caso de paragem regional.

Também pode posicionar contentores de armazenamento em várias regiões se estiver preocupado em perder o acesso aos dados. No entanto, tenha em mente que as transações entre regiões têm uma latência mais elevada e um custo mais elevado do que as transações que se mantêm dentro de uma região.

## <a name="protect-data-in-the-cluster-cache"></a>Proteja os dados na cache do cluster

Os dados em cache são sempre escritos aos ficheiros principais antes de uma paragem regular, mas numa paragem descontrolada, os dados alterados na cache podem ser perdidos.

Se utilizar o cluster para otimizar apenas as leituras de ficheiros, não há alterações a perder. Se também utilizar o cluster para alterar as alterações de ficheiros (escreve), considere se deve ou não ajustar as políticas de [cache](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) dos ficheiros centrais<!-- link to legacy doc --> para personalizar a frequência com que os dados são escritos para armazenamento a longo prazo.

Em geral, o seu plano de recuperação deve concentrar-se em apoiar os sistemas de armazenamento de back-end, que detêm mais dados e normalmente são mais importantes para restabelecer o seu fluxo de trabalho após uma falha.

## <a name="protect-data-in-nas-core-filers"></a>Proteja os dados nos ficheirores centrais da NAS

Utilize métodos aceites para proteger os dados armazenados num ficheiro central de hardware NAS no local, incluindo instantâneos e cópias de segurança completas, conforme recomendado pelo fornecedor nas. A recuperação de desastres para estes ficheiros principais está fora do âmbito deste artigo.

## <a name="protect-data-in-azure-blob-storage"></a>Proteja os dados no armazenamento da Blob Azure

Avere vFXT para Azure utiliza armazenamento localmente redundante (LRS) para filetes de núcleo Azure Blob. Isto significa que os dados dos seus contentores Blob são automaticamente copiados para proteção contra falhas de hardware transitórias dentro de um centro de dados.

Esta secção dá dicas sobre como proteger ainda mais os seus dados no armazenamento blob de falhas raras em toda a região ou supressões acidentais.

As melhores práticas para proteger dados no armazenamento da Blob Azure incluem:

* Copie frequentemente os seus dados críticos para outra conta de armazenamento noutra região (tantas vezes como determinado pelo seu plano de recuperação de desastres).
* Controlar o acesso a dados de todos os sistemas-alvo para evitar a eliminação acidental ou corrupção. Considere usar [fechaduras](../azure-resource-manager/management/lock-resources.md) de recursos no armazenamento de dados.
* Ative a função de [instantâneo](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) avere vFXT para nuvem Azure para os seus ficheiros de núcleo Blob.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Copiar Dados de ficheiros core Avere vFXT para uma conta de backup

Siga estes passos para estabelecer uma cópia de segurança de dados noutra conta.

1. Se necessário, gere uma nova chave de encriptação e armazene-a com segurança fora dos sistemas afetados.

   Se os seus dados forem encriptados pelo Avere vFXT para o cluster Azure, deverá gerar uma nova chave de encriptação antes de copiar os dados para outra conta de armazenamento. Guarde essa chave e senha de forma segura numa instalação segura e que não será afetada por uma falha regional.

   Deve fornecer esta chave ao adicionar o recipiente a um cluster - mesmo que esteja a adicioná-lo ao seu cluster original.

   Ler [definições de encriptação cloud](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> para informações detalhadas.

   Se o seu recipiente utilizar apenas a encriptação incorporada do Azure, pode saltar este passo.

1. Retire o filete principal do sistema. Isto força o cluster a escrever todos os dados alterados para o armazenamento de back-end.

   Embora tenha de voltar a adicionar o filete principal após a cópia de segurança, removê-lo é a melhor forma de garantir que todos os dados estão completamente escritos na parte de trás. (A opção "suspender" pode, por vezes, deixar dados alterados na cache.) <!-- xxx true? or just metadata? -->

   Tome nota do nome e informações de junção do ficheiro principal (listado na página **Namespace** no painel de controlo) para que possa replicá-lo quando voltar a adicionar o recipiente após a cópia de segurança.

   Utilize o painel de controlo do cluster para remover o filer do núcleo. [Abra o painel de controlo do cluster](avere-vfxt-cluster-gui.md) e escolha o ficheiro >  **Core**Gerir**os ficheiros core**. Encontre o sistema de armazenamento que pretende fazer recuar e utilize o botão **Remover** para o eliminar do cluster.

1. Crie um novo recipiente de armazenamento Blob vazio em outra conta de armazenamento em outra região.

1. Utilize qualquer ferramenta de cópia conveniente para copiar os dados do filete principal para o novo recipiente. A cópia deve replicar os dados sem alterações e sem perturbar o formato proprietário do sistema de ficheiros em nuvem utilizado pelo Avere vFXT para o Azure. As ferramentas baseadas em Azure incluem [AzCopy,](../storage/common/storage-use-azcopy-v10.md) [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)e [Azure Data Factory.](../data-factory/connector-azure-data-lake-store.md)

1. Depois de copiar os dados para o recipiente de reserva, adicione o recipiente original de volta ao cluster conforme descrito no [armazenamento configure](avere-vfxt-add-storage.md).

   * Utilize o mesmo nome de ficheiro principal e informações de junção para que os fluxos de trabalho do cliente não precisem de ser alterados.
   * Detete o valor do conteúdo do **Balde** para a opção de dados existente.
   * Se o recipiente foi encriptado pelo cluster, deve introduzir a chave de encriptação atual para o seu conteúdo. (Esta é a chave que atualizou no primeiro passo.)

Para cópias de segurança após a primeira, não precisa de criar um novo recipiente de armazenamento. No entanto, considere gerar uma nova chave de encriptação sempre que fizer uma cópia de segurança para se certificar de que tem a chave atual armazenada num local de que se lembra.

### <a name="access-a-backup-data-source-during-an-outage"></a>Aceda a uma fonte de dados de backup durante uma paragem

Para aceder ao recipiente de reserva a partir de um avere vFXT para cluster Azure, siga este processo:

1. Se necessário, crie um novo Avere vFXT para o cluster Azure numa região não afetada.

   > [!TIP]
   > Quando cria um Avere vFXT para cluster Azure, pode guardar uma cópia do seu modelo de criação e parâmetros. Se guardar esta informação ao criar o seu cluster primário, pode usá-la para criar um cluster de substituição com as mesmas propriedades. Na página [resumo,](avere-vfxt-deploy.md#validation-and-purchase) clique no modelo de descarregamento e no link **de parâmetros.** Guarde a informação para um ficheiro antes de criar o cluster.

1. Adicione um novo filer de núcleo de nuvem que aponta para o recipiente Blob duplicado.

   Certifique-se de especificar que o recipiente-alvo já contém dados na definição de conteúdo do **Balde** do assistente de criação de ficheiros core. (O sistema deve alertá-lo se acidentalmente deixar este conjunto **vazio**.)  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. Se necessário, atualize os clientes para que montem o novo cluster ou novo filete principal em vez do original. (Se adicionar o filete de núcleo de substituição com o mesmo nome e caminho de junção que o recipiente original, não precisará de atualizar os processos do cliente a menos que necessite de montar o novo cluster num novo endereço IP.)

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a personalização das definições para Avere vFXT para Azure, leia a [sintonização do Cluster](avere-vfxt-tuning.md).
* Saiba mais sobre recuperação de desastres e construção de aplicações resilientes em Azure:

  * [Orientações técnicas sobre resiliência do Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
  * [Recuperar de uma perturbação do serviço em toda a região](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Recuperação após desastre e elevada disponibilidade para aplicações do Azure](<https://docs.microsoft.com/azure/resiliency/resiliency-disaster-recovery-high-availability-azure-applications>)
  <!-- can't find these in the source tree to use relative links -->
