---
title: Crie um teste de Analisador de Internet utilizando o Portal | Microsoft Docs
description: Neste artigo, aprenda a criar o seu primeiro teste de Analisador de Internet.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "73501772"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Criar um teste de Analisador de Internet utilizando o Portal (Pré-visualização)

Existem duas formas de criar um recurso do Internet Analyzer, utilizando o portal Azure ou utilizando [o CLI.](internet-analyzer-cli.md) Esta secção ajuda-o a criar um novo recurso Azure Internet Analyzer utilizando a nossa experiência portal.

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

A pré-visualização pública está disponível para ser utilizada globalmente; no entanto, o armazenamento de dados é limitado a *US West 2* durante a pré-visualização.

## <a name="basics"></a>Noções básicas

1. Obtenha acesso de pré-visualização do Analisador de Internet seguindo [](internet-analyzer-faq.md)as instruções **de pré-visualização?**
2. A partir da página inicial no [portal Azure,](https://preview.portal.azure.com)clique **+ Crie um recurso.** O Internet Analyzer encontra-se atualmente disponível apenas a partir da versão de pré-visualização do portal Azure.
3. Na página **Nova,** procure "Internet Analyzer" no campo *Search the Marketplace.*
4. Clique **no Analisador de Internet (pré-visualização)**. Certifique-se de que o editor é a *Microsoft* e a categoria é *Networking*.
5. Na página **do Analisador de Internet (pré-visualização),** clique em **Criar** para abrir a página **Criar um Analisador de Internet.**
6. Especifique as seguintes definições de configuração para o seu recurso Internet Analyzer:

    * **Assinatura:** A subscrição do Azure para hospedar o novo recurso Internet Analyzer. **_Utilize o mesmo ID de subscrição usado para solicitar o acesso de pré-visualização._**
    * **Grupo de Recursos:** O grupo de recursos Azure em que o novo recurso Internet Analyzer será criado. Se não tiver um grupo de recursos existente, pode criar um novo.
    * **Nome:** O nome do novo perfil de recursos do Analisador de Internet.
    * **Região:** A região pública de Azure em que o recurso será criado. Durante a pré-visualização, *apenas us West 2* está disponível.

7. Quando terminar de especificar as definições de perfil, clique em **Rever + criar**.

## <a name="configuration"></a>Configuração

Completar os passos básicos é um pré-requisito para configurar um teste e incorporar o cliente JavaScript. Depois de criar um perfil, vá a **Definições > Configuração** para configurar o seu primeiro teste.

1. Dê ao seu teste um nome na caixa **de nome de teste.**
2. Adicione uma descrição para o seu teste no campo **Descrição.**
3. Clique **em Configurar Ponto final** - aparecerá um separador do lado direito. Selecione o tipo de ponto final que pretende configurar- uma única região de Azure, várias regiões do Azure ou um ponto final personalizado.

    >
    ***Pontos finais pré-configurados: combinações individuais e múltiplas da região de Azure***
    * Selecione uma região ou conjunto de regiões a partir de uma [lista pré-configurada de pontos finais Azure](internet-analyzer-faq.md).
    * Em seguida, selecione o tipo de aplicação ou arquitetura de entrega de conteúdos que gostaria de avaliar.
        * Região do Azure Único: Aceleração do local[(Azure Front Door),](https://azure.microsoft.com/services/frontdoor/)caching de conteúdo estático[(Azure CDN para a Microsoft),](https://azure.microsoft.com/services/cdn/)ou nenhuma.
        * Múltiplas regiões do Azure: Aceleração do local[(Azure Front Door),](https://azure.microsoft.com/services/frontdoor/)direção DNS[(Azure Traffic Manager)](https://azure.microsoft.com/services/traffic-manager/)  

    ***Pontos finais personalizados***
    * Siga as instruções na página ['Criar ponto final personalizado'.](internet-analyzer-custom-endpoint.md)
    * Cole a localização DO URL HTTPS da imagem de um pixel no portal.
    >

4. Clique **em Adicionar** para adicionar o ponto final ao seu teste.
5. Repita os passos 1-4 para configurar o segundo ponto final. O ponto final B é sempre medido em relação ao Ponto Final A - ao configurar pontos finais, considere qual o ponto final que deve ser o seu controlo de teste.
6. Clique no botão **Guardar** para guardar o seu teste. Uma vez que você guarda um teste, você não pode mais editar os pontos finais de um teste particular.
7. Selecione os testes(s) que pretende iniciar e clique em **Iniciar teste**. Isto mudará o **_Estado_*_ do seu(s) teste(s) para _*_Running_**. Pode iniciar testes a qualquer momento, mas o cliente JavaScript tem de ser incorporado para o teste para começar a recolher medições.
8. Adicione mais testes a qualquer momento. Note que o cliente JavaScript único não será gerado até que um teste seja criado.

## <a name="embed-client"></a>Cliente incorporado

Para iniciar qualquer teste, o cliente JavaScript tem de ser incorporado na sua aplicação Web. Depois de configurar pelo menos um teste, clique em **Rever + criar,** vá a **Definições > Configuração** e copie o cliente JavaScript. As instruções específicas podem ser encontradas na página do [Cliente analisador de Internet incorporado.](internet-analyzer-embed-client.md)  

## <a name="next-steps"></a>Passos seguintes

* Leia o [Analisador de Internet FAQ](internet-analyzer-faq.md)
* Saiba mais sobre a incorporação do [Cliente Analisador de Internet](internet-analyzer-embed-client.md) e a criação de um ponto final [personalizado.](internet-analyzer-custom-endpoint.md)
