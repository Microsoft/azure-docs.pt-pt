---
title: Microsoft Threat Modeling Tool lança 9/12/2018
titleSuffix: Azure
description: Documentando as notas de lançamento para a ferramenta de modelação de ameaças
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: a921310f14f0d48922165a46d750366e170ee374
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269920"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Ferramenta de Modelação de Ameaças GA lançamento 7.1.50911.2 - 9/12/2018

Estamos entusiasmados por anunciar que a Microsoft Threat Modeling Tool está agora disponível para download como um lançamento geralmente disponível (GA). Esta versão contém importantes atualizações de privacidade e segurança, bem como correções de bugs, atualizações de funcionalidades e melhorias de estabilidade. Os utilizadores existentes da versão Preview de 2017 serão solicitados a atualizar para o mais recente lançamento através da tecnologia ClickOnce ao abrir o cliente. Para novos utilizadores da ferramenta, pode [descarregar o cliente.](https://aka.ms/threatmodelingtool)

Com esta versão, estamos a terminar o suporte para a Pré-Visualização de 2017 e recomendamos a todos os utilizadores da atualização Preview para o lançamento da GA. Em ou depois de 15 de outubro de 2018, definiremos a versão clickOnce mínima necessária para a Ferramenta de Modelação de Ameaças, e todos os clientes de Pré-visualização serão obrigados a fazer upgrade.

A Microsoft Threat Modeling Tool 2016, que está disponível no [Microsoft Download Center,](https://www.microsoft.com/en-us/download/details.aspx?id=49168)permanece suportada até 1 de outubro de 2019 apenas para correções de segurança críticas.

## <a name="feature-changes"></a>Alterações de funcionalidades

### <a name="azure-stencil-updates"></a>Atualizações de stencil azure

Foram adicionados stencils azure adicionais e as suas ameaças e mitigações associadas ao stencil definido envio com esta libertação. Foram feitas alterações significativas nas áreas de foco dos "Serviços de Aplicações Azure", "Ofertas de Bases de Dados Azure" e "Armazenamento Azure".

![Atualizações Azure Stencil](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>Recurso de integração OneDrive removido

As funcionalidades "Save To OneDrive", "Open From OneDrive" e "Share a Link" da Pré-visualização foram removidas. Os utilizadores do OneDrive são encorajados a usar o [OneDrive](https://onedrive.live.com/about/en-us/download/) da Microsoft para o cliente Windows aceder aos seus ficheiros armazenados no OneDrive através do save de ficheiros padrão e dos diálogos abertos.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Bugs fixos notáveis reportados pelos clientes

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>Na pré-visualização tMT, a ferramenta falha ao usar o modelo padrão

- Quando um stencil genérico (por exemplo, "Fluxo genérico de dados") é adicionado à superfície de desenho e gera ameaças, a ferramenta pode colidir. Este problema foi corrigido.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>Na Pré-visualização do TMT, quando guardo um relatório ou copio as ameaças, os níveis de risco estão incorretos

- Se um utilizador modificar o nível de risco de ameaças específicas e, em seguida, guardar um relatório ou copiar os riscos, o nível de risco pode voltar a "Alto". Este problema foi corrigido.

## <a name="known-issues-and-faq"></a>Questões conhecidas e FAQ

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Os utilizadores de ecrãs de alta resolução podem experimentar um pequeno texto nas propriedades da ameaça

#### <a name="issue"></a>Problema

Na visão de análise da ferramenta, se o utilizador tiver um ecrã de alta resolução definido por padrão para ampliar para a legibilidade no Windows, a secção "Possível Mitigação(s)" de uma ameaça pode aparecer com texto pequeno.

![Problema conhecido com ecrãs de alta resolução](./media/threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Solução

O utilizador pode clicar no texto de mitigação e utilizar o controlo padrão de zoom do Windows (Roda de Rato Crtl) para aumentar a ampliação dessa secção.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Os ficheiros na secção "Modelos Recentemente Abertos" da janela principal podem não abrir

#### <a name="issue"></a>Problema

A função "Open From OneDrive" do lançamento de pré-visualização foi removida. Os utilizadores com "Modelos Recentemente Abertos" que foram guardados para o OneDrive receberão o seguinte erro.

![Característica OneDrive removida](./media/threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Solução

Os utilizadores do OneDrive são encorajados a usar o [OneDrive](https://onedrive.live.com/about/en-us/download/) da Microsoft para o cliente Windows aceder aos seus ficheiros armazenados no OneDrive através do diálogo padrão e "Abrir um modelo".

![Característica OneDrive removida](./media/threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>A minha organização usa a versão de 2016 da ferramenta, posso usar o conjunto de stencil Azure?

Sim, tu podes! O [conjunto de stencil Azure está disponível no GitHub,](https://github.com/Microsoft/threat-modeling-templates/)e pode ser carregado na versão de 2016 da ferramenta. Para criar um novo modelo com o conjunto de stencil Azure, utilize o diálogo "Modelo para Novos Modelos" no ecrã principal do menu. O TMT 2016 não consegue renderizar as ligações encontradas nos campos "Possíveis Mitigações" do conjunto de stencil Azure, pelo que poderá ver links apresentados como tags HTML.

![Atualizações Azure Stencil em 2016 Cliente](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - Microsoft Windows 10
- versão .NET necessária
  - .NET 3.5.2
- Requisitos Adicionais
  - É necessária uma ligação à Internet para receber atualizações da ferramenta, bem como modelos.

## <a name="documentation-and-feedback"></a>Documentação e feedback

- A documentação para a Ferramenta de Modelação de Ameaças está localizada na [docs.microsoft.com,](threat-modeling-tool.md)e inclui informações [sobre a utilização da ferramenta.](threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>Passos seguintes

Descarregue a versão mais recente da Ferramenta de [Modelação de Ameaças da Microsoft](https://aka.ms/threatmodelingtool).
