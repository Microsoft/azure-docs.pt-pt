---
title: Inserir cliente do Internet Analyzer | Microsoft Docs
description: Neste artigo, saiba como inserir o cliente JavaScript do Internet Analyzer em seu aplicativo.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f9ecb8d731945847160b49c68c554fafdd7285d9
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896374"
---
# <a name="embed-the-internet-analyzer-client"></a>Inserir o cliente do Internet Analyzer

Este artigo mostra como inserir o cliente JavaScript em seu aplicativo. A instalação desse cliente é necessária para executar testes e receber análises de Scorecard. **O cliente JavaScript específico do perfil é fornecido após a configuração do primeiro teste.** A partir daí, você pode continuar a adicionar ou remover testes para esse perfil sem precisar inserir um novo script. Para obter mais informações sobre o Internet Analyzer, consulte a [visão geral](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

O Internet Analyzer requer acesso ao Azure e a outros serviços da Microsoft para funcionar corretamente. Permita o acesso à rede para `fpc.msedge.net` e quaisquer URLs de ponto de extremidade pré-configuradas (visíveis por meio da [CLI](internet-analyzer-cli.md)) antes de inserir o cliente.

## <a name="find-the-client-script-url"></a>Localizar a URL de script do cliente

A URL do script pode ser encontrada por meio do portal do Azure ou do CLI do Azure depois que um teste tiver sido configurado. Para obter mais informações, consulte [criar um recurso do Internet Analyzer](internet-analyzer-create-test-portal.md).

Opção 1. No portal do Azure, use [este link](https://aka.ms/InternetAnalyzerPreviewPortal) para abrir a página do portal de visualização do Azure Internet Analyzer. Navegue até o perfil do Internet Analyzer para ver a URL do script acessando **configurações > configuração**.

Opção 2. Usando o CLI do Azure, verifique a propriedade `scriptFileUri`.
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>Detalhes do cliente

O script é gerado especificamente para seu perfil e testes. Depois de ser carregado, o script será executado em um atraso de 2 segundos. Primeiro, ele entra em contato com o serviço do Internet Analyzer para buscar a lista de pontos de extremidade configurados em seus testes. Em seguida, ele executa as medidas e carrega os resultados cronometrados de volta para o serviço do Internet Analyzer.

## <a name="client-examples"></a>Exemplos de cliente

Esses exemplos mostram alguns métodos básicos para inserir o JavaScript do cliente em sua página da Web ou aplicativo. Usamos `0bfcb32638b44927935b9df86dcfe397` como um exemplo de ID de perfil para a URL do script.

### <a name="run-on-page-load"></a>Executar no carregamento da página
O método mais simples é usar a marca de script dentro do bloco de metamarca. Essa marca executará o script uma vez por carregamento de página.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>Passos seguintes

Leia as [perguntas frequentes do Internet Analyzer](internet-analyzer-faq.md)
