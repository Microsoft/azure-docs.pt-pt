---
title: Descrição do mercado para um módulo Azure IoT Edge  Mercado Azure
description: Crie a descrição do mercado para um módulo IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 8021b6fe40e006a95b3eeff1031606c38cc47b87
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721630"
---
# <a name="iot-edge-module-marketplace-tab"></a>Separador marketplace do módulo IoT Edge

O separador **Marketplace** da página **New Offer** permite-lhe fornecer aos seus potenciais clientes informações e acordos de marketing, vendas e informações legais e gerir pistas geradas a partir do mercado. Esta longa forma é dividida em quatro secções: **Visão Geral,** **Artefactos de Marketing,** **Gestão de Chumbo,** e **Legal.**


## <a name="overview"></a>Descrição geral

Nesta seção, você insere as informações gerais sobre sua oferta do Azure Marketplace.  Um asterisco (*) anexado ao nome de campo indica que é necessário.

![Secção de visão geral do separador Marketplace no formulário New Offer para módulos IoT Edge](./media/iot-edge-module-marketplace-tab-overview.png)

A tabela a seguir descreve a finalidade e o conteúdo desses campos. Os campos obrigatórios são indicted por um asterisco (*).

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| **Título\***                 | Título da oferta. Será exibido proeminentemente no mercado.  Comprimento máximo de 50 caracteres. <!--ADD PICTURE IN ACTION-->|
| **Resumo\***               | Resumo curto da oferta. Comprimento máximo de 100 caracteres. <!--ADD PICTURE IN ACTION-->|
| **\* de resumo longo**          | Resumo mais longo da oferta (embora possa ser o mesmo que o **resumo).**  Comprimento máximo de 256 caracteres. <!--ADD PICTURE IN ACTION-->|
| **Descrição\***           | Descrição da oferta.  O comprimento máximo de 3000 caracteres oferece suporte à formatação HTML simples.<br/> Deve incluir um parágrafo mínimo de requisitos de *hardware* na parte inferior. Por exemplo: <br/> <p><u>Requisitos mínimos de hardware:</u> Linux x64 e arm32 OS, 1 GB de RAM, 500 Mb de armazenamento</p>
| **Identificação de Marketing\***  | Uma URL exclusiva para associar a esta oferta, normalmente inclui o nome da sua organização e da solução, o comprimento máximo de 50 caracteres.  Por exemplo: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **IDs de subscrição de pré-visualização** | Adicione um a 100 identificadores de assinatura de visualizações. Essas assinaturas listadas em branco terão acesso à oferta depois de publicadas, antes de entrar em tempo real. |
| **Links úteis**          | A multi-selecção de categorias empresariais e técnicas que oferecem pode ser melhor associada.  Um máximo de 10 permitidos. Certifique-se de adicionar pelo menos um link à sua documentação e um link para os dispositivos IoT Edge compatíveis do catálogo do [dispositivo Azure IoT](https://catalog.azureiotsolutions.com/). |
| **Categorias sugeridas\*** | Escolha até cinco categorias. Serão mostrados na sua página de detalhes do produto. Nas páginas de navegação, todos os módulos IoT Edge são mostrados na categoria de módulo sinuoso *\> IoT Edge.*|
|  |  |


### <a name="offer-example"></a>Dar exemplo

 Os exemplos seguintes mostram como os campos **title**, **Resumo,** **Descrição,** **Logotipos**e Screenshots aparecem em **diferentes** vistas.

 
#### <a name="on-the-azure-marketplace-website"></a>No site do Azure Marketplace:

- Ao navegar oferece:

    ![Como as ofertas de marketplace são exibidas no site do Azure Marketplace - navegue](./media/iot-edge-module-ampdotcom-card.png)

- Ao analisar os detalhes da oferta:

    ![Como o módulo IoT Edge aparece ao olhar para os detalhes do produto no site](./media/iot-edge-module-ampdotcom-pdp.png)


#### <a name="on-the-azure-portal-website"></a>No site do portal Azure:

- Ao navegar oferece:

    ![Como o módulo IoT Edge aparece ao navegar no portal Azure #1](./media/iot-edge-module-portal-browse.png)

    ![Como o módulo IoT Edge aparece ao navegar no portal Azure #2](./media/iot-edge-module-portal-product-picker.png)

- Ao procurar uma oferta:

    ![Como o módulo IoT Edge aparece ao pesquisar o portal Azure](./media/iot-edge-module-portal-search.png)

- Ao analisar os detalhes da oferta:

    ![Como o módulo IoT Edge aparece ao olhar para os detalhes do produto no portal](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>Artefatos de marketing

Esta secção tem as seguintes subsecções: **Logos,** **Screenshot**e **Vídeos**. 

>[!Note]
>Os logotipos são os únicos artefatos de marketing necessários; no entanto, todos são altamente recomendados para melhor apelo ao cliente.

![Seção artefatos de marketing da guia Marketplace no formulário nova oferta para máquinas virtuais](./media/publishvm_009.png)

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| *Logotipos*  | Veja as capturas de ecrã anteriores para ver como e onde serão utilizados os seus logótipos.  |
| **Pequenas\***                 | Formato PNG de 40x40 pixel                                                     |
| **\* média**                | Formato PNG de 90x90 pixel                                                     |
| **Grandes\***                 | Formato PNG de 115x115 pixel                                                  |
| **\***                  | Formato PNG de 255x115 pixel                                                   |
| **Herói**                  | Formato PNG de 815x290 pixel.  Opcional, no entanto, uma vez carregado, o ícone Hero não pode ser excluído. |
| *Screenshots*  | As imagens são exibidas na página de detalhes do produto. São uma boa forma de comunicar visualmente o que o seu módulo IoT Edge faz e como funciona. Pode mostrar diagramas de arquitetura ou usar ilustrações de caso, por exemplo. Opcional, mas no máximo cinco capturas de tela por SKU. |
| **Nome**                  | Nome ou título. Comprimento máximo de 100 caracteres.                             |
| **Imagem**                 | Imagem de captura de ecrã, formato DE PIXEL 533x324 pixel                               |
| *Vídeos*  | Os vídeos são exibidos na página de detalhes do produto. São uma boa forma de comunicar visualmente o que o seu módulo IoT Edge faz e como funciona. |
| **Nome**                  | Nome ou título. Comprimento máximo de 100 caracteres.                             |
| **Ligação**                  | URL de vídeo, hospedada no YouTube ou Vimeo                                        |
| **Miniatura**             | Formato 533x324 pixel PNG                                                     |
|  |  |


### <a name="logo-guidelines"></a>Diretrizes para logótipos

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Todos os logotipos carregados no Portal do Cloud Partner devem seguir as diretrizes:

*  O design do Azure tem uma paleta de cores simples. Mantenha o número de cores primárias e secundárias no seu logotipo baixo.
*  As cores temáticas do Portal Azure são brancas e pretas. Evite utilizar estas cores como cor de fundo para os seus logotipos. Use uma cor que tornará os seus logótipos proeminentes no portal Azure. Recomendamos cores primárias simples. Se estiver a usar um fundo transparente, certifique-se de que os logótipos/texto não são brancos, pretos ou azuis.
*  Não use um fundo de gradiente no seu logotipo.
*  Evite colocar texto, até mesmo o nome da sua empresa ou marca, no logotipo. A aparência do seu logotipo deve ser "simples" e deve evitar gradientes.
*  Não estique o logotipo.


#### <a name="hero-logo"></a>Logótipo de destaque

O logotipo do Herói é opcional.

>[!Important]
>Depois do logotipo do Herói ser carregado, não pode ser apagado.

Utilize as seguintes diretrizes para um logotipo herói: 

*  Fundos pretos, brancos e transparentes não são permitidos.
*  Evite utilizar qualquer cor clara como pano de fundo para o logotipo.  O nome da exibição da editora, o título do plano e o resumo da oferta são apresentados em cor de fonte branca, e devem destacar-se em segundo plano.
*  Evite utilizar a maioria do texto quando estiver a desenhar o logótipo. O nome da editora, o título do plano, o resumo da oferta e um botão de criação estão incorporados programáticamente dentro do logótipo quando a oferta está listada. 
* Inclua um espaço retangular não utilizado no lado direito do seu logotipo herói. Este espaço em branco é de 415x100 pixels, e compensado pela esquerda por 370 pixels.  

<!-- P2: would be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>Gestão de oportunidades potenciais

Esta secção permite-lhe configurar as opções de recolha de leads de clientes gerados a partir das suas ofertas do Azure Marketplace. Pode selecionar as seguintes opções de armazenamento a partir de uma lista de dropdown.

* **Nenhuma** - a informação por defeito, chumbo não é recolhida.
* Tabela do Azure-gravada na tabela do Azure especificada por uma cadeia de conexão.
* Dynamics CRM Online - escrito para a instância [Online Microsoft Dynamics 365,](https://dynamics.microsoft.com/) especificado por um URL e credenciais de autenticação.
* Ponto de extremidade HTTPS-gravado no ponto de extremidade HTTPS especificado como uma carga JSON.
* Marketo - escrito para a instância de [Marketo](https://www.marketo.com/) especificada, especificado por ID do servidor, identificação de munchkin e id de formulário.
* Salesforce - escrito para uma base de dados [salesforce,](https://www.salesforce.com/) especificado por um identificador de objetos.

Depois de publicar com sucesso a sua oferta, a ligação de chumbo é verificada e um fio de teste é automaticamente enviado para o destino que configura. 

>[!Note]
>As informações de Lead devem ser gerenciadas continuamente e essas configurações devem ser atualizadas imediatamente sempre que forem feitas alterações na sua arquitetura de gerenciamento de clientes.

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->


## <a name="legal"></a>Legal

Esta secção permite-lhe fornecer os dois documentos legais que são necessários para cada oferta: Política de Privacidade e os Termos de Utilização.

|  **Campo**                    |     **Descrição**                                                          |
|  ---------                    |     ---------------                                                          |
| **URL de política de privacidade\***      | URL para a política de privacidade postada                                            |
| **Use\* de Constract Padrão**  | Se usar o modelo padrão de contrato da Microsoft.  Para mais informações, consulte [O Contrato Padrão](https://docs.microsoft.com/azure/marketplace/standard-contract).   |
| **Termos de utilização\***            | *Termos de utilização* como HTML simples inline ou link para os termos de utilização publicados     |
|  |  |


## <a name="next-steps"></a>Passos Seguintes

Utilize o separador [Suporte](./cpp-support-tab.md) para fornecer os recursos técnicos e de suporte ao utilizador para a sua oferta.
