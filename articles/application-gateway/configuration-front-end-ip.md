---
title: Configuração de endereço IP frontal do Gateway de aplicação Azure
description: Este artigo descreve como configurar o endereço IP frontal do Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: d6cfac7f0fb3939e57ce64f552556138ce9feacd
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653191"
---
# <a name="application-gateway-front-end-ip-address-configuration"></a>Configuração de endereço IP frontal do Gateway de aplicação

Pode configurar a porta de aplicação para ter um endereço IP público, um endereço IP privado ou ambos. É necessário um endereço IP público quando acolhe uma parte traseira que os clientes devem aceder através da Internet através de um IP virtual (VIP) virado para a Internet.

## <a name="public-and-private-ip-address-support"></a>Suporte ao endereço IP público e privado

Atualmente, o Gateway V2 da aplicação não suporta apenas o modo IP privado. Suporta as seguintes combinações:

* Endereço IP privado e endereço IP público
* Endereço IP público apenas

Para obter mais informações, consulte [Perguntas frequentes sobre o Gateway de Aplicações.](application-gateway-faq.md#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address)


Um endereço IP público não é necessário para um ponto final interno que não esteja exposto à Internet. É conhecido como um *ponto final interno de load-balancer* (ILB) ou frontend IP privado. Um gateway de aplicações ILB é útil para aplicações internas de linha de negócio que não estão expostas à Internet. Também é útil para serviços e níveis numa aplicação de vários níveis dentro de uma fronteira de segurança que não estão expostos à Internet, mas que requerem distribuição de carga de robin redondo, adesivo de sessão ou rescisão de TLS.

Apenas um endereço IP público ou um endereço IP privado é suportado. Escolha o IP frontal quando criar o gateway de aplicações.

- Para um endereço IP público, pode criar um novo endereço IP público ou utilizar um IP público existente no mesmo local que o gateway de aplicações. Para mais informações, consulte [o endereço IP público estático vs. dinâmico](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- Para um endereço IP privado, pode especificar um endereço IP privado a partir da sub-rede onde o gateway de aplicações é criado. Se não especificar um, um endereço IP arbitrário é automaticamente selecionado a partir da sub-rede. O tipo de endereço IP que seleciona (estático ou dinâmico) não pode ser alterado mais tarde. Para obter mais informações, consulte [Criar uma porta de aplicação com um equilibrador de carga interno](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Um endereço IP frontal está associado a um *ouvinte*, que verifica os pedidos de entrada no IP frontal.

## <a name="next-steps"></a>Próximos passos

- [Saiba mais sobre a configuração do ouvinte](configuration-listeners.md)