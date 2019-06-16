---
title: Descrição geral de publicação de oferta de módulo do IoT Edge do Azure | O Azure Marketplace
description: Descrição geral do processo de publicação de um módulo do IoT Edge da oferta no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: pabutler
ms.openlocfilehash: 319031ec99d449ea5866bb5234cc617145954173
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942603"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>Descrição geral da publicação da oferta de módulo do IoT Edge

<table> <tr> <td>Esta secção explica como publicar uma nova oferta de módulo do Azure IoT Edge da Microsoft <a href="https://azuremarketplace.microsoft.com">do Azure Marketplace</a>. Módulo do IoT Edge é um contentor de compatível com o Docker tornou-se para ser executada num dispositivo IoT Edge. Módulos do IoT Edge do Azure são a menor unidade de computação gerenciada pelo IoT Edge e podem conter serviços do Azure ou o código de solução personalizada. </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>Processo de publicação

Os passos de alto nível para publicar uma oferta de módulo do IoT Edge são:

1. Criar a oferta<br> Fornece informações detalhadas sobre a oferta. Estas informações incluem: a descrição da oferta, as especificações dos ativos, informações de suporte e materiais de marketing.

2. Criar os recursos técnicos e comerciais<br> Criar os ativos empresariais (documentos legais e materiais de marketing) e ativos técnicos para a solução associada (as imagens de módulo de IoT Edge alojadas no Azure Container Registry.

3. Criar o SKU<br> Crie o SKU (s) associado com a oferta. Um SKU exclusivo é necessário para cada imagem que pretende publicar.

4. Certificar e publicar a oferta <br>Depois da oferta e os ativos técnicos são concluídos, pode enviar a oferta. Esta submissão inicia o processo de publicação. Durante este processo a solução é testada, validado, certified, em seguida, "entrar no ar" no Azure Marketplace.

## <a name="parts-of-an-offer"></a>Partes de uma oferta

Os artigos seguintes cobrem as principais partes de uma oferta de módulo do IoT Edge.

- [Pré-requisitos](./cpp-prerequisites.md) <br>Este artigo lista as técnicas e requisitos de negócio antes de poder criar ou publicar um módulo do IoT Edge oferecem.
- [Preparar o ativos técnicos do módulo do IoT Edge](./cpp-create-technical-assets.md) <br>Este artigo descreve como preparar os recursos técnicos para o módulo do IoT Edge. Estes recursos tem de cumprir todos os critérios de técnicos necessários para o módulo do IoT Edge pode ser publicado no Azure Marketplace.
- [Criar uma oferta de módulo do IoT Edge](./cpp-create-offer.md) <br>Este artigo lista os passos necessários para criar um novo IoT Edge módulo oferta entrada utilizando o [Cloud Partner Portal](https://cloudpartner.azure.com).
- [Publicar uma oferta de módulo do IoT Edge](./cpp-publish-offer.md)<br> Este artigo descreve como submeter a oferta para publicação no Azure Marketplace.

## <a name="next-steps"></a>Passos Seguintes

Reveja os [requisitos técnicos e empresariais](./cpp-prerequisites.md) para a publicação de um módulo do IoT Edge no Microsoft Azure Marketplace.
