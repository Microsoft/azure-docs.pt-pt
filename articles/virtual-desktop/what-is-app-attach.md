---
title: Aplicativo Virtual Desktop MSIX anexa visão geral - Azure
description: O que é a anexação da aplicação MSIX? Descubra neste artigo.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c6bf296b5173a662b1e9dd7b025648e3f16d23c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88556347"
---
# <a name="what-is-msix-app-attach"></a>O que é a anexação da aplicação MSIX?

O MSIX é um novo formato de embalagem que oferece muitas funcionalidades destinadas a melhorar a experiência de embalagem para todas as aplicações do Windows. Para saber mais sobre a MSIX, consulte a [visão geral](/windows/msix/overview)do MSIX .

O anexo de aplicações MSIX é uma forma de entregar aplicações MSIX a máquinas físicas e virtuais. No entanto, o anexo de aplicações MSIX é diferente do MSIX normal, porque é feito especialmente para o Windows Virtual Desktop. Este artigo descreverá o que a app MSIX anexa é e o que pode fazer por si.

## <a name="application-delivery-options-in-windows-virtual-desktop"></a>Opções de entrega de aplicativos no Windows Virtual Desktop

Pode entregar aplicações no Windows Virtual Desktop através de um dos seguintes métodos:

- Coloque aplicativos em uma imagem principal
- Utilize ferramentas como SCCM ou Intune para gestão central
- Fornecimento dinâmico de aplicativos (AppV, VMWare AppVolumes ou Citrix AppLayering)
- Crie ferramentas ou scripts personalizados usando a Microsoft e uma ferramenta de terceiros

## <a name="what-does-msix-app-attach-do"></a>O que é que a app MSIX anexa faz?

Numa implementação virtual do Windows Desktop, o anexo de aplicações MSIX pode:

- Criar separação entre os dados do utilizador, o SO e as aplicações utilizando [recipientes MSIX](/windows/msix/msix-container).
- Remova a necessidade de reembalagem ao entregar as aplicações de forma dinâmica.
- Reduza o tempo que um utilizador demora a iniciar sinse.
- Reduzir os requisitos de infraestrutura e os custos.

O anexo de aplicativo MSIX deve ser aplicável fora do VDI ou SBC.

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>Camadas tradicionais de aplicativos em comparação com o anexo de aplicações MSIX

A tabela a seguir compara a característica chave do anexamento de aplicações MSIX e a camada de aplicativo.

| Funcionalidade | Camadas tradicionais de aplicativos  | Anexação da aplicação MSIX  |
|-----|-----------------------------|--------------------|
| Formato               | Diferentes tecnologias de camadas de aplicativos requerem diferentes formatos proprietários. | Funciona com o formato de embalagem MSIX nativo.        |
| Reembalagem por cima | Os formatos proprietários requerem sequenciação e reembalagem por atualização.         | As aplicações publicadas como MSIX não requerem reembalagem. No entanto, se o pacote MSIX não estiver disponível, a reembalagem ainda se aplica. |
| Ecossistema            | N/A (por exemplo, os fornecedores não enviam App-V)  | A MSIX é a tecnologia mainstream da Microsoft que os principais parceiros ISV e aplicações internas como o Office estão a adotar. Pode utilizar o MSIX tanto em ambientes de trabalho virtuais como em computadores físicos do Windows. |
| Infraestrutura       | Infraestrutura adicional necessária (servidores, clientes, e assim por diante) | Apenas armazenamento   |
| Administração       | Requer manutenção e atualização   | Simplifica atualizações de aplicativos |
| Experiência do utilizador      | Impacta o tempo de inscrição do utilizador. Existe limite entre estado de SO, estado da aplicação e dados do utilizador.  | As aplicações entregues são indistinguíveis de aplicações instaladas localmente. |

## <a name="next-steps"></a>Passos seguintes

Se quiser saber mais sobre o anexo de aplicações MSIX, confira o nosso [glossário](app-attach-glossary.md) e [FAQ.](app-attach-faq.md) Caso contrário, inicie com [o anexo de aplicações Configurar.](app-attach.md)
