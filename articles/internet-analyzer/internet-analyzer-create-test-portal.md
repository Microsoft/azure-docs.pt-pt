---
title: Criar um teste de Análise da Internet utilizando o Portal [ Microsoft Docs
description: Neste artigo, aprenda a criar o seu primeiro teste de Análise de Internet.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73501772"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Criar um teste de análise da Internet utilizando o Portal (Pré-visualização)

Existem duas formas de criar um recurso do Analisador de Internet, utilizando o portal Azure ou utilizando o [CLI](internet-analyzer-cli.md). Esta secção ajuda-o a criar um novo recurso Azure Internet Analyzer utilizando a nossa experiência portal.

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

A pré-visualização pública está disponível para ser usada globalmente; no entanto, o armazenamento de dados está limitado a *US West 2* durante a pré-visualização.

## <a name="basics"></a>Noções básicas

1. Obtenha acesso de pré-visualização do Internet Analyzer seguindo o [Azure Internet Analyzer FAQ](internet-analyzer-faq.md) **como posso participar na pré-visualização?**
2. A partir da página inicial do [portal Azure,](https://preview.portal.azure.com)clique **+ Crie um recurso**. O Internet Analyzer está atualmente disponível apenas a partir da versão de pré-visualização do portal Azure.
3. Na **página Nova,** procure "Internet Analyzer" no campo *Search the Marketplace.*
4. Clique em Análise de **Internet (pré-visualização)**. Certifique-se de que a editora é *a Microsoft* e a categoria é *Networking*.
5. Na página do Analisador de **Internet (pré-visualização),** clique em **Criar** para abrir a página Create a **Internet Analyzer.**
6. Especifique as seguintes definições de configuração para o seu recurso Analisador de Internet:

    * **Subscrição:** A subscrição azure para acolher o novo recurso do Analisador de Internet. ***Utilize o mesmo ID de subscrição utilizado para solicitar acesso de pré-visualização.***
    * **Grupo de Recursos:** O grupo de recursos Azure onde será criado o novo recurso do Analisador de Internet. Se não tiver um grupo de recursos existente, pode criar um novo.
    * **Nome:** O nome do novo perfil de recursos do Analisador de Internet.
    * **Região:** A região pública de Azure onde o recurso será criado. Durante a pré-visualização, apenas *o US West 2* está disponível.

7. Quando terminar de especificar as definições do seu perfil, clique em **Rever + criar**.

## <a name="configuration"></a>Configuração

Completar os passos básicos é um pré-requisito para configurar um teste e incorporar o cliente JavaScript. Depois de ter criado um perfil, vá a **Definições > Configuração** para configurar o seu primeiro teste.

1. Dê ao seu teste um nome na caixa de nomes de **teste.**
2. Adicione uma descrição para o seu teste no campo **Descrição.**
3. Clique em **Configurar Endpoint** - aparecerá um separador do lado direito. Selecione o tipo de ponto final que deseja configurar uma única região Azure, várias regiões azure ou um ponto final personalizado.

    >
    ***Pontos finais reconfigurados: combinações únicas e múltiplas da região de Azure***
    * Selecione uma região ou conjunto de regiões a partir de uma [lista pré-configurada de pontos finais Azure](internet-analyzer-faq.md).
    * Em seguida, selecione o tipo de aplicação ou arquitetura de entrega de conteúdos que gostaria de avaliar.
        * Região azure única: Aceleração do local ([Porta Frontal Azure),](https://azure.microsoft.com/services/frontdoor/)cachede conteúdo estático[(Azure CDN para microsoft](https://azure.microsoft.com/services/cdn/)), ou nenhum.
        * Múltiplas regiões azure: Aceleração do local ([Porta frontal Azure),](https://azure.microsoft.com/services/frontdoor/)direção DNS[(Gestor de Tráfego Azure)](https://azure.microsoft.com/services/traffic-manager/)  

    ***Pontos finais personalizados***
    * Siga as instruções na página [Create Custom Endpoint.](internet-analyzer-custom-endpoint.md)
    * Colar a localização DO URL HTTPS da imagem de um pixel no portal.
    >

4. Clique em **Adicionar** o ponto final ao seu teste.
5. Repita os passos 1-4 para configurar o segundo ponto final. O ponto final B é sempre medido em relação ao ponto final A - ao configurar pontos finais, considere qual o ponto final que deve ser o seu controlo de teste.
6. Clique no botão **Guardar** para salvar o seu teste. Uma vez que guarde um teste, não pode mais editar os pontos finais de um determinado teste.
7. Selecione os testes(s) que pretende iniciar e clicar no **teste Iniciar**. Isto mudará o ***estado*** dos seus testes para ***Correr***. Pode iniciar os testes a qualquer momento, mas o cliente JavaScript deve ser incorporado para que o teste comece a recolher medições.
8. Adicione mais testes em qualquer ponto. Note que o cliente exclusivo javaScript não será gerado até que um teste seja criado.

## <a name="embed-client"></a>Cliente incorporado

Para iniciar qualquer teste, o cliente JavaScript deve ser incorporado na sua aplicação Web. Depois de configurar pelo menos um teste, clique em **Rever + criar,** vá para **Definições > Configuração**, e copie o cliente JavaScript. Instruções específicas podem ser encontradas na página do Cliente do Analisador de [Internet Incorporado.](internet-analyzer-embed-client.md)  

## <a name="next-steps"></a>Passos seguintes

* Leia o [FaQ do Analisador](internet-analyzer-faq.md) de Internet
* Saiba mais sobre incorporar o [Cliente analisador](internet-analyzer-embed-client.md) de Internet e criar um [ponto final personalizado.](internet-analyzer-custom-endpoint.md)
