---
title: Usar a CDN do Azure para acessar BLOBs com domínios personalizados por HTTPS
description: Saiba como integrar a CDN do Azure com o armazenamento de BLOBs para acessar BLOBs com domínios personalizados por HTTPS
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: f3386d04cd3316b38a094524d0d5d4f3c5ab36bb
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68986845"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Usar a CDN do Azure para acessar BLOBs com domínios personalizados por HTTPS

A rede de distribuição de conteúdo do Azure (CDN do Azure) agora dá suporte a HTTPS para nomes de domínio personalizados. Com a CDN do Azure, você pode acessar BLOBs usando seu nome de domínio personalizado por HTTPS. Para fazer isso, habilite a CDN do Azure no seu BLOB ou ponto de extremidade da Web e mapeie a CDN do Azure para um nome de domínio personalizado. Depois de terminar, o Azure simplifica a habilitação de HTTPS para seu domínio personalizado por meio do acesso de um clique e do gerenciamento completo de certificados. Não há aumento no preço normal da CDN do Azure.

A CDN do Azure ajuda a proteger a privacidade e a integridade dos dados de seus dados de aplicativo Web enquanto eles estão em trânsito. Usando o protocolo SSL para fornecer tráfego via HTTPS, a CDN do Azure mantém seus dados criptografados quando eles são enviados pela Internet. Usar HTTPS com a CDN do Azure ajuda a proteger seus aplicativos Web contra ataques.

> [!NOTE]  
> Além de fornecer suporte a SSL para nomes de domínio personalizados, a CDN do Azure pode ajudá-lo a dimensionar seu aplicativo para fornecer conteúdo de alta largura de banda em todo o mundo. Para saber mais, confira [visão geral da CDN do Azure](../../cdn/cdn-overview.md).

## <a name="quickstart"></a>Início Rápido

Para habilitar o HTTPS para o ponto de extremidade de armazenamento de BLOBs personalizado, faça o seguinte:

1.  [Integre uma conta de armazenamento do Azure com a CDN do Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md).  
    Este artigo explica como criar uma conta de armazenamento no portal do Azure, caso ainda não tenha feito isso.

    > [!NOTE]  
    > Para adicionar o ponto de extremidade da Web de armazenamento durante a visualização de suporte de sites estáticos no armazenamento do Azure, selecione **origem personalizada** na lista suspensa **tipo de origem** . No portal do Azure, você precisa fazer isso em seu perfil de CDN do Azure, em vez de diretamente em sua conta de armazenamento.

2.  [Mapeie o conteúdo da CDN do Azure para um domínio personalizado](../../cdn/cdn-map-content-to-custom-domain.md).

3.  [Habilite o HTTPS em um domínio personalizado da CDN do Azure](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Assinaturas de acesso compartilhado

Por padrão, os pontos de extremidade do armazenamento de BLOBs não permitem acesso de leitura anônimo. Se o ponto de extremidade do armazenamento de BLOBs estiver configurado para não permitir acesso de leitura anônimo, forneça um token de [assinatura de acesso compartilhado](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) em cada solicitação ao seu domínio personalizado. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).

A CDN do Azure não respeita nenhuma restrição que seja adicionada ao token de assinatura de acesso compartilhado. Por exemplo, todos os tokens de assinatura de acesso compartilhado expiram. Você ainda pode acessar o conteúdo com uma assinatura de acesso compartilhado expirada até que esse conteúdo seja limpo dos nós de borda da CDN do Azure. Pode controlar o tempo durante o qual os dados são colocados em cache na CDN do Azure, ao definir o cabeçalho de resposta da cache. Para saber como, consulte [gerenciar a expiração de blobs de armazenamento do Azure na CDN do Azure](../../cdn/cdn-manage-expiration-of-blob-content.md).

Se você criar duas ou mais URLs de assinatura de acesso compartilhado para o mesmo ponto de extremidade de BLOB, recomendamos ativar o cache de cadeia de caracteres de consulta para a CDN do Azure. Essa ação garante que o Azure trate cada URL como uma entidade exclusiva. Para obter mais informações, consulte [Control Azure CDN caching behavior with query strings](../../cdn/cdn-query-string.md)(Controlar o comportamento de colocação em cache do Azure CDN com cadeias de consulta).

## <a name="http-to-https-redirection"></a>Redirecionamento de HTTP para HTTPS

Você pode redirecionar o tráfego HTTP para HTTPS. Isso requer o uso da oferta Premium da CDN do Azure da Verizon. [Substitua o comportamento http pelo mecanismo de regras da CDN do Azure](../../cdn/cdn-rules-engine.md) aplicando a seguinte regra:

![Regra de redirecionamento de HTTP para HTTPS](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

*CDN-Endpoint-Name*, que você seleciona na lista suspensa, refere-se ao nome que você configurou para o ponto de extremidade da CDN do Azure. *Origem-caminho* refere-se ao caminho em sua conta de armazenamento de origem, em que o conteúdo estático é armazenado. Se você estiver hospedando todo o conteúdo estático em um único contêiner, substitua *Origin-path* pelo nome desse contêiner.

Para obter mais detalhes sobre as regras, consulte os [recursos do mecanismo de regras da CDN do Azure](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Preços e faturação

Ao acessar blobs por meio da CDN do Azure, você paga [preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/blobs/) de BLOBs para o tráfego entre os nós de borda e a origem (armazenamento de BLOBs). Você paga os [preços da CDN do Azure](https://azure.microsoft.com/pricing/details/cdn/) para os dados que são acessados dos nós de borda.

Por exemplo, digamos que você tenha uma conta de armazenamento no oeste dos EUA que está acessando por meio da CDN do Azure. Quando alguém no Reino Unido tenta acessar um blob na conta de armazenamento por meio da CDN do Azure, o Azure primeiro verifica o blob no nó de borda mais próximo do Reino Unido. Se o Azure encontrar o blob, ele acessará uma cópia e usará o preço da CDN do Azure, pois a CDN do Azure está acessando-a. Se o Azure não encontrar o blob, ele copiará o blob para o nó de borda. Essa ação resulta em encargos de egresso e de transações, conforme especificado no preço do armazenamento de BLOBs. Em seguida, o Azure acessa o arquivo no nó de borda, o que resulta na cobrança da CDN do Azure.

Na [página de preços da CDN do Azure](https://azure.microsoft.com/pricing/details/cdn/), o suporte HTTPS para nomes de domínio personalizados está disponível para a CDN do Azure somente de produtos Standard e Premium da Verizon.

## <a name="next-steps"></a>Passos seguintes

* [Configurar um nome de domínio personalizado para o ponto de extremidade do armazenamento de BLOBs](storage-custom-domain-name.md)
* [Alojamento de site estático no Armazenamento do Azure](storage-blob-static-website.md)
