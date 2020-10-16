---
title: ficheiro de inclusão
description: incluir arquivo de texto para contrato padrão de mercado comercial da Microsoft
documentationcenter: partner-center-commercial-marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 01/22/2020
ms.custom: include file
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: e1119cf5c40cdcb1fd598b85410ea9149a7cc146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91401108"
---
A Microsoft fornece um modelo de contrato padrão para o mercado comercial.

- **Utilizar o Contrato Padrão para o mercado comercial da Microsoft?**

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal dos fornecedores de software, a Microsoft oferece um Contrato Standard para o mercado comercial da Microsoft para ajudar a facilitar transações no mercado. Em vez de criarem termos e condições personalizados, os editores de marketplace comercial podem optar por oferecer o seu software ao abrigo do Contrato Padrão, que os clientes só precisam de vetar e aceitar uma vez. O Contrato Padrão pode ser consultado aqui: https://go.microsoft.com/fwlink/?linkid=2041178 .

Pode selecionar para utilizar o Contrato Padrão em vez de fornecer os seus próprios termos e condições personalizados selecionando a caixa de verificação "Use o Contrato Padrão para o mercado comercial".

![Utilização da caixa de verificação Standard Contract](./media/use-standard-contract.png)

> [!NOTE]
> Uma vez que publique uma oferta usando o contrato Standard para o mercado comercial da Microsoft, não poderá utilizar os seus próprios termos e condições personalizados. É um cenário de "ou". Ou oferece a sua solução nos termos **e** condições padrão. Se quiser modificar os termos do Contrato Padrão, pode fazê-lo através de Alterações Padrão contratuais.

**Alterações padrão do contrato**

As Alterações Padrão do Contrato permitem que os editores selecionem os termos do Contrato Padrão para a simplicidade e personalizem os termos para o seu produto ou negócio. Os clientes só precisam de rever as alterações ao contrato, caso já tenham revisto e aceite o Contrato Padrão da Microsoft.

Existem dois tipos de alterações disponíveis para os editores de mercado comercial:

- Alterações universais: Estas alterações são aplicadas universalmente ao Contrato-Tipo para todos os clientes. As alterações universais são mostradas a todos os clientes da oferta no fluxo de compra. Os clientes devem aceitar os termos do Contrato Padrão e a alteração antes de poderem utilizar a sua oferta.
- Alterações personalizadas: Estas alterações são alterações especiais ao Contrato Padrão que são dirigidas a clientes específicos apenas através de IDs de inquilinos Azure. Os editores podem escolher o inquilino que querem atingir. Apenas os clientes do arrendatário serão apresentados com os termos de alteração personalizados no fluxo de compra da oferta.  Os clientes devem aceitar os termos do Contrato Padrão e as alterações antes de poderem utilizar a sua oferta.

>[!NOTE]
> Estes dois tipos de emendas empilham-se em cima uns dos outros. Os clientes visados com alterações personalizadas também receberão a alteração universal ao Contrato Padrão durante a compra.

Termos de alteração universal **ao Contrato Padrão para o mercado comercial da Microsoft**: Introduza os termos de emenda universal nesta caixa. Pode fornecer uma única emenda universal por oferta. Pode introduzir um número ilimitado de caracteres nesta caixa. Estes termos são apresentados aos clientes no AppSource, Azure Marketplace e/ou portal Azure durante o fluxo de descoberta e compra.

**Termos de alteração personalizada ao Contrato Padrão para o mercado comercial da Microsoft**: Comece por selecionar os termos de alteração **personalizada**. Pode fornecer até 10 termos de emenda personalizados por oferta.

- **Termos de emenda personalizados**: Introduza os termos de emenda personalizados na caixa de termos de emenda personalizada. Pode introduzir um número ilimitado de caracteres nesta caixa. Apenas os clientes dos IDs do inquilino que especifique para estes termos personalizados serão apresentados com os termos de alteração personalizados no fluxo de compra da oferta no portal Azure.  
- **IDs do inquilino** (obrigatório): Cada alteração personalizada pode ser direcionada até 20 IDs de inquilino. Se adicionar uma emenda personalizada, deve fornecer pelo menos uma identificação de inquilino. A identificação do inquilino identifica o seu cliente em Azure. Pode pedir ao seu cliente este ID e eles podem encontrá-lo navegando para portal.azure.com > Azure Ative Directy > Properties. O valor de ID do diretório é o ID do inquilino (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode procurar o iD do inquilino da organização do seu cliente usando o seu nome de domínio URL em [Qual é o meu ID de inquilino Microsoft Azure e Microsoft 365?](https://www.whatismytenantid.com)
- **Descrição** (opcional): Opcionalmente fornecer uma descrição amigável para o ID do inquilino que o ajuda a identificar o cliente que você está visando com a emenda.

**Termos e condições**

Se quiser fornecer os seus próprios termos e condições personalizados, pode optar por inscrevê-los no campo de termos e condições. Pode introduzir até 10.000 caracteres de texto neste campo. Se os seus termos e condições requerem uma descrição mais longa, insira um único link URL neste campo onde os seus termos e condições podem ser encontrados. Será apresentado aos clientes como um link ativo.

Os clientes são obrigados a aceitar estes termos antes de poderem experimentar a sua oferta.

**Selecione Guardar o projeto** antes de continuar.
