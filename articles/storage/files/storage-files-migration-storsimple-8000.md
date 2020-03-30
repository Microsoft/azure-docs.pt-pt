---
title: Migração da série StorSimple 8000 para O Sincronizado de Ficheiros Azure
description: Aprenda a migrar um aparelho StorSimple 8100 ou 8600 para o Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7e5f70d0323aa5c502491ab99db303fde31ade83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528630"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 e 8600 migração para O Ficheiro Sincronizado Azure

A série StorSimple 8000 está representada pelos 8100 ou pelos 8600 aparelhos físicos, no local, e pelos seus componentes de serviço na nuvem. É possível migrar os dados de qualquer um destes aparelhos para um ambiente De sincronização de ficheiros Azure. O Azure File Sync é o serviço Azure de longo prazo padrão e estratégico para o quais os aparelhos StorSimple podem ser migrados.

A série StorSimple 8000 chegará ao fim [da vida](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) em dezembro de 2022. É importante começar a planear a sua migração o mais rápido possível. Este artigo fornece os conhecimentos de fundo necessários e etapas de migração para uma migração bem sucedida para O Ficheiro Sincronizado Azure. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> A Microsoft está empenhada em ajudar os clientes na sua migração. Envie AzureFilesMigration@microsoft um e-mail .com para um plano de migração personalizado, bem como assistência durante a migração.

O Azure File Sync é um serviço na nuvem da Microsoft, baseado em dois componentes principais:

* Sincronização de ficheiros e tiering em nuvem.
* As ações de arquivo como armazenamento nativo em Azure, que podem ser acedidas ao longo de vários protocolos como SMB e file REST. Uma partilha de ficheiros Azure é comparável a uma partilha de ficheiros num Windows Server, que pode montar de forma nativa como uma unidade de rede. Suporta aspetos importantes da fidelidade do ficheiro, como atributos, permissões e carimbos de tempo. Com as partilhas de ficheiros Azure, já não é necessário uma aplicação ou serviço para interpretar os ficheiros e pastas armazenados na nuvem. Pode aceder-lhes de forma nativa sobre protocolos familiares e clientes como o Windows File Explorer. Isto faz com que o ficheiro Azure partilhe a abordagem ideal e mais flexível para armazenar dados do servidor de ficheiros de propósito geral, bem como alguns dados de aplicação, na nuvem.

Este artigo centra-se nos passos da migração. Se antes de migrar, gostaria de saber mais sobre o Azure File Sync, recomendamos os seguintes artigos:

* [Sincronização de ficheiros Azure - visão geral](https://aka.ms/AFS "Descrição geral")
* [Sincronização de Ficheiros Azure - guia de implementação](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Objetivos de migração

O objetivo é garantir a integridade dos dados de produção, bem como garantir a disponibilidade. Este último requer manter o tempo de paragem ao mínimo, de modo a que possa encaixar ou apenas exceder ligeiramente as janelas de manutenção regulares.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>Caminho de migração da série StorSimple 8000 para O Sincronizado de Ficheiros Azure

Um Servidor Windows local é necessário para executar um agente Desincronização de Ficheiros Azure. O Windows Server pode estar no mínimo um servidor 2012R2, mas idealmente é um Windows Server 2019.

Existem numerosas vias de migração alternativas e criaria demasiado tempo de um artigo para documentá-los a todos e ilustrar por que razão eles têm riscos ou desvantagens sobre a rota que recomendamos como uma melhor prática neste artigo.

![Visão geral das fases de migração da série StorSimple 8000](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "Resumo da rota de migração da série StorSimple 8000 das fases mais abaixo neste artigo.")

A imagem anterior retrata fases que correspondem a secções neste artigo.
Utilizamos uma migração do lado da nuvem para evitar a recolha desnecessária de ficheiros para o seu aparelho StorSimple local. Esta abordagem evita afetar o comportamento local de cache ou o uso da largura de banda da rede, qualquer uma das quais pode afetar as suas cargas de trabalho de produção.
Uma migração do lado da nuvem está a operar num instantâneo (um clone de volume) dos seus dados. Assim, os seus dados de produção estão isolados deste processo até ao corte no final da migração. Trabalhar fora do que é essencialmente um backup, torna a migração segura e facilmente repetível, caso se depara com alguma dificuldade.

## <a name="considerations-around-existing-storsimple-backups"></a>Considerações em torno dos backups storSimple existentes

O StorSimple permite-lhe obter cópias de segurança sob a forma de clones de volume. Este artigo usa um novo clone de volume para migrar os seus ficheiros ao vivo.
Se precisa de migrar cópias de segurança para além dos seus dados ao vivo, então todas as orientações deste artigo ainda se aplicam. A única diferença é que, em vez de começar com um novo clone de volume, começará com o mais antigo clone de volume de cópia de segurança que precisa para migrar.

A sequência é a seguinte:

* Determine o conjunto mínimo de clones de volume que deve migrar. Recomendamos manter esta lista ao mínimo, se possível, porque quanto mais backups migrar, mais tempo o processo de migração global demorará.
* Ao passar pelo processo de migração, comece com o clone de volume mais antigo que pretende migrar e em cada migração subsequente, use o próximo mais antigo.
* Quando cada migração de clones de volume estiver concluída, deve tirar uma fotografia de partilha de ficheiros Azure. As imagens de partilha de [ficheiros Azure](storage-snapshots-files.md) são como manter cópias de segurança ponto-a-tempo dos ficheiros e estrutura de pastas para as suas partilhas de ficheiros Azure. Você precisará destes instantâneos após a migração terminar, para garantir que você tem versões preservadas de cada um dos seus clones de volume à medida que progride através da migração.
* Certifique-se de que leva fotos de partilha de ficheiros Azure para todas as ações de ficheiros Azure, que são servidas pelo mesmo volume StorSimple. Os clones de volume estão no nível de volume, as imagens de partilha de ficheiros Azure estão no nível de partilha. Deve tirar uma fotografia de partilha (em cada parte do ficheiro Azure) após a migração de um clone de volume estar concluída.
* Repita o processo de migração para um clone de volume e tire fotos de partilha após cada clone de volume até ser apanhado até uma foto dos dados ao vivo. O processo de migração de um clone de volume é descrito nas fases abaixo. 

Se não precisar de movimentar backups e pode iniciar uma nova cadeia de backups no lado da partilha de ficheiros Azure após a migração de apenas os dados vivos, então isso é benéfico para reduzir a complexidade na migração e o tempo que a migração levará. Pode tomar a decisão de mover ou não backups e quantos por cada volume (não cada ação) tem no StorSimple.

## <a name="phase-1-get-ready"></a>Fase 1: Prepare-se

:::row:::
    :::column:::
        ![Uma imagem que ilustra uma parte da imagem anterior e geral que ajuda a focar esta subsecção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        A base para a migração é um clone de volume e um aparelho de nuvem virtual, chamado StorSimple 8020.
Esta fase centra-se na implantação destes recursos no Azure.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>Implementar um aparelho virtual StorSimple 8020

A implantação de um aparelho em nuvem é um processo que requer segurança, networking e algumas outras considerações.

> [!IMPORTANT]
> O guia seguinte contém algumas secções desnecessárias. Leia e siga o artigo desde o início até "Passo 3". Então volte a este artigo. Não precisa de completar o "Passo 3" nem nada além dele nesse guia, neste momento.

[Implantação de um aparelho virtual StorSimple 8020](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>Determinar um clone de volume a utilizar

Quando estiver pronto para iniciar a migração, o primeiro passo é tomar um novo clone de volume - tal como faria para backup - que captura o estado atual do seu armazenamento em nuvem StorSimple. Pegue um clone para cada um dos volumes StorSimple que tiver.
Se precisa de backups em movimento, então o primeiro clone de volume que usa não é um clone recém-criado, mas o clone de volume mais antigo (cópia de segurança mais antiga) que precisa de migrar.
Consulte a secção "Considerações em torno das cópias de [segurança StorSimple existentes"](#considerations-around-existing-storsimple-backups) para obter orientações detalhadas.

> [!IMPORTANT]
> O guia seguinte contém algumas secções desnecessárias. Leia e siga apenas os passos descritos na secção ligada à secção ligada. Então volte a este artigo. Não precisa de seguir a secção "Próximos passos".

[Criar um clone de um volume](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>Use o clone de volume

A última fase da fase 1 é disponibilizar o clone de volume que escolheu, disponível no aparelho virtual 8020 em Azure.

> [!IMPORTANT]
> O guia seguinte contém os passos necessários, mas também - no final - uma instrução para formatar o volume. **NÃO FORFORNAO O VOLUME** Leia e siga a "secção 7" ligada ao início até instrução: "10. Para formatar um volume simples, ..."  Pare antes de seguir este passo e volte a este artigo.

[Monte um clone de volume no aparelho virtual 8020 em Azure](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>Resumo da fase 1

Agora que completou a fase 1, terá feito o seguinte:

* Implantou um aparelho virtual StorSimple 8020 em Azure.
* Determinou com que volume vai começar a migração.
* Montou o seu clone de volume (um para cada volume vivo) para o aparelho virtual StorSimple em Azure, com os seus dados disponíveis para posterior utilização.

## <a name="phase-2-cloud-vm"></a>Fase 2: Cloud VM

:::row:::
    :::column:::
        ![Uma imagem que ilustra uma parte da imagem anterior e geral que ajuda a focar esta subsecção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Depois de o seu clone inicial estar disponível no aparelho virtual StorSimple 8020 em Azure, é agora hora de fornecer um VM e expor o clone de volume (ou múltiplos) a esse VM sobre iSCSI.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Implementar um VM Azure

A máquina virtual do Windows Server em Azure é tal como o StorSimple 8020, uma peça temporária de infraestrutura que só é necessária durante a migração.
A configuração do VM que implementa depende principalmente do número de itens (ficheiros e pastas) que estará a sincronizar. Recomendamos que vá com uma configuração de desempenho mais elevada se tiver alguma preocupação.

Um único Servidor Windows pode sincronizar até 30 partilhas de ficheiros Azure.
As especificações que decidir sobre a necessidade de abranger cada ação/caminho ou a raiz do volume StorSimple e contar os itens (ficheiros e pastas).

O tamanho global dos dados é menos de um estrangulamento - é o número de itens a que é necessário para adaptar as especificações da máquina.

* [Saiba como dimensionar um Servidor do Windows com base no número de itens (ficheiros e pastas) que precisa de sincronizar.](storage-sync-files-planning.md#recommended-system-resources)

    **Por favor, note:** O artigo anteriormente ligado apresenta uma tabela com uma gama para memória do servidor (RAM). Oriente para o grande número para o VM Azure. Pode orientar-se para o número menor para a sua máquina no local.

* [Saiba como implementar um VM Windows Sever.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Certifique-se de que o VM está implantado na mesma região de Azure que o aparelho virtual StorSimple 8020. Se como parte desta migração, também precisa de alterar a região dos seus dados em nuvem da região onde está armazenada hoje, pode fazê-lo mais tarde, quando fornecer ações de ficheiros Azure.

> [!IMPORTANT]
> Para otimizar o desempenho, implemente um **disco OS muito rápido** para o seu VM em nuvem. Irá armazenar a base de dados de sincronização no disco OS para todos os seus volumes de dados. Além disso, certifique-se de que cria um **grande disco de Os**. Dependendo do número de itens (ficheiros e pastas) nos seus volumes StorSimple, o disco OS pode necessitar de **várias centenas** de GiB de espaço para acomodar a base de dados de sincronização.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>Expor os volumes StorSimple 8020 ao Azure VM

Nesta fase, está a ligar um ou vários volumes StorSimple do aparelho virtual 8020 sobre o iSCSI ao VM do Servidor do Windows que forprovisionou.

> [!IMPORTANT]
> Para os seguintes artigos, complete apenas o **IP privado Get para o aparelho em nuvem** e ligue-se sobre as secções **iSCSI** e volte a este artigo.

1. [Obter o IP privado da aplicação da cloud](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Ligar-se ao iSCSI](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>Resumo da fase 2

Agora que completou a fase 2, tem: 

* Aprovisionado um VM do Windows Server na mesma região que o aparelho 8020 virtual StorSimple
* Expôs todos os volumes aplicáveis desde o 8020 até ao VM do Servidor do Windows sobre o iSCSI.
* Deverá agora ver o conteúdo do ficheiro e da pasta, quando utilizar o File Explorer no VM do servidor nos volumes montados.

Só proceda à fase 3 quando tiver concluído estes passos para todos os volumes que necessitam de migração.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>Fase 3: Configurar as ações de ficheiros do Azure e preparar-se para o Azure File Sync

:::row:::
    :::column:::
        ![Uma imagem que ilustra uma parte da imagem anterior e geral que ajuda a focar esta subsecção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        Nesta fase, irá determinar e fornecer uma série de partilhas de ficheiros Azure, criando um Windows Server no local como uma substituição de aparelho StorSimple e configurará esse servidor para O Sincronizado de Ficheiros Azure. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Mapeie os seus espaços de nome existentes para as ações de ficheiros Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Implementar ações de ficheiros Azure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Se precisa de alterar a região azure da região atual onde residem os seus dados StorSimple, então provisão das ações de ficheiro sinuoso da Nova Região que pretende utilizar. Determina a região selecionando-a quando disponibiliza as contas de armazenamento que detêm as suas ações de ficheiroS Azure. Certifique-se de que também o recurso Azure File Sync que irá fornecer abaixo, está na mesma nova região.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Implementar o recurso cloud Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Se precisa de alterar a região Azure da região atual onde residem os seus dados StorSimple, então disponibilizou as contas de armazenamento das suas ações de ficheiroS Azure na nova região. Certifique-se de que selecionou essa mesma região quando implementar este Serviço de Sincronização de Armazenamento.

### <a name="deploy-an-on-premises-windows-server"></a>Implementar um Servidor Windows no local

* Crie um Windows Server 2019 - no mínimo 2012R2 - como uma máquina virtual ou servidor físico. Um cluster de falha do Windows Server também é suportado. Não reutilize o servidor que poderá ter na frente do StorSimple 8100 ou 8600.
* Provision ou adicionar Armazenamento Direta Anexado (DAS em comparação com nas, que não é suportado).

É uma boa prática dar ao seu novo Windows Server uma quantidade igual ou maior de armazenamento do que o seu aparelho StorSimple 8100 ou 8600 tem disponível localmente para cache. Utilizará o Windows Server da mesma forma que utilizou o aparelho StorSimple, se tiver a mesma quantidade de armazenamento que o aparelho, então a experiência de cache deve ser semelhante, se não a mesma.
Pode adicionar ou remover o armazenamento do seu Servidor Windows à vontade. Isto permite-lhe escalar o seu volume local e a quantidade de armazenamento local disponível para cache.

### <a name="prepare-the-windows-server-for-file-sync"></a>Prepare o Servidor do Windows para sincronização de ficheiros

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Configure Sincronização de ficheiros Azure no Servidor windows

O Windows Server registado no local deve estar pronto e ligado à internet para este processo.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Certifique-se de ligar o nível de nuvem!** O tiering em nuvem é a funcionalidade AFS que permite ao servidor local ter menos capacidade de armazenamento do que está armazenado na nuvem, mas tem o espaço de nome completo disponível. Dados localmente interessantes também são cached localmente para um desempenho rápido e local de acesso. Outra razão para ligar o tiering da nuvem neste passo é que não queremos sincronizar o conteúdo dos ficheiros nesta fase, apenas o espaço de nome deve estar se movendo neste momento.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>Fase 4: Configure o VM Azure para sincronização

:::row:::
    :::column:::
        ![Uma imagem que ilustra uma parte da imagem anterior e geral que ajuda a focar esta subsecção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Esta fase diz respeito ao seu Azure VM com o iSCSI montado, primeiro volume clone(s). Durante esta fase, irá ligar o VM através do Azure File Sync e iniciar uma primeira ronda de ficheiros em movimento a partir do seu clone de volume StorSimple.
        
    :::column-end:::
:::row-end:::

Já configurou o seu servidor no local que substituirá o seu aparelho StorSimple 8100 ou 8600, para o Azure File Sync. 

Configurar o VM Azure é um processo quase idêntico, com um passo adicional. Os seguintes passos irão guiá-lo durante o processo.

> [!IMPORTANT]
> É importante que o Azure VM não esteja **configurado com o tiering em nuvem habilitado!** Trocará o volume deste servidor com clones de volume mais recentes ao longo da migração. O tiering em nuvem não tem qualquer benefício e a sobrecarga no uso do CPU deve evitar.

1. [Implante o agente da AFS. (ver secção anterior)](#prepare-the-windows-server-for-file-sync)
2. [Preparando o VM para o Azure File Sync.](#get-the-vm-ready-for-azure-file-sync)
3. [Configurar sincronização](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>Prepare o VM para o Azure File Sync

O Azure File Sync é utilizado para mover os ficheiros dos volumes storSimple do iSCSI montados para as partilhas de ficheiros do target Azure.
Durante este processo de migração, você vai montar vários clones de volume para o seu VM, sob a mesma letra de unidade. O Azure File Sync deve ser configurado para ver o próximo clone de volume que montou como uma versão mais recente dos ficheiros e pastas e atualizar as partilhas de ficheiros Azure ligadas através do Azure File Sync. 

> [!IMPORTANT]
> Para que isto funcione, deve ser definida uma chave de registo no servidor antes de configurar o Azure File Sync.

1. Crie um novo diretório sobre o acionamento do sistema do VM. As informações do Azure File Sync terão de ser percequidas lá em vez de nos clones de volume montados. Por exemplo: `"C:\syncmetadata"`
2. Abra o regedite e localize a seguinte colmeia de registo:`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. Criar uma nova chave de cadeia de tipo, nomeada: ***MetadataRootPath***
4. Desloque o caminho completo para o diretório que criou no volume do sistema, por exemplo:`C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Configure Sincronização de Ficheiros Azure no Azure VM

Este passo é semelhante à secção anterior, que discute a forma como configura o AFS no servidor no local.

A diferença é que não deve ativar o tiering de nuvem neste servidor e que precisa de se certificar de que as pastas certas estão ligadas às partilhas de ficheiros azure certas. Caso contrário, o nome de partilhas de ficheiros Azure e conteúdo de dados não corresponderá e não há forma de renomear os recursos da nuvem ou pastas locais sem reconfigurar a sincronização.

Consulte a secção anterior sobre como configurar o Sync de [Ficheiros Azure num Servidor windows](#configure-azure-file-sync-on-the-windows-server).

### <a name="step-4-summary"></a>Resumo do passo 4

Neste momento, terá configurado com sucesso o Azure File Sync no Azure VM que montou o seu clone de volume StorSimple através do iSCSI.
Os dados estão agora a fluir do Azure VM para as várias partilhas de ficheiros Azure e a partir daí aparece um espaço de nome totalmente cansado no seu Windows Server no local.

> [!IMPORTANT]
> Certifique-se de que não existem alterações ou acesso ao utilizador concedido ao Windows Server neste momento.

Os dados iniciais do clone de volume que se movem através do VM Azure para as ações de ficheiro sinuoso podem demorar muito tempo, potencialmente semanas. Estimar o tempo que isto vai demorar é complicado e depende de muitos fatores. Mais notavelmente a velocidade a que o Azure VM pode aceder a ficheiros nos volumes StorSimple e a rapidez com que o Azure File Sync pode processar os ficheiros e pastas que precisam de ser sincronizados. 

Por experiência própria, podemos assumir que a largura de banda - portanto o tamanho real dos dados - desempenha um papel subordinado. O tempo que esta ou qualquer ronda de migração subsequente levará depende principalmente do número de itens que podem ser processados por segundo. Assim, por exemplo, 1 TiB com 100.000 ficheiros e pastas provavelmente terminará mais lento que 1 TiB com apenas 50.000 ficheiros e pastas.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>Fase 5: Iterado através de múltiplos clones de volume

:::row:::
    :::column:::
        ![Uma imagem que ilustra uma parte da imagem anterior e geral que ajuda a focar esta subsecção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Como discutido na fase anterior, a sincronização inicial pode demorar muito tempo. Os seus utilizadores e aplicações ainda estão a aceder ao aparelho StorSimple 8100 ou 8600 no local. Isto significa que as mudanças estão a acumular-se, e a cada dia um delta maior entre os dados vivos e o clone de volume inicial, você está atualmente migrando, formas. Nesta secção, aprenderá a minimizar o tempo de inatividade usando vários clones de volume e informando quando a sincronização é feita.
    :::column-end:::
:::row-end:::

Infelizmente, o processo de migração não é instantâneo. Isto significa que o já referido delta dos dados ao vivo é uma consequência inevitável. A boa notícia é que pode repetir o processo de montagem de novos clones de volume. O delta de cada volume será progressivamente menor. Assim, eventualmente, uma sincronização terminará numa duração que considere aceitável para levar utilizadores e aplicações offline para cortar para o seu servidor Windows no local.

Repita os seguintes passos até que a sincronização complete com uma duração suficientemente rápida para que se sinta confortável levando utilizadores e aplicações offline:

1. [Determinar a sincronização está completa para um determinado clone de volume.](#determine-when-sync-is-done)
2. [Pegue num novo clone de volume e monte-o no aparelho virtual 8020.](#the-next-volume-clones)
3. [Determine quando a sincronização estiver feita.](#determine-when-sync-is-done)
4. [Estratégia de corte](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>O clone de volume seguinte(s)

Discutimos a tomada de um clone de volume no início deste artigo.
Esta fase tem duas ações:

1. [Pegue um clone de volume](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [Monte o clone de volume (ver acima)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>Determine quando a sincronização é feita

Quando a sincronização estiver feita, pode parar a sua medição de tempo e determinar se precisa de repetir o processo de pegar num clone de volume e montá-lo ou se a sincronização de tempo demorou com o último clone de volume era suficientemente pequena.

Para determinar a sincronização está completa:

1. Abra o Espectador de Eventos e navegue para **Aplicações e Serviços**
2. Navegar e abrir **microsoft\FileSync\Agent\Telemetria**
3. Procure o mais recente **evento 9102,** que corresponde a uma sessão de sincronização concluída
4. Selecione **Detalhes** e confirme que o valor **SyncDirection** é **Upload**
5. Verifique o **HResult** e confirme que mostra **0**. Isto significa que a sessão de sincronização foi bem sucedida. Se hresult é um valor não zero, então houve um erro durante a sincronização. Se o **PerItemErrorCount** for superior a 0, então alguns ficheiros ou pastas não sincronizaram corretamente. É possível ter um HResult de 0 mas um PerItemErrorCount que é maior que 0. Neste momento, não precisa de se preocupar com o PerItemErrorCount. Vamos apanhar estes ficheiros mais tarde. Se esta contagem de erros for significativa, milhares de itens, contacte o apoio ao cliente e peça para ser ligado ao grupo de produtos Azure File Sync para orientação direta sobre as melhores fases.
6. Verifique para ver vários eventos 9102 com HResult 0 seguidos. Isto indica que a sincronização está completa para este clone de volume.

### <a name="cut-over-strategy"></a>Estratégia de corte

1. Determine se a sincronização de um clone de volume é rápida o suficiente agora. (Delta pequeno o suficiente.)
2. Desative o aparelho StorSimple.
3. Um RoboCopy final.

Meça o tempo e determine se a sincronização de um clone de volume recente pode terminar dentro de uma janela de tempo suficientemente pequena, que pode pagar como tempo de inatividade no seu sistema.

Está na altura de desativar o acesso do utilizador ao aparelho StorSimple. Sem mais mudanças. O tempo de inatividade começou.
Tem de deixar o aparelho on-line e ligado, mas deve agora evitar alterações.

Na fase 6, você vai apanhar qualquer delta nos dados ao vivo desde o último clone de volume.

## <a name="phase-6-a-final-robocopy"></a>Fase 6: Uma RoboCopy final

Neste ponto, existem duas diferenças entre o seu Windows Server no local e o storSimple 8100 ou 8600:

1. Pode haver ficheiros que não tenham sincronizado (ver **PerItemErrors** do registo do evento acima)
2. O aparelho StorSimple tem uma cache povoada vs. O Servidor do Windows é apenas um espaço de nome sem conteúdo de ficheiro armazenado localmente neste momento.

![Uma imagem que ilustra uma parte da imagem anterior e geral que ajuda a focar esta subsecção do artigo.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

Podemos levar a cache do Windows Server até ao estado do aparelho e garantir que nenhum ficheiro é deixado para trás com um RoboCopy final.

> [!CAUTION]
> É imperativo que o comando RoboCopy que siga, seja exatamente como descrito abaixo. Só queremos copiar ficheiros que sejam locais e ficheiros que não tenham passado pela abordagem clone+sync de volume antes. Podemos resolver os problemas porque não se sincronizaram mais tarde, depois da migração estar completa. (Ver Tiroteio de [problemas do Ficheiro Azure](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing). É muito provável que personagens imprimíveis em nomes de ficheiros que não perca quando forem apagados.)

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
      Permite que o RoboCopy execute várias linhas. O padrão é 8, o máximo é 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Estado de saídas para log file como UNICODE (substitui o registo existente).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Saídas para a janela da consola. Utilizado em conjunto com a saída para um ficheiro de registo.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Executa o RoboCopy no mesmo modo que uma aplicação de reserva usaria. Permite ao RoboCopy mover ficheiros que o utilizador atual não tem permissões.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Permite que o RoboCopy apenas considere deltas entre fonte (storSimple) e target (diretório do Windows Server).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      fidelidade da cópia de ficheiro (predefinição é /COPY:DAT), cópias: D=Data, A=Atributos, T=Timestamps, S=Security=NTFS ACLs, O=Informação do Proprietário, Informação u=auditing
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      COPIAR TODAS as informações de ficheiro (equivalentea a /COPY:DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      fidelidade para a cópia de diretórios (predefinido é /DCOPY:DA), cópias bandeiras: D=Data, A=Atributos, T=Timestamps
   :::column-end:::
:::row-end:::

Deve executar este comando RoboCopy para cada um dos diretórios no Windows Server como alvo, que configuracom sincronização de ficheiros num ficheiro Azure.

Pode executar vários destes comandos em paralelo.
Uma vez concluída esta etapa RoboCopy, pode permitir que os seus utilizadores e aplicações acedam ao Windows Server como fizeram com o aparelho StorSimple antes.

Consulte os ficheiros de registo de robocopia para ver se os ficheiros foram deixados para trás. Se os problemas deverão existir, na maioria dos casos pode resolvê-los após a migração estar concluída e os seus utilizadores e aplicações foram realojados no seu Servidor Windows. Se precisar de resolver algum problema, faça-o antes da fase 7.

É provável que seja necessário criar as ações SMB no Windows Server que tinha nos dados StorSimple anteriormente. Pode carregar este passo frontalmente e fazê-lo mais cedo para não perder tempo aqui, mas tem de garantir que, antes deste ponto, não ocorram alterações nos ficheiros no servidor do Windows.

Se tiver uma implementação DFS-N, pode indicar os espaços DE Nomes DFN para os novos locais da pasta do servidor. Se não tiver uma implementação DFS-N e tiver apresentado o seu aparelho 8100 8600 localmente com um Servidor Windows, pode retirar esse servidor do domínio e juntar-se ao seu novo Servidor Windows com AFS no domínio, dar-lhe o mesmo nome de servidor que o servidor antigo , e os mesmos nomes de partilha, em seguida, o cut-over para o novo servidor permanece transparente para os seus utilizadores, política de grupo ou scripts.

## <a name="phase-7-deprovision"></a>Fase 7: Desprovisionamento

Durante a última fase, tem eidiado através de vários clones de volume, e acabou por conseguir cortar o acesso do utilizador ao novo Servidor Windows depois de o ter desligado do aparelho StorSimple.

Pode agora começar a desfornecer recursos desnecessários.
Antes de começar, é uma boa prática observar a sua nova implementação de Sincronização de Ficheiros Azure em produção, por um tempo. Isso dá-lhe opções para resolver quaisquer problemas que possa encontrar.

Uma vez satisfeito e observado a sua implantação aFS por pelo menos alguns dias, você pode começar a desprovisionar recursos por esta ordem:

1. Desligue o VM Azure que usamos para mover dados dos clones de volume para as ações de ficheiros Azure através de sincronização de ficheiros.
2. Vá ao seu recurso de Serviço de Sincronização de Armazenamento em Azure e desregilhe o Azure VM. Isso retira-o de todos os grupos de sincronização.

    > [!WARNING]
    > **CERTIFIQUE-SE de que escolhe a máquina certa.** Desligou a nuvem VM, o que significa que deve mostrar-se como o único servidor offline na lista de servidores registados. Não deve escolher o Windows Server no local neste momento, o que o fará não o registará.

3. Eliminar a VM Azure e os seus recursos.
4. Desative o aparelho 8020 Virtual StorSimple.
5. Desprovisionamento de todos os recursos StorSimple no Azure.
6. Desligue o aparelho físico StorSimple do seu centro de dados.

A sua migração está completa.

## <a name="next-steps"></a>Passos seguintes

Conheça mais o Azure File Sync. Especialmente com a flexibilidade das políticas de tiering em nuvem.

Se vir no portal Azure, ou dos eventos anteriores, que alguns ficheiros não estão permanentemente sincronizados, reveja o guia de resolução de problemas para tomar medidas para resolver estes problemas.

* [Visão geral do Sincronizado de Ficheiros Azure: aka.ms/AFS](https://aka.ms/AFS)
* [Tiering de nuvem](storage-sync-cloud-tiering.md) 
* [Guia de resolução de problemas de Sincronização de Ficheiros Azure](storage-sync-files-troubleshoot.md)
