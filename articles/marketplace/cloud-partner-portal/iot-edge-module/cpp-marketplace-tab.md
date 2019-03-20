---
title: Descrição de mercado para um módulo do Azure IoT Edge | Documentos da Microsoft
description: Crie a descrição do marketplace para o módulo do IoT Edge.
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 30f44e0e8bae4071403b5c28cd4133970fc4d468
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58010945"
---
# <a name="iot-edge-module-marketplace-tab"></a>Separador de Marketplace de módulo do IoT Edge


O **Marketplace** separador da **nova oferta** página permite-lhe fornecer os seus clientes potenciais informações de marketing, vendas e legais e contratos e gerir as oportunidades potenciais geradas a partir do Marketplace. Esta forma longa é dividida em quatro seções: **Descrição geral**, **artefactos de Marketing**, **numa gestão**, e **legais**.

## <a name="overview"></a>Descrição geral

Nesta secção, é possível inserir as informações gerais sobre a sua oferta do Azure Marketplace.  Um asterisco (*) acrescentado ao nome do campo indica que é obrigatório.

![Secção de descrição geral da guia Marketplace do formulário de nova oferta para os módulos do IoT Edge](./media/iot-edge-module-marketplace-tab-overview.png)

A tabela seguinte descreve a finalidade e o conteúdo desses campos.

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| **Title** (Título)                 | Título da oferta. Será apresentado forma destacada no marketplace.  Comprimento máximo de 50 carateres. <!--ADD PICTURE IN ACTION-->|
| **Resumo**               | Breve resumo da oferta. Comprimento máximo de 100 carateres. <!--ADD PICTURE IN ACTION-->|
| **Resumo completo**          | Já resumo da oferta (embora pode ser igual a **resumo**).  Comprimento máximo de 256 carateres. <!--ADD PICTURE IN ACTION-->|
| **Descrição**           | Descrição da oferta.  Comprimento máximo de 3000 carateres, oferece suporte a formatação de HTML simples.<br/> Tem de incluir um *requisitos mínimos de hardware* parágrafo na parte inferior. Por exemplo: <br/> <p><u>Requisitos mínimos de hardware:</u> Linux x64 e OS arm32, 1 GB de RAM, 500 Mb de armazenamento</p>
| **Identificador de marketing**  | Uma URL exclusiva para associar a esta oferta, normalmente inclui a sua organização e o nome da solução, 50 carateres de comprimento máximo.  Por exemplo: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **IDs de subscrição de pré-visualização** | Adicione um e 100 identificadores de subscrição dos visualizadores. Estas subscrições listadas em branco têm acesso para a oferta assim que for publicado, antes de entrar no ar. |
| **Ligações úteis**          | Seleção múltipla de negócios e técnicas categorias que oferecem pode ser melhor associada.  Um máximo de 10 permitido. Certifique-se de adicionar pelo menos uma ligação para a documentação e uma ligação para os dispositivos do IoT Edge compatíveis dos [catálogo de dispositivos do IoT do Azure](https://catalog.azureiotsolutions.com/). |
| **Categorias sugeridas** | Escolha até cinco categorias. Serão apresentados na sua página de detalhes do produto. Nas páginas de procura, todos os módulos do IoT Edge são apresentados sob a *Internet das coisas \> módulo do IoT Edge* categoria.|

### <a name="offer-example"></a>Exemplo de oferta

 Os exemplos seguintes mostram como a oferta **Title**, **resumo**, **Descrição**, **logótipos**, e **capturas de ecrã** campos são apresentados em modos de exibição diferentes.
 
#### <a name="on-the-azure-marketplace-website"></a>No site do Azure Marketplace:

- Ao navegar em ofertas:

    ![Como as ofertas do marketplace são apresentadas no Web site do Azure Marketplace - procurar](./media/iot-edge-module-ampdotcom-card.png)

- Ao examinar os detalhes da oferta:

    ![Como módulo do IoT Edge aparece ao ver os detalhes do produto no Web site](./media/iot-edge-module-ampdotcom-pdp.png)

#### <a name="on-the-azure-portal-website"></a>No site de portal do Azure:

- Ao navegar em ofertas:

    ![Como módulo do IoT Edge exibido quando navegar no portal do Azure #1](./media/iot-edge-module-portal-browse.png)

    ![Como módulo do IoT Edge exibido quando navegar no portal do Azure #2](./media/iot-edge-module-portal-product-picker.png)

- Ao pesquisar uma oferta:

    ![Como módulo do IoT Edge aparece ao pesquisar o portal do Azure](./media/iot-edge-module-portal-search.png)

- Ao examinar os detalhes da oferta:

    ![Como módulo do IoT Edge aparece ao ver os detalhes do produto no portal](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>Artefactos de marketing

Esta secção tem as seguintes subsecções: **Logótipos**, **captura de ecrã**, e **vídeos**. 

>[!Note]
>Logótipos são o único necessário artefactos, de marketing, no entanto, todos são altamente recomendáveis para melhor apelo do cliente.

![Secção de artefactos da guia Marketplace do formulário de nova oferta de mensagens em fila para máquinas virtuais de marketing](./media/publishvm_009.png)

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| *Logótipos*  | Veja a que captura de ecrã anterior para ver como e onde os logótipos serão utilizados.  |
| **Pequenas**                 | formato do pixel de 40 x 40 PNG                                                     |
| **Médio**                | formato do pixel 90 x 90 PNG                                                     |
| **Grandes**                 | formato do pixel 115 x 115 PNG                                                  |
| **Ampla**                  | formato do pixel de 255 x 115 PNG                                                   |
| **Hero**                  | formato do pixel 815 x 290 PNG.  Opcional, no entanto uma vez carregado não é possível eliminar o ícone de hero. |
| *Capturas de ecrã*  | Capturas de ecrã são apresentadas na sua página de detalhes do produto. Eles são uma boa forma de comunicar visualmente o que faz o módulo do IoT Edge e como ela funciona. Pode mostrar diagramas de arquitetura ou utilizar as maiúsculas ilustrações por exemplo. É opcional, mas máximo de cinco capturas de ecrã por SKU. |
| **Nome**                  | Nome ou título. Comprimento máximo de 100 carateres.                             |
| **Imagem**                 | Imagem de captura de ecrã, formato do pixel 533 x 324 PNG                                         |
| *Vídeos*  | Vídeos são apresentados na sua página de detalhes do produto. Eles são uma boa forma de comunicar visualmente o que faz o módulo do IoT Edge e como ela funciona. |
| **Nome**                  | Nome ou título. Comprimento máximo de 100 carateres.                              |
| **Link**                  | URL de vídeo, alojado no YouTube ou Vimeo                                        |
| **Miniatura**             | formato do pixel 533 x 324 PNG                                                           |


### <a name="logo-guidelines"></a>Diretrizes para logótipos

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Todos os logótipos carregados para o Portal de parceiro de Cloud devem seguir as diretrizes:

*  O design do Azure tem uma paleta de cores simples. Mantenha o número de principal e secundário cores seu logótipo baixa.
*  As cores de tema do Portal do Azure são brancas e preto. Evite utilizar essas cores como a cor de fundo para os logotipos. Utilize uma cor que tornarão seu logótipos proeminente no portal do Azure. Recomendamos cores primárias simples. Se estiver a utilizar um plano de fundo transparente, certifique-se de que o texto/logótipos não são branco, preto ou azul.
*  Não utilize um plano de fundo de gradação no seu logótipo.
*  Evite colocar texto — até mesmo a sua empresa ou o nome da marca — no logótipo. O aspeto e funcionalidade do seu logótipo deve ser "simples" e deve evitar gradientes.
*  Não se estendem o logótipo.

#### <a name="hero-logo"></a>Logótipo de destaque

O logótipo de Hero é opcional.

>[!Important]
>Depois do logótipo de Hero é carregado, não pode ser eliminada.

Utilize as seguintes diretrizes para um logótipo de Hero: 

*  Planos de fundo transparentes, pretas e brancas não são permitidos.
*  Evite usar qualquer cor leve como plano de fundo para o logótipo.  O Editor nome a apresentar, plano de título e a oferta longa resumo são apresentados na cor da fonte branco e tem se destacam em segundo plano.
*  Evite utilizar a maioria dos textos quando estiver a conceber o logótipo. O nome do publicador, título do plano, oferta longa resumo e um botão de criar são incorporadas por meio de programação dentro o logótipo quando listados da oferta. 
* Inclua um espaço retangular não utilizado no lado direito do seu logótipo hero. Este espaço em branco é 415 x 100 pixéis e deslocamento a partir do esquerda por 370 pixels.  

<!-- P2: woudl be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>Gestão de oportunidades potenciais

Esta permite que a secção Configurar as opções para a recolha de cliente leva gerado a partir de suas ofertas do Azure Marketplace. Pode selecionar as seguintes opções de armazenamento numa lista suspensa.

* **Nenhum** -padrão, informações de oportunidades potenciais não é coletada.
* Tabela do Azure - escritos para a tabela do Azure especificada por uma cadeia de ligação.
* Dynamics CRM Online - escrito para o [do Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) instância, especificada pelas credenciais de uma URL e a autenticação.
* Ponto final de HTTPS - escrito para o ponto final HTTPS especificado como um payload JSON.
* Marketo - escrito especificado [Marketo](https://www.marketo.com/) instância, especificada pelo ID do servidor, o ID de munchkin e o ID do formulário.
* Salesforce – escrito para um [Salesforce](https://www.salesforce.com/) base de dados, especificada por um identificador de objeto.

Depois de publicar a oferta com êxito, a ligação de oportunidade potencial é verificada e um líder de teste é enviado automaticamente para o destino que configurou. 

>[!Note]
>Informações de provocar deverão ser geridas continuamente e estas definições devem ser imediatamente atualizadas sempre que forem feitas alterações em sua arquitetura de gestão do cliente.

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->

## <a name="legal"></a>Informações Legais

Esta secção permite-lhe fornecer os dois documentos legais que são necessários para cada oferta: Política de privacidade e termos de utilização.

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| **URL de política de privacidade**    | URL para a política de privacidade postado                                            |
| **Termos de utilização**          | Termos de utilização como inline simples HTML ou uma ligação para as suas postados termos de utilização       |

<br/>

## <a name="next-steps"></a>Passos Seguintes

Utilize o [suportar](./cpp-support-tab.md) separador para fornecer os técnicos e o utilizador recursos de suporte para a sua oferta.
