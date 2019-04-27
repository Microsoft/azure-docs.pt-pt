---
title: Atualizar a oferta de aplicação SaaS do Azure existente | Documentos da Microsoft
description: Como atualizar uma oferta de aplicação SaaS existente no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: 9d490a0ec09e351e4cee00e7c30c9161bdfac3e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594299"
---
# <a name="update-an-existing-saas-application-offer"></a>Atualizar uma oferta de aplicação SaaS existente

Existem vários tipos de atualizações que pode querer fazer sua oferta depois é foi publicado e está em direto. Qualquer alteração feita para a nova versão da sua oferta deve ser guardada e republicada tê-lo a refletir no Marketplace. Este artigo percorre os diferentes aspectos da atualizar a sua oferta SaaS no [Cloud Partner Portal](https://cloudpartner.azure.com/).

Há vários motivos por que razão poderá querer atualizar a sua oferta, tais como:

- Adicionar uma nova versão a uma aplicação existente.
- A atualizar uma aplicação.
- Acrescentar novas funcionalidades para uma aplicação.
- A atualizar os metadados para a oferta do marketplace.

Para ajudá-lo a essas modificações, o portal fornece o **Compare** e **histórico** funcionalidades.

## <a name="unpermitted-changes-to-a-saas-offer"></a>Alterações unpermitted numa oferta de SaaS

Existem atributos de uma oferta de SaaS que não pode ser alterado depois da oferta está em direto no Azure Marketplace. Não é possível alterar as seguintes definições:

- ID de oferta e o ID de publicador da oferta
- Versão etiquetas, por exemplo: `1.0.1`
- Modelo de faturação/licença é alterado para ofertas existentes.

## <a name="common-update-operations"></a>Operações de atualização comuns
 
As seguintes operações de atualização são comuns.

### <a name="update-offer-contacts"></a>Contactos de oferta de atualização

Utilize os seguintes passos para atualizar os contactos de suporte para a sua oferta.

1. Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).
2. Sob **todas as ofertas**, localize a oferta que pretende atualizar.
3. Vá para o **contactos** separador. Atualize seus contatos.
4. Selecione **publicar** para iniciar o fluxo de trabalho para publicar as alterações.


### <a name="update-offer-marketplace-metadata"></a>Atualizar os metadados do marketplace de oferta

Utilize os seguintes passos para atualizar os metadados de marketplace associados a sua oferta. (Por exemplo: da empresa nome, logótipos, etc.)

1. Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).
2. Sob **todas as ofertas**, localize a oferta que pretende atualizar.
3. Vá para o **detalhes de loja** separador. Utilize as instruções no [publicar SaaS oferecem](./cpp-publish-offer.md) artigo para fazer alterações de metadados.
4. Selecione **publicar** para iniciar o fluxo de trabalho para publicar as alterações.

## <a name="compare-feature"></a>Compare recursos

Quando fizer alterações a uma oferta publicada, pode utilizar a funcionalidade de comparação para auditar as alterações que fez. Captura de ecrã seguinte mostra a opção de comparação para uma oferta publicada.

![Utilizar a comparação para oferecer as alterações no Portal de parceiros da Cloud](./media/saas-offer-compare.png)

### <a name="to-use-the-compare-feature"></a>Para utilizar a funcionalidade de comparação:

1. Em qualquer momento no processo de edição, selecione a comparação para a sua oferta.
2. Ver versões lado a lado dos ativos de marketing e metadados.

## <a name="publishing-history"></a>Histórico de publicação

Para ver a atividade do histórico publicação, selecione o **histórico** separador na navegação à esquerda menu barra de Cloud Portal de parceiros. Pode ver as ações de timestamped tomadas durante o tempo de vida das suas ofertas do Azure Marketplace.

![Ver histórico no Portal de parceiros da Cloud da oferta](./media/saas-offer-history.png)

Pode utilizar a página de histórico de auditoria para procurar uma oferta específica e aplicar filtros como publicador, oferta e o tipo de evento (por exemplo, OfferGoLiveRequested.) Também pode transferir o histórico de auditoria como um ficheiro csv.


## <a name="next-steps"></a>Passos Seguintes

[Oferta da aplicação SaaS](./cpp-saas-offer.md)