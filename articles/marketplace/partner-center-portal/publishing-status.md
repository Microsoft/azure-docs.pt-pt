---
title: Verificar o estado de publicação da sua oferta do Marketplace comercial
description: Verifique o estado da validação, certificação e passos de pré-visualização necessárias para publicar uma oferta através do Marketplace comerciais na Microsoft Partner Center.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 9bf3136969974abbe9a99a5632478e3cbb22307e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474517"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Verificar o estado de publicação da sua oferta do Marketplace comercial

Pode ver o seu atual **estado de publicação** no **descrição geral da oferta** separador do [portal Marketplace comerciais](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Centro de parceiros.

Um dos indicadores de estado seguintes deve ser exibido para cada oferta.

| **Estado**    | **Descrição**  |
| :---------- | :-------------------|
| **Rascunho** | Oferta foi criada, mas ele não está a ser publicado. |
| **Publicar em curso** | Plano de oferta/está a funcionar ao percorrer os passos do processo de publicação. |
| **Atenção necessária** | Uma questão crítica foi descoberta durante a certificação da Microsoft ou de qualquer uma das etapas de publicação. |
| **Pré-visualização** | A oferta foi certificada pela Microsoft e agora awaits uma verificação final pelo editor. Selecione vai direto para tornar a oferta em direto. |
| **Live** | Oferta está em direta no marketplace e pode ser vista e adquirida por parte dos clientes. |
| **Venda de paragem pendente** | Publicador selecionada "parar venda" na oferta ou plano, mas ainda não foi concluída a ação. |
| **Não está disponível no marketplace** | Foi removido um publicadas anteriormente/plano de oferta no marketplace. |

## <a name="automated-validation"></a>Validação automáticos

A primeira etapa no processo de publicação é um conjunto de validações automatizadas. Cada etapa de validação corresponde a um recurso que escolheu para ativar na criação da sua oferta. Se essa funcionalidade não foi ativada, a validação ignora-se em frente para a próxima etapa de publicação. Cada verificação de validação deve ser concluída antes do Estado de publicação é aprovado.

- **Configuração de fluxo de compra da oferta (< 10 min)**

Neste passo, podemos assegurar que a sua oferta pode ser concluída se forem comprados pelos clientes através do portal do Azure. Este passo só é aplicável para ofertas que está a ser vendidas através do Microsoft.

- **Teste de validação de dados de unidade (~ 5 min)**

Neste passo, iremos validar os dados fornecidos na seção de configuração técnica da oferta de unidade de teste. Funcionalidade de teste de unidade é testada e aprovada. Este passo só é aplicável para ofertas com um test-drive ativado.

- **Testar a unidade de aprovisionamento (aproximadamente 30 min)**

Neste passo, após validar os dados e a funcionalidade da sua versão de teste no passo anterior, implemente e replicar as instâncias da sua versão de teste para que eles estão prontos para uso do cliente.  Este passo só é aplicável para ofertas com um test-drive ativado.

- **Levar a validação de gestão e de registo (< 15 min)**

Neste passo, vamos confirmar que o seu sistema de gestão de oportunidades potenciais pode receber oportunidades potenciais com base nos detalhes fornecidos na configuração da oferta. Este passo só é aplicável para ofertas com a gestão de oportunidades potenciais ativada.

## <a name="certification"></a>Certificação

Antes de serem publicados, devem ser certificadas submetidas para o mercado comercial no Centro de parceiros de ofertas. Submetido a ofertas de passar por testes rigorosos, algumas automatizadas e manuais, incluindo uma verificação em relação a outras pessoas a [políticas de certificação do Azure Marketplace](https://docs.microsoft.com/legal/marketplace/general-policies). As submissões devem ser marcadas elegíveis para a certificação antes de eles avançam para o passo seguinte no fluxo de publicação da oferta.

### <a name="types-of-validation-that-take-place-during-certification"></a>Tipos de validação que ocorrem durante a certificação

Existem três níveis de validação incluídos no processo de certificação para cada oferta submetido.

- Elegibilidade de negócios do publicador
- Validação de conteúdos
- Validação técnica

#### <a name="publisher-business-eligibility"></a>Elegibilidade de negócios do publicador

Cada tipo de oferta verifica um conjunto de critérios de elegibilidade de base que o publicador tem de cumprir. Os critérios de qualificação podem incluir o estado da MPN do publicador, competências mantidas, níveis de competência, etc.

#### <a name="content-validation"></a>Validação de conteúdos

Durante a validação de conteúdos, as informações que introduziu quando criou a sua oferta são verificados relativamente à qualidade e relevância. Estas verificações rever as suas entradas para o mercado de listagem a obter detalhes, preços, disponibilidade, planos de associados, etc. De acordo com o Azure Marketplace e/ou o AppSource critérios de listagem, iremos validar que a sua oferta inclui:

- um título que descreve com precisão a oferta;
- descrições bem escritas que fornecem uma visão geral completa e uma proposta de valor;
- capturas de ecrã de qualidade e que acompanha vídeos; e
- uma explicação de como a oferta utiliza as ferramentas e plataformas da Microsoft.

Saiba mais sobre os critérios de validação de conteúdo ao ler o [listagem políticas gerais](https://docs.microsoft.com/legal/marketplace/general-policies#10-general-listing-policies).

#### <a name="technical-validation"></a>Validação técnica

Durante a validação técnica, a oferta (pacote ou binário) passa pelas seguintes verificações.
- Analisados relativamente a software maligno
- Chamadas de rede monitorizadas
- Pacote analisado
- Análise completa de funcionalidade de real da oferta

A oferta é testada em várias plataformas e versões para garantir que é robusto.

Reveja os detalhes de configuração específica necessários para a sua oferta na seção de configuração técnicas deste documento.

### <a name="certification-failure-report"></a>Relatório de falha de certificação

Após a conclusão da revisão, se a sua oferta passou a certificação, em seguida, ele passa ao longo para o passo seguinte no processo de publicação. Se a sua oferta falhou qualquer uma das verificações de listagem, a técnica ou a política, ou se não é elegível para submeter uma oferta desse tipo, um relatório de falha de certificação é gerado e enviados por e-mail para si.

Este relatório contém descrições de todas as políticas que falhou, juntamente com as notas de revisão. Reveja este relatório de correio eletrónico, resolva problemas, fazer atualizações para a sua oferta em que for necessário e volte a submeter a oferta através do [portal Marketplace comerciais](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Centro de parceiros. (Pode submeter novamente a oferta quantas vezes conforme necessário até passar certificação).

## <a name="preview-creation"></a>Criação de pré-visualização

Durante a **pré-visualizar criação** passo, vamos criar uma versão da sua oferta acessível para apenas o público-alvo que especificou na secção pré-visualização da sua oferta.

## <a name="publisher-approval"></a>Aprovação do publicador

Neste passo, vai ser enviado por e-mail com um pedido para rever e aprovar a versão de demonstração de oferta antes da última etapa de publicação.

Se tiver selecionado para vender a sua oferta através da Microsoft, será capaz de testar a aquisição e a implantação da sua oferta para assegurar que cumpre os requisitos durante esta fase de aprovação de pré-visualização. A oferta não ainda estará disponível no marketplace públicas. Depois de testar e aprovar esta pré-visualização, terá de selecionar **Go-Live** sobre o [ **descrição geral da oferta** ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) dashboard.

Se desejar fazer alterações para a oferta durante esta fase de pré-visualização, pode editar e volte a submeter para publicar uma nova pré-visualização. Consulte o artigo [atualização existente marketplace oferece](#update-existing-marketplace-offers) para obter detalhes sobre mais alterações.

Se a sua oferta já está em direto e disponibilizado ao público no marketplace, as atualizações feitas não entrar em direto até selecionar **Go-live** sobre o [ **descrição geral da oferta** ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) dashboard.

### <a name="publish-offer-to-the-public"></a>Publicar oferta para o público

Inicie sessão no Centro de parceiros e aceder à oferta. Será redirecionado para o **descrição geral da oferta** página. Na parte superior desta página, verá uma opção para **implemente**. Selecione **ativação online,** e depois de confirmar, a oferta passarão a ser publicado para o público. Receberá uma notificação por e-mail quando a oferta está em direto.

## <a name="publish"></a>Publicar

Agora que selecionou a **implemente** na sua oferta, tornando-a disponível no marketplace, há uma série de verificações de validação final que irá ser apresentou para se certificar de que a oferta em direto é configurada como a pré-visualização versão da oferta.

- **Configuração de fluxo de compra da oferta (> 10 min)**

Neste passo, podemos assegurar que a sua oferta pode ser concluída se forem comprados pelos clientes através do portal do Azure. Este passo só é aplicável para ofertas que está a ser vendidas através do Microsoft.

- **Teste de validação de dados de unidade (~ 5 min)**

Neste passo, iremos validar os dados fornecidos na seção de configuração técnica da oferta de unidade de teste. Funcionalidade de teste de unidade é testada e aprovada. Este passo só é aplicável para ofertas com um test-drive ativado.

- **Testar a unidade de aprovisionamento (aproximadamente 30 min)**

Neste passo, implemente e replicar as instâncias da sua versão de teste para que eles estão prontos para uso do cliente.  Este passo só é aplicável para ofertas com um test-drive ativado.

- **Levar a validação de gestão e de registo (> 15 min)**

Neste passo, vamos confirmar que o seu sistema de gestão de oportunidades potenciais pode receber oportunidades potenciais com base nos detalhes fornecidos na configuração da oferta. Este passo só é aplicável para ofertas com a gestão de oportunidades potenciais ativada.

- **Publicar final (> 30 minutos)**

Neste passo, podemos assegurar que fica publicamente disponível a sua oferta no marketplace.

## <a name="update-existing-marketplace-offers"></a>Atualizar existente ofertas do marketplace

Se pretender efetuar alterações a uma oferta que já publicou, terá primeiro de atualizar a oferta existente e, em seguida, publicá-lo novamente.

## <a name="next-steps"></a>Passos Seguintes

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
