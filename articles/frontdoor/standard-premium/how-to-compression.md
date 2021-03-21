---
title: Melhorar o desempenho comprimindo ficheiros em Azure Front Door Standard/Premium (Preview)
description: Saiba como melhorar a velocidade de transferência de ficheiros e aumentar o desempenho da carga de página comprimindo os seus ficheiros na Porta Frontal Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4b526d82465862b1c0d27aed6443c6d7199bfb5b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099675"
---
# <a name="improve-performance-by-compressing-files-in-azure-front-door-standardpremium-preview"></a>Melhorar o desempenho comprimindo ficheiros em Azure Front Door Standard/Premium (Preview)

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

A compressão de ficheiros é um método eficaz para melhorar a velocidade de transferência de ficheiros e aumentar o desempenho da carga de página. A compressão reduz o tamanho do ficheiro antes de ser enviado pelo servidor. A compressão de ficheiros pode reduzir os custos de largura de banda e proporcionar uma melhor experiência para os seus utilizadores.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
Existem duas formas de permitir a compressão do ficheiro:

- Ativando a compressão no seu servidor de origem. A Azure Front Door passa ao longo dos ficheiros comprimidos e entrega-os aos clientes que os solicitam.
- Ativar a compressão diretamente nos servidores POP da porta frontal Azure *(compressão no ar*). Neste caso, a Porta Frontal Azure comprime os ficheiros e envia-os para os utilizadores finais.

> [!IMPORTANT]
> As alterações na configuração da porta frontal Azure demoram até 10 minutos a propagar-se em toda a rede. Se estiver a configurar a compressão pela primeira vez para o seu ponto final CDN, considere esperar 1-2 horas antes de resolver problemas para garantir que as definições de compressão se propagaram a todos os POPs.

## <a name="enabling-compression"></a>Compressão ativadora

> [!Note]
> Na Porta Frontal Azure, a compressão faz parte do **Enable Caching** in Route. Só quando **ativar o Caching,** poderá aproveitar a compressão na Porta frontal Azure.

Pode ativar a compressão das seguintes formas:
* Durante a criação rápida - Quando ativar o caching, pode ativar a compressão.
* Durante a criação personalizada - Ative o caching e a compressão quando estiver a adicionar uma rota. 
* Na rota endpoint manager.
* Na página de Otimização.

### <a name="enable-compression-in-endpoint-manager"></a>Ativar a compressão no gestor Endpoint

1. A partir da página de perfil Standard/Premium da Porta Frontal Azure, vá ao **Endpoint Manager** e selecione o ponto final que pretende ativar a compressão.

1. **Selecione Editar Ponto Final** e, em seguida, selecione o **percurso** que pretende ativar a compressão. 

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-1.png" alt-text="Screenshot da página de aterragem do Endpoint Manager." lightbox="../media/how-to-compression/front-door-compression-endpoint-manager-1-expanded.png":::   

1. Certifique-se **de que o Caching** é verificado e, em seguida, selecione a caixa de verificação para **ativar a compressão**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Ativar a compressão no gestor de pontos finais.":::   

1. Selecione **Update** para guardar a configuração.

### <a name="enable-compression-in-optimization"></a>Permitir a compressão na otimização

1. A partir da página de perfil Standard/Premium da Porta Frontal Azure, aceda a **Otimizações** em Definições. Expandir o ponto final para ver a lista de rotas. 

1. Selecione os três pontos ao lado da **rota** que tem compressão *Desativada*. Em seguida, selecione **a rota Configure**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-optimization-1.png" alt-text="Tela de ativação na página de otimização." lightbox="../media/how-to-compression/front-door-compression-optimization-1-expanded.png"::: 

1. Certifique-se **de que o Caching** é verificado e, em seguida, selecione a caixa de verificação para **ativar a compressão**.

     :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Imagem de tela de ativação da compressão no gestor de pontos finais."::: 

1. Clique em **Atualizar**.

## <a name="modify-compression-content-type"></a>Modificar o tipo de conteúdo de compressão

Pode modificar a lista predefinição dos tipos de MIME na página Otimizações.

1. A partir da página de perfil Standard/Premium da Porta Frontal Azure, aceda a **Otimizações** em Definições. Em seguida, selecione a **rota** que tem compressão *Ativada*.

1. Selecione os três pontos junto ao **percurso** que tem compressão *Ativada*. Em seguida, **selecione Ver Ver tipos de ficheiros comprimidos**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type.png" alt-text="Screenshot da página de otimização." lightbox="../media/how-to-compression/front-door-compression-edit-content-type-expanded.png"::: 

1. Elimine os formatos predefinidos ou **selecione Adicionar** novos tipos de conteúdo.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type-2.png" alt-text="Screenshot da página de compressão de ficheiros personalizados."::: 

1. **Selecione Guardar**, para atualizar a configuração da compressão .

## <a name="disabling-compression"></a>Compressão incapacitante

Pode desativar a compressão das seguintes formas:
* Desative a compressão na rota do gestor endpoint.
* Desative a compressão na página de Otimização.

### <a name="disable-compression-in-endpoint-manager"></a>Desativar a compressão no gestor endpoint

1. A partir da página de perfil Standard/Premium da Porta Frontal Azure, aceda ao **gestor Endpoint** em Definições. Selecione o ponto final que pretende desativar a compressão.

1. **Selecione Editar Endpoint** e, em seguida, selecione a **rota** desejada para desativar a compressão. Desmarque a caixa **de compressão Ativa.**

1. Selecione **Update** para guardar a configuração.

### <a name="disable-compression-in-optimizations"></a>Desativar a compressão em Otimizações

1. A partir da página de perfil Standard/Premium da Porta Frontal Azure, aceda a **Otimizações** em Definições. Em seguida, selecione a **rota** que tem compressão *Ativada*.

1. Selecione os três pontos junto ao **percurso** que tem compressão *Ativada,* em seguida, selecione *a rota de configuração*.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization.png" alt-text="Screenshot da compressão desativada na página de otimização."::: 

1. Desmarque a caixa **de compressão Ativa.**

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization-2.png" alt-text="Screenshot da página de rota de atualização para desativar a compressão."::: 

1. Selecione **Update** para guardar a configuração.

## <a name="compression-rules"></a>Regras de compressão

Na Porta frontal de Azure, apenas os ficheiros elegíveis são comprimidos. Para ser elegível para compressão, um ficheiro deve:
* Ser de um tipo MIME 
* Seja maior que 1 KB
* Seja menor que 8 MB

Estes perfis suportam as seguintes codificações de compressão:
* gzip (zip GNU)
* brotli 

Se o pedido suportar mais de um tipo de compressão, a compressão brotli tem precedência.

Quando um pedido de um ativo especifica a compressão gzip e o pedido resulta numa falha de cache, a Porta Frontal Azure faz a compressão gzip do ativo diretamente no servidor POP. Depois, o ficheiro comprimido é servido a partir da cache.

Se a origem utilizar a Codificação de Transferência Chunked (CTE) para enviar dados comprimidos para o POP da porta frontal Azure, então os tamanhos de resposta superiores a 8 MB não são suportados. 

## <a name="next-steps"></a>Passos seguintes

- Saiba como configurar o seu primeiro [Conjunto de Regras](how-to-configure-rule-set.md)
- Saiba mais sobre [as condições de jogo definidas por regras](concept-rule-set-match-conditions.md)
- Saiba mais sobre [o conjunto de regras da porta da frente Azure](concept-rule-set.md)
