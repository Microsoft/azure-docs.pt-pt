---
title: Como funcionam os instantâneos do Azure NetApp Files Microsoft Docs
description: Explica como funcionam os instantâneos do Azure NetApp Files, incluindo formas de criar instantâneos, formas de restaurar instantâneos, como usar instantâneos em configurações de replicação entre regiões.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/11/2021
ms.author: b-juche
ms.openlocfilehash: 4d21f7c4e74a87e409a73b22fc6b316e97e24a4e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122609"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Como funcionam as fotos dos ficheiros Azure NetApp

Este artigo explica como funcionam as fotos do Azure NetApp Files. A tecnologia snapshot Azure NetApp Files proporciona estabilidade, escalabilidade e recuperabilidade mais rápida, sem impacto no desempenho. A tecnologia Azure NetApp Files snapshot fornece a base para soluções de proteção de dados, incluindo restauros de ficheiros únicos, restauros de volume e clones e replicação transversal. 

Para obter medidas sobre a utilização de instantâneos de volume, consulte [Gerir as imagens utilizando ficheiros Azure NetApp](azure-netapp-files-manage-snapshots.md). Para obter considerações sobre a gestão instantânea na replicação entre regiões, consulte [requisitos e considerações para a utilização de replicações entre regiões.](cross-region-replication-requirements-considerations.md)

## <a name="what-volume-snapshots-are"></a>Que volume são instantâneos  

Um instantâneo Azure NetApp Files é uma imagem do sistema de ficheiros (volume) do sistema de ficheiros pontual. Pode servir como um backup online ideal. Pode utilizar uma imagem instantânea para [criar um novo volume,](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume) [restaurar um ficheiro,](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)ou [reverter um volume](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert). Em dados específicos da aplicação armazenados nos volumes do Azure NetApp Files, poderão ser necessários passos adicionais para garantir a consistência da aplicação.  

As imagens de baixo custo são possíveis pelas características únicas da tecnologia de virtualização de volume de subposição que faz parte dos Ficheiros Azure NetApp. Como uma base de dados, esta camada usa ponteiros para os blocos de dados reais no disco. Mas, ao contrário de uma base de dados, não reescreve os blocos existentes; escreve dados atualizados para um novo bloco e altera o ponteiro. Um instantâneo Azure NetApp Files simplesmente manipula ponteiros de blocos, criando uma visão "congelada", apenas de leitura de um volume que permite às aplicações aceder a versões mais antigas de ficheiros e hierarquias de diretório sem programação especial. Os blocos de dados reais não são copiados. Como tal, os instantâneos são eficientes no tempo necessário para criá-los; são quase instantâneos, independentemente do tamanho do volume. As fotos instantâneas também são eficientes no espaço de armazenamento. Eles próprios não consomem espaço, e apenas blocos delta entre instantâneos e volume ativo são mantidos. 

Os seguintes diagramas ilustram os conceitos:  

![Diagramas que mostram os conceitos-chave dos instantâneos](../media/azure-netapp-files/snapshot-concepts.png)

Nos diagramas acima, o instantâneo é tirado na Figura 1a. Na Figura 1b, os dados alterados são escritos para um *novo bloco* e o ponteiro é atualizado. Mas o ponteiro instantâneo ainda aponta para o *bloco previamente escrito,* dando-lhe uma visão ao vivo e histórica dos dados. Outra fotografia é tirada na Figura 1c. Agora tem acesso a três gerações de dados (os dados ao vivo, Snapshot 2 e Snapshot 1, por ordem de idade), sem ocupar o espaço de volume que três cópias completas exigiriam. 

Um instantâneo leva apenas uma cópia dos metadados de volume *(tabela inode).* Leva apenas alguns segundos para criar, independentemente do tamanho do volume, da capacidade utilizada, ou do nível de atividade no volume. Assim, tirar uma foto de um volume de 100 TiB leva o mesmo tempo (quase a zero) que tirar uma foto de um volume de 100 GiB. Após a criação de uma imagem instantânea, as alterações nos ficheiros de dados refletem-se na versão ativa dos ficheiros, como é normal.

Entretanto, os blocos de dados apontados a partir de um instantâneo permanecem estáveis e imutáveis. Devido à natureza "Redirecionamento na Escrita" dos instantâneos do Azure NetApp Files, um instantâneo não incorre em nenhuma sobrecarga de desempenho e por si só não consome nenhum espaço. Pode armazenar até 255 instantâneos por volume ao longo do tempo, todos acessíveis como versões apenas de leitura e online dos dados, consumindo tão pouca capacidade quanto o número de blocos alterados entre cada instantâneo. Os blocos modificados são armazenados no volume ativo. Os blocos apontados em instantâneos são mantidos (apenas para leitura) no volume de segurança, a serem reutilizados apenas quando todas as imagens (ponteiros) tiverem sido limpas. Portanto, a utilização do volume aumentará ao longo do tempo, quer por novos blocos de dados, quer por blocos de dados (modificados) mantidos em instantâneos.

 O seguinte diagrama mostra as fotos de um volume e o espaço usado ao longo do tempo: 

![Diagrama que mostra os instantâneos de um volume e espaço usado ao longo do tempo](../media/azure-netapp-files/snapshots-used-space-over-time.png)

Uma vez que um instantâneo de volume regista apenas as alterações do bloco desde o último instantâneo, fornece os seguintes benefícios principais:

* As fotos são ***armazenamento eficiente** _.   
    As imagens consomem espaço de armazenamento mínimo porque não copiam os blocos de dados de todo o volume. Duas imagens tomadas em sequência diferem apenas pelos blocos adicionados ou alterados no intervalo de tempo entre os dois. Este comportamento incremental de bloco limita o consumo de capacidade de armazenamento associado. Muitas implementações instantâneas alternativas consomem volumes de armazenamento iguais ao sistema de ficheiros ativos, aumentando os requisitos de capacidade de armazenamento. Dependendo das taxas diárias de alteração de _block nível de aplicação, os instantâneos do Azure NetApp Files consumirão mais ou menos capacidade, mas apenas em dados alterados. O consumo médio diário de instantâneos varia de apenas 1 a 5% da capacidade de volume usado para muitos volumes de aplicações, ou até 20-30% para volumes como volumes de base de dados SAP HANA. Certifique-se de [que monitoriza o seu volume e utilização instantânea](azure-netapp-files-metrics.md#volumes) para consumo de capacidade instantânea em relação ao número de instantâneos criados e mantidos.   

* As imagens são ***rápidas para criar, replicar, restaurar ou clonar** _.   
    Leva apenas alguns segundos para criar, replicar, restaurar ou clonar um instantâneo, independentemente do tamanho e nível de atividades de volume. Pode criar uma imagem instantânea de volume [a pedido](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume). Também pode utilizar [políticas instantâneas](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) para especificar quando os Ficheiros Azure NetApp devem criar automaticamente um instantâneo e quantas imagens devem guardar para um volume.  A consistência da aplicação pode ser alcançada orquestrando instantâneos com a camada de aplicação, por exemplo, usando a [ferramenta AzAcSnap](azacsnap-introduction.md) para SAP HANA.

_ As snapshots não têm impacto no volume ***desempenho** _.   
    Devido à natureza "Redirecionamento na Escrita" da tecnologia de subsedição, armazenar ou reter os instantâneos do Azure NetApp Files não tem impacto no desempenho, mesmo com uma atividade de dados pesados. A eliminação de um instantâneo também tem pouco ou nenhum impacto no desempenho em muitos casos. 

_ Os instantâneos proporcionam ***escalabilidade** _ porque podem ser criados com frequência e muitos podem ser retidos.   
    Os volumes Azure NetApp Files suportam até 255 instantâneos. A capacidade de armazenar um grande número de instantâneos de baixo impacto, frequentemente criados, aumenta a probabilidade de a versão desejada de dados poder ser recuperada com sucesso.

_ As imagens instantâneas proporcionam ***visibilidade do utilizador** _ e _*_a recuperação de ficheiros_*_.   
O alto desempenho, escalabilidade e estabilidade da tecnologia snapshot Azure NetApp Files significa que fornece uma cópia de segurança online ideal para a recuperação orientada para o utilizador. As imagens instantâneas podem ser tornadas acessíveis ao utilizador para fins de utilização de ficheiros, diretórios ou restauro de volume. As soluções adicionais permitem-lhe copiar backups para armazenamento offline ou [replicar cross-region](cross-region-replication-introduction.md) para fins de retenção ou recuperação de desastres.

## <a name="ways-to-create-snapshots"></a>Formas de criar instantâneos   

As fotos do Azure NetApp Files são versáteis em uso. Como tal, estão disponíveis vários métodos para criar e manter instantâneos:

_ Manualmente (a pedido), utilizando:   
    * O [portal Azure](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume), [REST API,](/rest/api/netapp/snapshots) [Azure CLI,](/cli/azure/netappfiles/snapshot)ou [ferramentas PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot)
    * Scripts (ver [exemplos)](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts)

* Automatizado, utilizando:
    * Políticas instantâneas, através do [portal Azure](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies), [REST API,](/rest/api/netapp/snapshotpolicies) [Azure CLI,](/cli/azure/netappfiles/snapshot/policy)ou [ferramentas PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy)
    * Ferramenta de instantâneo consistente de aplicação, como [AzAcSnap](azacsnap-introduction.md)

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>Como volumes e instantâneos são replicados entre regiões para DR  

O Azure NetApp Files suporta [a replicação transversal](cross-region-replication-introduction.md) para fins de recuperação de desastres (DR). A azure NetApp Files replicação cross-region usa a tecnologia SnapMirror. Apenas os blocos alterados são enviados pela rede num formato comprimido e eficiente. Após o início de uma replicação entre volumes, todo o conteúdo do volume (isto é, os blocos de dados armazenados efetivos) é transferido apenas uma vez. Esta operação *chama-se transferência de base.* Após a transferência inicial, apenas os blocos alterados (como capturados em instantâneos) são transferidos. É criada uma réplica assíncronea do volume de origem (incluindo todos os instantâneos).  Este comportamento segue um mecanismo de replicação para sempre completo e incremental. Esta tecnologia proprietária minimiza a quantidade de dados necessários para se replicar em todas as regiões, poupando assim os custos de transferência de dados. Também encurta o tempo de replicação. Você pode alcançar um objetivo de ponto de recuperação menor (RPO), porque mais instantâneos podem ser criados e transferidos com mais freqüentemente com transferências de dados limitadas.

O diagrama que se segue mostra o tráfego instantâneo em cenários de replicação entre regiões: 

![Diagrama que mostra tráfego instantâneo em cenários de replicação entre regiões](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="ways-to-restore-data-from-snapshots"></a>Formas de restaurar os dados a partir de instantâneos  

A tecnologia de snapshot Azure NetApp Files melhora consideravelmente a frequência e a fiabilidade das cópias de segurança. Incorre numa sobrecarga mínima de desempenho e pode ser criado com segurança num volume ativo. As imagens Azure NetApp Files permitem restauros quase instantâneos, seguros e opcionalmente geridos pelo utilizador. Esta secção descreve várias formas de aceder ou restaurar os dados a partir de imagens do Azure NetApp Files.

### <a name="restoring-files-or-directories-from-snapshots"></a>Restaurar ficheiros ou diretórios a partir de instantâneos 

Se a [visibilidade do Caminho instantâneo](azure-netapp-files-manage-snapshots.md#edit-the-hide-snapshot-path-option) não for ocultada, os utilizadores podem aceder diretamente a instantâneos para recuperar de eliminação acidental, corrupção ou modificação dos seus dados. A segurança dos ficheiros e diretórios é retida no instantâneo, e as fotos são apenas lidas por design. Como tal, a restauração é segura e simples. 

O diagrama a seguir mostra o arquivo ou o acesso do diretório a uma fotografia instantânea: 

![Diagrama que mostra o acesso de ficheiro ou diretório a um instantâneo](../media/azure-netapp-files/snapshot-file-directory-access.png)

No diagrama, o Snapshot 1 consome apenas os blocos delta entre o volume ativo e o momento da criação instantânea. Mas quando aceder ao instantâneo através do percurso do instantâneo de volume, os dados *aparecerão* como se fosse a capacidade total de volume no momento da criação do instantâneo. Ao aceder às pastas instantâneas, os utilizadores podem auto-restaurar os dados copiando ficheiros e diretórios de uma imagem de escolha.

Da mesma forma, os instantâneos nos volumes de replicação entre regiões-alvo podem ser acedidos apenas para a recuperação de dados na região DR.  

O diagrama que se segue mostra o acesso instantâneo em cenários de replicação entre regiões: 

![Diagrama que mostra acesso instantâneo na replicação transversal](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

Consulte [Restaurar um ficheiro a partir de uma imagem instantânea utilizando um cliente](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client) sobre a restauração de ficheiros ou diretórios individuais a partir de instantâneos.

### <a name="restoring-cloning-a-snapshot-to-a-new-volume"></a>Restaurar (clonagem) um instantâneo para um novo volume

As imagens Azure NetApp Files podem ser restauradas num volume separado e independente. Esta operação é quase instantânea, independentemente do tamanho do volume e da capacidade consumida. O volume recém-criado está quase imediatamente disponível para acesso, enquanto o volume real e os blocos de dados instantâneos estão a ser copiados. Dependendo do tamanho e da capacidade do volume, este processo pode demorar um tempo considerável durante o qual o volume e o instantâneo dos pais não podem ser eliminados. No entanto, o volume já pode ser acedido após a criação inicial, enquanto o processo de cópia está em curso em segundo plano. Esta capacidade permite a criação de volume rápido para recuperação de dados ou clonagem de volume para teste e desenvolvimento. Por natureza do processo de cópia de dados, o consumo de capacidade de armazenamento duplicará quando a restauração estiver concluída, e o novo volume mostrará a capacidade ativa total do instantâneo original. Após a conclusão deste processo, o volume será independente e dissociado com o volume original, e os volumes de origem e instantâneo podem ser geridos ou removidos independentemente do novo volume.

O diagrama a seguir mostra um novo volume criado pela restauração (clonagem) de um instantâneo:   

![Diagrama que mostra um novo volume criado pela restauração de um instantâneo](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

As mesmas operações podem ser realizadas em instantâneos replicados para um volume de recuperação de desastres (DR). Qualquer instantâneo pode ser restaurado para um novo volume, mesmo quando a replicação entre regiões permanece ativa ou em curso. Esta capacidade permite a criação não disruptiva de ambientes de teste e dev numa região dr, colocando os dados a utilizar, enquanto os volumes replicados seriam utilizados apenas para fins DR. Este caso de utilização permite que o teste e o desenvolvimento sejam isolados da produção, eliminando o impacto potencial nos ambientes de produção.  

O diagrama que se segue mostra a restauração do volume (clonagem) utilizando o instantâneo do volume alvo de DR enquanto se realiza a replicação entre regiões:  

![Diagrama que mostra restauração de volume usando o instantâneo do volume alvo de DR](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

Consulte [Restaurar uma imagem instantânea para um novo volume](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume) sobre as operações de restauro de volume.

### <a name="restoring-reverting-a-snapshot-in-place"></a>Restaurar (reverter) um instantâneo no lugar

Em alguns casos, porque o novo volume consumirá capacidade de armazenamento, a criação de um novo volume a partir de um instantâneo pode não ser necessária ou adequada. Para recuperar da corrupção de dados (por exemplo, corrupçãos de bases de dados ou ataques de ransomware) pode ser mais apropriado restaurar um instantâneo dentro do próprio volume. Esta operação pode ser feita utilizando a funcionalidade de reverte do snapshot do Azure NetApp Files. Esta funcionalidade permite-lhe reverter rapidamente um volume para o estado em que se encontrava quando uma determinada fotografia foi tirada. Na maioria dos casos, reverter um volume é muito mais rápido do que restaurar ficheiros individuais de uma imagem para o sistema de ficheiros ativos, especialmente em grandes volumes multi-TiB. 

Reverter um instantâneo de volume é quase instantâneo e leva apenas alguns segundos para ser concluído, mesmo para os maiores volumes. Os metadados de volume ativo *(tabela inode)* são substituídos pelos metadados instantâneos a partir do momento da criação do instantâneo, retirando assim o volume para esse ponto específico no tempo. Nenhum bloco de dados precisa de ser copiado para que o reverte faça efeito. Como tal, é mais eficiente em termos de espaço do que restaurar uma imagem instantânea para um novo volume. 

O diagrama a seguir mostra um volume que reverte para um instantâneo anterior:  

![Diagrama que mostra um volume a reverter para um instantâneo anterior](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> Os dados ativos do sistema de ficheiros que foram escritos e os instantâneos que foram tirados após a fotografia selecionada serão perdidos. A operação snapshot reverte substituirá todos os dados do volume direcionado com os dados na imagem selecionada. Deve prestar atenção ao conteúdo instantâneo e à data de criação quando selecionar uma fotografia. Não pode desfazer a operação do instantâneo.  

Consulte [reverter um volume utilizando instantâneo reverter](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert) sobre como utilizar esta função.

## <a name="how-snapshots-are-deleted"></a>Como os instantâneos são eliminados 

As fotos consomem capacidade de armazenamento. Como tal, não são normalmente mantidos indefinidamente. Para a proteção de dados, retenção e recuperabilidade, uma série de instantâneos (criados em vários pontos do tempo) são geralmente mantidos on-line por uma certa duração, dependendo dos requisitos de RPO, RTO e SLA de retenção. No entanto, as imagens mais antigas muitas vezes não têm de ser mantidas no serviço de armazenamento e podem precisar de ser eliminadas para libertar espaço. Qualquer instantâneo pode ser eliminado (não necessariamente por ordem de criação) por um administrador a qualquer momento. 

> [!IMPORTANT]
> A operação de eliminação instantânea não pode ser desfeita. 

Quando um instantâneo é eliminado, todos os ponteiros dessa instantâneo para os blocos de dados existentes serão removidos. Quando um bloco de dados não tem mais ponteiros apontando para ele (pelo volume ativo, ou outros instantâneos no volume), o bloco de dados é devolvido ao espaço livre de volume para utilização futura. Portanto, remover instantâneos geralmente liberta mais capacidade em um volume do que eliminar dados do volume ativo, porque os blocos de dados são frequentemente capturados em instantâneos previamente criados. 

O seguinte diagrama mostra o efeito no consumo de armazenamento de supressão instantânea para um volume:  

![Diagrama que mostra o efeito de consumo de armazenamento da eliminação de instantâneos](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

Certifique-se de monitorizar o [volume e o consumo instantâneo](azure-netapp-files-metrics.md#volumes) e entender como a aplicação, o volume ativo e o consumo instantâneo interagem. 

Consulte [As imagens de Eliminar](azure-netapp-files-manage-snapshots.md#delete-snapshots) sobre como gerir a eliminação de imagens. Consulte [As políticas de instantâneo](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) sobre como automatizar este processo.

## <a name="next-steps"></a>Passos seguintes

* [Gerir instantâneos com o Azure NetApp Files](azure-netapp-files-manage-snapshots.md)
* [Monitor de volume e métricas instantâneas](azure-netapp-files-metrics.md#volumes)
* [Resolver problemas das políticas de instantâneo](troubleshoot-snapshot-policies.md)
* [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md) (Limites dos recursos do Azure NetApp Files)
* [Azure NetApp Files Snapshots 101 vídeo](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [NetApp Snapshot - Biblioteca de Vídeos NetApp](https://tv.netapp.com/detail/video/2579133646001/snapshot)



