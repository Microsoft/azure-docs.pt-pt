---
title: Criar uma Cache Azure HPC
description: Como criar uma instância de Cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: efa9037b345cdfc5f165e9c5e0c1831ea97b52ed
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106496"
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

## <a name="enable-azure-key-vault-encryption-optional"></a>Ativar encriptação do Cofre de Chaves Azure (opcional)

Se o seu cache estiver numa região que suporta chaves de encriptação geridas pelo cliente, a página de chaves de **encriptação do Disco** aparece entre os separadores **Cache** e **Tags.** A partir do tempo de publicação, esta opção é apoiada nos EUA Orientais, Centro-Sul dos EUA e West US 2.

Se pretender gerir as chaves de encriptação utilizadas com o seu armazenamento de cache, forneça as informações do Cofre de Chaves Azure na página de chaves de **encriptação do Disco.** O cofre-chave deve estar na mesma região e na mesma subscrição que a cache.

Pode saltar esta secção se não precisar de chaves geridas pelo cliente. O Azure encripta dados com chaves geridas pela Microsoft por padrão. Leia encriptação de [armazenamento Azure](../storage/common/storage-service-encryption.md) para saber mais.

> [!NOTE]
>
> * Não é possível alterar entre as chaves geridas pela Microsoft e as chaves geridas pelo cliente após a criação do cache.
> * Depois da cache ser criada, deve autorizá-la a aceder ao cofre da chave. Clique no botão de **encriptação Enable** na página **'Overview'** da cache para ligar a encriptação. Dê este passo dentro de 90 minutos após a criação da cache.
> * Os discos cache são criados após esta autorização. Isto significa que o tempo inicial de criação do cache é curto, mas a cache não estará pronta para ser usada durante dez minutos ou mais após autorizar o acesso.

Para obter uma explicação completa do processo de encriptação da chave gerida pelo cliente, leia [Utilize as chaves de encriptação geridas pelo cliente para o Cache Azure HPC](customer-keys.md).

![screenshot da página de chaves de encriptação com campos de cofre selecionados e chave "geridos pelo cliente" mostrando](media/create-encryption.png)

Selecione **O Cliente conseguiu** escolher a encriptação da chave gerida pelo cliente. Os campos de especificação do cofre principais aparecem. Selecione o Cofre de Chave Azure para utilizar e, em seguida, selecione a tecla e a versão para utilizar para esta cache. A chave deve ser uma chave RSA de 2048. Pode criar um novo cofre de chaves, chave ou versão chave a partir desta página.

Depois de criar a cache, deve autorizá-la a utilizar o serviço de cofre chave. Leia a encriptação do Cofre de [Chaves Azure a partir da cache](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) para obter mais detalhes.

## <a name="add-resource-tags-optional"></a>Adicionar etiquetas de recursos (opcional)

A página **Tags** permite adicionar [etiquetas](https://go.microsoft.com/fwlink/?linkid=873112) de recursos à sua instância de Cache Azure HPC.

## <a name="finish-creating-the-cache"></a>Termine de criar a cache

Depois de configurar a nova cache, clique no **separador Rever + criar.** O portal valida as suas seleções e permite-lhe rever as suas escolhas. Se estiver tudo correto, clique em **Criar**.

A criação de cache leva cerca de 10 minutos. Pode acompanhar os progressos no painel de notificações do portal Azure.

![screenshot da criação de cache "implantação em curso" e páginas de "notificações" no portal](media/hpc-cache-deploy-status.png)

Quando a criação termina, uma notificação aparece com um link para a nova instância de Cache Azure HPC, e a cache aparece na lista de **Recursos** da sua subscrição.

![screenshot da instância Azure HPC Cache no portal Azure](media/hpc-cache-new-overview.png)

> [!NOTE]
> Se o seu cache utilizar chaves de encriptação geridas pelo cliente, a cache pode aparecer na lista de recursos antes que o estado de implementação se mude para ser concluído. Assim que o estado da cache estiver à espera da **chave,** [podeautorizar-a](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) a usar o cofre da chave.

## <a name="next-steps"></a>Passos seguintes

Depois da sua cache aparecer na lista de **Recursos,** pode passar para o próximo passo.

* [Defina os alvos de armazenamento](hpc-cache-add-storage.md) para dar acesso à sua cache às suas fontes de dados.
* Se utilizar as chaves de encriptação geridas pelo cliente, tem de autorizar a [encriptação do Cofre de Chaves Azure](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) a partir da página de visão geral do cache para completar a configuração da cache. Tem de fazer este passo antes de poder adicionar o armazenamento. Leia [Utilize as chaves de encriptação geridas pelo cliente](customer-keys.md) para obter detalhes.
