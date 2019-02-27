---
title: Otimizar a CDN do Azure para o tipo de entrega de conteúdos
description: Otimizar a CDN do Azure para o tipo de entrega de conteúdos
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2018
ms.author: magattus
ms.openlocfilehash: 5be1835de2aa1631c4ec2115dd34ea85f0267403
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870895"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>Otimizar a CDN do Azure para o tipo de entrega de conteúdos

Quando publicar conteúdo para uma vasta audiência global, é fundamental para garantir a entrega otimizada do seu conteúdo. [Rede de entrega de conteúdos do Azure (CDN)](cdn-overview.md) pode otimizar a experiência de entrega com base no tipo de conteúdo que tem. O conteúdo pode ser um Web site, uma transmissão em direto, um vídeo ou um arquivo grande para download. Quando cria um ponto final da CDN, especifique um cenário no **otimizado para** opção. À sua escolha determina qual otimização é aplicada ao conteúdo entregue a partir do ponto final da CDN.

Opções de otimização são projetadas para usar os comportamentos de melhores práticas para melhorar o desempenho de entrega de conteúdos e descarga de origem melhor. As opções de cenário afetam o desempenho modificando as configurações para a colocação em cache parcial, a segmentação de objeto e a política de repetição de falha de origem. 

Este artigo fornece uma descrição geral de várias funcionalidades de Otimização e quando deve usá-los. Para obter mais informações sobre os recursos e limitações, consulte os respectivos artigos em cada tipo de otimização individuais.

> [!NOTE]
> Quando cria um ponto final da CDN, o **otimizado para** opções podem variar com base no tipo de perfil é criado o ponto final. Fornecedores CDN do Azure aplicam-se melhoria de diversas formas, dependendo do cenário. 

## <a name="provider-options"></a>Opções do fornecedor

**CDN Standard do Azure da Microsoft** perfis suporta as seguintes otimizações:

* [Entrega geral web](#general-web-delivery). Esta otimização também é utilizada para suportes de dados de transmissão em fluxo e transferência de ficheiros grandes.


**CDN Standard do Azure da Verizon** e **CDN do Azure Premium da Verizon** perfis suportam as seguintes otimizações:

* [Entrega geral web](#general-web-delivery). Esta otimização também é utilizada para suportes de dados de transmissão em fluxo e transferência de ficheiros grandes.

* [Aceleração de site dinâmico](#dynamic-site-acceleration) 


**CDN Standard do Azure da Akamai** perfis suportam as seguintes otimizações:

* [Entrega geral web](#general-web-delivery) 

* [Transmissão geral de multimédia](#general-media-streaming)

* [Transmissão de multimédia de vídeo a pedido](#video-on-demand-media-streaming)

* [Transferência de ficheiros grandes](#large-file-download)

* [Aceleração de site dinâmico](#dynamic-site-acceleration) 

A Microsoft recomenda que teste variações de desempenho entre provedores diferentes para selecionar o fornecedor ideal para a sua entrega.

## <a name="select-and-configure-optimization-types"></a>Selecionar e configurar tipos de otimização

Quando cria um ponto final da CDN, selecione um tipo de otimização que descreva o cenário e tipo de conteúdo que pretende que o ponto final para entregar. **Entrega geral web** é a seleção predefinida. Para existente **CDN do Azure Standard da Akamai** pontos finais apenas, pode atualizar a opção de otimização em qualquer altura. Esta alteração não interrompe a entrega a partir da CDN do Azure. 

1. Num **CDN do Azure Standard da Akamai** de perfil, selecione um ponto de extremidade.

    ![Seleção de ponto final](./media/cdn-optimization-overview/01_Akamai.png)

2. Em definições, selecione **otimização**. Em seguida, selecione um tipo a partir da **otimizado para** na lista pendente.

    ![Otimização e o tipo de seleção](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Otimização para cenários específicos

Pode otimizar o ponto final da CDN para um desses cenários. 

### <a name="general-web-delivery"></a>Entrega geral Web

Entrega geral web é a opção de otimização mais comuns. Foi concebido para a otimização de conteúdo web geral, tais como aplicações web e de páginas da Web. Esta otimização também pode ser utilizada para o ficheiro e downloads de vídeo.

Um site típico contém conteúdo estático e dinâmico. Conteúdo estático inclui imagens, bibliotecas JavaScript e folhas de estilos que podem ser armazenados em cache e entregues aos utilizadores diferentes. Conteúdo dinâmico é personalizado para um utilizador individual, tais como itens de notícias são adaptadas para um perfil de utilizador. Não é colocado em cache de conteúdo dinâmico, como o conteúdo do carrinho de compras, uma vez que é exclusivo para cada utilizador. Entrega geral web pode otimizar o seu site inteiro. 

> [!NOTE]
> Se estiver a utilizar um **CDN do Azure Standard da Akamai** de perfil, selecione este tipo de otimização se o tamanho de arquivo média é menor do que 10 MB. Caso contrário, se o tamanho de arquivo média for superior a 10 MB, selecione **transferência de ficheiros grandes** partir do **otimizado para** na lista pendente.

### <a name="general-media-streaming"></a>Transmissão geral de multimédia

Se precisar de utilizar o ponto final de transmissão em direto e vídeo a pedido de transmissão em fluxo, selecione o tipo de otimização de transmissão em fluxo de suporte geral.

Suporte de dados de transmissão em fluxo é sensíveis ao tempo, porque os pacotes que chegam tardia no cliente, como o armazenamento em buffer frequente de conteúdo de vídeo, podem causar uma experiência de visualização degradada. Suporte de dados de transmissão em fluxo otimização reduz a latência de entrega de conteúdos de multimédia e fornece uma experiência de transmissão em fluxo uniforme para os usuários. 

Este cenário é comum para clientes do serviço de multimédia do Azure. Quando utiliza os serviços de multimédia do Azure, obtém um único ponto final de transmissão que pode ser utilizado para transmissão em direto e a pedido. Com este cenário, os clientes não precisam de mudar para outro ponto final quando mudam de em direto para a pedido de transmissão em fluxo. Otimização de transmissão em fluxo geral de multimédia oferece suporte a esse tipo de cenário.

Para **CDN Standard do Microsoft Azure**, **CDN do Azure Standard da Verizon**, e **CDN do Azure Premium da Verizon**, utilize o tipo de otimização de entrega geral web para disponibilize conteúdo de multimédia de transmissão em fluxo geral.

Para obter mais informações sobre a Otimização da transmissão em fluxo de multimédia, veja [suporte de dados de transmissão em fluxo otimização](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Transmissão de multimédia de vídeo a pedido

Otimização de transmissão em fluxo de multimédia de vídeo a pedido melhora o conteúdo de transmissão em fluxo de vídeo a pedido. Se utilizar um ponto final de transmissão em fluxo de vídeo a pedido, utilize esta opção.

Para **CDN Standard do Microsoft Azure**, **CDN do Azure Standard da Verizon**, e **CDN do Azure Premium da Verizon** perfis, utilizar a otimização de entrega geral web Escreva para disponibilizar conteúdo de multimédia de transmissão em fluxo vídeo a pedido.

Para obter mais informações sobre a Otimização da transmissão em fluxo de multimédia, veja [suporte de dados de transmissão em fluxo otimização](cdn-media-streaming-optimization.md).

> [!NOTE]
> Se o ponto final da CDN serve principalmente conteúdo de vídeo a pedido, utilize este tipo de otimização. A principal diferença entre esse tipo de Otimização e o suporte de dados geral, o tipo de otimização de transmissão em fluxo é o tempo limite de tentativas de ligação. O tempo limite é muito menor para trabalhar com cenários de transmissão em fluxo em direto.
>

### <a name="large-file-download"></a>Transferência de ficheiro grande

Para **CDN do Azure Standard da Akamai** perfis de ficheiros grandes downloads são otimizados para o conteúdo mais de 10 MB. Se o tamanho de arquivo média é menor do que 10 MB, utilize a entrega geral web. Se os tamanhos de arquivos média são consistentemente maiores do que 10 MB, poderá ser mais eficiente para criar um ponto de final separado para ficheiros grandes. Por exemplo, o firmware ou atualizações de software normalmente são arquivos grandes. Para entregar os ficheiros mais de 1,8 GB, a otimização de transferência de ficheiros grandes é necessária.

Para **CDN Standard do Microsoft Azure**, **CDN do Azure Standard da Verizon**, e **CDN do Azure Premium da Verizon** perfis, utilizar a otimização de entrega geral web Escreva para fornecer conteúdo de transferência de ficheiros grandes. Não é sem limite de tamanho de download do arquivo.

Para obter mais informações sobre a otimização de ficheiros grandes, veja [otimização de ficheiros grandes](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Aceleração de site dinâmico

 Aceleração de sites dinâmicos (DSA) está disponível para **CDN do Azure Standard da Akamai**, **CDN do Azure Standard da Verizon**, e **CDN do Azure Premium da Verizon** perfis. Essa otimização envolve uma taxa adicional a utilizar. Para obter mais informações, consulte [preços de rede de entrega de conteúdos](https://azure.microsoft.com/pricing/details/cdn/).

DSA inclui várias técnicas que tiram partido a latência e o desempenho do conteúdo dinâmico. Técnicas incluem a Otimização da rota e da rede, a otimização de TCP e muito mais. 

Pode usar essa otimização para acelerar a uma aplicação web que inclui várias respostas que não estão em cache. Os exemplos são os resultados da pesquisa, transações de Check-out ou dados em tempo real. Pode continuar a utilizar os principais recursos de colocação em cache da CDN do Azure para dados estáticos. 

Para obter mais informações sobre aceleração de sites dinâmicos, consulte [aceleração de sites dinâmicos](cdn-dynamic-site-acceleration.md).



