---
title: No local migração nas instalações da NAS para ações de ficheiros Azure
description: Saiba como migrar ficheiros de uma localização de armazenamento (NAS) anexada à rede no local para partilhas de ficheiros Azure com a Azure DataBox.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 666e9f01d090acf29b8013470ed0264cd83f6d47
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2021
ms.locfileid: "106293639"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-azure-file-shares"></a>Utilize a DataBox para migrar de armazenamento anexado à rede (NAS) para ações de ficheiros Azure

Este artigo de migração é um dos vários que envolvem as palavras-chave NAS e Azure DataBox. Verifique se este artigo se aplica ao seu cenário:

> [!div class="checklist"]
> * Fonte de dados: Armazenamento Ligado à Rede (NAS)
> * Rota de migração: partilha de ficheiros NAS &rArr; DataBox &rArr; Azure
> * Caching ficheiros no local: Não, o objetivo final é usar as partilhas de ficheiros Azure diretamente na nuvem. Não há nenhum plano para usar o Azure File Sync.

Se o seu cenário for diferente, olhe através da [tabela de guias de migração.](storage-files-migration-overview.md#migration-guides)

Este artigo guia-o de ponta a ponta através das configurações de planeamento, implantação e networking necessárias para migrar do seu aparelho NAS para partilhas funcionais de ficheiros Azure. Este guia utiliza o Azure DataBox para o transporte de dados a granel (transporte de dados offline).

## <a name="migration-goals"></a>Objetivos de migração

O objetivo é mover as ações que tem no seu aparelho NAS para Azure e fazê-las tornarem-se partilhas de ficheiros Azure nativas que pode utilizar sem necessidade de um Servidor Windows. Esta migração tem de ser feita de forma a garantir a integridade dos dados de produção e a disponibilidade durante a migração. Este último requer manter o tempo de inatividade ao mínimo, de modo a que possa encaixar ou apenas exceder ligeiramente as janelas de manutenção regulares.

## <a name="migration-overview"></a>Descrição geral da migração

O processo de migração consiste em várias fases. Terá de implementar contas de armazenamento Azure e ações de ficheiros, configurar a rede, migrar usando a Azure DataBox, recuperar as alterações via RoboCopy e, finalmente, cortar os seus utilizadores para as ações de ficheiros Azure recém-criadas. As seguintes secções descrevem em detalhe as fases do processo de migração.

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

## <a name="phase-4-provision-a-temporary-windows-server"></a>Fase 4: Provisionamento temporário do Servidor do Windows

Enquanto espera pela chegada do seu Azure DataBox(es), já pode implementar um ou mais Servidores Windows que necessitará para executar trabalhos roboCopy. 

- A primeira utilização destes servidores será copiar ficheiros para a DataBox.
- A segunda utilização destes servidores será para recuperar as alterações que ocorreram no aparelho NAS enquanto o DataBox estava no transporte. Esta abordagem mantém o tempo de inatividade no lado da fonte ao mínimo.

A rapidez com que os seus empregos robocopia dependem principalmente destes fatores:

* IOPS na origem e armazenamento de alvos
* a largura de banda disponível entre eles </br> Saiba mais detalhes na secção de resolução de [problemas: considerações de IOPS e largura de banda](#iops-and-bandwidth-considerations)
* a capacidade de processar rapidamente ficheiros e pastas num espaço de nome </br> Veja mais detalhes na secção resolução de problemas: [Velocidade de processamento](#processing-speed)
* o número de alterações entre roboCopy corre </br> Encontre mais detalhes na secção de resolução de problemas: [Evite trabalhos desnecessários](#avoid-unnecessary-work)

É importante ter em mente os detalhes referenciados ao decidir sobre a contagem de fios RAM e thread que irá fornecer aos seus Servidores(s) temporários do Windows Server.

## <a name="phase-5-preparing-to-use-azure-file-shares"></a>Fase 5: Preparação para utilizar ações de ficheiros Azure

Para economizar tempo, deverá proceder a esta fase enquanto espera que a sua DataBox chegue. Com as informações nesta fase, poderá decidir como os seus servidores e utilizadores em Azure e fora do Azure poderão utilizar as suas ações de ficheiros Azure. As decisões mais críticas são:

- **Ligação em rede:** Ative as suas redes para encaminhar o tráfego SMB.
- **Autenticação:** Configure Azure contas de armazenamento para a autenticação Kerberos. AdConnect e Domain que se juntam à sua conta de armazenamento permitirão que as suas apps e utilizadores utilizem a sua identidade de AD para a autenticação
- **Autorização:** Os ACLs de nível de partilha para cada partilha de ficheiros Azure permitirão que os utilizadores e grupos de AD acedam a uma determinada partilha e dentro de uma partilha de ficheiros Azure, os ACLs NTFS nativos assumirão o controlo. A autorização baseada em ACLs de ficheiros e pastas funciona como funciona para as ações SMB no local.
- **Continuidade do negócio:** A integração das ações de ficheiros Azure num ambiente existente implica frequentemente preservar os endereços de ações existentes. Se ainda não está a utilizar espaços com nomes DFS, considere estabelecer isso no seu ambiente. Seria capaz de manter os endereços de partilha que os seus utilizadores e scripts usam, inalterados. Você usaria o DFS-N como um serviço de encaminhamento de espaço de nome para SMB, redirecionando DFS-Namespace alvos para ações de ficheiros Azure após a sua migração.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
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

## <a name="phase-6-copy-files-onto-your-databox"></a>Fase 6: Copiar ficheiros na sua Caixa de Dados

Quando o seu DataBox chegar, tem de configurar a sua DataBox numa linha de visão para o seu aparelho NAS. Siga a documentação de configuração do tipo DataBox que encomendou.

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

## <a name="phase-7-catch-up-robocopy-from-your-nas"></a>Fase 7: Catch-up RoboCopy do seu NAS

Assim que o DataBox informa que todos os ficheiros e pastas foram colocados nas partilhas de ficheiros Azure planeadas, pode continuar com esta fase.
Um RoboCopy de atualização só é necessário se os dados do NAS puderem ter mudado desde que a cópia DataBox foi iniciada. Em certos cenários em que usa uma parte para fins de arquivamento, poderá ser capaz de parar as alterações na partilha do seu NAS até que a migração esteja completa. Você também pode ter a capacidade de servir os seus requisitos de negócio, definindo as ações da NAS apenas para ler apenas durante a migração.

Nos casos em que é necessário uma partilha para ser lida-escrita durante a migração e só pode absorver uma pequena janela de tempo de inatividade, este passo robocopy de atualização será importante para ser concluído antes do fail-over do acesso do utilizador diretamente à partilha de ficheiros Azure.

Neste passo, irá executar trabalhos roboCopy para recuperar as suas partilhas na nuvem com as mais recentes alterações no seu NAS desde o momento em que forfurou as suas ações na DataBox.
Este RoboCopy de recuperação pode terminar rapidamente ou demorar um pouco, dependendo da quantidade de churn que aconteceu nas suas ações nas suas ações nas.

Execute a primeira cópia local para a pasta alvo do Windows Server:

1. Identifique a primeira localização do seu aparelho NAS.
1. Identifique a partilha de ficheiros Azure correspondente.
1. Monte a partilha de ficheiros Azure como uma unidade de rede local no seu Servidor Windows temporário
1. Inicie a cópia usando RoboCopy conforme descrito

### <a name="mounting-an-azure-file-share"></a>Montagem de uma partilha de ficheiros Azure

Antes de poder utilizar o RoboCopy, tem de tornar a partilha de ficheiros Azure acessível em SMB. A maneira mais fácil é montar a partilha como uma unidade de rede local para o Windows Server que está a planear usar para o RoboCopy. 

> [!IMPORTANT]
> Antes de conseguir montar com sucesso uma partilha de ficheiros Azure num Servidor Windows local, tem de ter concluído fase : Preparar-se para utilizar ações de ficheiros Azure!

Assim que estiver pronto, reveja a partilha de [ficheiros Azure com o windows how-to article](storage-how-to-use-files-windows.md) e monte a partilha de ficheiros Azure para a qual pretende iniciar o RoboCopy de atualização do NAS.

### <a name="robocopy"></a>RoboCopy

O seguinte comando RoboCopy irá copiar apenas as diferenças (ficheiros e pastas atualizados) do seu armazenamento NAS para a sua partilha de ficheiros Azure. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> [Consulte a secção de resolução de problemas](#troubleshoot) se o RoboCopy está a afetar o seu ambiente de produção, relata muitos erros ou não está a progredir tão rapidamente como o esperado.

### <a name="user-cut-over"></a>Corte de utilizador

Quando executam o comando RoboCopy pela primeira vez, os seus utilizadores e aplicações ainda estão a aceder a ficheiros no NAS e potencialmente alterá-los. É possível que o RoboCopy tenha processado um diretório, passa para o seguinte e, em seguida, um utilizador na localização de origem (NAS) adiciona, altera ou elimina um ficheiro que agora não será processado nesta atual execução do RoboCopy. Este comportamento é esperado.

A primeira corrida é sobre mover a maior parte dos dados retorcerados para a sua partilha de ficheiros Azure. Esta primeira cópia pode demorar um pouco. Consulte a [secção de resolução de problemas](#troubleshoot) para obter mais informações sobre o que pode afetar as velocidades do RoboCopy.

Uma vez concluída a execução inicial, volte a executar o comando.

Uma segunda vez que executar o RoboCopy para a mesma parte, terminará mais rápido, porque só precisa de transportar mudanças que aconteceram desde a última corrida. Podes ter empregos repetidos pela mesma parte.

Quando considerar o tempo de inatividade aceitável, tem de remover o acesso do utilizador às suas ações baseadas no NAS. Pode fazê-lo por quaisquer passos que impeçam os utilizadores de alterar a estrutura e conteúdo do ficheiro e da pasta. Um exemplo é apontar o seu DFS-Namespace para um local não existente ou alterar os ACLs de raiz na partilha.

Executar uma última rodada de RoboCopy. Vai detetar quaisquer alterações que possam ter sido perdidas.
Quanto tempo este passo final leva, depende da velocidade do scan RoboCopy. Pode estimar o tempo (que é igual ao seu tempo de inatividade) medindo o tempo que a execução anterior demorou.

Crie uma partilha na pasta do Servidor do Windows e, possivelmente, ajuste a sua implementação DFS-N para a apontar. Certifique-se de definir as mesmas permissões de nível de partilha que na sua parte NAS SMB. Se tiver um NAS de domínio de classe empresarial, então os SIDs do utilizador corresponderão automaticamente à medida que os utilizadores existem no Ative Directory e roboCopy copiam ficheiros e metadados com total fidelidade. Se utilizou utilizadores locais no seu NAS, precisa de recriar estes utilizadores como utilizadores locais do Windows Server e mapear os SIDs existentes roboCopy transferidos para o seu Servidor do Windows para os SIDs dos seus novos utilizadores locais do Windows Server.

Terminou de migrar uma parte/grupo de ações numa raiz ou volume comum. (Dependendo do seu mapeamento da Fase 1)

Podes tentar executar algumas destas cópias em paralelo. Recomendamos o processamento do âmbito de uma partilha de ficheiros Azure de cada vez.

## <a name="troubleshoot"></a>Resolução de problemas

A velocidade e a taxa de sucesso de uma determinada execução roboCopy dependerá de vários fatores:

* IOPS na origem e armazenamento de alvos
* a largura de banda disponível entre eles
* a capacidade de processar rapidamente ficheiros e pastas num espaço de nome
* o número de alterações entre roboCopy corre


### <a name="iops-and-bandwidth-considerations"></a>IOPS e considerações de largura de banda

Nesta categoria é necessário considerar as capacidades da **fonte** (o seu NAS), o **alvo** (Azure DataBox e mais tarde a partilha de ficheiros Azure) e a **rede** que os liga. A produção máxima possível é determinada pelo mais lento destes três componentes. Um DataBox padrão vem com interfaces de rede duplos de 10 Gbps. Dependendo do seu NAS, pode ser capaz de comparar isso. Certifique-se de que a sua infraestrutura de rede está configurada para suportar velocidades de transferência ideais para as suas melhores capacidades.

> [!CAUTION]
> Embora a cópia o mais rápido possível seja muitas vezes a mais desejada, considere a utilização da sua rede local e do aparelho NAS para outras tarefas críticas, muitas vezes empresariais.

Copiar o mais rapidamente possível pode não ser desejável quando existe o risco de a migração poder monopolizar os recursos disponíveis.

* Considere quando é melhor no seu ambiente executar migrações: durante o dia, fora de horas ou durante os fins de semana.
* Considere também o QoS de networking num Servidor do Windows para acelerar a velocidade roboCopy e, assim, o impacto no NAS e na rede.
* Evite trabalhos desnecessários para as ferramentas de migração.

O próprio RobCopy também tem a capacidade de inserir atrasos inter-pacotes especificando o `/IPG:n` interruptor onde é medido em `n` milissegundos entre pacotes RoboCopy. A utilização deste interruptor pode ajudar a evitar a monopolização de recursos em ambos os dispositivos NAS restritos da IO e ligações de rede altamente utilizadas. 

`/IPG:n` não pode ser utilizado para um estrangulamento preciso da rede a um determinado Mbps. Utilize o QoS da Rede de Servidor do Windows. A RoboCopy baseia-se inteiramente no protocolo SMB para toda a rede e, portanto, não tem a capacidade de influenciar a própria produção da rede, mas pode abrandar a sua utilização. 

Uma linha de pensamento semelhante aplica-se ao IOPS observado no NAS. O tamanho do cluster no volume NAS, tamanhos de pacotes e uma variedade de outros fatores influenciam o IOPS observado. Introduzir atrasos entre pacotes é muitas vezes a forma mais fácil de controlar a carga no NAS. Teste vários valores, por exemplo, de cerca de 20 milissegundos (n=20) a múltiplos disso para ver quanto atraso permite que os seus outros requisitos sejam reparados, mantendo a velocidade RoboCopy no máximo para as suas restrições.

### <a name="processing-speed"></a>Velocidade de processamento

O RoboCopy atravessará o espaço de nome a que é apontado e avaliará cada ficheiro e pasta para cópia. Cada ficheiro será avaliado durante uma cópia inicial, como uma cópia sobre a rede local para uma DataBox, e mesmo durante cópias de captura sobre o link WAN para uma partilha de ficheiros Azure.

Muitas vezes, não consideramos a largura de banda o fator mais limitativo numa migração - o que pode ser verdade. Mas a capacidade de enumerar um espaço de nome pode influenciar o tempo total de copiar ainda mais para espaços de nome maiores com ficheiros menores. Considere-se que copiar 1 TiB de pequenos ficheiros levará consideravelmente mais tempo do que copiar 1 TiB de menos, mas maiores ficheiros - tendo em conta que todas as outras variáveis são as mesmas.

A causa para esta diferença é o poder de processamento necessário para andar por um espaço de nome. O RoboCopy suporta cópias multi-roscadas através do `/MT:n` parâmetro onde n representa o número de fios do processador. Assim, ao fornecer uma máquina especificamente para roboCopy, considere o número de núcleos de processador e a sua relação com a contagem de fios que eles fornecem. Mais comum são dois fios por núcleo. A contagem de núcleo e de rosca de uma máquina é um ponto de dados importante para decidir quais os valores multi-thread `/MT:n` que deve especificar. Considere também quantos empregos robocopia você pretende executar em paralelo numa determinada máquina.

Mais fios copiarão o nosso exemplo 1Tib de pequenos ficheiros consideravelmente mais rápido do que menos fios. Ao mesmo tempo, há uma diminuição do retorno do investimento no nosso 1Tib de ficheiros maiores. Continuarão a copiar mais rapidamente quanto mais linhas atribuir, mas está a tornar-se mais provável que seja a largura de banda da rede ou a IO limitada.

### <a name="avoid-unnecessary-work"></a>Evitar trabalhos desnecessários

Evite alterações em larga escala no seu espaço de nome. Isto inclui mover ficheiros entre diretórios, alterar propriedades em larga escala, ou alterar permissões (NTFS ACLs) porque muitas vezes têm um efeito de mudança em cascata quando os ACLs de pasta mais próximos da raiz de uma ação são alterados. As consequências podem ser:

* tempo de funcionamento prolongado do roboCopy devido a cada ficheiro e pasta afetado por uma alteração ACL que precisa de ser atualizada
* a eficácia da utilização do DataBox pode diminuir quando as estruturas das pastas mudam após os ficheiros terem sido copiados para uma DataBox. Um trabalho roboCopy não será capaz de "reproduzir" uma mudança de espaço de nome e, em vez disso, terá de expurgar os ficheiros transportados para uma partilha de ficheiros Azure e enviar novamente os ficheiros da nova estrutura de pasta para o Azure.

Outro aspeto importante é utilizar eficazmente a ferramenta RoboCopy. Com o script RoboCopy recomendado, irá criar e guardar um ficheiro de registo para erros. Podem ocorrer erros de cópia - isto é normal. Estes erros muitas vezes tornam necessário executar várias rondas de uma ferramenta de cópia como roboCopy. Uma execução inicial, por exemplo, do NAS para o DataBox, e uma ou mais extras com o interruptor /MIR para capturar e revassar ficheiros que não foram copiados.

Você deve estar preparado para executar várias rondas de RoboCopy contra um determinado âmbito de espaço de nome. As sucessivas corridas terminarão mais rapidamente, uma vez que têm menos para copiar, mas são cada vez mais limitadas pela velocidade de processamento do espaço de nome. Quando fizer várias rondas, pode acelerar cada ronda sem que o RoboCopy tente de forma irracional para copiar tudo à primeira tentativa. Estes interruptores RoboCopy podem fazer uma diferença significativa:

* `/R:n` n = quantas vezes se recandid copiar um ficheiro falhado e 
* `/W:n` n = quantos segundos para esperar entre retróstas

`/R:5 /W:5` é uma definição razoável que pode ajustar ao seu gosto. Neste exemplo, um ficheiro falhado será novamente julgado cinco vezes, com um tempo de espera de cinco segundos entre as retrórias. Se o ficheiro ainda não conseguir copiar, o próximo trabalho do RoboCopy tentará novamente e muitas vezes os ficheiros que falharam porque estão a ser utilizados ou devido a problemas de tempo limite podem eventualmente ser copiados com sucesso desta forma.


## <a name="next-steps"></a>Passos seguintes

Há mais a descobrir sobre as ações de ficheiros Azure. Os seguintes artigos ajudam a compreender opções avançadas, boas práticas, e também contêm ajuda para resolver problemas. Estes artigos ligam-se à [documentação de partilha de ficheiros Azure](storage-files-introduction.md) conforme apropriado.

* [Descrição geral da migração](storage-files-migration-overview.md)
* [Monitorizar, diagnosticar e resolver problemas do Armazenamento do Microsoft Azure (Monitor, diagnose, and troubleshoot Microsoft Azure Storage)](../common/storage-monitoring-diagnosing-troubleshooting.md)
* [Considerações de rede para acesso direto](storage-files-networking-overview.md)
* [Backup: Azure file share snapshots](storage-snapshots-files.md)
