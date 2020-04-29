---
title: StorSimple 1200 migração para O Sincronizado de Ficheiros Azure
description: Aprenda a migrar um aparelho virtual da série StorSimple 1200 para o Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 69225da1506ced879363b10b098d939df93cbfba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502362"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>StorSimple 1200 migração para O Sincronizado de Ficheiros Azure

A série StorSimple 1200 é um aparelho virtual que é executado num centro de dados no local. É possível migrar os dados deste aparelho para um ambiente De sincronização de ficheiros Azure. O Azure File Sync é o serviço Azure de longo prazo padrão e estratégico para o quais os aparelhos StorSimple podem ser migrados.

A série StorSimple 1200 chegará ao fim [da vida](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) em dezembro de 2022.  É importante começar a planear a sua migração o mais rápido possível. Este artigo fornece os conhecimentos de fundo necessários e etapas de migração para uma migração bem sucedida para O Ficheiro Sincronizado Azure. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> A Microsoft está empenhada em ajudar os clientes na sua migração. Envie AzureFilesMigration@microsoft um e-mail .com para um plano de migração personalizado, bem como assistência durante a migração.

O Azure File Sync é um serviço na nuvem da Microsoft, baseado em dois componentes principais:

* Sincronização de ficheiros e tiering em nuvem.
* As ações de arquivo como armazenamento nativo em Azure, que podem ser acedidas ao longo de vários protocolos como SMB e file REST. Uma partilha de ficheiros Azure é comparável a uma partilha de ficheiros num Windows Server, que pode montar de forma nativa como uma unidade de rede. Suporta aspetos importantes da fidelidade do ficheiro, como atributos, permissões e carimbos de tempo. Ao contrário do StorSimple, não é necessária nenhuma aplicação/serviço para interpretar os ficheiros e pastas armazenados na nuvem. A abordagem ideal, e mais flexível para armazenar dados do servidor de ficheiros de propósito geral, bem como alguns dados de aplicação, na nuvem.

Este artigo centra-se nos passos da migração. Se antes de migrar, gostaria de saber mais sobre o Azure File Sync, recomendamos os seguintes artigos:

* [Sincronização de ficheiros Azure - visão geral](https://aka.ms/AFS "Descrição geral")
* [Sincronização de Ficheiros Azure - guia de implementação](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Objetivos de migração

O objetivo é garantir a integridade dos dados de produção, bem como garantir a disponibilidade. Este último requer manter o tempo de paragem ao mínimo, de modo a que possa encaixar ou apenas exceder ligeiramente as janelas de manutenção regulares.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>Caminho de migração StorSimple 1200 para O Sincronizado de Ficheiros Azure

Um Servidor Windows local é necessário para executar um agente Desincronização de Ficheiros Azure. O Windows Server pode estar no mínimo um servidor 2012R2, mas idealmente é um Windows Server 2019.

Existem numerosas vias de migração alternativas e criaria demasiado tempo de um artigo para documentá-los a todos e ilustrar por que razão eles têm riscos ou desvantagens sobre a rota que recomendamos como uma melhor prática neste artigo.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Visão geral da rota de migração dos passos abaixo neste artigo.":::

A imagem anterior retrata passos que correspondem a secções neste artigo.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>Passo 1: Fornecer o seu Servidor e armazenamento no local do Windows

1. Crie um Windows Server 2019 - no mínimo 2012R2 - como uma máquina virtual ou servidor físico. Um cluster de falha do Windows Server também é suportado.
2. Provision ou adicionar Armazenamento Direta Anexado (DAS em comparação com nas, que não é suportado). O tamanho do armazenamento do Windows Server deve ser igual ou maior do que o tamanho da capacidade disponível do seu aparelho Virtual StorSimple 1200.

### <a name="step-2-configure-your-windows-server-storage"></a>Passo 2: Configure o armazenamento do Servidor do Windows

Neste passo, mapeia a sua estrutura de armazenamento StorSimple (volumes e partilhas) para a sua estrutura de armazenamento do Windows Server.
Se planeia fazer alterações na sua estrutura de armazenamento, ou seja, o número de volumes, a associação de pastas de dados em volumes, ou a estrutura da subpasta acima ou abaixo das suas atuais ações SMB/NFS, então agora é a altura de tomar em consideração estas alterações.
Alterar a estrutura de ficheiros e pastas depois de o Azure File Sync estar configurado, é complicado e deve ser evitado.
Este artigo assume que está a mapear 1:1, por isso deve ter em consideração as suas alterações de mapeamento quando seguir os passos deste artigo.

* Nenhum dos seus dados de produção deve acabar no volume do sistema Do Servidor do Windows. O tiering em nuvem não é suportado nos volumes do sistema. No entanto, esta funcionalidade é necessária para a migração, bem como operações contínuas como uma substituição StorSimple.
* Disponibilize o mesmo número de volumes no seu Servidor Windows que tem no seu aparelho virtual StorSimple 1200.
* Configure quaisquer funções, funcionalidades e definições do Windows Server de que necessite. Recomendamos que opte pelas atualizações do Windows Server para manter o seu SISTEMA seguro e atualizado. Da mesma forma, recomendamos que opte pelo Microsoft Update para manter as aplicações da Microsoft atualizadas, incluindo o agente DoSS de Ficheiros Azure.
* Não configure quaisquer pastas ou partilhas antes de ler os seguintes passos.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>Passo 3: Implementar o primeiro recurso em nuvem Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>Passo 4: Combine o volume local e a estrutura de pastas com os recursos de partilha de ficheiros Azure File Sync e Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>Passo 5: Provisão de ações de ficheiros Azure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>Passo 6: Configurar as pastas-alvo do Servidor do Windows

Em etapas anteriores, já considerou todos os aspetos que determinarão os componentes das suas topoologias de sincronização. Chegou a hora de preparar o servidor para receber ficheiros para upload.

Crie **todas as** pastas que sincronizarão cada uma com a sua própria partilha de ficheiros Azure.
É importante que siga a estrutura da pasta que documentou anteriormente. Se, por exemplo, decidiu sincronizar várias ações locais de SMB numa única partilha de ficheiros Azure, então precisa colocá-las sob uma pasta de raiz comum no volume. Crie agora esta pasta-raiz-alvo no volume.

O número de ações de ficheiros Azure que disponibilizou deve corresponder ao número de pastas que criou neste passo + o número de volumes que irá sincronizar ao nível da raiz.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>Passo 7: Implementar o agente Dessincronização de Ficheiros Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>Passo 8: Configurar a sincronização

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Certifique-se de ligar o nível de nuvem!** Isto é necessário se o seu servidor local não tiver espaço suficiente para armazenar o tamanho total dos seus dados no armazenamento em nuvem StorSimple. Desnove a sua política de tiering, temporariamente para a migração, para 99% de espaço livre de volume.

Repita os passos de criação de grupo sincronizado e a adição da pasta do servidor correspondente como ponto final do servidor para todas as partilhas de ficheiros Do Azure/localizações do servidor, que precisam de ser configuradas para sincronização.

### <a name="step-9-copy-your-files"></a>Passo 9: Copiar os seus ficheiros

A abordagem de migração básica é um RoboCopy do seu aparelho virtual StorSimple para o seu Servidor Windows, e o Azure File Sync para as partilhas de ficheiros Azure.

Execute a primeira cópia local para a sua pasta de alvo do Windows Server:

* Identifique a primeira localização do seu aparelho Virtual StorSimple.
* Identifique a pasta correspondente no Servidor do Windows, que já tem o Azure File Sync configurado no mesmo.
* Inicie a cópia usando RoboCopy

O seguinte comando RoboCopy recordará ficheiros do seu armazenamento StorSimple Azure para o seu StorSimple local e, em seguida, transferi-los para a pasta-alvo do Servidor do Windows. O Windows Server irá sincronizá-lo com as partilhas de ficheiros Azure. À medida que o volume do Windows Server local fica cheio, o tiering em nuvem irá entrar e os ficheiros de nível que já sincronizaram com sucesso. O tiering em nuvem gerará espaço suficiente para continuar a cópia do aparelho virtual StorSimple. O tiering de nuvem verifica uma vez por hora para ver o que se sincronizou e libertar espaço em disco para alcançar o espaço livre de 99% de volume.

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
      Permite executar este comando RoboCopy várias vezes, sequencialmente no mesmo alvo/destino. Identifica o que foi copiado antes e omite-o. Apenas alterações, adições e "*exclusões*" serão processadas, o que ocorreu desde a última execução. Se o comando não foi executado antes, nada é omitido. Esta é uma excelente opção para locais de origem que ainda são ativamente utilizados e alterados.
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

Quando executa o comando RoboCopy pela primeira vez, os seus utilizadores e aplicações ainda estão a aceder aos ficheiros e pastas StorSimple e potencialmente alterá-lo. É possível que a RoboCopy tenha processado um diretório, passapara o seguinte e, em seguida, um utilizador na localização de origem (StorSimple) adiciona, altera ou elimina um ficheiro que agora não será processado nesta execução roboCopy atual. Não faz mal.

A primeira corrida é sobre mover a maior parte dos dados de volta para o local, para o seu Servidor Windows e fazer backup para a nuvem através do Azure File Sync. Isto pode demorar muito tempo, dependendo de:

* sua largura de banda de download
* a velocidade de recuperação do serviço de nuvem StorSimple
* a largura de banda de carregamento
* o número de itens (ficheiros e pastas), que precisam de ser processados por qualquer um dos serviços

Uma vez concluída a execução inicial, volte a executar o comando.

A segunda vez terminará mais rápido, porque só precisa de transportar mudanças que aconteceram desde a última corrida. Essas mudanças são provavelmente locais para o StorSimple já, porque são recentes. Isto reduz ainda mais o tempo porque a necessidade de retirada da nuvem é reduzida. Durante esta segunda corrida, ainda assim, novas mudanças podem acumular-se.

Repita este processo até estar convencido de que o tempo que demora a concluir é um tempo de inatividade aceitável.

Quando considerar o tempo de inatividade aceitável e estiver preparado para desligar a localização StorSimple, faça-o agora: Por exemplo, remova a parte SMB para que nenhum utilizador possa aceder à pasta ou dar qualquer outro passo apropriado que impeça que o conteúdo mude nesta pasta no StorSimple.

Executar uma última rodada roboCopy. Isto vai captar quaisquer alterações que possam ter sido perdidas.
O tempo que este último passo demora, depende da velocidade da varredura RoboCopy. Pode estimar o tempo (que é igual ao seu tempo de inatividade) medindo o tempo que o percurso anterior demorou.

Crie uma parte na pasta Do Servidor do Windows e, possivelmente, ajuste a sua implementação DFS-N para a indicar. Certifique-se de que define as mesmas permissões de nível de partilha que na sua quota StorSimple SMB.

Terminou de migrar uma parte/grupo de ações para uma raiz ou volume comum. (Dependendo do que mapeou e decidiu que precisava entrar na mesma parte de ficheiros Azure.)

Pode tentar executar algumas destas cópias em paralelo. Recomendamos o processamento do âmbito de uma partilha de ficheiros Azure de cada vez.

> [!WARNING]
> Uma vez que tenha movido todos os dados do seu StorSimple para o Windows Server, e a sua migração esteja completa: Volte a ***todos os*** grupos de sincronização no portal Azure e ajuste o valor de volume de volume sem volume de volume em nuvem para algo mais adequado para a utilização do cache, digamos 20%. 

A política de espaço livre de volume de volume de nível de nuvem atua a um nível de volume com pontos finais de servidor potencialmente múltiplos sincronizados a partir dele. Se se esquecer de ajustar o espaço livre num mesmo ponto final do servidor, o sync continuará a aplicar a regra mais restritiva e tentará manter 99% de espaço livre no disco, fazendo com que a cache local não esteja a funcionar como seria de esperar. A menos que o seu objetivo seja apenas ter o espaço de nome para um volume que apenas contém dados de arquivo raramente acedidos.

## <a name="troubleshoot"></a>Resolução de problemas

O problema mais provável é que o comando RoboCopy falha com *"Volume cheio"* no lado do Servidor do Windows. Se for esse o caso, então a sua velocidade de descarregamento é provavelmente melhor do que a sua velocidade de carregamento. O tiering em nuvem atua uma vez por hora para evacuar o conteúdo do disco local do Windows Server, que tem sincronizado.

Deixe sincronizar o progresso e o tiering de nuvem liberte o espaço do disco. Pode observar isso no File Explorer no seu Servidor Windows.

Quando o seu Servidor Windows tiver capacidade disponível suficiente, a reexecução do comando resolverá o problema. Nada quebra quando se entra nesta situação e pode seguir em frente com confiança. O inconveniente de voltar a dirigir o comando é a única consequência.

Também pode encontrar outros problemas de Sincronização de Ficheiros Azure.
Por mais improvável que sejam, se isso acontecer, dê uma olhada no guia de resolução de problemas link **Azure File Sync**.

## <a name="relevant-links"></a>Links relevantes

Conteúdo migratório:

* [Guia de migração da série StorSimple 8000](storage-files-migration-storsimple-8000.md)

Conteúdo de Sincronização de Ficheiros Azure:

* [Visão geral da AFS](https://aka.ms/AFS)
* [Guia de implantação da AFS](storage-files-deployment-guide.md)
* [Resolução de problemas da AFS](storage-sync-files-troubleshoot.md)
