---
title: Separador de mercado de máquinavirtual no Portal do Parceiro cloud para o Mercado Azure
description: Descreve o separador Marketplace utilizado na criação de uma oferta De VM do Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 2c3d316d0d2810cb2a25ffd3bc4e34cf3454c10d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146863"
---
# <a name="virtual-machine-marketplace-tab"></a>Separador mercado de máquina virtual

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover a gestão das suas ofertas de Máquina Virtual Azure para partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [Create a Azure Virtual Machine para](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) gerir as suas ofertas migratórias.

O separador **Marketplace** da página **New Offer** permite-lhe fornecer aos seus potenciais clientes informações e acordos de marketing, vendas e informações legais e gerir pistas geradas a partir do mercado. Esta longa forma é dividida em quatro secções: **Visão Geral,** **Artefactos de Marketing,** **Gestão de Chumbo,** e **Legal.**


## <a name="overview-section"></a>Secção de descrição geral
Nesta secção, insira as informações gerais sobre a sua Oferta de Mercado Azure.  Um asterisco anexado (*) no nome do campo indica que é necessário.

![Secção de visão geral do separador Marketplace para máquinas virtuais](./media/publishvm_008.png)

A tabela seguinte descreve o propósito e o conteúdo destes campos. Os campos necessários são indiciados por um asterisco (*).

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| **Título\***                 | Título da oferta, muitas vezes o nome longo e formal. Este título será exibido de forma proeminente no mercado.  Comprimento máximo de 50 caracteres. |
| **Resumo\***               | Breve finalidade ou função de solução.  Comprimento máximo de 100 caracteres. |
| **Resumo longo\***          | Finalidade ou função de solução.  Comprimento máximo de 256 caracteres. |
| **Descrição\***           | Descrição da solução.  O comprimento máximo de 3000 caracteres, suporta formatação HTML simples. |
| **Canal revendedor Microsoft CSP\*** | O canal parceiro Cloud Solution Providers (CSP) já está disponível.  Consulte os [Fornecedores de Soluções cloud](../../cloud-solution-providers.md) para obter mais informações sobre o marketing da sua oferta através dos canais parceiros do Microsoft CSP. |
| **Identificador de Marketing\***  | Um URL único para associar a esta oferta, tipicamente inclui a sua organização e nome de solução, comprimento máximo 50 caracteres.  Por exemplo: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **IDs de subscrição de pré-visualização\*** | Adicione um a 100 identificadores de subscrição de pré-telespectadores. Estas subscrições listadas em branco terão acesso à oferta uma vez publicada, antes de entrar em direto. |
| **Links úteis**          | Adicione URLs às documentações, notas de lançamento, PERGUNTAS frequentes, e assim por diante. |
| **Categorias Sugeridas\*** | Selecione até duas (2) categorias, incluindo uma categoria primária e secundária (opcional). Selecione até duas (2) subcategorias para cada categoria primária e/ou secundária. Se nenhuma subcategoria for selecionada, a sua oferta ainda será detetável apenas na categoria selecionada. |
|  |  |


## <a name="marketing-artifacts-section"></a>Secção de Artefactos de Marketing

Esta segunda secção é dividida em três subsecções: **Logos,** **Screenshot**e **Vídeos**. Os logótipos são os únicos artefactos de marketing necessários, no entanto todos são altamente recomendados para o melhor apelo do cliente. 

![Secção de Artefactos de Marketing do separador Marketplace no formulário New Offer para máquinas virtuais](./media/publishvm_009.png)

A tabela seguinte descreve o propósito e o conteúdo destes campos. Os campos necessários são indiciados por um asterisco (*).

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| *Logotipos*  |  |
| **Pequeno\***                 | 40x40 pixel .ico bitmap                                                      |
| **Medium\***                | 90x90 pixel .ico bitmap                                                      |
| **Grande\***                 | 115x115 pixel .ico bitmap                                                   |
| **Largo\***                  | 255x115 pixel .ico bitmap                                                    |
| **Herói**                  | Bitmap 815x290.  Opcional, no entanto, uma vez carregado o ícone herói não pode ser eliminado. |
| *Capturas de Ecrã*  | Opcional, mas máximo de cinco imagens por SKU. |
| **Nome**                  | Nome ou título <!-- TODO - max char length? none specified in UI -->                               |
| **Imagem**                 | Imagem de captura de ecrã, 533x324 pixel                                         |
| *Vídeos*  |  |
| **Nome**                  | Nome ou título  <!-- TODO - max char length? -->                              |
| **Ligação**                  | URL de vídeo, hospedado no YouTube ou Vimeo                                        |
| **Miniatura**             | Bitmap 533x324                                                               |
|   |   |

### <a name="logo-guidelines"></a>Diretrizes para logótipos

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Todos os logótipos enviados para o Portal do Parceiro cloud devem seguir as orientações:

*  O design do Azure tem uma paleta de cores simples. Mantenha o número de cores primárias e secundárias no seu logotipo baixo.
*  As cores temáticas do portal Azure são brancas e pretas. Portanto, evite usar estas cores como cor de fundo dos seus logotipos. Use alguma cor que tornaria os seus logótipos em destaque no portal Azure. Recomendamos cores primárias simples. Se estiver a utilizar fundos transparentes, certifique-se de que os logótipos/texto não são brancos ou pretos ou azuis.
*  Não utilize um fundo de gradiente no seu logotipo.
*  Evite colocar texto — mesmo a sua empresa ou nome de marca - no logotipo. A aparência e a sensação do seu logotipo devem ser "planas" e devem evitar gradientes.
*  Não estique o logótipo.

#### <a name="hero-logo"></a>Logótipo de destaque

O logótipo herói é opcional; no entanto, uma vez carregado, o ícone do herói não pode ser eliminado.  O ícone do logotipo do Herói deve seguir as orientações:

*  Origens pretas, brancas e transparentes não são permitidas para ícones de herói.
*  Evite utilizar qualquer cor clara como pano de fundo do ícone do herói.  O nome de exibição da Editora, o título do plano e o resumo da oferta são apresentados em cor de fonte branca, e devem destacar-se em segundo plano.
*  Evite utilizar a maioria do texto enquanto está a desenhar o logótipo do herói.  O nome da editora, o título do plano, o resumo da oferta e um botão de criação estão incorporados programáticamente dentro do ícone do herói quando a oferta lista. 
* Inclua um retângulo não utilizado no lado direito do seu ícone herói, de tamanho 415x100 pixel e compense 370 px do lado esquerdo.  

Como exemplo, o seguinte ícone herói é para o Serviço de Contentores Azure.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Ícone herói exemplo para serviço de contentores Azure](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Exemplo de informação de marketing 

A imagem que se segue demonstra como as informações de marketing são apresentadas na página principal do produto do Microsoft Windows Server.

![Página de produto de exemplo para Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Secção de Gestão de Chumbo

A terceira secção permite-lhe recolher os leads gerados pelas suas ofertas do Azure Marketplace. Oferece as seguintes opções de armazenamento (a partir de uma lista de dropdown) para esta informação de chumbo.

* **Nenhuma** - a informação por defeito, chumbo não é recolhida.
* Tabela Azure - escrita na tabela Azure especificada por uma corda de ligação.
* Dynamics CRM Online - escrito para a instância [Online Microsoft Dynamics 365,](https://dynamics.microsoft.com/) especificado por um URL e credenciais de autenticação.
* HTTPS Endpoint - escrito para o ponto final HTTPS especificado como uma carga útil JSON.
* Marketo - escrito para a instância de [Marketo](https://www.marketo.com/) especificada, especificado por ID do servidor, identificação de munchkin e id de formulário.
* Salesforce - escrito para uma base de dados [salesforce,](https://www.salesforce.com/) especificado por um identificador de objetos.

Depois de publicar com sucesso a sua oferta, a ligação de chumbo é validada e um fio de teste é automaticamente enviado para o destino configurado. As informações de chumbo devem ser continuamente geridas e estas configurações devem ser prontamente atualizadas sempre que forem feitas alterações na sua arquitetura de gestão de clientes.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Secção legal

Esta última secção permite-lhe fornecer os documentos legais necessários para cada oferta.  

|  **Campo**                    |     **Descrição**                                        |
|  ---------                    |     ---------------                                        |
| **URL de Política de Privacidade\***      | URL para a sua política de privacidade postada                          |
| **Usar o Contrato Padrão?\***  |   |
| **Termos de utilização\***            | política como texto simples ou html simples.                       |
|  |  |


## <a name="next-steps"></a>Passos seguintes

No próximo separador [Suporte,](./cpp-support-tab.md) irá fornecer recursos técnicos e de suporte ao utilizador para a sua oferta.
