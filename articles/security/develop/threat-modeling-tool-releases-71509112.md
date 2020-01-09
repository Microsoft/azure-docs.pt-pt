---
title: Versão Microsoft Threat Modeling Tool 9/12/2018 – Azure
description: Documentando as notas de versão da ferramenta de modelagem de ameaças
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: ad489d6de222380e85a962c7b50c63a5c2122c72
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75548807"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Threat Modeling Tool GA versão 7.1.50911.2-9/12/2018

Estamos felizes em anunciar o Microsoft Threat Modeling Tool agora está disponível para download como uma versão de GA (disponível para o público). Esta versão contém atualizações de segurança e privacidade importantes, bem como correções de bugs, atualizações de recursos e aprimoramentos de estabilidade. Os usuários existentes da versão de visualização 2017 serão solicitados a atualizar para a versão mais recente por meio da tecnologia ClickOnce ao abrir o cliente. Para novos usuários da ferramenta, você pode [baixar o cliente](https://aka.ms/threatmodelingtool).

Com esta versão, estamos terminando o suporte para a versão prévia 2017 e recomendamos que todos os usuários da atualização de visualização para o lançamento de GA. Em ou após outubro de 15 2018, definiremos a versão mínima necessária do ClickOnce para o Threat Modeling Tool, e todos os clientes de visualização serão necessários para a atualização.

O Microsoft Threat Modeling Tool 2016, que está disponível no [centro de download da Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=49168), permanece com suporte até 1 2019 de outubro apenas para correções de segurança críticas.

## <a name="feature-changes"></a>Alterações de recurso

### <a name="azure-stencil-updates"></a>Atualizações do estêncil do Azure

Os estênceis do Azure adicionais e suas ameaças e atenuações associadas foram adicionados ao conjunto de estênceis fornecido com esta versão. Alterações significativas foram feitas nas áreas de foco de "serviços Azure App", "ofertas de banco de dados do Azure" e "armazenamento do Azure".

![Atualizações do estêncil do Azure](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>Recurso de integração do OneDrive removido

Os recursos "salvar no OneDrive", "abrir do OneDrive" e "compartilhar um link" da versão prévia foram removidos. Os usuários do OneDrive são incentivados a usar o cliente do [onedrive para Windows](https://onedrive.live.com/about/en-us/download/) da Microsoft para acessar seus arquivos armazenados no onedrive por meio das caixas de diálogo Salvar e abrir arquivo padrão.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Erros fixos importantes relatados pelos clientes

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>Na visualização do TMT, a ferramenta falha ao usar o modelo padrão

- Quando um estêncil genérico (por exemplo, "fluxo de dados genérico") é adicionado à superfície de desenho e gera ameaças, a ferramenta pode falhar. Este problema foi corrigido.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>Na visualização do TMT, quando eu salvo um relatório ou copio as ameaças, os níveis de risco estão incorretos

- Se um usuário modificar o nível de risco de ameaças específicas e, em seguida, salvar um relatório ou copiar os riscos, o nível de risco poderá ser revertido para "alto". Este problema foi corrigido.

## <a name="known-issues-and-faq"></a>Problemas conhecidos e perguntas frequentes

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Os usuários de telas de alta resolução podem apresentar texto pequeno nas propriedades de ameaça

#### <a name="issue"></a>Problema

Na exibição de análise da ferramenta, se o usuário tiver uma tela de alta resolução definida por padrão para ampliar para facilitar a leitura no Windows, a seção "possíveis atenuações" de uma ameaça poderá aparecer com texto pequeno.

![Problema conhecido com telas de alta resolução](./media/threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Solução

O usuário pode clicar no texto de mitigação e usar o controle de zoom padrão do Windows (CRTL-mouse wheel up) para aumentar a ampliação dessa seção.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Os arquivos na seção "modelos abertos recentemente" da janela principal podem falhar ao abrir

#### <a name="issue"></a>Problema

O recurso "abrir do OneDrive" da versão de visualização foi removido. Os usuários com "modelos abertos recentemente" que foram salvos no OneDrive receberão o seguinte erro.

![Recurso do OneDrive removido](./media/threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Solução

Os usuários do OneDrive são incentivados a usar o Microsoft [onedrive para Windows](https://onedrive.live.com/about/en-us/download/) Client para acessar seus arquivos armazenados no onedrive por meio da caixa de diálogo padrão e "abrir um modelo".

![Recurso do OneDrive removido](./media/threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>Minha organização usa a versão 2016 da ferramenta, posso usar o conjunto de estênceis do Azure?

Sim, você pode! O [conjunto de estênceis do Azure está disponível no GitHub](https://github.com/Microsoft/threat-modeling-templates/)e pode ser carregado na versão 2016 da ferramenta. Para criar um novo modelo com o conjunto de estêncil do Azure, use a caixa de diálogo "modelo para novos modelos" na tela do menu principal. O TMT 2016 não pode renderizar os links encontrados nos campos "possíveis atenuações" do conjunto de estênceis do Azure, portanto, você pode ver links exibidos como marcas HTML.

![Atualizações do estêncil do Azure no cliente 2016](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - Microsoft Windows 10
- Versão do .NET necessária
  - .NET 3.5.2
- Requisitos Adicionais
  - Uma conexão com a Internet é necessária para receber atualizações para a ferramenta, bem como modelos.

## <a name="documentation-and-feedback"></a>Documentação e comentários

- A documentação do Threat Modeling Tool está localizada em [docs.Microsoft.com](threat-modeling-tool.md)e inclui informações [sobre como usar a ferramenta](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Passos seguintes

Baixe a versão mais recente do [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
