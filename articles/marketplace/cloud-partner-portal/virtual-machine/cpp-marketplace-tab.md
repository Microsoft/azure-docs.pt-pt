---
title: Separador de Marketplace de máquina virtual no Portal de parceiros na Cloud do Azure | Documentos da Microsoft
description: Descreve a guia Marketplace utilizada na criação de uma oferta de VM do Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 551f2bbc4846a1b47dafaa03cca08b424572ebf5
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214882"
---
# <a name="virtual-machine-marketplace-tab"></a>Separador do Marketplace de máquina virtual

O **Marketplace** separador da **nova oferta** página permite-lhe fornecer os seus clientes potenciais informações de marketing, vendas e legais e contratos e gerir as oportunidades potenciais geradas a partir do Marketplace. Esta forma longa é dividida em quatro seções: **Descrição geral**, **artefactos de Marketing**, **numa gestão**, e **legais**. 

## <a name="overview-section"></a>Seção de visão geral
Nesta secção, é possível inserir as informações gerais sobre a sua oferta do Azure Marketplace.  Um anexado asterisco (*) no nome do campo indica que é necessário.

![Secção de descrição geral da guia Marketplace do formulário de nova oferta de mensagens em fila para máquinas virtuais](./media/publishvm_008.png)

A tabela seguinte descreve a finalidade e o conteúdo desses campos.

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| **Title** (Título)                 | Título da oferta, muitas vezes, o nome longo, formal. Este título será ser apresentado de forma destacada no marketplace.  Comprimento máximo de 50 carateres. |
| **Resumo**               | Breve objetivo ou a função da solução.  Comprimento máximo de 100 carateres. |
| **Resumo completo**          | Objetivo ou a função da solução.  Comprimento máximo de 256 carateres. |
| **Descrição**           | Descrição da solução.  Comprimento máximo de 3000 carateres, oferece suporte a formatação de HTML simples. |
| **Canal de revendedor CSP da Microsoft** | Fornecedores de soluções (CSP) parceiro canal optar ativamente por participar na cloud agora está disponível.  Veja [fornecedores de soluções Cloud](../../cloud-solution-providers.md) para obter mais informações sobre a sua oferta através do CSP da Microsoft de marketing de parceiros canais. |
| **Identificador de marketing**  | Uma URL exclusiva para associar a esta oferta, normalmente inclui a sua organização e o nome da solução, 50 carateres de comprimento máximo.  Por exemplo: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **IDs de subscrição de pré-visualização** | Adicione um e 100 identificadores de subscrição dos visualizadores. Estas subscrições listadas em branco têm acesso para a oferta assim que for publicado, antes de entrar no ar. |
| **Ligações úteis**          | Adicione o URL para a documentação atualizados, notas de versão, FAQs e assim por diante. |
| **Sugestões de categorias (máx. 5)** | Seleção múltipla de negócios e técnicas categorias que oferecem pode ser melhor associada.  Máximo permitido de cinco.  |
|  |  |


## <a name="marketing-artifacts-section"></a>Secção de artefactos de marketing

Esta segunda secção está dividida em três subsecções: **Logótipos**, **captura de ecrã**, e **vídeos**. Logótipos são o único necessário artefactos, de marketing, no entanto, todos são altamente recomendáveis para melhor apelo do cliente.

![Secção de artefactos da guia Marketplace do formulário de nova oferta de mensagens em fila para máquinas virtuais de marketing](./media/publishvm_009.png)

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| *Logótipos*  |  |
| **Pequenas**                 | mapa de bits do pixel de 40 x 40. ico                                                      |
| **Médio**                | mapa de bits do pixel 90 x 90. ico                                                      |
| **Grandes**                 | mapa de bits do pixel 115 x 115. ico                                                   |
| **Ampla**                  | mapa de bits do pixel de 255 x 115. ico                                                    |
| **Hero**                  | 815x290 bitmap.  Opcional, no entanto uma vez carregado não é possível eliminar o ícone de hero. |
| *Capturas de ecrã*  | É opcional, mas máximo de cinco capturas de ecrã por SKU. |
| **Nome**                  | Nome ou o título <!-- TODO - max char length? none specified in UI -->                               |
| **Imagem**                 | Imagem de captura de ecrã, 533 x 324 pixel                                         |
| *Vídeos*  |  |
| **Nome**                  | Nome ou o título  <!-- TODO - max char length? -->                              |
| **Link**                  | URL de vídeo, alojado no YouTube ou Vimeo                                        |
| **Miniatura**             | 533 x 324 bitmap                                                               |
|  |  |


### <a name="logo-guidelines"></a>Diretrizes para logótipos

<!-- TD: It seems like this section could be better located in some common area, maybe a AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Todos os logótipos carregados para o Portal de parceiro de Cloud devem seguir as diretrizes:

*  O design do Azure tem uma paleta de cores simples. Mantenha o número de principal e secundário cores seu logótipo baixa.
*  As cores de tema do portal do Azure são brancas e preto. Portanto, evite usando essas cores como a cor de fundo de suas logótipos. Utilize uma cor que tornaria os logótipos proeminente no portal do Azure. Recomendamos cores primárias simples. Se estiver a utilizar o fundo transparente, em seguida, certifique-se de que o texto/logótipos não são branco ou preto ou azul.
*  Não utilize um plano de fundo de gradação no seu logótipo.
*  Evite colocar texto — até mesmo a sua empresa ou o nome da marca — no logótipo. O aspeto e funcionalidade do seu logótipo deve ser "simples" e deve evitar gradientes.
*  Não stretch o logótipo.

#### <a name="hero-logo"></a>Logótipo de destaque

O logótipo de Hero é opcional; No entanto, depois de carregados, não é possível eliminar o ícone de hero.  O ícone de logótipo Hero deve seguir as diretrizes:

*  Planos de fundo transparentes, pretas e brancas não são permitidos para os ícones de hero.
*  Evite usar qualquer cor leve como plano de fundo do ícone hero.  O nome de exibição do publicador, título de plano e a oferta longa resumo são apresentados na cor da fonte branco e tem se destacam em segundo plano.
*  Evite utilizar a maioria dos textos durante a conceção o logótipo de hero.  O nome do publicador, título do plano, oferta longa resumo e um botão de criar são incorporadas por meio de programação dentro do ícone de hero quando a oferta apresenta uma lista. 
* Incluir um retângulo não utilizado no lado direito do seu ícone hero, de pixel de tamanho 415 x 100 e compensadas 370 px da esquerda.  

Por exemplo, o ícone de hero seguinte é para o Azure Container Service.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Ícone de hero de exemplo para o Azure Container Service](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Exemplo de informações de marketing 

A imagem seguinte demonstra como informações de marketing é apresentado na página de produto principal do Microsoft Windows Server.

![Página de produto de exemplo para o Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Levar a secção sobre gestão
<!-- this all should be referenced in a common location for lead management, not in this file. nothing unique for a vm specifically. -->

A terceira seção permite-lhe recolher clientes oportunidades potenciais geradas a partir de suas ofertas do Azure Marketplace. Ele oferece as seguintes opções de armazenamento (a partir de uma lista suspensa) para obter estas informações de oportunidades potenciais.

* **Nenhum** -padrão, informações de oportunidades potenciais não é coletada.
* Tabela do Azure - escritos para a tabela do Azure especificada por uma cadeia de ligação.
* Dynamics CRM Online - escrito para o [do Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) instância, especificada pelas credenciais de uma URL e a autenticação.
* Ponto final de HTTPS - escrito para o ponto final HTTPS especificado como um payload JSON.
* Marketo - escrito especificado [Marketo](https://www.marketo.com/) instância, especificada pelo ID do servidor, o ID de munchkin e o ID do formulário.
* Salesforce – escrito para um [Salesforce](https://www.salesforce.com/) base de dados, especificada por um identificador de objeto.

Depois de publicar a oferta com êxito, a ligação da oportunidade potencial é validada e um líder de teste é enviado automaticamente para o destino configurado. Informações de provocar deverão ser geridas continuamente e estas definições devem ser imediatamente atualizadas sempre que forem feitas alterações em sua arquitetura de gestão do cliente.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Secção legal

Esta última seção permite-lhe fornecer os dois documentos legais necessários para cada oferta: Política de privacidade e termos de utilização.

|  **Campo**                |     **Descrição**                                                          |
|  ---------                |     ---------------                                                          |
| **URL de política de privacidade**    | URL para a política de privacidade postado                                            |
| **Termos de utilização**          | política como texto sem formatação ou simple HTML.  <!-- TODO - max char length? -->       |
|  |  |

<br/>

Nos próximos [suportar](./cpp-support-tab.md) guia, irá fornecer recursos de suporte técnico e de utilizador para a sua oferta.

