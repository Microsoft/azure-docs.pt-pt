---
title: Mapear uma estrutura de pasta para uma topologia de sincronização de ficheiros Azure
description: Mapear uma estrutura de ficheiros e pastas existente para as partilhas de ficheiros Azure para utilização com o Azure File Sync. Um bloco de texto comum, partilhado em todos os documentos migratórios.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 948090d0ee956ca1798d7b0f46bb33276c4d6354
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143606"
---
Neste passo, está a avaliar quantas ações de ficheiros Azure precisas. Uma única instância do Windows Server (ou cluster) pode sincronizar até 30 partilhas de ficheiros Azure.

Pode ter mais pastas nos seus volumes que partilha atualmente localmente, à medida que a SMB partilha para os seus utilizadores e aplicações. A maneira mais fácil é prever uma partilha no local que mapeie 1:1 para uma partilha de ficheiros Azure. Se tiver um número pequeno o suficiente, abaixo dos 30 para uma única instância do Windows Server, então recomendamos um mapeamento 1:1.

Se você tem mais ações do que 30, muitas vezes é desnecessário mapear uma parte no local 1:1 para uma partilha de ficheiros Azure. Considere as seguintes opções.

#### <a name="share-grouping"></a>Agrupamento de ações

Se o seu departamento de RH (por exemplo) tiver um total de 15 ações, poderá considerar armazenar todos os dados de RH numa única partilha de ficheiros Azure. Armazenar várias ações no local numa partilha de ficheiros Azure não o impede de criar as habituais 15 ações SMB na sua instância local do Windows Server. Significa apenas que organiza as pastas-raiz destas 15 ações como subpastas sob uma pasta comum. Em seguida, sincroniza esta pasta comum com uma partilha de ficheiros Azure. Desta forma, apenas uma única parte de ficheiro Sino Azure na nuvem é necessária para este grupo de ações no local.

#### <a name="volume-sync"></a>Sincronização de volume

O Azure File Sync suporta sincronizar a raiz de um volume numa partilha de ficheiros Azure. Se sincronizar a pasta raiz, todas as subpastas e ficheiros irão para a mesma partilha de ficheiros Azure.

Sincronizar a raiz do volume nem sempre é a melhor resposta. Existem benefícios na sincronização de vários locais. Por exemplo, fazê-lo ajuda a manter o número de itens mais baixo por âmbito de sincronização. A configuração do Sync de Ficheiros Azure com um número mais baixo de itens não é apenas benéfica para a sincronização de ficheiros. Um número mais baixo de itens também beneficia cenários como estes:

* A restauração do lado da nuvem a partir de um instantâneo de partilha de ficheiros Azure pode ser tomada como uma cópia de segurança.
* A recuperação de desastres de um servidor no local pode acelerar significativamente.
* As alterações efetuadas diretamente numa partilha de ficheiros Azure (sincronização externa) podem ser detetadas e sincronizadas mais rapidamente.

#### <a name="a-structured-approach-to-a-deployment-map"></a>Uma abordagem estruturada para um mapa de implantação

Antes de implementar o armazenamento em nuvem num passo posterior, é importante criar um mapa entre pastas no local e partilhas de ficheiros Azure. Este mapeamento irá então informar quantos e quais os recursos do *grupo Desincronização* de Ficheiros Azure que irá fornecer. Um grupo de sincronização liga a partilha de ficheiros Azure e a pasta no servidor e estabelece uma ligação sincronizada.

Para tomar a decisão sobre quantas ações de ficheiro Saque De que necessita, reveja os seguintes limites e boas práticas. Ao fazê-lo, irá ajudá-lo a otimizar o seu mapa.

* Um servidor com o agente Azure File Sync instalado pode sincronizar até 30 partilhas de ficheiros Azure.
* Uma partilha de ficheiros Azure é implantada dentro de uma conta de armazenamento. Isto faz da conta de armazenamento um alvo de escala para números de desempenho como IOPS e entrada. 

  Duas ações de ficheiros Azure standard (não premium) podem teoricamente saturar o desempenho máximo que uma conta de armazenamento pode entregar. Se planeia apenas anexar o Azure File Sync a estas ações de ficheiros, então agrupar várias ações de ficheiros Azure na mesma conta de armazenamento não criará qualquer problema. Reveja os objetivos de desempenho da partilha de ficheiros Azure para uma visão mais profunda das métricas relevantes a considerar. 

  Se planeia levantar uma aplicação para o Azure que irá utilizar a partilha de ficheiros Azure de forma nativa, então poderá precisar de mais desempenho a partir da sua partilha de ficheiros Azure. Se esta é uma possibilidade, mesmo no futuro, então mapear uma parte de ficheiro Azure para a sua própria conta de armazenamento é o melhor.
* Há um limite de 250 contas de armazenamento por subscrição numa única região do Azure.

> [!TIP]
> Com esta informação em mente, muitas vezes torna-se necessário agrupar várias pastas de alto nível nos seus volumes num novo diretório comum e raiz. Em seguida, sincroniza este novo diretório raiz, e todas as pastas que agrupou nele, para uma única partilha de ficheiros Azure. Esta técnica permite-lhe ficar dentro do limite de 30 sincronizações de partilha de ficheiros Azure por servidor.
>
> Este agrupamento sob uma raiz comum não tem impacto no acesso aos seus dados. Os vossos ACLs ficam como estão. Só precisaria de ajustar quaisquer caminhos de partilha (como ações SMB ou NFS) que poderia ter nas pastas do servidor que agora transformou numa raiz comum. Nada mais muda.

Outro aspeto importante do Azure File Sync e um desempenho e experiência equilibrados é compreender os fatores de escala para o desempenho do Azure File Sync. Obviamente, quando os ficheiros são sincronizados através da internet, os ficheiros maiores demoram mais tempo e largura de banda a sincronizar.

> [!IMPORTANT]
> O vetor de escala mais importante para o Azure File Sync é o número de itens (ficheiros e pastas) que precisam de ser sincronizados.

O Azure File Sync suporta sincronizar até 100.000 itens numa única partilha de ficheiros Azure. Este limite pode ser ultrapassado e apenas mostra o que a equipa azure File Sync testa regularmente.

É uma boa prática manter o número de itens por âmbito de sincronização baixo. É um fator importante a ter em conta no seu mapeamento de pastas para ações de ficheiros Azure.

Na sua situação, é possível que um conjunto de pastas possa sincronizar logicamente a mesma partilha de ficheiros Azure (utilizando a nova abordagem de pasta-raiz comum mencionada anteriormente). Mas ainda seria melhor reagrupar pastas de modo a sincronizarem duas em vez de uma partilha de ficheiros Azure. Pode utilizar esta abordagem para manter o número de ficheiros e pastas por partilha de ficheiros equilibrado em todo o servidor.

#### <a name="create-a-mapping-table"></a>Criar uma mesa de mapeamento

:::row:::
    :::column:::
        [![](media/storage-files-migration-namespace-mapping/namespace-mapping.png "An example of a mapping table. Download the file below to experience and use the content of this image.")](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Utilize uma combinação dos conceitos anteriores para ajudar a determinar quantas partilhas de ficheiros Azure precisa e quais as partes dos seus dados existentes que acabarão em que o ficheiro Azure partilha.
        
        Crie uma tabela que grave os seus pensamentos, para que possa encaminhá-la no próximo passo. Manter-se organizado é importante, porque pode ser fácil perder detalhes do seu plano de mapeamento quando você está a fornecer muitos recursos Azure ao mesmo tempo. Para ajudá-lo a criar um mapeamento completo, pode descarregar um ficheiro Microsoft Excel como modelo.

[//]: # (HTML aparece como a única maneira de conseguir adicionar uma tabela de duas colunas aninhada com análise de imagem de trabalho e texto/hiperligação na mesma linha.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">Descarregue um modelo de mapeamento de espaço de nome.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
