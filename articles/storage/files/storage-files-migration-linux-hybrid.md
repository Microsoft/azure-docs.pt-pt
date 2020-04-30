---
title: Migração linux para O Sincronizado de Ficheiros Azure
description: Saiba como migrar ficheiros de um servidor Linux para uma implantação híbrida em nuvem com partilhas de ficheiros Azure File Sync e Azure.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 3131d6a7e3675027968eadd5f3e3ca8a7f2449c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143548"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migrar do Linux para uma nuvem híbrida com Bluee File Sync

O Azure File Sync funciona em instâncias do Windows Server com armazenamento em anexo direto (DAS). Não suporta sincronização de e para o Linux ou uma partilha remota do Bloco de Mensagens do Servidor (SMB).

Como resultado, transformar os seus serviços de ficheiros numa implementação híbrida torna necessária uma migração para o Windows Server. Este artigo guia-o através do planeamento e execução de tal migração.

## <a name="migration-goals"></a>Objetivos de migração

O objetivo é mover as ações que tem no seu servidor Linux Samba para uma instância do Windows Server. Em seguida, utilize o Azure File Sync para uma implantação híbrida em nuvem. Esta migração tem de ser feita de forma a garantir a integridade dos dados de produção, bem como a disponibilidade durante a migração. Este último requer manter o tempo de paragem ao mínimo, de modo a que possa encaixar ou apenas exceder ligeiramente as janelas de manutenção regulares.

## <a name="migration-overview"></a>Descrição geral da migração

Como referido no artigo de visão geral da [migração](storage-files-migration-overview.md)dos Ficheiros Azure, é importante utilizar a ferramenta e abordagem corretas. O seu servidor Linux Samba está a expor as ações da SMB diretamente na sua rede local. A Robocopy, incorporada no Windows Server, é a melhor forma de mover os seus ficheiros neste cenário de migração.

Se não estiver a executar samba no seu servidor Linux e quiser migrar pastas para uma implementação híbrida no Windows Server, pode utilizar ferramentas de cópia Linux em vez de Robocopy. Se o fizer, esteja ciente das capacidades de fidelidade na sua ferramenta de cópia de ficheiros. Reveja a secção de bases da [migração](storage-files-migration-overview.md#migration-basics) no artigo de visão geral da migração para saber o que procurar numa ferramenta de cópia.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: Identifique quantas ações de ficheiro Saque Iaazul de que precisa

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-instance-on-premises"></a>Fase 2: Fornecer uma instância adequada do Servidor do Windows no local

* Crie uma instância do Windows Server 2019 como uma máquina virtual ou servidor físico. O Windows Server 2012 R2 é o requisito mínimo. Um cluster de failover do Windows Server também é suportado.
* Provisionou ou adicione armazenamento direta mente anexado (DAS). O armazenamento ligado à rede (NAS) não é suportado.

  A quantidade de armazenamento que disponibiliza pode ser menor do que a que está a utilizar atualmente no seu servidor Linux Samba, se utilizar a função de [tiering](storage-sync-cloud-tiering.md) de cloud Do Minsinha de Ficheiros Azure. No entanto, quando copiar os seus ficheiros do espaço maior do servidor Linux Samba para o volume menor do Windows Server numa fase posterior, terá de trabalhar em lotes:

  1. Mova um conjunto de ficheiros que se encaixe mindisposto no disco.
  2. Deixe a sincronização de ficheiros e o tiering em nuvem envolverem-se.
  3. Quando for criado mais espaço livre no volume, proceda ao próximo lote de ficheiros. 
    
  Pode evitar esta abordagem de loteamento, disponibilizando o espaço equivalente na instância do Windows Server que os seus ficheiros ocupam no servidor Linux Samba. Considere permitir a duplicação no Windows. Se não quiser comprometer permanentemente esta elevada quantidade de armazenamento na sua instância do Windows Server, pode reduzir o tamanho do volume após a migração e antes de ajustar as políticas de tiering em nuvem. Isso cria uma cache menor no local das suas ações de ficheiros Azure.

A configuração de recursos (computação e RAM) da instância do Windows Server que implementa depende principalmente do número de itens (ficheiros e pastas) que estará a sincronizar. Recomendamos que vá com uma configuração de maior desempenho se tiver alguma preocupação.

[Saiba como dimensionar uma instância do Windows Server com base no número de itens (ficheiros e pastas) que precisa de sincronizar.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> O artigo anteriormente ligado apresenta uma tabela com uma gama para memória do servidor (RAM). Pode orientar-se para o número menor para o seu servidor, mas espere que a sincronização inicial possa demorar significativamente mais tempo.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fase 3: Implementar o recurso cloud Da nuvem De sincronização de ficheiros Azure

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fase 4: Implantar recursos de armazenamento azure

Nesta fase, consulte a tabela de mapeamento da Fase 1 e utilize-a para fornecer o número correto de contas de armazenamento Azure e ações de arquivo dentro delas.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fase 5: Implementar o agente Dessincronização de Ficheiros Azure

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server-deployment"></a>Fase 6: Configure O Sincronizado de Ficheiros Azure na implementação do Servidor do Windows

A instância registada no local do Windows Server deve estar pronta e ligada à internet para este processo.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> O tiering em nuvem é a funcionalidade De sincronização de ficheiros Azure que permite ao servidor local ter menos capacidade de armazenamento do que o armazenado na nuvem, mas ainda tem o espaço de nome completo disponível. Dados interessantes localmente também são cached localmente para desempenho de acesso rápido. O tiering em nuvem é uma funcionalidade opcional para cada ponto final do servidor Do Ficheiro Azure.

> [!WARNING]
> Se for resumido menos armazenamento nos volumes do Windows Server do que os dados utilizados no servidor Linux Samba, então o tiering em nuvem é obrigatório. Se não ligar o tiering da nuvem, o seu servidor não libertará espaço para armazenar todos os ficheiros. Desnove a sua política de tiering, temporariamente para a migração, para 99% de espaço livre para um volume. Certifique-se de que volta às definições de nível de nuvem após a migração estar completa e coloque a política num nível mais útil a longo prazo.

Repita os passos da criação do grupo sync e a adição da pasta do servidor correspondente como ponto final do servidor para todas as partilhas de ficheiros Azure e localizações do servidor que precisam de ser configuradas para sincronização.

Após a criação de todos os pontos finais do servidor, a sincronização está a funcionar. Pode criar um ficheiro de teste e vê-lo sincronizar desde a localização do servidor até à partilha de ficheiros Azure conectada (conforme descrito pelo ponto final da nuvem no grupo de sincronização).

Ambas as localizações, as pastas do servidor e as partilhas de ficheiros Azure, estão vazias e aguardam dados. No próximo passo, começará a copiar ficheiros para a instância do Windows Server para o Azure File Sync os mover para a nuvem. Se tiver ativado o tiering da nuvem, o servidor começará a tierificar ficheiros se ficar sem capacidade nos volumes locais.

## <a name="phase-7-robocopy"></a>Fase 7: Robocopia

A abordagem básica da migração é usar a Robocopy para copiar ficheiros e usar o Azure File Sync para fazer a sincronização.

Execute a primeira cópia local para a sua pasta de alvo do Windows Server:

1. Identifique a primeira localização no seu servidor Linux Samba.
1. Identifique a pasta correspondente na instância do Servidor do Windows que já tem o Azure File Sync configurado no mesmo.
1. Inicie a cópia usando robocópia.

O seguinte comando Robocopy irá copiar ficheiros do armazenamento do servidor Do Samba Linux para a sua pasta de alvo do Windows Server. O Windows Server irá sincronizá-lo com as partilhas de ficheiros Azure. 

Se disponibilizou menos armazenamento na instância do Windows Server do que os seus ficheiros assumem no servidor Linux Samba, então tem configurado o tiering da nuvem. À medida que o volume do Windows Server local fica cheio, o [tiering em nuvem](storage-sync-cloud-tiering.md) irá iniciar e tiering ficheiros que já sincronizaram com sucesso. O tiering em nuvem gerará espaço suficiente para continuar a cópia do servidor Linux Samba. O tiering de nuvem verifica uma vez por hora para ver o que se sincronizou e libertar espaço em disco para alcançar a política de 99% de espaço livre para um volume.

É possível que a Robocopy mova ficheiros mais rápido do que pode sincronizar com a nuvem e o nível local, fazendo com que você se esgote do espaço do disco local. A robocópia falhará. Recomendamos que trabalhe através das ações numa sequência que impeça o problema. Por exemplo, considere não iniciar empregos de Robocopy para todas as ações ao mesmo tempo. Ou considere mover ações que se encaixem na quantidade atual de espaço livre na instância do Servidor do Windows. Se o seu trabalho de Robocopy falhar, pode sempre reexecutar o comando desde que utilize a seguinte opção espelho/purga:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Antecedentes:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Permite que a Robocopy execute várias linhas. O padrão é 8, máximo é 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:\<nome do ficheiro\>
   :::column-end:::
   :::column span="1":::
      Estado de saída para um ficheiro de registo como Unicode (substitui o registo existente).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Saídas para uma janela de consola. Utilizado em conjunto com a saída para um ficheiro de registo.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Executa a Robocopy no mesmo modo que uma aplicação de reserva usaria. Permite à Robocopy mover ficheiros que o utilizador atual não tem permissões.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Permite executar este comando Robocopia várias vezes, sequencialmente, no mesmo alvo/destino. Identifica e omite o que já foi copiado antes. Apenas alterações, adições e supressões que ocorreram desde a última execução são processadas. Se o comando não foi executado antes, nada é omitido. A bandeira **/MIR** é uma excelente opção para locais de origem que ainda são ativamente utilizados e em mudança.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      Fidelidade da cópia do ficheiro (predefinição é /COPY:DAT). As bandeiras de cópia são: D=data, A=atributos, T=timestamps, S=security=NTFS ACLs, O=informação do proprietário, informações de auditoria U=auditadas.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      COPIAR TODAS as informações de ficheiro (equivalentes a /COPY:DATSOU).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      Fidelidade para a cópia de diretórios (predefinido é /DCOPY:DA). As bandeiras de cópia são: D=data, A=atributos, T=timestamps.
   :::column-end:::
:::row-end:::

## <a name="phase-8-user-cut-over"></a>Fase 8: Recorte do utilizador

Quando executa o comando Robocopy pela primeira vez, os seus utilizadores e aplicações ainda estão a aceder a ficheiros no servidor Linux Samba e potencialmente a mudá-los. É possível que a Robocopy tenha processado um diretório e passando para o seguinte, e depois um utilizador na localização de origem (Linux) adiciona, altera ou elimina um ficheiro que agora não será processado nesta atual execução robocopia. Este comportamento é esperado.

A primeira execução é sobre mover a maior parte dos dados para a instância do Windows Server e para a nuvem através do Azure File Sync. Esta primeira cópia pode demorar muito tempo, dependendo de:

* A sua largura de banda de download.
* A largura de banda de upload.
* A velocidade da rede local, e o número de como o número ideal de fios robocopia combina com ele.
* O número de itens (ficheiros e pastas) que a Robocopy e o Azure File Sync precisam de processar.

Depois da execução inicial estar completa, volte a executar o comando.

Termina mais rápido da segunda vez, porque só precisa de transportar alterações que aconteceram desde a última corrida. Durante este segundo, as novas alterações ainda podem acumular-se.

Repita este processo até estar convencido de que o tempo que leva para completar uma operação robocopia para um local específico está dentro de uma janela aceitável para o tempo de inatividade.

Quando considerar o tempo de inatividade aceitável e estiver preparado para desligar a localização do Linux, pode alterar OS ACLs na raiz de partilha de modo a que os utilizadores já não possam aceder ao local. Ou pode dar qualquer outro passo apropriado que impeça que o conteúdo mude nesta pasta no seu servidor Linux.

Executar uma última ronda de Robocopia. Vai apanhar quaisquer alterações que possam ter sido perdidas. O tempo que este último passo leva depende da velocidade da varredura robocopia. Pode estimar o tempo (que é igual ao seu tempo de inatividade) medindo o tempo que o percurso anterior demorou.

Crie uma parte na pasta Do Servidor do Windows e, possivelmente, ajuste a sua implementação DFS-N para a indicar. Certifique-se de definir as mesmas permissões de nível de partilha que nas suas ações SMB do servidor Linux Samba. Se utilizou utilizadores locais no seu servidor Linux Samba, tem de recriar estes utilizadores como utilizadores locais do Windows Server. Também precisa de mapear os SIDs existentes que a Robocopy passou para a instância do Windows Server para os SIDs dos seus novos utilizadores locais do Windows Server. Se usou contas de Diretório Ativo e ACLs, a Robocopy irá movê-las como está, e não são necessárias mais medidas.

Terminou de migrar uma parte ou um grupo de ações para uma raiz ou volume comum (dependendo do seu mapeamento da Fase 1).

Pode tentar executar algumas destas cópias em paralelo. Recomendamos o processamento do âmbito de uma partilha de ficheiros Azure de cada vez.

> [!WARNING]
> Depois de ter transferido todos os dados do seu servidor Linux Samba para a instância do Windows Server, e a sua migração estar completa, regresse a *todos os* grupos de sincronização do portal Azure. Ajuste a percentagem de espaço livre para o volume de tiering em nuvem para algo mais adequado para a utilização de cache, como 20 por cento. 

A política de espaço livre no volume de tiering em nuvem atua a um nível de volume com pontos finais de servidor potencialmente múltiplos sincronizados a partir dele. Se se esquecer de ajustar o espaço livre num mesmo ponto final do servidor, a sincronização continuará a aplicar a regra mais restritiva e tentará manter o espaço livre do disco a 99%. A cache local pode então não funcionar como espera. O desempenho pode ser aceitável se o seu objetivo é ter o espaço de nome para um volume que contém apenas dados de arquivo raramente acedidos, e você está reservando o resto do espaço de armazenamento para outro cenário.

## <a name="troubleshoot"></a>Resolução de problemas

O problema mais comum é que o comando Robocopy falha com **volume cheio** no lado do Servidor windows. O tiering em nuvem atua uma vez por hora para evacuar o conteúdo do disco local do Windows Server que se sincronizou. O seu objetivo é alcançar um espaço livre de 99 por cento no volume.

Deixe sincronizar o progresso e o tiering de nuvem liberte o espaço do disco. Pode observar isso no File Explorer no Windows Server.

Quando a sua instância do Windows Server tiver capacidade disponível suficiente, a reexecução do comando resolverá o problema. Nada quebra quando se entra nesta situação, e pode seguir em frente com confiança. O inconveniente de voltar a dirigir o comando é a única consequência.

Verifique o link na secção seguinte para resolver problemas de sincronização de ficheiros Azure.

## <a name="next-steps"></a>Passos seguintes

Há mais para descobrir sobre as ações de ficheiros Azure e o Azure File Sync. Os seguintes artigos contêm opções avançadas, boas práticas e ajuda para resolver problemas. Estes artigos ligam-se ao [ficheiro Azure, partilha ndo documentação](storage-files-introduction.md) conforme apropriado.

* [Visão geral do Sincronizado de Ficheiros Azure](https://aka.ms/AFS)
* [Guia de implantação de sincronização de ficheiros Azure](storage-files-deployment-guide.md)
* [Resolução de problemas do Azure File Sync](storage-sync-files-troubleshoot.md)
