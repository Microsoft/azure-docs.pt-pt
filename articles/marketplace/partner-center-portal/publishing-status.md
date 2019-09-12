---
title: Verificar o status de publicação da sua oferta do Marketplace comercial
description: Verifique o status das etapas de validação, certificação e visualização necessárias para publicar uma oferta por meio do Marketplace comercial no Microsoft Partner Center.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: ec2a174c365e31520fe99ca0b505bbb62faad8c6
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883400"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Verificar o status de publicação da sua oferta do Marketplace comercial

Você pode exibir seu **status de publicação** atual na guia **visão geral da oferta** do portal do [Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Partner Center.

Um dos seguintes indicadores de status deve ser exibido para cada oferta.

| **Estado**    | **Descrição**  |
| :---------- | :-------------------|
| **Esboça** | A oferta foi criada, mas não está sendo publicada. |
| **Publicação em andamento** | A oferta/plano está trabalhando de sua maneira por meio das etapas do processo de publicação. |
| **Atenção necessária** | Um problema crítico foi descoberto durante a certificação pela Microsoft ou por qualquer uma das etapas de publicação. |
| **Pré-visualização** | A oferta foi certificada pela Microsoft e agora aguarda uma verificação final pelo Publicador. Selecione entrar em tempo real para tornar a oferta ativa. |
| **Live** | A oferta está ativa no Marketplace e pode ser vista e adquirida pelos clientes. |
| **Venda de parada pendente** | O Publicador selecionou "parar venda" na oferta ou no plano, mas a ação ainda não foi concluída. |
| **Não disponível no Marketplace** | Uma oferta/plano publicado anteriormente no Marketplace foi removido. |

## <a name="automated-validation"></a>Validação automatizada

A primeira etapa no processo de publicação é um conjunto de validações automatizadas. Cada etapa de validação corresponde a um recurso que você optou por habilitar na criação de sua oferta. Se esse recurso não tiver sido habilitado, a validação passará para a próxima etapa de publicação. Cada verificação de validação deve ser concluída antes que o status de publicação seja aprovado.

- **Instalação do fluxo de compra da oferta (< 10 min)**

Nesta etapa, garantimos que sua oferta pode ser atendida quando adquirida por clientes por meio do portal do Azure. Esta etapa só é aplicável para ofertas vendidas pela Microsoft.

- **Validação de dados do Test Drive (aproximadamente 5 min)**

Nesta etapa, validamos os dados fornecidos na seção de configuração técnica test drive da oferta. A funcionalidade do Test Drive é testada e aprovada. Esta etapa é aplicável somente para ofertas com um test drive habilitado.

- **Provisionamento de drive de teste (~ 30 min)**

Nesta etapa, depois de validar os dados e a funcionalidade do seu test drive na etapa anterior, implantamos e replicamos instâncias do seu test drive para que elas estejam prontas para uso do cliente.  Esta etapa é aplicável somente para ofertas com um test drive habilitado.

- **Validação e registro de gerenciamento de leads (< 15 min)**

Nesta etapa, confirmamos que o seu sistema de gerenciamento de leads pode receber clientes potenciais com base nos detalhes fornecidos na configuração da oferta. Esta etapa só é aplicável para ofertas com gerenciamento de Lead habilitado.

## <a name="certification"></a>Certificação

Antes de ser publicado, as ofertas enviadas para o mercado comercial no Partner Center devem ser certificadas. As ofertas enviadas passam por testes rigorosos, alguns manuais automatizados e outros, incluindo uma verificação nas [políticas de certificação do Azure Marketplace](https://docs.microsoft.com/legal/marketplace/general-policies). Os envios de oferta devem ser marcados como qualificados para certificação antes de passar para a próxima etapa no fluxo de publicação.

### <a name="types-of-validation-that-take-place-during-certification"></a>Tipos de validação que ocorrem durante a certificação

Há três níveis de validação incluídos no processo de certificação para cada oferta enviada.

- Elegibilidade de negócios do Publicador
- Validação de conteúdo
- Validação técnica

#### <a name="publisher-business-eligibility"></a>Elegibilidade de negócios do Publicador

Cada tipo de oferta verifica um conjunto de critérios de qualificação base que o Publicador deve atender. Os critérios de qualificação podem incluir o status do MPN do editor, as competências mantidas, os níveis de competência, etc.

#### <a name="content-validation"></a>Validação de conteúdo

Durante a validação de conteúdo, as informações inseridas quando você criou sua oferta são verificadas quanto à qualidade e relevância. Essas verificações revisarão suas entradas para o Marketplace listar detalhes, preços, disponibilidade, planos associados etc. Para atender aos critérios de listagem do Azure Marketplace e/ou AppSource, validaremos que sua oferta inclui:

- um título que descreve com precisão a oferta;
- descrições bem escritas que fornecem uma visão geral completa e uma proposta de valor;
- capturas de tela de qualidade e vídeos de acompanhamento; e
- uma explicação de como a oferta utiliza plataformas e ferramentas da Microsoft.

Saiba mais sobre os critérios de validação de conteúdo lendo as [políticas de listagem geral](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general).

#### <a name="technical-validation"></a>Validação técnica

Durante a validação técnica, a oferta (pacote ou binário) passa pelas verificações a seguir.
- Verificação de malware
- Chamadas de rede monitoradas
- Pacote analisado
- Verificação completa da funcionalidade real da oferta

A oferta é testada em várias plataformas e versões a fim de garantir que ela seja robusta.

Examine os detalhes de configuração específicos necessários para sua oferta na seção de configuração técnica deste documento.

### <a name="certification-failure-report"></a>Relatório de falha de certificação

Após a conclusão da revisão, se sua oferta tiver passado para a certificação, ela passará para a próxima etapa no processo de publicação. Se sua oferta falhou em qualquer uma das listas, técnicas ou verificações de política, ou se você não estiver qualificado para enviar uma oferta desse tipo, um relatório de falha de certificação será gerado e enviado por email para você.

Este relatório contém descrições de todas as políticas que falharam, juntamente com as notas de revisão. Examine este relatório de email, resolva quaisquer problemas, faça atualizações em sua oferta quando necessário e reenvie a oferta usando o [portal do Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Partner Center. (Você pode reenviar a oferta quantas vezes forem necessárias até a aprovação da certificação).

## <a name="preview-creation"></a>Criação de visualização

Durante a etapa de **criação de visualização** , criamos uma versão de sua oferta acessível apenas para o público que você especificou na seção de visualização de sua oferta.

>[!Note]
> Não use esta etapa para dar às pessoas fora da visibilidade da sua organização uma oferta. Em vez disso, use a opção **oferta privada** . Neste ponto, sua oferta não foi totalmente testada e validada e não está pronta para distribuição externa.

## <a name="publisher-approval"></a>Aprovação do Publicador

Nesta etapa, você receberá um email com uma solicitação para revisar e aprovar sua versão prévia da oferta antes da etapa de publicação final.

Se você tiver optado por vender sua oferta pela Microsoft, poderá testar a aquisição e a implantação de sua oferta para garantir que ela atenda aos seus requisitos durante este estágio de aprovação de visualização. Sua oferta ainda não estará disponível no Marketplace público. Depois de testar e aprovar essa visualização, será necessário selecionar **Go-Live** no painel [**visão geral da oferta**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) .

Se desejar fazer alterações na oferta durante esse estágio de visualização, você poderá editar e reenviar para publicar uma nova visualização. Consulte o artigo [Atualizar ofertas existentes do Marketplace](#update-existing-marketplace-offers) para obter detalhes sobre mais alterações.

Se sua oferta já estiver ativa e disponível para o público no Marketplace, todas as atualizações feitas não entrarão em funcionamento até que você selecione **Go-Live** no painel [**visão geral da oferta**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) .

### <a name="publish-offer-to-the-public"></a>Publicar oferta no público

Entre no Partner Center e acesse a oferta. Você será redirecionado para a página **visão geral da oferta** . Na parte superior desta página, você verá uma opção para entrar no **ar**. Selecione **Go Live** e, depois de confirmar, a oferta começará a ser publicada no público. Você receberá uma notificação por email quando a oferta estiver ativa.

## <a name="publish"></a>Publicar

Agora que você optou por **entrar em funcionamento** com sua oferta, disponibilizando-a no Marketplace, há uma série de verificações finais de validação que serão percorridas para garantir que a oferta ao vivo seja configurada da mesma forma que a versão prévia da oferta.

- **Instalação do fluxo de compra da oferta (> 10 min)**

Nesta etapa, garantimos que sua oferta pode ser atendida quando adquirida por clientes por meio do portal do Azure. Esta etapa só é aplicável para ofertas vendidas pela Microsoft.

- **Validação de dados do Test Drive (aproximadamente 5 min)**

Nesta etapa, validamos os dados fornecidos na seção de configuração técnica test drive da oferta. A funcionalidade do Test Drive é testada e aprovada. Esta etapa é aplicável somente para ofertas com um test drive habilitado.

- **Provisionamento de drive de teste (~ 30 min)**

Nesta etapa, implantamos e replicamos instâncias do seu test drive para que elas estejam prontas para uso do cliente.  Esta etapa é aplicável somente para ofertas com um test drive habilitado.

- **Validação e registro de gerenciamento de leads (> 15 min)**

Nesta etapa, confirmamos que o seu sistema de gerenciamento de leads pode receber clientes potenciais com base nos detalhes fornecidos na configuração da oferta. Esta etapa só é aplicável para ofertas com gerenciamento de Lead habilitado.

- **Publicação final (> 30 minutos)**

Nesta etapa, garantimos que sua oferta se torne publicamente disponível no Marketplace.

## <a name="update-existing-marketplace-offers"></a>Atualizar ofertas existentes do Marketplace

Se você quiser fazer alterações em uma oferta que já publicou, precisará primeiro atualizar a oferta existente e, em seguida, publicá-la novamente.

## <a name="next-steps"></a>Passos Seguintes

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
