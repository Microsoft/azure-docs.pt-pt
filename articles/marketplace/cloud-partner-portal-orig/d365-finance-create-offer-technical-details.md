---
title: Como preencher o formulário de informações técnicas
description: Explica como introduza os valores para do formulário de informações técnicas para uma nova aplicação do Dynamics 365 Business Central.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: dbc38fab5bd8e55f6dd280ecc46af1b1a5ae7ede
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935070"
---
<a name="how-to-fill-out-the-technical-info-form"></a>Como preencher o formulário de informações técnicas
===========================================

1.  Na **escolha o tipo de aplicação** secção, carregar o ficheiro de pacote de extensão (. App) e quaisquer ficheiros de pacote de extensão sua extensão tem uma dependência no.

    ![Informações de pacote de aplicação](./media/d365-financials/image015.png)

-   **Ficheiro de pacote de extensões** - necessária - o ficheiro de pacote de extensão (. App).

-   **Ficheiro de pacote de dependência** – obrigatório se a aplicação tem uma dependência no outro aplicativo publicado no AppSource. Este ficheiro. App de uma extensão já publicada no AppSource, o qual depende a aplicação atual. 

-   **Ficheiro de pacote de biblioteca** -necessário se a aplicação tem uma dependência sobre outra aplicação que está *não* publicado no AppSource. Este arquivo. App de uma aplicação existente, mas uma que não foi e não será publicado no AppSource.

-   **Automação de teste do aplicativo** - necessária - o pacote de teste codificado do VS que tem de criar para testes de extensões automatizados.

1. Na **informações adicionais para a extensão** secção, carregar informações adicionais para sua extensão. Estas informações são utilizadas durante a validação.

   ![Informações adicionais para o formulário de extensão de aplicação](./media/d365-financials/image016.png)


-   **URL para a documentação do produto** - necessária - URL para a documentação para a extensão.

-   **Cenários de utilização de chave** - necessária - um documento que lista os detalhes de configuração e utilização de passo a passo para a extensão. Um exemplo pode ser encontrado no artigo [documentação do cenário de utilizador](https://docs.microsoft.com/dynamics-nav/compliance/apptest-userscenario/).

-   **Versão de destino** -necessária - selecione a versão em que pretende implementar a aplicação. Selecione **atual** para implementar na atual versão do mercado. Selecione **menor junto** para implementar com a próxima versão secundária a ser lançado. Selecione **principais a seguir** para implementar com a próxima versão principal a ser lançado.

-   **Requer o Premium SKU** -- Nepovinné – selecione o Premium botão se a aplicação requer o SKU Premium. Gestão de serviço e de produção estão disponíveis apenas em premium. Informações detalhadas sobre o Essential vs Premium pode ser encontrado no artigo [alterar que funcionalidades são apresentadas](https://docs.microsoft.com/dynamics365/financials/ui-experiences).

-   **Explicação para erros de análise de código** -- Nepovinné - documento que lista e justifica qualquer código que não cumpre os requisitos.

-   **Explicação da principal funcionalidade afetada** -- Nepovinné - documento que lista e explica qualquer funcionalidade principal que é limitada pela extensão de.

-   **Contas de teste** contas de utilizador-- Nepovinné – para serviços remotos, web sites, etc., que serão necessários para concluir o teste de utilização de ponto a ponto.

-   **Exceções de requisitos de experiência do Usuário** -- Nepovinné - documento que lista e justifica quaisquer requisitos de experiência de utilizador não é cumpridos à extensão.

A próxima etapa é adicionar vitrine detalhes da sua oferta.
