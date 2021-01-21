---
title: Migração da série StorSimple 8000 para Azure File Sync
description: Saiba como migrar um aparelho StorSimple 8100 ou 8600 para Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 76a244810042adf3cec64b15fe847c5b684527c2
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631189"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Migração StorSimple 8100 e 8600 para Azure File Sync

A série StorSimple 8000 é representada pelos aparelhos 8100 ou 8600 físicos e nos seus componentes de serviço de nuvem. É possível migrar os dados de qualquer um destes aparelhos para um ambiente Azure File Sync. Azure File Sync é o serviço Azure de longo prazo padrão e estratégico para o qual os aparelhos StorSimple podem ser migrados.

A série StorSimple 8000 chegará ao seu [fim de vida](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) em dezembro de 2022. É importante começar a planear a sua migração o mais rápido possível. Este artigo fornece os conhecimentos de fundo necessários e passos de migração para uma migração bem sucedida para Azure File Sync.

## <a name="phase-1-prepare-for-migration"></a>Fase 1: Preparar para a migração

Esta secção contém os passos que deve tomar no início da sua migração de volumes StorSimple para ações de ficheiros Azure.

### <a name="inventory"></a>Inventário

Quando começar a planear a sua migração, identifique primeiro todos os aparelhos e volumes StorSimple necessários para migrar. Depois de ter feito isso, pode decidir qual é o melhor caminho de migração para si.

* Os aparelhos físicos StorSimple (série 8000) utilizam este guia de migração.
* Aparelhos virtuais, [série StorSimple 1200, utilize um guia de migração diferente](storage-files-migration-storsimple-1200.md).

### <a name="migration-cost-summary"></a>Resumo dos custos de migração

As ações de ficheiros Azure de volumes StorSimple através de trabalhos de serviço de transformação de dados num recurso StorSimple Data Manager são gratuitas. Outros custos podem ser incorridos durante e após uma migração:

* **Saída de rede:** Os seus ficheiros StorSimple vivem numa conta de armazenamento dentro de uma região específica de Azure. Se você providenciar as ações de arquivo Azure que você migra para uma conta de armazenamento que está localizada na mesma região de Azure, não haverá custo de saída. Pode mover os seus ficheiros para uma conta de armazenamento numa região diferente como parte desta migração. Nesse caso, os custos de saída aplicar-se-ão a si.
* **Transações de ações de ficheiros Azure:** Quando os ficheiros são copiados numa partilha de ficheiros Azure (como parte de uma migração ou fora de um), os custos de transação aplicam-se à medida que os ficheiros e metadados estão a ser escritos. Como uma boa prática, inicie a sua partilha de ficheiros Azure no nível otimizado de transação durante a migração. Mude para o nível desejado após o fim da migração. As fases seguintes irão chamar isto no ponto apropriado.
* **Alterar um nível de partilha de ficheiros Azure:** Alterar o nível de uma ação de ações Azure operações de custos. Na maioria dos casos, será mais rentável seguir os conselhos do ponto anterior.
* **Custo de armazenamento:** Quando esta migração começa a copiar ficheiros numa partilha de ficheiros Azure, o armazenamento do Azure Files é consumido e faturado.
* **StorSimple:** Até ter a oportunidade de desprovisionar os dispositivos StorSimple e as contas de armazenamento, o custo StorSimple para armazenamento, cópias de segurança e aparelhos continuará a ocorrer.

### <a name="direct-share-access-vs-azure-file-sync"></a>Acesso direto a partilha vs. Azure File Sync

As ações de ficheiros Azure abrem um novo mundo de oportunidades para estruturar a implementação dos seus serviços de ficheiros. Uma partilha de ficheiros Azure é apenas uma partilha de SMB na nuvem que pode configurar para que os utilizadores tenham acesso diretamente ao protocolo SMB com a autenticação conhecida de Kerberos e permissões NTFS existentes (ACLs de ficheiros e pastas) que funcionam de forma nativa. Saiba mais sobre [o acesso à identidade das ações de ficheiros Azure](storage-files-active-directory-overview.md).

Uma alternativa ao acesso direto é [o Azure File Sync](./storage-sync-files-planning.md). O Azure File Sync é um analógico direto para a capacidade da StorSimple de cache ficheiros frequentemente utilizados no local.

Azure File Sync é um serviço de nuvem da Microsoft, baseado em dois componentes principais:

* Sincronização de ficheiros e tiering de nuvem.
* Ações de arquivo como armazenamento nativo em Azure que podem ser acedidas sobre vários protocolos como SMB e file REST.

As ações de ficheiros Azure retêm importantes aspetos de fidelidade de ficheiros em ficheiros armazenados, como atributos, permissões e timetamps. Com as partilhas de ficheiros Azure, já não existe necessidade de uma aplicação ou serviço para interpretar os ficheiros e pastas armazenados na nuvem. Pode aceder-lhes de forma nativa sobre protocolos familiares e clientes como o Windows File Explorer. As ações de ficheiros Azure permitem armazenar dados de servidores de ficheiros de uso geral e dados de aplicação na nuvem. A cópia de segurança de uma partilha de ficheiros Azure é uma funcionalidade incorporada e pode ser melhorada pelo Azure Backup.

Este artigo centra-se nos passos de migração. Se quiser saber mais sobre o Azure File Sync antes de migrar, consulte os seguintes artigos:

* [Visão geral do Azure File Sync](./storage-sync-files-planning.md "Descrição geral")
* [Guia de implementação de Sincronização de Ficheiros Azure](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>Chave de encriptação de dados de serviço StorSimple

Quando montou o seu aparelho StorSimple pela primeira vez, gerou uma Chave de Encriptação de Dados de Serviço e instruiu-o a armazenar a chave de forma segura. Esta chave é utilizada para encriptar todos os dados na conta de armazenamento Azure associada, onde o aparelho StorSimple armazena os seus ficheiros.

A Chave de Encriptação de Dados de Serviço é necessária para uma migração bem sucedida. Agora é uma boa hora para recuperar esta chave dos seus registos, para cada um dos aparelhos no seu inventário.

Se não encontrar as chaves nos seus registos, pode recuperar a chave do aparelho. Cada aparelho tem uma chave de encriptação única. Para recuperar a chave:

* Arquive um pedido de suporte com o Microsoft Azure através do portal Azure. O conteúdo do pedido deve ter os números de série do dispositivo StorSimple e o pedido de recuperação da "Chave de Encriptação de Dados de Serviço".
* Um engenheiro de suporte StorSimple entrará em contacto consigo com um pedido de reunião de partilha de ecrã.
* Certifique-se de que, antes do início da reunião, ligue-se ao seu aparelho StorSimple [através de uma consola em série](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) ou através de uma [sessão remota do PowerShell](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple).

> [!CAUTION]
> Quando estiver a decidir como ligar-se ao seu aparelho StorSimple, considere o seguinte:
>
> * A ligação através de uma sessão HTTPS é a opção mais segura e recomendada.
> * A ligação direta à consola em série do dispositivo é segura, mas a ligação à consola em série sobre os interruptores de rede não é.
> * As ligações de sessão HTTP são uma opção, mas não são *encriptadas.* Não são recomendados a menos que sejam usados numa rede fechada e de confiança.

### <a name="storsimple-volume-backups"></a>Backups de volume StorSimple

A StorSimple oferece cópias de segurança diferenciais ao nível do volume. As ações de ficheiros Azure também têm esta capacidade, chamadas snapshots de partilha.

Decida se como parte da sua migração, também tem a obrigação de mover quaisquer backups.

> [!CAUTION]
> Pare aqui se tiver de migrar cópias de segurança dos volumes StorSimple.
>
> Atualmente, só pode migrar a sua cópia de segurança de volume mais recente. O apoio à migração de apoio chegará no final de 2020. Se começar agora, não pode "aparafusá-lo" mais tarde. Na próxima versão, as cópias de segurança devem ser "reproduzidas" para as ações de ficheiros Azure, das mais antigas às mais recentes, com as fotos de partilha de ficheiros Azure tiradas entre elas.

Se quiser migrar apenas os dados ao vivo e não tiver necessidades de cópias de segurança, pode continuar a seguir este guia. Se tiver um requisito de retenção de backup de curto prazo de, digamos, um mês ou dois, pode decidir continuar a sua migração agora e desprovisionar os seus recursos StorSimple após esse período. Esta abordagem permite-lhe criar o histórico de backup no lado da partilha de ficheiros Azure que precisar. Para o tempo em que mantém ambos os sistemas em funcionamento, aplica-se um custo adicional, o que faz com que esta abordagem não considere se precisa de mais do que retenção de backup a curto prazo.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Mapear os seus volumes StorSimple existentes para ações de ficheiros Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Número de contas de armazenamento

A sua migração provavelmente beneficiará de uma implementação de várias contas de armazenamento que cada uma detém um número menor de ações de ficheiros Azure.

Se as suas ações de ficheiros forem altamente ativas (utilizadas por muitos utilizadores ou aplicações), duas ações de ficheiros Azure poderão atingir o limite de desempenho da sua conta de armazenamento. Por isso, a melhor prática é migrar para múltiplas contas de armazenamento, cada uma com as suas próprias ações individuais de ficheiros e, normalmente, não mais do que duas ou três ações por conta de armazenamento.

Uma boa prática é implementar contas de armazenamento com uma partilha de ficheiros cada. Você pode juntar várias ações de arquivo Azure na mesma conta de armazenamento, se tiver ações de arquivo nelas.

Estas considerações aplicam-se mais ao [acesso direto](#direct-share-access-vs-azure-file-sync) à nuvem (através de um VM ou serviço Azure) do que ao Azure File Sync. Se planeia utilizar o Azure File Sync apenas nestas ações, agrupar várias numa única conta de armazenamento Azure está bem. Considere também que pode querer levantar e transferir uma aplicação para a nuvem que, em seguida, acederia diretamente a uma partilha de ficheiros. Ou pode começar a usar um serviço em Azure que também beneficiaria de ter iops mais elevados e números de produção disponíveis.

Se fez uma lista das suas ações, mapeia cada ação para a conta de armazenamento onde ela irá residir.

> [!IMPORTANT]
> Decida uma região de Azure e certifique-se de que cada conta de armazenamento e recurso Azure File Sync correspondem à região selecionada.

### <a name="phase-1-summary"></a>Resumo da fase 1

No final da Fase 1:

* Tem uma boa visão geral dos seus dispositivos e volumes StorSimple.
* O serviço de transformação de dados está pronto para aceder aos seus volumes StorSimple na nuvem porque recuperou a chave de encriptação de dados de serviço para cada dispositivo StorSimple.
* Tem um plano para o qual os volumes precisam de ser migrados e também como mapear os seus volumes para o número adequado de ações de ficheiros Azure e contas de armazenamento.

> [!CAUTION]
> Se tiver de migrar cópias de segurança dos volumes StorSimple, **PARE AQUI**.
>
> Esta abordagem de migração baseia-se em novas capacidades de serviço de transformação de dados que atualmente não podem migrar backups. O apoio à migração de apoio chegará no final de 2020. Atualmente só pode migrar os seus dados ao vivo. Se começar agora, não pode "aparafusá-lo" mais tarde. As cópias de segurança devem ser "reproduzidas" para as ações de ficheiros Azure, das mais antigas às mais recentes para os dados ao vivo, com os instantâneos de partilha de ficheiros Azure pelo meio.

Se quiser migrar apenas os dados ao vivo e não tiver necessidades de cópias de segurança, pode continuar a seguir este guia.

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>Fase 2: Implantar recursos de armazenamento e migração do Azure

Esta secção discute considerações em torno da implantação dos diferentes tipos de recursos que são necessários em Azure. Alguns irão deter os seus dados após a migração, e alguns são necessários apenas para a migração. Não comeces a mobilizar recursos até teres finalizado o teu plano de implantação. É difícil, às vezes impossível, mudar certos aspetos dos seus recursos azuis depois de terem sido implantados.

### <a name="deploy-storage-accounts"></a>Implementar contas de armazenamento

Provavelmente terá de implementar várias contas de armazenamento da Azure. Cada um deles terá um número menor de ações de ficheiros Azure, de acordo com o seu plano de implantação, concluído na secção anterior deste artigo. Vá ao portal Azure para [implementar as suas contas de armazenamento planeadas.](../common/storage-account-create.md#create-a-storage-account) Considere aderir às seguintes definições básicas para qualquer nova conta de armazenamento.

#### <a name="subscription"></a>Subscrição

Pode utilizar a mesma subscrição que utilizou para a sua implementação StorSimple ou outra. A única limitação é que a sua subscrição deve estar no mesmo inquilino do Azure Ative Directory que a subscrição StorSimple. Considere mover a assinatura StorSimple para o inquilino correto antes de iniciar uma migração. Só pode mover toda a subscrição. Os recursos individuais StorSimple não podem ser transferidos para um inquilino ou subscrição diferente.

#### <a name="resource-group"></a>Grupo de recursos

Os grupos de recursos estão a ajudar na organização de recursos e permissões de gestão de administração. Saiba mais sobre [grupos de recursos em Azure.](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)

#### <a name="storage-account-name"></a>Nome da conta de armazenamento

O nome da sua conta de armazenamento passará a fazer parte de um URL e tem determinadas limitações de caracteres. Na sua convenção de nomeação, considere que os nomes das contas de armazenamento têm de ser únicos no mundo, permitem apenas letras e números minúsculos, requerem entre 3 a 24 caracteres, e não permitem caracteres especiais como hífens ou sublinhados. Para obter mais informações, consulte [as regras de nomeação de recursos de armazenamento Azure](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage).

#### <a name="location"></a>Localização

A localização ou região de Azure de uma conta de armazenamento é muito importante. Se utilizar o Azure File Sync, todas as suas contas de armazenamento devem estar na mesma região que o seu recurso De Serviço de Sincronização de Armazenamento. A região Azure que escolher deve ser próxima ou central para os seus servidores e utilizadores locais. Depois de o seu recurso ter sido implantado, não pode mudar a sua região.

Pode escolher uma região diferente de onde reside atualmente os seus dados StorSimple (conta de armazenamento).

> [!IMPORTANT]
> Se escolher uma região diferente da sua conta de armazenamento StorSimple atual, [os encargos de saída serão aplicados](https://azure.microsoft.com/pricing/details/bandwidth) durante a migração. Os dados deixarão a região StorSimple e entrarão na sua nova região de conta de armazenamento. Não se aplicam taxas de largura de banda se permanecer na mesma região de Azure.

#### <a name="performance"></a>Desempenho

Tem a opção de escolher armazenamento premium (SSD) para ações de ficheiros Azure ou armazenamento padrão. O armazenamento padrão inclui [vários níveis para uma partilha de ficheiros.](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share) O armazenamento padrão é a opção certa para a maioria dos clientes que migram da StorSimple.

Ainda não tem certeza?

* Escolha o armazenamento premium se precisar do [desempenho de uma partilha de ficheiros Azure premium.](understanding-billing.md#provisioned-model)
* Escolha o armazenamento padrão para as cargas de trabalho do servidor de ficheiros de uso geral, que inclui dados quentes e dados de arquivo. Escolha também o armazenamento padrão se a única carga de trabalho na partilha na nuvem será O Azure File Sync.

#### <a name="account-kind"></a>Tipo de conta

* Para armazenamento normal, escolha *StorageV2 (finalidade geral v2)*.
* Para ações de ficheiros premium, escolha *o FileStorage*.

#### <a name="replication"></a>Replicação

Existem várias definições de replicação disponíveis. Saiba mais sobre os diferentes tipos de replicação.

Escolha apenas entre uma das duas opções seguintes:

* *Armazenamento localmente redundante (LRS)*.
* *Armazenamento redundante zona (ZRS),* que não está disponível em todas as regiões de Azure.

> [!NOTE]
> Apenas os tipos de redundância LRS e ZRS são compatíveis com as grandes ações de ficheiros Azure de 100-TiB.capacitária.

O armazenamento geo redundante (GRS) em todas as variações não é suportado atualmente. Pode mudar o seu tipo de redundância mais tarde e mudar para GRS quando o suporte chegar ao Azure.

#### <a name="enable-100-tib-capacity-file-shares"></a>Ativar ações de ficheiros de 100-TiB-capacidade

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Uma imagem mostrando o separador Advanced no portal Azure para a criação de uma conta de armazenamento.":::
    :::column-end:::
    :::column:::
        Sob a secção **Avançada** do novo assistente de conta de armazenamento no portal Azure, pode ativar o suporte **de grandes ações** de ficheiros nesta conta de armazenamento. Se esta opção não estiver disponível para si, é provável que tenha selecionado o tipo de redundância errado. Certifique-se de que seleciona apenas LRS ou ZRS para que esta opção fique disponível.
    :::column-end:::
:::row-end:::

Optar pelas grandes ações de ficheiros de 100-TiB tem vários benefícios:

* O seu desempenho é muito aumentado em comparação com as ações de ficheiros de 5-TiB mais pequenas (por exemplo, 10 vezes o IOPS).
* Sua migração terminará significativamente mais rápido.
* Certifique-se de que uma partilha de ficheiros terá capacidade suficiente para reter todos os dados que irá migrar para ele.
* O crescimento futuro está coberto.

### <a name="azure-file-shares"></a>Ações de ficheiros Azure

Após a criação das suas contas de armazenamento, vá à secção de partilha de **ficheiros** da conta de armazenamento e implemente o número adequado de ações de ficheiros Azure de acordo com o seu plano de migração a partir da Fase 1. Considere aderir às seguintes definições básicas para as suas novas ações de ficheiros no Azure.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Uma imagem do portal Azure mostrando a nova UI partilhada por ficheiros.":::
    :::column-end:::
    :::column:::
        </br>**Nome**</br>Letras minúsculas, números e hífenes são suportados.</br></br>**Quota**</br>A quota aqui é comparável a uma quota rígida SMB numa instância do Windows Server. A melhor prática é não definir uma quota aqui porque a sua migração e outros serviços falharão quando a quota for atingida.</br></br>**Escalões**</br>Selecione **Transação otimizada** para a sua nova partilha de ficheiros. Durante a migração, muitas transações ocorrerão. É mais rentável mudar o seu nível mais tarde para o nível mais adequado à sua carga de trabalho.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager

O recurso Azure que irá manter os seus empregos de migração chama-se **StorSimple Data Manager.** Selecione **Novo recurso** e procure-o. Em seguida, selecione **Criar**.

Este recurso temporário é utilizado para orquestração. Desprovisioná-lo depois da sua migração terminar. Deve ser implantado na mesma subscrição, grupo de recursos e região que a sua conta de armazenamento StorSimple.

### <a name="azure-file-sync"></a>Azure File Sync

Com o Azure File Sync, pode adicionar no local o caching dos ficheiros mais acedidos. Semelhante às habilidades de cache do StorSimple, a funcionalidade de nivelamento de nuvem Azure File Sync oferece latência de acesso local em combinação com um controlo melhorado sobre a capacidade de cache disponível na instância do Windows Server e sincronização de vários locais. Se ter uma cache no local é o seu objetivo, então na sua rede local, prepare um VM do Windows Server (servidores físicos e clusters de failover também são suportados) com capacidade de armazenamento direta suficiente.

> [!IMPORTANT]
> Não instale o Azure File Sync ainda. É melhor configurar o Azure File Sync depois de concluída a migração da sua parte. A implementação do Azure File Sync não deve começar antes da fase 4 de uma migração.

### <a name="phase-2-summary"></a>Resumo da fase 2

No final da Fase 2, terá implantado as suas contas de armazenamento e todas as ações de ficheiros Azure através delas. Também terá um recurso StorSimple Data Manager. Usará este último na fase 3 quando configurar os seus trabalhos de migração.

## <a name="phase-3-create-and-run-a-migration-job"></a>Fase 3: Criar e executar um trabalho de migração

Esta secção descreve como configurar um trabalho de migração e mapear cuidadosamente os diretórios num volume StorSimple que deve ser copiado para o ficheiro target Azure que seleciona. Para começar, vá ao seu Gestor de Dados StorSimple, encontre **definições de Job** no menu e selecione **+ Definição de trabalho**. O tipo de armazenamento alvo é a partilha de **ficheiros Azure** predefinido .

![StorSimple 8000 série de empregos de migração.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "Uma imagem do portal Job definitions Azure com uma nova caixa de diálogo de definições de emprego aberta que pede o tipo de trabalho: Copiar para uma partilha de ficheiros ou um recipiente blob.")

> [!IMPORTANT]
> Antes de executar qualquer trabalho de migração, pare quaisquer cópias de segurança programadas automaticamente dos seus volumes StorSimple.

:::row:::
    :::column:::
        ![StorSimple 8000 trabalho de migração série.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Uma imagem do novo formulário de criação de emprego para um trabalho de serviço de transformação de dados.")
    :::column-end:::
    :::column:::
        **Nome da definição de emprego**</br>Este nome deve indicar o conjunto de ficheiros que está a mover. Dar-lhe um nome semelhante ao da sua partilha de ficheiros Azure é uma boa prática. </br></br>**Local onde o trabalho funciona**</br>Ao selecionar uma região, deve selecionar a mesma região que a sua conta de armazenamento StorSimple ou, se isso não estiver disponível, então uma região próxima dela. </br></br><h3>Origem</h3>**Assinatura de origem**</br>Selecione a subscrição na qual armazena o seu recurso StorSimple Device Manager. </br></br>**Recurso StorSimple**</br>Selecione o seu StorSimple Device Manager com o qual o seu aparelho está registado. </br></br>**Chave de encriptação de dados de serviço**</br>Consulte esta [secção anterior neste artigo](#storsimple-service-data-encryption-key) caso não consiga localizar a chave nos seus registos. </br></br>**Dispositivo**</br>Selecione o seu dispositivo StorSimple que detém o volume onde pretende migrar. </br></br>**Volume**</br>Selecione o volume de origem. Mais tarde, decidirá se pretende migrar todo o volume ou subdiretórios para a partilha de ficheiros target Azure. </br></br><h3>Destino</h3>Selecione a subscrição, a conta de armazenamento e a partilha de ficheiros Azure como alvo deste trabalho de migração.
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> A cópia de segurança de volume mais recente será usada para realizar a migração. Certifique-se de que pelo menos uma cópia de segurança de volume está presente ou o trabalho falhará. Também certifique-se de que o mais recente backup que tem é bastante recente para manter o delta ao vivo o mais pequeno possível. Pode valer a pena ativar manualmente e completar outra cópia de segurança de volume *antes* de executar o trabalho que acabou de criar.

### <a name="directory-mapping"></a>Mapeamento de diretório

O mapeamento do diretório é opcional para o seu trabalho de migração. Se deixar a secção vazia, *todos os* ficheiros e pastas na raiz do seu volume StorSimple serão movidos para a raiz da sua partilha de ficheiros Target Azure. Na maioria dos casos, armazenar um conteúdo inteiro de volume numa partilha de ficheiros Azure não é a melhor abordagem. Muitas vezes é melhor dividir o conteúdo de um volume em várias ações de ficheiros em Azure. Se ainda não fez um plano, consulte [o mapa do seu volume StorSimple para ações de ficheiros Azure](#map-your-existing-storsimple-volumes-to-azure-file-shares) primeiro.

Como parte do seu plano de migração, pode ter decidido que as pastas de um volume StorSimple precisam de ser divididas em várias ações de ficheiros Azure. Se for esse o caso, podes conseguir essa divisão por:

1. Definindo vários trabalhos para migrar as pastas num só volume. Cada um terá a mesma fonte de volume StorSimple, mas uma partilha de ficheiros Azure diferente como o alvo.
1. Especificando com precisão quais as pastas do volume StorSimple que devem ser migradas para a parte de ficheiros especificada, utilizando a secção de mapeamento do **Diretório** do formulário de criação de emprego e seguindo a [semântica](#semantic-elements)de mapeamento específica .

> [!IMPORTANT]
> Os caminhos e expressões de mapeamento desta forma não podem ser validados quando o formulário é submetido. Se os mapeamentos forem especificados incorretamente, um trabalho pode falhar completamente ou produzir um resultado indesejável. Nesse caso, normalmente é melhor apagar a partilha de ficheiros Azure, reutilá-la e, em seguida, corrigir as declarações de mapeamento num novo trabalho de migração para a parte. Executar um novo trabalho com declarações de mapeamento fixo pode corrigir pastas omitidas e trazê-las para a parte existente. No entanto, apenas as pastas que foram omitidas devido a erros ortográficos do caminho podem ser abordadas desta forma.

#### <a name="semantic-elements"></a>Elementos semânticos

Um mapeamento é expresso da esquerda para a direita: [\caminho de origem] \> [\caminho alvo].

|Caráter semântico          | Significado  |
|:---------------------------|:---------|
| **\\**                     | Indicador de nível de raiz.       |
| **\>**                     | [Source] e operador [target-mapping].     |
|**\|** ou RETURN (nova linha) | Separador de duas instruções de mapeamento de pastas. </br>Em alternativa, pode omitir este personagem e selecionar **Enter** para obter a próxima expressão de mapeamento na sua própria linha.        |

### <a name="examples"></a>Exemplos
Move o conteúdo da pasta *Os dados do Utilizador* para a raiz da partilha de ficheiros-alvo:
``` console
\User data > \
```
Move todo o conteúdo do volume para um novo caminho na partilha de ficheiros-alvo:
``` console
\ > \Apps\HR tracker
```
Move o conteúdo da pasta de origem para um novo caminho na partilha de ficheiros-alvo:
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
Classifica vários locais de origem numa nova estrutura de diretório:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Regras semânticas

* Especifique sempre os caminhos das pastas em relação ao nível da raiz.
* Inicie cada caminho de pasta com um indicador de nível de raiz \\ " "
* Não inclua cartas de condução.
* Ao especificar vários caminhos, os caminhos de origem ou alvo não podem sobrepor-se:</br>
   Exemplo de sobreposição de caminho de origem inválido:</br>
    *\\pasta\1 > \\ pasta*</br>
    *\\pasta \\ 1 \\ 2 > \\ 2*</br>
   Exemplo de sobreposição de caminho-alvo inválido:</br>
   *\\> pasta \\*</br>
   *\\> pasta2 \\*</br>
* As pastas de origem que não existem serão ignoradas.
* Serão criadas estruturas de pasta que não existam no alvo.
* Tal como o Windows, os nomes das pastas são casos insensíveis, mas preservam casos.

> [!NOTE]
> O conteúdo da pasta *de informação de volume de sistema \e* do *$Recycle.Bin* no seu volume StorSimple não será copiado pelo trabalho de transformação.

### <a name="phase-3-summary"></a>Resumo da fase 3

No final da Fase 3, terá gerido os seus trabalhos de serviço de transformação de dados a partir de volumes StorSimple em ações de ficheiros Azure. Pode agora concentrar-se na criação do Azure File Sync para a partilha (após o fim dos empregos de migração para uma parte) ou em direcionar o acesso de ações para os seus trabalhadores de informação e aplicações para a partilha de ficheiros Azure.

## <a name="phase-4-access-your-azure-file-shares"></a>Fase 4: Aceda às suas ações de ficheiros Azure

Existem duas estratégias principais para aceder às suas partilhas de ficheiros Azure:

* **Azure File Sync**: [Implementar o Azure File Sync](#deploy-azure-file-sync) para uma instância do Windows Server no local. O Azure File Sync tem todas as vantagens de uma cache local, tal como o StorSimple.
* **Acesso direto a partilhas**: [Implementar acesso direto à partilha](#deploy-direct-share-access). Utilize esta estratégia se o seu cenário de acesso para uma determinada partilha de ficheiros Azure não beneficiar do caching local, ou deixar de ter a capacidade de hospedar uma instância do Windows Server no local. Aqui, os seus utilizadores e aplicações continuarão a aceder a ações SMB ao longo do protocolo SMB. Estas ações já não se encontram num servidor no local, mas diretamente na nuvem.

Já devia ter decidido qual a melhor opção para si na [Fase 1](#phase-1-prepare-for-migration) deste guia.

O restante desta secção centra-se nas instruções de implantação.

### <a name="deploy-azure-file-sync"></a>Implementar Azure File Sync

É hora de implementar uma parte do Azure File Sync.

1. Crie o recurso cloud Azure File Sync.
1. Implemente o agente Azure File Sync no seu servidor no local.
1. Registe o servidor com o recurso cloud.

Não crie grupos de sincronização ainda. A configuração da sincronização com uma partilha de ficheiros Azure só deverá ocorrer após os seus trabalhos de migração para uma partilha de ficheiros Azure terem terminado. Se começasse a usar o Azure File Sync antes da sua migração ser concluída, tornaria a sua migração desnecessariamente difícil, uma vez que não poderia dizer facilmente quando era a hora de iniciar um corte.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Implementar o recurso cloud Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Se quiser alterar a região Azure em que os seus dados residem após o fim da migração, implante o Serviço de Sincronização de Armazenamento na mesma região que as contas de armazenamento-alvo para esta migração.

#### <a name="deploy-an-on-premises-windows-server-instance"></a>Implementar uma instância do Windows Server no local

* Criar o Windows Server 2019 (no mínimo 2012R2) como máquina virtual ou servidor físico. Um cluster de falha do Windows Server também é suportado. Não reutilizar o servidor na frente do StorSimple 8100 ou 8600.
* Provisão ou adicionar armazenamento direto. O armazenamento ligado à rede não é suportado.

É melhor dar ao seu novo Windows Server uma quantidade igual ou maior de armazenamento do que o seu aparelho StorSimple 8100 ou 8600 tem disponível localmente para caching. Utilizará a instância do Windows Server da mesma forma que utilizou o aparelho StorSimple. Se tiver a mesma quantidade de armazenamento que o aparelho, a experiência de caching deve ser semelhante, se não a mesma. Pode adicionar ou remover o armazenamento da sua instância do Windows Server à vontade. Esta capacidade permite-lhe escalar o tamanho do volume local e a quantidade de armazenamento local disponível para o caching.

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>Prepare a instância do Servidor do Windows para sincronização de ficheiros

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>Configure Azure File Sync na instância do Servidor do Windows

A sua instância registada no Windows Server deve estar pronta e ligada à internet para este processo.

> [!IMPORTANT]
> A sua migração StorSimple de ficheiros e pastas para a partilha de ficheiros Azure tem de estar completa antes de prosseguir. Certifique-se de que não há mais alterações na partilha de ficheiros.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Certifique-se de ligar o nível da nuvem. O tiering em nuvem é a funcionalidade Azure File Sync que permite ao servidor local ter menos capacidade de armazenamento do que é armazenada na nuvem, mas tem o espaço de nome completo disponível. Os dados locais interessantes também são cached localmente para um desempenho rápido e local de acesso. Outra razão para ligar o nível da nuvem neste passo é que não queremos sincronizar o conteúdo do ficheiro nesta fase. Só o espaço de nomes deve estar em movimento neste momento.

### <a name="deploy-direct-share-access"></a>Implementar acesso direto a partilhas

:::row:::
    :::column:::
        [![Guia passo a passo e demonstração para como expor de forma segura as partilhas de ficheiros Azure diretamente aos trabalhadores e apps de informação - clique para jogar!](./media/storage-files-migration-storsimple-8000/azure-files-direct-access-video-placeholder.png)](https://youtu.be/KG0OX0RgytI)
    :::column-end:::
    :::column:::
        Este vídeo é um guia e demonstração para como expor de forma segura as partilhas de ficheiros Azure diretamente a trabalhadores de informação e apps em cinco passos simples.</br>
        O vídeo refere documentação dedicada a alguns tópicos:

* [Descrição geral de identidade](storage-files-active-directory-overview.md)
* [Como o domínio juntar uma conta de armazenamento](storage-files-identity-auth-active-directory-enable.md)
* [Visão geral de networking para ações de ficheiros Azure](storage-files-networking-overview.md)
* [Como configurar pontos finais públicos e privados](storage-files-networking-endpoints.md)
* [Como configurar uma VPN S2S](storage-files-configure-s2s-vpn.md)
* [Como configurar uma VPN Windows P2S](storage-files-configure-p2s-vpn-windows.md)
* [Como configurar um Linux P2S VPN](storage-files-configure-p2s-vpn-linux.md)
* [Como configurar o encaminhamento do DNS](storage-files-networking-dns.md)
* [Configurar DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Resumo da fase 4

Nesta fase, criou e executou vários trabalhos de serviço de transformação de dados no seu StorSimple Data Manager. Esses empregos migraram os seus ficheiros e pastas para ações de ficheiros Azure. Também implementou o Azure File Sync ou preparou as suas contas de rede e armazenamento para acesso direto a partilha.

## <a name="phase-5-user-cut-over"></a>Fase 5: Corte do utilizador

Esta fase tem tudo a ver com encerrar a sua migração:

* Planeie o seu tempo de descanso.
* Acompanhe quaisquer alterações que os seus utilizadores e aplicações produzidas no lado StorSimple enquanto os trabalhos de transformação de dados na Fase 3 estão em execução.
* Falhe os seus utilizadores na nova instância do Windows Server com o Azure File Sync ou as ações de ficheiros Azure através do acesso direto à partilha.

### <a name="plan-your-downtime"></a>Planeie o seu tempo de inatividade

Esta abordagem de migração requer algum tempo de inatividade para os seus utilizadores e aplicações. O objetivo é manter o tempo de inatividade ao mínimo. As seguintes considerações podem ajudar:

* Mantenha os seus volumes StorSimple disponíveis enquanto executa os seus trabalhos de transformação de dados.
* Quando terminar de executar os seus trabalhos de migração de dados por uma parte, é hora de remover o acesso do utilizador (pelo menos escrever acesso) dos volumes ou partilhas StorSimple. Um RoboCopy final irá apanhar a sua parte de ficheiros Azure. Depois pode cortar os seus utilizadores. O caso de executar o RoboCopy depende se optou por utilizar o Azure File Sync ou o acesso direto à partilha. A próxima secção da RoboCopy cobre este assunto.
* Depois de ter concluído a atualização do RoboCopy, está pronto para expor a nova localização aos seus utilizadores através da partilha de ficheiros Azure diretamente ou de uma partilha SMB numa instância do Windows Server com o Azure File Sync. Muitas vezes, uma implementação DFS-N ajudará a realizar um corte de forma rápida e eficiente. Manterá os seus endereços de ações existentes consistentes e repontará para um novo local que contenha os seus ficheiros e pastas migrados.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Determine quando o seu espaço de nome está totalmente sincronizado com o seu servidor

Quando utiliza o Azure File Sync para uma partilha de ficheiros Azure, é importante que determine que todo o seu espaço de nome terminou de descarregar para o servidor *antes* de iniciar qualquer RoboCopy local. O tempo que leva para descarregar o seu espaço de nome depende do número de itens na sua partilha de ficheiros Azure. Existem dois métodos para determinar se o seu espaço de nome chegou completamente ao servidor.

#### <a name="azure-portal"></a>Portal do Azure

Você pode usar o portal Azure para ver quando o seu espaço de nome chegou completamente.

* Inscreva-se no portal Azure e vá para o seu grupo de sincronização. Verifique o estado de sincronização do seu grupo de sincronização e ponto final do servidor.
* A direção interessante é o download. Se o ponto final do servidor for recentemente provisionado, irá mostrar **a sincronização inicial**, o que indica que o espaço de nome ainda está a descer.
Depois disso muda para qualquer coisa menos **sincronização inicial,** o seu espaço de nome será totalmente povoado no servidor. Agora pode prosseguir com um RoboCopy local.

#### <a name="windows-server-event-viewer"></a>Espectador de eventos do Servidor do Windows

Também pode utilizar o Visualizador de Eventos na sua instância do Windows Server para saber quando o espaço de nome chegou completamente.

1. Abra o **Visualizador de Eventos** e vá a **Aplicações e Serviços.**
1. Vá e abra **microsoft\FileSync\Agente\Telemetria**.
1. Procure o mais recente **evento 9102,** que corresponde a uma sessão de sincronização completa.
1. Selecione **Detalhes** e confirme que está a olhar para um evento onde o valor **SyncDirection** é **Download.**
1. Para o momento em que o seu espaço de nome tiver concluído o download para o servidor, haverá um único evento com **Scenario**, o valor **FullGhostedSync**, e **HResult**  =  **0**.
1. Se perder esse evento, também pode procurar outros **eventos 9102** com Download e Cenário   =     =  **"RegularSync"** do SyncDirection. Encontrar um destes eventos também indica que o espaço de nomes terminou o download e a sincronização progrediu para sessões regulares de sincronização, quer haja algo para sincronizar ou não neste momento.

### <a name="a-final-robocopy"></a>Um RoboCopy final

Neste momento, existem diferenças entre a instância do Windows Server no local e o aparelho StorSimple 8100 ou 8600.

1. É necessário acompanhar as alterações que os utilizadores ou apps produziram no lado StorSimple enquanto a migração estava em curso.
1. Para os casos em que utiliza o Azure File Sync: O aparelho StorSimple tem uma cache povoada contra a instância do Windows Server com apenas um espaço de nome sem nenhum conteúdo de ficheiro armazenado localmente neste momento. O RoboCopy final pode ajudar a iniciar a cache local de Azure File Sync, puxando o conteúdo de ficheiros em cache local tanto quanto está disponível e pode caber no servidor Azure File Sync.
1. Alguns ficheiros podem ter sido deixados para trás pelo trabalho de transformação de dados por causa de caracteres inválidos. Em caso afirmativo, copie-os para a instância do Servidor do Windows ativada por Ficheiros Azure. Mais tarde, podes ajustá-los para que se sincronizem. Se não utilizar o Azure File Sync para uma determinada participação, é melhor renomear os ficheiros com caracteres inválidos no volume StorSimple. Em seguida, executar o RoboCopy diretamente contra a partilha de ficheiros Azure.

> [!WARNING]
> Robocopy no Windows Server 2019 vive atualmente um problema que fará com que os ficheiros tiered by Azure File Sync no servidor alvo sejam recopiados a partir da fonte e re-carregados para Azure quando utilizarem a função /MIR de robocopia. É imperativo que utilize robocopia num Servidor windows diferente de 2019. Uma escolha preferida é o Windows Server 2016. Esta nota será atualizada caso o problema seja resolvido através do Windows Update.

> [!WARNING]
> *Não deve* iniciar o RoboCopy antes que o servidor tenha o espaço de nome para uma partilha de ficheiros Azure descarregada na totalidade. Para mais informações, consulte [Determine quando o seu espaço de nome foi totalmente descarregado para o seu servidor.](#determine-when-your-namespace-has-fully-synced-to-your-server)

 Só queres copiar ficheiros que foram alterados depois do trabalho de migração ter sido feito pela última vez e ficheiros que nunca passaram por estes empregos antes. Pode resolver o problema por que não se moveram mais tarde no servidor, depois da migração estar completa. Para obter mais informações, consulte [a resolução de problemas do Azure File Sync](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

RoboCopy tem vários parâmetros. O exemplo a seguir mostra um comando acabado e uma lista de razões para escolher estes parâmetros.

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /NP /B /MIR /IT /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Antecedentes:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Permite que o RoboCopy corra com vários fios. O padrão é 8, e o máximo é 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Estado das saídas para ficheiro LOG como UNICODE (substitui registo existente).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Saídas para a janela da consola. Usado em conjunto com a saída para um ficheiro de registo.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /NP
   :::column-end:::
   :::column span="1":::
      Omite o registo de progresso para manter o registo legível.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Executa roboCopy no mesmo modo que uma aplicação de backup usaria. Permite que o RoboCopy mova ficheiros para os que o utilizador atual não tem permissões.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Permite que o RoboCopy considere apenas deltas entre a fonte (aparelho StorSimple) e o alvo (diretório do Windows Server).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /IT
   :::column-end:::
   :::column span="1":::
      Garante que a fidelidade é preservada em certos cenários de espelhos.</br>Exemplo: Entre dois Robocopy executa um ficheiro experimenta uma alteração ACL e uma atualização de atributos, por exemplo, também está marcada *escondida*. Sem /IT, a alteração ACL pode ser perdida pela Robocopy e, portanto, não transferida para o local alvo.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      Fidelidade da cópia do ficheiro (predefinição é /COPY:DAT), bandeiras de cópia: D=Data, A=Atributos, T=Timestamps, S=Security=NTFS ACLs, O=Informações do Proprietário, U=aUditing informações.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      COPIAR TODAS as informações de ficheiros (equivalente a /COPY:DATSOU).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      Fidelidade para a cópia de diretórios (padrão é /DCOPY:DA), bandeiras de cópia: D=Dados, A=Atributos, T=Timestamps.
   :::column-end:::
:::row-end:::

Ao configurar as localizações de origem e alvo do comando RoboCopy, certifique-se de que revê a estrutura da fonte e do alvo para garantir que correspondem. Se usou a característica de mapeamento de diretórios do trabalho de migração, a sua estrutura de diretório de raiz pode ser diferente da estrutura do seu volume StorSimple. Se for esse o caso, podes precisar de vários empregos da RoboCopy, um para cada subdireção. Se não tiver a certeza se o comando funcionará como esperado, pode utilizar o parâmetro */L,* que simulará o comando sem realmente efetuar quaisquer alterações.

Este comando RoboCopy utiliza /MIR, pelo que não move ficheiros iguais (ficheiros hierárquicos, por exemplo). Mas se errar no caminho da origem e do alvo, o /MIR também purga estruturas de diretório na sua instância do Windows Server ou na partilha de ficheiros Azure que não estão presentes na via de origem StorSimple. Eles devem corresponder exatamente ao trabalho roboCopy para alcançar o objetivo pretendido de atualizar o seu conteúdo migrado com as últimas alterações feitas enquanto a migração está em curso.

Consulte o ficheiro de registo roboCopy para ver se os ficheiros foram deixados para trás. Se existirem problemas, corrija-os e reexame o comando RoboCopy. Não desprovisione quaisquer recursos StorSimple antes de corrigir problemas pendentes para ficheiros ou pastas que lhe interessam.

Se não utilizar o Azure File Sync para cache a partilha de ficheiros Azure em questão, mas optou pelo acesso direto à partilha:

1. [Monte a sua partilha de ficheiros Azure](storage-how-to-use-files-windows.md#mount-the-azure-file-share) como uma unidade de rede para uma máquina local do Windows.
1. Execute o RoboCopy entre o seu StorSimple e a partilha de ficheiros Azure montado. Se os ficheiros não copiarem, corrija os seus nomes no lado StorSimple para remover caracteres inválidos. Em seguida, retry RoboCopy. O comando RoboCopy anteriormente listado pode ser executado várias vezes sem causar uma chamada desnecessária ao StorSimple.

### <a name="user-cut-over"></a>Corte de utilizador

Se utilizar o Azure File Sync, é provável que tenha de criar as ações SMB na sua instância do Windows Server ativada por Azure File Sync que corresponda às ações que tinha nos volumes StorSimple. Pode carregar este passo frontal e fazê-lo mais cedo para não perder tempo aqui. Mas tem de garantir que antes deste ponto, ninguém tem acesso para causar alterações na instância do Windows Server.

Se tiver uma implementação DFS-N, pode apontar o DFN-Namespaces para as novas localizações da pasta do servidor. Se não tiver uma implementação DFS-N e tiver frontal o seu aparelho 8100 ou 8600 localmente com uma instância do Windows Server, pode retirar esse servidor do domínio. Em seguida, o domínio junta-se à sua nova instância do Servidor do Windows ativada por Ficheiros Azure. Durante esse processo, dê ao servidor o mesmo nome do servidor e partilhe nomes como o servidor antigo, de modo a que o cut-over se mantenha transparente para os seus utilizadores, política de grupo e scripts.

Saiba mais sobre [o DFS-N.](/windows-server/storage/dfs-namespaces/dfs-overview)

## <a name="deprovision"></a>Deprovisionamento

Ao desprovisionar um recurso, perde-se o acesso à configuração desse recurso e aos seus dados. A desprovisionamento não pode ser desfeita. Não prossiga até confirmar que:

* A sua migração está completa.
* Não existem dependências nos ficheiros, pastas ou cópias de segurança de volume StorSimple que está prestes a desprovisionar.

Antes de começar, é uma boa prática observar a sua nova implementação de Azure File Sync em produção durante algum tempo. Esse tempo dá-lhe a oportunidade de resolver qualquer problema que possa encontrar. Depois de ter observado a sua implementação de Azure File Sync durante pelo menos alguns dias, pode começar a desprovisionar recursos nesta ordem:

1. Desprovisionar o seu recurso StorSimple Data Manager através do portal Azure. Todos os seus trabalhos de DTS serão apagados com ele. Não conseguirá recuperar facilmente os registos de cópias. Se forem importantes para os seus registos, recupere-os antes de desprovisionar.
1. Certifique-se de que os seus aparelhos físicos StorSimple foram migrados e, em seguida, não os registe. Se não tens a certeza absoluta de que foram migradas, não prossigas. Se desprovisionar estes recursos enquanto ainda são necessários, não conseguirá recuperar os dados ou a sua configuração.<br>Opcionalmente, pode primeiro desprovisionar o recurso de volume StorSimple, que irá limpar os dados do aparelho. Isto pode demorar vários dias e **não irá,** forensemente, eliminar os dados do aparelho. Se isto for importante para si, manuseie o disco a zeros separadamente da desprovisionamento de recursos e de acordo com as suas políticas.
1. Se não houver mais dispositivos registados num Gestor de Dispositivos StorSimple, pode proceder à remoção do próprio recurso do Gestor de Dispositivos.
1. Está na hora de apagar a conta de armazenamento StorSimple em Azure. Mais uma vez, pare e confirme que a sua migração está completa e que nada e ninguém depende destes dados antes de prosseguir.
1. Desligue o aparelho físico StorSimple do seu centro de dados.
1. Se possuir o aparelho StorSimple, é livre de o reciclar para PC. Se o seu dispositivo estiver alugado, informe o locador e devolva o dispositivo conforme apropriado.

A sua migração está completa.

> [!NOTE]
> Ainda tem perguntas ou encontrou algum problema?</br>
> Estamos aqui para AzureFilesMigration@microsoft.com ajudar.

## <a name="next-steps"></a>Passos seguintes

* Conheça melhor [o Azure File Sync: aka.ms/AFS](./storage-sync-files-planning.md).
* Compreender a flexibilidade das políticas de [nivelamento em nuvem.](storage-sync-cloud-tiering.md)
* [Ative o Azure Backup](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) nas suas ações de ficheiros Azure para agendar instantâneos e definir horários de retenção de backup.
* Se vir no portal Azure que alguns ficheiros não estão a sincronizar permanentemente, consulte o [guia de resolução de problemas](storage-sync-files-troubleshoot.md) para obter medidas para resolver estes problemas.