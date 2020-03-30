---
title: Criar uma Cache Azure HPC
description: Como criar uma instância de Cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 11/11/2019
ms.author: rohogue
ms.openlocfilehash: c6090d19ce530829b79dca69636c2123e2519961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129559"
---
# <a name="create-an-azure-hpc-cache"></a>Criar uma Cache Azure HPC

Utilize o portal Azure para criar a sua cache.

![screenshot de cache visão geral no portal Azure, com botão de criação na parte inferior](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definir detalhes básicos

![screenshot da página de detalhes do projeto no portal Azure](media/hpc-cache-create-basics.png)

Em Detalhes do **Projeto**, selecione o grupo de subscrição e recursos que irá acolher a cache. Certifique-se de que a subscrição está na lista de [acesso.](hpc-cache-prereqs.md#azure-subscription)

Em Detalhes de **Serviço,** detete o nome cache e estes outros atributos:

* Localização - Selecione uma das [regiões apoiadas](hpc-cache-overview.md#region-availability).
* Rede virtual - Pode selecionar uma existente ou criar uma nova rede virtual.
* Subnet - Escolha ou crie uma sub-rede com pelo menos 64 endereços IP (/24) que serão utilizados apenas para esta instância de Cache Azure HPC.

## <a name="set-cache-capacity"></a>Definir capacidade de cache
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na página **Cache,** deve definir a capacidade da sua cache. Os valores aqui definidos determinam quanto supõe os dados que a sua cache pode reter e a rapidez com que pode atender os pedidos dos clientes.

A capacidade também afeta o custo do cache.

Escolha a capacidade definindo estes dois valores:

* A taxa máxima de transferência de dados para a cache (entrada), em GB/segundo
* A quantidade de armazenagem atribuída aos dados em cache, na Tuberculose

Escolha um dos valores de entrada disponíveis e tamanhos de armazenamento em cache.

Tenha em mente que a taxa real de transferência de dados depende da carga de trabalho, das velocidades da rede e do tipo de alvos de armazenamento. Os valores que escolher definir a potência máxima para todo o sistema de cache, mas parte disso é usado para tarefas aéreas. Por exemplo, se um cliente solicitar um ficheiro que ainda não está armazenado na cache, ou se o ficheiro estiver marcado como velho, a sua cache utiliza parte da sua entrada para o obter do armazenamento de backend.

O Azure HPC Cache gere quais os ficheiros que estão em cache e pré-carregados para maximizar as taxas de impacto do cache. Os conteúdos da cache são continuamente avaliados e os ficheiros são transferidos para armazenamento a longo prazo quando são menos frequentemente acedidos. Escolha um tamanho de armazenamento de cache que possa manter confortavelmente o conjunto ativo de ficheiros de trabalho com espaço adicional para metadados e outras despesas gerais.

![screenshot da página de tamanho cache](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>Adicionar etiquetas de recursos (opcional)

A página **Tags** permite adicionar [etiquetas](https://go.microsoft.com/fwlink/?linkid=873112) de recursos à sua instância de Cache Azure HPC.

## <a name="finish-creating-the-cache"></a>Termine de criar a cache

Depois de configurar a nova cache, clique no **separador Rever + criar.** O portal valida as suas seleções e permite-lhe rever as suas escolhas. Se estiver tudo correto, clique em **Criar**.

A criação de cache leva cerca de 10 minutos. Pode acompanhar os progressos no painel de notificações do portal Azure.

![screenshot da criação de cache "implantação em curso" e páginas de "notificações" no portal](media/hpc-cache-deploy-status.png)

Quando a criação termina, uma notificação aparece com um link para a nova instância de Cache Azure HPC, e a cache aparece na lista de **Recursos** da sua subscrição.
<!-- double check on notification -->

![screenshot da instância Azure HPC Cache no portal Azure](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Passos seguintes

Depois da sua cache aparecer na lista **de Recursos,** defina os alvos de armazenamento para dar acesso à sua cache às suas fontes de dados.

* [Adicionar destinos de armazenamento](hpc-cache-add-storage.md)
