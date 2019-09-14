---
title: Guia Marketplace da oferta de aplicativo do Azure
description: Use a guia Marketplace para identificar ativos de marketing para uma oferta de aplicativo do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: b5d54e4c6744675da0dea5b095590b71df6d2641
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962864"
---
# <a name="azure-application-marketplace-tab"></a>Guia Marketplace de aplicativos do Azure

Use a guia Marketplace para descrever seu aplicativo do Azure e fornecer ativos de marketing. Essa guia inclui os seguintes formulários: Visão geral, artefatos de marketing, gerenciamento de leads e ofício.

## <a name="overview-form"></a>Formulário de visão geral

O formulário de visão geral tem os campos obrigatórios e opcionais mostrados na próxima captura de tela. Os campos obrigatórios são indicted por um asterisco (*).

![Formulário de visão geral](./media/azureapp-marketplace-overview.png)

A tabela a seguir descreve as configurações a serem usadas para criar uma vitrine para a oferta.   Os campos anexados a um asterisco são obrigatórios.

|      Campo         |    Descrição    |
|  ---------------   |  ---------------  |
| **Título\***        | Título da oferta. Ele será exibido em destaque no Marketplace. O comprimento máximo é de 50 caracteres. |
| **Resumo\***      | Breve resumo da oferta. O comprimento máximo é de 100 caracteres.           |
| **Resumo longo\*** | Resumo mais longo da oferta (embora possa ser o mesmo que o resumo). O comprimento máximo é de 256 caracteres.           |
| **Ndescrição\***  | Descrição da oferta. O comprimento máximo é de 3000 caracteres. A formatação HTML simples é permitida, &lt;incluindo&gt;marcas &lt;p&gt;, &lt;em&gt;, &lt;UL&gt;, &lt;li,ol&gt; e header.  |
| **Identificador de marketing\*** | Uma URL exclusiva para associar a esta oferta, normalmente inclui o nome da sua organização e da solução, o comprimento máximo de 50 caracteres. Escolha um identificador de marketing curto e amigável para seu serviço. Isso será usado em URLs do Marketplace para esta oferta. Por exemplo, se sua ID de editor for "contoso" e seu identificador de marketing for "sampleApp", a URL para sua oferta no Azure Marketplace será https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp  
| **Visualizar IDs de assinatura\*** | Adicione de um a 100 identificadores de assinatura de visualizações. Essas assinaturas listadas em branco terão acesso à sua oferta, enquanto estiverem disponíveis na visualização depois de publicadas, antes de entrar em tempo real.          |
| **Links úteis**    | Opcionalmente, você pode fornecer links para vários recursos para os usuários de sua oferta, como suporte, documentação, fóruns, etc.  É recomendável que você adicione pelo menos um link à sua documentação.            |
| **Categorias sugeridas (máx. 5)\*** | Selecione uma a cinco categorias. As categorias selecionadas são usadas para mapear sua oferta para as categorias de produto disponíveis no Azure Marketplace e no portal do Azure. Eles serão mostrados em procurar páginas e na página de detalhes do produto. |
|  |  |


## <a name="marketing-artifacts"></a>Artefatos de marketing

O formulário artefatos de marketing tem os campos obrigatórios e opcionais mostrados na próxima captura de tela. Os campos obrigatórios são indicted por um asterisco (*).

![Formulário artefatos de marketing](./media/azureapp-marketplace-artifacts.png)

A tabela a seguir descreve os artefatos de marketing.

|      Campo         |    Descrição    |
|  ---------------   |  ---------------  |
| **Menores\***        | Logotipo pequeno: 40x40 pixels no formato PNG     |
| **Médio\***       | Logotipo médio: 90x90 pixels no formato PNG    |
| **Vários\***        | Logotipo grande: 115x115 pixels no formato PNG   |
| **Amplia\***         | Logotipo amplo: 255x115 pixels no formato PNG    |
| **'**           | Logotipo opcional do Hero: 815x290 pixels no formato PNG. **Nota:** O ícone Hero não pode ser excluído após ser carregado. |
| **Capturas de tela (máx. 5)** |        Capturas de tela são exibidas na página de detalhes do produto. Eles são uma boa maneira de comunicar visualmente o que seu aplicativo faz e como ele funciona. Por exemplo, você pode mostrar diagramas de arquitetura ou ilustrações de caso de uso. Capturas de tela são opcionais e você se limita a 5 por SKU. Para adicionar uma captura de tela:<ul><li>Selecione **+ Adicionar captura de tela** para abrir a janela de captura de tela</li><li>**Nome** – Insira um nome/título (comprimento máximo de 100 caracteres).</li><li>**Carregar** – carregue a imagem. Ele deve estar no formato PNG e o tamanho é 533 x 324 pixels.</li></ul>           |
| **Adicionar vídeo**      | Opcional, os vídeos são exibidos na página de detalhes do produto. Eles são uma boa maneira de comunicar visualmente o que seu aplicativo faz e como ele funciona. Para adicionar um vídeo: <ul><li>Selecione **+ Adicionar vídeo** para abrir a janela de vídeo</li><li>**Nome** – Insira um nome/título (comprimento máximo de 100 caracteres).</li><li>**Link** – Insira a URL para o site que está hospedando o vídeo (YouTube ou Vimeo)</li><li>**Miniatura** – carregar uma miniatura. Ele deve estar no formato PNG e o tamanho é 533 x 324 pixels.</li></ul>          |
|  |  |


### <a name="artifact-examples-in-azure-marketplace"></a>Exemplos de artefato no Azure Marketplace

A próxima captura de tela mostra um exemplo de um resultado de pesquisa do Marketplace.

![Resultado da pesquisa da oferta do Marketplace](./media/azureapp-marketplace-example-browse.png)

A imagem a seguir mostra como a oferta é exibida no Marketplace depois que um cliente clica no bloco da oferta no resultado da pesquisa.

![Detalhes do resultado da pesquisa de oferta do Marketplace](./media/azureapp-marketplace-example-details.png)


### <a name="artifact-examples-in-azure-portal"></a>Exemplos de artefato no portal do Azure

As capturas de tela a seguir mostram como uma oferta é exibida no portal do Azure. A oferta de aplicativo neste exemplo é encontrada navegando até o **Marketplace > tudo > dev + Test > Jenkins**. A oferta Jenkins mostra um logotipo, título e nome de exibição do Publicador.

![Procurar ofertas no portal do Azure](./media/azureapp-portalbrowse-artifacts-jenkins.png)

A próxima captura de tela mostra informações detalhadas sobre o aplicativo quando um usuário seleciona Jenkins.

![Detalhes da oferta no portal do Azure](./media/azureapp-portal-artifacts-jenkins-details.png)


### <a name="logo-guidelines"></a>Diretrizes para logótipos

Todos os logotipos carregados no Portal do Cloud Partner devem seguir as diretrizes:

- O design do Azure tem uma paleta de cores simples. Mantenha o número de cores primárias e secundárias no seu logotipo baixo.
- As cores do tema do portal do Azure são brancas e pretas. Evite usar essas cores como a cor do plano de fundo dos seus logotipos. Use uma cor que fará seus logotipos proeminentes na portal do Azure. Recomendamos cores primárias simples. Se você estiver usando um plano de fundo transparente, verifique se os logotipos/texto não são branco, preto ou azul.
- Não use um plano de fundo de gradiente no seu logotipo.
- Evite colocar texto, até mesmo o nome da sua empresa ou marca, no logotipo. A aparência do seu logotipo deve ser "simples" e deve evitar gradientes.
- Não estique o logotipo.


#### <a name="hero-logo"></a>Logótipo de destaque

O logotipo do Hero é opcional.

>[!IMPORTANT]
>Não é possível excluir o logotipo Hero depois que ele é carregado.

Use as seguintes diretrizes para um logotipo Hero:

- Planos de fundo pretos, brancos e transparentes não são permitidos.
- Evite usar qualquer cor clara como plano de fundo do logotipo. O nome de exibição do Publicador, o título do plano e o resumo longo da oferta são exibidos em cor de fonte branca e devem se destacar em relação ao plano de fundo.
- Evite usar a maior parte do texto quando estiver projetando o logotipo. O nome do editor, o título do plano, o resumo longo da oferta e um botão criar são inseridos programaticamente dentro do logotipo quando a oferta é listada.
- Inclua um espaço Retangular não utilizado no lado direito do logotipo do Hero. Esse espaço em branco é 415x100 à direita pixels e deslocado da esquerda em 370 pixels.


## <a name="lead-management"></a>Gerenciamento de leads

O formulário gerenciamento de leads tem um campo opcional para configurar o gerenciamento de leads. Para configurar o gerenciamento de Lead, selecione o destino do cliente potencial na lista suspensa. A próxima captura de tela mostra os destinos disponíveis.

![Selecionar destino de gerenciamento de Lead](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Selecione o ícone de informações para ver esta mensagem: "Selecione o sistema no qual seus leads serão armazenados. Saiba como se conectar ao sistema CRM [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) . "

Para obter mais informações, consulte [configurar clientes potenciais do cliente](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).


## <a name="legal"></a>Informações Legais

Use o formulário legal para fornecer a documentação legal necessária para cada oferta.

Forneça as seguintes informações:

- URL da política de privacidade – Insira um link para a política de privacidade do aplicativo. **\***
- **Termosdeuso\***  – Insira os termos de uso para seu aplicativo. Os clientes precisam aceitar esses termos antes de tentarem seu aplicativo.

![Formulário legal](./media/azureapp-marketplace-legal.png)


## <a name="next-steps"></a>Passos Seguintes

[Separador Suporte](./cpp-support-tab.md)
