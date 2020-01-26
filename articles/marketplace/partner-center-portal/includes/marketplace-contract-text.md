---
title: incluir ficheiro
description: incluir ficheiro de texto para contrato padrão de mercado comercial da Microsoft
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: ChJenk
manager: evansma
editor: ''
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/22/2020
ms.author: v-chjen
ms.custom: include file
ms.openlocfilehash: 76603ef30cefa14018a7c6b976eb8541b9b608f4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76760908"
---
A Microsoft fornece um modelo de contrato padrão para o mercado comercial.

- **Usar o contrato padrão para o Marketplace comercial da Microsoft?**

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para fornecedores de software, a Microsoft oferece um contrato padrão para o Microsoft Commercial Marketplace para ajudar a facilitar as transações no Marketplace. Em vez de criar termos e condições personalizados, os editores de mercado comercial podem optar por oferecer seu software sob o contrato padrão, que os clientes precisam apenas examinaremos e aceitar uma vez. O contrato padrão pode ser encontrado aqui: https://go.microsoft.com/fwlink/?linkid=2041178.

Você pode optar por usar o contrato padrão em vez de fornecer seus próprios termos e condições personalizados selecionando a caixa de seleção "usar o contrato padrão para o Marketplace comercial".

![Usando a caixa de seleção de contrato padrão](./media/use-standard-contract.png)

> [!NOTE]
> Depois de publicar uma oferta usando o contrato padrão do Microsoft Commercial Marketplace, você não poderá usar seus próprios termos e condições personalizados. É um cenário de "ou". Você pode oferecer sua solução sob o contrato Standard **ou** seus próprios termos e condições. Se você quiser modificar os termos do contrato padrão, poderá fazer isso por meio de emendas de contrato padrão.

**Alterações-padrão do contrato**

As emendas de contrato padrão permitem que os editores selecionem os termos de contrato padrão para simplificar e personalizar os termos para seus produtos ou negócios. Os clientes precisam apenas examinar as emendas ao contrato, caso já tenham revisado e aceito o contrato padrão da Microsoft.

Há dois tipos de emendas disponíveis para editores de mercado comercial:

- Emendas universais: essas emendas são aplicadas universalmente ao contrato padrão para todos os clientes. As emendas universais são mostradas a todos os clientes da oferta no fluxo de compra. Os clientes devem aceitar os termos do contrato padrão e o aditamento antes de poderem usar sua oferta.
- Emendas personalizadas: essas emendas são emendas especiais ao contrato padrão que são direcionados a clientes específicos somente por meio de IDs de locatário do Azure. Os editores podem escolher o locatário que desejam direcionar. Somente os clientes do locatário serão apresentados com os termos personalizados de emenda no fluxo de compra da oferta.  Os clientes devem aceitar os termos do contrato padrão e as emendas antes de poderem usar sua oferta.

>[!NOTE]
> Esses dois tipos de emendas se empilham um sobre o outro. Os clientes destinados a emendas personalizadas também terão a emenda universal ao contrato padrão durante a compra.

**Termos de emenda universal para o contrato padrão do Marketplace comercial da Microsoft**: Insira os termos de emenda universal nesta caixa. Você pode fornecer uma única emenda universal por oferta. Você pode inserir um número ilimitado de caracteres nesta caixa. Esses termos são exibidos para clientes no AppSource, no Azure Marketplace e/ou portal do Azure durante a descoberta e o fluxo de compra.

**Termos personalizados de emenda para o contrato padrão do Marketplace comercial da Microsoft**: comece selecionando **Adicionar termos de emenda personalizada**. Você pode fornecer até 10 termos personalizados de emenda por oferta.

- **Termos personalizados de emenda**: insira seus termos de emenda personalizada na caixa termos personalizados de emenda. Você pode inserir um número ilimitado de caracteres nesta caixa. Somente os clientes das IDs de locatário que você especificar para esses termos personalizados serão apresentados com os termos personalizados de emenda no fluxo de compra da oferta no portal do Azure.  
- **IDs de locatário** (obrigatório): cada alteração personalizada pode ser direcionada para até 20 IDs de locatário. Se você adicionar uma emenda personalizada, deverá fornecer pelo menos uma ID de locatário. A ID de locatário identifica seu cliente no Azure. Você pode solicitar que o cliente tenha essa ID e pode encontrá-la navegando até portal.azure.com > Azure Active Directory Propriedades de >. O valor da ID de diretório é a ID do locatário (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode pesquisar a ID de locatário da organização de seu cliente usando a URL do nome de domínio em [qual Microsoft Azure é a ID do locatário do Office 365 e do Microsoft?](https://www.whatismytenantid.com).
- **Descrição** (opcional): opcionalmente, forneça uma descrição amigável para a ID do locatário que ajuda a identificar o cliente que você está direcionando para o aditamento.

**Termos e condições**

Se você quiser fornecer seus próprios termos e condições personalizados, poderá optar por inseri-los no campo termos e condições. Você pode inserir até 10.000 caracteres de texto neste campo. Se seus termos e condições exigirem uma descrição mais longa, insira um link de URL único nesse campo, onde os termos e condições podem ser encontrados. Ele será exibido aos clientes como um link ativo.

Os clientes precisam aceitar esses termos antes de tentarem sua oferta.

Lembre-se de **salvar** antes de passar para a próxima seção.
Lembre-se de **salvar** antes de passar para a próxima seção.
