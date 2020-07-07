---
title: Consulte o estado editorial da sua oferta de Mercado Comercial
description: Verifique o estado da validação, certificação e passos de pré-visualização necessários para publicar uma oferta através do Mercado Comercial no Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 012a574887d9980e0c71c3af84ff70ca8d31312c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80275684"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Consulte o estado editorial da sua oferta de Mercado Comercial

Pode ver o seu **estado atual de Publicação** no separador **'Oferta'** do [portal Mercado Comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Partner Center.

Um dos seguintes indicadores de estado deve ser apresentado para cada oferta.

| **Estado**    | **Descrição**  |
| :---------- | :-------------------|
| **Rascunho** | A oferta foi criada, mas não está a ser publicada. |
| **Publicar em curso** | A Oferta/Plano está a trabalhar nos passos do processo de publicação. |
| **Atenção necessária** | Um problema crítico foi descoberto durante a certificação pela Microsoft ou qualquer uma das etapas de publicação. |
| **Pré-visualizar** | A oferta foi certificada pela Microsoft, e aguarda agora uma verificação final pela editora. Selecione ir ao vivo para fazer a oferta ao vivo. |
| **Em direto** | A oferta é ao vivo no mercado e pode ser vista e adquirida pelos clientes. |
| **Pendente parar de vender** | A editora selecionou "stop sell" em oferta ou plano, mas a ação ainda não foi concluída. |
| **Não disponível no mercado** | Foi removida uma oferta/plano previamente publicado no mercado. |

## <a name="automated-validation"></a>Validação automatizada

O primeiro passo no processo de publicação é um conjunto de validações automatizadas. Cada passo de validação corresponde a uma funcionalidade que escolheu para permitir a criação da sua oferta. Se essa funcionalidade não foi ativada, a validação avança para o próximo passo de publicação. Cada verificação de validação deve ser completada antes da aprovação do estado de publicação.

- **Oferta configuração do fluxo de compra (<10 min)**

Neste passo, garantimos que a sua oferta pode ser cumprida quando adquirida pelos clientes através do portal Azure. Este passo só é aplicável para as ofertas que são vendidas através da Microsoft.

- **Validação de dados de test drive (~5 min)**

Neste passo, validamos os dados fornecidos na secção de configuração técnica da oferta. A funcionalidade de test drive é testada e aprovada. Este passo só é aplicável para ofertas com um test drive ativado.

- **Provisão de unidade de ensaio (~30 min)**

Neste passo, depois de validar os dados e funcionalidades do seu test drive no passo anterior, implementamos e replicamos instâncias do seu test drive para que estejam prontos para uso do cliente.  Este passo só é aplicável para ofertas com um test drive ativado.

- **Validação e registo de gestão de chumbo (<15 min)**

Neste passo, confirmamos que o seu sistema de gestão de chumbo pode receber leads de clientes com base nos detalhes fornecidos na configuração da Oferta. Este passo só é aplicável para ofertas com gestão lead habilitada.

## <a name="certification"></a>Certificação

Antes de serem publicadas, as ofertas submetidas ao Mercado Comercial no Centro de Parceiros devem ser certificadas. As ofertas submetidas são submetidas a testes rigorosos, alguns manuais automatizados e outros, incluindo uma verificação contra as políticas de certificação do [Azure Marketplace.](https://docs.microsoft.com/legal/marketplace/general-policies) As submissões de oferta devem ser marcadas elegíveis para certificação antes de seguirem para o próximo passo no fluxo editorial.

### <a name="types-of-validation-that-take-place-during-certification"></a>Tipos de validação que ocorrem durante a certificação

Existem três níveis de validação incluídos no processo de certificação para cada oferta submetida.

- Elegibilidade do negócio dos editores
- Validação de conteúdos
- Validação técnica

#### <a name="publisher-business-eligibility"></a>Elegibilidade do negócio dos editores

Cada tipo de oferta verifica um conjunto de critérios de elegibilidade base que o editor deve cumprir. Os critérios de elegibilidade podem incluir o estatuto de MPN do editor, competências detidas, níveis de competência, etc.

#### <a name="content-validation"></a>Validação de conteúdos

Durante a validação do conteúdo, as informações inseridas quando criou a sua oferta são verificadas para obter qualidade e relevância. Estes cheques irão rever as suas entradas para os detalhes da listagem do mercado, preços, disponibilidade, planos associados, etc. Para cumprir os critérios de listagem Azure Marketplace e/ou AppSource, validaremos que a sua oferta inclui:

- um título que descreve com precisão a oferta;
- descrições bem escritas que fornecem uma visão geral e uma proposta de valor;
- imagens de qualidade e vídeos que acompanham; e
- uma explicação de como a oferta utiliza plataformas e ferramentas da Microsoft.

Saiba mais sobre os critérios de validação de conteúdos através da leitura das [políticas gerais de listagem.](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general)

#### <a name="technical-validation"></a>Validação técnica

Durante a validação técnica, a oferta (embalagem ou binária) passa pelas seguintes verificações.
- Digitalizado para malware
- Chamadas de rede monitorizadas
- Pacote analisado
- Digitalização minuciosa da funcionalidade real da oferta

A oferta é testada em várias plataformas e versões de forma a garantir que é robusta.

Reveja os detalhes de configuração específicos necessários para a sua oferta na secção de configuração técnica deste documento.

### <a name="certification-failure-report"></a>Relatório de falha de certificação

Após a conclusão da revisão, se a sua oferta tiver passado a certificação, então vai para o passo seguinte no processo de publicação. Se a sua oferta falhou em qualquer uma das verificações de listagem, técnicas ou políticas, ou se não estiver elegível para submeter uma oferta desse tipo, é gerado e enviado um relatório de falha de certificação para si.

Este relatório contém descrições de quaisquer políticas que falharam, juntamente com as notas de revisão. Reveja este relatório de e-mail, aborde quaisquer problemas, fazendo atualizações à sua oferta sempre que necessário, e reenvia a oferta usando o [portal mercado comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Partner Center. (Pode reenviar a oferta quantas vezes for necessária até passar a certificação).

## <a name="preview-creation"></a>Criação de pré-visualização

Durante o passo **de criação de pré-visualização,** criamos uma versão da sua oferta acessível apenas ao público especificado na secção pré-visualização da sua oferta.

>[!Note]
> Não use este passo para dar visibilidade às pessoas fora da sua organização. Em vez disso, utilize a opção **Oferta Privada.** Neste momento, a sua oferta não foi totalmente testada e validada, e não está pronta para distribuição externa.

## <a name="publisher-approval"></a>Aprovação de editor

Neste passo, será enviado por e-mail com um pedido para que reveja e aprove a sua pré-visualização da oferta antes da fase final de publicação.

Se tiver selecionado vender a sua oferta através da Microsoft, poderá testar a aquisição e implementação da sua oferta para garantir que satisfaz os seus requisitos durante esta fase de aprovação de pré-visualização. A sua oferta ainda não estará disponível no mercado público. Assim que testar e aprovar esta pré-visualização, terá de selecionar o **Go-Live** no painel [**de visão geral**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) da oferta.

Se quiser fazer alterações na oferta durante esta fase de pré-visualização, poderá editar e reenviar para publicar uma nova pré-visualização. Consulte o artigo [Atualizar as ofertas de marketplace existentes](#update-existing-marketplace-offers) para mais alterações.

Se a sua oferta já estiver ao vivo e disponível para o público no mercado, quaisquer atualizações que faça não serão transmitidas ao vivo até que selecione **Go-live** no painel [**de visão geral**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) da Oferta.

### <a name="publish-offer-to-the-public"></a>Publicar oferta ao público

Inscreva-se no Partner Center e aceda à oferta. Será redirecionado para a página geral da **Oferta.** No topo desta página, verá uma opção para **Ir ao vivo.** Selecione **Go ao vivo,** e depois de confirmar, a oferta começará a ser publicada ao público. Receberá uma notificação por e-mail quando a oferta estiver ao vivo.

## <a name="publish"></a>Publicar

Agora que selecionou para **ir ao vivo** com a sua oferta, disponibilizando-a no mercado, existem uma série de verificações de validação finais que serão pisadas para garantir que a oferta ao vivo está configurada tal como a versão de pré-visualização da oferta.

- **Oferta configuração do fluxo de compra (>10 min)**

Neste passo, garantimos que a sua oferta pode ser cumprida quando adquirida pelos clientes através do portal Azure. Este passo só é aplicável para as ofertas que são vendidas através da Microsoft.

- **Validação de dados de test drive (~5 min)**

Neste passo, validamos os dados fornecidos na secção de configuração técnica da oferta. A funcionalidade de test drive é testada e aprovada. Este passo só é aplicável para ofertas com um test drive ativado.

- **Provisão de unidade de ensaio (~30 min)**

Neste passo, implementamos e replicamos instâncias do seu test drive para que estejam prontos para uso do cliente.  Este passo só é aplicável para ofertas com um test drive ativado.

- **Validação e registo de gestão de chumbo (>15 min)**

Neste passo, confirmamos que o seu sistema de gestão de chumbo pode receber leads de clientes com base nos detalhes fornecidos na configuração da Oferta. Este passo só é aplicável para ofertas com gestão lead habilitada.

- **Publicação final (>30 minutos)**

Neste passo, garantimos que a sua oferta se torne publicamente disponível no mercado.

## <a name="update-existing-marketplace-offers"></a>Atualizar ofertas de mercado existentes

Se quiser fazer alterações a uma oferta que já publicou, terá de atualizar primeiro a oferta existente e depois publicá-la novamente.

## <a name="next-steps"></a>Passos seguintes

- [Atualizar uma oferta existente no Marketplace Comercial](./update-existing-offer.md)
