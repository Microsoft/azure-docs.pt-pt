---
title: Orientação de recuperação de desastres para Avere vFXT para Azure
description: Como proteger os dados em Avere vFXT para Azure contra eliminação acidental ou interrupções
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 5cc4678b082aa5a4a3f90518ff8fac448f414f1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92342251"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Orientação de recuperação de desastres para Avere vFXT para Azure

Este artigo descreve estratégias para proteger o seu fluxo de trabalho Avere vFXT para o Azure e dá orientações para o backup de dados para que possa recuperar de acidentes ou interrupções.

Avere vFXT para a Azure armazena temporariamente dados na sua cache. Os dados são armazenados a longo prazo em sistemas de armazenamento back-end - sistemas de hardware no local, recipientes de armazenamento Azure Blob, ou ambos.

Para evitar interrupções e possíveis perdas de dados, considere estas quatro áreas:

* Proteção contra tempo de inatividade se um sistema Avere vFXT para Azure ficar indisponível
* Proteger dados na cache do cluster
* Proteger dados no armazenamento de hardware nas nas traseiras
* Proteger dados no armazenamento em nuvem Azure Blob

Todos os clientes Avere vFXT para a Azure devem criar o seu próprio plano abrangente de recuperação de desastres que inclua planos para estes itens. Também pode construir resiliência em aplicações que utiliza com o cluster vFXT. Leia os links nos [próximos passos](#next-steps) para obter ajuda.

## <a name="protect-against-downtime"></a>Proteja contra o tempo de inatividade

A redundância é incorporada no avere vFXT para o produto Azure:

* O cluster está altamente disponível, e os nós de cluster individuais podem falhar com a interrupção mínima.
* Os dados alterados na cache são escritos regularmente para ficheiros de base de back-end (hardware NAS ou Azure Blob) para armazenamento a longo prazo.

Cada agrupamento Avere vFXT para Azure deve estar localizado numa única zona de disponibilidade, mas você pode usar clusters redundantes localizados em diferentes zonas ou diferentes regiões para fornecer acesso rápido em caso de paragem regional.

Também pode posicionar recipientes de armazenamento em várias regiões se estiver preocupado em perder o acesso aos dados. No entanto, tenha em mente que as transações entre regiões têm uma latência mais elevada e um custo mais elevado do que as transações que permanecem dentro de uma região.

## <a name="protect-data-in-the-cluster-cache"></a>Proteger os dados na cache do cluster

Os dados em cache são sempre escritos para os ficheiros principais antes de uma paragem regular, mas numa paragem descontrolada, podem perder-se os dados alterados na cache.

Se utilizar o cluster para otimizar apenas as leituras de ficheiros, não há alterações a perder. Se também utilizar o cluster para cache alterações de ficheiros (escreve), considere se deve ou não ajustar as políticas de [cache](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) dos ficheiros principais<!-- link to legacy doc --> para personalizar a frequência com que os dados são escritos para armazenamento a longo prazo.

Em geral, o seu plano de recuperação deve focar-se em apoiar os sistemas de armazenamento back-end, que detêm mais dados e normalmente são mais importantes para restabelecer o seu fluxo de trabalho após uma falha.

## <a name="protect-data-in-nas-core-filers"></a>Proteja os dados nos ficheiros principais da NAS

Utilize métodos aceites para proteger os dados armazenados num ficheiro principal de hardware NAS no local, incluindo instantâneos e cópias de segurança completas, conforme recomendado pelo fornecedor NAS. A recuperação de desastres para estes ficheiros principais está fora do âmbito deste artigo.

## <a name="protect-data-in-azure-blob-storage"></a>Proteger dados no armazenamento da Azure Blob

Avere vFXT para Azure utiliza armazenamento localmente redundante (LRS) para filetes de núcleo Azure Blob. Isto significa que os dados nos seus recipientes Blob são automaticamente copiados para proteção contra falhas de hardware transitórios dentro de um centro de dados.

Esta secção dá dicas sobre como proteger ainda mais os seus dados no armazenamento blob de interrupções raras em toda a região ou eliminações acidentais.

As melhores práticas para proteger os dados no armazenamento da Azure Blob incluem:

* Copie os seus dados críticos para outra conta de armazenamento noutra região frequentemente (sempre que determinado pelo seu plano de recuperação de desastres).
* Controlar o acesso aos dados em todos os sistemas-alvo para evitar a eliminação acidental ou a corrupção. Considere usar [bloqueios de recursos](../azure-resource-manager/management/lock-resources.md) no armazenamento de dados.
* Ativar o avere vFXT para a funcionalidade [de instantâneo em nuvem](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) Azure para os seus ficheiros de núcleo Blob.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Copiar dados do ficheiro principal avere vFXT para uma conta de backup

Siga estes passos para estabelecer uma cópia de segurança de dados noutra conta.

1. Se necessário, gere uma nova chave de encriptação e armazene-a com segurança fora dos sistemas afetados.

   Se os seus dados forem encriptados pelo avere vFXT para o cluster Azure, deverá gerar uma nova chave de encriptação antes de copiar os dados para outra conta de armazenamento. Guarde essa chave e palavra-passe de forma segura numa instalação segura e que não seja afetada por uma falha regional.

   Deve fornecer esta chave ao adicionar o recipiente a um aglomerado - mesmo que esteja a adicioná-lo novamente ao seu cluster original.

   Leia [as definições de encriptação da Nuvem](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)<!-- link to legacy doc site --> para informações detalhadas.

   Se o seu recipiente utilizar apenas a encriptação incorporada do Azure, pode saltar este passo.

1. Retire o ficheiro principal do sistema. Isto força o cluster a escrever todos os dados alterados para o armazenamento de back-end.

   Embora tenha de voltar a adicionar o ficheiro principal após a cópia de segurança, removê-lo é a melhor forma de garantir que todos os dados estão completamente escritos na parte de trás. (A opção "suspender" pode, por vezes, deixar os dados alterados na cache.) <!-- xxx true? or just metadata? -->

   Tome nota do nome e da informação de junção do ficheiro principal (listado na página **Namespace** no painel de controlo) para que possa replicá-lo quando voltar a adicionar o recipiente após a cópia de segurança.

   Utilize o painel de controlo do cluster para remover o ficheiro do núcleo. [Abra o painel de controlo do cluster](avere-vfxt-cluster-gui.md) e escolha o ficheiro **Core** Gerir  >  **os ficheiros centrais**. Encontre o sistema de armazenamento que pretende fazer recuar e utilize o botão **Remover** para eliminá-lo do cluster.

1. Crie um novo recipiente de armazenamento Blob vazio em outra conta de armazenamento em outra região.

1. Utilize qualquer ferramenta de cópia conveniente para copiar os dados do ficheiro principal para o novo recipiente. A cópia deve replicar os dados sem alterações, e sem perturbar o formato de sistema de ficheiros em nuvem utilizado pela Avere vFXT para o Azure. As ferramentas baseadas em Azure incluem [AzCopy,](../storage/common/storage-use-azcopy-v10.md) [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)e [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

1. Depois de copiar os dados para o recipiente de reserva, adicione o recipiente original de volta ao cluster, conforme descrito no [armazenamento Configure](avere-vfxt-add-storage.md).

   * Utilize o mesmo nome de ficheiro principal e informações de junção para que os fluxos de trabalho do cliente não precisem de ser alterados.
   * Desaver o valor **do conteúdo do Balde** para a opção de dados existente.
   * Se o recipiente foi encriptado pelo cluster, deve introduzir a chave de encriptação atual para o seu conteúdo. (Esta é a chave que atualizou no primeiro passo.)

Para cópias de segurança após a primeira, não precisa de criar um novo recipiente de armazenamento. No entanto, considere gerar uma nova chave de encriptação sempre que fizer uma cópia de segurança para se certificar de que tem a chave atual armazenada num local de que se lembra.

### <a name="access-a-backup-data-source-during-an-outage"></a>Aceda a uma fonte de dados de backup durante uma paragem

Para aceder ao recipiente de backup a partir de um agrupamento Avere vFXT para Azure, siga este processo:

1. Se necessário, crie um novo Avere vFXT para o cluster Azure numa região não afetada.

   > [!TIP]
   > Quando criar um agrupamento Avere vFXT para Azure, pode guardar uma cópia do seu modelo e parâmetros de criação. Se guardar esta informação ao criar o seu cluster primário, pode usá-la para criar um cluster de substituição com as mesmas propriedades. Na página [do resumo,](avere-vfxt-deploy.md#validation-and-purchase) clique no modelo de descarregamento e na ligação **de parâmetros.** Guarde a informação para um ficheiro antes de criar o cluster.

1. Adicione um novo filete de núcleo de nuvem que aponta para o recipiente Blob duplicado.

   Certifique-se de especificar que o recipiente-alvo já contém **dados** na definição do conteúdo do Balde do assistente de criação do ficheiro principal. (O sistema deve alertá-lo se deixar acidentalmente este conjunto para **Empty**.)  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. Se necessário, atualize os clientes de modo a que montem o novo cluster ou o novo ficheiro principal em vez do original. (Se adicionar o ficheiro principal de substituição com o mesmo nome e caminho de junção que o recipiente original, não necessitará de atualizar os processos do cliente a menos que precise de montar o novo cluster num novo endereço IP.)

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a personalização das definições para Avere vFXT para Azure, leia [afinação do Cluster](avere-vfxt-tuning.md).
* Saiba mais sobre a recuperação de desastres e a construção de aplicações resilientes em Azure:

  * [Orientações técnicas sobre resiliência do Azure](/azure/architecture/framework/resiliency/overview)
  * [Recupere de uma interrupção de serviço ao nível da região](/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Recuperação após desastre e elevada disponibilidade para aplicações do Azure](/azure/architecture/framework/resiliency/backup-and-recovery)
  <!-- can't find these in the source tree to use relative links -->