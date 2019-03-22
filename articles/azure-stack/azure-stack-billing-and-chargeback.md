---
title: Cliente de faturação e estorno no Azure Stack | Documentos da Microsoft
description: Saiba como obter informações de utilização de recursos do Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: c1b0a1523e65014b153ff3edb0c22b53ce7fb8a3
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337855"
---
# <a name="usage-and-billing-in-azure-stack"></a>Utilização e faturação no Azure Stack

Este artigo descreve como os utilizadores do Azure Stack são cobrados a utilização de recursos e como as informações de faturas são acessadas para análise e de cobrança novamente.

O Azure Stack recolhe e grupos de dados de utilização de recursos que são utilizados. Em seguida, o Azure Stack reencaminha estes dados para Azure Commerce. Azure Commerce cobra-lhe para utilização do Azure Stack da mesma forma que ele cobra-lhe para utilização do Azure.

Também pode obter dados de utilização e a exportação para o seu próprio faturação ou estorno fazer uma cópia system com um adaptador de faturação ou exportá-lo a uma ferramenta de inteligência de negócio como o Microsoft Power BI.

## <a name="usage-pipeline"></a>Pipeline de utilização

Cada fornecedor de recursos no Azure Stack publica os dados de utilização por utilização de recursos. O serviço de utilização periodicamente (hora a hora e diariamente) agrega dados de utilização e armazena-os na base de dados de utilização. Operadores de pilha e os utilizadores do Azure podem acessar os dados de utilização armazenado através da utilização de recursos do Azure Stack APIs.

Se tiver [registado a sua instância do Azure Stack com o Azure](azure-stack-register.md), Azure Stack está configurado para enviar os dados de utilização para o Azure Commerce. Depois dos dados são carregados para o Azure, pode acessá-lo através do portal de faturação ou através de APIs de utilização de recursos do Azure. Para obter mais informações sobre os dados de utilização são comunicados ao Azure, consulte [relatórios de dados de utilização](azure-stack-usage-reporting.md).  

A imagem seguinte mostra os principais componentes no pipeline de utilização:

![Pipeline de utilização](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>As informações de utilização posso encontrar e como?

Fornecedores de recursos do Azure Stack (por exemplo, computação, armazenamento e rede) geram dados de utilização em intervalos por hora para cada subscrição. Os dados de utilização contém informações sobre o recurso usada, como o nome do recurso, a subscrição utilizada e a quantidade utilizada. Para saber mais sobre os recursos de ID dos medidores, veja a [FAQ da API de utilização](azure-stack-usage-related-faq.md).

Depois que foram recolhidos os dados de utilização, é [reportado para o Azure](azure-stack-usage-reporting.md) para gerar uma fatura, o qual pode ser visualizada através do portal de faturação do Azure.

> [!NOTE]  
> Relatórios de dados de utilização não é necessário para o Azure Stack Development Kit (ASDK) e para os utilizadores de sistema integrado do Azure Stack portadores de licenças de acordo com o modelo de capacidade. Para saber mais sobre o licenciamento no Azure Stack, veja a [empacotamento e a folha de dados de preços](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

O portal de faturação do Azure mostra dados de utilização para os recursos faturáveis. Além de recursos faturáveis, o Azure Stack captura dados de utilização para um conjunto mais amplo de recursos, o qual pode aceder no seu ambiente do Azure Stack através de REST APIs ou cmdlets do PowerShell. Operadores do Azure Stack podem obter os dados de utilização para todas as subscrições de utilizador. Utilizadores individuais só podem obter seus próprios detalhes de utilização.

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Utilização de relatórios para fornecedores de serviços Cloud de multi-inquilino

Um inquilino de multi fornecedor de serviços Cloud (CSP) que tenha muitos clientes com o Azure Stack pode desejar cada utilização do cliente de relatórios em separado, para que o fornecedor pode cobrar a utilização de diferentes subscrições do Azure.

Cada cliente tem a sua identidade representada por um inquilino diferente do Azure Active Directory (Azure AD). O Azure Stack oferece suporte a atribuir uma subscrição do CSP para cada inquilino do Azure AD. Pode adicionar os inquilinos e as suas subscrições para o registo do Azure Stack base. O registo de base é feito para todas as instâncias do Azure Stack. Se uma subscrição não está registada para um inquilino, o utilizador ainda pode utilizar o Azure Stack e a utilização das mesmas será enviada para a subscrição utilizada para o registo de base.

## <a name="next-steps"></a>Passos Seguintes

- [Registe-se com o Azure Stack](azure-stack-registration.md)
- [Report Azure Stack usage data to Azure](azure-stack-usage-reporting.md) (Comunicar dados de utilização do Azure Stack ao Azure)
- [API de utilização de recursos do fornecedor](azure-stack-provider-resource-api.md)
- [Tenant Resource Usage API](azure-stack-tenant-resource-usage-api.md) (API de Utilização de Recursos de Inquilino)
- [Perguntas Freqüentes relacionadas com a utilização](azure-stack-usage-related-faq.md)