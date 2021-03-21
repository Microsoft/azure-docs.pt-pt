---
title: Migração nas instalações da NAS para Azure File Sync via Azure DataBox
description: Saiba como migrar ficheiros de uma localização de Armazenamento (NAS) anexado à rede no local para uma implementação em nuvem híbrida com Azure File Sync via Azure DataBox.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 144b2f23e40f315441c3de2482ae8aeffe77ec75
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584067"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Utilize a DataBox para migrar do Armazenamento Ligado à Rede (NAS) para uma implementação em nuvem híbrida com Azure File Sync

Este artigo de migração é um dos vários que envolvem as palavras-chave NAS, Azure File Sync e Azure DataBox. Verifique se este artigo se aplica ao seu cenário:

> [!div class="checklist"]
> * Fonte de dados: Armazenamento Ligado à Rede (NAS)
> * Rota de migração: Nos &rArr; DataBox &rArr; Azure file share &rArr; sync with Windows Server
> * Caching ficheiros no local: Sim, o objetivo final é uma implementação de Azure File Sync.

Se o seu cenário for diferente, olhe através da [tabela de guias de migração.](storage-files-migration-overview.md#migration-guides)

O Azure File Sync funciona em locais de armazenamento direto anexado (DAS) e não suporta sincronização em locais de armazenamento anexado à rede (NAS).
Este facto torna necessária uma migração dos seus ficheiros e este artigo guia-o através do planeamento e execução de tal migração.

## <a name="migration-goals"></a>Objetivos de migração

O objetivo é mover as ações que tem no seu aparelho NAS para um Servidor Windows. Em seguida, utilize o Azure File Sync para uma implementação de nuvem híbrida. Esta migração tem de ser feita de forma a garantir a integridade dos dados de produção e a disponibilidade durante a migração. Este último requer manter o tempo de inatividade ao mínimo, de modo a que possa encaixar ou apenas exceder ligeiramente as janelas de manutenção regulares.

## <a name="migration-overview"></a>Descrição geral da migração

O processo de migração consiste em várias fases. Terá de implementar contas de armazenamento Azure e partilhas de ficheiros, implementar um Windows Server no local, configurar o Azure File Sync, migrar usando o RoboCopy e, finalmente, fazer o corte. As seguintes secções descrevem em detalhe as fases do processo de migração.

> [!TIP]
> Se estiver a regressar a este artigo, utilize a navegação do lado direito para saltar para a fase de migração onde paraste.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fase 1: Identifique quantas ações de ficheiroS Azure precisa

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>Fase 2: Implantar recursos de armazenamento Azure

Nesta fase, consulte a tabela de mapeamento da Fase 1 e use-a para prever o número correto de contas de armazenamento Azure e ações de ficheiro dentro delas.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>Fase 3: Determinar quantos aparelhos Azure DataBox precisa

Inicie este passo apenas, depois de ter concluído a fase anterior. Os seus recursos de armazenamento Azure (contas de armazenamento e ações de ficheiros) devem ser criados neste momento. Durante a sua encomenda DataBox, tem de especificar em que contas de armazenamento o DataBox está a mover dados.

Nesta fase, é necessário mapear os resultados do plano de migração da fase anterior até aos limites das opções disponíveis da DataBox. Estas considerações irão ajudá-lo a fazer um plano para as opções dataBox que deve escolher e quantas delas necessitará de transferir as suas ações nas para ações de ficheiros Azure.

Para determinar quantos dispositivos precisa, considere estes limites importantes:

* Qualquer Azure DataBox pode mover dados para até 10 contas de armazenamento. 
* Cada opção DataBox tem a sua própria capacidade utilizável. Consulte as [opções DataBox](#databox-options).

Consulte o seu plano de migração para o número de contas de armazenamento que decidiu criar e as ações em cada uma delas. Então olhe para o tamanho de cada uma das ações do seu NAS. Combinar estas informações permitir-lhe-á otimizar e decidir que aparelho deve enviar dados para as contas de armazenamento. Pode ter dois dispositivos DataBox a mover ficheiros para a mesma conta de armazenamento, mas não divida o conteúdo de uma única partilha de ficheiros através de 2 DataBoxes.

### <a name="databox-options"></a>Opções dataBox

Para uma migração padrão, uma ou uma combinação destas três opções DataBox devem ser escolhidas: 

* DataBox Discos A Microsoft enviar-lhe-á um e até cinco discos SSD com uma capacidade de 8 TiB cada, para um total máximo de 40 TiB. A capacidade utilizável é cerca de 20% menor, devido à encriptação e à sobrecarga do sistema de ficheiros. Para obter mais informações, consulte a [documentação dataBox Disks](../../databox/data-box-disk-overview.md).
* DataBox Esta é a opção mais comum. Um aparelho DataBox robusto, que funciona semelhante a um NAS, será enviado para si. Tem uma capacidade utilizável de 80 TiB. Para mais informações, consulte [a documentação dataBox](../../databox/data-box-overview.md).
* DataBox Heavy Esta opção apresenta um aparelho DataBox robusto sobre rodas, que funciona semelhante a um NAS, com uma capacidade de 1 PiB. A capacidade utilizável é cerca de 20% menor, devido à encriptação e à sobrecarga do sistema de ficheiros. Para obter mais informações, consulte [a documentação da DataBox Heavy](../../databox/data-box-heavy-overview.md).

## <a name="phase-4-provision-a-suitable-windows-server-on-premises"></a>Fase 4: Provisionar um servidor do Windows adequado no local

Enquanto espera pela chegada do seu Azure DataBox(es), já pode começar a rever as necessidades de um ou mais Servidores windows que irá utilizar com o Azure File Sync.

* Crie um Windows Server 2019 - no mínimo 2012R2 - como uma máquina virtual ou servidor físico. Um cluster de falha do Windows Server também é suportado.
* Provisão ou adicionar Armazenamento Direto Anexado (DAS em comparação com o NAS, que não é suportado).

A configuração de recursos (computação e RAM) do Servidor do Windows que implementa depende principalmente do número de itens (ficheiros e pastas) que estará a sincronizar. Recomenda-se uma configuração de desempenho mais elevada se tiver alguma preocupação.

[Saiba como dimensionar um Servidor do Windows com base no número de itens (ficheiros e pastas) que necessita para sincronizar.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> O artigo anteriormente ligado apresenta uma tabela com um intervalo para memória do servidor (RAM). Pode orientar-se para o número menor para o seu servidor, mas espera-se que a sincronização inicial possa demorar significativamente mais tempo.

## <a name="phase-5-copy-files-onto-your-databox"></a>Fase 5: Copiar ficheiros na sua Caixa de Dados

Quando o seu DataBox chegar, tem de configurar a Sua Caixa de Dados na linha de visão para o seu aparelho NAS. Siga a documentação de configuração do tipo DataBox que encomendou.

* [Configurar o Data Box](../../databox/data-box-quickstart-portal.md)
* [Configurar o Data Box Disk](../../databox/data-box-disk-quickstart-portal.md)
* [Configurar o Data Box Heavy](../../databox/data-box-heavy-quickstart-portal.md)

Dependendo do tipo DataBox, talvez existam ferramentas de cópia DataBox disponíveis para si. Neste momento, não são recomendados para migrações para ações de ficheiros Azure, uma vez que não copiam os seus ficheiros com total fidelidade à DataBox. Use robocopy em vez disso.

Quando o seu DataBox chegar, terá ações SMB pré-aussadas disponíveis para cada conta de armazenamento especificada no momento da sua encomenda.

* Se os seus ficheiros forem para uma partilha de ficheiros Azure premium, haverá uma parte SMB por conta de armazenamento de "armazenamento de ficheiros" premium.
* Se os seus ficheiros entrarem numa conta de armazenamento padrão, haverá três ações SMB por padrão (GPv1 e GPv2). Apenas as ações de ficheiro com que `_AzFiles` terminam são relevantes para a sua migração. Ignore qualquer partilha de blocos e blob de página.

Siga os passos na documentação Azure DataBox:

1. [Ligar ao Data Box](../../databox/data-box-deploy-copy-data.md)
1. Copiar dados para o Data Box
1. [Prepare a sua Caixa de Dados para a partida para Azure](../../databox/data-box-deploy-picked-up.md)

A documentação ligada da DataBox especifica um comando RoboCopy. No entanto, o comando não é adequado para preservar a fidelidade completa do ficheiro e da pasta. Utilize este comando em vez disso:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>Fase 6: Implementar o recurso cloud Azure File Sync

Antes de continuar com este guia, aguarde até que todos os seus ficheiros tenham chegado nas partilhas de ficheiros Azure corretas. O processo de envio e ingestão de dados da DataBox levará tempo.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>Fase 7: Implementar o agente Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server"></a>Fase 8: Configurar o Ficheiro Azure no Servidor do Windows

O seu Windows Server registado no local deve estar pronto e ligado à internet para este processo.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

Ligue a função de tiering da nuvem e selecione "Namespace only" na secção inicial de descarregamento.

> [!IMPORTANT]
> O tiering em nuvem é a funcionalidade AFS que permite ao servidor local ter menos capacidade de armazenamento do que é armazenado na nuvem, mas tem o espaço de nome completo disponível. Os dados locais interessantes também são cached localmente para o desempenho de acesso rápido. O tiering em nuvem é uma funcionalidade opcional por Azure File Sync "ponto final do servidor". Tem de utilizar esta funcionalidade se não tiver capacidade de disco local suficiente no Windows Server para guardar todos os dados da cloud e se pretender evitar descarregar todos os dados da nuvem!

Repita os passos da criação do grupo de sincronização e a adição da pasta do servidor correspondente como ponto final do servidor para todas as partilhas de ficheiros /localizações do servidor Azure, que precisam de ser configuradas para sincronização. Aguarde até que a sincronização do espaço de nome esteja completa. A secção seguinte irá detalhar como pode garantir isso.

> [!NOTE]
> Após a criação de um ponto final do servidor, a sincronização está a funcionar. No entanto, a sincronização precisa de enumerar (descobrir) os ficheiros e pastas que moveu através da DataBox para a partilha de ficheiros Azure. Dependendo do tamanho do espaço de nome, isto pode demorar um tempo significativo antes que o espaço de nome da nuvem comece a aparecer no servidor.

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>Fase 9: Aguarde que o espaço de nome apareça completamente no servidor

É imperativo que espere com os próximos passos da sua migração que o servidor tenha descarregado completamente o espaço de nome da partilha de nuvem. Se começar a mover ficheiros muito cedo para o servidor, pode arriscar uploads desnecessários e até mesmo arquivar conflitos.

Para saber se o seu servidor completou a sincronização inicial de descarregamento, abra o Visualizador de Eventos no seu Servidor de Sincronização do Windows e utilize o registo de eventos de telemetria Azure File Sync.
O registo de eventos de telemetria está localizado no Visualizador de Eventos em Aplicações e Serviços\Microsoft\FileSync\Agent.

Procure o mais recente evento 9102. O ID 9102 do evento é registado assim que uma sessão de sincronização estiver concluída. No caso de texto lá, é um campo para a direção de sincronização de descarregamento. (precisa `HResult` de ser zero e os ficheiros descarregados também).
 
Você deseja ver dois eventos consecutivos deste tipo e conteúdo para dizer que o servidor terminou de descarregar o espaço de nome. Tudo bem se houver eventos diferentes disparando entre dois eventos 9102.

## <a name="phase-10-catch-up-robocopy-from-your-nas"></a>Fase 10: Catch-up RoboCopy do seu NAS

Uma vez que o seu servidor tenha concluído a sincronização inicial de todo o espaço de nome a partir da partilha de nuvem, pode prosseguir com este passo. É imperativo que este passo esteja completo, antes que continue com este passo. Consulte a secção anterior para mais detalhes.

Neste passo, irá executar trabalhos roboCopy para recuperar as suas partilhas na nuvem com as mais recentes alterações no seu NAS desde o momento em que forfurou as suas ações na DataBox.
Este RoboCopy de recuperação pode terminar rapidamente ou demorar um pouco, dependendo da quantidade de churn que aconteceu nas suas ações nas suas ações nas.

> [!WARNING]
> Devido a um comportamento de RoboCopy regredido no Windows Server 2019, o interruptor /MIR do RoboCopy não é compatível com um directório-alvo hierárquico. Não deve utilizar o cliente Windows Server 2019 ou Windows 10 para esta fase da migração. Utilize o RoboCopy num Windows Server intermédio 2016.

A abordagem básica de migração é um RoboCopy do seu aparelho NAS para o seu Windows Server, e Azure File Sync para ações de ficheiros Azure.

Execute a primeira cópia local para a pasta alvo do Windows Server:

1. Identifique a primeira localização do seu aparelho NAS.
1. Identifique a pasta correspondente no Servidor do Windows, que já tem O Azure File Sync configurado no mesmo.
1. Inicie a cópia usando RoboCopy

O seguinte comando RoboCopy irá copiar apenas as diferenças (ficheiros e pastas atualizados) do seu armazenamento NAS para a pasta alvo do Windows Server. Em seguida, o Windows Server sincroniza-os com a partilha de ficheiros Azure. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

Se fornequir menos armazenamento no seu Windows Server do que os seus ficheiros ocuparem o aparelho NAS, então tem um nível de nuvem configurado. À medida que o volume do Servidor do Windows local fica cheio, [o tiering](storage-sync-cloud-tiering-overview.md) da nuvem entrará em edição de ficheiros que já tenham sincronizado com sucesso. O tiering da nuvem gerará espaço suficiente para continuar a cópia do aparelho NAS. O tiering da nuvem verifica uma vez por hora para ver o que sincronizou e para libertar o espaço do disco para alcançar o espaço livre de volume de 99%.
É possível que o RoboCopy precise de mover inúmeros ficheiros, mais do que o armazenamento local para o Windows Server. Em média, pode esperar que o RoboCopy se mova muito mais rapidamente do que o Azure File Sync pode enviar os seus ficheiros e tier-los do volume local. RoboCopy vai falhar. Recomenda-se que trabalhe através das ações numa sequência que o impeça. Por exemplo, não iniciar trabalhos roboCopy para todas as ações ao mesmo tempo, ou apenas mover ações que se encaixam na quantidade atual de espaço livre no Windows Server, para mencionar algumas. A boa notícia é que o interruptor /MIR só moverá deltas e uma vez que um delta tenha sido movido, um trabalho reiniciado não precisará mover este ficheiro novamente.

### <a name="user-cut-over"></a>Corte de utilizador

Quando executam o comando RoboCopy pela primeira vez, os seus utilizadores e aplicações ainda estão a aceder a ficheiros no NAS e potencialmente alterá-los. É possível que o RoboCopy tenha processado um diretório, passa para o seguinte e, em seguida, um utilizador na localização de origem (NAS) adiciona, altera ou elimina um ficheiro que agora não será processado nesta atual execução do RoboCopy. Este comportamento é esperado.

A primeira execução é sobre mover a maior parte dos dados retorcidas para o seu Servidor do Windows e para a nuvem através do Azure File Sync. Esta primeira cópia pode demorar muito tempo, dependendo de:

* a largura de banda de upload
* a velocidade da rede local e o número de quão idealmente o número de fios RoboCopy corresponde a
* o número de itens (ficheiros e pastas), que precisam de ser processados pela RoboCopy e pela Azure File Sync

Uma vez concluída a execução inicial, volte a executar o comando.

Uma segunda vez que executar o RoboCopy para a mesma parte, terminará mais rápido, porque só precisa de transportar mudanças que aconteceram desde a última corrida. Podes ter empregos repetidos pela mesma parte.

Quando considerar o tempo de inatividade aceitável, tem de remover o acesso do utilizador às suas ações baseadas no NAS. Pode fazê-lo por quaisquer passos que impeçam os utilizadores de alterar a estrutura e conteúdo do ficheiro e da pasta. Um exemplo é apontar o seu DFS-Namespace para um local não existente ou alterar os ACLs de raiz na partilha.

Executar uma última rodada de RoboCopy. Vai detetar quaisquer alterações que possam ter sido perdidas.
Quanto tempo este passo final leva, depende da velocidade do scan RoboCopy. Pode estimar o tempo (que é igual ao seu tempo de inatividade) medindo o tempo que a execução anterior demorou.

Crie uma partilha na pasta do Servidor do Windows e, possivelmente, ajuste a sua implementação DFS-N para a apontar. Certifique-se de definir as mesmas permissões de nível de partilha que na sua parte NAS SMB. Se tiver um NAS de domínio de classe empresarial, então os SIDs do utilizador corresponderão automaticamente à medida que os utilizadores existem no Ative Directory e roboCopy copiam ficheiros e metadados com total fidelidade. Se utilizou utilizadores locais no seu NAS, precisa de recriar estes utilizadores como utilizadores locais do Windows Server e mapear os SIDs existentes roboCopy transferidos para o seu Servidor do Windows para os SIDs dos seus novos utilizadores locais do Windows Server.

Terminou de migrar uma parte/grupo de ações numa raiz ou volume comum. (Dependendo do seu mapeamento da Fase 1)

Podes tentar executar algumas destas cópias em paralelo. Recomendamos o processamento do âmbito de uma partilha de ficheiros Azure de cada vez.

## <a name="troubleshoot"></a>Resolução de problemas

O problema mais provável é que o comando RoboCopy falhe com *o "Volume cheio"* no lado do Windows Server. O nível da nuvem atua uma vez a cada hora para evacuar o conteúdo do disco local do Windows Server, que foi sincronizado. O seu objetivo é alcançar o seu espaço livre de 99% no volume.

Deixe sincronizar o progresso e o tiering da nuvem libertar o espaço do disco. Pode observar isso no Explorador de Ficheiros no seu Servidor windows.

Quando o seu Servidor do Windows tiver capacidade disponível suficiente, a reexecução do comando resolverá o problema. Nada se rompe quando se entra nesta situação e se pode avançar com confiança. O inconveniente de voltar a comandar o comando é a única consequência.

Verifique o link na secção seguinte para verificar problemas de resolução de problemas problemas com o Azure File Sync.

## <a name="next-steps"></a>Passos seguintes

Há mais a descobrir sobre as ações de ficheiros Azure e a Azure File Sync. Os seguintes artigos ajudam a compreender opções avançadas, boas práticas, e também contêm ajuda para resolver problemas. Estes artigos ligam-se à [documentação de partilha de ficheiros Azure](storage-files-introduction.md) conforme apropriado.

* [Descrição geral da migração](storage-files-migration-overview.md)
* [Visão geral da AFS](./storage-sync-files-planning.md)
* [Guia de implantação da AFS](./storage-how-to-create-file-share.md)
* [Resolução de problemas da AFS](storage-sync-files-troubleshoot.md)
