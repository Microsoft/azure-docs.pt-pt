---
title: Criar um teste do Internet Analyzer usando o portal | Microsoft Docs
description: Neste artigo, saiba como criar seu primeiro teste do Internet Analyzer.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501772"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Criar um teste do Internet Analyzer usando o portal (versão prévia)

Há duas maneiras de criar um recurso do Internet Analyzer – usando o portal do Azure ou usando a [CLI](internet-analyzer-cli.md). Esta seção ajuda você a criar um novo recurso do Azure Internet Analyzer usando nossa experiência com o Portal.

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

A visualização pública está disponível para uso global. no entanto, o armazenamento de dados é limitado ao *oeste dos EUA 2* durante a visualização.

## <a name="basics"></a>Noções básicas

1. Obtenha acesso à visualização do Internet Analyzer seguindo o **como fazer participar da versão prévia?** instruções das [perguntas frequentes sobre o Azure Internet Analyzer](internet-analyzer-faq.md).
2. Na home page na [portal do Azure](https://preview.portal.azure.com), clique em **+ criar um recurso**. Atualmente, o Internet Analyzer só está disponível na versão de visualização do portal do Azure.
3. Na página **novo** , pesquise "analisador da Internet" no campo *Pesquisar no Marketplace* .
4. Clique em **analisador da Internet (versão prévia)** . Verifique se o Publicador é a *Microsoft* e se a categoria está em *rede*.
5. Na página do **Internet Analyzer (versão prévia)** , clique em **criar** para abrir a página **criar um analisador da Internet** .
6. Especifique as seguintes definições de configuração para o recurso do Internet Analyzer:

    * **Assinatura:** A assinatura do Azure para hospedar o novo recurso do Internet Analyzer. ***Use a mesma ID de assinatura usada para solicitar acesso de visualização.***
    * **Grupo de recursos:** O grupo de recursos do Azure no qual o novo recurso do Internet Analyzer será criado. Se você não tiver um grupo de recursos existente, poderá criar um novo.
    * **Nome:** O nome do novo perfil de recurso do Internet Analyzer.
    * **Região:** A região pública do Azure em que o recurso será criado. Durante a visualização, somente o *oeste dos EUA 2* está disponível.

7. Quando terminar de especificar as configurações de perfil, clique em **revisar + criar**.

## <a name="configuration"></a>Configuração

A conclusão das etapas básicas é um pré-requisito para configurar um teste e inserir o cliente JavaScript. Depois de criar um perfil, vá para **configurações > configuração** para configurar seu primeiro teste.

1. Dê um nome ao seu teste na caixa **nome do teste** .
2. Adicione uma descrição para seu teste no campo **Descrição** .
3. Clique em **Configurar ponto de extremidade** -uma guia será exibida do lado direito. Selecione o tipo de ponto de extremidade que você deseja configurar-uma única região do Azure, várias regiões do Azure ou um ponto de extremidade personalizado.

    >
    ***Pontos de extremidade pré-configurados: combinações de uma única e várias regiões do Azure***
    * Selecione uma região ou um conjunto de regiões de uma [lista pré-configurada de pontos de extremidade do Azure](internet-analyzer-faq.md).
    * Em seguida, selecione o tipo de aplicativo ou a arquitetura de entrega de conteúdo que você gostaria de avaliar.
        * Única região do Azure: aceleração de site ([Azure front door](https://azure.microsoft.com/services/frontdoor/)), cache de conteúdo estático ([CDN do Azure para Microsoft](https://azure.microsoft.com/services/cdn/)) ou nenhum.
        * Várias regiões do Azure: aceleração de site ([Azure front door](https://azure.microsoft.com/services/frontdoor/)), direcionamento de DNS ([Gerenciador de tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/))  

    ***Pontos de extremidade personalizados***
    * Siga as instruções na página [criar ponto de extremidade personalizado](internet-analyzer-custom-endpoint.md) .
    * Cole o local da URL HTTPS da imagem de um pixel no Portal.
    >

4. Clique em **Adicionar** para adicionar o ponto de extremidade ao teste.
5. Repita as etapas 1-4 para configurar o segundo ponto de extremidade. O ponto de extremidade B sempre é medido em relação ao ponto de extremidade A – ao configurar pontos de extremidades, considere qual ponto de extremidade deve ser seu controle de teste.
6. Clique no botão **salvar** para salvar o teste. Depois de salvar um teste, você não poderá mais editar os pontos de extremidade de um teste específico.
7. Selecione os testes que você deseja iniciar e clique em **Iniciar teste**. Isso alterará o ***estado*** dos testes para ***em execução***. Você pode iniciar testes a qualquer momento, mas o cliente JavaScript deve ser inserido para que o teste comece a coletar medidas.
8. Adicione mais testes a qualquer momento. Observe que o cliente JavaScript exclusivo não será gerado até que um teste seja criado.

## <a name="embed-client"></a>Inserir cliente

Para iniciar qualquer teste, o cliente JavaScript deve ser inserido em seu aplicativo Web. Depois de configurar pelo menos um teste, clique em **revisar + criar**, vá para **Configurações > configuração**e copie o cliente JavaScript. Instruções específicas podem ser encontradas na página [Inserir cliente do Internet Analyzer](internet-analyzer-embed-client.md) .  

## <a name="next-steps"></a>Passos seguintes

* Leia as [perguntas frequentes do Internet Analyzer](internet-analyzer-faq.md)
* Saiba mais sobre como inserir o [cliente do Internet Analyzer](internet-analyzer-embed-client.md) e criar um [ponto de extremidade personalizado](internet-analyzer-custom-endpoint.md).
