---
title: Como rever e publicar uma oferta ao mercado comercial da Microsoft
description: Use partner Center para submeter a sua oferta para pré-visualizar, pré-visualizar a sua oferta e, em seguida, publicá-la no mercado comercial da Microsoft.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 08/12/2020
ms.openlocfilehash: eff1db3aca2cab77d18698634b84e8e1b1c99f7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89375890"
---
# <a name="how-to-review-and-publish-an-offer-to-the-commercial-marketplace"></a>Como rever e publicar uma oferta ao mercado comercial

Este artigo mostra-lhe como usar o Partner Center para submeter a sua oferta de publicação, pré-visualizar a sua oferta e, em seguida, publicá-la no mercado comercial. Também cobrimos como verificar o seu estado de publicação à medida que avança através das escadas de publicação. Já deve ter criado uma oferta que quer publicar.

## <a name="offer-status"></a>Estado da oferta

Pode rever o seu estado de oferta no **separador Visão Geral** do painel de instrumentos do mercado comercial no [Partner Center.](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) Um dos seguintes indicadores de estado é apresentado na coluna **'Estado'** de cada oferta.

| Estado | Descrição |
| ------------ | ------------- |
| Rascunho | A oferta foi criada, mas não está a ser publicada. |
| Publicar em curso | A oferta está a trabalhar no processo de publicação. |
| Atenção necessária | Descobrimos um problema crítico durante a certificação ou durante outra fase de publicação. |
| Pré-visualizar | Certificamos a oferta, que aguarda agora uma verificação final pela editora. Selecione **Vá ao vivo** para publicar a oferta ao vivo. |
| Em direto | A oferta é ao vivo no mercado e pode ser vista e adquirida pelos clientes. |
| Pendente parar de vender | A editora selecionou "stop sell" numa oferta ou plano, mas a ação ainda não foi concluída. |
| Não disponível no mercado | Uma oferta publicada anteriormente no mercado foi removida. |
|||

## <a name="validation-and-publishing-steps"></a>Passos de validação e publicação

Quando estiver pronto para submeter uma oferta de publicação, selecione **'Reveja e publique** no canto superior direito do portal'. A página **de Revisão e publicação** mostra o estado de cada página para a sua oferta, que pode ser uma das seguintes:

   - **Não começou** – A página está incompleta.
   - **Incompleto** – Falta informação necessária ou tem erros que precisam de ser corrigidos. Terá de voltar à página e atualizá-la.
   - **Completo** - A página está completa. Todos os dados necessários foram fornecidos e não há erros.

Se alguma das páginas tiver um estado diferente do **Completo,** tem de corrigir o problema nessa página e, em seguida, voltar à página **de Revisão e publicar** para confirmar que o estado agora mostra como **Completo**. Alguns tipos de oferta requerem testes. Em caso afirmativo, verá um campo **de Notas para certificação** onde precisa de fornecer instruções de teste à equipa de certificação e quaisquer notas suplementares úteis para a compreensão da sua app.

Depois de todas as páginas estarem completas e tiver introduzido notas de teste aplicáveis, **selecione Publicar** para iniciar os processos de validação e publicação. As fases e a sequência geral podem variar dependendo do tipo de oferta que está a publicar. A tabela a seguir mostra um possível fluxo de publicação. Cada fase é explicada mais detalhadamente nas seguintes secções.

| Fase | O que acontece |
| ------------ | ------------- | ------------- |
| [Validação automatizada](#automated-validation-phase) | Processamos um conjunto de validações automatizadas. |
| [Certificação](#certification-phase) | Fazemos validações manuais. |
| [Criação de pré-visualização](#preview-creation-phase) | A página de listagem para a pré-visualização da sua oferta está disponível para qualquer pessoa que tenha o link de pré-visualização. Se a sua oferta for vendida através da Microsoft (transacionável), apenas o público especificado na página de audiência de **pré-visualização** da sua oferta pode comprar e aceder à oferta para testes. |
| [Assinatura de editor](#publisher-sign-off-phase) | Enviamos-lhe um e-mail com um pedido para que faça a pré-visualização e aprovação da sua oferta. |
| [Publicar](#publish-phase) | Fazemos uma série de passos para verificar se a oferta de pré-visualização é publicada em direto para o mercado comercial. |
|||

## <a name="automated-validation-phase"></a>Fase de validação automatizada

O primeiro passo no processo de publicação é um conjunto de validações automatizadas. Cada passo de validação corresponde a uma funcionalidade que escolheu quando criou a oferta. Cada verificação de validação deve ser completa antes que a oferta possa avançar para o próximo passo no processo de publicação.

- **Oferta configuração do fluxo de compra** (<10 min)

   Garantimos que a sua oferta pode ser cumprida quando adquirida pelos clientes através do portal Azure. Este passo só é aplicável para as ofertas que são vendidas através da Microsoft.

- **Validação de dados de test drive** (~5 min)

   Validamos os dados que forneceu na página de configuração técnica da oferta. Testamos e aprovamos a funcionalidade de test drive. Este passo só é aplicável para ofertas com um test drive ativado.

- **Provisão de unidade de** ensaio (~30 min)

    Depois de validar os dados e funcionalidades do seu test drive no passo anterior, implementamos e replicamos instâncias do seu test drive para que estejam prontos para uso do cliente. Este passo só é aplicável para ofertas com um test drive ativado.

- **Validação e registo de gestão de chumbo** (<15 min)

    Confirmamos que o seu sistema de gestão de chumbo pode receber leads de clientes com base nos detalhes fornecidos na página **de configuração** da Oferta. Este passo só é aplicável para ofertas com gestão de chumbo habilitada.

## <a name="certification-phase"></a>Fase de certificação

As ofertas submetidas ao mercado comercial devem ser certificadas antes de serem publicadas. As ofertas são submetidas a testes rigorosos, alguns manuais automatizados e outros. Para saber mais, consulte [as políticas de certificação do mercado comercial.](https://aka.ms/commercial-marketplace-certification-policies)

### <a name="types-of-validation-that-take-place-during-certification"></a>Tipos de validação que ocorrem durante a certificação

Existem três níveis de validação incluídos no processo de certificação para cada oferta submetida.

- Elegibilidade do negócio dos editores
- Validação de conteúdos
- Validação técnica

#### <a name="publisher-business-eligibility"></a>Elegibilidade do negócio dos editores

Cada tipo de oferta verifica um conjunto de critérios de elegibilidade de base necessários. Estes critérios podem incluir o estatuto de MPN do editor, competências detidas, níveis de competência, etc.

#### <a name="content-validation"></a>Validação de conteúdos

A informação inserida quando criou a sua oferta é verificada pela qualidade e relevância. Estes cheques irão rever as suas entradas para os detalhes da listagem do mercado, preços, disponibilidade, planos associados, e assim por diante. Para cumprir os critérios de listagem do Microsoft AppSource e do Azure Marketplace, validaremos que a sua oferta inclui:

- Um título que descreve com precisão a oferta
- Descrições bem escritas que fornecem uma visão geral e uma proposta de valor
- Imagens e vídeos de qualidade
- Uma explicação de como a oferta utiliza plataformas e ferramentas da Microsoft.

Saiba mais sobre os critérios de validação de conteúdos através da leitura das [políticas gerais de listagem.](https://aka.ms/commercial-marketplace-certification-policies#100-general)

#### <a name="technical-validation"></a>Validação técnica

Durante a validação técnica, a oferta (embalagem ou binária) passa pelas seguintes verificações.

- Digitalizado para malware
- Chamadas de rede monitorizadas
- Pacote analisado
- Digitalização minuciosa da funcionalidade da oferta

A oferta é testada em várias plataformas e versões para garantir que é robusta.

### <a name="certification-failure-report"></a>Relatório de falha de certificação

Se a sua oferta falhar em qualquer uma das verificações de listagem, técnicas ou políticas, ou se não estiver elegível para submeter uma oferta desse tipo, enviaremos um relatório de falha de certificação para si.

Este relatório contém descrições de quaisquer políticas que falharam, juntamente com as notas de revisão. Reveja este relatório de e-mail, aborde quaisquer problemas, faça atualizações à sua oferta sempre que necessário e reenvia a oferta usando o portal do [mercado comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Partner Center. Pode reenviar a oferta quantas vezes for necessária até passar a certificação.

## <a name="preview-creation-phase"></a>Fase de criação em pré-visualização

Durante a fase de pré-visualização da criação, criamos uma versão da sua oferta que estará acessível apenas ao público especificado na página de audiência de **pré-visualização** da sua oferta, caso existam. A versão de pré-visualização da sua oferta não estará disponível para ninguém fora do público de pré-visualização até publicar a oferta ao vivo.

> [!NOTE]
> Não utilize o público de pré-visualização para dar visibilidade às pessoas fora da sua organização. Em vez disso, utilize a opção Oferta Privada. Neste momento, a sua oferta não foi totalmente testada e validada, e não está pronta para distribuição externa.

## <a name="publisher-sign-off-phase"></a>Fase de aprovação da editora

Quando a oferta estiver pronta para que você reveja e seja assinado, enviaremos um e-mail para solicitar que reveja e aprove a sua pré-visualização da oferta. Também pode atualizar a página geral da **Oferta** no seu navegador para ver se a sua oferta chegou à fase de desinsusitada do Publisher. Se tiver, o botão **Go ao vivo** e os links de pré-visualização estarão disponíveis.

A imagem que se segue mostra a página **geral da Oferta** para uma oferta SaaS. Os passos de validação que verá nesta página variam consoante o tipo de oferta e as seleções que fez quando criou a oferta.

![Ilustra a página geral da Oferta para uma oferta no Partner Center. São mostrados os botões Go ao vivo e os links de pré-visualização.](./media/publish-status-publisher-signoff.png)

### <a name="previewing-and-approving-your-offer"></a>Visualização e aprovação da sua oferta

> [!IMPORTANT]
> Para validar o fluxo de compra e configuração de ponta a ponta, compre a sua oferta enquanto estiver em Pré-Visualização. Primeiro, notifique a Microsoft com um [bilhete de apoio](https://aka.ms/marketplacesupport) para garantir que não processamos uma taxa.

Na página **'Geral' da Oferta,** verá links de pré-visualização no botão **Go live.** Haverá um link para a pré-visualização do AppSource, pré-visualização do Azure Marketplace ou ambos dependendo das opções que escolheu ao criar a sua oferta. Se optar por vender a sua oferta através da Microsoft, qualquer pessoa que tenha sido adicionada ao público de pré-visualização pode testar a aquisição e implementação da sua oferta para garantir que ela cumpre os seus requisitos durante esta fase.

Depois de aprovar a sua pré-visualização, selecione **Go live** para publicar a sua oferta ao vivo no mercado comercial. 

Se quiser fazer alterações após a pré-visualização da oferta, pode editar e reenviar o seu pedido de publicação. Se a sua oferta já estiver ao vivo e disponível para o público no mercado, quaisquer atualizações que faça não serão transmitidas ao vivo até que escolha **Vá ao vivo*. Para mais informações, consulte [Atualizar uma oferta existente no mercado comercial](./partner-center-portal/update-existing-offer.md)

## <a name="publish-phase"></a>Fase de publicação

Agora que escolheu ir ao vivo com a sua oferta, que a disponibiliza no mercado comercial, realizamos uma série de verificações de validação final para garantir que a oferta ao vivo está configurada tal como a versão de pré-visualização da oferta.

- **Oferta configuração do fluxo de compra** (>10 min)

    Garantimos que a sua oferta pode ser cumprida quando adquirida pelos clientes através do portal Azure. Este passo só é aplicável para as ofertas que são vendidas através da Microsoft.

- **Validação de dados de test drive** (~5 min)

    Validamos os dados que forneceu na página de configuração técnica da oferta. Testamos e aprovamos a funcionalidade de test drive. Este passo só é aplicável para ofertas com um test drive ativado.

- **Provisão de unidade de** ensaio (~30 min)

    Implementamos e replicamos instâncias do seu test drive para que estejam prontos para uso do cliente. Este passo só é aplicável para ofertas com um test drive ativado.

- **Validação e registo de gestão de chumbo** (>15 min)

    Confirmamos que o seu sistema de gestão de chumbo pode receber leads de clientes com base nos detalhes fornecidos na sua página **de configuração da Oferta.** Este passo só é aplicável para ofertas com gestão de chumbo habilitada.

- **Publicação final (>30 minutos)**

    Garantimos que a sua oferta se torne publicamente disponível no mercado.

Após estes cheques de validação estarem completos, a sua oferta será ao vivo no mercado.

## <a name="next-steps"></a>Passos seguintes

[Aceder a relatórios analíticos para o mercado comercial no Partner Center](partner-center-portal/analytics.md)
