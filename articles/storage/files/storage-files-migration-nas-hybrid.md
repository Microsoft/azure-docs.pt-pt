---
title: No local migração da NAS para O Sincronizado de Ficheiros Azure
description: Saiba como migrar ficheiros de uma localização de armazenamento anexado (NAS) da rede no local para uma implantação híbrida em nuvem com partilhas de ficheiros Azure File Sync e Azure.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7b0c7a30580d3863a78e85b8b45287a598bbf394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247355"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migrar da rede anexado ao armazenamento (NAS) para uma implantação híbrida em nuvem com O Sincronizado de Ficheiros Azure

O Azure File Sync trabalha em localizações de armazenamento direta anexado (DAS) e não suporta sincronização com as localizações de Armazenamento Anexado à Rede (NAS).
Este facto torna necessária uma migração dos seus ficheiros e este artigo guia-o através do planeamento e execução de tal migração.

## <a name="migration-goals"></a>Objetivos de migração

O objetivo é transferir as ações que tem no seu aparelho NAS para um Servidor Windows. Em seguida, utilize o Azure File Sync para uma implantação híbrida em nuvem. Esta migração tem de ser feita de forma a garantir a integridade dos dados de produção, bem como a disponibilidade durante a migração. Este último requer manter o tempo de paragem ao mínimo, de modo a que possa encaixar ou apenas exceder ligeiramente as janelas de manutenção regulares.

## <a name="migration-overview"></a>Descrição geral da migração

Como referido no artigo de visão geral da [migração](storage-files-migration-overview.md)dos Ficheiros Azure, é importante utilizar a ferramenta e abordagem corretas. O seu aparelho NAS está a expor as ações da SMB diretamente na sua rede local. RoboCopy, incorporado no Windows Server, é a melhor forma de mover os seus ficheiros neste cenário de migração.

- Fase 1: [Identifique quantas ações de ficheiro Saque Iaazul de que precisa](#phase-1-identify-how-many-azure-file-shares-you-need)
- Fase 2: [Fornecer um servidor windows adequado no local](#phase-2-provision-a-suitable-windows-server-on-premises)
- Fase 3: Implementar o recurso cloud Da nuvem De sincronização de [ficheiros Azure](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- Fase 4: Implantar recursos de [armazenamento azure](#phase-4-deploy-azure-storage-resources)
- Fase 5: [Implementar o agente Dessincronização de Ficheiros Azure](#phase-5-deploy-the-azure-file-sync-agent)
- Fase 6: [Configurar sincronização de ficheiros Azure no Servidor windows](#phase-6-configure-azure-file-sync-on-the-windows-server)
- Fase 7: [RoboCopy](#phase-7-robocopy)
- Fase 8: [Recorte do utilizador](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: Identifique quantas ações de ficheiro Saque Iaazul de que precisa

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>Fase 2: Fornecer um servidor windows adequado no local

* Crie um Windows Server 2019 - no mínimo 2012R2 - como uma máquina virtual ou servidor físico. Um cluster de falha do Windows Server também é suportado.
* Provision ou adicionar Armazenamento Direta Anexado (DAS em comparação com nas, que não é suportado).

    A quantidade de armazenamento que disponibiliza pode ser menor do que a que está a utilizar atualmente no seu aparelho NAS, se utilizar a função de [tiering](storage-sync-cloud-tiering.md) em nuvem Azure File Syncs.
    No entanto, quando copiar os seus ficheiros do espaço NAS maior para o menor volume do Windows Server numa fase posterior, terá de trabalhar em lotes:

    1. Mova um conjunto de ficheiros que se encaixe minem no disco
    2. deixar sincronização de ficheiros e tiering nuvem envolver
    3. quando for criado mais espaço livre no volume, proceda ao próximo lote de ficheiros. 
    
    Pode evitar esta abordagem de loteamento, disponibilizando o espaço equivalente no Windows Server que os seus ficheiros ocupam no aparelho NAS. Considere a duplicação no NAS/Windows. Se não quiser comprometer permanentemente esta elevada quantidade de armazenamento no seu Servidor Windows, pode reduzir o tamanho do volume após a migração e antes de ajustar as políticas de tiering em nuvem. Isso cria uma cache menor no local das suas ações de ficheiros Azure.

A configuração de recursos (computação e RAM) do Servidor windows que implementa depende principalmente do número de itens (ficheiros e pastas) que estará a sincronizar. Recomendamos que vá com uma configuração de desempenho mais elevada se tiver alguma preocupação.

[Saiba como dimensionar um Servidor do Windows com base no número de itens (ficheiros e pastas) que precisa de sincronizar.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> O artigo anteriormente ligado apresenta uma tabela com uma gama para memória do servidor (RAM). Pode orientar-se para o número menor para o seu servidor, mas esperar que a sincronização inicial possa demorar significativamente mais tempo.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fase 3: Implementar o recurso cloud Da nuvem De sincronização de ficheiros Azure

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fase 4: Implantar recursos de armazenamento azure

Nesta fase, consulte a tabela de mapeamento da Fase 1 e utilize-a para fornecer o número correto de contas de armazenamento Azure e ações de arquivo dentro delas.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fase 5: Implementar o agente Dessincronização de Ficheiros Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>Fase 6: Configurar sincronização de ficheiros Azure no Servidor windows

O Windows Server registado no local deve estar pronto e ligado à internet para este processo.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> O tiering em nuvem é a funcionalidade AFS que permite ao servidor local ter menos capacidade de armazenamento do que está armazenado na nuvem, mas tem o espaço de nome completo disponível. Dados interessantes localmente também são cached localmente para desempenho de acesso rápido. O tiering em nuvem é uma funcionalidade opcional por Azure File Sync "ponto final do servidor".

> [!WARNING]
> Se for resumido menos armazenamento no volume ou s do seu servidor Windows do que os dados utilizados no aparelho NAS, então o tiering em nuvem é obrigatório. Se não ligar o tiering da nuvem, o seu servidor não libertará espaço para armazenar todos os ficheiros. Desnove a sua política de tiering, temporariamente para a migração, para 99% de espaço livre de volume. Certifique-se de que volta às definições de nível de nuvem após a migração estar completa e coloque-a num nível útil mais longo a longo prazo.

Repita os passos de criação de grupo sincronizado e a adição da pasta do servidor correspondente como ponto final do servidor para todas as partilhas de ficheiros Do Azure/localizações do servidor, que precisam de ser configuradas para sincronização.

Após a criação de todos os pontos finais do servidor, a sincronização está a funcionar. Pode criar um ficheiro de teste e vê-lo sincronizar desde a localização do servidor até à partilha de ficheiros Azure conectada (conforme descrito pelo ponto final da nuvem no grupo de sincronização).

Ambas as localizações, as pastas do servidor e as partilhas de ficheiros Azure estão vazias e aguardam dados em qualquer dos locais. No próximo passo, começará a copiar ficheiros no Windows Server para O Sincronizado de Ficheiros Azure para os mover para a nuvem. Caso tenha ativado o tiering da nuvem, o servidor começará a tierar ficheiros, caso se esgote a capacidade no volume local.

## <a name="phase-7-robocopy"></a>Fase 7: RoboCopy

A abordagem de migração básica é um RoboCopy do seu aparelho NAS para o seu Servidor Windows, e o Azure File Sync para as partilhas de ficheiros Azure.

Execute a primeira cópia local para a sua pasta de alvo do Windows Server:

* Identifique a primeira localização do seu aparelho NAS.
* Identifique a pasta correspondente no Servidor do Windows, que já tem o Azure File Sync configurado no mesmo.
* Inicie a cópia usando RoboCopy

O seguinte comando RoboCopy copiará ficheiros do seu armazenamento NAS para a sua pasta alvo do Windows Server. O Windows Server irá sincronizá-lo com as partilhas de ficheiros Azure. 

Se forme menos armazenamento no seu Servidor Windows do que os seus ficheiros no aparelho NAS, então tem um tiering de nuvem configurado. À medida que o volume do Windows Server local fica cheio, o [tiering em nuvem](storage-sync-cloud-tiering.md) irá entrar e os ficheiros de nível que já sincronizaram com sucesso. O tiering em nuvem gerará espaço suficiente para continuar a cópia do aparelho NAS. O tiering de nuvem verifica uma vez por hora para ver o que se sincronizou e libertar espaço em disco para alcançar o espaço livre de 99% de volume.
É possível que o RoboCopy mova ficheiros mais rapidamente do que pode sincronizar com a nuvem e o nível local, ficando assim sem espaço de disco local. RoboCopy vai falhar. Recomenda-se que trabalhe através das ações numa sequência que o impeça. Por exemplo, não iniciar os trabalhos da RoboCopy para todas as ações ao mesmo tempo, ou apenas movimentar ações que se encaixem na quantidade atual de espaço livre no Windows Server, para mencionar algumas.

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
      /UNILOG:\<nome do ficheiro\>
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
      Permite executar este comando RoboCopy várias vezes, sequencialmente no mesmo alvo/destino. Identifica o que foi copiado antes e omite-o. Apenas alterações, adições e "*exclusões*" serão processadas, o que ocorreu desde a última execução. Se o comando não foi executado antes, nada é omitido. A bandeira */MIR* é uma excelente opção para locais de origem que ainda são ativamente utilizados e em mudança.
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

## <a name="phase-8-user-cut-over"></a>Fase 8: Recorte do utilizador

Quando executa o comando RoboCopy pela primeira vez, os seus utilizadores e aplicações ainda estão a aceder a ficheiros no NAS e potencialmente a trocá-los. É possível que a RoboCopy tenha processado um diretório, passando para o próximo e, em seguida, um utilizador na localização de origem (NAS) adiciona, altera ou elimina um ficheiro que agora não será processado nesta execução roboCopy atual. Este comportamento é esperado.

A primeira execução é sobre mover a maior parte dos dados para o seu Servidor Windows e para a nuvem através do Azure File Sync. Esta primeira cópia pode demorar muito tempo, dependendo de:

* sua largura de banda de download
* a largura de banda de carregamento
* a velocidade de rede local e o número de quão idealmente o número de fios RoboCopy combina com ele
* o número de itens (ficheiros e pastas), que precisam de ser processados pela RoboCopy e pelo Azure File Sync

Uma vez concluída a execução inicial, volte a executar o comando.

A segunda vez terminará mais rápido, porque só precisa de transportar mudanças que aconteceram desde a última corrida. Durante esta segunda corrida, ainda assim, novas mudanças podem acumular-se.

Repita este processo até estar convencido de que o tempo que leva para completar um RoboCopy para uma localização específica está dentro de uma janela aceitável para o tempo de inatividade.

Quando considera o tempo de inatividade aceitável e está preparado para desligar a localização nas: Para desativar o acesso do utilizador, tem a opção de alterar OS ACLs na raiz de partilha de modo a que os utilizadores não possam mais aceder ao local ou dar qualquer outro passo apropriado que impede que o conteúdo mude nesta pasta no seu NAS.

Executar uma última rodada roboCopy. Vai apanhar quaisquer alterações que possam ter sido perdidas.
O tempo que este último passo demora, depende da velocidade da varredura RoboCopy. Pode estimar o tempo (que é igual ao seu tempo de inatividade) medindo o tempo que o percurso anterior demorou.

Crie uma parte na pasta Do Servidor do Windows e, possivelmente, ajuste a sua implementação DFS-N para a indicar. Certifique-se de definir as mesmas permissões de nível de partilha que na sua parte NAS SMB. Se tiver um NAS de classe empresarial, então os SIDs do utilizador corresponderão automaticamente à medida que os utilizadores existem em Ficheiros de cópias de cópias de Cópias de Diretório Ativo e RoboCopy com total fidelidade. Se utilizou utilizadores locais no seu NAS, precisa de recriar estes utilizadores como utilizadores locais do Windows Server e mapear os SIDs existentes Que roboCopy passou para o seu Servidor Windows para os SIDs dos seus novos utilizadores locais do Windows Server.

Terminou de migrar uma parte/grupo de ações para uma raiz ou volume comum. (Dependendo do seu mapeamento da Fase 1)

Pode tentar executar algumas destas cópias em paralelo. Recomendamos o processamento do âmbito de uma partilha de ficheiros Azure de cada vez.

> [!WARNING]
> Depois de ter movido todos os dados da SUA NAS para o Windows Server, e a sua migração estiver completa: Volte a ***todos os*** grupos de sincronização do portal Azure e ajuste o valor de volume de volume sem volume de volume em nuvem para algo mais adequado para a utilização de cache, digamos 20%. 

A política de espaço livre de volume de volume de nível de nuvem atua a um nível de volume com pontos finais de servidor potencialmente múltiplos sincronizados a partir dele. Se se esquecer de ajustar o espaço livre num mesmo ponto final do servidor, o sync continuará a aplicar a regra mais restritiva e tentará manter 99% de espaço livre no disco, fazendo com que a cache local não esteja a funcionar como seria de esperar. A menos que o seu objetivo seja apenas ter o espaço de nome para um volume que apenas contém dados de arquivo raramente acedidos e você está reservando o resto do espaço de armazenamento para outro cenário.

## <a name="troubleshoot"></a>Resolução de problemas

O problema mais provável é que o comando RoboCopy falha com *"Volume cheio"* no lado do Servidor do Windows. O tiering em nuvem atua uma vez por hora para evacuar o conteúdo do disco local do Windows Server, que tem sincronizado. O seu objetivo é alcançar o seu espaço 99% livre no volume.

Deixe sincronizar o progresso e o tiering de nuvem liberte o espaço do disco. Pode observar isso no File Explorer no seu Servidor Windows.

Quando o seu Servidor Windows tiver capacidade disponível suficiente, a reexecução do comando resolverá o problema. Nada quebra quando se entra nesta situação e pode seguir em frente com confiança. O inconveniente de voltar a dirigir o comando é a única consequência.

Verifique o link na secção seguinte para resolver problemas com problemas de sincronização de ficheiros Azure.

## <a name="next-steps"></a>Passos seguintes

Há mais a descobrir sobre as ações de ficheiros Azure e o Azure File Sync. Os seguintes artigos ajudam a compreender opções avançadas, boas práticas e também contêm ajuda para resolver problemas. Estes artigos ligam-se ao [ficheiro Azure, partilha ndo documentação](storage-files-introduction.md) conforme apropriado.

* [Visão geral da AFS](https://aka.ms/AFS)
* [Guia de implantação da AFS](storage-files-deployment-guide.md)
* [Resolução de problemas da AFS](storage-sync-files-troubleshoot.md)
