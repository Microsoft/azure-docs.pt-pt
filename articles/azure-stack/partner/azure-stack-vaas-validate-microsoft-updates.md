---
title: Validar as atualizações de software da Microsoft no Azure Stack validação como um serviço | Documentos da Microsoft
description: Aprenda a validar as atualizações de software da Microsoft com a validação como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ad9d5057c18d316dcf3254dc57a3184c1b75fc50
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780846"
---
# <a name="validate-software-updates-from-microsoft"></a>Validar as atualizações de software da Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

A Microsoft lançará periodicamente atualizações para o software do Azure Stack. Estas atualizações são fornecidas para o Azure Stack coengineering parceiros. As atualizações são fornecidas com antecedência de publicamente disponíveis. Pode verificar as atualizações em relação a sua solução e fornecer comentários à Microsoft.

As atualizações de software do Microsoft Azure Stack são designadas usando uma convenção de nomenclatura, por exemplo, 1803 que indica que a atualização é de Março de 2018. Para obter informações sobre a política de atualização do Azure Stack, cadência e notas de versão estão disponíveis, consulte [política de manutenção do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy).

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar o processo de atualização mensal no VaaS, deve estar familiarizado com os seguintes itens:

- [Validação como um conceito de principais de serviço](azure-stack-vaas-key-concepts.md)
- [Teste de verificação de recurso interativo](azure-stack-vaas-interactive-feature-verification.md)

## <a name="required-tests"></a>Testes necessários

Os seguintes testes devem ser executados pela seguinte ordem para a validação de software mensais:

1. Verificação de atualização mensal do Azure Stack
2. Mecanismo de simulação de cloud

## <a name="validating-software-updates"></a>Ao validar as atualizações de software

1. Criar uma nova **validação do pacote** fluxo de trabalho.
1. Para os testes requeridos acima, siga as instruções em [testes de validação do pacote executar](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Consulte a secção abaixo para obter instruções adicionais sobre o **verificação de atualização de pilha do Azure mensal** testar.

### <a name="apply-the-monthly-update"></a>Aplicar a atualização mensal

1. Selecione a executar testes em relação a um agente.
1. Agenda **verificação de atualização mensal do Azure Stack**.
1. Indique a localização para o pacote de extensão de OEM implantado no momento em que o carimbo e a localização para o pacote de extensão de OEM que será aplicada durante a atualização. Para configurar os URLs para esses pacotes, consulte [gerenciamento de pacotes para a validação](azure-stack-vaas-validate-oem-package.md#managing-packages-for-validation).
1. Siga os passos na IU do agente selecionado.

Se tiver dúvidas ou preocupações, contacte [VaaS ajuda](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Passos Seguintes

- [Monitorizar e gerir os testes no portal do VaaS](azure-stack-vaas-monitor-test.md)