---
title: StorSimple migração 1200 para Azure File Sync
description: Saiba como migrar um aparelho virtual da série StorSimple 1200 para Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 78c7953ef6432d37542a7a8b06f226a07f2b701f
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630486"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>StorSimple migração 1200 para Azure File Sync

A série StorSimple 1200 é um aparelho virtual que é executado num centro de dados no local. É possível migrar os dados deste aparelho para um ambiente Azure File Sync. Azure File Sync é o serviço Azure de longo prazo padrão e estratégico para o qual os aparelhos StorSimple podem ser migrados.

A série StorSimple 1200 chegará ao seu [fim de vida](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) em dezembro de 2022.  É importante começar a planear a sua migração o mais rápido possível. Este artigo fornece os conhecimentos de fundo necessários e passos de migração para uma migração bem sucedida para Azure File Sync. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> A Microsoft está empenhada em ajudar os clientes na sua migração. O AzureFilesMigration@microsoft e-mail .com para um plano de migração personalizado, bem como assistência durante a migração.

Azure File Sync é um serviço de nuvem da Microsoft, baseado em dois componentes principais:

* Sincronização de ficheiros e tiering de nuvem.
* Ações de ficheiros como armazenamento nativo em Azure, que podem ser acedidas em vários protocolos como SMB e FILE REST. Uma partilha de ficheiros Azure é comparável a uma partilha de ficheiros num Servidor do Windows, que pode montar de forma nativa como uma unidade de rede. Suporta importantes aspetos de fidelidade de ficheiros como atributos, permissões e timetamps. Ao contrário do StorSimple, não é necessária nenhuma aplicação/serviço para interpretar os ficheiros e pastas armazenados na nuvem. A abordagem ideal e mais flexível para armazenar dados gerais do servidor de ficheiros de propósito, bem como alguns dados da aplicação, na nuvem.

Este artigo centra-se nos passos de migração. Se antes de migrar gostaria de saber mais sobre o Azure File Sync, recomendamos os seguintes artigos:

* [Azure File Sync - visão geral](./storage-sync-files-planning.md "Descrição Geral")
* [Azure File Sync - guia de implementação](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Objetivos de migração

O objetivo é garantir a integridade dos dados de produção, bem como garantir a disponibilidade. Este último requer manter o tempo de inatividade ao mínimo, de modo a que possa encaixar ou apenas exceder ligeiramente as janelas de manutenção regulares.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>StorSimple caminho de migração 1200 para Azure File Sync

Um servidor local do Windows é necessário para executar um agente Azure File Sync. O Windows Server pode ser no mínimo um servidor 2012R2, mas idealmente é um Windows Server 2019.

Existem numerosas vias de migração alternativas e criaria demasiado tempo de um artigo para documentar todos eles e ilustrar por que razão carregam riscos ou desvantagens ao longo da rota que recomendamos como uma boa prática neste artigo.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Visão geral da rota de migração dos passos mais abaixo neste artigo.":::

A imagem anterior retrata passos que correspondem a secções neste artigo.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>Passo 1: Provisione o servidor e armazenamento do Windows No local

1. Crie um Windows Server 2019 - no mínimo 2012R2 - como uma máquina virtual ou servidor físico. Um cluster de falha do Windows Server também é suportado.
2. Provisão ou adicionar Armazenamento Direto Anexado (DAS em comparação com o NAS, que não é suportado). O tamanho do armazenamento do Windows Server deve ser igual ou superior ao tamanho da capacidade disponível do seu aparelho virtual StorSimple 1200.

### <a name="step-2-configure-your-windows-server-storage"></a>Passo 2: Configurar o armazenamento do Windows Server

Neste passo, mapeia a sua estrutura de armazenamento StorSimple (volumes e partilhas) para a sua estrutura de armazenamento do Windows Server.
Se planeia fazer alterações na sua estrutura de armazenamento, ou seja, o número de volumes, a associação de pastas de dados a volumes, ou a estrutura de sub-dobradores acima ou abaixo das atuais ações SMB/NFS, então agora é a altura de tomar em consideração estas alterações.
Alterar a estrutura do ficheiro e da pasta depois de configurado O Azure File Sync é complicado, é complicado e deve ser evitado.
Este artigo pressupõe que está a mapear 1:1, por isso deve ter em conta as alterações de mapeamento quando seguir os passos deste artigo.

* Nenhum dos seus dados de produção deve acabar no volume do sistema Do Windows Server. O tiering da nuvem não é suportado nos volumes do sistema. No entanto, esta funcionalidade é necessária para a migração, bem como operações contínuas como uma substituição StorSimple.
* Forcam o mesmo número de volumes no seu Servidor Windows que tem no seu aparelho virtual StorSimple 1200.
* Configure quaisquer funções, funcionalidades e definições do Windows Server que necessite. Recomendamos que opte pelas atualizações do Windows Server para manter o seu SISTEMA seguro e atualizado. Da mesma forma, recomendamos optar pelo Microsoft Update para manter as aplicações da Microsoft atualizadas, incluindo o agente Azure File Sync.
* Não configuure nenhuma pasta ou partilha antes de ler os seguintes passos.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>Passo 3: Implementar o primeiro recurso em nuvem Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>Passo 4: Combine o volume local e a estrutura da pasta com os recursos de partilha de ficheiros Azure File Sync e Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>Passo 5: Provision Azure file shares

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>Passo 6: Configurar as pastas alvo do Servidor do Windows

Em etapas anteriores, considerou todos os aspetos que determinarão os componentes das suas topologias de sincronização. Chegou a hora de preparar o servidor para receber ficheiros para o upload.

Crie **todas as** pastas que sincronizam cada uma com a sua própria partilha de ficheiros Azure.
É importante que siga a estrutura da pasta que documentou anteriormente. Se, por exemplo, decidiu sincronizar múltiplas partilhas locais de SMB numa única partilha de ficheiros Azure, então precisa colocá-las sob uma pasta de raiz comum no volume. Crie agora esta pasta raiz do alvo no volume.

O número de ações de ficheiros Azure que forneceste deve corresponder ao número de pastas que criaste neste passo + o número de volumes que irá sincronizar ao nível da raiz.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>Passo 7: Implementar o agente Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>Passo 8: Configutura sincronização

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Certifique-se de ligar o nível da nuvem!** Isto é necessário se o seu servidor local não tiver espaço suficiente para armazenar o tamanho total dos seus dados no armazenamento em nuvem StorSimple. Desaça a sua política de tiering, temporariamente para a migração, para 99% de espaço livre de volume.

Repita os passos da criação do grupo de sincronização e a adição da pasta do servidor correspondente como ponto final do servidor para todas as partilhas de ficheiros /localizações do servidor Azure, que precisam de ser configuradas para sincronização.

### <a name="step-9-copy-your-files"></a>Passo 9: Copiar os seus ficheiros

A abordagem básica de migração é um RoboCopy do seu aparelho virtual StorSimple para o seu Windows Server e Azure File Sync para ações de ficheiros Azure.

Execute a primeira cópia local para a pasta alvo do Windows Server:

* Identifique a primeira localização do seu aparelho StorSimple virtual.
* Identifique a pasta correspondente no Servidor do Windows, que já tem O Azure File Sync configurado no mesmo.
* Inicie a cópia usando RoboCopy

O seguinte comando RoboCopy irá relembr o ficheiro do seu armazenamento StorSimple Azure para o seu StorSimple local e, em seguida, transferi-los para a pasta alvo do Windows Server. O Servidor do Windows sincroniza-o com a partilha de ficheiros Azure. À medida que o volume do Servidor do Windows local fica cheio, o tiering da nuvem entrará em edição de ficheiros que já tenham sincronizado com sucesso. O tiering em nuvem gerará espaço suficiente para continuar a cópia do aparelho virtual StorSimple. O tiering da nuvem verifica uma vez por hora para ver o que sincronizou e para libertar o espaço do disco para alcançar o espaço livre de volume de 99%.

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
      Permite executar este comando RoboCopy várias vezes, sequencialmente no mesmo alvo/destino. Identifica o que já foi copiado antes e omite-o. Apenas alterações, adições e " eliminações " serão *processadas,* que ocorreram desde a última execução. Se o comando não foi dirigido antes, nada é omitido. Esta é uma excelente opção para locais de origem que ainda são usados e alterados ativamente.
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

Quando executam o comando RoboCopy pela primeira vez, os seus utilizadores e aplicações ainda estão a aceder aos ficheiros e pastas StorSimple e potencialmente alterá-lo. É possível que a RoboCopy tenha processado um diretório, passa para o seguinte e, em seguida, um utilizador na localização de origem (StorSimple) adiciona, altera ou elimina um ficheiro que agora não será processado nesta atual execução do RoboCopy. Não faz mal.

A primeira execução é sobre mover a maior parte dos dados de volta para as instalações, para o seu Windows Server e backup para a nuvem via Azure File Sync. Isto pode demorar muito tempo, dependendo de:

* sua largura de banda de descarregamento
* a velocidade de recuperação do serviço de nuvem StorSimple
* a largura de banda de upload
* o número de itens (ficheiros e pastas), que precisam de ser processados por qualquer um dos serviços

Uma vez concluída a execução inicial, volte a executar o comando.

A segunda vez terminará mais rápido, porque só precisa de transportar mudanças que aconteceram desde a última corrida. Essas mudanças são provavelmente locais para o StorSimple já, porque são recentes. Isso está a reduzir ainda mais o tempo, porque a necessidade de se recuperar da nuvem é reduzida. Durante esta segunda execução, ainda assim, novas mudanças podem acumular-se.

Repita este processo até que esteja convencido de que o tempo que leva para ser concluído é um tempo aceitável de paragem.

Quando considerar o tempo de inatividade aceitável e estiver preparado para desligar a localização StorSimple, faça-o agora: Por exemplo, remova a partilha SMB para que nenhum utilizador possa aceder à pasta ou dar qualquer outro passo apropriado que impeça que o conteúdo mude nesta pasta no StorSimple.

Executar uma última rodada de RoboCopy. Isto vai detetar quaisquer alterações, que podem ter sido perdidas.
Quanto tempo este passo final leva, depende da velocidade do scan RoboCopy. Pode estimar o tempo (que é igual ao seu tempo de inatividade) medindo o tempo que a execução anterior demorou.

Crie uma partilha na pasta do Servidor do Windows e, possivelmente, ajuste a sua implementação DFS-N para a apontar. Certifique-se de definir as mesmas permissões de nível de partilha que na sua ação StorSimple SMB.

Terminou de migrar uma parte/grupo de ações numa raiz ou volume comum. (Dependendo do que mapeou e decidiu que precisava entrar na mesma partilha de ficheiros Azure.)

Podes tentar executar algumas destas cópias em paralelo. Recomendamos o processamento do âmbito de uma partilha de ficheiros Azure de cada vez.

> [!WARNING]
> Depois de ter movido todos os dados do seu StorSimple para o Windows Server, e a sua migração está completa: Volte a * **todos os** grupos de sincronização no portal Azure e ajuste o valor de espaço livre de volume de nível de nuvem para algo mais adequado para a utilização da cache, digamos 20%. 

A política de espaço livre de nível de nivelamento da nuvem atua a nível de volume com pontos finais potencialmente múltiplos do servidor sincronizados a partir dele. Se se esquecer de ajustar o espaço livre num ponto final de um servidor, a sincronização continuará a aplicar a regra mais restritiva e tentará manter 99% de espaço livre em disco, fazendo com que a cache local não esteja a funcionar como seria de esperar. A menos que o seu objetivo seja ter apenas o espaço de nome para um volume que apenas contém dados de arquivo raramente acedidos.

## <a name="troubleshoot"></a>Resolução de problemas

O problema mais provável é que o comando RoboCopy falhe com _"Volume cheio"* no lado do Windows Server. Se for esse o caso, a sua velocidade de descarregamento é provavelmente melhor do que a velocidade de carregamento. O nível da nuvem atua uma vez a cada hora para evacuar o conteúdo do disco local do Windows Server, que foi sincronizado.

Deixe sincronizar o progresso e o tiering da nuvem libertar o espaço do disco. Pode observar isso no Explorador de Ficheiros no seu Servidor windows.

Quando o seu Servidor do Windows tiver capacidade disponível suficiente, a reexecução do comando resolverá o problema. Nada se rompe quando se entra nesta situação e se pode avançar com confiança. O inconveniente de voltar a comandar o comando é a única consequência.

Também pode encontrar outros problemas do Azure File Sync.
Por muito improváveis que sejam, se isso acontecer, dê uma olhada no **guia de resolução de problemas do LINK Azure File Sync**.

## <a name="relevant-links"></a>Ligações relevantes

Conteúdo de migração:

* [Guia de migração da série StorSimple 8000](storage-files-migration-storsimple-8000.md)

Conteúdo de Azure File Sync:

* [Visão geral da AFS](./storage-sync-files-planning.md)
* [Guia de implantação da AFS](storage-files-deployment-guide.md)
* [Resolução de problemas da AFS](storage-sync-files-troubleshoot.md)