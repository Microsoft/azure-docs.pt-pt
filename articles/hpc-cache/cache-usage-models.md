---
title: Modelos de utilização Azure HPC Cache
description: Descreve os diferentes modelos de utilização da cache e como escolher entre eles para definir apenas leitura ou ler/escrever caching e controlar outras definições de cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 3ad252520ca0cf7acdb3c84ef1da87c8076f3172
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775719"
---
# <a name="understand-cache-usage-models"></a>Compreender modelos de utilização de cache

Os modelos de utilização da Cache permitem-lhe personalizar a forma como o seu Azure HPC Cache armazena ficheiros para acelerar o seu fluxo de trabalho.

## <a name="basic-file-caching-concepts"></a>Conceitos básicos de caching de ficheiros

O cache de ficheiros é como a Azure HPC Cache acelera os pedidos dos clientes. Utiliza estas práticas básicas:

* **Leia o caching** - AZure HPC Cache guarda uma cópia de ficheiros que os clientes solicitam do sistema de armazenamento. Da próxima vez que um cliente solicitar o mesmo ficheiro, a cache HPC pode fornecer a versão na sua cache em vez de ter que buscá-la do sistema de armazenamento back-end novamente.

* **Escrever caching** - Opcionalmente, a Cache Azure HPC pode armazenar uma cópia de quaisquer ficheiros alterados enviados a partir das máquinas do cliente. Se vários clientes fizerem alterações no mesmo ficheiro durante um curto período de tempo, a cache pode recolher todas as alterações na cache em vez de ter de escrever cada alteração individualmente para o sistema de armazenamento back-end.

  Depois de um período de tempo especificado sem alterações, a cache desloca o ficheiro para o sistema de armazenamento de longo prazo.

  Se o cache de escrita for desativado, o cache não armazena o ficheiro alterado e escreve-o imediatamente para o sistema de armazenamento back-end.

* **Atraso de descrumação** - Para uma cache com caching de escrita ligado, o atraso de recídula é o tempo que o cache aguarda por alterações adicionais de ficheiro antes de copiar o ficheiro para o sistema de armazenamento de back-end.

* **Verificação de back-end** - A definição de verificação de back-end determina a frequência com que a cache compara a sua cópia local de um ficheiro com a versão remota no sistema de armazenamento de back-end. Se a cópia traseira for mais recente do que a cópia em cache, a cache recolhe a cópia remota e armazena-a para pedidos futuros.

  A definição de verificação de back-end mostra quando o cache compara *automaticamente* os seus ficheiros com ficheiros de origem no armazenamento remoto. No entanto, pode forçar a Azure HPC Cache a comparar ficheiros através da realização de uma operação de diretório que inclui um pedido de readdirplus. Readdirplus é uma API padrão NFS (também chamada de leitura estendida) que devolve metadados de diretório, o que faz com que a cache compare e atualize ficheiros.

Os modelos de utilização incorporados na Cache Azure HPC têm valores diferentes para estas configurações para que possa escolher a melhor combinação para a sua situação.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>Escolha o modelo de utilização certo para o seu fluxo de trabalho

Deve escolher um modelo de utilização para cada alvo de armazenamento montado em NFS que utilize. Os alvos de armazenamento Azure Blob têm um modelo de uso incorporado que não pode ser personalizado.

Os modelos de utilização da cache HPC permitem-lhe escolher como equilibrar a resposta rápida com o risco de obter dados antigos. Se pretender otimizar a velocidade para os ficheiros de leitura, pode não se importar se os ficheiros da cache são verificados contra os ficheiros de fundo. Por outro lado, se quiser certificar-se de que os seus ficheiros estão sempre atualizados com o armazenamento remoto, escolha um modelo que verifique com frequência.

Estas são as opções do modelo de utilização:

* **Leia escritas pesadas e pouco frequentes** - Utilize esta opção se quiser acelerar o acesso de leitura a ficheiros que são estáticos ou raramente alterados.

  Esta opção caches cliente lê mas não cache escreve. Passa a escrever para o armazenamento de fundo imediatamente.
  
  Os ficheiros armazenados na cache não são automaticamente comparados com os ficheiros do volume de armazenamento NFS. (Leia a descrição da verificação de back-end acima para aprender a compará-las manualmente.)

  Não utilize esta opção se existir o risco de um ficheiro poder ser modificado diretamente no sistema de armazenamento sem antes o escrever para a cache. Se isso acontecer, a versão em cache do ficheiro estará dessincronizada com o ficheiro back-end.

* **Mais de 15% escreve** - Esta opção acelera tanto a leitura como a gravação. Ao utilizar esta opção, todos os clientes devem aceder aos ficheiros através da Cache Azure HPC em vez de montarem o armazenamento de back-end diretamente. Os ficheiros em cache terão alterações recentes que ainda não foram copiadas para a parte de trás.

  Neste modelo de utilização, os ficheiros na cache só são verificados com os ficheiros de armazenamento de back-end a cada oito horas. Presume-se que a versão em cache do ficheiro é mais atual. Um ficheiro modificado na cache é escrito para o sistema de armazenamento de back-end depois de ter estado na cache por 20 minutos<!-- an hour --> sem alterações adicionais.

* **Os clientes escrevem para o alvo NFS, contornando a cache** - Escolha esta opção se algum cliente no seu fluxo de trabalho escrever dados diretamente para o sistema de armazenamento sem primeiro escrever para a cache, ou se pretende otimizar a consistência dos dados. Os ficheiros que os clientes solicitam estão em cache (leituras), mas quaisquer alterações a esses ficheiros do cliente (escreve) não estão em cache. São passados diretamente para o sistema de armazenamento back-end.

  Com este modelo de utilização, os ficheiros na cache são frequentemente verificados com as versões back-end para atualizações - a cada 30 segundos. Esta verificação permite que os ficheiros sejam alterados fora da cache, mantendo a consistência dos dados.

  > [!TIP]
  > Estes três primeiros modelos básicos de utilização podem ser usados para lidar com a maioria dos fluxos de trabalho da Cache Azure HPC. As próximas opções são para cenários menos comuns.

* **Mais de 15% escreve, verificando o servidor de suporte para alterações a cada 30 segundos** e **mais de 15% escreve, verificando o servidor de suporte para alterações a cada 60 segundos** - Estas opções são concebidas para fluxos de trabalho onde pretende acelerar tanto as leituras como as escritas, mas há a possibilidade de outro utilizador escrever diretamente para o sistema de armazenamento back-end. Por exemplo, se vários conjuntos de clientes estiverem a trabalhar nos mesmos ficheiros de diferentes locais, estes modelos de utilização podem fazer sentido equilibrar a necessidade de acesso rápido de ficheiros com baixa tolerância para conteúdos antigos da fonte.

* **Mais de 15% escreve, escreva para o servidor a cada 30 segundos** - Esta opção é projetada para o cenário em que vários clientes estão a modificar ativamente os mesmos ficheiros, ou se alguns clientes acedem diretamente ao armazenamento de back-end em vez de montarem a cache.

  As frequentes gravações no back-end afetam o desempenho da cache, pelo que deve considerar a utilização do modelo de utilização **de mais de 15%** se houver um baixo risco de conflito de ficheiros - por exemplo, se souber que diferentes clientes estão a trabalhar em diferentes áreas do mesmo conjunto de ficheiros.

* **Leia pesado, verificando o servidor de suporte a cada 3 horas** - Esta opção prioriza leituras rápidas do lado do cliente, mas também atualiza regularmente ficheiros em cache do sistema de armazenamento back-end, ao contrário do modelo de utilização **de escrita pesada e pouco frequente.**

Esta tabela resume as diferenças do modelo de utilização:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

Se tiver dúvidas sobre o melhor modelo de utilização para o seu fluxo de trabalho Azure HPC Cache, fale com o seu representante da Azure ou abra um pedido de ajuda de apoio.

## <a name="next-steps"></a>Passos seguintes

* [Adicione alvos de armazenamento](hpc-cache-add-storage.md) à sua Cache Azure HPC
