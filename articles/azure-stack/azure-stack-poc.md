---
title: O que é o Azure Stack? | Microsoft Docs
description: O Azure Stack permite-lhe executar serviços do Azure no seu centro de dados.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/25/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.lastreviewed: 02/25/2019
ms.openlocfilehash: 65894ccd9514bce1d429b336f8bd5e6674048e65
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820268"
---
# <a name="what-is-azure-stack"></a>O que é o Azure Stack?

O Microsoft Azure Stack é uma plataforma de cloud híbrida que lhe permite fornecer serviços do Azure no seu datacenter. Esta plataforma foi concebida para suportar as suas crescentes necessidades comerciais. O Azure Stack pode ativar cenários de novo para as suas aplicações modernas, tais como o limite e ambientes desligados, ou cumpre os requisitos específicos de segurança e conformidade.

O Azure Stack é oferecida na duas opções de implementação para satisfazer as necessidades.


## <a name="azure-stack-development-kit"></a>Development Kit do Azure Stack

O [Development Kit do Azure Stack (ASDK)](./asdk/asdk-what-is.md) da Microsoft é uma implementação de nó único do Azure Stack, que pode utilizar para avaliar e saber mais sobre o Azure Stack.  Também pode utilizar o ASDK como um ambiente de programação para criar aplicações com as APIs e ferramentas que seja consistente com o Azure.

>[!Note]
>O ASDK não se destina a ser utilizado como um ambiente de produção.

O ASDK tem as seguintes limitações:

* O ASDK está associado um único do Azure Active Directory (Azure AD) ou o fornecedor de identidade de serviços de Federação do Active Directory (AD FS). Pode criar vários utilizadores neste diretório e atribuir as subscrições a cada utilizador.
* Como os componentes da pilha do Azure são implementados num computador anfitrião único, existem limitados de recursos físicos disponíveis para os recursos de inquilino. Esta configuração não se destina à escala ou de avaliação de desempenho.
* Os cenários de rede estão limitados devido aos requisitos de implementação NIC e de único anfitrião.

## <a name="azure-stack-integrated-systems"></a>Sistemas integrados do Azure Stack
Os sistemas integrados do Azure Stack são fornecidos através de uma parceria da Microsoft e [parceiros de hardware](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), ao criar uma solução que oferece inovação ao ritmo da cloud e simplicidade na gestão de computação. Por o Azure Stack ser oferecido como um sistema de hardware e software integrados, tem a flexibilidade e controlo de que precisa, juntamente com a capacidade de inovar na cloud. Sistemas de pilha integrado do Azure vão de tamanho de 4 a 16 nós e, em conjunto, são suportados pela Microsoft e parceiros de hardware.  Utilize os sistemas do Azure Stack integrados para criar novos cenários e implementar novas soluções para as cargas de trabalho de produção.

## <a name="next-steps"></a>Passos Seguintes

[Funcionalidades e conceitos principais](azure-stack-key-features.md)

[Azure Stack: Uma extensão do Azure (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
