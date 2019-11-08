---
title: Descrição do Marketplace para um módulo Azure IoT Edge | Azure Marketplace
description: Crie a descrição do Marketplace para um módulo IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 57bae42eb44c174657e59fa8b017da2931ae681b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814592"
---
# <a name="iot-edge-module-marketplace-tab"></a>Guia Marketplace do módulo IoT Edge

A guia **Marketplace** da página **nova oferta** permite que você forneça a seus clientes potenciais com marketing, vendas e informações legais e contratos e gerencie clientes potenciais gerados no Marketplace. Essa forma longa é dividida em quatro seções: **visão geral**, **artefatos de marketing**, **Gerenciamento de leads**e **ofício**.


## <a name="overview"></a>Descrição geral

Nesta seção, você insere as informações gerais sobre sua oferta do Azure Marketplace.  Um asterisco (*) acrescentado ao nome do campo indica que ele é necessário.

![Seção de visão geral da guia Marketplace no formulário nova oferta para módulos IoT Edge](./media/iot-edge-module-marketplace-tab-overview.png)

A tabela a seguir descreve a finalidade e o conteúdo desses campos. Os campos obrigatórios são indicted por um asterisco (*).

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| **Título\***                 | Título da oferta. Ele será exibido em destaque no Marketplace.  Comprimento máximo de 50 caracteres. <!--ADD PICTURE IN ACTION-->|
| **\* de resumo**               | Breve resumo da oferta. Comprimento máximo de 100 caracteres. <!--ADD PICTURE IN ACTION-->|
| **Resumo longo\***          | Resumo mais longo da oferta (embora possa ser o mesmo que o **Resumo**).  Comprimento máximo de 256 caracteres. <!--ADD PICTURE IN ACTION-->|
| **Descrição\***           | Descrição da oferta.  O comprimento máximo de 3000 caracteres oferece suporte à formatação HTML simples.<br/> Ele deve incluir um parágrafo de *requisitos mínimos de hardware* na parte inferior. por exemplo: <br/> <p><u>Requisitos mínimos de hardware:</u> Linux x64 e arm32 OS, 1 GB de RAM, 500 MB de armazenamento</p>
| **Identificador de marketing\***  | Uma URL exclusiva para associar a esta oferta, normalmente inclui o nome da sua organização e da solução, o comprimento máximo de 50 caracteres.  Por exemplo: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **Visualizar IDs de assinatura** | Adicione um a 100 identificadores de assinatura de visualizações. Essas assinaturas listadas em branco terão acesso à oferta depois de publicadas, antes de entrar em tempo real. |
| **Links úteis**          | Várias seleções de categorias técnicas e de negócios que oferecem o podem ser mais bem associadas ao.  Um máximo de 10 permitidos. Certifique-se de adicionar pelo menos um link à sua documentação e um link para os dispositivos de IoT Edge compatíveis do [Catálogo de dispositivos do Azure IOT](https://catalog.azureiotsolutions.com/). |
| **Categorias sugeridas\*** | Escolha até cinco categorias. Eles serão mostrados na página de detalhes do produto. Nas páginas de procura, todos os módulos de IoT Edge são mostrados na categoria *Internet das Coisas \> IOT Edge do módulo* .|
|  |  |


### <a name="offer-example"></a>Exemplo de oferta

 Os exemplos a seguir mostram como os campos **título**da oferta, **Resumo**, **Descrição**, **logotipos**e **capturas de tela** aparecem em modos de exibição diferentes.

 
#### <a name="on-the-azure-marketplace-website"></a>No site do Azure Marketplace:

- Quando a navegação oferece:

    ![Como as ofertas do Marketplace são exibidas no site do Azure Marketplace – procurar](./media/iot-edge-module-ampdotcom-card.png)

- Ao examinar os detalhes da oferta:

    ![Como IoT Edge módulo aparece ao procurar os detalhes do produto no site](./media/iot-edge-module-ampdotcom-pdp.png)


#### <a name="on-the-azure-portal-website"></a>No site portal do Azure:

- Quando a navegação oferece:

    ![Como IoT Edge módulo aparece ao navegar na portal do Azure #1](./media/iot-edge-module-portal-browse.png)

    ![Como IoT Edge módulo aparece ao navegar na portal do Azure #2](./media/iot-edge-module-portal-product-picker.png)

- Ao procurar uma oferta:

    ![Como IoT Edge módulo aparece ao pesquisar o portal do Azure](./media/iot-edge-module-portal-search.png)

- Ao examinar os detalhes da oferta:

    ![Como IoT Edge módulo aparece ao procurar os detalhes do produto no portal](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>Artefatos de marketing

Esta seção tem as seguintes subseções: **logotipos**, **capturas de tela**e **vídeos**. 

>[!Note]
>Os logotipos são os únicos artefatos de marketing necessários; no entanto, todos são altamente recomendados para melhor apelo ao cliente.

![Seção artefatos de marketing da guia Marketplace no formulário nova oferta para máquinas virtuais](./media/publishvm_009.png)

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| *Logotipos*  | Consulte a tela anterior captura para ver como e onde seus logotipos serão usados.  |
| **\* pequeno**                 | formato PNG de pixel 40x40                                                     |
| **\* médio**                | formato PNG de pixel 90x90                                                     |
| **\* grandes**                 | formato PNG de pixel 115x115                                                  |
| **\* largo**                  | formato PNG de pixel 255x115                                                   |
| **'**                  | formato PNG de pixel 815x290.  Opcional, no entanto, uma vez carregado, o ícone Hero não pode ser excluído. |
| *Capturas*  | Capturas de tela são exibidas na página de detalhes do produto. Eles são uma boa maneira de comunicar visualmente o que seu módulo IoT Edge faz e como ele funciona. Você pode mostrar diagramas de arquitetura ou ilustrações de caso de uso por instância. Opcional, mas no máximo cinco capturas de tela por SKU. |
| **Nome**                  | Nome ou título. Comprimento máximo de 100 caracteres.                             |
| **Imagem**                 | Imagem de captura de tela, formato PNG de pixel 533x324                               |
| *Vídeos*  | Vídeos são exibidos na página de detalhes do produto. Eles são uma boa maneira de comunicar visualmente o que seu módulo IoT Edge faz e como ele funciona. |
| **Nome**                  | Nome ou título. Comprimento máximo de 100 caracteres.                             |
| **Ligação**                  | URL de vídeo, hospedada no YouTube ou Vimeo                                        |
| **Miniatura**             | formato PNG de pixel 533x324                                                     |
|  |  |


### <a name="logo-guidelines"></a>Diretrizes para logótipos

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Todos os logotipos carregados no Portal do Cloud Partner devem seguir as diretrizes:

*  O design do Azure tem uma paleta de cores simples. Mantenha o número de cores primárias e secundárias no seu logotipo baixo.
*  As cores do tema do portal do Azure são brancas e pretas. Evite usar essas cores como a cor do plano de fundo dos seus logotipos. Use uma cor que fará seus logotipos proeminentes na portal do Azure. Recomendamos cores primárias simples. Se você estiver usando um plano de fundo transparente, verifique se os logotipos/texto não são branco, preto ou azul.
*  Não use um plano de fundo de gradiente no seu logotipo.
*  Evite colocar texto, até mesmo o nome da sua empresa ou marca, no logotipo. A aparência do seu logotipo deve ser "simples" e deve evitar gradientes.
*  Não estique o logotipo.


#### <a name="hero-logo"></a>Logótipo de destaque

O logotipo do Hero é opcional.

>[!Important]
>Depois que o logotipo do Hero for carregado, ele não poderá ser excluído.

Use as seguintes diretrizes para um logotipo Hero: 

*  Planos de fundo pretos, brancos e transparentes não são permitidos.
*  Evite usar qualquer cor clara como plano de fundo do logotipo.  O nome de exibição do Publicador, o título do plano e o resumo longo da oferta são exibidos em cor da fonte branca e devem se destacar em relação ao plano de fundo.
*  Evite usar a maior parte do texto quando estiver projetando o logotipo. O nome do editor, o título do plano, o resumo longo da oferta e um botão criar são inseridos programaticamente dentro do logotipo quando a oferta é listada. 
* Inclua um espaço Retangular não utilizado no lado direito do logotipo do Hero. Esse espaço em branco é 415x100 à direita pixels e deslocado da esquerda em 370 pixels.  

<!-- P2: woudl be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>Gestão de oportunidades potenciais

Esta seção permite que você configure as opções para coletar clientes potenciais gerados de suas ofertas do Azure Marketplace. Você pode selecionar as seguintes opções de armazenamento em uma lista suspensa.

* **Nenhum** -o padrão, as informações de Lead não são coletadas.
* Tabela do Azure-gravada na tabela do Azure especificada por uma cadeia de conexão.
* Dynamics CRM Online – gravado na instância [online do Microsoft Dynamics 365](https://dynamics.microsoft.com/) , especificada por uma URL e credenciais de autenticação.
* Ponto de extremidade HTTPS-gravado no ponto de extremidade HTTPS especificado como uma carga JSON.
* Marketo-gravado na instância especificada do [marketo](https://www.marketo.com/) , especificada pela ID do servidor, ID do MUNCHKIN e ID do formulário.
* Salesforce-gravado em um banco de dados do [Salesforce](https://www.salesforce.com/) , especificado por um identificador de objeto.

Depois de publicar com êxito sua oferta, a conexão de Lead é verificada e um cliente potencial de teste é enviado automaticamente para o destino que você configurou. 

>[!Note]
>As informações de Lead devem ser gerenciadas continuamente e essas configurações devem ser atualizadas imediatamente sempre que forem feitas alterações na sua arquitetura de gerenciamento de clientes.

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->


## <a name="legal"></a>Informações Legais

Esta seção permite que você forneça os dois documentos legais necessários para cada oferta: política de privacidade e os termos de uso.

|  **Campo**                    |     **Descrição**                                                          |
|  ---------                    |     ---------------                                                          |
| **URL da política de privacidade\***      | URL para a política de privacidade postada                                            |
| **Usar\* Constract padrão**  | Se o modelo padrão de contrato da Microsoft deve ser usado.  Para obter mais informações, consulte [contrato Standard](https://docs.microsoft.com/azure/marketplace/standard-contract).   |
| **Termos de utilização\***            | *Termos de uso* como HTML simples embutido ou link para a página de termos de uso postados     |
|  |  |


## <a name="next-steps"></a>Passos seguintes

Use a guia [suporte](./cpp-support-tab.md) para fornecer os recursos técnicos e de suporte ao usuário para sua oferta.
