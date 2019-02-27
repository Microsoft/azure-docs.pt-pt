---
title: Aplicação do Azure oferecem separador Marketplace | Documentos da Microsoft
description: Utilize o separador de mercado para identificar ativos de marketing para uma oferta de aplicação do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 1203f33458c83bbde7a263022b78563bad183143
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870742"
---
# <a name="azure-application-marketplace-tab"></a>Separador do Marketplace de aplicações do Azure

Utilize o separador de mercado para descrever a aplicação do Azure e fornecer recursos de marketing. Este guia inclui as seguintes formas: Descrição geral, os artefactos de Marketing, gestão líder e legais.

## <a name="overview-form"></a>Formulário de descrição geral

O formulário de descrição geral tem os campos obrigatórios e opcionais, mostrados na captura de ecrã seguinte. Campos obrigatórios são indicted por um asterisco (*).

![Formulário de descrição geral](./media/azureapp-marketplace-overview.png)

A tabela seguinte descreve as definições a utilizar para a criação de uma loja para a oferta.

|    **Campo**       |  **Descrição**  |
|  ---------------   |  ---------------  |
|        Título            |        Título da oferta. Será apresentado forma destacada no marketplace. O comprimento máximo é de 50 carateres.           |
|         Resumo           |        Breve resumo da oferta. O comprimento máximo é 100 carateres.           |
|         Resumo completo           |        Já resumo da oferta (embora ele poderia ser o mesmo que o resumo). O comprimento máximo é de 256 carateres.           |
|       Descrição            |         Descrição da oferta. O comprimento máximo é 3000 carateres. Formatação de HTML simples é permitido, incluindo &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; e etiquetas de cabeçalho.           |
|        Identificador de marketing            |         Uma URL exclusiva para associar a esta oferta, normalmente inclui a sua organização e o nome da solução, 50 carateres de comprimento máximo. Escolha um identificador de marketing curto e amigável para o seu serviço. Isso será usado no marketplace URLs para esta oferta. Por exemplo, se o seu ID de publicador for "contoso" e o identificador do marketing é "sampleApp", o URL para a sua oferta no Azure Marketplace será https://azuremarketplace.microsoft.com/en-us/marketplace/apps/contoso.sampleApp          |
|        IDs de subscrição de pré-visualização            |         Adicione a partir de um para 100 identificadores de subscrição dos visualizadores. Estas subscrições white-listados terão acesso a sua oferta, enquanto ele está disponível em pré-visualização depois de ser publicado, antes de entrar no ar.          |
|         Ligações Úteis           |        Pode fornecer ligações para vários recursos para os utilizadores da sua oferta, como suporte, documentação, fóruns, etc. Certifique-se de adicionar pelo menos um link para sua documentação.            |
|         Sugestões de categorias (máx. 5)           |         Escolha até cinco categorias. Categorias selecionadas são utilizadas para mapear a sua oferta para as categorias de produtos disponíveis no Azure Marketplace e o Portal do Azure. Serão apresentadas nas páginas de procura e na sua página de detalhes do produto.          |

## <a name="marketing-artifacts"></a>Artefactos de marketing

O formulário de artefactos de Marketing tem os campos obrigatórios e opcionais, mostrados na captura de ecrã seguinte. Campos obrigatórios são indicted por um asterisco (*).

![Formulário de artefactos de marketing](./media/azureapp-marketplace-artifacts.png)

A tabela seguinte descreve os artefactos de marketing.

|    **Campo**       |  **Descrição**  |
|  ---------------   |  ---------------  |
|       Pequeno             |        40 x 40 pixels no formato PNG           |
|       Médio             |        90 x 90 pixels no formato PNG           |
|       Grande             |        115 x 115 pixels no formato PNG         |
|       Ampla             |        255 x 115 pixels no formato PNG           |
|       Destaque             |        815 x 290 pixels no formato PNG. Opcional. **Nota:** Não é possível eliminar o ícone de hero após ser carregada.           |
|       Capturas de ecrã (máx. 5)             |        Capturas de ecrã são apresentadas na sua página de detalhes do produto. Eles são uma boa forma de comunicar visualmente o que seu aplicativo faz e como ele funciona. Por exemplo, pode mostrar os diagramas de arquitetura ou utilizar ilustrações maiúsculas. Capturas de ecrã são opcionais e limitado a 5 por SKU. Para adicionar uma captura de ecrã:<ul><li>Selecione **+ adicionar captura de ecrã** para abrir a janela de captura de ecrã</li><li>**Nome** -introduza um nome/título (comprimento máximo de 100 carateres.)</li><li>**Carregar** -carregar a imagem. Tem de estar no formato PNG e o tamanho é 533 x 324 pixéis.</li></ul>           |
|        Adicionar vídeo            |        Vídeos são apresentados na sua página de detalhes do produto. Eles são uma boa forma de comunicar visualmente o que seu aplicativo faz e como ele funciona. Para adicionar um vídeo: <ul><li>Selecione **+ adicionar vídeo** para abrir a janela de vídeo</li><li>**Nome** -introduza um nome/título (comprimento máximo de 100 carateres.)</li><li>**Ligação** – introduza o URL para o site que está a alojar o vídeo (YouTube ou Vimeo)</li><li>**Miniatura** – carregar uma miniatura. Tem de estar no formato PNG e o tamanho é 533 x 324 pixéis.</li></ul>          |

### <a name="artifact-examples-in-azure-marketplace"></a>Exemplos de artefactos no Azure Marketplace

Captura de ecrã seguinte mostra um exemplo de um resultado de pesquisa de mercado.

![Resultado de pesquisa de oferta do Marketplace](./media/azureapp-marketplace-example-browse.png)

A imagem seguinte mostra como a oferta é exibida no Marketplace após um cliente clica no mosaico da oferta no resultado da pesquisa.

![Detalhes de resultado de pesquisa de oferta do Marketplace](./media/azureapp-marketplace-example-details.png)

### <a name="artifact-examples-in-azure-portal"></a>Exemplos de artefacto no Portal do Azure

O ecrã seguinte captura de mostrar como uma oferta é apresentada no Portal do Azure. A oferta de aplicação neste exemplo é encontrada, navegando até **Marketplace > Tudo > programação + teste > Jenkins**. A oferta do Jenkins mostra um logótipo, o título e o nome a apresentar do publicador.

![Procurar oferece no portal do Azure](./media/azureapp-portalbrowse-artifacts-jenkins.png)

Captura de ecrã seguinte mostra as informações detalhadas sobre a aplicação quando um utilizador seleciona o Jenkins.

![Detalhes da oferta no portal do Azure](./media/azureapp-portal-artifacts-jenkins-details.png)

### <a name="logo-guidelines"></a>Diretrizes para logótipos

Todos os logótipos carregados para o Portal de parceiro de Cloud devem seguir as diretrizes:

- O design do Azure tem uma paleta de cores simples. Mantenha o número de principal e secundário cores seu logótipo baixa.
- As cores de tema do Portal do Azure são brancas e preto. Evite utilizar essas cores como a cor de fundo para os logotipos. Utilize uma cor que tornarão seu logótipos proeminente no portal do Azure. Recomendamos cores primárias simples. Se estiver a utilizar um plano de fundo transparente, certifique-se de que o texto/logótipos não são branco, preto ou azul.
- Não utilize um plano de fundo de gradação no seu logótipo.
- Evite colocar texto, até mesmo sua empresa ou o nome da marca, sobre o logótipo. O aspeto e funcionalidade do seu logótipo deve ser "simples" e deve evitar gradientes.
- Não se estendem o logótipo.

#### <a name="hero-logo"></a>Logótipo de destaque

O logótipo de Hero é opcional.

>[!IMPORTANT]
>Não é possível eliminar o logótipo de Hero após ser carregada.

Utilize as seguintes diretrizes para um logótipo de Hero:

- Planos de fundo transparentes, pretas e brancas não são permitidos.
- Evite usar qualquer cor leve como plano de fundo para o logótipo. O Editor nome a apresentar, plano de título e a oferta longa resumo são apresentados na cor da fonte branco e tem se destacam em segundo plano.
- Evite utilizar a maioria dos textos quando estiver a conceber o logótipo. O nome do publicador, título do plano, oferta longa resumo e um botão de criar são incorporadas por meio de programação dentro o logótipo quando listados da oferta.
- Inclua um espaço retangular não utilizado no lado direito do seu logótipo hero. Este espaço em branco é 415 x 100 pixéis e deslocamento a partir do esquerda por 370 pixels.

## <a name="lead-management"></a>Numa gestão

A forma de levar gestão tem um campo opcional para configurar a gestão de oportunidades potenciais. Para configurar a gestão de oportunidades potenciais, selecione o destino de oportunidades potenciais na lista pendente. Captura de ecrã seguinte mostra os destinos disponíveis.

![Destino de gestão de oportunidades potenciais selecione](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Selecione o ícone de informações para ver esta mensagem: "Selecionar o sistema onde serão armazenados os suas oportunidades potenciais. Saiba como ligar ao seu sistema CRM [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) . "


Para obter mais informações, consulte [configurar oportunidades potenciais](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).

## <a name="legal"></a>Informações Legais

Utilize o formulário Legal para fornecer a documentação legal necessária para cada oferta.

Forneça as seguintes informações:

- **URL de política de privacidade** – introduza uma ligação para a política de privacidade da sua aplicação.
- **Termos de utilização** – introduza os termos de utilização para a sua aplicação. Os clientes têm de aceitar estes termos antes de eles experimentam a sua aplicação.

![Formulário legal](./media/azureapp-marketplace-legal.png)

## <a name="next-steps"></a>Passos Seguintes

[Separador Suporte](./cpp-support-tab.md)