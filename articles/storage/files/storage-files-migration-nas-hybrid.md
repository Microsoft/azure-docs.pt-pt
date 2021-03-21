---
title: Migração nas instalações da NAS para Azure File Sync
description: Saiba como migrar ficheiros de uma localização de Armazenamento (NAS) anexado à rede no local para uma implementação em nuvem híbrida com partilhas de ficheiros Azure File Sync e Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 86e79302716fa502d8562dd563b0a5c5fb220a67
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547561"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migrar do Armazenamento Ligado à Rede (NAS) para uma implementação em nuvem híbrida com Azure File Sync

Este artigo de migração é um dos vários que envolvem as palavras-chave NAS e Azure File Sync. Verifique se este artigo se aplica ao seu cenário:

> [!div class="checklist"]
> * Fonte de dados: Armazenamento Ligado à Rede (NAS)
> * Rota de migração: UPLOAD e sincronização do SERVIDOR NAS &rArr; Windows com a partilha de &rArr; ficheiros Azure
> * Caching ficheiros no local: Sim, o objetivo final é uma implementação de Azure File Sync.

Se o seu cenário for diferente, olhe através da [tabela de guias de migração.](storage-files-migration-overview.md#migration-guides)

O Azure File Sync funciona em locais de armazenamento direto anexado (DAS) e não suporta sincronização em locais de armazenamento anexado à rede (NAS).
Este facto torna necessária uma migração dos seus ficheiros e este artigo guia-o através do planeamento e execução de tal migração.

## <a name="migration-goals"></a>Objetivos de migração

O objetivo é mover as ações que tem no seu aparelho NAS para um Servidor Windows. Em seguida, utilize o Azure File Sync para uma implementação de nuvem híbrida. Geralmente, as migrações têm de ser feitas de uma forma que garantisse a integridade dos dados de produção e a sua disponibilidade durante a migração. Este último requer manter o tempo de inatividade ao mínimo, de modo a que possa encaixar ou apenas exceder ligeiramente as janelas de manutenção regulares.

## <a name="migration-overview"></a>Descrição geral da migração

Como mencionado no artigo geral de [migração](storage-files-migration-overview.md)dos Ficheiros Azure, é importante utilizar a ferramenta e abordagem corretas de cópia. O seu aparelho NAS está a expor as ações da SMB diretamente na sua rede local. O RoboCopy, incorporado no Windows Server, é a melhor forma de mover os seus ficheiros neste cenário de migração.

- Fase 1: [Identifique quantas ações de ficheiroS Azure precisa](#phase-1-identify-how-many-azure-file-shares-you-need)
- Fase 2: [Provisão de um servidor do Windows adequado no local](#phase-2-provision-a-suitable-windows-server-on-premises)
- Fase 3: [Implementar o recurso cloud Azure File Sync](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- Fase 4: [Implantar recursos de armazenamento Azure](#phase-4-deploy-azure-storage-resources)
- Fase 5: [Implementar o agente Azure File Sync](#phase-5-deploy-the-azure-file-sync-agent)
- Fase 6: [Configurar o Ficheiro Azure no Servidor do Windows](#phase-6-configure-azure-file-sync-on-the-windows-server)
- Fase 7: [RoboCopy](#phase-7-robocopy)
- Fase 8: [Corte do utilizador](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: Identifique quantas ações de ficheiroS Azure precisa

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>Fase 2: Provisão de um servidor do Windows adequado no local

* Crie um Windows Server 2019 - no mínimo 2012R2 - como uma máquina virtual ou servidor físico. Um cluster de falha do Windows Server também é suportado.
* Provisão ou adicionar Armazenamento Direto Anexado (DAS em comparação com o NAS, que não é suportado).

    A quantidade de armazenamento que o seu fornecimento pode ser menor do que o que está a utilizar atualmente no seu aparelho NAS. Esta escolha de configuração requer que também utilize a funcionalidade de [tiering](storage-sync-cloud-tiering-overview.md) de cloud Syncs Azure File Syncs.
    No entanto, quando copiar os seus ficheiros do espaço NAS maior para o menor volume do Windows Server numa fase posterior, terá de trabalhar em lotes:

    1. Mova um conjunto de ficheiros que se encaixe no disco
    2. deixe sincronizar ficheiros e nivelar o nível de nuvem
    3. quando mais espaço livre for criado no volume, proceda com o próximo lote de ficheiros. Em alternativa, reveja o comando RoboCopy na próxima [secção RoboCopy](#phase-7-robocopy) para utilização do novo `/LFSM` comutador. A utilização `/LFSM` pode simplificar significativamente os seus trabalhos roboCopy, mas não é compatível com outros interruptores RoboCopy de que possa depender.
    
    Pode evitar esta abordagem de lote, disponibilizando o espaço equivalente no Windows Server que os seus ficheiros ocupam no aparelho NAS. Considere a deduplicação no NAS / Windows. Se não quiser comprometer permanentemente esta elevada quantidade de armazenamento no seu Windows Server, pode reduzir o tamanho do volume após a migração e antes de ajustar as políticas de tiering da nuvem. Isso cria uma cache menor no local das suas ações de ficheiros Azure.

A configuração de recursos (computação e RAM) do Servidor do Windows que implementa depende principalmente do número de itens (ficheiros e pastas) que estará a sincronizar. Recomendamos que vá com uma configuração de desempenho mais alta se tiver alguma preocupação.

[Saiba como dimensionar um Servidor do Windows com base no número de itens (ficheiros e pastas) que necessita para sincronizar.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> O artigo anteriormente ligado apresenta uma tabela com um intervalo para memória do servidor (RAM). Pode orientar-se para o número menor para o seu servidor, mas espera-se que a sincronização inicial possa demorar significativamente mais tempo.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fase 3: Implementar o recurso cloud Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fase 4: Implantar recursos de armazenamento Azure

Nesta fase, consulte a tabela de mapeamento da Fase 1 e use-a para prever o número correto de contas de armazenamento Azure e ações de ficheiro dentro delas.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fase 5: Implementar o agente Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>Fase 6: Configurar o Ficheiro Azure no Servidor do Windows

O seu Windows Server registado no local deve estar pronto e ligado à internet para este processo.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> O tiering em nuvem é a funcionalidade AFS que permite ao servidor local ter menos capacidade de armazenamento do que é armazenado na nuvem, mas tem o espaço de nome completo disponível. Os dados locais interessantes também são cached localmente para o desempenho de acesso rápido. O tiering em nuvem é uma funcionalidade opcional por Azure File Sync "ponto final do servidor".

> [!WARNING]
> Se fornequir menos armazenamento no volume(s) do seu servidor Windows do que os seus dados utilizados no aparelho NAS, então o tiering de nuvem é obrigatório. Se não ligar o tiering da nuvem, o seu servidor não libertará espaço para armazenar todos os ficheiros. Desaça a sua política de tiering, temporariamente para a migração, para 99% de espaço livre de volume. Certifique-se de que volta às definições de nivelamento da nuvem após a conclusão da migração e defina-a para um nível útil mais a longo prazo.

Repita os passos da criação do grupo de sincronização e a adição da pasta do servidor correspondente como ponto final do servidor para todas as partilhas de ficheiros /localizações do servidor Azure, que precisam de ser configuradas para sincronização.

Após a criação de todos os pontos finais do servidor, a sincronização está a funcionar. Pode criar um ficheiro de teste e vê-lo sincronizado desde a localização do servidor até à partilha de ficheiros Azure conectada (conforme descrito pelo ponto final da nuvem no grupo de sincronização).

Ambas as localizações, as pastas do servidor e as ações de ficheiros Azure estão vazias e aguardam dados em qualquer local. No passo seguinte, começará a copiar ficheiros no Windows Server para O Azure File Sync para os mover para a nuvem. Caso tenha ativado o tiering da nuvem, o servidor começará a nivelar os ficheiros, caso fique sem capacidade no volume(s) local.

## <a name="phase-7-robocopy"></a>Fase 7: RoboCopy

A abordagem básica de migração é um RoboCopy do seu aparelho NAS para o seu Windows Server, e Azure File Sync para ações de ficheiros Azure.

Execute a primeira cópia local para a pasta alvo do Windows Server:

* Identifique a primeira localização do seu aparelho NAS.
* Identifique a pasta correspondente no Servidor do Windows, que já tem O Azure File Sync configurado no mesmo.
* Inicie a cópia usando RoboCopy

O seguinte comando RoboCopy copiará ficheiros do seu armazenamento NAS para a pasta alvo do Windows Server. O Servidor do Windows sincroniza-o com a partilha de ficheiros Azure. 

Se fornequir menos armazenamento no seu Windows Server do que os seus ficheiros ocuparem o aparelho NAS, então tem um nível de nuvem configurado. À medida que o volume do Servidor do Windows local fica cheio, [o tiering](storage-sync-cloud-tiering-overview.md) da nuvem entrará em edição de ficheiros que já tenham sincronizado com sucesso. O tiering da nuvem gerará espaço suficiente para continuar a cópia do aparelho NAS. O tiering da nuvem verifica uma vez por hora para ver o que sincronizou e para libertar o espaço do disco para alcançar o espaço livre de volume de 99%.
É possível que o RoboCopy mova os ficheiros mais rapidamente do que pode sincronizar com a nuvem e o nível localmente, ficando assim sem espaço de disco local. RoboCopy vai falhar. Recomenda-se que trabalhe através das ações numa sequência que o impeça. Por exemplo, não iniciar trabalhos roboCopy para todas as ações ao mesmo tempo, ou apenas mover ações que se encaixam na quantidade atual de espaço livre no Windows Server, para mencionar algumas.

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>Fase 8: Corte do utilizador

Quando executam o comando RoboCopy pela primeira vez, os seus utilizadores e aplicações ainda estão a aceder a ficheiros no NAS e potencialmente alterá-los. É possível que o RoboCopy tenha processado um diretório, passa para o seguinte e, em seguida, um utilizador na localização de origem (NAS) adiciona, altera ou elimina um ficheiro que agora não será processado nesta atual execução do RoboCopy. Este comportamento é esperado.

A primeira execução é sobre mover a maior parte dos dados para o seu Servidor do Windows e para a nuvem através do Azure File Sync. Esta primeira cópia pode demorar muito tempo, dependendo de:

* sua largura de banda de descarregamento
* a largura de banda de upload
* a velocidade da rede local e o número de quão idealmente o número de fios RoboCopy corresponde a
* o número de itens (ficheiros e pastas), que precisam de ser processados pela RoboCopy e pela Azure File Sync

Uma vez concluída a execução inicial, volte a executar o comando.

A segunda vez terminará mais rápido, porque só precisa de transportar mudanças que aconteceram desde a última corrida. Durante esta segunda execução, ainda assim, novas mudanças podem acumular-se.

Repita este processo até estar convencido de que o tempo necessário para completar um RoboCopy para uma localização específica está dentro de uma janela aceitável para o tempo de inatividade.

Quando considerar o tempo de inatividade aceitável, tem de remover o acesso do utilizador às suas ações baseadas no NAS. Pode fazê-lo por quaisquer passos que impeçam os utilizadores de alterar a estrutura e conteúdo do ficheiro e da pasta. Um exemplo é apontar o seu DFS-Namespace para um local não existente ou alterar os ACLs de raiz na partilha.

Executar uma última rodada de RoboCopy. Vai detetar quaisquer alterações que possam ter sido perdidas.
Quanto tempo este passo final leva, depende da velocidade do scan RoboCopy. Pode estimar o tempo (que é igual ao seu tempo de inatividade) medindo o tempo que a execução anterior demorou.

Crie uma partilha na pasta do Servidor do Windows e, possivelmente, ajuste a sua implementação DFS-N para a apontar. Certifique-se de definir as mesmas permissões de nível de partilha que na sua parte NAS SMB. Se tiver um NAS de domínio de classe empresarial, então os SIDs do utilizador corresponderão automaticamente à medida que os utilizadores existem no Ative Directory e roboCopy copiam ficheiros e metadados com total fidelidade. Se utilizou utilizadores locais no seu NAS, precisa de recriar estes utilizadores como utilizadores locais do Windows Server e mapear os SIDs existentes roboCopy transferidos para o seu Servidor do Windows para os SIDs dos seus novos utilizadores locais do Windows Server.

Terminou de migrar uma parte/grupo de ações numa raiz ou volume comum. (Dependendo do seu mapeamento da Fase 1)

Podes tentar executar algumas destas cópias em paralelo. Recomendamos o processamento do âmbito de uma partilha de ficheiros Azure de cada vez.

> [!WARNING]
> Depois de ter transferido todos os dados do SEU NAS para o Windows Server, e a sua migração está completa: Volte a ***todos os***  grupos de sincronização no portal Azure e ajuste o valor de espaço livre de nível de cloud para algo mais adequado para a utilização da cache, digamos 20%. 

A política de espaço livre de nível de nivelamento da nuvem atua a nível de volume com pontos finais potencialmente múltiplos do servidor sincronizados a partir dele. Se se esquecer de ajustar o espaço livre num ponto final de um servidor, a sincronização continuará a aplicar a regra mais restritiva e tentará manter 99% de espaço livre em disco, fazendo com que a cache local não esteja a funcionar como seria de esperar. A menos que seja seu objetivo ter apenas o espaço de nome para um volume que apenas contém dados de arquivo raramente acedidos e você está reservando o resto do espaço de armazenamento para outro cenário.

## <a name="troubleshoot"></a>Resolução de problemas

O problema mais provável é que o comando RoboCopy falhe com *o "Volume cheio"* no lado do Windows Server. O nível da nuvem atua uma vez a cada hora para evacuar o conteúdo do disco local do Windows Server, que foi sincronizado. O seu objetivo é alcançar o seu espaço livre de 99% no volume.

Deixe sincronizar o progresso e o tiering da nuvem libertar o espaço do disco. Pode observar isso no Explorador de Ficheiros no seu Servidor windows.

Quando o seu Servidor do Windows tiver capacidade disponível suficiente, a reexecução do comando resolverá o problema. Nada se rompe quando se entra nesta situação e se pode avançar com confiança. O inconveniente de voltar a comandar o comando é a única consequência.

Verifique o link na secção seguinte para verificar problemas de resolução de problemas problemas com o Azure File Sync.

## <a name="next-steps"></a>Passos seguintes

Há mais a descobrir sobre as ações de ficheiros Azure e a Azure File Sync. Os seguintes artigos ajudam a compreender opções avançadas, boas práticas, e também contêm ajuda para resolver problemas. Estes artigos ligam-se à [documentação de partilha de ficheiros Azure](storage-files-introduction.md) conforme apropriado.

* [Visão geral da AFS](./storage-sync-files-planning.md)
* [Guia de implantação da AFS](./storage-how-to-create-file-share.md)
* [Resolução de problemas da AFS](storage-sync-files-troubleshoot.md)