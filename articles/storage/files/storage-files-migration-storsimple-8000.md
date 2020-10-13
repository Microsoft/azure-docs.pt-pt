---
title: Migração da série StorSimple 8000 para Azure File Sync
description: Saiba como migrar um aparelho StorSimple 8100 ou 8600 para Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: be61a6e75c4aa9b5714ffbf3b4f19656b347c493
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653252"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Migração StorSimple 8100 e 8600 para Azure File Sync

A série StorSimple 8000 é representada pelos aparelhos 8100 ou 8600 físicos no local e pelos seus componentes de serviço de nuvem. É possível migrar os dados de qualquer um destes aparelhos para um ambiente Azure File Sync. Azure File Sync é o serviço Azure de longo prazo padrão e estratégico para o qual os aparelhos StorSimple podem ser migrados.

A série StorSimple 8000 chegará ao seu [fim de vida](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) em dezembro de 2022. É importante começar a planear a sua migração o mais rápido possível. Este artigo fornece os conhecimentos de fundo necessários e passos de migração para uma migração bem sucedida para Azure File Sync. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> A Microsoft está empenhada em ajudar os clientes na sua migração. Envie um e-mail AzureFilesMigration@microsoft para um plano de migração personalizado, bem como assistência durante a migração.

Azure File Sync é um serviço de nuvem da Microsoft, baseado em dois componentes principais:

* Sincronização de ficheiros e tiering de nuvem.
* Ações de ficheiros como armazenamento nativo em Azure, que podem ser acedidas em vários protocolos como SMB e FILE REST. Uma partilha de ficheiros Azure é comparável a uma partilha de ficheiros num Servidor do Windows, que pode montar de forma nativa como uma unidade de rede. Suporta importantes aspetos de fidelidade de ficheiros como atributos, permissões e timetamps. Com as partilhas de ficheiros Azure, já não existe necessidade de uma aplicação ou serviço para interpretar os ficheiros e pastas armazenados na nuvem. Pode aceder-lhes de forma nativa sobre protocolos familiares e clientes como o Windows File Explorer. Isso faz com que o ficheiro Azure partilhe a abordagem ideal e mais flexível para armazenar dados de servidores de ficheiros de propósito geral, bem como alguns dados da aplicação, na nuvem.

Este artigo centra-se nos passos de migração. Se antes de migrar gostaria de saber mais sobre o Azure File Sync, recomendamos os seguintes artigos:

* [Azure File Sync - visão geral](https://aka.ms/AFS "Descrição geral")
* [Azure File Sync - guia de implementação](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Objetivos de migração

O objetivo é garantir a integridade dos dados de produção, bem como garantir a disponibilidade. Este último requer manter o tempo de inatividade ao mínimo, de modo a que possa encaixar ou apenas exceder ligeiramente as janelas de manutenção regulares.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>StorSimple caminho de migração da série 8000 para Azure File Sync

Um servidor local do Windows é necessário para executar um agente Azure File Sync. O Windows Server pode ser no mínimo um servidor 2012R2, mas idealmente é um Windows Server 2019.

Existem numerosas vias de migração alternativas e criaria demasiado tempo de um artigo para documentar todos eles e ilustrar por que razão carregam riscos ou desvantagens ao longo da rota que recomendamos como uma boa prática neste artigo.

![StorSimple 8000 fases de migração de séries](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "StorSimple 8000 série rota de migração panorâmica das fases mais abaixo neste artigo.")

A imagem anterior retrata fases que correspondem a secções neste artigo.
Utilizamos uma migração do lado da nuvem para evitar a recolha desnecessária de ficheiros para o seu aparelho StorSimple local. Esta abordagem evita o impacto do comportamento do caching local ou o uso de largura de banda de rede, qualquer um dos quais pode afetar as suas cargas de trabalho de produção.
Uma migração do lado da nuvem está a operar num instantâneo (um clone de volume) dos seus dados. Assim, os seus dados de produção estão isolados deste processo até ao corte no final da migração. Trabalhar fora do que é essencialmente um backup, torna a migração segura e facilmente repetível, caso se detesse de alguma dificuldade.

## <a name="considerations-around-existing-storsimple-backups"></a>Considerações em torno dos backups StorSimple existentes

O StorSimple permite-lhe fazer backups sob a forma de clones de volume. Este artigo usa um novo clone de volume para migrar os seus ficheiros ao vivo.
Se precisar de migrar cópias de segurança para além dos seus dados ao vivo, então todas as orientações deste artigo ainda se aplicam. A única diferença é que, em vez de começar com um novo clone de volume, começará com o clone de volume de reserva mais antigo que precisa para migrar.

A sequência é a seguinte:

* Determine o conjunto mínimo de clones de volume que deve migrar. Recomendamos manter esta lista ao mínimo, se possível, porque quanto mais backups migrar, mais tempo demorará o processo de migração global.
* Ao passar pelo processo de migração, comece com o clone de volume mais antigo que pretende migrar e em cada migração subsequente, use o próximo mais antigo.
* Quando cada migração de clone de volume estiver concluída, deve tirar uma foto de partilha de ficheiros Azure. [As imagens de partilha de ficheiros Azure](storage-snapshots-files.md) são como mantém cópias de segurança pontuais dos ficheiros e estrutura de pastas para as suas partilhas de ficheiros Azure. Você precisará destes instantâneos após a conclusão da migração, para garantir que você preservou versões de cada um dos seus clones de volume à medida que progride através da migração.
* Certifique-se de que toma fotos de partilha de ficheiros Azure para todas as ações de ficheiros Azure, que são servidas pelo mesmo volume StorSimple. Os clones de volume estão no nível de volume, as imagens de partilha de ficheiros Azure estão no nível de ações. Deve tirar uma fotografia de partilha (em cada partilha de ficheiros Azure) após a migração de um clone de volume.
* Repita o processo de migração para um clone de volume e faça snapshots de partilha após cada clone de volume até ser apanhado numa imagem dos dados ao vivo. O processo de migração de um clone de volume é descrito nas fases abaixo. 

Se não precisar de movimentar backups e puder iniciar uma nova cadeia de backups no lado da partilha de ficheiros Azure após a migração de apenas os dados ao vivo, então isso é benéfico para reduzir a complexidade na migração e o tempo que a migração levará. Pode tomar a decisão de mover ou não backups e quantos por cada volume (e não cada ação) que tenha no StorSimple.

## <a name="phase-1-get-ready"></a>Fase 1: Prepare-se

:::row:::
    :::column:::
        ![Uma imagem que ilustra uma parte da imagem anterior, visão geral que ajuda a focar esta subsecção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        A base para a migração é um clone de volume e um aparelho em nuvem virtual, chamado StorSimple 8020.
Esta fase centra-se na implantação destes recursos em Azure.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>Implementar um aparelho virtual StorSimple 8020

A implantação de um aparelho em nuvem é um processo que requer segurança, ligação em rede e algumas outras considerações.

> [!IMPORTANT]
> O seguinte guia contém algumas secções desnecessárias. Leia e siga o artigo desde o início até ao "Passo 3". Então volte a este artigo. Não precisa completar o "Passo 3" ou qualquer coisa além dele nesse guia, neste momento.

[Implantação de um aparelho virtual StorSimple 8020](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>Determine um clone de volume para usar

Quando estiver pronto para iniciar a migração, o primeiro passo é pegar num novo clone de volume - tal como faria para o backup - que captura o estado atual do seu armazenamento em nuvem StorSimple. Tome um clone para cada um dos volumes StorSimple que tiver.
Se precisa de mover backups, então o primeiro clone de volume que utiliza não é um clone recém-criado, mas o clone de volume mais antigo (cópia de segurança mais antiga) que precisa para migrar.
Consulte a secção ["Considerações em torno das cópias de segurança StorSimple existentes"](#considerations-around-existing-storsimple-backups) para obter orientações detalhadas.

> [!IMPORTANT]
> O seguinte guia contém algumas secções desnecessárias. Leia e siga apenas os passos descritos na secção ligada à secção. Então volte a este artigo. Não é necessário seguir a secção "Próximos passos".

[Criar um clone de um volume](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>Use o clone de volume

A última fase da fase 1 é disponibilizar o clone de volume que escolheu, disponível no aparelho virtual 8020 em Azure.

> [!IMPORTANT]
> O seguinte guia contém os passos necessários, mas também - no final - uma instrução para formatar o volume. **NÃO FORMATAR O VOLUME** Leia e siga o "secção 7" ligado à "secção 7" desde o início até à instrução: "10. Para formatar um volume simples, ..."  Pare antes de seguir este passo e volte a este artigo.

[Monte um clone de volume no aparelho virtual 8020 em Azure](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>Resumo da fase 1

Agora que completou a fase 1, terá feito o seguinte:

* Implantado um aparelho virtual StorSimple 8020 em Azure.
* Determinado com que volume de clone começará a migração.
* Montou o seu ous clone de volume (um para cada volume vivo) para o aparelho virtual StorSimple em Azure, com os seus dados disponíveis para posterior utilização.

## <a name="phase-2-cloud-vm"></a>Fase 2: Cloud VM

:::row:::
    :::column:::
        ![Ilustração que mostra que agora é hora de providenciar um VM e expor o clone de volume (ou múltiplo) ao VM sobre iSCSI.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Depois de o seu clone inicial estar disponível no aparelho virtual StorSimple 8020 em Azure, é agora altura de disponibilizar um VM e expor o clone de volume (ou múltiplo) ao VM sobre o iSCSI.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Implementar um VM Azure

A máquina virtual do Windows Server em Azure é tal como o StorSimple 8020, uma infraestrutura temporária que só é necessária durante a migração.
A configuração do VM que implementa depende principalmente do número de itens (ficheiros e pastas) que estará a sincronizar. Recomendamos que vá com uma configuração de desempenho mais alta se tiver alguma preocupação.

Um único Servidor windows pode sincronizar até 30 ações de ficheiros Azure.
As especificações que decide sobre a necessidade de abranger cada partilha/caminho ou a raiz do volume StorSimple e contar os itens (ficheiros e pastas).

O tamanho geral dos dados é menos de um estrangulamento - é o número de itens que você precisa para adaptar as especificações da máquina.

* [Saiba como dimensionar um Servidor do Windows com base no número de itens (ficheiros e pastas) que necessita para sincronizar.](storage-sync-files-planning.md#recommended-system-resources)

    **Por favor, note:** O artigo anteriormente ligado apresenta uma tabela com um intervalo para memória do servidor (RAM). Oriente para o grande número para o VM Azure. Pode orientar-se para o número menor para a sua máquina no local.

* [Saiba como implementar um Windows Sever VM.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Certifique-se de que o VM está implantado na mesma região de Azure que o aparelho virtual StorSimple 8020. Se, como parte desta migração, também precisar de alterar a região dos seus dados em nuvem da região onde está armazenada hoje, pode fazê-lo mais tarde, quando fornecer ações de ficheiros Azure.

> [!IMPORTANT]
> Frequentemente, um Windows Server no local é utilizado para fazer frente ao seu aparelho StorSimple no local. Nesta configuração, é possível ativar a função "[Deduplicação de Dados](https://docs.microsoft.com/windows-server/storage/data-deduplication/install-enable)" no Windows Server. **Se utilizou a Deduplicação de Dados com os seus dados StorSimple, certifique-se de que também ativa a Deduplicação de Dados neste Azure VM.** Não confunda esta deduplica ao nível do ficheiro com a deduplica de nível de bloco incorporada, para a qual não é necessária qualquer ação.

> [!IMPORTANT]
> Para otimizar para o desempenho, implemente um **disco de SO rápido** para o seu VM na nuvem. Armazenará a base de dados de sincronização no disco OS para todos os seus volumes de dados. Além disso, certifique-se de que cria um **grande disco de SO**. Dependendo do número de itens (ficheiros e pastas) nos seus volumes StorSimple, o disco OS poderá necessitar de **várias centenas** de GiB de espaço para acomodar a base de dados de sincronização.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>Expor os volumes StorSimple 8020 ao Azure VM

Nesta fase, está a ligar um ou vários volumes StorSimple do aparelho virtual 8020 ao iSCSI ao VM do Windows Server que a provisionou.

> [!IMPORTANT]
> Para os seguintes artigos, complete apenas o **IP get privado para o aparelho em nuvem** e Conecte-se sobre as secções **iSCSI** e volte a este artigo.

1. [Obter o IP privado da aplicação da cloud](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Conecte-se sobre o iSCSI](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>Resumo da fase 2

Agora que terminou a fase 2, tem: 

* A provisionou um VM do Windows Server na mesma região que o aparelho StorSimple 8020
* Expôs todos os volumes aplicáveis do 8020 ao Windows Server VM sobre o iSCSI.
* Deve agora ver o conteúdo do ficheiro e da pasta, quando utilizar o Explorador de Ficheiros no VM do servidor nos volumes montados.

Só proceda à fase 3 quando tiver concluído estes passos para todos os volumes que necessitam de migração.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>Fase 3: Configurar ações de ficheiros Azure e preparar-se para o Azure File Sync

:::row:::
    :::column:::
        ![Ilustração que mostra a necessidade de determinar e providenciar uma série de partilhas de ficheiros Azure e criar um Servidor do Windows no local como uma substituição do aparelho StorSimple.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        Nesta fase, irá determinar e a provisionar uma série de partilhas de ficheiros Azure, criando um Servidor do Windows no local como substituição de aparelho StorSimple e configurar esse servidor para Azure File Sync. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Mapeie os seus espaços de nome existentes para ações de ficheiros Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Implementar ações de ficheiros Azure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Se precisar de alterar a região Azure da região atual, os seus dados StorSimple residem, então fornece as ações de ficheiroS Azure na nova região que pretende utilizar. Você determina a região selecionando-a quando fornece as contas de armazenamento que detêm as suas ações de ficheiroS Azure. Certifique-se de que também o recurso Azure File Sync que irá disponibilizar abaixo, está nessa mesma nova região.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Implementar o recurso cloud Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Se precisar de alterar a região Azure da região atual em que residem os seus dados StorSimple, então fornece as contas de armazenamento das suas ações de ficheiroS Azure na nova região. Certifique-se de que selecionou a mesma região quando implementar este Serviço de Sincronização de Armazenamento.

### <a name="deploy-an-on-premises-windows-server"></a>Implementar um Servidor Windows no local

* Crie um Windows Server 2019 - no mínimo 2012R2 - como uma máquina virtual ou servidor físico. Um cluster de falha do Windows Server também é suportado. Não reutilizar o servidor que poderá ter na frente do StorSimple 8100 ou 8600.
* Provisão ou adicionar Armazenamento Direto Anexado (DAS em comparação com o NAS, que não é suportado).

É melhor dar ao seu novo Windows Server uma quantidade igual ou maior de armazenamento do que o seu aparelho StorSimple 8100 ou 8600 tem disponível localmente para caching. Utilizará o Servidor do Windows da mesma forma que utilizou o aparelho StorSimple, se tiver a mesma quantidade de armazenamento que o aparelho, então a experiência de caching deve ser semelhante, se não a mesma.
Pode adicionar ou remover o armazenamento do seu Servidor Windows à vontade. Isto permite-lhe escalar o seu tamanho de volume local e a quantidade de armazenamento local disponível para caching.

### <a name="prepare-the-windows-server-for-file-sync"></a>Prepare o Servidor do Windows para sincronização de ficheiros

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Configure Azure File Sync no Servidor windows

O seu Windows Server registado no local deve estar pronto e ligado à internet para este processo.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Certifique-se de ligar o nível da nuvem!** O tiering em nuvem é a funcionalidade AFS que permite ao servidor local ter menos capacidade de armazenamento do que é armazenado na nuvem, mas tem o espaço de nome completo disponível. Os dados locais interessantes também são cached localmente para um desempenho rápido e local de acesso. Outra razão para ligar o nível da nuvem neste passo é que não queremos sincronizar o conteúdo dos ficheiros nesta fase, apenas o espaço de nomes deve estar em movimento neste momento.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>Fase 4: Configurar o Azure VM para sincronização

:::row:::
    :::column:::
        ![Ilustração que mostra como você vai obter o VM conectado via Azure File Sync e iniciar uma primeira rodada de ficheiros em movimento a partir do seu(s) clone de volume StorSimple.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Esta fase diz respeito ao seu Azure VM com o iSCSI montado, clone de primeiro volume. Durante esta fase, você terá o VM ligado através do Azure File Sync e iniciará uma primeira ronda de ficheiros móveis a partir do seu(s) clone de volume StorSimple.
        
    :::column-end:::
:::row-end:::

Já configura o seu servidor no local que substituirá o seu aparelho StorSimple 8100 ou 8600, por Azure File Sync. 

Configurar o Azure VM é um processo quase idêntico, com um passo adicional. Os seguintes passos irão guiá-lo através do processo.

> [!IMPORTANT]
> É importante que o VM Azure não esteja **configurado com o nível de nuvem ativado!** Você vai trocar o volume deste servidor com clones de volume mais recentes durante toda a migração. O tiering em nuvem não tem nenhum benefício e sobrecarga no uso do CPU que você deve evitar.

1. [Desdobre o agente AFS. (ver secção anterior)](#prepare-the-windows-server-for-file-sync)
2. [Preparar o VM para o Azure File Sync.](#get-the-vm-ready-for-azure-file-sync)
3. [Configure sincronização](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>Prepare o VM para o Azure File Sync

O Azure File Sync é utilizado para mover os ficheiros dos volumes iSCSI StorSimple montados para as ações de ficheiros target Azure.
Durante este processo de migração, irá montar vários clones de volume para o seu VM, sob a mesma letra de unidade. O Azure File Sync tem de ser configurado para ver o próximo clone de volume que montou como uma versão mais recente dos ficheiros e pastas e atualizar as ações de ficheiros Azure ligadas através do Azure File Sync. 

> [!IMPORTANT]
> Para que isto funcione, uma chave de registo deve ser definida no servidor antes de o Azure File Sync ser configurado.

1. Criar um novo diretório na unidade de sistema do VM. As informações do Azure File Sync terão de ser persistidos lá em vez de nos clones de volume montados. Por exemplo: `"C:\syncmetadata"`
2. Abra o regedit e localize a seguinte colmeia de registo: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. Criar uma nova chave de tipo String, chamada: ***MetadataRootPath***
4. Desacorra o caminho completo para o diretório que criou no volume do sistema, por exemplo: `C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Configure Azure File Sync no Azure VM

Este passo é semelhante à secção anterior, que discute como configurar o AFS no servidor no local.

A diferença é que não deve ativar o tiering de nuvem neste servidor e que precisa de se certificar de que as pastas certas estão ligadas às ações de ficheiros Azure certas. Caso contrário, o seu nome de ações de ficheiros Azure e o conteúdo dos dados não corresponderão e não há forma de renomear os recursos da nuvem ou pastas locais sem reconfigurar a sincronização.

Consulte a [secção anterior sobre como configurar o Azure File Sync num Servidor do Windows](#configure-azure-file-sync-on-the-windows-server).

### <a name="step-4-summary"></a>Resumo do passo 4

Neste momento, terá configurado com sucesso o Azure File Sync no Azure VM que montou o seu clone de volume StorSimple através do iSCSI.
Os dados estão agora a fluir do Azure VM para as várias partilhas de ficheiros Azure e a partir daí aparece um espaço de nome totalmente cansado no seu Windows Server.

> [!IMPORTANT]
> Certifique-se de que não existem alterações ou acesso ao utilizador concedido ao Servidor do Windows neste momento.

Os dados iniciais do clone de volume que se movem através do Azure VM para as ações de ficheiros Azure podem demorar muito tempo, potencialmente semanas. Estimar o tempo que isto vai demorar é complicado e depende de muitos fatores. Mais notavelmente a velocidade a que o Azure VM pode aceder a ficheiros nos volumes StorSimple e a rapidez com que o Azure File Sync pode processar os ficheiros e pastas que precisam de ser sincronizados. 

Por experiência própria, podemos assumir que a largura de banda - portanto, o tamanho real dos dados - desempenha um papel subordinado. O tempo que esta ou qualquer ronda de migração subsequente demorará depende principalmente do número de itens que podem ser processados por segundo. Assim, por exemplo, 1 TiB com 100.000 ficheiros e pastas provavelmente terminará mais devagar do que 1 TiB com apenas 50.000 ficheiros e pastas.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>Fase 5: Iterada através de clones de vários volumes

:::row:::
    :::column:::
        ![Ilustração que mostra como minimizar o tempo de inatividade usando vários clones de volume e dizendo quando a sincronização é feita.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Como discutido na fase anterior, a sincronização inicial pode demorar muito tempo. Os seus utilizadores e aplicações ainda estão a aceder ao aparelho StorSimple 8100 ou 8600 no local. Isto significa que as mudanças estão a acumular-se, e a cada dia um delta maior entre os dados vivos e o clone de volume inicial, está atualmente a migrar, formas. Nesta secção, aprenderá a minimizar o tempo de inatividade utilizando vários clones de volume e dizendo quando a sincronização é feita.
    :::column-end:::
:::row-end:::

Infelizmente, o processo de migração não é instantâneo. Isto significa que o delta acima referido para os dados vivos é uma consequência inevitável. A boa notícia é que pode repetir o processo de montagem de novos clones de volume. O delta de cada clone de volume será progressivamente menor. Assim, eventualmente, uma sincronização terminará numa duração de tempo que considere aceitável para levar utilizadores e apps offline para cortar para o seu servidor Windows no local.

Repita os seguintes passos até que a sincronização complete numa duração suficientemente rápida para que se sinta confortável em levar os utilizadores e aplicações offline:

1. [Determine que a sincronização está completa para um dado clone de volume.](#determine-when-sync-is-done)
2. [Pegue num novo clone de volume e monte-o no aparelho virtual 8020.](#the-next-volume-clones)
3. [Determinar quando a sincronização é feita.](#determine-when-sync-is-done)
4. [Estratégia de corte](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>O próximo clone de volume(s)

Discutimos a tomada de um clone de volume no início deste artigo.
Esta fase tem duas ações:

1. [Pegue um clone de volume](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [Monte o clone de volume (ver acima)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>Determinar quando a sincronização é feita

Quando a sincronização estiver feita, pode parar a sua medição de tempo e determinar se precisa de repetir o processo de tomada de um clone de volume e montá-lo ou se a sincronização de tempo demorou com o último clone de volume era suficientemente pequena.

Para determinar a sincronização está completo:

1. Abra o Observador de Eventos e navegue para **Aplicações e Serviços**
2. Navegar e abrir **Microsoft\FileSync\Agente\Telemetria**
3. Procure o mais recente **evento 9102,** que corresponde a uma sessão de sincronização completa
4. Selecione **Detalhes** e confirme que o valor **SyncDirection** é **upload**
5. Verifique o **HResult** e confirme que mostra **0**. Isto significa que a sessão de sincronização foi um sucesso. Se o HResult é um valor não-zero, então houve um erro durante a sincronização. Se o **PerItemErrorCount** for superior a 0, então alguns ficheiros ou pastas não sincronizaram corretamente. É possível ter um HResult de 0 mas um PerItemErrorCount que seja maior que 0. Neste momento, não precisas de te preocupar com o PerItemErrorCount. Vamos apanhar estes ficheiros mais tarde. Se esta contagem de erros for significativa, milhares de itens, contacte o suporte do cliente e peça para ser ligado ao grupo de produtos Azure File Sync para obter orientação direta sobre as melhores e próximas fases.
6. Verifique para ver vários eventos 9102 com HResult 0 seguidos. Isto indica que a sincronização está completa para este clone de volume.

### <a name="cut-over-strategy"></a>Estratégia de corte

1. Determine se a sincronização de um clone de volume é rápida o suficiente agora. (Delta pequeno o suficiente.)
2. Desative o aparelho StorSimple.
3. Um RoboCopy final.

Meça o tempo e determine se a sincronização de um clone de volume recente pode terminar dentro de uma janela de tempo suficientemente pequena, que pode pagar o tempo de inatividade no seu sistema.

É tempo de desativar o acesso do utilizador ao aparelho StorSimple. Sem mais mudanças. O tempo de inatividade já começou.
Tem de deixar o aparelho ligado e ligado, mas deve agora evitar alterações.

Na fase 6, irá apanhar qualquer delta nos dados ao vivo desde o último clone de volume.

## <a name="phase-6-a-final-robocopy"></a>Fase 6: Um RoboCopy final

Neste momento, existem duas diferenças entre o seu Windows Server e o aparelho StorSimple 8100 ou 8600:

1. Pode haver ficheiros que não tenham sincronizado (ver **PerItemErrors** do registo de eventos acima)
2. O aparelho StorSimple tem uma cache povoada vs. o Windows Server apenas um espaço de nome sem nenhum conteúdo de ficheiro armazenado localmente neste momento.

![Ilustração que mostra como a cache do Servidor do Windows foi trazida para o estado do aparelho e garante que nenhum ficheiro é deixado para trás com um RoboCopy final.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

Podemos levar a cache do Servidor do Windows até ao estado do aparelho e garantir que nenhum ficheiro seja deixado para trás com um RoboCopy final.

> [!CAUTION]
> É imperativo que o comando RoboCopy que segue, seja exatamente como descrito abaixo. Só queremos copiar ficheiros que sejam locais e ficheiros que não tenham passado pela abordagem clone+sync de volume antes. Podemos resolver os problemas porque não sincronizaram mais tarde, depois da migração estar completa. (Ver [Azure File Sync Resolução de problemas](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing). É mais provável que os caracteres imprimíveis em nomes de ficheiros que não percam quando forem apagados.)

Comando RoboCopy:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Antecedentes:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Permite que o RoboCopy corra com vários fios. O padrão é 8, máx é 128.
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
      /B
   :::column-end:::
   :::column span="1":::
      Executa roboCopy no mesmo modo que uma aplicação de backup usaria. Permite que o RoboCopy mova ficheiros para os quais o utilizador atual não tem permissões.
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
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      fidelidade da cópia do ficheiro (predefinição é /COPY:DAT), bandeiras de cópia: D=Data, A=Atributos, T=Timestamps, S=Security=NTFS ACLs, O=Informação do Proprietário, U=aUditing info
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      CÓPIA DE TODAS AS informações de ficheiro (equivalente a /COPY:DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      fidelidade para a cópia de diretórios (padrão é /DCOPY:DA), bandeiras de cópia: D=Dados, A=Atributos, T=Timestamps
   :::column-end:::
:::row-end:::

Deve executar este comando RoboCopy para cada um dos diretórios no Windows Server como alvo, que configuraste com sincronização de ficheiros para um ficheiro Azure.

Pode executar vários destes comandos em paralelo.
Uma vez concluído este passo RoboCopy, pode permitir que os seus utilizadores e aplicações acedam ao Servidor do Windows como fizeram com o aparelho StorSimple anteriormente.

Consulte o ficheiro de registo robocopia para ver se os ficheiros foram deixados para trás. Se existirem problemas, na maioria dos casos pode resolvê-los após a migração estar concluída e os seus utilizadores e aplicações foram realondo-os no seu Windows Server. Se precisar de corrigir problemas, faça-o antes da fase 7.

É provável que seja necessário criar as ações SMB no Servidor do Windows que tinha nos dados StorSimple antes. Pode carregar este passo frontalmente e fazê-lo mais cedo para não perder tempo aqui, mas tem de garantir que antes deste ponto não ocorram alterações nos ficheiros no servidor do Windows.

Se tiver uma implementação DFS-N, pode apontar o DFN-Namespaces para as novas localizações da pasta do servidor. Se não tiver uma implementação DFS-N e tiver frontalmente o seu aparelho 8100 8600 localmente com um Windows Server, pode tirar esse servidor do domínio, e o domínio juntar-se ao seu novo Servidor do Windows com AFS ao domínio, dar-lhe o mesmo nome de servidor que o antigo servidor, e os mesmos nomes de partilha, então o corte para o novo servidor permanece transparente para os seus utilizadores. , política de grupo, ou scripts.

## <a name="phase-7-deprovision"></a>Fase 7: Deprovisionamento

Durante a última fase, foi iterado através de vários clones de volume e, eventualmente, conseguiu reduzir o acesso do utilizador ao novo Windows Server depois de o ter desligado do aparelho StorSimple.

Pode agora começar a desprovisionar recursos desnecessários.
Antes de começar, é uma boa prática observar a sua nova implementação de Azure File Sync em produção, por um tempo. Isso dá-lhe opções para resolver quaisquer problemas que possa encontrar.

Uma vez satisfeito e tiver observado a sua implantação de AFS durante pelo menos alguns dias, pode começar a desprovisionar recursos nesta ordem:

1. Desligue o Azure VM que usamos para mover dados dos clones de volume para as ações de ficheiros Azure através de sincronização de ficheiros.
2. Vá ao seu recurso de Serviço de Sincronização de Armazenamento em Azure e não registe o Azure VM. Isso remove-o de todos os grupos de sincronização.

    > [!WARNING]
    > **CERTIFIQUE-se de escolher a máquina certa.** Desligou o VM da nuvem, o que significa que deve ser o único servidor offline na lista de servidores registados. Não deve escolher o Windows Server neste passo, ao fazê-lo irá desregissi-lo.

3. Eliminar Azure VM e os seus recursos.
4. Desative o aparelho StorSimple virtual 8020.
5. Desprovisionar todos os recursos StorSimple no Azure.
6. Desligue o aparelho físico StorSimple do seu centro de dados.

A sua migração está completa.

## <a name="next-steps"></a>Passos seguintes

Conheça melhor o Azure File Sync. Especialmente com a flexibilidade das políticas de nivelamento em nuvem.

Se vir no portal Azure, ou nos eventos anteriores, que alguns ficheiros não estão permanentemente a sincronizar, reveja o guia de resolução de problemas para que sejam medidas para resolver estes problemas.

* [Visão geral do Azure File Sync: aka.ms/AFS](https://aka.ms/AFS)
* [Tiering de nuvem](storage-sync-cloud-tiering.md) 
* [Guia de resolução de problemas do Azure File Sync](storage-sync-files-troubleshoot.md)
