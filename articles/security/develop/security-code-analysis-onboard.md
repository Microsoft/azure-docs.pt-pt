---
title: Guia de integração da análise de código de segurança Microsoft Azure
description: Este artigo trata da instalação da extensão de análise de código de segurança
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
ms.openlocfilehash: 8a8eca73205d4f8f33d4d069fda72638af61d355
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718304"
---
# <a name="how-to-onboarding-and-installing"></a>Como: Integração e instalação

Pré-requisitos para começar a usar a análise de código de segurança da Microsoft
  - Oferta de serviços Suporte Unificado da Microsoft qualificados (detalhes abaixo)
  - Uma organização DevOps do Azure
  - Permissões para instalar extensões para a organização DevOps do Azure
  - Código-fonte que pode ser sincronizado com um pipeline DevOps do Azure hospedado na nuvem


## <a name="onboarding-microsoft-security-code-analysis-extension"></a>Integração da extensão de análise de código de segurança da Microsoft

- Se você já tiver uma das seguintes ofertas de suporte, basta entrar em contato com seu gerente técnico de conta e purchase\swap horas existentes para obter acesso à extensão.
   - Suporte unificado – nível avançado e de desempenho, Suporte Premier para desenvolvedores, Suporte Premier para parceiros ou Suporte Premier para empresas.
- Se você tiver um dos seguintes serviços de suporte ou não tiver nenhum plano de suporte com a Microsoft, será necessário atualizar para uma oferta de suporte qualificada:
   - Suporte do Azure para parceiros, Azure básico, desenvolvedor do Azure, Standard do Azure, Professional Direct do Azure ou suporte unificado – camada central
- Para adquirir uma oferta de suporte elegível, visite nossos [serviços de suporte Home Page](https://www.microsoft.com/enterprise/services/support)
- Quando um contrato de suporte estiver em vigor, entre em contato com seu gerente técnico de conta que pode ajudá-lo a começar e a coletar todos os detalhes necessários.
 
>[!NOTE]
> Somente parceiros registrados na rede de parceiros da Microsoft estão qualificados para comprar Suporte Premier para parceiros; caso contrário, compre uma das ofertas de suporte qualificadas mencionadas anteriormente

## <a name="installing-microsoft-security-code-analysis-extension"></a>Instalando a extensão de análise de código de segurança da Microsoft

1. Depois que a extensão for compartilhada com sua organização DevOps do Azure, navegue até a página da organização do Azure DevOps (por exemplo, http://dev.azure.com/contoso)
2. Clique no ícone de bolsa de compras no canto superior direito ao lado de seu nome e clique em gerenciar extensões 
3. Clique na extensão análise de código de segurança da Microsoft e inicie o assistente de interface do usuário do Azure DevOps para iniciar a instalação.
4. Escolha a organização do Azure DevOps para instalar a extensão na lista suspensa
5. Clique em instalar. Após a conclusão, você pode continuar usando a extensão

>[!NOTE]
> Mesmo que você não tenha acesso, continue com as etapas de instalação e você poderá solicitar acesso do administrador da organização do Azure DevOps durante o processo.
>
Depois que a extensão for instalada, as tarefas de compilação de desenvolvimento seguro estarão visíveis e disponíveis para serem adicionadas ao seu Azure Pipelines.

## <a name="adding-specific-build-tasks-to-your-devops-pipeline"></a>Adicionando tarefas de compilação específicas ao pipeline do DevOps

1. Abra seu projeto de equipe da sua organização de DevOps do Azure.
2. Navegue até a guia compilações em pipelines 
3. Selecione o pipeline no qual você deseja adicionar as tarefas de compilação de extensão. 
   - Novo-clique em **novo** e siga as etapas detalhadas para criar um novo pipeline.
   - Editar – selecione o pipeline e clique em **Editar** para começar a editar um pipeline existente.
4. Clique em + para navegar até o painel **adicionar tarefas** .
5. Localize a tarefa de compilação que você deseja adicionar da lista ou usando a caixa de pesquisa e clique em **Adicionar**. 
6. Agora você pode continuar a especificar os parâmetros necessários para a tarefa.
>[!NOTE]
>Os caminhos de arquivo ou diretório são relativos à raiz do repositório de origem e os parâmetros que especificam a pasta/arquivos de saída serão substituídos pelo local comum que definimos no agente de compilação.

7. Enfileirar uma nova compilação.
> [!TIP]
>  - Para executar a análise após sua compilação, coloque as tarefas de compilação da análise de código de segurança da Microsoft após a etapa publicar artefatos de compilação de sua compilação. Dessa forma, sua compilação pode terminar e postar os resultados antes de executar as ferramentas de análise estática.
>  - Sempre marque a opção **' continuar no erro '** de tarefas de compilação de desenvolvimento seguro. Mesmo que uma das ferramentas falhe, as outras podem ser executadas. Não há interdependências.
>  - As tarefas de compilação da análise de código de segurança da Microsoft falharão somente se a ferramenta não for executada com êxito, mas não falhará se e quando a ferramenta identificar problemas no código. Você pode configurar sua compilação para ser interrompida quando uma ferramenta identificar problemas no código, usando a tarefa de compilação **pós-análise** .
>  - Algumas tarefas de compilação DevOps do Azure não têm suporte quando executadas por meio de um pipeline de "liberação". Essa é uma limitação do Azure DevOps. Eles não dão suporte a tarefas que publicam artefatos de dentro de um pipeline de lançamento.
>  - Para obter uma lista de variáveis predefinidas no Azure DevOps Team Build, que você pode especificar como parâmetros, consulte [variáveis de compilação do DevOps do Azure](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como configurar as tarefas de compilação, consulte nosso [Guia de configuração](security-code-analysis-customize.md)

Se você tiver mais dúvidas sobre a extensão e as ferramentas oferecidas, [Consulte nossa página perguntas frequentes.](security-code-analysis-faq.md)


