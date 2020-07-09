---
title: Reveja e publique uma oferta ao mercado comercial da Microsoft
description: Use partner Center para submeter a sua oferta para pré-visualizar, pré-visualizar a sua oferta e, em seguida, publicá-la no mercado comercial da Microsoft.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 07/05/2020
ms.openlocfilehash: 34e56e5d92526cbf46408c670127e87781e342cd
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119740"
---
# <a name="review-and-publish-an-offer-to-commercial-marketplace"></a>Rever e publicar uma oferta ao mercado comercial

Este artigo mostra-lhe como usar o Partner Center para submeter a sua oferta para pré-visualizar, pré-visualizar a sua oferta e, em seguida, publicá-la no mercado comercial da Microsoft. Também cobrimos como verificar o seu estado de publicação à medida que avança através das escadas de publicação. Já deve ter criado uma oferta que quer publicar.

## <a name="go-to-your-offer-in-commercial-marketplace"></a>Vá à sua oferta no mercado comercial

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
1. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview**.
1. No **separador Visão Geral,** em **Ofertas,** um dos seguintes indicadores de estado é apresentado na coluna **Status** para cada oferta.
 
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

4. Na coluna **'Oferta'** é a seguinte, selecione a oferta que pretende visualizar e publicar.

## <a name="submit-your-offer-to-preview"></a>Submeta a sua oferta para pré-visualização

1. Para submeter a sua oferta para pré-visualização, selecione **'Rever' e publicar** no canto superior direito do portal. Aparece a página **de Revisão e publicação.**
1. Certifique-se de que a coluna **'Estado'** de cada página diz **Complete**. Os três estatutos possíveis são os seguintes:
   - **Não foi iniciado** – A página não foi tocada e precisa de ser concluída.
   - **Incompleto** – Falta informação necessária ou tem erros que precisam de ser corrigidos. Terá de voltar à página e atualizá-la.
   - **Completo** - A página está completa. Todos os dados necessários foram fornecidos e não há erros.
1. Se alguma das páginas tiver um estado diferente do **Completo**, na coluna **Página,** selecione o nome da página, corrija o problema, guarde a página e, em seguida, selecione **'Rever e publicar** novamente para voltar a esta página.'
1. Depois de todas as páginas estarem completas, na **caixa de notas para certificação,** forneça instruções de teste à equipa de certificação para garantir que a sua aplicação seja testada corretamente. Forneça quaisquer notas suplementares úteis para entender a sua aplicação.
1. Para submeter a oferta de publicação, **selecione Publicar**. A página **geral da Oferta** aparece e mostra o estado editorial.

## <a name="validation-and-publishing-steps"></a>Passos de validação e publicação

Depois de selecionar **Publicar,** os processos de validação e publicação prosseguem por ordem. O processo de publicação mais comum é mostrado nesta tabela:

| Fase | O que acontece | 
| ------------ | ------------- | ------------- |
| Validação automatizada | Processamos um conjunto de validações automatizadas. | 
| Certificação | Fazemos validações manuais. | 
| Criação de pré-visualização | A página de listagem para a pré-visualização da sua oferta está disponível para qualquer pessoa que tenha o link de pré-visualização. Se a sua oferta for vendida através da Microsoft (transacionável), apenas o público especificado na página de audiência de **pré-visualização** da sua oferta pode comprar e aceder à oferta para testes. | 
| Assinatura de editor | Enviamos-lhe um e-mail com um pedido para que faça a pré-visualização e aprovação da sua oferta. | 
| Publicar | Fazemos uma série de passos para verificar se a oferta de pré-visualização é publicada em direto para o mercado comercial. | 
|||

## <a name="automated-validation-phase"></a>Fase de validação automatizada

O primeiro passo no processo de publicação é um conjunto de validações automatizadas. Cada passo de validação corresponde a uma funcionalidade que escolheu quando criou a oferta. Cada verificação de validação deve ser completada antes que a oferta possa avançar para o próximo passo no processo de publicação.

- **Oferta configuração do fluxo de compra (<10 min)**

   Neste passo, garantimos que a sua oferta pode ser cumprida quando adquirida pelos clientes através do portal Azure. Este passo só é aplicável para as ofertas que são vendidas através da Microsoft.
- **Validação de dados de test drive (~5 min)**

   Neste passo, validamos os dados que forneceu na página de configuração Técnica da oferta. A funcionalidade de test drive é testada e aprovada. Este passo só é aplicável para ofertas com um test drive ativado.

-   **Provisão de unidade de ensaio (~30 min)**

    Neste passo, depois de validar os dados e funcionalidades do seu test drive no passo anterior, implementamos e replicamos instâncias do seu test drive para que estejam prontos para uso do cliente. Este passo só é aplicável para ofertas com um test drive ativado.

-   **Validação e registo de gestão de chumbo (<15 min)**

    Neste passo, confirmamos que o seu sistema de gestão de chumbo pode receber leads de clientes com base nos detalhes fornecidos na página **de configuração da Oferta.** Este passo só é aplicável para ofertas com gestão de chumbo habilitada.

## <a name="certification-phase"></a>Fase de certificação

Antes de serem publicadas, as ofertas submetidas ao mercado comercial devem ser certificadas. As ofertas submetidas são submetidas a testes rigorosos, alguns manuais automatizados e outros. Reveja as [políticas de certificação do mercado comercial](https://aka.ms/commercial-marketplace-certification-policies) para saber mais.

### <a name="types-of-validation-that-take-place-during-certification"></a>Tipos de validação que ocorrem durante a certificação
Existem três níveis de validação incluídos no processo de certificação para cada oferta submetida.
-   Elegibilidade do negócio dos editores
-   Validação de conteúdos
-   Validação técnica

#### <a name="publisher-business-eligibility"></a>Elegibilidade do negócio dos editores
Cada tipo de oferta verifica um conjunto de critérios de elegibilidade base que o editor deve cumprir. Os critérios de elegibilidade podem incluir o estatuto de MPN do editor, competências detidas, níveis de competência, etc.

#### <a name="content-validation"></a>Validação de conteúdos

A informação inserida quando criou a sua oferta é verificada pela qualidade e relevância. Estes cheques irão rever as suas entradas para os detalhes da listagem do mercado, preços, disponibilidade, planos associados, e assim por diante. Para cumprir os critérios de listagem do Azure Marketplace e do Microsoft AppSource, validaremos que a sua oferta inclui:
-   Um título que descreve com precisão a oferta
-   Descrições bem escritas que fornecem uma visão geral e uma proposta de valor
-   Imagens e vídeos de qualidade
-   Uma explicação de como a oferta utiliza plataformas e ferramentas da Microsoft.

Saiba mais sobre os critérios de validação de conteúdos através da leitura das [políticas gerais de listagem.](https://aka.ms/commercial-marketplace-certification-policies#100-general)

#### <a name="technical-validation"></a>Validação técnica
Durante a validação técnica, a oferta (embalagem ou binária) passa pelas seguintes verificações.
-   Digitalizado para malware
-   Chamadas de rede monitorizadas
-   Pacote analisado
-   Digitalização minuciosa da funcionalidade da oferta

A oferta é testada em várias plataformas e versões para garantir que é robusta.

### <a name="certification-failure-report"></a>Relatório de falha de certificação

Se a sua oferta falhar em qualquer uma das verificações de listagem, técnicas ou políticas, ou se não estiver elegível para submeter uma oferta desse tipo, enviaremos um relatório de falha de certificação para si.

Este relatório contém descrições de quaisquer políticas que falharam, juntamente com as notas de revisão. Reveja este relatório de e-mail, aborde quaisquer problemas, faça atualizações à sua oferta sempre que necessário, e reenvia a oferta usando o portal do [mercado comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Partner Center. Pode reenviar a oferta quantas vezes for necessária até passar a certificação.

## <a name="preview-creation-phase"></a>Fase de pré-visualização da criação

Durante a fase de pré-visualização da criação, criamos uma versão da sua oferta que estará acessível apenas ao público especificado na página de audiência de **pré-visualização** da sua oferta, caso existam. A versão de pré-visualização da sua oferta não estará disponível para ninguém fora do público de pré-visualização até publicar a oferta ao vivo.

> [!NOTE]
> Não utilize o público de pré-visualização para dar visibilidade às pessoas fora da sua organização. Em vez disso, utilize a opção Oferta Privada. Neste momento, a sua oferta não foi totalmente testada e validada, e não está pronta para distribuição externa. 

## <a name="publisher-signoff-phase"></a>Fase de aprovação de editores

Quando a oferta estiver pronta para que você reveja e assine, enviaremos um e-mail para solicitar que reveja e aprove a sua pré-visualização da oferta. Também pode atualizar a página geral da **Oferta** no seu navegador para ver se a sua oferta chegou à fase de assinatura do Publisher. Se tiver, o botão **Go ao vivo** e os links de pré-visualização estarão disponíveis.

A imagem que se segue mostra a página **geral da Oferta** para uma oferta SaaS. Os passos de validação que verá nesta página variam consoante o tipo de oferta e as seleções que fez quando criou a oferta.

![Ilustra a página geral da Oferta para uma oferta no Partner Center. São mostrados os botões Go ao vivo e os links de pré-visualização.](./partner-center-portal/media/publish-status-publisher-signoff.png)

**Para pré-visualizar a sua oferta e assinar**
1. Na página **'Visão geral' da Oferta,** para pré-visualizar a sua oferta, selecione o link no botão **Go live.**
   > [!NOTE]
   > Haverá um link para a pré-visualização do AppSource, pré-visualização do Azure Marketplace ou ambos dependendo das opções que escolheu ao criar a sua oferta. Se optar por vender a sua oferta através da Microsoft, qualquer pessoa que tenha sido adicionada ao público de pré-visualização pode testar a aquisição e implementação da sua oferta para garantir que ela cumpre os seus requisitos durante esta fase.

1. Se quiser fazer alterações após a pré-visualização da oferta, pode editar e reenviar para publicar uma nova pré-visualização. Para mais informações, consulte [Atualizar uma oferta existente no mercado comercial.](./partner-center-portal/update-existing-offer.md)

1. Depois de aprovar a sua pré-visualização, para publicar a sua oferta ao vivo no mercado comercial, selecione **Go live**.
   > [!TIP]
   > Se a sua oferta já estiver ao vivo e disponível para o público no mercado, quaisquer atualizações que faça não serão transmitidas ao vivo até que selecione **Go live**.

## <a name="publish-phase"></a>Fase de publicação

Agora que escolheu ir ao vivo com a sua oferta, disponibilizando-a no mercado comercial, existem uma série de verificações de validação final que fazemos para garantir que a oferta ao vivo está configurada tal como a versão de pré-visualização da oferta.

-   **Oferta configuração do fluxo de compra (>10 min)**

    Neste passo, garantimos que a sua oferta pode ser cumprida quando adquirida pelos clientes através do portal Azure. Este passo só é aplicável para as ofertas que são vendidas através da Microsoft.
-   **Validação de dados de test drive (~5 min)**

    Neste passo, validamos os dados que forneceu na página de configuração Técnica da oferta. A funcionalidade de test drive é testada e aprovada. Este passo só é aplicável para ofertas com um test drive ativado.

-   **Provisão de unidade de ensaio (~30 min)**

      Neste passo, implementamos e replicamos instâncias do seu test drive para que estejam prontos para uso do cliente. Este passo só é aplicável para ofertas com um test drive ativado.
-   **Validação e registo de gestão de chumbo (>15 min)**

    Neste passo, confirmamos que o seu sistema de gestão de chumbo pode receber leads de clientes com base nos detalhes fornecidos na página de **configuração** da Oferta da oferta. Este passo só é aplicável para ofertas com gestão de chumbo habilitada.

-   **Publicação final (>30 minutos)**

    Neste passo, garantimos que a sua oferta se torne publicamente disponível no mercado.

Depois destes cheques de validação concluídos, a sua oferta será ao vivo no mercado.

## <a name="next-step"></a>Passo seguinte

[Aceder a relatórios analíticos para o mercado comercial no Partner Center](./partner-center-portal/analytics.md)