---
title: Criar um cache do HPC do Azure (versão prévia)
description: Como criar uma instância de cache do HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 1cc77b24c96514f40c86115f7d611076facd406b
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71181037"
---
# <a name="create-an-azure-hpc-cache-preview"></a>Criar um cache do HPC do Azure (versão prévia)

Use o portal do Azure para criar o cache. 

![captura de tela da visão geral do cache no portal do Azure, com o botão criar na parte inferior](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definir detalhes básicos

![captura de tela da página de detalhes do projeto no portal do Azure](media/hpc-cache-create-basics.png)

Em **detalhes do projeto**, selecione a assinatura e o grupo de recursos que hospedarão o cache do HPC do Azure. Verifique se a assinatura está na lista de [acesso de visualização](hpc-cache-prereqs.md#azure-subscription) .

Em **detalhes do serviço**, defina o nome do cache e esses outros atributos:

* Local-selecione uma das [regiões com suporte](hpc-cache-overview.md#region-availability).
* Rede virtual-você pode selecionar um existente ou criar uma nova rede virtual.
* Sub-rede – escolha ou crie uma sub-rede com pelo menos 64 endereços IP (/24) que serão usados somente para o cache do HPC do Azure.

## <a name="set-cache-capacity"></a>Definir capacidade de cache
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na página **cache** , você deve definir a capacidade do cache do Azure HPC. Esse valor determina a quantidade de dados que seu cache pode conter e a rapidez com que ele pode atender às solicitações do cliente. Após o período de visualização pública, a capacidade também afetará o custo do cache.

A capacidade do cache é medida em operações de entrada/saída por segundo (IOPS). Escolha a capacidade definindo estes dois valores:

* A taxa máxima de transferência de dados para o cache (taxa de transferência), em GB/segundo
* A quantidade de armazenamento alocada para dados armazenados em cache, em TB

Escolha um dos valores de taxa de transferência disponíveis e tamanhos de armazenamento em cache. A capacidade de IOPS é calculada e mostrada abaixo dos seletores de valor.

Tenha em mente que a taxa de transferência de dados real depende da carga de trabalho, das velocidades de rede e do tipo de destinos de armazenamento. Se um arquivo não estiver no cache ou estiver marcado como obsoleto, o serviço usará alguma taxa de transferência para obtê-lo do armazenamento de back-end. O valor escolhido define a taxa de transferência máxima para todo o cache e nem todas elas estão disponíveis para solicitações de cliente.

Para o armazenamento em cache, o cache HPC do Azure gerencia quais arquivos são armazenados em cache e pré-carregados para maximizar as tarifas de acesso ao cache. O conteúdo do cache é avaliado continuamente e os arquivos são movidos para o armazenamento de longo prazo quando são acessados com menos frequência. Escolha um tamanho de armazenamento de cache que possa manter confortavelmente o conjunto ativo de arquivos de trabalho com espaço adicional para metadados e outras sobrecargas.

![captura de tela da página de dimensionamento do cache](media/hpc-cache-create-iops.png)

## <a name="add-storage-targets"></a>Adicionar destinos de armazenamento

Os destinos de armazenamento são o armazenamento de longo prazo e de back-end para o conteúdo do seu cache.

Você pode definir destinos de armazenamento ao criar o cache, mas também pode adicioná-los posteriormente com o link na seção **Configurar** da página do seu cache no Portal.

![captura de tela da página de destinos de armazenamento](media/hpc-cache-storage-targets-pop.png)

Clique no **link Adicionar destino de armazenamento** para definir seus sistemas de armazenamento de back-end. O armazenamento pode ser contêineres de blob do Azure ou sistemas NFS locais.

Você pode definir até dez destinos de armazenamento diferentes.

As instruções passo a passo para adicionar um destino de armazenamento estão incluídas em [Adicionar destinos de armazenamento](hpc-cache-add-storage.md). O procedimento é diferente para armazenamento de BLOBs ou para exportações NFS.

Aqui estão algumas dicas: 

* Para os dois tipos de armazenamento, você deve especificar como encontrar o sistema de armazenamento de back-end (um endereço NFS ou um nome de contêiner de BLOB) e o caminho do namespace voltado para o cliente.

* Ao criar um destino de armazenamento de BLOBs, verifique se o cache tem permissões de acesso à conta de armazenamento, conforme descrito em [adicionar funções de controle de acesso](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account). Se você não tiver certeza de que a configuração de função será bem-sucedida, crie o cache primeiro e, em seguida, adicione o armazenamento de BLOBs posteriormente.

* Ao criar um destino de armazenamento NFS, especifique um [modelo de uso](hpc-cache-add-storage.md#choose-a-usage-model). A configuração do modelo de uso ajuda o cache a otimizar seu fluxo de trabalho.

## <a name="add-resource-tags-optional"></a>Adicionar marcas de recurso (opcional)

A página **marcas** permite que você adicione [marcas de recurso](https://go.microsoft.com/fwlink/?linkid=873112) ao cache do HPC do Azure. 

## <a name="finish-creating-the-cache"></a>Concluir a criação do cache

Depois de configurar o novo cache, clique na guia **revisar + criar** . O portal valida suas seleções e permite que você examine suas escolhas. Se tudo estiver correto, clique em **criar**. 

A criação do cache leva cerca de 10 minutos. Você pode acompanhar o progresso no painel de notificações do portal do Azure. 

![captura de tela das páginas "implantação em andamento" e "notificações" de criação de cache no portal](media/hpc-cache-deploy-status.png)

Quando a criação for concluída, uma notificação será exibida com um link para a nova instância de cache do Azure HPC e o cache aparecerá na lista de **recursos** da sua assinatura. 

![captura de tela da instância de cache do HPC do Azure no portal do Azure](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Passos seguintes

Depois que o cache aparecer na lista de **recursos** , você poderá montá-lo para acesso de cliente, usá-lo para mover seus dados de conjunto de trabalho para um novo destino de armazenamento de BLOBs do Azure ou definir fontes de dados adicionais.

* [Montar o cache HPC do Azure](hpc-cache-mount.md)
* [Mover dados para o armazenamento de BLOBs do Azure para o cache HPC do Azure](hpc-cache-ingest.md)
* [Adicionar destinos de armazenamento](hpc-cache-add-storage.md)
