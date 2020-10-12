---
title: Mapear uma estrutura de pasta para uma topologia de sincronização de ficheiros Azure
description: Mapear uma estrutura de ficheiros e pasta existentes para ações de ficheiros Azure para utilização com a Azure File Sync. Um bloco de texto comum, partilhado através de documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 16b9342f0374377349f338db7ce5c8389c77ea18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87425000"
---
Neste passo, está a avaliar quantas ações de ficheiros Azure precisa. Uma única instância do Windows Server (ou cluster) pode sincronizar até 30 ações de ficheiros Azure.

Pode ter mais pastas nos seus volumes que partilha localmente como partilha sMB para os seus utilizadores e apps. A maneira mais fácil é visualizar uma partilha no local que mapeia 1:1 para uma partilha de ficheiros Azure. Se tiver um número pequeno o suficiente, abaixo de 30 para uma única instância do Windows Server, recomendamos um mapeamento de 1:1.

Se tiver mais ações do que 30, muitas vezes é desnecessário mapear uma partilha no local 1:1 para uma partilha de ficheiros Azure. Considere as seguintes opções.

#### <a name="share-grouping"></a>Agrupamento de partilha

Se o seu departamento de RH (por exemplo) tiver um total de 15 ações, poderá considerar armazenar todos os dados de RH numa única partilha de ficheiros Azure. Armazenar várias ações no local numa partilha de ficheiros Azure não o impede de criar as habituais 15 ações SMB na sua instância local do Windows Server. Significa apenas que organiza as pastas de raiz destas 15 ações como sub-dobradeiras sob uma pasta comum. Em seguida, sincroniza esta pasta comum a uma partilha de ficheiros Azure. Desta forma, apenas uma única partilha de ficheiros Azure na nuvem é necessária para este grupo de ações no local.

#### <a name="volume-sync"></a>Sincronização de volume

O Azure File Sync suporta sincronizar a raiz de um volume para uma partilha de ficheiros Azure. Se sincronizar a pasta raiz, todas as sub-dobradeiras e ficheiros irão para a mesma partilha de ficheiros Azure.

Sincronizar a raiz do volume nem sempre é a melhor resposta. Existem benefícios em sincronizar vários locais. Por exemplo, fazê-lo ajuda a manter o número de itens mais baixo por âmbito de sincronização. Configurar o Azure File Sync com um número mais baixo de itens não é apenas benéfico para a sincronização de ficheiros. Um menor número de itens também beneficia cenários como estes:

* A restauração do lado da nuvem a partir de uma imagem de partilha de ficheiros Azure pode ser tomada como uma cópia de segurança.
* A recuperação de desastres de um servidor no local pode acelerar significativamente.
* As alterações efetuadas diretamente numa partilha de ficheiros Azure (sincronização externa) podem ser detetadas e sincronizadas mais rapidamente.

#### <a name="a-structured-approach-to-a-deployment-map"></a>Uma abordagem estruturada de um mapa de implantação

Antes de implementar o armazenamento em nuvem num passo posterior, é importante criar um mapa entre as pastas no local e as partilhas de ficheiros Azure. Este mapeamento informará então quantos e quais os recursos do *grupo Azure* File Sync que irá providenciar. Um grupo de sincronização liga a partilha de ficheiros Azure e a pasta no seu servidor e estabelece uma ligação sincronizada.

Para tomar a decisão sobre quantas ações de arquivo Azure precisa, reveja os seguintes limites e boas práticas. Ao fazê-lo, irá ajudá-lo a otimizar o seu mapa.

* Um servidor com o agente Azure File Sync instalado pode sincronizar até 30 ações de ficheiros Azure.
* Uma partilha de ficheiros Azure é implantada dentro de uma conta de armazenamento. Isso faz da conta de armazenamento um alvo de escala para números de desempenho como IOPS e produção. 

  Duas ações de ficheiro padrão (não premium) Azure podem teoricamente saturar o desempenho máximo que uma conta de armazenamento pode fornecer. Se planeia anexar apenas o Azure File Sync a estas ações de ficheiros, então agrupar várias ações de ficheiros Azure na mesma conta de armazenamento não criará um problema. Reveja os alvos de desempenho da partilha de ficheiros Azure para obter uma visão mais profunda das métricas relevantes a ter em conta. 

  Se planeia levantar uma aplicação para o Azure que utilizará a partilha de ficheiros Azure de forma nativa, então poderá precisar de mais desempenho da sua partilha de ficheiros Azure. Se esta é uma possibilidade, mesmo no futuro, então mapear uma parte de ficheiro Azure para a sua própria conta de armazenamento é o melhor.
* Há um limite de 250 contas de armazenamento por subscrição numa única região de Azure.

> [!TIP]
> Com esta informação em mente, muitas vezes torna-se necessário agrupar várias pastas de alto nível nos seus volumes num novo diretório de raiz comum. Em seguida, sincroniza este novo diretório de raiz, e todas as pastas que agrupou nele, para uma única partilha de ficheiros Azure. Esta técnica permite-lhe permanecer dentro do limite de 30 sincronizações de partilha de ficheiros Azure por servidor.
>
> Este agrupamento sob uma raiz comum não tem impacto no acesso aos seus dados. Os teus ACLs ficam como estão. Só precisa de ajustar quaisquer caminhos de partilha (como ações SMB ou NFS) que possa ter nas pastas do servidor que agora se transformou numa raiz comum. Nada mais muda.

Outro aspeto importante do Azure File Sync e um desempenho e experiência equilibrados é compreender os fatores de escala para o desempenho do Azure File Sync. Obviamente, quando os ficheiros são sincronizados pela internet, ficheiros maiores demoram mais tempo e largura de banda a sincronizar.

> [!IMPORTANT]
> O vetor de escala mais importante para O Azure File Sync é o número de itens (ficheiros e pastas) que precisam de ser sincronizados.

O Azure File Sync suporta sincronizar até 100 Milhões de itens para uma única partilha de ficheiros Azure. Este limite pode ser ultrapassado e só mostra o que a equipa Azure File Sync testa regularmente.

É uma boa prática manter o número de itens por sincronização baixo. É um fator importante a ter em conta no seu mapeamento de pastas para ações de ficheiros Azure.

Na sua situação, é possível que um conjunto de pastas possa logicamente sincronizar-se com a mesma partilha de ficheiros Azure (utilizando a nova abordagem comum da pasta raiz mencionada anteriormente). Mas ainda pode ser melhor reagrupar pastas de modo a sincronizar em duas em vez de uma partilha de ficheiros Azure. Pode utilizar esta abordagem para manter o número de ficheiros e pastas por partilha de ficheiros equilibrado em todo o servidor.

#### <a name="create-a-mapping-table"></a>Criar uma mesa de mapeamento

:::row:::
    :::column:::
        [![Um exemplo de uma mesa de mapeamento. Descarregue o ficheiro abaixo para experimentar e utilizar o conteúdo desta imagem.](media/storage-files-migration-namespace-mapping/namespace-mapping.png)](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Utilize uma combinação dos conceitos anteriores para ajudar a determinar quantas ações de ficheiros Azure precisa e quais as partes dos seus dados existentes que acabarão em que a ação de ficheiros Azure.
        
        Crie uma tabela que registe os seus pensamentos, para que possa encaminhá-la no próximo passo. Manter-se organizado é importante, porque pode ser fácil perder detalhes do seu plano de mapeamento quando está a autar muitos recursos Azure ao mesmo tempo. Para ajudá-lo a criar um mapeamento completo, pode descarregar um ficheiro Microsoft Excel como modelo.

[//]: # (HTML aparece como a única maneira de conseguir adicionar uma tabela aninhada de duas colunas com a análise de imagem de trabalho e texto/hiperligação na mesma linha.)

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
