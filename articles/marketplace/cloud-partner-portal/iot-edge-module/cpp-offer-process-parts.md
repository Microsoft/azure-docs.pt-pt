---
title: Visão geral de publicação de oferta de módulo de Azure IoT Edge | Azure Marketplace
description: Visão geral do processo de publicação de uma oferta de módulo IoT Edge no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: pabutler
ms.openlocfilehash: 97df9a61d15e0d90e81f42cef327aea23873ffa0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814326"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>Visão geral de publicação de oferta de módulo de IoT Edge

<table> <tr> <td>Esta seção explica como publicar uma nova oferta de módulo Azure IoT Edge no Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>. Um módulo IoT Edge é um contêiner compatível com o Docker que é feito para ser executado em um dispositivo IoT Edge. Os módulos Azure IoT Edge são a menor unidade de computação gerenciada pelo IoT Edge e podem conter serviços do Azure ou código de solução personalizada. </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>Processo de publicação

As etapas de alto nível para a publicação de uma oferta de módulo IoT Edge são:

1. Criar a oferta<br> Forneça informações detalhadas sobre a oferta. Essas informações incluem: a descrição da oferta, materiais de marketing, informações de suporte e especificações de ativos.

2. Crie os ativos comerciais e técnicos<br> Crie os ativos de negócios (documentos legais e materiais de marketing) e ativos técnicos para a solução associada (as imagens de módulo IoT Edge hospedadas em um registro de contêiner do Azure.

3. Criar a SKU<br> Crie as SKUs associadas à oferta. Um SKU exclusivo é necessário para cada imagem que você pretende publicar.

4. Certificar e publicar a oferta <br>Depois que a oferta e os ativos técnicos forem concluídos, você poderá enviar a oferta. Esse envio inicia o processo de publicação. Durante esse processo, a solução é testada, validada, certificada e, em seguida, "fica ativa" no Azure Marketplace.

## <a name="parts-of-an-offer"></a>Partes de uma oferta

Os artigos a seguir abrangem as principais partes de uma oferta de módulo IoT Edge.

- [Pré-requisitos](./cpp-prerequisites.md) <br>Este artigo lista os requisitos técnicos e de negócios antes que você possa criar ou publicar uma oferta de módulo IoT Edge.
- [Preparar os ativos técnicos do módulo IoT Edge](./cpp-create-technical-assets.md) <br>Este artigo descreve como preparar os ativos técnicos para um módulo IoT Edge. Esses ativos devem atender a todos os critérios técnicos necessários antes que o módulo IoT Edge possa ser publicado no Azure Marketplace.
- [Criar uma oferta de módulo do IoT Edge](./cpp-create-offer.md) <br>Este artigo lista as etapas necessárias para criar uma nova entrada de oferta de módulo IoT Edge usando o [portal do Cloud Partner](https://cloudpartner.azure.com).
- [Publicar uma oferta de módulo do IoT Edge](./cpp-publish-offer.md)<br> Este artigo descreve como enviar a oferta para publicação no Azure Marketplace.

## <a name="next-steps"></a>Passos seguintes

Examine os [requisitos técnicos e de negócios](./cpp-prerequisites.md) para publicar um módulo de IoT Edge no Microsoft Azure Marketplace.
