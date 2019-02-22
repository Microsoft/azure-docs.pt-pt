---
title: Uma visão geral de validação como um serviço para o Azure Stack | Documentos da Microsoft
description: Uma descrição geral da validação de pilha do Azure como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 12/20/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 6126bacf50d47029c29772b35f6dc1d552d47029
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56592643"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>O que é a validação como um serviço para o Azure Stack?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Validação como um serviço (VaaS) é um serviço do Azure nativo concebido para os parceiros de solução que são de engenharia conjuntas ofertas do Azure Stack com a Microsoft. Parceiros de solução podem utilizar o serviço para verificar se as suas soluções de cumprem os requisitos da Microsoft e a funcionar conforme esperado com o Azure Stack.

As utilizações primárias VaaS são:

- A validar novas soluções do Azure Stack
- A validar alterações no software do Azure Stack
- Assinatura digital de pacotes de parceiro de soluções utilizadas durante a implementação
- Pré-visualização VaaS acessórios de teste

## <a name="validate-a-new-azure-stack-solution"></a>Validar uma nova solução do Azure Stack

Parceiros de utilização a **validação de solução** fluxo de trabalho para validar as novas soluções do Azure Stack. A solução tem de passar os testes de componente necessários do Hardware Lab Kit (HLK) do Azure Stack. Para certificar uma variedade de configurações de hardware, o fluxo de trabalho tem de ser executado duas vezes para cada nova solução: uma vez a cada para as configurações mínimas e máximas.

Para obter mais informações, consulte [validar uma nova solução do Azure Stack](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-software"></a>Validar as alterações ao software do Azure Stack

Parceiros de utilização a **validação do pacote** fluxo de trabalho para verificar que a sua solução funciona com as atualizações de software mais recentes do Azure Stack. O fluxo de trabalho de validação do pacote deve ser executado num ambiente de hardware recomendadas pela Microsoft onde os patches e atualizações (P & U) foi utilizado para aplicar a atualização. Recomenda-se também executar o fluxo de trabalho na compilação de linha de base.

Para obter mais informações, consulte [validar as atualizações de software da Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Obter solução assinada digitalmente pacotes de parceiro

Além de validação de atualizações do Azure Stack, parceiros utilização a **validação do pacote** fluxo de trabalho para validar as atualizações para pacotes de personalização OEM, que incluem drivers de parceiros específicos do Azure Stack, firmware e outro software utilizado durante a implementação do software do Azure Stack. Implemente o pacote que está a validar a versão atual do software do Azure Stack com, pelo menos, a solução de tamanho mínimo de mensagens em fila que será suportada. O pacote foi submetido para o VaaS antes de executar testes. Se os testes forem bem sucedidas, notificar [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) que o pacote foi concluída de teste e deve ser digitalmente assinadas com a assinatura digital do Azure Stack. A Microsoft assina o pacote e notifica o parceiro do Azure Stack que o pacote está disponível para download no portal do VaaS.

Para obter mais informações, consulte [pacotes de validar OEM](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-vaas-test-collateral"></a>Acessórios de teste de VaaS de pré-visualização

Microsoft regularmente disponibiliza novas funcionalidades no Azure Stack. Como parte do processo de desenvolvimento para o fornecimento desses recursos no mercado, novo acessórios de teste é disponibilizado no **aprovação de teste** fluxo de trabalho. O fluxo de trabalho de aprovação de teste inclui acessórios de teste dos outros fluxos de trabalho para permitir a execução de teste não oficial. Não utilize o fluxo de trabalho de aprovação de teste para enviar resultados para aprovação. Utilize os fluxos de trabalho de validação de solução e a validação do pacote para obter aprovação oficial para a sua solução.

Para obter mais informações, consulte [início rápido: Utilize a validação como um portal de serviço para agendar o seu primeiro teste](azure-stack-vaas-schedule-test-pass.md).

## <a name="validation-workflow-tests-summary"></a>Resumo de testes de fluxo de trabalho de validação

| Fluxo de trabalho de validação | Testes necessários |
|----|------------|
| [Nova validação de solução](azure-stack-vaas-validate-solution-new.md) | Mecanismo de simulação de cloud<br>Computação Suite operacional do SDK<br>Teste de identificação de disco<br>Conjunto de operacional de SDK de extensão do Cofre de chaves<br>Suite operacionais do SDK do Cofre de chaves<br>Conjunto de operacional de SDK de rede<br>Conjunto operacional do SDK de conta de armazenamento<br> |
| [Validação do pacote de OEM](azure-stack-vaas-validate-oem-package.md) | Verificação do pacote de extensão de OEM<br>Mecanismo de simulação de cloud |
| [Validação de atualização mensal](azure-stack-vaas-validate-microsoft-updates.md) | Verificação de atualização de AzureStack mensal<br>Mecanismo de simulação de cloud<br> |

## <a name="next-steps"></a>Passos Seguintes

- [Configurar a sua validação como recursos de serviço](azure-stack-vaas-set-up-resources.md)
- Saiba mais sobre [validação como um conceito de principais de serviço](azure-stack-vaas-key-concepts.md)
