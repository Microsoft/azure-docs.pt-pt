---
title: Validação funcional de uma oferta de Finanças e Operações AppSource Dynamics 365 no Azure Marketplace.
description: Como validar funcionalmente uma oferta de Finanças e Operações Dynamics 365 no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: emuench
ms.author: navits
ms.date: 07/17/2020
ms.openlocfilehash: 9be90cdac742a581c6346f923f44e769c8a70f76
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102613634"
---
# <a name="appsource-dynamics-365-finance-and-operations-functional-validation"></a>AppSource Dynamics 365 Validação funcional de Finanças e Operações

Para completar uma primeira publicação no [Partner Center,](https://partner.microsoft.com/dashboard/home)as ofertas para a Dynamics 365 Finance and Operations requerem duas validações funcionais:

- Faça upload de um vídeo de demonstração do ambiente Dynamics 365 que mostra funcionalidade básica.
- Imagens presentes que demonstram o ambiente de [Serviços](https://lcs.dynamics.com/) de Ciclo de Vida (LCS) da solução.

> [!NOTE]
> As publicações de recertificação subsequentes não requerem demonstração. Para saber mais, consulte o [documento da Política AppSource.](/legal/marketplace/certification-policies#1440-dynamics-365-finance-ops)

## <a name="how-to-validate"></a>Como validar

Existem duas opções para validação funcional:

- Realize uma conferência de 30 minutos conosco durante o horário de trabalho do Pacific Standard (PST) para demonstrar e registar o ambiente e a solução [LCS,](https://lcs.dynamics.com/) ou
- No Partner Center, vá ao [Commercial Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)  >  **Overview** e faça upload de um URL de demonstração e imagens de LCS no separador conteúdo suplementar da oferta.

A equipa de certificação da Microsoft analisa o vídeo e os ficheiros, e depois aprova a solução ou envia-lhe e-mails sobre os próximos passos.

### <a name="option-1-30-minute-conference-call"></a>Opção 1: chamada de conferência de 30 minutos

Para agendar uma chamada final de revisão, contacte [appsourceCRM@microsoft.com](mailto:appsourceCRM@microsoft.com) com o nome da sua oferta e algumas faixas horárias entre as 8:00 e as 17:00 horas do Pacífico.

### <a name="option-2-upload-a-demo-video-and-lcs-screenshots"></a>Opção 2: Carregar um vídeo de demonstração e imagens LCS

1. Grave um vídeo e faça o upload do endereço para o local de hospedagem à sua escolha. Siga estas orientações:

    - Visível pela equipa de certificação da Microsoft.
    - Menos de 20 minutos de duração.
    - Inclui até três destaques de funcionalidades fundamentais da sua solução no ambiente Dynamics 365.

    > [!NOTE]
    > É aceitável utilizar um vídeo de marketing existente se cumprir as diretrizes.

2. Tome as seguintes imagens do ambiente [LCS](https://lcs.dynamics.com/) que correspondam à oferta ou solução que pretende publicar. Devem ser suficientemente claros para que a equipa de certificação leia o texto. Guarde as imagens como ficheiros JPG. Pode fornecer [appSourceCRM@microsoft.com](mailto:appSourceCRM@microsoft.com) permissão ao seu ambiente LCS para que possamos verificar a configuração em vez de fornecer imagens.

    1. Vá à biblioteca **do**  >  Projeto **Modelador de Processos de Negócio** LCS.  >   Tire as imagens de todos os passos do Processo. Inclua os **diagramas** e colunas **revistas,** como mostrado aqui:

       :::image type="content" source="media/dynamics-365-finance-operations/project-library.png" alt-text="Mostra a janela da biblioteca do projeto.":::

      2. Vá ao **Pacote** de  >  Solução **de Gestão de**  >  **Soluções** LCS. Veja as imagens que incluem a visão geral do pacote e os conteúdos apresentados nestes exemplos:

    | Campo | Imagem |
    | --- | --- |
    | Descrição geral do pacote | [![Screenshot que mostra a janela "Package overview".](media/dynamics-365-finance-operations/package-overview-45.png)](media/dynamics-365-finance-operations/package-overview.png#lightbox) |
    | <ul><li>Aprovadores de soluções</li></ul> | [![Ecrã geral do pacote](media/dynamics-365-finance-operations/solution-approvers-45.png)](media/dynamics-365-finance-operations/solution-approvers.png#lightbox) |
    | Conteúdo do pacote<ul><li>Modelação</li><li>Pacote implementável de software</li></ul> | [![Ecrã de conteúdo de pacote um](media/dynamics-365-finance-operations/package-contents-1-45.png)](media/dynamics-365-finance-operations/package-contents-1.png#lightbox) |
    | <ul><li>Configuração GER</li><li>Backup de base de dados</li></ul><br>Os artefactos não são necessários na secção **de configuração GER.** | [![Ecrã de conteúdo de pacote dois](media/dynamics-365-finance-operations/package-contents-2-45.png)](media/dynamics-365-finance-operations/package-contents-2.png#lightbox) |
    | <ul><li>Modelo de relatório de bi de potência</li><li>Artefacto BPM</li></ul><br>Os artefactos não são necessários na secção **Power BI.** | [![Ecrã de conteúdo do pacote três](media/dynamics-365-finance-operations/package-contents-3-45.png)](media/dynamics-365-finance-operations/package-contents-3.png#lightbox) |
    | <ul><li>Pacote de dados de processo</li><li>Contrato de licença de solução e política de privacidade</li></ul><br>As **secções de modelo ger e** power bi **report** são opcionais para incluir para ofertas de Finanças e Operações. | [![Ecrã de conteúdo de pacote quatro](media/dynamics-365-finance-operations/package-contents-4-45.png)](media/dynamics-365-finance-operations/package-contents-4.png#lightbox) |

    Para saber mais sobre cada secção do portal LCS, consulte o Guia do Utilizador do [LCS.](/dynamics365/fin-ops-core/dev-itpro/lifecycle-services/lcs-user-guide)

3. Faça upload para o Centro de Parceiros.

    1. Crie um documento de texto que inclua o endereço de vídeo de demonstração e imagens, ou guarde as imagens como ficheiros JPG separados.
    2. Adicione o texto e quaisquer ficheiros JPG a um ficheiro .zip no [Mercado Comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) no Centro de Parceiros no separador de **Conteúdo Suplementar** da sua oferta de Finanças e Operações.

    [![Mostra a janela da biblioteca do projeto](media/dynamics-365-finance-operations/supplemental-content.png)](media/dynamics-365-finance-operations/supplemental-content.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

Para aprender a criar uma oferta, consulte: [Criar uma oferta Dynamics 365 for Operations.](./partner-center-portal/create-new-operations-offer.md)