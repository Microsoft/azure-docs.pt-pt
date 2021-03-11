---
title: Migração linux para Azure File Sync
description: Saiba como migrar ficheiros de uma localização do servidor Linux para uma implementação de nuvem híbrida com partilhas de ficheiros Azure File Sync e Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: ff26318cafdf493579961fc718643f831ae9efeb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564259"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migrar do Linux para uma implementação de nuvem híbrida com Azure File Sync

Este artigo de migração é um dos vários que envolvem as palavras-chave NFS e Azure File Sync. Verifique se este artigo se aplica ao seu cenário:

> [!div class="checklist"]
> * Fonte de dados: Armazenamento Ligado à Rede (NAS)
> * Rota de migração: Linux Server com SAMBA &rArr; Windows Server 2012R2 ou posterior &rArr; sincronização com a partilha de ficheiros Azure
> * Caching ficheiros no local: Sim, o objetivo final é uma implementação de Azure File Sync.

Se o seu cenário for diferente, olhe através da [tabela de guias de migração.](storage-files-migration-overview.md#migration-guides)

O Azure File Sync funciona em instâncias do Windows Server com armazenamento direto anexado (DAS). Não suporta sincronização de e para clientes Linux, ou uma partilha remota do Bloco de Mensagens do Servidor (SMB) ou do Sistema de Ficheiros de Rede (NFS).

Como resultado, transformar os seus serviços de ficheiros numa implementação híbrida torna necessária uma migração para o Windows Server. Este artigo guia-o através do planeamento e execução de tal migração.

## <a name="migration-goals"></a>Objetivos de migração

O objetivo é mover as ações que tem no seu servidor Linux Samba para uma instância do Windows Server. Em seguida, utilize o Azure File Sync para uma implementação de nuvem híbrida. Esta migração tem de ser feita de forma a garantir a integridade dos dados de produção e a disponibilidade durante a migração. Este último requer manter o tempo de inatividade ao mínimo, de modo a que possa encaixar ou apenas exceder ligeiramente as janelas de manutenção regulares.

## <a name="migration-overview"></a>Descrição geral da migração

Como mencionado no artigo geral de [migração](storage-files-migration-overview.md)dos Ficheiros Azure, é importante utilizar a ferramenta e abordagem corretas de cópia. O seu servidor Linux Samba está a expor as ações da SMB diretamente na sua rede local. Robocopia, incorporada no Windows Server, é a melhor forma de mover os seus ficheiros neste cenário de migração.

Se não estiver a executar Samba no seu servidor Linux e preferir migrar pastas para uma implementação híbrida no Windows Server, pode utilizar ferramentas de cópia Linux em vez de Robocopy. Esteja atento às capacidades de fidelidade da sua ferramenta de cópia. Reveja a secção de bases da [migração](storage-files-migration-overview.md#migration-basics) no artigo de visão geral da migração para saber o que procurar numa ferramenta de cópia.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: Identifique quantas ações de ficheiroS Azure precisa

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-instance-on-premises"></a>Fase 2: Provisionar uma instância adequada do Servidor do Windows no local

* Crie um exemplo do Windows Server 2019 como uma máquina virtual ou servidor físico. O Windows Server 2012 R2 é o requisito mínimo. Um cluster de falha do Windows Server também é suportado.
* Provisão ou adicionar armazenamento direto anexado (DAS). O armazenamento ligado à rede (NAS) não é suportado.

  A quantidade de armazenamento que disponibiliza pode ser menor do que a que está a utilizar atualmente no seu servidor Linux Samba, se utilizar a funcionalidade [de nivelamento de nuvem](storage-sync-cloud-tiering-overview.md) Azure File Sync. 

A quantidade de armazenamento que presta pode ser menor do que a que está a utilizar atualmente no seu servidor Linux Samba. Esta escolha de configuração requer que também utilize a funcionalidade de [tiering](storage-sync-cloud-tiering-overview.md) de cloud Syncs Azure File Syncs. No entanto, quando copiar os seus ficheiros do maior espaço do servidor Linux Samba para o menor volume do Windows Server numa fase posterior, terá de trabalhar em lotes:

  1. Mova um conjunto de ficheiros que se encaixem no disco.
  2. Deixe a sincronização de ficheiros e o nível de nivelamento da nuvem.
  3. Quando for criado mais espaço livre no volume, proceda ao próximo lote de ficheiros. Em alternativa, reveja o comando RoboCopy na próxima [secção RoboCopy](#phase-7-robocopy) para utilização do novo `/LFSM` comutador. A utilização `/LFSM` pode simplificar significativamente os seus trabalhos roboCopy, mas não é compatível com outros interruptores RoboCopy de que possa depender.
    
  Pode evitar esta abordagem de lote, provisionando o espaço equivalente na instância do Windows Server que os seus ficheiros ocupam no servidor Linux Samba. Considere permitir a deduplicação no Windows. Se não quiser comprometer permanentemente esta elevada quantidade de armazenamento na sua instância do Windows Server, pode reduzir o tamanho do volume após a migração e antes de ajustar as políticas de tiering da nuvem. Isso cria uma cache menor no local das suas ações de ficheiros Azure.

A configuração de recursos (computação e RAM) da instância do Servidor do Windows que implementa depende principalmente do número de itens (ficheiros e pastas) que estará a sincronizar. Recomendamos que vá com uma configuração de maior desempenho se tiver alguma preocupação.

[Saiba como dimensionar uma instância do Windows Server com base no número de itens (ficheiros e pastas) que necessita para sincronizar.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> O artigo anteriormente ligado apresenta uma tabela com um intervalo para memória do servidor (RAM). Pode orientar-se para o número menor para o seu servidor, mas espera-se que a sincronização inicial possa demorar significativamente mais tempo.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fase 3: Implementar o recurso cloud Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fase 4: Implantar recursos de armazenamento Azure

Nesta fase, consulte a tabela de mapeamento da Fase 1 e use-a para prever o número correto de contas de armazenamento Azure e ações de ficheiro dentro delas.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fase 5: Implementar o agente Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server-deployment"></a>Fase 6: Configurar o Ficheiro Azure sincronizado na implementação do Servidor do Windows

A sua instância registada no Windows Server deve estar pronta e ligada à internet para este processo.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> O tiering em nuvem é a funcionalidade Azure File Sync que permite ao servidor local ter menos capacidade de armazenamento do que é armazenada na nuvem, mas tem o espaço de nome completo disponível. Os dados locais interessantes também são cached localmente para o desempenho de acesso rápido. O tiering em nuvem é uma função opcional para cada ponto final do servidor Azure File Sync.

> [!WARNING]
> Se fornequir menos armazenamento nos volumes do Windows Server do que os seus dados utilizados no servidor Linux Samba, então o tiering da nuvem é obrigatório. Se não ligar o tiering da nuvem, o seu servidor não libertará espaço para armazenar todos os ficheiros. Desaça a sua política de tiering, temporariamente para a migração, para 99% de espaço livre para um volume. Certifique-se de que volta às definições de nivelamento da nuvem após a conclusão da migração e defina a política para um nível mais útil a longo prazo.

Repita os passos da criação do grupo de sincronização e a adição da pasta do servidor correspondente como ponto final do servidor para todas as partilhas de ficheiros Azure e localizações do servidor que precisam de ser configuradas para sincronização.

Após a criação de todos os pontos finais do servidor, a sincronização está a funcionar. Pode criar um ficheiro de teste e vê-lo sincronizado desde a localização do servidor até à partilha de ficheiros Azure conectada (conforme descrito pelo ponto final da nuvem no grupo de sincronização).

Ambas as localizações, as pastas do servidor e as partilhas de ficheiros Azure, estão vazias e aguardam dados. No passo seguinte, começará a copiar ficheiros para a instância do Windows Server para o Azure File Sync para os mover para a nuvem. Se tiver ativado o tiering da nuvem, o servidor começará a nivelar os ficheiros se ficar sem capacidade nos volumes locais.

## <a name="phase-7-robocopy"></a>Fase 7: Robocopia

A abordagem básica de migração é usar robocopia para copiar ficheiros e usar o Azure File Sync para fazer a sincronização.

Execute a primeira cópia local para a pasta alvo do Windows Server:

1. Identifique a primeira localização no seu servidor Linux Samba.
1. Identifique a pasta correspondente na instância do Servidor do Windows que já tem O Azure File Sync configurado nele.
1. Inicie a cópia utilizando robocopia.

O seguinte comando Robocopy copiará ficheiros do armazenamento do seu servidor Linux Samba para a pasta alvo do Windows Server. O Windows Server sincroniza-o com as ações de ficheiros Azure. 

Se fornequir menos armazenamento na sua instância do Windows Server do que os seus ficheiros ocuparem o servidor Linux Samba, então tem um nível de nuvem configurado. À medida que o volume do Servidor do Windows local fica cheio, [o tiering da nuvem](storage-sync-cloud-tiering-overview.md) iniciará e os ficheiros de nível já sincronizados com sucesso. O tiering em nuvem gerará espaço suficiente para continuar a cópia do servidor Linux Samba. O nível da nuvem verifica uma vez por hora para ver o que sincronizou e para libertar espaço em disco para alcançar a política de 99% de espaço livre para um volume.

É possível que a Robocopy mova ficheiros mais rapidamente do que pode sincronizar com a nuvem e o nível localmente, fazendo com que fique sem espaço em disco local. A robocopia falhará. Recomendamos que trabalhe através das ações numa sequência que previna o problema. Por exemplo, considere não iniciar empregos robocopia para todas as ações ao mesmo tempo. Ou considere ações móveis que se encaixem na quantidade atual de espaço livre na instância do Windows Server. Se o seu trabalho de Robocopia falhar, pode sempre repetir o comando desde que utilize a seguinte opção de espelho/purga:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>Fase 8: Corte do utilizador

Quando executam o comando Robocopy pela primeira vez, os seus utilizadores e aplicações ainda estão a aceder a ficheiros no servidor Linux Samba e potencialmente a alterá-los. É possível que a Robocopy tenha processado um diretório e passa para o seguinte, e depois um utilizador no local de origem (Linux) adiciona, altera ou elimina um ficheiro que agora não será processado nesta atual execução de Robocopia. Este comportamento é esperado.

A primeira execução é sobre mover a maior parte dos dados para a sua instância do Windows Server e para a nuvem através do Azure File Sync. Esta primeira cópia pode demorar muito tempo, dependendo de:

* A sua largura de banda de descarregamento.
* A largura de banda do upload.
* A velocidade da rede local, e o número de como o número de fios Robocopy corresponde perfeitamente.
* O número de itens (ficheiros e pastas) que a Robocopy e o Azure File Sync precisam de processar.

Depois de concluída a execução inicial, volte a executar o comando.

Termina mais rápido na segunda vez, porque só precisa de transportar alterações que aconteceram desde a última corrida. Durante este segundo, novas mudanças ainda podem acumular-se.

Repita este processo até estar convencido de que o tempo necessário para completar uma operação robocopia para uma localização específica está dentro de uma janela aceitável para o tempo de inatividade.

Quando considerar o tempo de inatividade aceitável e estiver preparado para desligar a localização do Linux, pode alterar ACLs na raiz da partilha de modo a que os utilizadores não possam mais aceder à localização. Ou pode dar qualquer outro passo apropriado que impeça que o conteúdo seja alterado nesta pasta no seu servidor Linux.

Correr uma última ronda de Robocopia. Vai detetar quaisquer alterações que possam ter sido perdidas. O tempo que este passo final leva depende da velocidade do exame robocopia. Pode estimar o tempo (que é igual ao seu tempo de inatividade) medindo o tempo que a execução anterior demorou.

Crie uma partilha na pasta do Servidor do Windows e, possivelmente, ajuste a sua implementação DFS-N para a apontar. Certifique-se de definir as mesmas permissões de nível de partilha que nas ações SMB do seu servidor Linux Samba. Se utilizou utilizadores locais no seu servidor Linux Samba, tem de recriar estes utilizadores como utilizadores locais do Windows Server. Também precisa de mapear os SIDs existentes que a Robocopy mudou para a sua instância do Windows Server para os SIDs dos seus novos utilizadores locais do Windows Server. Se usou contas de Diretório Ativo e ACLs, a Robocopy move-as como está, e não são necessárias mais medidas.

Terminou de migrar uma parte ou um grupo de ações para uma raiz ou volume comum (dependendo do seu mapeamento da Fase 1).

Podes tentar executar algumas destas cópias em paralelo. Recomendamos o processamento do âmbito de uma partilha de ficheiros Azure de cada vez.

> [!WARNING]
> Depois de ter transferido todos os dados do seu servidor Linux Samba para a instância do Windows Server, e a sua migração estar completa, volte a *todos os*  grupos de sincronização no portal Azure. Ajuste a percentagem de espaço livre para o volume de nivelamento de nuvens para algo mais adequado para a utilização da cache, como 20 por cento. 

A política de espaço livre no volume de nivelamento de nuvem atua a nível de volume com pontos finais potencialmente múltiplos do servidor sincronizados a partir dele. Se se esquecer de ajustar o espaço livre num ponto final de um servidor, a sincronização continuará a aplicar a regra mais restritiva e tentará manter o espaço livre do disco a 99%. A cache local pode então não funcionar como espera. O desempenho pode ser aceitável se o seu objetivo é ter o espaço de nome para um volume que contém apenas dados de arquivo raramente acedidos, e você está reservando o resto do espaço de armazenamento para outro cenário.

## <a name="troubleshoot"></a>Resolução de problemas

O problema mais comum é que o comando Robocopy falha com o **Volume cheio** no lado do Servidor do Windows. O nível da nuvem atua uma vez a cada hora para evacuar o conteúdo do disco local do Windows Server que tenha sincronizado. O seu objetivo é alcançar um espaço livre de 99 por cento no volume.

Deixe sincronizar o progresso e o tiering da nuvem libertar o espaço do disco. Pode observar isso no Explorador de Ficheiros no Windows Server.

Quando a sua instância do Windows Server tiver capacidade disponível suficiente, a repetição do comando resolverá o problema. Nada se rompe quando se entra nesta situação, e pode avançar com confiança. O inconveniente de voltar a comandar o comando é a única consequência.

Verifique o link na secção seguinte para verificar problemas de resolução de problemas do Azure File Sync.

## <a name="next-steps"></a>Passos seguintes

Há mais a descobrir sobre as ações de ficheiros Azure e a Azure File Sync. Os seguintes artigos contêm opções avançadas, boas práticas e ajuda na resolução de problemas. Estes artigos ligam-se à [documentação de partilha de ficheiros Azure](storage-files-introduction.md) conforme apropriado.

* [Visão geral do Azure File Sync](./storage-sync-files-planning.md)
* [Guia de implementação de Sincronização de Ficheiros Azure](./storage-how-to-create-file-share.md)
* [Resolução de problemas do Azure File Sync](storage-sync-files-troubleshoot.md)