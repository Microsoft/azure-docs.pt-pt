---
title: Criar uma Cache Azure HPC
description: Como criar uma instância cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 06/01/2020
ms.author: v-erkel
ms.openlocfilehash: 122ef938aad51e83f88cd7f1e4ab18e3975d7d80
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344306"
---
# <a name="create-an-azure-hpc-cache"></a>Criar uma Cache Azure HPC

Utilize o portal Azure para criar o seu cache.

![screenshot da visão geral da cache no portal Azure, com o botão criar na parte inferior](media/hpc-cache-home-page.png)

Clique na imagem abaixo para ver uma [demonstração](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) de vídeo de criar uma cache e adicionar um alvo de armazenamento.

[![miniatura de vídeo: Azure HPC Cache: Configuração (clique para visitar a página de vídeo)](media/video4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="define-basic-details"></a>Definir detalhes básicos

![screenshot da página de detalhes do projeto no portal Azure](media/hpc-cache-create-basics.png)

Em **Detalhes do Projeto,** selecione o grupo de subscrição e recursos que irá acolher a cache. Certifique-se de que a subscrição está na lista [de acesso.](hpc-cache-prereqs.md#azure-subscription)

Em **Detalhes de Serviço,** desateie o nome da cache e estes outros atributos:

* Localização - Selecione uma das [regiões apoiadas.](hpc-cache-overview.md#region-availability)
* Rede virtual - Pode selecionar uma existente ou criar uma nova rede virtual.
* Sub-rede - Escolha ou crie uma sub-rede com pelo menos 64 endereços IP (/24). Esta sub-rede deve ser utilizada apenas para esta instância cache Azure HPC.

## <a name="set-cache-capacity"></a>Definir capacidade de cache
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na página **Cache,** deve definir a capacidade do seu cache. Os valores aqui definidos determinam a quantidade de dados que o seu cache pode reter e a rapidez com que pode atender os pedidos do cliente.

A capacidade também afeta o custo do cache.

Escolha a capacidade definindo estes dois valores:

* A taxa máxima de transferência de dados para a cache (produção), em GB/segundo
* A quantidade de armazenamento atribuída aos dados em cache, em Tuberculose

Escolha um dos valores de produção disponíveis e tamanhos de armazenamento em cache.

Tenha em mente que a taxa real de transferência de dados depende da carga de trabalho, das velocidades de rede e do tipo de alvos de armazenamento. Os valores que escolher definem a produção máxima para todo o sistema de cache, mas alguns deles são utilizados para tarefas aéreas. Por exemplo, se um cliente solicitar um ficheiro que já não esteja armazenado na cache, ou se o ficheiro estiver marcado como velho, o seu cache utiliza parte da sua produção para o ir buscar ao armazenamento traseiro.

A Azure HPC Cache gere quais os ficheiros em cache e pré-carregados para maximizar as taxas de certeamento de cache. Os conteúdos da cache são continuamente avaliados e os ficheiros são transferidos para armazenamento a longo prazo quando são menos acessados. Escolha um tamanho de armazenamento de cache que possa confortavelmente segurar o conjunto ativo de ficheiros de trabalho, além de espaço adicional para metadados e outras despesas gerais.

![screenshot da página de tamanho cache](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Ativar encriptação do cofre da chave Azure (opcional)

Se o seu cache estiver numa região que suporta chaves de encriptação geridas pelo cliente, a página **das chaves de encriptação do disco** aparece entre os separadores **Cache** e **Tags.** Na altura da publicação, esta opção é suportada nos EUA, Centro Sul dos EUA e Nos 2 Oeste.

Se pretender gerir as chaves de encriptação utilizadas com o armazenamento do cache, forneça as informações do Cofre da Chave Azure na página das **chaves de encriptação** do disco. O cofre-chave deve estar na mesma região e na mesma subscrição que a cache.

Pode saltar esta secção se não precisar de chaves geridas pelo cliente. O Azure encripta os dados com as teclas geridas pela Microsoft por padrão. Leia [a encriptação de armazenamento Azure](../storage/common/storage-service-encryption.md) para saber mais.

> [!NOTE]
>
> * Não é possível alterar entre as teclas geridas pela Microsoft e as teclas geridas pelo cliente depois de criar o cache.
> * Depois da cache ser criada, deve autorizar o acesso ao cofre da chave. Clique no botão **de encriptação Ativar** na página **de visão geral** do cache para ativar a encriptação. Dê este passo dentro de 90 minutos após a criação da cache.
> * Os discos cache são criados após esta autorização. Isto significa que o tempo inicial de criação da cache é curto, mas a cache não estará pronta para ser usada por dez minutos ou mais depois de autorizar o acesso.

Para obter uma explicação completa do processo de encriptação da chave gerido pelo cliente, leia [Use as chaves de encriptação geridas pelo cliente para a cache Azure HPC](customer-keys.md).

![screenshot da página de chaves de encriptação com "cliente gerido" selecionado e campos de cofre chave mostrando](media/create-encryption.png)

O Cliente Selecionado **conseguiu** escolher a encriptação de chave gerida pelo cliente. Os campos de especificação do cofre aparecem. Selecione o Cofre da Chave Azure para utilizar e, em seguida, selecione a chave e a versão a utilizar para esta cache. A chave deve ser uma chave RSA de 2048. Pode criar um novo cofre, chave ou versão chave a partir desta página.

Depois de criar a cache, deve autorizar a utilização do serviço de cofre de chaves. Leia [a encriptação do Cofre da Chave Azure autorizada a partir da cache](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) para obter mais detalhes.

## <a name="add-resource-tags-optional"></a>Adicionar etiquetas de recursos (opcional)

A página **Tags** permite adicionar [etiquetas de recursos](https://go.microsoft.com/fwlink/?linkid=873112) à sua instância cache Azure HPC.

## <a name="finish-creating-the-cache"></a>Termine de criar a cache

Depois de configurar a nova cache, clique no **separador 'Rever +' criar.** O portal valida as suas seleções e permite-lhe rever as suas escolhas. Se tudo estiver correto, clique em **Criar**.

A criação de cache leva cerca de 10 minutos. Pode acompanhar o progresso no painel de notificações do portal Azure.

![screenshot da criação de cache "implantação em curso" e "notificações" páginas no portal](media/hpc-cache-deploy-status.png)

Quando a criação termina, uma notificação aparece com um link para a nova instância Azure HPC Cache, e a cache aparece na lista **de Recursos** da sua subscrição.

![screenshot de Azure HPC Cache instância no portal Azure](media/hpc-cache-new-overview.png)

> [!NOTE]
> Se o seu cache utilizar chaves de encriptação geridas pelo cliente, a cache pode aparecer na lista de recursos antes que o estado de implantação seja concluído. Assim que o estado da cache estiver à espera da **chave,** pode [autorizar a](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) utilização do cofre da chave.

## <a name="next-steps"></a>Próximos passos

Depois da sua cache aparecer na lista **de Recursos,** pode passar para o próximo passo.

* [Defina os alvos de armazenamento](hpc-cache-add-storage.md) para dar acesso ao seu cache às suas fontes de dados.
* Se utilizar as chaves de encriptação geridas pelo cliente, tem de autorizar a encriptação do Cofre da [Chave Azure](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) a partir da página geral do cache para completar a configuração do cache. Tem de dar este passo antes de poder adicionar armazenamento. Leia [Utilize as chaves de encriptação geridas pelo cliente](customer-keys.md) para obter detalhes.
