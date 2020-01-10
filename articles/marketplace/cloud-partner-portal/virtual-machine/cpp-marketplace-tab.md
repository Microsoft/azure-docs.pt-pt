---
title: Guia Marketplace de máquina virtual no Portal do Cloud Partner para o Azure Marketplace
description: Descreve a guia Marketplace usada na criação de uma oferta de VM do Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 283274986c753fc8ad05b9b7b0dd87aea956bcce
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75762897"
---
# <a name="virtual-machine-marketplace-tab"></a>Guia Marketplace de máquina virtual

A guia **Marketplace** da página **nova oferta** permite que você forneça a seus clientes potenciais com marketing, vendas e informações legais e contratos e gerencie clientes potenciais gerados no Marketplace. Essa forma longa é dividida em quatro seções: **visão geral**, **artefatos de marketing**, **Gerenciamento de leads**e **ofício**.


## <a name="overview-section"></a>Secção de descrição geral
Nesta seção, você insere as informações gerais sobre sua oferta do Azure Marketplace.  Um asterisco anexado (*) no nome do campo indica que ele é necessário.

![Seção de visão geral da guia Marketplace para máquinas virtuais](./media/publishvm_008.png)

A tabela a seguir descreve a finalidade e o conteúdo desses campos. Os campos obrigatórios são indicted por um asterisco (*).

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| **Título\***                 | Título da oferta, geralmente o nome longo e formal. Este título será exibido em destaque no Marketplace.  Comprimento máximo de 50 caracteres. |
| **Resumo\***               | Curto objetivo ou função da solução.  Comprimento máximo de 100 caracteres. |
| **Resumo longo\***          | Finalidade ou função da solução.  Comprimento máximo de 256 caracteres. |
| **Descrição\***           | Descrição da solução.  O comprimento máximo de 3000 caracteres oferece suporte à formatação HTML simples. |
| **\* de canal do revendedor CSP da Microsoft** | A aceitação do canal de parceiros do CSP (provedores de soluções na nuvem) já está disponível.  Consulte os [provedores de soluções de nuvem](../../cloud-solution-providers.md) para obter mais informações sobre como comercializar sua oferta por meio dos canais de parceiros do Microsoft CSP. |
| **Identificador de marketing\***  | Uma URL exclusiva para associar a esta oferta, normalmente inclui o nome da sua organização e da solução, o comprimento máximo de 50 caracteres.  Por exemplo: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Visualizar IDs de assinatura\*** | Adicione um a 100 identificadores de assinatura de visualizações. Essas assinaturas listadas em branco terão acesso à oferta depois de publicadas, antes de entrar em tempo real. |
| **Links úteis**          | Adicione URLs às documentações, notas de versão, perguntas frequentes e assim por diante. |
| **Categorias sugeridas\*** | Selecione até duas (2) categorias, incluindo uma categoria primária e uma secundária (opcional). Selecione até duas (2) subcategorias para cada categoria primária e/ou secundária. Se nenhuma subcategoria for selecionada, sua oferta ainda poderá ser descoberta somente na categoria selecionada. |
|  |  |


## <a name="marketing-artifacts-section"></a>Seção artefatos de marketing

Esta segunda seção é dividida em três subseções: **logotipos**, **capturas de tela**e **vídeos**. Os logotipos são os únicos artefatos de marketing necessários; no entanto, todos são altamente recomendados para melhor apelo ao cliente. 

![Seção artefatos de marketing da guia Marketplace no formulário nova oferta para máquinas virtuais](./media/publishvm_009.png)

A tabela a seguir descreve a finalidade e o conteúdo desses campos. Os campos obrigatórios são indicted por um asterisco (*).

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| *Logotipos*  |  |
| **\* pequeno**                 | bitmap 40x40 pixel. ico                                                      |
| **\* médio**                | bitmap 90x90 pixel. ico                                                      |
| **\* grandes**                 | bitmap 115x115 pixel. ico                                                   |
| **\* largo**                  | bitmap 255x115 pixel. ico                                                    |
| **'**                  | bitmap 815x290.  Opcional, no entanto, uma vez carregado, o ícone Hero não pode ser excluído. |
| *Capturas*  | Opcional, mas no máximo cinco capturas de tela por SKU. |
| **Nome**                  | Nome ou título <!-- TODO - max char length? none specified in UI -->                               |
| **Imagem**                 | Imagem de captura de tela, 533x324 pixel                                         |
| *Vídeos*  |  |
| **Nome**                  | Nome ou título  <!-- TODO - max char length? -->                              |
| **Ligação**                  | URL de vídeo, hospedada no YouTube ou Vimeo                                        |
| **Miniatura**             | bitmap 533x324                                                               |
|   |   |

### <a name="logo-guidelines"></a>Diretrizes para logótipos

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Todos os logotipos carregados no Portal do Cloud Partner devem seguir as diretrizes:

*  O design do Azure tem uma paleta de cores simples. Mantenha o número de cores primárias e secundárias no seu logotipo baixo.
*  As cores do tema dos portal do Azure são brancas e pretas. Portanto, evite usar essas cores como a cor do plano de fundo dos seus logotipos. Use alguma cor que torne seus logotipos proeminentes na portal do Azure. Recomendamos cores primárias simples. Se você estiver usando um plano de fundo transparente, verifique se os logotipos/texto não são brancos ou pretos ou azuis.
*  Não use um plano de fundo de gradiente no seu logotipo.
*  Evite colocar texto, até mesmo o nome da sua empresa ou marca, no logotipo. A aparência do seu logotipo deve ser "simples" e deve evitar gradientes.
*  Não estique o logotipo.

#### <a name="hero-logo"></a>Logótipo de destaque

O logotipo do Hero é opcional; no entanto, uma vez carregado, o ícone Hero não pode ser excluído.  O ícone de logotipo Hero deve seguir as diretrizes:

*  Planos de fundo pretos, brancos e transparentes não são permitidos para ícones Hero.
*  Evite usar qualquer cor clara como o plano de fundo do ícone Hero.  O nome de exibição do Publicador, o título do plano e o resumo longo da oferta são exibidos em cor da fonte branca e devem se destacar em relação ao plano de fundo.
*  Evite usar a maior parte do texto enquanto estiver projetando o logotipo Hero.  O nome do editor, o título do plano, o resumo longo da oferta e um botão criar são inseridos programaticamente dentro do ícone Hero quando a oferta lista. 
* Inclua um retângulo não usado no lado direito do ícone do Hero, de tamanho 415x100 à direita pixel e offset 370 PX da esquerda.  

Por exemplo, o seguinte ícone de Hero é para o serviço de contêiner do Azure.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Exemplo de ícone de Hero para o serviço de contêiner do Azure](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Exemplo de informações de marketing 

A imagem a seguir demonstra como as informações de marketing são exibidas na página principal do produto Microsoft Windows Server.

![Exemplo de página de produtos para o Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Seção Gerenciamento de leads

A terceira seção permite que você colete clientes potenciais gerados de suas ofertas do Azure Marketplace. Ele oferece as seguintes opções de armazenamento (de uma lista suspensa) para essas informações de Lead.

* **Nenhum** -o padrão, as informações de Lead não são coletadas.
* Tabela do Azure-gravada na tabela do Azure especificada por uma cadeia de conexão.
* Dynamics CRM Online – gravado na instância [online do Microsoft Dynamics 365](https://dynamics.microsoft.com/) , especificada por uma URL e credenciais de autenticação.
* Ponto de extremidade HTTPS-gravado no ponto de extremidade HTTPS especificado como uma carga JSON.
* Marketo-gravado na instância especificada do [marketo](https://www.marketo.com/) , especificada pela ID do servidor, ID do MUNCHKIN e ID do formulário.
* Salesforce-gravado em um banco de dados do [Salesforce](https://www.salesforce.com/) , especificado por um identificador de objeto.

Depois de publicar com êxito sua oferta, a conexão de cliente potencial é validada e um cliente potencial de teste é enviado automaticamente para o destino configurado. As informações de Lead devem ser gerenciadas continuamente e essas configurações devem ser atualizadas imediatamente sempre que forem feitas alterações na sua arquitetura de gerenciamento de clientes.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Seção legal

Esta última seção permite que você forneça os documentos legais necessários necessários para cada oferta.  

|  **Campo**                    |     **Descrição**                                        |
|  ---------                    |     ---------------                                        |
| **URL da política de privacidade\***      | URL para a política de privacidade postada                          |
| **Usar contrato padrão?\***  |   |
| **Termos de utilização\***            | política como texto sem formatação ou HTML simples.                       |
|  |  |


## <a name="next-steps"></a>Passos seguintes

Na próxima guia [suporte](./cpp-support-tab.md) , você fornecerá recursos técnicos e de suporte ao usuário para sua oferta.
