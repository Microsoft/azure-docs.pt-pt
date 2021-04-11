---
title: Guia de análise de código de segurança da Microsoft
description: Saiba como embarcar e instalar a extensão de Análise de Código de Segurança do Microsoft. Consulte os pré-requisitos e veja recursos adicionais.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c827dc81c1ef1ab03dd6c9178a609b512e59ef15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801219"
---
# <a name="onboarding-and-installing"></a>Integração e instalação

> [!Note]
> A partir de 1 de março de 2022, a extensão do Código de Segurança da Microsoft (MSCA) será retirada. Os clientes MSCA existentes manterão o seu acesso à MSCA até 1 de março de 2022. Consulte as [Ferramentas de Análise de Código Fonte OWASP](https://owasp.org/www-community/Source_Code_Analysis_Tools) para obter opções alternativas em Azure DevOps. Para clientes que planeiam migrar para o GitHub, você pode verificar [GitHub Advanced Security](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

Pré-requisitos para começar com a Microsoft Security Code Analysis:

- Uma oferta de Suporte Unificado da Microsoft elegível, conforme detalhado na secção seguinte.
- Uma organização da Azure DevOps.
- Permissão para instalar extensões para a organização Azure DevOps.
- Código fonte que pode ser sincronizado com um pipeline Azure DevOps hospedado na nuvem.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Embarque na extensão de Análise de Código de Segurança da Microsoft

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Interessado em comprar a extensão de Análise de Código de Segurança da Microsoft?

Se tiver uma das seguintes ofertas de suporte, contacte o seu Gestor de Conta Técnica para adquirir ou trocar horas existentes para ter acesso à extensão:

- Nível avançado de suporte unificado
- Nível de desempenho de suporte unificado
- Apoio premier para desenvolvedores
- Apoio premier aos parceiros
- Premier Support for Enterprise

Se não tiver um dos acordos de apoio acima mencionados, pode adquirir a extensão a um dos nossos Parceiros.

**Passos Seguintes:**

Se cumprir as qualificações acima, contacte um parceiro da lista abaixo para adquirir a extensão de Análise do Código de Segurança da Microsoft. Caso contrário, contacte [o Microsoft Security Code Analysis Support](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request).

>**Parceiros:**

- Zonas – Detalhes de contacto: cloudsupport@zones.com
- Wortell – Detalhes de contacto: info@wortell.nl
- Logicalis – Detalhes de contacto: logicalisleads@us.logicalis.com

### <a name="become-a-partner"></a>Tornar-se um Parceiro

A equipa de Análise de Códigos de Segurança da Microsoft está à procura de parceiros a bordo com um acordo premier Support for Partners. Os parceiros ajudarão a capacitar os clientes da Azure DevOps a desenvolverem-se de forma mais segura, vendendo a extensão aos clientes que a desejem comprar, mas não têm um acordo de Apoio à Empresa com a Microsoft. Os parceiros interessados podem inscrever-se [aqui.](http://www.microsoftpartnersupport.com/msrd/opin)

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Instalação da extensão de Análise de Código de Segurança da Microsoft

1. Depois de a extensão ser partilhada com a sua organização Azure DevOps, vá à sua página de organização Azure DevOps. Um URL de exemplo para tal página é `https://dev.azure.com/contoso` .
1. Selecione o ícone do saco de compras no canto superior direito ao lado do seu nome e, em seguida, **selecione Gerir as extensões**.
1. Selecione **Compartilhado**.
1. Selecione a extensão de Análise de Código de Segurança da Microsoft, selecione **instalar.**
1. A partir da lista de drop-down, escolha a organização Azure DevOps para instalar a extensão.
1. Selecione **Instalar**. Após a instalação estar concluída, pode começar a utilizar a extensão.

>[!NOTE]
> Mesmo que não tenha acesso para instalar a extensão, continue com os passos de instalação. Pode solicitar acesso à sua administração da organização Azure DevOps durante o processo de instalação.

Depois de instalar a extensão, as tarefas seguras de construção de desenvolvimento são visíveis e disponíveis para adicionar aos seus Pipelines Azure.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Adicionar tarefas de construção específicas ao seu pipeline Azure DevOps

1. Da sua organização Azure DevOps, abra o seu projeto de equipa.
1. Selecione   >  **Pipelines Builds**.
1. Selecione o pipeline no qual pretende adicionar as tarefas de construção de extensão:
   - Novo oleoduto: Selecione **New** e siga os passos detalhados para criar um novo oleoduto.
   - Editar o pipeline: Selecione um pipeline existente e, em seguida, **selecione Editar** para começar a editar o pipeline.
1. Selecione **+** e vá para o painel **'Adicionar Tarefas'.**
1. A partir da lista ou utilizando a caixa de pesquisa, encontre a tarefa de construção que pretende adicionar. Selecione **Adicionar**.
1. Especifique os parâmetros necessários para a tarefa.
1. Fila de uma nova construção.
   >[!NOTE]
   >Os caminhos de arquivo e de pasta são relativos à raiz do seu repositório de origem. Se especificar os ficheiros de saída e as pastas como parâmetros, são substituídos pela localização comum que definimos no agente de construção.

> [!TIP]
>
> - Para executar uma análise após a sua construção, coloque as tarefas de construção do Código de Segurança da Microsoft após o passo de Publicar Os Artefactos da Sua Construção. Desta forma, a sua construção pode terminar e publicar resultados antes de executar ferramentas de análise estática.
> - **Selecione Continue em Erro** para tarefas de construção de desenvolvimento seguro. Mesmo que uma ferramenta falhe, as outras podem correr. Não há interdependências entre as ferramentas.
> - As tarefas de construção do Código de Segurança do Microsoft só falham se uma ferramenta não funcionar com sucesso. Mas têm sucesso mesmo que uma ferramenta identifique problemas no código. Ao utilizar a tarefa de construção pós-análise, pode configurar a sua construção para falhar quando uma ferramenta identifica problemas no código.
> - Algumas tarefas de construção de Azure DevOps não são suportadas quando executadas através de um oleoduto de libertação. Mais especificamente, a Azure DevOps não suporta tarefas que publiquem artefactos dentro de um oleoduto de lançamento.
> - Para obter uma lista de variáveis pré-finadas na Azure DevOps Team Build que pode especificar como parâmetros, consulte [Azure DevOps Build Variables](/azure/devops/pipelines/build/variables?tabs=batch).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a configuração das tarefas de construção, consulte o nosso [guia de configuração](security-code-analysis-customize.md) ou [guia de configuração YAML](yaml-configuration.md).

Se tiver mais perguntas sobre a extensão e as ferramentas oferecidas, consulte a nossa [página de PERGUNTAS Frequentes.](security-code-analysis-faq.md)