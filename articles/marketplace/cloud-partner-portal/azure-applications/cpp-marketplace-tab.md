---
title: Oferta de aplicação Azure Marketplace
description: Utilize o separador Marketplace para identificar ativos de marketing para uma oferta de aplicação Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 6113759cc68d07af4c22edf03b3274d92dcbe780
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416299"
---
# <a name="azure-application-marketplace-tab"></a>Separador de mercado de aplicações Azure

Utilize o separador Marketplace para descrever a sua aplicação Azure e fornecer ativos de marketing. Este separador inclui os seguintes formulários: Visão Geral, Artefactos de Marketing, Gestão de Chumbo e Legal.

## <a name="overview-form"></a>Formulário de visão geral

O formulário Overview tem os campos necessários e opcionais mostrados na captura do próximo ecrã. Os campos necessários são indiciados por um asterisco (*).

![Formulário de visão geral](./media/azureapp-marketplace-overview.png)

A tabela seguinte descreve as definições a utilizar para criar uma montra para a oferta.   Os campos anexados com um asterisco são necessários.

|      Campo         |    Descrição    |
|  ---------------   |  ---------------  |
| **Título\***        | Título da oferta. Será exibido proeminentemente no mercado. O comprimento máximo é de 50 caracteres. |
| **Resumo\***      | Resumo curto da oferta. O comprimento máximo é de 100 caracteres.           |
| **Resumo longo\*** | Resumo mais longo da oferta (embora possa ser o mesmo que o resumo). O comprimento máximo é de 256 caracteres.           |
| **Descrição\***  | Descrição da oferta. O comprimento máximo é de 3000 caracteres. É permitida uma formatação &lt;&gt;HTML &lt;&gt;simples, incluindo p, em, &lt;ul,&gt; &lt;&gt;li, &lt;ol&gt; e etiquetas de cabeçalho.  |
| **Identificador de Marketing\*** | Um URL único para associar a esta oferta, tipicamente inclui a sua organização e nome de solução, comprimento máximo 50 caracteres. Escolha um identificador de marketing curto e amigável para o seu serviço. Isto será usado em URLs de mercado para esta oferta. Por exemplo, se o seu ID da editora for "contoso" e o seu identificador de marketing for "sampleApp", o URL para a sua oferta no Azure Marketplace será`https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  
| **IDs de subscrição de pré-visualização\*** | Adicione de um a 100 identificadores de subscrição de pré-visualizadores. Estas subscrições listadas com brancos terão acesso à sua oferta enquanto está disponível em pré-visualização após a sua publicação, antes de entrar em funcionação.          |
| **Links úteis**    | Opcionalmente, pode fornecer links para vários recursos para os utilizadores da sua oferta, tais como suporte, documentação, fóruns, etc.  Recomenda-se que adicione pelo menos um link à sua documentação.            |
| **Categorias Sugeridas (Max 5)\*** | Selecione de uma a cinco categorias. As categorias selecionadas são usadas para mapear a sua oferta para as categorias de produtos disponíveis no Azure Marketplace e no Portal Azure. Serão mostrados nas páginas de navegação e na página de detalhes do produto. |
|  |  |


## <a name="marketing-artifacts"></a>Artefactos de Marketing

O formulário De Artefactos de Marketing tem os campos necessários e opcionais mostrados na captura do próximo ecrã. Os campos necessários são indiciados por um asterisco (*).

![Forma de artefactos de marketing](./media/azureapp-marketplace-artifacts.png)

A tabela seguinte descreve os artefactos de marketing.

|      Campo         |    Descrição    |
|  ---------------   |  ---------------  |
| **Pequeno\***        | Logotipo pequeno: 40x40 pixels em formato PNG     |
| **Medium\***       | Logotipo médio: 90x90 pixels em formato PNG    |
| **Grande\***        | Logotipo grande: 115x115 pixels em formato PNG   |
| **Largo\***         | Logotipo largo: 255x115 pixels em formato PNG    |
| **Herói**           | Logotipo de herói opcional: 815x290 pixels em formato PNG. **Nota:** O ícone do herói não pode ser apagado depois de ser carregado. |
| **Screenshots (Max 5)** |        As imagens são exibidas na página de detalhes do produto. São uma boa forma de comunicar visualmente o que a sua aplicação faz e como funciona. Por exemplo, pode mostrar diagramas de arquitetura ou usar ilustrações de caso. As imagens são opcionais, e você limitado a 5 por SKU. Para adicionar uma imagem:<ul><li>Selecione **+ Adicione screenshot** para abrir a janela Screenshot</li><li>**Nome** - Introduza um nome/título (comprimento máximo de 100 caracteres.)</li><li>**Upload** - Faça upload da imagem. Deve estar em formato PNG, e o tamanho é de 533 x 324 pixels.</li></ul>           |
| **Adicionar vídeo**      | Opcional, os vídeos são exibidos na página de detalhes do produto. São uma boa forma de comunicar visualmente o que a sua aplicação faz e como funciona. Para adicionar um vídeo: <ul><li>Selecione **+ Adicione vídeo** para abrir a janela Vídeo</li><li>**Nome** - Introduza um nome/título (comprimento máximo de 100 caracteres.)</li><li>**Link** - Introduza o URL para o site que está hospedando o vídeo (YouTube ou Vimeo)</li><li>**Miniatura** - Faça upload de uma miniatura. Deve estar em formato PNG, e o tamanho é de 533 x 324 pixels.</li></ul>          |
|  |  |


### <a name="artifact-examples-in-azure-marketplace"></a>Exemplos de artefactos no Azure Marketplace

A próxima captura de ecrã mostra um exemplo de um resultado de pesquisa do Marketplace.

![Resultado da pesquisa de oferta de mercado](./media/azureapp-marketplace-example-browse.png)

A imagem que se segue mostra como a oferta é exibida no Marketplace depois de um cliente clicar no azulejo da oferta no resultado da pesquisa.

![Marketplace oferecer detalhes do resultado da pesquisa](./media/azureapp-marketplace-example-details.png)


### <a name="artifact-examples-in-azure-portal"></a>Exemplos de artefactos no Portal Azure

As capturas de ecrã seguem mostram como uma oferta é exibida no Portal Azure. A oferta de aplicação neste exemplo é encontrada navegando para **marketplace>Everything>Dev + Test>Jenkins.** A oferta de Jenkins mostra um logotipo, título e nome de exibição de editor.

![Procure ofertas no portal Azure](./media/azureapp-portalbrowse-artifacts-jenkins.png)

A próxima captura do ecrã mostra informações detalhadas sobre a aplicação quando um utilizador seleciona Jenkins.

![Oferecer detalhes no portal Azure](./media/azureapp-portal-artifacts-jenkins-details.png)


### <a name="logo-guidelines"></a>Diretrizes para logótipos

Todos os logótipos enviados para o Portal do Parceiro cloud devem seguir as orientações:

- O design do Azure tem uma paleta de cores simples. Mantenha o número de cores primárias e secundárias no seu logotipo baixo.
- As cores temáticas do Portal Azure são brancas e pretas. Evite utilizar estas cores como cor de fundo para os seus logotipos. Use uma cor que tornará os seus logótipos proeminentes no portal Azure. Recomendamos cores primárias simples. Se estiver a usar um fundo transparente, certifique-se de que os logótipos/texto não são brancos, pretos ou azuis.
- Não use um fundo de gradiente no seu logotipo.
- Evite colocar texto, mesmo a sua empresa ou marca, no logotipo. A aparência e a sensação do seu logotipo devem ser "planas" e devem evitar gradientes.
- Não estique o logotipo.


#### <a name="hero-logo"></a>Logótipo de destaque

O logotipo do Herói é opcional.

>[!IMPORTANT]
>Não pode apagar o logótipo do Herói depois de ser carregado.

Utilize as seguintes diretrizes para um logotipo herói:

- Fundos pretos, brancos e transparentes não são permitidos.
- Evite utilizar qualquer cor clara como pano de fundo para o logotipo. O nome da exibição da editora, o título do plano e o resumo da oferta são apresentados em cor de fonte branca e devem destacar-se em segundo plano.
- Evite utilizar a maioria do texto quando estiver a desenhar o logótipo. O nome da editora, o título do plano, o resumo da oferta e um botão de criação estão incorporados programáticamente dentro do logótipo quando a oferta está listada.
- Inclua um espaço retangular não utilizado no lado direito do seu logotipo herói. Este espaço em branco é de 415x100 pixels e compensado pela esquerda por 370 pixels.


## <a name="lead-management"></a>Gestão de Chumbo

O formulário Lead Management tem um campo opcional para configurar a gestão de chumbo. Para configurar a gestão de chumbo, selecione o destino Lead da lista de dropdown. A próxima captura de ecrã mostra os destinos disponíveis.

![Selecione destino de gestão de chumbo](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Selecione o ícone da informação para ver esta mensagem: "Selecione o sistema onde os seus cabos serão armazenados. Aprenda a ligar-se ao seu sistema CRM [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) ."

Para mais informações, consulte as pistas do [cliente Configure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).


## <a name="legal"></a>Legal

Utilize o formulário Legal para fornecer a documentação legal necessária para cada oferta.

Forneça as seguintes informações:

- **URL\* de política** de privacidade - Introduza um link para a política de privacidade da sua aplicação.
- **Termos de\* utilização** - Introduza os termos de utilização da sua aplicação. Os clientes são obrigados a aceitar estes termos antes de poderem experimentar a sua aplicação.

![Formulário legal](./media/azureapp-marketplace-legal.png)


## <a name="next-steps"></a>Passos seguintes

[Separador Suporte](./cpp-support-tab.md)
