---
title: Guia de integração da análise de código de segurança da Microsoft
description: Este artigo descreve como instalar a extensão de análise de código de segurança da Microsoft
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 77be6d8518774a1e2656e23024490dd9c1431865
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707258"
---
# <a name="onboarding-and-installing"></a>Integração e instalação

Pré-requisitos para a introdução à análise de código de segurança da Microsoft:

- Uma oferta Suporte Unificado da Microsoft qualificada, conforme detalhado na seção a seguir.
- Uma organização DevOps do Azure.
- Permissão para instalar extensões para a organização de DevOps do Azure.
- Código-fonte que pode ser sincronizado com um pipeline DevOps do Azure hospedado na nuvem.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Integração da extensão de análise de código de segurança da Microsoft

- Se você tiver uma das seguintes ofertas de suporte, entre em contato com seu gerente técnico de conta para comprar ou trocar horas existentes para obter acesso à extensão:
   - Camada avançada de suporte unificado
   - Nível de desempenho de suporte unificado
   - Suporte Premier para desenvolvedores
   - Suporte Premier para parceiros
   - Suporte Premier para empresas
- Se você tiver um dos seguintes serviços de suporte ou não tiver um plano de suporte da Microsoft, será necessário atualizar para uma oferta de suporte elegível:
   - Suporte do Azure para Parceiros
   - Suporte Básico do Azure
   - Suporte Developer do Azure
   - Suporte Standard do Azure
   - Professional Direct do Azure
   - Camada de núcleo de suporte unificado
- Para adquirir uma oferta de suporte elegível, acesse nossos [serviços de suporte Home Page](https://www.microsoft.com/enterprise/services/support).
- Depois que um contrato de suporte estiver em vigor, entre em contato com seu gerente técnico de conta para ajudá-lo a começar e para ajudá-lo a coletar todos os detalhes necessários.

>[!NOTE]
> Se você for um parceiro registrado na rede de parceiros da Microsoft, você estará qualificado para comprar Suporte Premier para parceiros. Caso contrário, você deve comprar uma das ofertas de suporte qualificadas mencionadas anteriormente.

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Instalando a extensão de análise de código de segurança da Microsoft

1. Depois que a extensão for compartilhada com sua organização do DevOps do Azure, vá para a página da organização do DevOps do Azure. Uma URL de exemplo para essa página é `https://dev.azure.com/contoso`.
1. Selecione o ícone de bolsa de compras no canto superior direito ao lado de seu nome e, em seguida, selecione **gerenciar extensões**.
1. Selecione **compartilhado**.
1. Selecione a extensão análise de código de segurança da Microsoft, selecione **instalar**.
1. Na lista suspensa, escolha a organização DevOps do Azure na qual instalar a extensão.
1. Selecione **Instalar**. Após a conclusão da instalação, você pode começar a usar a extensão.

>[!NOTE]
> Mesmo que você não tenha acesso para instalar a extensão, continue com as etapas de instalação. Você pode solicitar acesso do administrador da organização do Azure DevOps durante o processo de instalação.

Depois de instalar a extensão, as tarefas de compilação de desenvolvimento seguro ficam visíveis e disponíveis para serem adicionadas aos seus pipelines do Azure.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Adicionando tarefas de compilação específicas ao pipeline DevOps do Azure

1. Em sua organização do Azure DevOps, abra seu projeto de equipe.
1. Selecione **pipelines** > **compilações**.
1. Selecione o pipeline no qual você deseja adicionar as tarefas de compilação de extensão:
   - Novo pipeline: selecione **novo** e siga as etapas detalhadas para criar um novo pipeline.
   - Editar pipeline: selecione um pipeline existente e, em seguida, selecione **Editar** para começar a editar o pipeline.
1. Selecione **+** e vá para o painel **adicionar tarefas** .
1. Na lista ou usando a caixa de pesquisa, localize a tarefa de compilação que você deseja adicionar. Selecione **Adicionar**.
1. Especifique os parâmetros necessários para a tarefa.
1. Enfileirar uma nova compilação.
   >[!NOTE]
   >Os caminhos de arquivo e pasta são relativos à raiz do seu repositório de origem. Se você especificar os arquivos de saída e as pastas como parâmetros, elas serão substituídas pelo local comum que definimos no agente de compilação.

> [!TIP]
>
> - Para executar uma análise após sua compilação, coloque as tarefas de compilação da análise de código de segurança da Microsoft após a etapa publicar artefatos de compilação de sua compilação. Dessa forma, sua compilação pode terminar e postar os resultados antes de executar as ferramentas de análise estática.
> - Sempre selecione **continuar** se houver erro para tarefas de compilação de desenvolvimento seguro. Mesmo que uma ferramenta falhe, as outras podem ser executadas. Não há interdependências entre as ferramentas.
> - As tarefas de compilação da análise de código de segurança da Microsoft falharão somente se uma ferramenta não for executada com êxito. Mas eles têm sucesso mesmo se uma ferramenta identificar problemas no código. Usando a tarefa de compilação pós-análise, você pode configurar sua compilação para falhar quando uma ferramenta identificar problemas no código.
> - Algumas tarefas de compilação DevOps do Azure não têm suporte quando executadas por meio de um pipeline de liberação. Mais especificamente, o Azure DevOps não dá suporte a tarefas que publicam artefatos de dentro de um pipeline de lançamento.
> - Para obter uma lista de variáveis predefinidas no Azure DevOps Team Build que você pode especificar como parâmetros, consulte [variáveis de compilação do DevOps do Azure](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como configurar as tarefas de compilação, consulte nosso [Guia de configuração](security-code-analysis-customize.md).

Se você tiver mais perguntas sobre a extensão e as ferramentas oferecidas, Confira nossa [página de perguntas frequentes](security-code-analysis-faq.md).
