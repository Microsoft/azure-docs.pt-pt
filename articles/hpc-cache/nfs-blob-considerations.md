---
title: Use armazenamento NFS Blob com cache Azure HPC
description: Descreve procedimentos e limitações ao utilizar o armazenamento de bolhas ADLS-NFS com cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: d861a41d8cdeac548729ff341b3ffe27c0aa8152
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259982"
---
# <a name="use-nfs-mounted-blob-storage-preview-with-azure-hpc-cache"></a>Utilize o armazenamento de bolhas montado em NFS (PREVIEW) com cache Azure HPC

A partir de março de 2021 pode utilizar recipientes de bolhas montados em NFS com cache Azure HPC. Leia mais sobre o suporte ao [protocolo NFS 3.0 no armazenamento Azure Blob](../storage/blobs/network-file-system-protocol-support.md) no site de documentação de armazenamento Blob.

> [!NOTE]
> O suporte ao protocolo NFS 3.0 no armazenamento Azure Blob está em pré-visualização e não deve ser utilizado em ambientes de produção. Verifique se há atualizações e mais detalhes na documentação de suporte do [protocolo NFS.](../storage/blobs/network-file-system-protocol-support.md)

A Azure HPC Cache utiliza o armazenamento de bolhas ativado por NFS no seu tipo de alvo de armazenamento ADLS-NFS. Estes alvos de armazenamento são semelhantes aos alvos regulares de armazenamento NFS, mas também têm alguma sobreposição com alvos regulares da Azure Blob.

Este artigo explica estratégias e limitações que deve entender quando utiliza alvos de armazenamento ADLS-NFS.

Também deve ler a documentação do blob NFS, especialmente estas secções que descrevem cenários compatíveis e incompatíveis:

* [Descrição geral das funcionalidades](../storage/blobs/network-file-system-protocol-support.md#applications-and-workloads-suited-for-this-feature)
* [Características ainda não suportadas](../storage/blobs/network-file-system-protocol-support.md#azure-storage-features-not-yet-supported)
* [Considerações de desempenho](../storage/blobs/network-file-system-protocol-support-performance.md)

## <a name="understand-consistency-requirements"></a>Compreender os requisitos de consistência

A Cache HPC requer uma forte consistência para os alvos de armazenamento ADLS-NFS. Por predefinição, o armazenamento de blob ativado por NFS não atualiza rigorosamente os metadados de ficheiros, o que impede a Cache HPC de comparar com precisão as versões de ficheiros.

Para contornar esta diferença, a Azure HPC Cache desativa automaticamente o cache de atributo NFS em qualquer recipiente de bolhas ativado por NFS utilizado como alvo de armazenamento.

Esta regulação persiste durante o tempo de vida útil do recipiente, mesmo que o retire da cache.

## <a name="preload-data-with-nfs-protocol"></a>Pré-carregamento de dados com o protocolo NFS

Num recipiente de bolhas ativado por NFS, *um ficheiro só pode ser editado pelo mesmo protocolo utilizado quando foi criado*. Ou seja, se utilizar a Azure REST API para preencher um recipiente, não pode utilizar NFS para atualizar esses ficheiros. Como a Cache Azure HPC só utiliza NFS, não pode editar quaisquer ficheiros que foram criados com a AZure REST API.

Não é um problema para a cache se o seu recipiente está vazio, ou se os ficheiros foram criados usando NFS.

Se os ficheiros do seu contentor foram criados com a API REST da Azure Blob em vez de NFS, a Cache Azure HPC está restrita a estas ações nos ficheiros originais:

* Enuse o ficheiro num diretório.
* Leia o ficheiro (e guarde-o na cache para leituras posteriores).
* Apague o ficheiro.
* Esvazie o ficheiro (truncá-lo para 0).
* Guarde uma cópia do ficheiro. A cópia é marcada como um ficheiro criado por NFS, e pode ser editada usando NFS.

A Azure HPC Cache **não pode** editar o conteúdo de um ficheiro que foi criado usando REST. Isto significa que não pode salvar um ficheiro alterado de um cliente de volta para o alvo de armazenamento.

É importante compreender esta limitação, pois pode causar problemas de integridade dos dados se utilizar modelos de utilização de caching de leitura/escrita em ficheiros que não foram criados com NFS.

> [!TIP]
> Saiba mais sobre ler e escrever caching em [modelos de utilização de cache .](cache-usage-models.md)

### <a name="write-caching-scenarios"></a>Escreva cenários de caching

Estes modelos de utilização de cache incluem o cache de escrita:

* **Mais de 15% escreve**
* **Mais de 15% escreve, verificando o servidor de suporte para alterações a cada 30 segundos**
* **Mais de 15% escreve, verificando o servidor de suporte para alterações a cada 60 segundos**
* **Mais de 15% escreve, escreva para o servidor a cada 30 segundos**

Utilize apenas estes modelos de utilização para editar ficheiros que foram criados com NFS.

Se tentar utilizar o caching de escrita em ficheiros criados pelo REST, as alterações de ficheiros podem ser perdidas. Isto porque a cache não tenta guardar as edições de ficheiros para o recipiente de armazenamento imediatamente.

Eis como tentar cache escreve para ficheiros criados pelo REST coloca os dados em risco:

1. A cache aceita edições de clientes e devolve uma mensagem de sucesso em cada mudança.
1. A cache mantém o ficheiro alterado no seu armazenamento e aguarda alterações adicionais.
1. Passado algum tempo, o cache tenta guardar o ficheiro alterado para o recipiente de fundo. Neste momento, receberá uma mensagem de erro porque está a tentar escrever para um ficheiro criado pelo REST com NFS.

   É demasiado tarde para dizer à máquina do cliente que as suas alterações não foram aceites, e a cache não tem forma de atualizar o ficheiro original. Assim, as mudanças dos clientes serão perdidas.

### <a name="read-caching-scenarios"></a>Leia cenários de caching

Os cenários de caching são apropriados para ficheiros criados com API NFS ou Azure Blob REST.

Estes modelos de utilização utilizam apenas o caching:

* **Leia escritos pesados e pouco frequentes**
* **Os clientes escrevem para o alvo da NFS, contornando a cache**
* **Leia pesado, verificando o servidor de apoio a cada 3 horas**

Pode utilizar estes modelos de utilização com ficheiros criados pela REST API ou pela NFS. Qualquer NFS escreve enviado de um cliente para o recipiente de back-end ainda falhará, mas eles falharão imediatamente e devolverão uma mensagem de erro ao cliente.

Um fluxo de trabalho de cache de leitura ainda pode envolver alterações de ficheiros, desde que estes não estejam em cache. Por exemplo, os clientes podem aceder a ficheiros a partir do contentor, mas escrever as suas alterações como um novo ficheiro, ou podem guardar ficheiros modificados num local diferente.

## <a name="recognize-network-lock-manager-nlm-limitations"></a>Reconhecer limitações do Gestor de Bloqueio de Rede (NLM)

Os recipientes blob ativados por NFS não suportam o Network Lock Manager (NLM), que é um protocolo NFS comumente utilizado para proteger ficheiros de conflitos.

Se o seu fluxo de trabalho NFS foi originalmente escrito para sistemas de armazenamento de hardware, as aplicações do seu cliente podem incluir pedidos de NLM. Para contornar esta limitação ao mover o seu processo para o armazenamento de bolhas ativada pela NFS, certifique-se de que os seus clientes desativam o NLM quando montarem a cache.

Para desativar o NLM, utilize a opção ``-o nolock`` no comando dos seus clientes. ``mount`` Esta opção impede que os clientes solicitem fechaduras NLM e recebam erros em resposta.

Em algumas situações, a própria Azure HPC Cache reconhece os pedidos da NLM do cliente. O modelo de utilização da cache chamado **Ler escritos pesados e pouco frequentes** reconhece pedidos de NLM em nome do seu armazenamento back-end. Este sistema evita erros no cliente, mas na verdade não cria um bloqueio no recipiente ADLS-NFS.

Se mudar o modelo de utilização de um alvo de armazenamento ADLS-NFS de ou para **ler escritas pesadas e pouco frequentes,** deve voltar a montar todos os clientes utilizando a ``nolock`` opção.

Mais informações sobre os modelos NLM, HPC Cache e utilização estão incluídas nos [modelos de utilização da Cache Understand.](cache-usage-models.md#know-when-to-remount-clients-for-nlm)

## <a name="streamline-writes-to-nfs-enabled-containers-with-hpc-cache"></a>Streamline escreve para recipientes ativados por NFS com Cache HPC

A Azure HPC Cache pode ajudar a melhorar o desempenho numa carga de trabalho que inclui a escrita de alterações a um alvo de armazenamento ADLS-NFS.

> [!NOTE]
> Tem de utilizar o NFS para povoar o seu recipiente de armazenamento ADLS-NFS se pretender modificar os seus ficheiros através da Cache Azure HPC.

Uma das limitações descritas no artigo de [consideração](../storage/blobs/network-file-system-protocol-support-performance.md) de desempenho de blob ativado pela NFS é que o armazenamento ADLS-NFS não é muito eficiente na sobreposição dos ficheiros existentes. Se utilizar a Cache Azure HPC com armazenamento de bolhas montadas em NFS, a cache manuseia reescritas intermitentes à medida que os clientes modificam um ficheiro ativo. A latência de escrever um ficheiro no recipiente de trás está escondida dos clientes.

Tenha em mente as limitações acima explicadas nos [dados de pré-carga com o protocolo NFS](#preload-data-with-nfs-protocol).

## <a name="next-steps"></a>Passos seguintes

* Aprenda [pré-requisitos do alvo de armazenamento ADLS-NFS](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview)
* Criar uma [conta de armazenamento de bolhas ativada por NFS](../storage/blobs/network-file-system-protocol-support-how-to.md)
