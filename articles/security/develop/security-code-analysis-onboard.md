---
title: Guia de análise de código de segurança da Microsoft
description: Este artigo descreve a instalação da extensão de análise de código de segurança da Microsoft
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 5b7e36c12dab65f4bcc6c05a1ee902301c402d5e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147672"
---
# <a name="onboarding-and-installing"></a>Embarque e instalação

Pré-requisitos para começar com a Análise do Código de Segurança da Microsoft:

- Uma oferta de suporte unificado da Microsoft elegível, conforme detalhado na secção seguinte.
- Uma organização Azure DevOps.
- Permissão para instalar extensões à organização Azure DevOps.
- Código fonte que pode ser sincronizado com um oleoduto Azure DevOps hospedado em nuvem.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>No embarque na extensão de análise do código de segurança da Microsoft

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Interessado em adquirir a extensão de Análise de Código de Segurança da Microsoft?

Se tiver uma das seguintes ofertas de suporte, contacte o seu Gestor de Conta Técnica para comprar ou trocar as horas existentes para ter acesso à extensão:

- Suporte avançado de suporte unificado
- Nível de desempenho de suporte unificado
- Suporte Premier para Desenvolvedores
- Apoio principal aos parceiros
- Suporte premier para a empresa

Se não tiver um dos acordos de apoio acima mencionados, pode adquirir a extensão a um dos nossos Parceiros.

**Próximos passos:**

Se cumprir as qualificações acima, contacte um parceiro da lista abaixo para adquirir a extensão de Análise de Código de Segurança da Microsoft. Caso contrário, contacte o Suporte de Análise de [Códigode Segurança da Microsoft](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request).

>**Parceiros:**

- Zonas - Detalhes de contato:cloudsupport@zones.com
- Wortell – Detalhes de contacto:info@wortell.nl

### <a name="become-a-partner"></a>Torne-se um Parceiro

A equipa de Análise de Códigos de Segurança da Microsoft está a procurar parceiros a bordo com um acordo de Suporte Premier para Parceiros. Os parceiros ajudarão a capacitar os clientes da Azure DevOps a desenvolverem-se de forma mais segura, vendendo a extensão aos clientes que a desejem comprar, mas não têm um acordo de Suporte Empresarial com a Microsoft. Os parceiros interessados podem inscrever-se [aqui.](http://www.microsoftpartnersupport.com/msrd/opin)

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Instalação da extensão de análise de código de segurança da Microsoft

1. Depois de a extensão ser partilhada com a sua organização Azure DevOps, vá à sua página de organização Azure DevOps. Um URL de exemplo `https://dev.azure.com/contoso`para tal página é .
1. Selecione o ícone do saco de compras no canto superior direito ao lado do seu nome e, em seguida, selecione **extensões de gestão**.
1. Selecione **Shared**.
1. Selecione a extensão de análise de código de segurança da Microsoft, **selecione instalar**.
1. A partir da lista de lançamentos, escolha a organização Azure DevOps para instalar a extensão.
1. Selecione **Instalar**. Depois de concluída a instalação, pode começar a utilizar a extensão.

>[!NOTE]
> Mesmo que não tenha acesso para instalar a extensão, continue com as etapas de instalação. Pode solicitar acesso ao administrador da organização Azure DevOps durante o processo de instalação.

Depois de instalar a extensão, as tarefas de construção de desenvolvimento segura são visíveis e disponíveis para adicionar aos seus Pipelines Azure.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Adicionar tarefas específicas de construção ao seu pipeline Azure DevOps

1. Da sua organização Azure DevOps, abra o seu projeto de equipa.
1. Selecione **Pipelines** > **Builds**.
1. Selecione o gasoduto no qual pretende adicionar as tarefas de construção de extensão:
   - Novo oleoduto: Selecione **Novo** e siga os passos detalhados para criar um novo oleoduto.
   - Editar o gasoduto: Selecione um gasoduto existente e, em seguida, selecione **Editar** para começar a editar o pipeline.
1. Selecione **+** e vá ao painel **adicionar tarefas.**
1. A partir da lista ou utilizando a caixa de pesquisa, encontre a tarefa de construção que pretende adicionar. Selecione **Adicionar**.
1. Especifique os parâmetros necessários para a tarefa.
1. Faça fila com uma nova construção.
   >[!NOTE]
   >Os caminhos de ficheiros e pastas são relativos à raiz do seu repositório de origem. Se especificar os ficheiros e pastas de saída como parâmetros, estes são substituídos pela localização comum que definimos no agente de construção.

> [!TIP]
>
> - Para fazer uma análise após a sua construção, coloque as tarefas de construção do Código de Segurança da Microsoft após o passo da publicação de Artefactos de Construção da sua construção. Dessa forma, a sua construção pode terminar e publicar resultados antes de executar ferramentas de análise estática.
> - Selecione sempre **Continuar a procurar** tarefas de construção de desenvolvimento seguro. Mesmo que uma ferramenta falhe, as outras podem correr. Não há interdependências entre ferramentas.
> - As tarefas de construção de códigos de segurança da Microsoft só falham se uma ferramenta não funcionar com sucesso. Mas têm sucesso mesmo que uma ferramenta identifique problemas no código. Ao utilizar a tarefa de construção pós-análise, pode configurar a sua construção para falhar quando uma ferramenta identifica problemas no código.
> - Algumas tarefas de construção de Azure DevOps não são suportadas quando executadas através de um gasoduto de libertação. Mais especificamente, a Azure DevOps não suporta tarefas que publiquem artefactos dentro de um oleoduto de lançamento.
> - Para obter uma lista de variáveis predefinidas na Azure DevOps Team Build que pode especificar como parâmetros, consulte [Azure DevOps Build Variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts).

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre a configuração das tarefas de construção, consulte o nosso guia de [configuração](security-code-analysis-customize.md) ou [o guia de configuração YAML](yaml-configuration.md).

Se tiver mais dúvidas sobre a extensão e as ferramentas oferecidas, confira a nossa [página de PERGUNTAS FREQUENTEs](security-code-analysis-faq.md).
