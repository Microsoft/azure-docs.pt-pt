---
title: Criar um cache HPC do Azure
description: Como criar uma instância de cache do HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: rohogue
ms.openlocfilehash: 07aba1b1536635e414fc5fab4ece148683909188
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168554"
---
# <a name="create-an-azure-hpc-cache"></a>Criar um cache HPC do Azure

Use o portal do Azure para criar o cache.

![captura de tela da visão geral do cache no portal do Azure, com o botão criar na parte inferior](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definir detalhes básicos

![captura de tela da página de detalhes do projeto no portal do Azure](media/hpc-cache-create-basics.png)

Em **detalhes do projeto**, selecione a assinatura e o grupo de recursos que hospedarão o cache. Verifique se a assinatura está na lista de [acesso](hpc-cache-prereqs.md#azure-subscription) .

Em **detalhes do serviço**, defina o nome do cache e esses outros atributos:

* Local-selecione uma das [regiões com suporte](hpc-cache-overview.md#region-availability).
* Rede virtual-você pode selecionar um existente ou criar uma nova rede virtual.
* Sub-rede – escolha ou crie uma sub-rede com pelo menos 64 endereços IP (/24) que serão usados somente para esta instância de cache do Azure HPC.

## <a name="set-cache-capacity"></a>Definir capacidade de cache
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na página **cache** , você deve definir a capacidade do seu cache. Os valores definidos aqui determinam a quantidade de dados que o cache pode conter e a rapidez com que ele pode atender às solicitações do cliente.

Após o período de visualização pública, a capacidade também afetará o custo do cache.

Escolha a capacidade definindo estes dois valores:

* A taxa máxima de transferência de dados para o cache (taxa de transferência), em GB/segundo
* A quantidade de armazenamento alocada para dados armazenados em cache, em TB

Escolha um dos valores de taxa de transferência disponíveis e tamanhos de armazenamento em cache.

Tenha em mente que a taxa de transferência de dados real depende da carga de trabalho, das velocidades de rede e do tipo de destinos de armazenamento. Os valores escolhidos definem a taxa de transferência máxima para todo o sistema de cache, mas algumas delas são usadas para tarefas de sobrecarga. Por exemplo, se um cliente solicitar um arquivo que ainda não esteja armazenado no cache, ou se o arquivo estiver marcado como obsoleto, seu cache usará parte de sua taxa de transferência para obtê-lo do armazenamento de back-end.

O cache HPC do Azure gerencia quais arquivos são armazenados em cache e pré-carregados para maximizar as tarifas de acesso ao cache. O conteúdo do cache é avaliado continuamente e os arquivos são movidos para o armazenamento de longo prazo quando são acessados com menos frequência. Escolha um tamanho de armazenamento de cache que possa manter confortavelmente o conjunto ativo de arquivos de trabalho com espaço adicional para metadados e outras sobrecargas.

![captura de tela da página de dimensionamento do cache](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>Adicionar marcas de recurso (opcional)

A página **marcas** permite que você adicione [marcas de recurso](https://go.microsoft.com/fwlink/?linkid=873112) à instância de cache do HPC do Azure.

## <a name="finish-creating-the-cache"></a>Concluir a criação do cache

Depois de configurar o novo cache, clique na guia **revisar + criar** . O portal valida suas seleções e permite que você examine suas escolhas. Se tudo estiver correto, clique em **criar**.

A criação do cache leva cerca de 10 minutos. Você pode acompanhar o progresso no painel de notificações do portal do Azure.

![captura de tela das páginas "implantação em andamento" e "notificações" de criação de cache no portal](media/hpc-cache-deploy-status.png)

Quando a criação for concluída, uma notificação será exibida com um link para a nova instância de cache do Azure HPC e o cache aparecerá na lista de **recursos** da sua assinatura.
<!-- double check on notification -->

![captura de tela da instância de cache do HPC do Azure no portal do Azure](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Passos seguintes

Depois que o cache aparecer na lista de **recursos** , defina os destinos de armazenamento para dar acesso ao cache às suas fontes de dados.

* [Adicionar destinos de armazenamento](hpc-cache-add-storage.md)
