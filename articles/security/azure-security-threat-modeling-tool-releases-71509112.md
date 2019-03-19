---
title: Ferramenta de modelagem de ameaças libera - ferramenta de modelagem de ameaças Microsoft - Azure | Documentos da Microsoft
description: Documentar as notas de versão para a ferramenta de modelagem de ameaças
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2019
ms.author: jegeib
ms.openlocfilehash: bdf8b701567aaa3a0d9006333557bcec4f312723
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890921"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>GA da ferramenta de modelagem de ameaças da versão 7.1.50911.2 - 9/12/2018

Temos o prazer de anunciar que a ferramenta de modelagem de ameaças da Microsoft já está disponível para download como um lançamento de disponibilidade geral (GA) suportado. Esta versão contém privacidade importante e atualizações de segurança, bem como correções, atualizações de funcionalidades e melhorias de estabilidade. Os utilizadores existentes da versão de pré-visualização de 2017 serão pedidos para atualizar para a versão mais recente através da tecnologia ClickOnce na abertura do cliente. Para os novos utilizadores da ferramenta, [clique aqui para transferir o cliente](https://aka.ms/threatmodelingtool).

Com esta versão, são descontinuar o suporte para a pré-visualização de 2017 e recomendamos a todos os utilizadores da atualização pré-visualização para a versão de disponibilidade geral. Em ou após 15 de Outubro de 2018, vamos definir a versão mínima necessária do ClickOnce para a ferramenta de modelagem de ameaças e serão necessário para atualizar todos os clientes de pré-visualização.

A Microsoft Threat modelagem ferramenta 2016, que está disponível a partir da [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=49168), ser suportada até 1 de Outubro de 2019 para correções de segurança críticas, apenas.

## <a name="feature-changes"></a>Alterações de funcionalidade

### <a name="azure-stencil-updates"></a>Atualizações de estêncil do Azure

Estênceis adicionais do Azure e de seus associadas de ameaças e mitigações foram adicionadas para o conjunto de envio com esta versão de estêncil. Alterações significativas efetuadas nas áreas de foco de "Serviços de aplicações do Azure", "Ofertas de base de dados do Azure" e "O armazenamento do Azure."

![Atualizações de estêncil do Azure](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>Funcionalidade de integração do OneDrive removida

Os recursos de "Guardar para OneDrive", "Aberto do OneDrive" e "Partilha de um Link" de pré-visualização foram removidos. Os utilizadores do OneDrive são encorajados a utilizar da Microsoft [OneDrive para o Windows](https://onedrive.live.com/about/en-us/download/) cliente para aceder aos ficheiros armazenados no OneDrive por meio do arquivo padrão salvar e abrir caixas de diálogo.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Notável corrigida bugs relatados por clientes

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>Em pré-visualização TMT, a ferramenta Falha ao utilizar o modelo padrão

- Quando um estêncil genérico (por exemplo "genérico fluxo de dados") é adicionado à superfície de desenho e gera a ameaças, a ferramenta pode falhar. Este problema foi corrigido.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>Em pré-visualização TMT, quando eu guardar um relatório ou copia as ameaças, os níveis de risco estão incorretos

- Se um utilizador modifica o nível de risco de ameaças específicas e, em seguida, guarda um relatório ou copia os riscos, o nível de risco pode reverter para "Alto". Este problema foi corrigido.

## <a name="known-issues-and-faq"></a>Problemas conhecidos e FAQ

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Os utilizadores de telas de alta resolução podem ter pequenas texto nas propriedades de ameaças

#### <a name="issue"></a>Problema

Na vista de análise da ferramenta, se o utilizador tem um ecrã de alta resolução que é definido por padrão para ampliar para facilitar a leitura no Windows, a seção "Mitigation(s) possível" de uma ameaça pode aparecer com texto pequeno.

![Problema conhecido com telas de alta resolução](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Solução

O utilizador pode clicar no texto de atenuação e usar o controle de zoom padrão do Windows (Crtl Mouse roda de segurança) para aumentar a ampliação dessa secção.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Ficheiros na seção "Recentemente aberta Models" da janela principal podem não conseguir abrir

#### <a name="issue"></a>Problema

A funcionalidade de "Open do OneDrive" da versão de pré-visualização foi removida. Os utilizadores com "Recentemente aberta Models" que foram guardados no OneDrive irão receber o seguinte erro.

![Recurso do OneDrive removido](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Solução

Os utilizadores do OneDrive são encorajados a utilizar da Microsoft [OneDrive para o Windows](https://onedrive.live.com/about/en-us/download/) cliente para aceder aos ficheiros armazenados no OneDrive através do padrão e a caixa de diálogo "Abrir um modelo de".

![Recurso do OneDrive removido](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>A minha organização utiliza a versão de 2016 da ferramenta, posso usar o conjunto de estêncil do Azure?

Sim, pode! O [conjunto de estêncil do Azure está disponível no github](https://github.com/Microsoft/threat-modeling-templates/)e podem ser carregados para a versão de 2016 da ferramenta. Para criar um novo modelo com o conjunto de estêncil do Azure, utilize a caixa de diálogo "Modelo de novos modelos" no ecrã do menu principal. TMT 2016 não é possível compor os links encontrados nos campos "Possíveis atenuações" do conjunto de estêncil do Azure, por conseguinte, poderá ver links apresentados como etiquetas HTML.

![Atualizações de estêncil do Azure num cliente de 2016](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - Microsoft Windows 10
- Versão do .NET necessária
  - .NET 3.5.2
- Requisitos Adicionais
  - Uma ligação à Internet é necessária para receber atualizações para a ferramenta, bem como os modelos.

## <a name="documentation-and-feedback"></a>Documentação e comentários

- Documentação para a ferramenta de modelagem de ameaças está localizada em [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)e inclui informações [sobre como utilizar a ferramenta](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Passos Seguintes

Baixe a versão mais recente dos [ferramenta de modelagem de ameaças na Microsoft](https://aka.ms/threatmodelingtool).
