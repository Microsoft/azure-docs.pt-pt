---
title: Como rever e publicar uma oferta ao mercado comercial da Microsoft
description: Use partner Center para submeter a sua oferta para pré-visualizar, pré-visualizar a sua oferta e, em seguida, publicá-la no mercado comercial da Microsoft.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/27/2020
ms.openlocfilehash: b038834df3889a8b4b7f4a749568635e99f8408e
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129580"
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

Se alguma das páginas tiver um estado diferente do **Completo,** tem de corrigir o problema nessa página e, em seguida, voltar à página **de Revisão e publicar** para confirmar que o estado agora mostra como **Completo** . Alguns tipos de oferta requerem testes. Em caso afirmativo, verá um campo **de Notas para certificação** onde precisa de fornecer instruções de teste à equipa de certificação e quaisquer notas suplementares úteis para a compreensão da sua app.

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

As ofertas submetidas ao mercado comercial devem ser certificadas antes de serem publicadas. As ofertas são submetidas a testes rigorosos, alguns manuais automatizados e outros. Para saber mais, consulte [as políticas de certificação do mercado comercial.](/legal/marketplace/certification-policies)

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

Saiba mais sobre os critérios de validação de conteúdos através da leitura das [políticas gerais de listagem.](/legal/marketplace/certification-policies#100-general)

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

## <a name="cancel-publishing"></a>Cancelar publicação

Para cancelar uma oferta com a **Publicação em curso:**

1. Selecione o nome da oferta para abrir a página **geral da Oferta.**
1. Selecione **Cancelar a publicação** do canto superior direito da página.
1. Confirme que deseja impedir que a oferta seja publicada.

Se quiser publicar a oferta mais tarde, terá de começar o processo de publicação.

> [!NOTE]
> Só pode impedir que uma oferta seja publicada se a oferta ainda não tiver progredido para que a editora assine o passo. Depois de selecionar **Go ao vivo,** não terá a opção de cancelar a publicação por mais tempo.

## <a name="publisher-sign-off-phase"></a>Fase de aprovação da editora

Quando a oferta estiver pronta para que você reveja e seja assinado, enviaremos um e-mail para solicitar que reveja e aprove a sua pré-visualização da oferta. Também pode atualizar a página geral da **Oferta** no seu navegador para ver se a sua oferta chegou à fase de desinsusitada do Publisher. Se tiver, o botão **Go ao vivo** e os links de pré-visualização estarão disponíveis.

A imagem que se segue mostra a página **geral da Oferta** para uma oferta SaaS. Os passos de validação que verá nesta página variam consoante o tipo de oferta e as seleções que fez quando criou a oferta.

![Ilustra a página geral da Oferta para uma oferta no Partner Center. São mostrados os botões Go ao vivo e os links de pré-visualização.](./media/publish-status-publisher-signoff.png)

### <a name="previewing-and-approving-your-offer"></a>Visualização e aprovação da sua oferta

> [!IMPORTANT]
> Para validar o fluxo de compra e configuração de ponta a ponta, compre a sua oferta enquanto estiver em Pré-Visualização. Primeiro, notifique a Microsoft com um [bilhete de apoio](https://aka.ms/marketplacesupport) para garantir que não processamos uma taxa.

Na página **'Geral' da Oferta,** verá links de pré-visualização no botão **Go live.** Haverá um link para a pré-visualização do AppSource, pré-visualização do Azure Marketplace ou ambos dependendo das opções que escolheu ao criar a sua oferta. Se optar por vender a sua oferta através da Microsoft, qualquer pessoa que tenha sido adicionada ao público de pré-visualização pode testar a aquisição e implementação da sua oferta para garantir que ela cumpre os seus requisitos durante esta fase.

Depois de aprovar a sua pré-visualização, selecione **Go live** para publicar a sua oferta ao vivo no mercado comercial. 

Se quiser fazer alterações após a pré-visualização da oferta, pode editar e reenviar o seu pedido de publicação. Se a sua oferta já estiver ao vivo e disponível para o público no mercado, quaisquer atualizações que faça não serão transmitidas ao vivo até que escolha * *Vá ao vivo* . Para mais informações, consulte [Atualizar uma oferta existente no mercado comercial](partner-center-portal/update-existing-offer.md)

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

## <a name="publishing-history"></a>História da publicação

A página **História** no Partner Center mostra os eventos de publicação das suas ofertas de marketplace comercial. Para cada evento, a página exibe o utilizador que iniciou a ação, o tipo de evento e a data e hora do evento. As [etapas de validação e publicação são](#validation-and-publishing-steps) listadas com a data e hora de conclusão.

Para ver a história da sua oferta:

1.    Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2.    No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview** .
3.    Selecione a sua oferta.
4.    Selecione o **separador Histórico** no menu de navegação à esquerda. A página fornece detalhes para os seguintes eventos, conforme aplicável à sua oferta:

|Evento    |Description    |
|---------|---------------|
|Oferta criada    |A oferta foi criada no Partner Center. Um utilizador selecionou o tipo de oferta, oferece iD e oferece um pseudónimo na Visão Geral **do Mercado Comercial.**  >  **Overview**    |
|Plano criado: *nome do plano*    |Um utilizador criou um novo plano ao introduzir o ID do plano e o nome do plano no separador **Plan overview.**</br>*Este evento aplica-se apenas para oferecer tipos que suportem planos.*    |
|Plano eliminado    |Um utilizador eliminou um projeto de plano que não tinha sido publicado selecionando **o projeto de eliminação** da página **de visão geral** do Plano.</br>*Este evento aplica-se apenas para oferecer tipos que suportem planos.*    |
|Plano iniciado parar de vender: *nome do plano*    |Um utilizador iniciou um plano de stop-sell selecionando Parar de **vender** na página **de visão geral** do Plano.</br>*Este evento aplica-se apenas para oferecer tipos que suportem planos.*    |
|Desfazer plano parar de vender: *nome do plano*    |Um utilizador cancelou um plano de stop-sell selecionando **undo para parar** de vender a partir da página **de visão geral** do Plano.</br>*Este evento aplica-se apenas para oferecer tipos que suportem planos.*    |
|Proposta submetida para pré-visualização    |Um utilizador submeteu a oferta para pré-visualização selecionando **Publicar** a partir da página **de Revisão e publicação.**    |
|Iniciado submeter para cancelamento de pré-visualização    |Um utilizador solicitou que cancelasse a publicação da oferta para pré-visualização, selecionando **a publicação de Cancel a** partir da página geral da **Oferta** após a submissão à pré-visualização.</br>*Este evento é apresentado à medida que o pedido de cancelamento está a ser processado.*    |
|Submissão cancelada para pré-visualização    |Um utilizador cancelou a publicação da oferta para pré-visualização selecionando **a publicação de Cancel a** partir da página geral da **Oferta** após a submissão à pré-visualização.</br>*Este evento é apresentado após o pedido de cancelamento ser processado com sucesso.*    |
|Sign-off para ir ao vivo    |Um utilizador publicou a oferta para o mercado comercial selecionando **Go ao vivo** a partir da página geral da **Oferta.**    |
|Iniciada publicação para cancelamento de mercado    |Um utilizador solicitou o cancelamento da publicação da oferta selecionando a **publicação cancele** a partir da página geral da **Oferta** após a assinatura para entrar em direto.</br>*Este evento é apresentado à medida que o pedido de cancelamento está a ser processado.*    |
|Publicação cancelada no mercado comercial    |Um utilizador cancelou a publicação da oferta selecionando a **publicação cancele** a partir da página geral da **Oferta** após a assinatura para entrar em direto.</br>*Este evento é apresentado após o pedido de cancelamento ser processado com sucesso.*    |
|Sincronizar audiência privada    |Um utilizador atualizou e sincronizou o público privado selecionando o **público privado Sync** a partir da página de **visão geral** do Plano ou da página **de preços do Plano &.**</br>*Este evento aplica-se apenas para oferecer tipos que suportem planos privados.*    |
|Pare de vender oferta    |Um utilizador deixou de vender a oferta selecionando Parar de **vender** na página **de visão geral** da Oferta.    |

> [!NOTE]
> A página de História não diz quando um rascunho de oferta foi salvo.

### <a name="filter-options"></a>Opções de filtragem

Pode utilizar filtros para reduzir todo o histórico da sua oferta a eventos de publicação específicos:

1.    Selecione o botão do filtro no canto superior direito da página.
2.    Escolha um filtro e, em seguida, **selecione Aplicar** para ver quais os eventos de história que correspondem aos critérios selecionados.
3.    Selecione **Filtros Clear** para voltar ao histórico completo da sua oferta.

Há quatro filtros:
* Eventos
* Utilizadores
* Date
* Páginas

Ao escolher o filtro **Páginas,** pode selecionar qualquer uma das páginas do Centro parceiro que sejam aplicáveis ao seu tipo de oferta. Quando aplicado, o filtro **Páginas** mostra toda a **oferta submetida a eventos de pré-visualização** com alterações na página selecionada.

* Para todas as ofertas, a página **de configuração da Oferta** está incluída por padrão para cada evento de submissão.
* Para ofertas que suportam planos, a página **de visão geral** do Plano está incluída para cada evento de submissão.
* Para ofertas que suportam a unidade de teste, a página **test drive** está incluída para cada evento de submissão.

### <a name="users"></a>Utilizadores

Se um evento foi iniciado por um utilizador, a página Histórico mostrará o utilizador de acordo com os seguintes cenários:

#### <a name="the-event-was-initiated-by-the-publisher"></a>O evento foi iniciado pela editora

Os utilizadores com permissões de publicação para uma oferta terão o seu nome apresentado para os eventos de publicação que iniciam.

[![A página Histórico apresenta o nome dos utilizadores com permissões de publicação.](./media/review-publish-offer/event-initiated-by-publisher.png)](./media/review-publish-offer/event-initiated-by-publisher.png#lightbox)

#### <a name="the-event-was-initiated-by-microsoft"></a>O evento foi iniciado pela Microsoft

Pode dar permissões aos administradores da Microsoft para iniciarem ações em seu nome ou tomarem ações corretivas após um erro inesperado do sistema. O nome e o logótipo da Microsoft são apresentados para a publicação de eventos iniciados pela Microsoft em nome da sua conta.

[![Exemplo de como a página De Histórico exibe eventos iniciados pela Microsoft.](./media/review-publish-offer/event-initiated-by-microsoft.png)](./media/review-publish-offer/event-initiated-by-microsoft.png#lightbox)

#### <a name="the-event-was-initiated-by-an-unidentified-user"></a>O evento foi iniciado por um utilizador não identificado

Os utilizadores que já não estejam associados a uma conta terão o seu nome removido da coluna **Iniciada** após a retirada das suas permissões de publicação.

[![Exemplo de como a página Histórico exibe eventos iniciados por utilizadores não identificados.](./media/review-publish-offer/event-initiated-by-unidentified-user.png)](./media/review-publish-offer/event-initiated-by-unidentified-user.png#lightbox)

#### <a name="validation-and-publishing-steps"></a>Passos de validação e publicação

Nenhum utilizador é apresentado para processos do sistema que correspondam a [etapas de validação e publicação](#validation-and-publishing-steps). Estes eventos são codificados por cores de acordo com o estado de conclusão do evento.

[![Exemplo de como a página De Histórico apresenta passos de validação e publicação.](./media/review-publish-offer/validation-and-publishing-event.png)](./media/review-publish-offer/validation-and-publishing-event.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

[Aceder a relatórios analíticos para o mercado comercial no Partner Center](partner-center-portal/analytics.md)